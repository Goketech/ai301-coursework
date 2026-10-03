# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: in a practice submission (for example `eval/packages/calib-01.md`), the plan's cause statement sits under `## Candidate plan` (its `Cause` or `Diagnosis` lines), and the behavior it must explain sits under `## Repro evidence` (the numbered `Steps`, any `Control` runs, and the `Artifact` or timing output plus the `Expected` versus `Actual` lines). The `## Issue` body and `## Thread highlights` are context only: a confident thread diagnosis never overrides the repro block. Live, the same split holds: the cause is in the draft `plan.md`, the pinning behavior is the student's posted repro comment, and the issue thread is context.

What good looks like: the stated cause cites behavior the repro evidence actually shows and survives every control run. In calib-01 the push-status-refresh cause fits all four steps including the Esc-and-return control, so it passes. In calib-03 the plan adopts the thread's confident key-binding diagnosis, but the repro's timing matrix shows the 26-second cost with no pager in the loop at all (step 3) and instant seeks with bindings unchanged and colors off (step 2): the evidence pins highlighting work, not bindings, so the diagnosis fails no matter how polished the write-up is.

## Scope

Where it lives: the plan's bounding language under `## Candidate plan` (its `Change`, `Scope`, `In scope`, `Out` or `Not in scope`, and `Files and areas` lines), read against the `## Issue`'s observed failure and the `## Repro evidence` steps. The comment never bounds scope; only the plan does. Live, the same lines in the draft `plan.md` bound scope against the issue body and the posted repro.

What good looks like: one bounded change that serves the observed failure, with everything else explicitly deferred or marked out. Calib-01 is the anchor: a one-change fix in the push callback with an explicit `Out` line excluding push-status computation and other views' refresh behavior. A plan that bundles the fix with a migration, refactor, redesign, new feature, or cross-area rework the issue never asked for fails, even when its core fix is correct.

## Executability

Where it lives: the plan's `Files`, `Approach`, `Changes`, and ordered `Steps` lines under `## Candidate plan`. The repro block and the comment are not executability evidence: the question is whether the plan section alone lets someone else start. Live, the same lines in the draft `plan.md`.

What good looks like: a stranger can start without asking the author anything because at least one concrete file or area and one chosen approach are named in a workable order. Calib-01 passes while terse: it names `pkg/gui/controllers/sync_controller.go` and the single approach of adding the commits context to the post-push refresh scope. Calib-02 fails the same test: `poke around the editor components this weekend` names no file, chooses no approach, and defers every decision to build time.

## Test plan

Where it lives: the plan's `Test` or `Test plan` lines under `## Candidate plan`, mapped back onto the `## Repro evidence` steps, artifacts, and `Expected` outcome. Controls in the repro block (width-2 runs, no-background runs, no-toggle runs) are part of the map: a good test re-runs them unchanged. Live, the draft plan's test section mapped onto the posted repro comment's steps.

What good looks like: the test re-runs the repro's pinned steps or fixture and names the observable artifact that proves the fix, plus the controls that must stay unchanged. Calib-01 passes: re-run the repro steps and at step 3 the color must flip without leaving the view. Calib-02 fails (`undo works after toggling` names no artifact), and calib-04 is the borderline anchor for the same rule: a genuinely bounded plan whose whole test is `run the full test suite` names no observable outcome for the fix itself, so it fails this check even though the rest of the plan is solid.

## Honesty

Where it lives: certainty and uncertainty claims across `## Candidate plan` (its `Risk`, open questions, verification claims such as tool-by-tool support tables, cost language, and any deviation notes) read against what `## Repro evidence` and `## Thread highlights` actually establish. Confidence in the `## Candidate plan comment` that the plan section does not earn counts here too. Live, the same claims in the draft plan read against the posted repro and the live thread.

What good looks like: open questions, unverified support, and unmeasured costs are stated as such and left for review or follow-up. Calib-04 passes: it presents the absolute-prefix normalization as opt-in behavior for review given the documented gitignore semantics instead of claiming the semantics debate is settled. Calib-03 fails the mirror image: it presents the binding registration as traced and contained while the repro block rules that mechanism out, which is false confidence rather than an honest unknown.

## Comms

Where it lives: the `## Candidate plan comment` read against two things: the `## Thread highlights` maintainer signals (owner or collaborator direction, a posted test binary with a testing request, prior-art PRs, listed fix options) and the `## Repo facts` block's stated templates, contributing asks, and contribution policy including any AI-use disclosure rule. The plan body is not comms evidence. Live, the draft comment read against the live issue thread and the repo's docs and policy files.

What good looks like: the comment is thread-aware and convention-following. Calib-01 is the compact model: it cites the repro report, sketches the one-change fix, names the PR as the next step, and stays minimal per the CONTRIBUTING review-bandwidth note. Calib-04 shows both halves at once: it engages the owner's hard-fix note by scoping out of the `ignore` crate with reasons, and it satisfies the repo's AI policy with a human-in-the-loop statement written in the author's own words. A comment that never mentions explicit in-thread direction, or that omits a disclosure the repo-facts block requires, fails even when the plan body is strong.
