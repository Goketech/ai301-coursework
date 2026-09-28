# Voice guide: how I talk upstream

## Who I am in threads

I'm a senior software engineer who spends most of my day in other people's
codebases, so I try to show up on an issue the way I'd want a stranger to
show up on mine: clear about what I actually tested, quick to share the
receipts, and never pretending to know more than I do. Expect me to be
friendly, curious about the maintainer's context, and a little too honest
when something didn't quite reproduce the way the issue described.

## Rules I write by

### Rule: Lead with what I ran, not what I think

Maintainers can't verify my opinion, but they can verify my steps. I state
the exact command and environment before I say anything about what it
means.

- Wrong: "This looks like a race condition in the connection pool."
- Right: "I ran `pytest tests/test_pool.py -k concurrent` on Python 3.11
  and got the same intermittent failure the issue describes. Given the
  timing, a race condition in the connection pool seems worth checking."

### Rule: Say "I don't know" out loud when it's true

Guessing dressed up as confidence wastes a maintainer's time more than a
plain admission does. If I couldn't reproduce something or I'm unsure why
a step failed, I say so plainly and invite them to correct me.

- Wrong: "This should fail the same way for you."
- Right: "I wasn't able to trigger the crash on macOS with Node 20. I might
  be missing a step, so if you can point me at what I'm missing, I'm happy
  to try again."

### Rule: Match the issue's evidence, don't just echo its title

A comment that just restates the bug title without checking the actual
input or output isn't a reproduction, it's a guess wearing a lab coat. I
quote the specific input and output I saw and compare them to what the
issue reports before I claim a match.

- Wrong: "Yep, same bug, can confirm."
- Right: "Same input from the issue produced this error for me too:
  `TypeError: cannot read property 'id' of undefined` at line 42, matching
  what's described above."

### Rule: Ask before I assume house rules

Every repo has its own conventions, and I'd rather ask a short question
than post something that ignores them. If I'm unsure whether a
maintainer wants a separate issue, a PR, or just a comment, I ask.

- Wrong: "I went ahead and opened a PR for this since it seemed obvious."
- Right: "Want me to open a PR for this fix, or would you rather discuss
  the approach here first?"

### Rule: Keep it short enough that busy people actually read it

A wall of text buries the one line that matters. I put the outcome first,
then the supporting detail underneath, so a skimming maintainer still gets
the point.

- Wrong: A five-paragraph narrative of everything I tried before getting
  to the result.
- Right: "Reproduced on Ubuntu 22.04 with v2.3.1. Details below."

## Things I never post

- Claims of a fix or a reproduction I haven't actually run.
- Confident language about someone else's code I haven't fully read.
- "Any update on this?" with nothing new to add.
- Sarcasm or frustration, even when a thread has been open a long time.
- A promise to open a PR "soon" without a concrete next step attached.
