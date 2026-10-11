# Procedure: how this tool grades a PR package

## Read order

1. Read the repo facts first (eval: the `## Repo facts` block; live: the repo's PR template and `docs/CONTRIBUTING.md`). Note every PR template section, checklist item, and required file (changelog, whatsnew), the repo's stated test or check commands, and any AI-use policy, word for word. This sets the standards checks before the PR's prose can charm them away.
2. Read the issue and thread highlights second. Note the observed failure in one sentence and any explicit maintainer direction.
3. Read the plan context third, before any part of the PR. List (a) the planned change, (b) the files or areas it names, (c) everything it scopes out or defers, (d) any deviation notes, and (e) each test-plan line and failure mode, plus the repro's recorded before. The plan comes first because every later check is a side-by-side against it.
4. Read the diff fourth, hunk by hunk, before the description. For each hunk, write its file and a short label of what it changes. Reading the diff before the description keeps the description's fidelity claims from framing what you see.
5. Read the commit list fifth and note any commit whose message signals work outside the fix ("misc cleanups", "fmt", "wip").
6. Read the test-evidence section sixth. Note each command run, its observable output, and whether it shows a before, an after, or both.
7. Read the PR title and description last. List every factual claim it makes about what changed, every template section it fills, any deferral it restates, and any AI disclosure.

## Evidence gathering

Record a one-line fact or quote for each pairing before grading anything:

1. Diff vs plan (plan-fidelity): put the hunk list from read-order step 4 next to the plan's files and scope list from step 3. Mark each hunk "planned", "test/record for the plan", "deviation noted", or "outside plan". Then mark each planned change "present", "deferred", or "missing".
2. Description vs diff (description-matches-diff): put each claim from step 7 next to the hunk or commit that delivers it. Mark any claim with no matching hunk, and any fidelity claim ("exactly", "no other changes") that a hunk marked "outside plan" contradicts.
3. Evidence vs test plan (decisive-evidence): put each test-plan line and failure mode from step 3 next to the evidence that addresses it. For the primary repro, record whether the inputs match the issue's failing path or only a control or unchanged path, and whether an observable before and after are shown. For every other failure mode, record the stated result, or record "never mentioned" if the evidence is silent on it.
4. Evidence vs repo checks (repo-checks-run): put the repo's stated check commands from step 1 next to the commands shown in the evidence. Record which ran, with what outcome, and any honest reason given for a check that didn't run.
5. Debris sweep (reviewable-diff): scan every added and removed line for debug output, commented-out code, dead functions, stray TODOs, whitespace-only or re-indent changes, blocks of identical lines removed and re-added with no change in the block, and import reshuffles. Before marking identical lines as churn, check whether they belong to a statement that contains a real change in the same hunk; if they do, they're context, not churn. Quote the first instance of each kind you find.
6. Template vs description (template-and-asks): put each template section, checklist item, issue-link ask, and required file from step 1 next to where the description or diff meets it. Record each one as met or absent.
7. Policy vs description (ai-disclosure): if step 1 recorded an AI policy that requires disclosure, quote the description's disclosure sentence or record its absence. If the repo states no AI policy, record "no AI policy stated".
8. Deferrals (honest-shortfall): quote any deferral or limit from the plan and description.

## Check execution

1. Run the rubric's checks in table order: plan-fidelity, description-matches-diff, decisive-evidence, repo-checks-run, reviewable-diff, template-and-asks, ai-disclosure, then honest-shortfall.
2. Grade each check only from the fact you recorded for it, applying the rubric's pass condition literally. Don't re-read the whole package to rescue or sink a check.
3. When evidence for a check is genuinely absent (no test-evidence section, no plan test plan), grade it unclear and write the absence as the evidence line. Never invent the missing fact. Vacuous passes (no template, no AI policy) are passes, not unclear.
4. Grade the thing, not the polish. A terse PR whose hunks all map to the plan passes. A confident, well-formatted one with an "outside plan" hunk fails. If a check passes by its condition but feels wrong, it still passes; note the tension in the summary.
5. Grade each check on its own condition. One defect can fail two checks (an unplanned flag that the description calls "exactly as planned" fails both plan-fidelity and description-matches-diff), and both fails stand.
6. A disclosed, deferred shortfall is not drift and not missing evidence. Grade fidelity and evidence against the plan with its deviation notes, not against the whole issue.

## Verdict assembly

1. Apply the rubric's verdict rule: accept if and only if every required check passes. Preferred checks never enter the verdict.
2. Count unclear on any required check as fail. The only verdicts are accept (ready to submit) and reject (hold).
3. Quote the deciding check. For a reject, that's the first failing or unclear required check in table order: lead the summary with its evidence line. For an accept, give the evidence line that most narrowly carried each required check.
4. Write the short readable summary first: one line per check with its grade and evidence, then any procedure gaps you noticed, then (live mode) any voice-guide notes. End with the fenced JSON block: item, every check's name, grade, and one-line evidence, and the verdict. Nothing follows the JSON block.
