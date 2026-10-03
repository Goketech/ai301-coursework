# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**goketech**

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/15#issuecomment-5968497748

Reproduced on macOS (arm64), Python 3.11.16 via `.venv`, `main` @ `2f4e82f`.

I ran the two-`run()` script from my repro comment on one `Orchestrator` instance (`profile-1` then `profile-2`). Both runs returned `{'call_number': 1}` with `tool.calls == 1`, so `execute()` never ran for profile-2 (full log in my repro comment). The key repeats because `_build_plan` hardcodes `market_analyzer` input and the cache key excludes `profile_id`.

Plan: add `ContextManager.clear()` in `agent/memory/context_manager.py`, then at the very start of `Orchestrator.run()` in `agent/orchestrator.py` (before `_build_plan`) call `self.context_manager.clear()` and, when configured, `self.session_store.delete(profile_id)` (existing method, no change needed in `session_store.py`). This also clears the `cached_results` return value. Out of scope: per-profile keys, DAG validation, failure propagation, restart persistence. Verify by re-running the same script and expecting `tool.calls == 2` with `{'call_number': 2}` on profile-2 plus a cache miss, plus a new two-sequential-`run()` regression test, then `make test-unit` and lint/typecheck green.

One unknown I'm leaving for review: deleting saved session state at start is what the issue prescribes, but I haven't verified whether anything relies on resume-across-runs. Happy to narrow it if you want different semantics.

Want me to open a PR for this fix, or would you rather discuss the approach here first?


---

## Your branch

**fix/15-agent-session-state**

**Evidence**

The repro script (`main.py`, same instance, `profile-1` then `profile-2`) prints `{'call_number': 1}`, `{'call_number': 2}`, `2`. Both runs log `tool_result_cache_miss`, and there is no `tool_cache_hit`.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

One full 20-package run, no partial or smoke runs:

> `agreement: 19/20 scored items  (bar: 18/20: PASS)`

> `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`

The single miss is `pkg-09` (`pkg-09  clear-accept       accept  reject   NO     failed: honest-unknowns`). This score is the agreement line in the committed `eval-run.txt`.

**Package analysis**

`pkg-09` (sharkdp/fd#2067): my rubric decided `reject`, the gold label says `accept`. The eval table row reads:

> `pkg-09  clear-accept       accept  reject   NO     failed: honest-unknowns`

Gold's note reads: `"honestly scoped-down: option 2 normalization gated to Windows, defers the direct-globset rework with reasons, decisive three-spelling fixture test; arguable on the deferral, ready as scoped"`. The plan's own gating language reads:

> `Risk: '\' is a legal filename character on Unix; the normalization is therefore gated to Windows targets only, and I call that out for review since it is the one place this change could alter matches.`

My rubric read it as reject because the `honest-unknowns` check fired on the plan's confident matching/output sentences alongside that Risk disclosure:

> `The normalized bytes are used only for matching, never for output, so printed paths keep native separators.`

> `the Linux control still matches (no behavior change where '\' cannot be a separator).`

The grader treated those as asserting-as-fact behavior the package never ran (no Linux run appears in the repro block), tripping the check's `claims verification it never ran` clause, while gold reads the same lines as covered by the explicit Windows-gating Risk flag.

**Check rationale**

The check, exactly as it reads in the `rubric.md` uploaded to `tools/plan-check/`:

> `| honest-unknowns | Risk, unknown, and certainty claims in the plan read against what the repro evidence and thread actually establish | Pass if open questions, unverified support, unmeasured costs, and deviations are stated as such and left for review or follow-up. Fail if the plan asserts as fact what the evidence marks uncertain, claims verification it never ran, or dresses a guess as a traced result. | required |`

It reads that way because I wrote it against the false-confidence failure family (the calib-03 trap: a polished plan adopting the thread's confident diagnosis over the repro's control runs). I rejected the softer alternative — pass any plan that merely contains a Risk or unknowns section — because that version waves through dressed-up guesses. The strict clause I kept is `claims verification it never ran`, which is exactly the clause that fires on `pkg-09`'s never-for-output and Linux-behavior sentences quoted above.

**Trade-offs**

The case I accept this check will miss is `pkg-09` itself: a plan whose Risk disclosure is arguably honest but whose confident matching/output sentences my strict `claims verification it never ran` clause still fails. I keep that strictness because loosening the clause to rescue one arguable accept would wave through the false-confidence rejects the clause exists to catch, and the rest of the set shows the price is contained to that single package:

> `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`

Nothing changed elsewhere for the same reason: this was a single full run with no revisions, so there was no loosened check to flip a prior agreement and no `--only` canary re-runs were needed.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
