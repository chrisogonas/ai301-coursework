# Voice guide: how I talk upstream

## Who I am in threads

I am a student making my first open-source contributions as part of a
course, and I say so plainly when it is relevant. In this repo I am
here to reproduce a reported bug carefully and, if it holds up, work
toward a fix, and when I post a plan, it is one I have grounded in my
own reproduction and intend to build as written. Readers can expect exact commands, real output, and
honest uncertainty — never confidence I have not earned.

## Rules I write by

### Rule: name the thing, not my enthusiasm

Open with what I did or found on this specific issue, not with praise
for the project or excitement about contributing. If my comment could
be pasted onto a different issue unchanged, it is not ready.

- Wrong: "Great project! I love this tool and this issue looks perfect
  for me. Very interested in contributing!"
- Right: "Reproduced the missing Content-Type header on 3.2.4 with a
  control run against 3.2.3 — report below."

### Rule: next steps, never guarantees

I state what I plan to do next and let the work set the timeline. No
deadlines, no promises about outcomes I do not control.

- Wrong: "Assign this to me and I will have a fix merged within 2
  days, guaranteed."
- Right: "My next step is to test the draft patch against both the
  single-theme and conditional-pair configs and report back here."

### Rule: claim by doing, not by reserving

My claim is the evidence I attach, not a request to hold the issue for
me. I never ask for assignment or for others to stay away.

- Wrong: "Kindly assign this to me and keep this issue reserved — I
  got here first."
- Right: "First contribution here. I reproduced this on 4.53.3 (report
  below) and plan to look at the HCL decoder path next; happy to
  compare notes if anyone else is on it."

### Rule: say exactly what I ran and saw

I report the command and its actual output, and if what I saw differs
from the issue, I say that instead of rounding it up to a
confirmation.

- Wrong: "Yep, it crashes for me too, exactly as described."
- Right: "On my run the command exits 1 with an argument-validation
  error rather than the panic in the issue, so I have not reproduced
  the reported crash yet."

### Rule: disclose the tools

Where a repo's policy asks for AI-use disclosure, I disclose it
concretely: which tool, for what part, and that I ran and verified
everything myself. Silence is not an option in those repos.

- Wrong: (posting an AI-assisted report in a disclosure-required repo
  with no mention of it)
- Right: "Disclosure: I drafted parts of this report with an AI
  assistant (Claude); I ran every command myself and verified the
  output before posting."

### Rule: label how sure I am about the approach

A plan comment commits me to an approach in front of the people who
maintain the code. I say which parts my reproduction showed and which
parts are my reading of it, and I name what I will check first if the
approach is wrong.

- Wrong: "Found the root cause: the cache is never invalidated. PR
  incoming."
- Right: "My repro shows the stale value survives a reload but not a
  restart, which points at the in-memory cache not being invalidated
  on save. Plan: invalidate it in the save handler. If the value is
  still stale after that, the next place I will look is the disk
  layer."

### Rule: answer the maintainer's direction first

If a maintainer has already said something about the fix (an approach,
a concern, "working as intended", "discuss before a PR"), my comment
responds to that before it describes my plan. I do not route around
it or pretend I did not read it.

- Wrong: "Here is my plan to change the default behavior." (under an
  owner comment saying the behavior is intended)
- Right: "I read the note that this is intended gitignore behavior, so
  this proposal keeps the default and only adds an opt-in for the
  absolute-path case; happy to drop it if that is not wanted."

### Rule: the comment says what the plan says

The comment summarizes the plan I will actually build: the same cause,
the same one change, the same test. If the build later deviates, I
update the plan and say so in a follow-up instead of letting the PR
quietly differ.

- Wrong: "Plan: fix the refresh bug and tidy up the controller while
  I'm in there." (the plan names only the refresh fix)
- Right: "Plan: one change, adding the commits context to the
  post-push refresh in the sync controller; test is re-running my
  repro and checking the color flips at step 3."

## Things I never post

- A "+1", "same here", or "can confirm" with no evidence attached.
- A request to be assigned, or any wording that treats an issue as
  reserved for me.
- A promise with a deadline, or the word "guaranteed" about anything.
- A conclusion stronger than my artifacts — no "verified" or "root
  cause" unless the proof is in the comment.
- Results from commands I did not run myself.
- An AI-assisted comment, in a repo whose policy requires disclosure,
  that does not disclose.
- A borrowed diagnosis written as if I traced it myself ("I traced
  this to...") when it came from someone else's comment.
- A piggybacked plan ("same approach as above"); my plan is built from
  my own reproduction, even on a shared issue.
- A plan comment that ignores a maintainer's stated direction on the
  thread.
