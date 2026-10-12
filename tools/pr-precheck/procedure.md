# Procedure: how this tool grades a PR package

<!--
Rewritten from the Unit 3 plan-check procedure for a PR package. The
key change is the read order: the plan is read BEFORE the diff, and
the diff BEFORE the description, so the description's story cannot
decide what the diff "means" (calib-03's "implements the plan exactly"
reads true until the property.js hunk is set beside the plan's file
list). Most gathering steps are side-by-side pairings: diff against
plan, test evidence against test plan, description against diff,
PR against repo asks. These are steps for GRADING a PR, not for
writing or fixing one. The grader never runs the code; it compares
what the package says against what the package shows.
-->

## Read order

1. Identify the mode. Eval mode: the input is a package bundle; it is
   the whole world, so fetch nothing, run nothing, and ignore
   `scope.md` and `voice-guide.md`. Live mode: SKILL.md's scope check
   has already passed; the inputs are the invoked files (`plan.md`,
   the test evidence file, the PR draft file), `git diff main...HEAD`
   and `git log main..HEAD`, the issue thread, and the repo's PR
   template and `docs/CONTRIBUTING.md`.
2. Read the repo facts first (eval: `## Repo facts`; live: the PR
   template and `docs/CONTRIBUTING.md`). Write down every stated PR
   ask as a numbered list: template sections, checklist items,
   "closes #N" link, changelog/whatsnew/release-notes entry, title
   convention, tests-with-fixes rule, checks to run (suite, lint,
   format, pre-commit). Separately list every contribution-policy
   rule: AI-use disclosure, own-words rule, CLA, vouch or
   discussion-first gate, stated technical rules. Write "none stated"
   for an empty field.
3. Read the issue and the thread highlights second. Note in one line
   the reported behavior and the expected behavior. List every OWNER,
   MEMBER, or COLLABORATOR comment and what it asks or decides.
4. Read the plan third, before anything in the candidate PR (eval:
   `## Plan context`; live: `plan.md`). Write down:
   - the planned change, as a list of distinct pieces of work;
   - the files or areas the plan names;
   - the "not in scope" items;
   - the test plan, as a list of distinct items (each repro or failure
     mode to re-run with its expected result, each control, each
     suite or command);
   - the reproduction steps and the expected-after result;
   - every deviation note (live: the deviation notes in `plan.md`).
   This list is the yardstick for the side-by-side checks. Reading it
   before the PR keeps the PR's story from redefining the plan.
5. Read the diff fourth (eval: `### Diff`; live: `git diff
   main...HEAD`). List every changed file, and for each file every
   hunk, in one line each (what the hunk does).
6. Read the commit list fifth (eval: `### Commits`; live: `git log
   main..HEAD`).
7. Read the test evidence sixth (eval: `### Test evidence`; live: the
   test evidence file, e.g. `test_evidence.md`). List every command or
   check shown and its observed before and after.
8. Read the PR title and description last (eval: `### Title`,
   `### Description`; live: the PR draft file, e.g. `pr_draft.md`).
   List every factual claim it makes, and any deviation note or
   disclosed limit it contains.

## Evidence gathering

Pull each pairing below from the lists made in Read order, using
`references/evidence-guide.md` for where each family lives. Record
each item as a short quote or "absent". Never record content the
package does not contain.

1. Hunks vs plan (plan-fidelity, diff-reviewable): for every hunk from
   Read order step 5, mark it `planned` (carries out a planned piece
   of work), `support` (a test, doc, or changelog edit the planned
   change needs), `noted` (covered by a deviation note in the plan or
   description; quote the note), or `unplanned` (none of these; say
   what it does). Flag any `unplanned` hunk that touches an item on
   the plan's "not in scope" list.
2. Plan vs hunks (plan-fidelity): for every planned piece of work and
   every file the plan names, mark it `present` (a hunk carries it
   out), `noted` (a deviation note says it was dropped or changed,
   with why), or `missing`.
3. Description claims vs diff and evidence (description-truthful):
   next to every claim from Read order step 8, write what in the diff
   or test evidence backs it, or "contradicted by ..." or "no
   backing". Pay most attention to fidelity claims ("exactly the
   plan", "no changes beyond it") and inclusion claims ("tests
   added", "changelog included").
