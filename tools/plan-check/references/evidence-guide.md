# Evidence guide: where evidence lives in a plan package

<!--
The map for rubric.md's checks. Each family says WHERE the evidence
lives (in an eval package, then live) and WHAT GOOD LOOKS LIKE. The
eval package layout, top to bottom: header (source, captured date),
`## Repo facts`, `## Issue`, `## Thread highlights`, `## Repro
evidence`, `## Candidate plan`, `## Candidate plan comment`. The
candidate plan may be sectioned (### Diagnosis, ### Scope, ...) or a
few plain paragraphs ("Cause: ... Change: ... Test: ..."): always find
content by what it says, never by a heading, and never assume a part
exists that the package does not contain.
-->

## Diagnosis and grounding

Checks: diagnosis-grounded, fixes-the-cause.

Where it lives:
- The cause: in `## Candidate plan`, the sentence(s) saying why the
  bug happens. Often under `### Diagnosis` or after "Cause:", but it
  may be the plan's first paragraph or folded into a summary.
- What the cause must explain: `## Repro evidence`, its numbered
  steps and results, timings, artifacts, any "Control:" line, and the
  Expected/Actual pair at the end. Controls are the most decisive
  facts: they show what changes when the bug appears or disappears.
- Where a borrowed cause comes from: `## Thread highlights`. A
  non-maintainer comment claiming a root cause or a fix PR is a
  theory, not evidence.
- Live: the student's posted repro comment on the issue (or the house
  repro pack quoted in the drafts) and the issue thread.

What good looks like: the stated cause accounts for every step and
control in the repro evidence, and nothing there contradicts it
(calib-01: "the view's model is not refreshed" explains why the color
flips only after re-entry). Bad: the repro shows the bug persisting
with the blamed component removed (calib-03: 25.8 s with no pager at
all, yet the plan blames pager key bindings), or the plan adopts a
thread theory the repro evidence does not support. For
fixes-the-cause, good means the change acts on that cause; bad means
it masks the symptom (a bigger timeout, a swallowed error, a spinner,
a special case for the repro input).

## Scope

Check: bounded-scope (and preferred states-out-of-scope).

Where it lives:
- In `## Candidate plan`: the scope statement (`### Scope`, "In:" /
  "Out:", "Not in scope"), the change or approach steps (`### Changes`,
  `### Approach`, "Change:"), and any files, functions, or areas
  named.
- Also scan the whole plan and `## Candidate plan comment` for extra
  commitments ("while I'm in there", "also clean up", "and upgrade").
- Live: the student's `plan.md` and draft comment.

What good looks like: every piece of committed work traces back to
the stated cause, plus only the test and doc edits that change needs
(calib-01: one edit to the push callback's refresh scope; "Out: how
push status is computed, other views"). Bad: unrelated refactors,
renames, new options or features, dependency bumps, or fixes for
other issues riding along. An explicit "out of scope" line is nice
(preferred) but its absence is not a scope failure; a plan that names
an out-of-scope line and then does that work anyway fails.

## Executability

Check: executable.

Where it lives:
- In `## Candidate plan`: the change/approach steps and the named code
  location (file path, function, module, component, config site).
- Live: the student's `plan.md`.

What good looks like: a contributor who never read the thread could
open the named location and start the named edit without asking the
author anything (calib-01: "the push completion callback in
`pkg/gui/controllers/sync_controller.go` adds the commits context to
its post-push refresh scope"). Bad: the plan is a promise to
investigate ("poke around the editor components this weekend, figure
out where the undo history lives", calib-02), or names neither a
location nor a concrete edit.

## Test plan

Check: test-observes-fix.

Where it lives:
- In `## Candidate plan`: the test plan (`### Test plan`, "Test:",
  or a verification sentence anywhere in the plan, including any test
  added under the change steps).
- Compare against `## Repro evidence`: its steps and its
  Expected/Actual pair, which define the behavior that must flip.
- Live: the student's `plan.md`, read against their posted repro
  comment.

What good looks like: the test re-runs the reproduced behavior (or
asserts it in an automated test) and names the result that must now
differ (calib-01: "repro steps above; at step 3 the color must flip
without leaving the view", plus neighbouring paths that share the
callback). Manual checks are fine. Bad: a test that cannot see the
bug, such as "run `cargo test --workspace` and make sure nothing
regresses" (calib-04), "undo works after toggling" with no steps or
observable (calib-02), or a test of something other than the
reproduced behavior.

## Honesty

Check: honest-claims (and preferred names-risks).

Where it lives:
- Certainty language anywhere in `## Candidate plan` and
  `## Candidate plan comment`: "I traced", "confirmed", "verified",
  "this fixes", "all cases", "guaranteed".
- What backs it: `## Repro evidence` (what was actually observed) and
  `## Thread highlights` (where a claim was borrowed from).
- Stated unknowns: a risks/unknowns section, hedges ("likely",
  "I would present it as opt-in"), or a deviation note.
- Live: the drafts, plus any deviation note the student added to
  `plan.md` after the build diverged.

What good looks like: claims match their backing; a cause inferred
from the repro is stated plainly, and what is not known is said to be
unknown. Bad: "I traced this" when the cause is a thread commenter's
theory restated (calib-03's comment), a claimed verification the repro
evidence does not show, or a promise the plan does not back. A plan
with no risks section is not dishonest; a risks line is a preferred
extra.

## Comms

Check: comms-fit.

Where it lives:
- The words: `## Candidate plan comment` (live: the draft comment).
- The thread: `## Thread highlights`, especially comments tagged
  OWNER, MEMBER, or COLLABORATOR, and what they decide or ask
  ("working as intended", "the fix is hard", "discuss before a PR").
  "(no comments)" means there is no thread signal to honor.
- The repo: `## Repo facts`, the "contribution policy" line (limits
  on outside PRs, AI-use disclosure requirements, "comments must be
  in your own words") and the "bug reports" template line.
- Live: the issue thread, the repo's CONTRIBUTING file, issue
  templates, and any AI policy file.

What good looks like: the comment says what the plan actually does
and visibly works with the thread and the repo's rules (calib-01:
"keeping it minimal given the review-bandwidth note in CONTRIBUTING";
calib-04: engages the owner's "the fix is hard" note and gives the
AI-use disclosure the repo's policy requires). Bad: pushing ahead
against a maintainer's stated direction, announcing a PR when the
policy limits outside PRs or asks for discussion first, a missing
disclosure the policy requires, or a comment promising a different
change than the plan. Generic enthusiasm with no thread or policy
signal to honor is not a comms failure by itself (calib-02's "Wish me
luck!" passes comms-fit; that package is held by executable and
test-observes-fix).
