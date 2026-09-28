# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Environment | The repro report's environment record, read against the issue's stated environment (its reported OS and program/tool version). | Pass if the report names a specific operating system and a specific program/tool version used to run the reproduction; if either differs from the issue's reported environment, the difference is called out explicitly. Fail if the OS or version is missing, vague ("a Mac", "the latest version"), or silently swapped without comment. | required |
| Backtrack | The repro report's steps/execution section: the sequence of actions and the exact command(s) it says were run. | Pass if the report gives the concrete sequence of actions and quotes the exact command(s) run, with exact flags, parameter values, or input content for whatever specifically triggers the bug, in enough detail that a stranger could reconstruct the same run from a cold start. A helper file's full text does not need to be pasted if the exact parameter values that define it are given (for example, an issue's own snippet plus the exact range or config values applied to it). Fail if steps stay in vague prose ("configured X", "set up the project") with no concrete values, a command is described but never quoted verbatim, or the reconstruction depends on state that is never shared or specified. | required |
| Input_similarity | The input used in the repro (file contents, payload, arguments), read against the input the issue describes. | Pass if the repro's input is the same as the issue's input, or an equivalent that preserves whatever property triggers the bug (any difference named). Also pass if the report is an honest, evidenced attempt: the input tried is a genuine, concrete effort aimed at the issue's stated trigger, and the report names what about that attempt may not have matched the trigger. Fail if the input is a different case that does not exercise the same trigger and the mismatch goes unacknowledged, or no input is given to compare at all. | required |
| Output_similarity | The repro's stated output/result (error text, exit code, panic, observed behavior), read against the output/behavior the issue describes. | Pass if the repro's output matches the issue's reported output, or is an equivalent instance of the same failure. Also pass if the report honestly documents a cannot-reproduce: it shows the actual output from a genuine attempt, states plainly that it does not match the issue's reported behavior, and never narrates that different output as confirming the issue. Fail if a different output or failure mode is presented as matching or confirming the issue's bug without acknowledging the difference, or if no real output is shown to compare at all. | required |
| Evidence | Documentation, logs, stack traces, or other artifacts in the package that show the situation the report describes, as opposed to prose assertion alone. | Pass if the package includes a concrete artifact (log excerpt, stack trace, pasted output) backing the report's stated result, whether that result is the failure occurring or a genuine, documented attempt that did not produce it. Fail if the only support is narrative claim with no artifact quoted, or if a claimed attempt has no real execution shown behind it. | required |
| Disclosure | The repo-facts block's contribution/AI-use policy, read against the claim and repro comments for a disclosure statement. | Pass if the policy states no requirement to disclose AI assistance in issue or PR comments (including "no stated AI policy," a permissive-with-responsibility policy, or a policy that only asks for disclosure in PR descriptions, not issue comments). Also pass if the policy does require disclosure and either comment states plainly that AI assistance was used and to what extent. Fail only if the policy requires disclosing AI assistance in comments and neither comment contains any disclosure statement. | required |
| Comms | The candidate claim comment (and repro comment), read against the specific issue they were posted to. | Pass if the comment is specific to this issue and this attempt: it references a concrete detail of the issue or repro (not generic praise or a line that could be pasted onto any issue) and does not demand exclusive assignment or promise a fix/timeline it has no grounds for. Fail if the comment is interchangeable boilerplate, is mostly flattery or enthusiasm with no issue-specific content, or promises a guaranteed fix or deadline. | required |

## Verdict rule

Accept only if every required check (Environment, Backtrack,
Input_similarity, Output_similarity, Evidence, Disclosure, Comms)
grades `pass`. A single `fail` on any of these checks holds the
package (reject). Treat `unclear` the same as `fail`: a check whose
evidence cannot be found or verified is not proof the package is ready
to post.