4. Test evidence vs test plan (repro-verified): first sort the
   test-plan items from Read order step 4 into bug items (the
   reproduction and each failure mode or bug behavior the plan names)
   and supporting items (controls, smoke checks, neighbouring
   behaviors; suite commands go to repo-checks-run). Mark each bug
   item `shown` (the evidence shows the command and an observable
   before and after, with the after matching the expected result),
   `disclosed` (stated as not run, with a reason), or `missing`. Mark
   each supporting item `reported` (shown, or stated in a sentence as
   checked with its result), `disclosed`, or `missing`. Note whether
   the shown run exercises the code path the diff changed.
5. Reported checks vs requested checks (repo-checks-run): for every
   check the repo facts or the plan ask to run, quote where the test
   evidence or description reports it with the command or suite named
   and its outcome, or record "not reported" or "reported without
   naming the command".
6. Debris scan (diff-reviewable): scan every hunk for debug prints or
   logging, commented-out code, dead or unused additions, whitespace
   or formatting churn on lines the fix does not need, drive-by edits,
   stray files, and dependency or lockfile churn the plan does not
   call for. Quote each one found, or record "none".
7. PR vs repo asks (template-honored): for every PR ask from Read
   order step 2, quote where the title, description, or diff meets it
   in substance (a heading is not required), or record "ignored" or
   "boilerplate".
8. PR vs policy and maintainers (policy-honored): for every policy
   rule from Read order step 2 and every maintainer comment from step
   3, note whether the PR honors, ignores, or contradicts it; quote
   the AI-use disclosure if the policy requires one.
9. Title and commits (preferred checks): quote the title and each
   commit message.

Live mode only: record the source of each item (file and line, diff
path, or issue comment URL). The issue thread is the live thread
fetched with `gh`; the repo asks come from the repo's PR template and
`docs/CONTRIBUTING.md`.

## Check execution

1. Run the checks in the rubric's table order: plan-fidelity,
   description-truthful, repro-verified, repo-checks-run,
   diff-reviewable, template-honored, policy-honored, then the
   preferred checks.
2. For each check, apply only the rubric's pass condition to the
   evidence recorded for it in Evidence gathering. Do not add
   requirements the pass condition does not state (do not require
   section headings, automated tests the repo does not ask for, or a
   particular number of commits).
3. plan-fidelity fails if any hunk is `unplanned` or any planned item
   is `missing`. repro-verified fails if any test-plan item is
   `missing` or the reproduction itself is not `shown`; a supporting
   item that is `reported` in a sentence passes. A `noted`,
   `disclosed`, or `reported` item never fails a check on its own.
4. Grade `pass` when the pass condition is met and `fail` when it is
   not. Grade `unclear` only when the recorded evidence genuinely
   supports both readings, and say what the two readings are.
5. When the evidence a check needs is absent from the package (no
   test evidence, no description), grade `fail`, not `unclear`, and
   record "absent" as the evidence. Do not infer or invent the
   missing part.
6. Grade every check, even after a required check has already failed:
   the full list is the feedback the author needs.
7. A check may be graded from the recorded evidence without
   re-reading the package. Re-read the relevant part only when the
   recorded quote is too short to decide the pass condition.
8. For each check, write one evidence line: the quote or fact that
   decided the grade (e.g. "src/language-js/print/property.js hunk
   refactors printPropertyKey; plan names only statement.js, no
   deviation note").

## Verdict assembly

1. List the grades of the `required` checks.
2. If every required check is `pass`, the verdict is `accept`.
3. If any required check is `fail` or `unclear`, the verdict is
   `reject`. `unclear` counts exactly like `fail`.
4. Ignore the preferred checks when deciding; still report their
   grades.
5. In the readable summary, name the deciding check: for a reject,
   the first required check in rubric order that did not pass, quoted
   with its evidence line, followed by every other required check that
   did not pass; for an accept, state that all required checks passed.
6. In live mode, add any voice-guide rule the PR title or description
   breaks to the summary, quoting the rule and the offending words; it
   does not change the verdict.
7. Note any step of this procedure that did not fit the package (a
   procedure gap) in the summary.
8. Emit the JSON block from SKILL.md last, with every check (required
   and preferred) in rubric order and the verdict from step 2 or 3.
