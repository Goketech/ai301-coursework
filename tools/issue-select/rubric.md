# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer alive | Repo facts: last 5 default-branch commit dates; maintainer first-response sample; author_association badges in the issue thread | At least one of the last 5 default-branch commits is dated within 90 days of the capture/today date, OR a maintainer (Owner/Member/Collaborator) replied to an issue within 30 days | required |
| Repo in use | Repo facts: archived flag, last push to any branch, latest release date | Repo is not archived, AND (last push within 90 days OR a release within 180 days) | required |
| Scope fits a newcomer | Issue body and comment thread | Issue is one bounded piece of work: not an umbrella/tracking issue, not an unsettled design debate, not called out by a maintainer as touching core internals, and not a pure usage/support question | required |
| Nobody already on it | Assignees, linked PRs, claim comments, label-add dates | No assignee, no open linked PR, and no claim comment ("I'll take this", "working on this") older than 14 days without abandonment evidence — except on Path Review, where other students' claim comments never block the issue per the house rule | required |
| AI-contribution policy allows it | CONTRIBUTING.md / AI policy files / PR template, under "contribution policy" | No outright ban on AI-generated contributions; disclosure, testing, or human-review conditions are fine and do not fail this check | required |
| Good-first-issue signal | Issue labels; who opened the issue | Issue carries a "good first issue" / "help wanted" label, or was opened or endorsed by a maintainer | preferred |
| Language matches fit profile | Issue body / repo's primary language | Issue's primary language is Python, JavaScript, or TypeScript | preferred |

## Verdict rule

Accept if every required check passes. Preferred checks never change the
verdict; they only rank accepted issues (more preferred passes ranks
higher). `unclear` on any required check counts as `fail` for that
check: a first issue you cannot verify is not a first issue you should
take.
