# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**https://github.com/codepath/pathreview-ai301-fa26-s3/pull/133**

[Link to the pull request you opened. It must be the pull request's own page on the Path
Review repo, not your fork's branch page.]

**Branch**

`fix/15-agent-session-state` (fixes issue #15)

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run (20 packages): `agreement: 18/20 scored items  (bar: 18/20: PASS)`, with `categories: clear-accept 5/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. Misses: `pkg-02  clear-accept  accept  reject   NO     failed: reviewable-diff` and `pkg-05  clear-accept  accept  reject   NO     failed: decisive-evidence`.
2. Partial run after revising: `--only pkg-02,pkg-05,pkg-18,pkg-12,pkg-07,pkg-04,calib-04 --include-calibration`, giving `agreement: 6/6 scored items`, with calib-04 also agreeing (`calib-04  calib  reject  reject   yes`).
3. Full confirming run, saved with `--save-run eval-run.txt`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, with `categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.

**Package analysis**

**pkg-05** (nushell/nushell#18848, clear-accept). Gold label: `accept`. On my first full run, my rubric decided `reject`, failing only `decisive-evidence`. The grader's evidence line was: "Issue-script before/after shown, but the plan's second scenario (same-name same-key: one row plus a warning) has only the claim 'Same-key redefine prints the one-time warning', with no observable output."

My rubric read it that way because the first version of `decisive-evidence` required "an observable after, paired with an observable before ... for every failure mode the plan's test plan names". pkg-05's plan lists two scenarios ("re-run the issue's script, expect both `atuin` rows; re-run with two same-name same-key bindings, expect one row plus a warning"). The evidence shows before and after tables only for the first. My check treated a stated result for the second scenario as a bare claim, the same as pkg-04's "tested locally, colors work now".

That was too strict. The primary repro is decisively shown, and the secondary scenario has a specific stated outcome, not silence. After I revised the check, pkg-05 grades `accept`, matching gold.

**Check rationale**

From `tools/pr-precheck/rubric.md`:

> | decisive-evidence | The test-evidence section (and any before/after in the description) read against the plan's test plan and the repro steps | Pass if the evidence re-runs the plan's primary repro (the failing path the issue reported, with the same inputs or fixture) and shows its observable after, paired with an observable before or the repro's recorded before, and every other failure mode the plan's test plan names is addressed with a stated result (output, a named passing test, or a specific observed outcome). Fail if the primary repro's evidence is a claim without observable output ("tested locally, works now", "verified on my machine"), if it exercises only a control or an unchanged path, or if a failure mode the plan's test plan names is never mentioned in the evidence. | required |

It reads this way because of two packages pulling in opposite directions. My first version demanded observable before/after output "for every failure mode the plan's test plan names", which wrongly rejected pkg-05. Just dropping the multi-failure-mode condition would have lost calib-04, whose plan names both failure modes but whose evidence "re-runs repro 1 only, silent on the --exec abort the plan named".

So I split the condition. The primary repro must show observable output. Every other named failure mode must at least be addressed with a stated result, and a failure mode that is "never mentioned in the evidence" still fails. That keeps the line between pkg-05 (addressed) and calib-04 (silent). I rejected the simpler option of grading only the primary repro, because it would accept calib-04.

**Trade-offs**

Loosening `decisive-evidence` (and, in the same revision, `reviewable-diff` for pkg-02's re-rendered `export_html(` call lines) could flip rejects that agreed before. So I re-ran canaries with `--only` from each category the change touched:

- not-tested: `pkg-04` and `pkg-07`, plus `calib-04` as a free trap check
- unreviewable: `pkg-12` and `pkg-18`, where pkg-18's re-printed `println!` blocks are the case the churn rule must still catch

All stayed `reject`/`yes`, and the confirming full run kept every category at full marks (`not-tested 4/4`, `unreviewable 3/3`).

The case I accept it will miss: a PR whose secondary failure mode is covered only by an unverified one-line statement now passes, as long as the primary repro is shown. A dishonest "the other case works too" would slip through `decisive-evidence`. I'm trading that for not holding honest, terse PRs like pkg-05.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
