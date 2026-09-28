# Evidence guide: where proof lives in a reproduction package

## Environment

- **Where it lives.** Eval bundle: the environment line or section at
  the top of the candidate repro report; the issue's own target lives
  in the issue context (the version/OS fields the reporter filled in)
  and in the repo-facts block's template asks. Live mode: the
  environment record in the student's draft repro file; the issue's
  target in the issue body on GitHub; what the record must contain in
  the repo's bug-report template.
- **What good looks like.** The record names the version under test
  and the platform it ran on (plus install method when the template
  asks), and it lines up with the issue: either the same target, or
  the difference is named in so many words ("the issue was filed
  against 4.53.2; I tested the current release"). A record you cannot
  place against the issue's target — or no record at all — is not an
  environment, however good the rest of the report is.

## Steps

- **Where it lives.** Eval bundle: the preparation and steps portion
  of the candidate repro report, including any input files, configs,
  or commands it quotes. Live mode: the same sections of the draft
  repro file.
- **What good looks like.** A stranger with the recorded environment
  could replay the run: every command exact, every input or config
  either shown, small enough to retype, or pinned to an exact public
  source ("the issue's snippet verbatim", "the script from the issue
  with rangeStart 19" counts as fully specified — the stranger reading
  the thread has the issue too). Filler that cannot change the trigger
  (the valid part of a config whose quoted invalid section is the
  trigger) may be described rather than quoted. Steps fail the moment
  they lean on something the stranger cannot have (a private repo, an
  unshared config) or compress real work into prose ("set up the
  project", "configure as usual").

## Behavior shown

- **Where it lives.** Eval bundle: the artifacts inside the candidate
  repro report — output excerpts, logs, screenshots — read against the
  behavior described in the issue context (the issue body's error
  text, panic trace, exit behavior, or observable symptom). Live mode:
  the artifacts in the draft against the issue body on GitHub.
- **What good looks like.** The artifact shows the issue's failure
  with the same signature: the same error message or class, a panic
  where the issue reports a panic (not a graceful validation error), a
  crash where the issue reports a crash (not garbled-but-alive
  output). Compare the artifact to the issue line by line before
  believing the report's narration; an adjacent failure dressed in
  confident prose is the classic trap. For a cannot-reproduce, the
  artifacts must show a faithful attempt at the issue's exact trigger
  and what actually happened instead. An artifact that only proves the
  program runs shows nothing.

## Honesty

- **Where it lives.** The seam between the report's conclusions (its
  Actual, Analysis, or summary lines, and any confidence language) and
  the artifacts it actually shows. In an eval bundle both sit inside
  the candidate repro report; live, both sit in the draft.
- **What good looks like.** The headline outcome is covered by
  something shown: "reproduced" only over a matching artifact, "on
  both versions" only if both runs appear, a root cause only if
  evidence for it is on the page. Side observations from the same
  session (a control variant's one-line result) may be stated without
  pasted output when they are not the deciding proof. An honest
  cannot-reproduce — what was attempted, what happened, what differed
  from the reporter's setup, what a triggering setup might need — is a
  passing outcome. Red flags: "I verified", "guaranteed", "definitely
  the cause" with nothing shown; conclusions that generalize past the
  run performed; an Expected/Actual pair that contradicts the artifact
  above it.

## Comms

- **Where it lives.** Eval bundle: the candidate claim comment read
  against the issue context, and both comments read against the
  repo-facts block (bug-report template asks; contribution policy,
  including any stated AI-use policy). Live mode: the draft comment(s)
  against the issue thread, the repo's CONTRIBUTING.md and issue
  templates, and any AI policy file the repo publishes.
- **What good looks like.** The claim could only have been written for
  this issue: it names the observed behavior, the version tested, or a
  concrete finding, and states a realistic next step. Boilerplate
  ("kindly assign me", "I will fix it in 2 days guaranteed", bare +1)
  reads as interchangeable and over-promising next to that. Policy
  compliance follows what the policy literally demands of the comment
  text (course packages and drafts count as AI-assisted work): a
  disclosure-required policy needs a visible disclosure in the
  comment; a comments-must-be-human-written-in-own-words policy is
  satisfied by a comment in the student's own voice, with no
  disclosure owed; a permissive or responsibility-only policy — or no
  stated policy — asks nothing extra of the comment.
