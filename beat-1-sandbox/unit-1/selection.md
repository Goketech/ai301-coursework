# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**https://github.com/codepath/pathreview-ai301-fa26-s3/issues/15**

[The individual Path Review issue page. A link to the repository or the issue list
does not satisfy this field.]

**Verdict output**

[Your skill's live-mode output for this issue, pasted verbatim and ending with the
fenced JSON verdict block. A summary does not satisfy this field.]

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

```
Evidence gathering is complete. All three issues are in the scoped repo, opened by a COLLABORATOR, with zero comments, no assignees, and no linked PRs.

Shared repo facts (measured against today, 2026-09-20):
- Not archived; last push 2026-09-16 (4 days ago); last 5 default-branch commits dated 2026-09-16 ×3 and 2026-08-24 ×2
- Primary language Python; no releases (irrelevant — the push clause carries the check)
- docs/CONTRIBUTING.md, README.md, and the PR template contain no AI-contribution language, and no AI_POLICY.md/AGENTS.md exists — silence, which passes

Ranked read-out

Accepted, in fit order:

1. #15 — Agent session state is not cleared between reviews — the best fit: a genuine behavioral bug (stale ContextManager cache returning a prior run's result), spanning three Python files across agent/orchestrator.py and the memory layer. That is the "explore and work on real issues" technical work the fit profile asks for, and the cross-file tracing suits 2 years of experience.
2. #26 — Add a safety event count to the health check endpoint — also a real bug-labeled defect (safety_events_last_hour hardcoded to 0), Python, two files, with the fix path already named. Slightly narrower than #15, hence second.
3. #12 — Add snapshot tests for prompt templates — passes everything, but it is an enhancement touching a single test file. Test scaffolding rather than a live defect, so it ranks last against a profile that prefers real issues.

Rejected: none.

All three tie on both preferred checks (each carries good first issue and was opened by a collaborator; each is Python), so the fit profile alone set the order.

[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/15",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "Default-branch commit 2026-09-16 by Aburke225 ('chore: sync the issues manifest'), 4 days before today"},
      {"name": "Repo in use", "grade": "pass", "evidence": "isArchived: false; pushedAt 2026-09-16T21:50:20Z, within 90 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "One bounded fix across 3 named files, est. 3-4 hours; no umbrella list, no design debate (0 comments), no core-internals warning"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: []; timeline shows only 3 label events by the opener; no linked PRs, no claim comments"},
      {"name": "AI-contribution policy allows it", "grade": "pass", "evidence": "No AI language in docs/CONTRIBUTING.md, README.md, or PULL_REQUEST_TEMPLATE.md; no AI_POLICY file (search count 0)"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labels include 'good first issue'; opened by Aburke225 with author_association COLLABORATOR"},
      {"name": "Language matches fit profile", "grade": "pass", "evidence": "Repo primaryLanguage Python; relevant files are agent/orchestrator.py, agent/memory/session_store.py, agent/memory/context_manager.py"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/26",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "Default-branch commit 2026-09-16 by Aburke225, 4 days before today"},
      {"name": "Repo in use", "grade": "pass", "evidence": "isArchived: false; pushedAt 2026-09-16T21:50:20Z, within 90 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "Single defect with the fix source named ('SafetyMonitor.get_event_count() ... can supply per-type counts'), est. 2-4 hours"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: []; timeline shows only 4 label events by the opener; no linked PRs, no claim comments"},
      {"name": "AI-contribution policy allows it", "grade": "pass", "evidence": "No AI language in docs/CONTRIBUTING.md, README.md, or PULL_REQUEST_TEMPLATE.md; no AI_POLICY file (search count 0)"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labels include 'good first issue' and 'bug'; opened by COLLABORATOR Aburke225"},
      {"name": "Language matches fit profile", "grade": "pass", "evidence": "Repo primaryLanguage Python; relevant files are api/routes/health.py and safety/monitoring.py"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/12",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "Default-branch commit 2026-09-16 by Aburke225, 4 days before today"},
      {"name": "Repo in use", "grade": "pass", "evidence": "isArchived: false; pushedAt 2026-09-16T21:50:20Z, within 90 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "One bounded task in a single file tests/unit/test_prompt_templates.py, est. 3-5 hours; not an umbrella issue or support question"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: []; timeline shows only 6 label events by the opener; no linked PRs, no claim comments"},
      {"name": "AI-contribution policy allows it", "grade": "pass", "evidence": "No AI language in docs/CONTRIBUTING.md, README.md, or PULL_REQUEST_TEMPLATE.md; no AI_POLICY file (search count 0)"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labels include 'good first issue'; opened by COLLABORATOR Aburke225"},
      {"name": "Language matches fit profile", "grade": "pass", "evidence": "Repo primaryLanguage Python; target file is a pytest module"}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

Only one run was made. From `eval-run.txt`: "agreement: 16/20 scored items  (bar: 18/20: below the bar)"

**Issue analysis**

`issue-01`. From `eval-run.txt`: "issue-01  accept  reject   NO     failed: Scope fits a newcomer, Good-first-issue signal (preferred)". Gold label is `accept`; the rubric's decision was `reject`. The reasoning: the verdict rule states "Accept if every required check passes," and issue-01 failed the required check "Scope fits a newcomer" (it also failed the preferred check "Good-first-issue signal", but preferred checks "never change the verdict"). A single failed required check is enough to force a reject regardless of how the rest of the issue reads, which is why the rubric disagreed with the gold `accept` label here.

**Check rationale**

From `rubric.md`: "Scope fits a newcomer | Issue body and comment thread | Issue is one bounded piece of work: not an umbrella/tracking issue, not an unsettled design debate, not called out by a maintainer as touching core internals, and not a pure usage/support question | required"

This check is `required` rather than `preferred` because scope is one of the four families the lecture named as killing first contributions, and an oversized or unsettled issue can burn a newcomer's time before they ever get a review. It is written as a list of exclusions ("not an umbrella/tracking issue, not an unsettled design debate...") rather than a vague adjective like "reasonably scoped" so that someone else applying the rubric to the same issue body would land on the same grade.

**Trade-offs**

This check is what flipped issue-01 (gold `accept`, rubric `reject`) and issue-19 (gold `accept`, rubric `reject`, both noted as failing "Scope fits a newcomer" in `eval-run.txt`). The exclusion list is strict enough that an issue a human reviewer would still call "one bounded piece of work" can get graded as an umbrella/design-debate/support question and rejected instead. The check trades recall — it will reject some issues that are genuinely fine for a newcomer — for a verifiable, low-ambiguity pass condition that avoids accepting issues whose scope only *looks* bounded until a newcomer is inside them.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Fit: I have two years of experience and the issue is a genuine behavioral bug (a stale `ContextManager` cache returning a prior run's result) rather than a documentation or scaffolding task, which matches wanting to "explore and work on real issues." At an estimated 3-4 hours across three named files (`agent/orchestrator.py`, `agent/memory/session_store.py`, `agent/memory/context_manager.py`), it's a scope I can finish in the time I have without it turning into a multi-day investigation.

2. The verdict correctly identified the mechanical facts that make an issue safe to claim: the repo isn't stale (a default-branch commit 4 days ago), nobody is assigned or has claimed it, there's no AI-contribution ban in the docs, and it carries the "good first issue" label from a collaborator. What the rubric couldn't weigh is how the bug is actually described — that it names the specific root cause (stale cache) and the specific files involved, which is what told me the fix is traceable rather than just "sounds simple." That's a judgment call about the quality of the bug report itself, not something a pass/fail check on labels or dates can capture.

3. The anticipated difficulty is in reproducing the stale-cache condition across the two review runs before I can confirm the fix — the bug likely depends on ordering/state between calls to `ContextManager`, so I expect most of the effort to go into writing a reliable repro rather than the fix itself, which should be small once the state that isn't cleared is identified.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
