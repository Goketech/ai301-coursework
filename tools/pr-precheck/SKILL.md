---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question about one PR package: is this ready to submit? A PR package is a candidate pull request (its title, description, commit list, diff, and test evidence) read against the plan it claims to implement (with its deviation notes and test plan) and the issue that plan belongs to, including the repo's stated PR template and contribution policy. Grade exactly one package per run. Don't review code style, suggest a better fix, or grade the plan itself: the plan is the yardstick, not the subject. Never answer from gut feel; answer by executing the files in this directory.

## Inputs and modes

Run in exactly one of two modes.

- **Live mode**: the student's own PR before it goes out. Read these inputs, all from the student's working copy of their fork unless noted:
  - `plan.md`, the posted plan with its deviation notes and test plan.
  - The branch's diff: run `git diff main...HEAD` (three dots) from the working copy. Also run `git log main..HEAD --oneline` for the commit list.
  - The draft PR title and description in `pr_draft.md`.
  - The test evidence in `test_evidence.md`: the output of `make test-unit`, `make test-integration`, `make lint`, and `make typecheck`, plus the repro steps re-run before (on `main`) and after (on the branch).
  - The issue's thread, fetched from the scoped repo with `gh issue view <number> --comments`.
  - The repo's PR template (`.github/pull_request_template.md`) and `docs/CONTRIBUTING.md`, read from the working copy or fetched from the scoped repo.

  A house-chain student reads the house plan and the house repro pack in place of their own plan and repro; the same checks grade the same things. If `plan.md`, the diff, `pr_draft.md`, or `test_evidence.md` is missing, don't guess its contents: grade the checks that depend on it as unclear and name the missing file.
- **Eval mode**: a package bundle is the whole world. Every fact comes from the bundle text (repo facts, issue, thread highlights, plan context, and the candidate PR's title, description, commits, diff, and test evidence). Don't fetch anything or read any other file. Eval mode always grades a complete package: every check in the rubric, the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. Its `Repo:` line names the only repository whose PRs you may grade, and its house rules govern how evidence is read there: PR from the student's own fork branch, one PR per issue, the template always used. If the scope's `Repo:` line still carries an unfilled placeholder (angle brackets such as `<ORG>/<PATH-REVIEW-REPO>`), stop without grading. Tell the student to fill the `Repo:` line in `scope.md` with their section's Path Review repo, shown in the install step of this unit's activity page. If the package's issue or branch targets any other repository, refuse to grade and say which repo is in scope. Never guess a scope. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after grading, read `voice-guide.md`, the student's own rules for how they write upstream. Hold the outgoing PR title and description against each rule. In the summary, report every rule the draft breaks, quoting the rule and the offending phrase. The voice guide never changes the verdict: the rubric decides, and no rubric check reads the voice guide. Voice is personal, and the verdict must mean the same thing for every student. In eval mode, ignore `voice-guide.md` entirely.

## Component reads

- `rubric.md` defines the checks (name, evidence, pass condition, weight) and the verdict rule. Grade every check in it and no other.
- `references/evidence-guide.md` is the map: where each evidence family lives in a bundle and in a live working copy, and what good looks like there.
- `procedure.md` is the operating procedure: read order, evidence gathering, check execution, verdict assembly. Execute it as written, step by step. Where it's silent on a step you need, report the gap in the summary instead of improvising around it.

If `rubric.md` has no checks or `procedure.md` has no steps (only headings and instruction comments), refuse to grade. Say which file is empty and stop: a tool that invents checks at runtime produces noise that looks like judgment.

## Verdict and output

The verdict is binary: `accept` means ready to submit, `reject` means hold. There's no third verdict and no score; reservations go in the check evidence lines. Before the JSON you may give a short readable summary: one line per check with its grade and evidence, procedure gaps, and (live mode) voice-guide notes. End your reply with this fenced JSON block, valid and last, with nothing after it. The harness parses the last fenced JSON block.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote that decided it. "Looks fine" isn't evidence.
- Grade the thing, not the polish: a terse PR whose diff matches its plan can be ready, and a confident, beautifully formatted one can be hiding drift. Read the diff against the plan, the evidence against the test plan, and the description against the diff, never the formatting.
- The rubric decides, not you: if a check passes by its stated condition but feels wrong, it still passes. Note the tension in the summary; the fix belongs in the rubric.
- The procedure decides how, not you: follow `procedure.md` as written and report its gaps instead of papering over them.
- Unclear defaults to fail: treat `unclear` as the rubric's verdict rule directs. Where the rule is silent, an unverifiable claim is a failing one, because a PR you can't verify from the package isn't ready to submit.
- An honest shortfall isn't a failure: a PR that discloses and defers part of the issue, in the plan or the description, is graded against what it promised.
