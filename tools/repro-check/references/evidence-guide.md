# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: in an eval bundle, the repro report's `Environment:`
line, read against the issue section's own stated OS/version (if the
reporter gave one) and the repo-facts block's latest release. In live
mode, the draft repro comment's environment line, read against the
issue thread's stated environment and the repo's bug-report template
fields.

What good looks like: a specific OS (with version) and a specific
program/tool version actually run, not a stand-in like "a Mac" or "the
latest version." If the version or OS differs from what the issue
targets, the report names the difference instead of leaving it for the
reader to notice.

## Steps

Where it lives: in an eval bundle, the repro report's numbered
steps/command block, read against the issue's own "steps to
reproduce." In live mode, the same block in the draft, read against
the issue thread.

What good looks like: every step is a literal command or config value,
not a paraphrase ("configured X" with no config shown), so a stranger
starting cold could copy them and land in the same state. The specific
step that creates the issue's triggering condition (the argument, input
shape, or setting the bug depends on) is present and unmodified, not
skipped or swapped for a similar-looking one.

## Behavior shown

Where it lives: in an eval bundle, the repro report's pasted output,
log excerpt, or transcript, read against the issue section's "current
result" / "expected result" (or equivalent). In live mode, the draft's
pasted output, read against the issue thread's own pasted output or
error text.

What good looks like: the artifact shows the same failure the issue
reports (the same error message, the same exit code or crash class),
not an adjacent symptom produced by a slightly different input or
command. A control run (the same command with the triggering
ingredient removed) is a strong signal, since it shows the artifact
only appears when the trigger is actually present.

## Honesty

Where it lives: in an eval bundle, the repro report's stated
conclusion ("expected... actual..."), read against what the artifacts
above it actually show. In live mode, the draft's closing claim, read
the same way against its own pasted output.

What good looks like: the words claim exactly what the artifact
supports, no more. A real attempt that honestly says "I could not
reproduce this" and names what differed (environment, input, version)
counts as a pass. A confident diagnosis, a root cause, or "guaranteed
reproducible" with no artifact underneath it does not.

## Comms

Where it lives: in an eval bundle, the candidate claim comment and
repro comment, read against the repo-facts block's bug-report template
asks and its contribution/AI-use policy. In live mode, the draft, read
against the repo's actual CONTRIBUTING.md, issue template, and any
stated AI-use policy on GitHub.

What good looks like: the comment says something specific to this
issue and this attempt, not boilerplate generic enough to paste onto
any issue, and it does not promise a fix or a timeline it cannot back
up. If the repo's policy requires disclosing AI assistance, the
comment discloses it plainly; silence where disclosure is required
fails this check even if every other proof check passes.
