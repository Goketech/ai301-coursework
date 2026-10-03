# Plan

## Environment

- OS: macOS (Apple Silicon, arm64)
- Python: 3.11.16 (via `.venv`)
- pip: 26.2.1
- Repo: `main` @ `2f4e82f`

Command run:

```bash
.venv/bin/python -c 'import sys, platform; print(sys.version.split()[0], platform.machine())'; .venv/bin/pip --version; git rev-parse --short HEAD; git branch --show-current
```

Observed failure (issue body):

> "`Orchestrator` keeps one `ContextManager` and never empties it, so one orchestrator handling two reviews returns the first run's result for every tool whose input hasn't changed. Empty that cache and delete the profile's saved session state when `run` starts."

Repro script (same `Orchestrator` instance, two `run()` calls, `CountingTool` named `market_analyzer`):

```python
from agent.orchestrator import Orchestrator
from agent.tools.base import BaseTool, ToolResult

class CountingTool(BaseTool):
    name = "market_analyzer"
    description = "test tool"
    def __init__(self):
        self.calls = 0
    def execute(self, input_data: dict) -> ToolResult:
        self.calls += 1
        return ToolResult(success=True, data={"call_number": self.calls})

tool = CountingTool()
orch = Orchestrator(tools={"market_analyzer": tool})

result1 = orch.run("profile-1", {"resume_text": "irrelevant"})
result2 = orch.run("profile-2", {"resume_text": "irrelevant"})
```

Observed output:

```
tool_result_cache_miss  key=market_analyzer:95e4a8f9... tool=market_analyzer   # profile-1
tool_result_stored      key=market_analyzer:95e4a8f9... tool=market_analyzer
tool_result_cache_hit   key=market_analyzer:95e4a8f9... tool=market_analyzer   # profile-2
tool_cache_hit          tool=market_analyzer

{'call_number': 1}
{'call_number': 1}   # <- should be 2 (fresh execution for profile-2)
1                    # <- tool.execute() only ran once across two run() calls
```

My interpretation:

> "`profile-2`'s report silently contains `profile-1`'s cached `market_analyzer` result, and `tool.calls` confirms `execute()` never ran a second time."

Pinning facts:

1. `Orchestrator` creates a single `ContextManager()` in `__init__` (`agent/orchestrator.py:30`) and reuses it across every `run()` call.
2. `ContextManager`'s cache key is `f"{tool_name}:{input_hash}"` (`agent/memory/context_manager.py:26,40`), where `input_hash = ContextManager.hash_input(tool_input)` (`agent/orchestrator.py:147`) — a SHA-256 of the sorted-JSON input (`agent/memory/context_manager.py:58-69`). It depends only on tool input, not on `profile_id`.
3. `market_analyzer`'s input is hardcoded as `{"detected_skills": {}}` in `_build_plan` (`agent/orchestrator.py:126-128`), so the second run hashes identically and reproduces every time. Clearing the cache also fixes the `cached_results` leak, since `run()` returns `self.context_manager.get_all_results()` (`agent/orchestrator.py:75`).
4. No separate control run was posted: both runs used the same orchestrator instance, same irrelevant `resume_text`, and differed only in `profile_id` (`profile-1` vs `profile-2`), which isolates the shared instance as the cause. I did not run the fresh-`Orchestrator`-per-`run()` control (expected `calls == 2`) — noted as not run, not claimed.

## Diagnosis (grounded cause)

The stale result comes from in-process memoization leaking across `run()` invocations: the single `ContextManager.results` dict survives from `profile-1` to `profile-2`, and because the lookup key excludes `profile_id`, the identical `market_analyzer:95e4a8f9...` key hits on the second run and `_execute_tool` returns the cached `ToolResult` without calling `execute()` again. The persisted `SessionStore` path (`get` then `update`/`set` in `Orchestrator.run`, `agent/orchestrator.py:49-68`) only ever loads/merges forward and never deletes, so a profile's saved Redis state can likewise leak into the next review. This cites only behavior the repro shows (cache-miss then cache-hit on the same key, `tool.calls == 1`, identical `{'call_number': 1}` outputs) and contradicts no control.

Thread direction: the issue body explicitly prescribes the fix ("Empty that cache and delete the profile's saved session state when `run` starts") and names the three relevant files (`agent/orchestrator.py`, `agent/memory/session_store.py`, `agent/memory/context_manager.py`). I checked the public issue page for #15 and saw no other maintainer comments, prior-art PRs, posted test binaries, or listed fix options beyond that body direction; this plan follows that direction exactly. If a maintainer has posted direction I could not see (login-gated), I will rebase onto it.

## Scope

In scope (serves the observed failure only):

- Scope memoization to a single `run()` invocation by clearing in-memory cache at the start of `run()`.
- Drop the profile's previously saved persisted session state at the start of `run()` when a session store is configured.

Out of scope / explicitly deferred with reasons:

