# Procedure: how this skill grades a plan package

## Read order

1. Read the repo-facts block first and note any stated AI-use disclosure rule, human-words comment rule, and bug-report or contribution asks. This sets the convention check before any plan content can charm it away.
2. Read the issue body second and note the observed failure in one sentence: what breaks, under what conditions.
3. Read the thread highlights third and note any explicit maintainer direction: a named culprit file, a posted test binary with a testing request, prior-art PRs or issues, or listed fix options. Mark threads with no direction as vacuous so later checks pass them without hunting.
4. Read the repro-evidence block fourth and note what behavior it pins down: the numbered steps, each control run and what it rules out, the artifact or output, and the expected-versus-actual pair. Do this before touching the plan so the plan is judged against the evidence, not against its own framing.
5. Read the candidate plan fifth and note its cause, scope bounds, files, approach, test lines, and any risk or unknown claims, each in the evidence family's own words.
6. Read the candidate plan comment last, the way a maintainer on the thread will read the posted comment: as the package's voice, not as extra plan content.

## Evidence gathering

For each family, pull the exact fact and record a one-line quote or step reference before grading any check:

1. Grounding: copy the plan's cause sentence and, beside it, the repro step plus control lines that must support it. If the plan names no cause, record that absence as the fact.
2. Scope: copy the plan's in-scope change and its out-of-scope or deferral lines, plus the file or area list. If no bound is stated, record the full change list as unbounded.
3. Executability: copy the named files or areas and the chosen approach in order. If either is missing or written as investigate-first language, record the verbatim hedge.
4. Test plan: copy the test lines and, beside each, the repro step or artifact it maps to and the expected observable. If no mapping exists, record that gap.
5. Honesty: copy every certainty claim (verified, traced, measured, will-fix language) and every stated unknown, risk, or opt-in flag, with the repro or thread line that settles or unsettles it.
6. Comms: copy the comment's repro citation, change sketch, and next step, plus the thread-direction note and the repo-policy note from the read-order pass. Grade the comment against those two notes, not against generic politeness.

## Check execution

1. Execute the rubric's checks in table order: grounded-cause, bounded-scope, executable, decisive-test, honest-unknowns, thread-engagement, convention-disclosure, then comment-ready.
2. Grade each check only from the gathered fact recorded for it. Do not re-read the whole package to rescue or sink a check; if the recorded fact is silent, the check is unclear, not a quiet pass.
3. When evidence for a check is genuinely absent (no cause stated, no files named, no test lines, no thread direction, no repo policy), grade unclear and record the absence as the evidence line. Never invent the missing fact, and never borrow a neighboring family's fact to fill the gap.
4. Grade the plan, not the polish: a terse complete plan passes and a long confident one fails when the recorded facts say so. When a check passes by its stated condition but feels wrong, it still passes; note the tension in the summary for the rubric author.
5. One failure is one failure: the same underlying defect may show in two checks (for example a vague plan failing both executability and test decisiveness), and each check is graded on its own condition without discount.

## Verdict assembly

1. Collect the per-check grades and apply the rubric's verdict rule exactly: accept if and only if every required check passes; preferred checks never enter the verdict.
2. Treat every unclear on a required check as fail. The only verdicts are accept (ready to post and build from) or reject (hold).
3. Quote the deciding fact in the output: for a reject, the evidence line of each failed or unclear required check; for an accept, the evidence line that most narrowly carried each required check.
4. Emit the short readable summary first (one line per check with its evidence, plus any procedure gaps noticed), then the fenced JSON block last with item, per-check name plus grade plus one-line evidence, and verdict. The JSON block must be present, valid, and last.
