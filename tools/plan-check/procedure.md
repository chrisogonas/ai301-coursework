# Procedure: how this skill grades a plan package

<!--
Group 17's worksheet procedure, turned into executable steps. Changes
from the worksheet, each driven by a calibration package:
- Read order: the worksheet read the plan first, so its story framed
  everything after it and calib-03's wrong cause sounded right. The
  repro evidence is now read BEFORE the plan, and the facts it pins
  down are written down before the plan can color them.
- Evidence gathering: the worksheet said "execute the test plan". The
  grader never runs anything; it compares what the plan says against
  what the package already shows.
- Check execution: the worksheet looked for named sections
  ("Diagnosis", "Scope"). Plans may be one paragraph (calib-01), so
  every step finds content by what it says, not by its heading, and
  never assumes a part exists that the package does not contain.
These are steps for GRADING a plan, not for writing or fixing one.
-->

## Read order

1. Identify the mode. Eval mode: the input is a package bundle; it is
   the whole world, so fetch nothing and ignore `scope.md` and
   `voice-guide.md`. Live mode: the input is the student's `plan.md`,
   draft plan comment, and issue URL; `scope.md` has already been
   checked per SKILL.md before this procedure starts.
2. Read the issue (title, body, labels) first. Note in one line the
   reported behavior and the expected behavior.
3. Read the repro evidence second, before anything the plan says.
   Write down, as a numbered list of facts:
   - each step and its observed result (including timings);
   - every control: a variation where the bug did or did not appear
     (e.g. "with colors off it is fast", "without the toggle undo
     works");
   - the expected vs actual result.
   These facts are the yardstick for the diagnosis and test checks.
   Reading them before the plan keeps the plan's story from deciding
   what the evidence "means".
4. Read the thread highlights third. Note every comment by an OWNER,
   MEMBER, or COLLABORATOR and what it asks or decides (e.g. "working
   as intended", "discuss first", "the fix is hard"). Separately note
   any non-maintainer comment that proposes a cause, marked as an
   unverified theory.
5. Read the repo-facts block fourth. Note the contribution policy
   asks, any AI-use disclosure rule, and any limit on outside PRs.
   Write "none stated" when a field asks nothing.
6. Read the candidate plan fifth, all of it, whatever its layout
   (sections, a paragraph, or bullets).
7. Read the candidate plan comment last.

## Evidence gathering

Pull each item below from the parts read above, using
`references/evidence-guide.md` for where each family lives. Find each
item by what the text says, not by a heading: a plan with no
"Diagnosis" heading may still state its cause in its first sentence.
Record each item as a short quote or "absent". Never write down
content the package does not contain.

1. Stated cause (for diagnosis-grounded): quote the sentence(s) in
   the plan that say why the bug happens. Note whether the plan
   presents it as its own reading of the evidence or adopts it from a
   thread comment.
2. Cause vs evidence (for diagnosis-grounded): for each repro fact
   from Read order step 3, mark whether the stated cause explains it,
   contradicts it, or ignores it. Pay most attention to controls: a
   control that removes the blamed component while the bug persists
   is a contradiction.
3. Proposed change (for fixes-the-cause, bounded-scope, executable):
   quote what the plan says it will change, where (file, function,
   module, component), and how. List every distinct piece of work it
   commits to, including tests, docs, and any "while I'm here" extras.
4. Test plan (for test-observes-fix): quote how the plan says success
   will be checked. Note which repro step(s) it re-runs or asserts,
   and the observable result it expects. If it re-runs none, record
   "does not exercise the repro".
5. Certainty claims (for honest-claims): quote every phrase in the
   plan and comment that claims tracing, confirming, verifying, or
   guaranteed results ("I traced", "confirmed", "fixes all"). Next to
   each, note what in the package backs it, or "no backing".
6. Comment vs thread and repo (for comms-fit): put the maintainer
   notes from Read order step 4 and the policy notes from step 5 next
   to the plan comment. Note for each whether the comment honors,
   ignores, or contradicts it. Also note whether the comment describes
   the same change the plan describes.
7. Out-of-scope statement and risks (for the preferred checks): quote
   any "not in scope" / "won't touch" line and any named risk, unknown,
   or neighbouring behavior to check, or record "absent".

Live mode only: the repro evidence is the student's posted repro
comment on the issue (or, on the house issue, the repro pack as quoted
in the drafts); the thread is the live issue thread; repo facts come
from the repo's CONTRIBUTING file, issue templates, and any AI policy
file. Record the source of each item.

## Check execution

1. Run the checks in the rubric's table order: diagnosis-grounded,
   fixes-the-cause, bounded-scope, executable, test-observes-fix,
   honest-claims, comms-fit, then the preferred checks.
2. For each check, apply only the rubric's pass condition to the
   evidence items recorded for it in Evidence gathering. Do not add
   requirements the pass condition does not state (for example, do not
   require automated tests, file lists, or section headings).
3. Grade `pass` when the pass condition is met, `fail` when it is not.
   Grade `unclear` only when the recorded evidence genuinely supports
   both readings; say what the two readings are.
4. When the evidence a check needs is absent from the package (no
   stated cause, no test plan, no location), grade `fail`, not
   `unclear`, and record "absent" as the evidence. Do not infer or
   invent the missing part.
5. Grade every check, even after a required check has already failed:
   the full list is the feedback the student needs.
6. A check may be graded from the recorded evidence without
   re-reading the package. Re-read the relevant part only when the
   recorded quote is too short to decide the pass condition.
7. For each check, write one evidence line: the quote or fact that
   decided the grade (e.g. "repro step 3: 25.8 s with no pager, but
   the plan blames the pager bindings").

## Verdict assembly

1. List the grades of the `required` checks.
2. If every required check is `pass`, the verdict is `accept`.
3. If any required check is `fail` or `unclear`, the verdict is
   `reject`. `unclear` counts exactly like `fail`.
4. Ignore the preferred checks when deciding; still report their
   grades.
5. In the readable summary, name the deciding check(s): for a reject,
   every required check that did not pass, with its evidence line;
   for an accept, state that all required checks passed.
6. In live mode, add any voice-guide rule the draft comment breaks to
   the summary, quoting the rule; it does not change the verdict.
7. Note any step of this procedure that did not fit the package (a
   procedure gap) in the summary.
8. Emit the JSON block from SKILL.md last, with every check (required
   and preferred) in rubric order and the verdict from step 2 or 3.