- No per-profile cache keys or persistent cross-run caching — the issue asks for clearing, not keying, and keying would preserve the leak for same-profile re-reviews.
- No DAG-based plan validation of tool prerequisites (separate enhancement, issue C-14).
- No surfacing of failed-tool `tool_results` entries to review output (separate bug, issue C-04).
- No persisting in-flight state across API restarts (separate concern, issue C-07; deleting at start is in tension with it — left for maintainer review, see Risks).
- No changes to `_build_plan` ordering, retry/timeout policy, or any tool implementation.
- No migration, refactor, redesign, or new feature beyond the two clearing calls plus the new `clear()` method.

## Files I'll touch

- `agent/memory/context_manager.py` — add a `clear()` method that empties `self.results` (new, ~3 lines; `SessionStore.delete` already exists so no change needed there beyond calling it).
- `agent/orchestrator.py` — at the very start of `run()`, before `_build_plan`: call `self.context_manager.clear()`, and if `self.session_store` is set, call `self.session_store.delete(profile_id)`.
- `agent/memory/session_store.py` — read-only reference (provides existing `delete()`); no edit expected unless review asks for error-handling around the new call.
- Tests: per `docs/CONTRIBUTING.md` ("Every code change should include or update relevant tests"), I will add a regression test for two sequential `run()` calls on one `Orchestrator` (fresh `CountingTool`-style stub asserting `calls == 2`), plus re-run the ad hoc repro script and the existing suite `make test-unit` / `pytest tests/unit -q`. There is currently no orchestrator unit test in `tests/unit/` and no xfail marker for #15, so this is a new test, not a marker removal.

## Approach (chosen, in workable order)

1. Add `ContextManager.clear()` in `agent/memory/context_manager.py`:
   ```python
   def clear(self) -> None:
       """Empty the in-memory memoization cache."""
       self.results.clear()
   ```
   with Google-style docstring per `docs/CONTRIBUTING.md`.
2. Edit `Orchestrator.run()` in `agent/orchestrator.py` to clear both states first, before `plan = self._build_plan(profile_data)`:
   ```python
   self.context_manager.clear()
   if self.session_store:
       self.session_store.delete(profile_id)
   ```
   Keep the existing load/merge/persist flow below unchanged.
3. Verify `session_store.py` needs no change (`delete()` at `agent/memory/session_store.py:68-81` already deletes `session:{id}` and logs `session_deleted` / `session_delete_error`).
4. Branch per contributing guide as `fix/15-agent-session-state` on my fork, conventional commits (e.g. `fix(agent): clear session state at start of orchestrator run`), run `make check && make test-unit` before pushing.

## Test plan

1. Re-run the exact week-2 repro script above (same `CountingTool`, same orchestrator instance, `orch.run("profile-1", {"resume_text": "irrelevant"})` then `orch.run("profile-2", {"resume_text": "irrelevant"})`).
   - Expected after fix (observable artifacts): `tool.calls == 2`; `result1["tool_results"]["market_analyzer"] == {'call_number': 1}`; `result2["tool_results"]["market_analyzer"] == {'call_number': 2}`; logs show `tool_result_cache_miss` (not `tool_result_cache_hit` / `tool_cache_hit`) on the `profile-2` run.
   - Controls that must stay unchanged: same instance reused across both runs, same hardcoded `market_analyzer` input `{"detected_skills": {}}`, same irrelevant `resume_text`.
2. Session-store check (with a fake/minimal Redis stub): seed `session_store` with a prior value for `profile-2`, run `run("profile-2", ...)`, assert `delete("profile-2")` was called before `get`/`set` and that `set` was called with only the fresh run's results (not merged stale state). I assert on the `delete`-then-`set` call order/payload — not on `tool_results` alone, since `run()` returns `results` rather than `session_state` and that assertion would already pass on unpatched code.
3. Regression guard plus new test: run the new two-sequential-`run()` regression test and `make test-unit` (`pytest tests/unit -q`) stays green; `make lint && make typecheck` (ruff/black/mypy) stays green per `docs/CONTRIBUTING.md` CI requirement. Full-suite green alone is not the proof — the CountingTool artifact (`tool.calls == 2`, `{'call_number': 2}` on profile-2) is.

## Risks and unknowns (honest, not verified)

- I have not yet implemented or run the fix; all expectations above are predictions from the repro, stated as such.
- Deleting the saved session at `run()` start is what the issue prescribes, but I have not verified how callers that rely on resume-across-runs or the C-07 "persist across restarts" direction use `SessionStore.get`; if a maintainer wants resume semantics, this delete may need narrowing (e.g. only same-user re-review vs. restart recovery). Left for review.
- I have not measured cost: `clear()` + one Redis `DEL` per run is expected negligible, but unmeasured.
- Unknown: whether any existing test asserts the current sticky-cache behavior (I did not find an orchestrator unit test in `tests/unit/`); if CI surfaces one, I will update this plan rather than silently change the test.
- Concurrent same-profile `run()` calls could interleave clear/delete; serializing concurrent reviews (issue E-07 per-profile lock) is out of scope here.
- Redis `delete()` failure only logs (`session_delete_error`) and does not raise; a failed delete would silently keep stale state — noted, not changed in this fix.

## Deviations

The fix itself went in exactly as planned. `ContextManager.clear()` was added, and `run()` now calls `self.context_manager.clear()` and, when a store is configured, `self.session_store.delete(profile_id)` before `_build_plan`. `session_store.py` needed no edit.
