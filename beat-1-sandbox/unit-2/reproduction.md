# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

[Your GitHub username, exactly as it appears on your profile — no `@`, no profile URL. Your
comments upstream are identified by this name.]

goketech

---

## Posted upstream

**Claim comment**
https://github.com/codepath/pathreview-ai301-fa26-s3/issues/15#issuecomment-5878563177

Hi, I'd like to claim this issue.

As I understand it, the Orchestrator keeps a single ContextManager across
reviews and never empties it, so a second review from the same user gets
back the first run's result for any tool whose input didn't change.

I'm going to start by tracing session_store.py and context_manager.py to
confirm where that stale state is actually held, then look at clearing it
(and the profile's saved session state) at the start of run in
orchestrator.py. I'll post a reproduction report here with what I find.

**Reproduction comment**
https://github.com/codepath/pathreview-ai301-fa26-s3/issues/15#issuecomment-5879451967

Ran terminal command:  .venv/bin/python -c 'import sys, platform; print(sys.version.split()[0], platform.machine())'; .venv/bin/pip --version; git rev-parse --short HEAD; git branch --show-current

```markdown
## Environment

- OS: macOS (Apple Silicon, arm64)
- Python: 3.11.16 (via `.venv`)
- pip: 26.2.1
- Repo: `main` @ `2f4e82f`

## Reproduction

`Orchestrator` creates a single `ContextManager()` in `__init__` and reuses it across every call to `run()`. Since `ContextManager`'s cache key (`f"{tool_name}:{hash(tool_input)}"`) only depends on the tool's input, not on `profile_id`, a second `run()` call on the same orchestrator instance can return a stale result from a completely different profile whenever a tool's input hashes the same as a previous call. `market_analyzer`'s input is hardcoded as `{"detected_skills": {}}` in `_build_plan`, so it reproduces every time.

Script:

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

print(result1["tool_results"]["market_analyzer"])
print(result2["tool_results"]["market_analyzer"])
print(tool.calls)
```

Output:

```
tool_result_cache_miss  key=market_analyzer:95e4a8f9... tool=market_analyzer   # profile-1
tool_result_stored      key=market_analyzer:95e4a8f9... tool=market_analyzer
tool_result_cache_hit   key=market_analyzer:95e4a8f9... tool=market_analyzer   # profile-2
tool_cache_hit          tool=market_analyzer

{'call_number': 1}
{'call_number': 1}   # <- should be 2 (fresh execution for profile-2)
1                    # <- tool.execute() only ran once across two run() calls
```

`profile-2`'s report silently contains `profile-1`'s cached `market_analyzer` result, and `tool.calls` confirms `execute()` never ran a second time.

## Proposed fix

In `Orchestrator.run()`, at the very start (before building the plan):
- Call `self.context_manager.clear()` to empty the in-memory memoization cache (new `ContextManager.clear()` method).
- Call `self.session_store.delete(profile_id)` (when a session store is configured) to drop that profile's previously saved Redis session state, instead of only ever loading/merging it forward.

This scopes memoization to a single `run()` invocation and prevents both the in-process cache and the persisted session state from leaking results across profiles/reviews.

Want me to open a PR for this fix, or would you rather discuss the approach here first?


## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. `agreement: 15/20 scored items  (bar: 18/20: below the bar; category floor unmet: no match in disclosure)` — the starting rubric (Environment, Backtrack, Input_similarity, Output_similarity, Evidence only) had no check for AI-use disclosure at all, so it graded the one `disclosure`-category package as an accept.
2. `agreement: 18/20 scored items  (bar: 18/20: PASS)` — after relaxing Backtrack's verbatim-paste requirement, adding an honest-cannot-reproduce carve-out to Input_similarity/Output_similarity/Evidence, and adding a required `Disclosure` check, the bar passed, but two packages flipped disagreement (`pkg-03` on `Environment`, `pkg-19` graded `accept` with no check catching its boilerplate claim comment).
3. `agreement: 20/20 scored items  (bar: 18/20: PASS)` — after adding the `Comms` check for boilerplate/over-promising claim comments, every package agreed with gold in that run.
4. `agreement: 19/20 scored items  (bar: 18/20: PASS)` — the confirming full run saved to `eval-run.txt`, same rubric/evidence-guide text as run 3 (identical file hashes), but the Sonnet judge read `pkg-05`'s env.yml description as too vague this time (`failed: Backtrack`) even though the same file passed on the prior run. This is the score recorded in the committed `eval-run.txt`.

**Package analysis**

`pkg-05` (conda/conda#16543). Gold label: `accept` ("minimal env.yml repro with a json.tool parse failure as the artifact"). My rubric's verdict in the committed run: `reject`, `failed: Backtrack`.

The repro report says: "Steps: wrote a minimal `env.yml` containing a valid `dependencies:` list plus a `category:` section (the section conda does not recognize), then:" followed by the exact `conda env update --quiet --json -f env.yml 2>/dev/null` command and its output. The commands are quoted verbatim and the specific triggering key (`category:`, the same key named in the error output) is exact, which is what my Backtrack check's carve-out ("a helper file's full text does not need to be pasted if the exact parameter values that define it are given") was written to allow — and it did pass on an earlier run of mine. But the env.yml's other content ("a valid `dependencies:` list") is still prose ("a valid list", not literal file text), and on this run the grader read that residual vagueness as failing "the reconstruction depends on state that is never shared or specified," even though the one value that actually matters to trigger the bug is exact. That's a real ambiguity in the check's wording, not just grader noise: I never say how much of a non-triggering config value is allowed to stay in prose before the check should fail.

**Check rationale**

> Comms | The candidate claim comment (and repro comment), read against the specific issue they were posted to. | Pass if the comment is specific to this issue and this attempt: it references a concrete detail of the issue or repro (not generic praise or a line that could be pasted onto any issue) and does not demand exclusive assignment or promise a fix/timeline it has no grounds for. Fail if the comment is interchangeable boilerplate, is mostly flattery or enthusiasm with no issue-specific content, or promises a guaranteed fix or deadline. | required

I added this check because my first five checks (Environment, Backtrack, Input_similarity, Output_similarity, Evidence) all judge the repro report, and none of them ever look at the claim comment on its own. That gap let `pkg-19` (vuejs/core#15205) grade `accept`: its repro report is genuinely solid, but its claim comment reads "Hello sir! Great project, I love Vue and use it every day... Kindly assign it to me, I will fix it within 2 days guaranteed" — exactly the boilerplate/over-promising pattern gold rejects it for (`unfollowable-comms`, "the claim comment is interchangeable assign-me boilerplate promising a guaranteed 2-day fix"). I rejected folding this into the `Evidence` check because Evidence is about artifacts backing a result, while this is about whether the words respect the reader's time and the repo's norms — a different family the evidence guide's `Comms` section already named but the checks table never covered until I added this row.


**Trade-offs**

Adding the honest-cannot-reproduce carve-out to `Input_similarity`, `Output_similarity`, and `Evidence` is what flipped `pkg-09` (sharkdp/fd#2033) and `pkg-10` (starship/starship#7648) from `reject` to `accept`, matching gold on both. Before the carve-out, a report that honestly says "I tried and could not reproduce this" automatically failed Output_similarity (its output never matches the issue's failure) and Evidence (its artifact backs a non-occurrence, not "the failure occurring") — the checks were structurally unable to pass a true negative result, no matter how well-evidenced.

The trade-off: that same leniency is exactly what makes `pkg-05`'s Backtrack grading unstable across runs (see Package analysis above) — a check written to tolerate "the trigger value is exact even if surrounding prose is not" is inherently less mechanical than one requiring everything pasted verbatim, so I accept it will occasionally read a borderline report either way rather than always the same way.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
