# Rubric: is this pull request ready to submit?

<!--
Built from the Unit 3 plan-check rubric and the four calibration
packages, rewritten for a PR. Every "no" in the activity's Files and
Claims tables maps to a check here:
- Files: a changed file the plan never asked for (calib-02's tcell
  bump and gofmt churn, calib-03's property.js refactor) fails
  plan-fidelity and diff-reviewable; a file the plan asked for that
  never shows up fails plan-fidelity unless a note discloses it.
- Claims: "implements the plan exactly, no changes beyond it"
  (calib-03) fails description-truthful when the diff says otherwise;
  a test plan item that was never run and never mentioned (calib-04's
  repro 2) fails repro-verified.
The four failure families map one to one: silent drift ->
plan-fidelity and description-truthful; not tested -> repro-verified
and repo-checks-run; unreviewable -> diff-reviewable; standards wall
-> template-honored and policy-honored. Every check judges the
substance of the PR against the plan, the issue, and the repo's
stated asks, never the layout of the description. A PR that honestly
discloses a shortfall (a deviation note, a "not tested on X because
Y") can be ready; only the silent kind holds it.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-fidelity | Every file and hunk in the diff, read against the plan's stated change, files, and "not in scope" line, plus any deviation note (in the plan or the PR description) | Passes if every hunk carries out the plan's change, or adds the tests, docs, or changelog entry that change needs, or is covered by a deviation note that says what changed and why; AND every piece of work the plan commits to is in the diff or is named as not done in a deviation note. Fails if the diff does more than the plan with no note (unrelated refactor, dependency bump, a fix for a different issue, work the plan marked out of scope), does less with no note (a planned file, failure mode, or step missing), or replaces the planned approach with a different one. | required |
| description-truthful | Each factual claim in the PR title and description (what changed, "matches the plan", "no other changes", "tests added", "changelog included", "fixes all cases"), read against the diff and the test evidence | Passes if every claim is borne out by the diff or the test evidence, and the description describes the change the diff actually makes. Fails if any claim is contradicted (claims plan fidelity while the diff adds unplanned work; claims a test, changelog, or doc file the diff does not contain; claims a verification the test evidence does not show), or if the description describes a different change than the diff. Read each claim at the level a reviewer would: it is contradicted only when the diff or evidence plainly shows it false, and a case exercised inside another test's input or assertion backs a claim about that case. Hedged or disclosed limits ("not tested on Windows") pass. | required |
| repro-verified | The test evidence, read against the plan's test plan and the reproduction steps in the plan context | Passes if the test evidence re-runs the reproduction (or an automated test asserting that same behavior) on the changed code path and shows an observable before and after, with the after matching the expected result the plan or issue states; AND every distinct failure mode or bug behavior the plan's test plan names is shown the same way, or explicitly disclosed as not run with a reason. Controls, smoke checks, and neighbouring-behavior checks the test plan names may be reported in a sentence ("the -j 64 control is unchanged"); they need not show output. Fails if there is no test evidence, if it only says "works" or "tests pass" with no observable result for the bug itself, if it exercises a path the diff did not change, or if a test-plan item is silently missing (neither shown, reported, nor disclosed). | required |
| repo-checks-run | The test evidence and description, read against the checks the repo-facts block and the plan's test plan ask for (named test suite, lint or format commands, pre-commit, generate steps) | Passes if each check the repo or plan asks to run is reported with the command or suite named and its outcome. Passes automatically when neither the repo nor the plan names any check to run. Fails if a requested check is not reported, or is reported only as "tests pass" with no command or suite named. | required |
| diff-reviewable | The unified diff and the commit list | Passes if the diff contains only the change and its supporting tests, docs, and changelog, so a reviewer can see the fix at a glance. Fails if debris rides along: debug prints or logging left in, commented-out code, dead or unused code added, formatting or whitespace churn in lines the fix does not touch, drive-by edits to unrelated code, stray files (backups, editor files, local config), or unrelated lockfile or dependency churn. | required |
| template-honored | The repo-facts block's PR template and contributing asks, read against the PR title, description, and diff | Passes if every ask the repo states for a PR is met in substance somewhere in the title, description, or diff: required sections or checklist items answered with real content (or "n/a" with a reason), a required "closes/fixes #N" link present, a required changelog, whatsnew, or release-notes entry present in the diff, a required title convention followed, tests included when the repo asks for tests with fixes (or the description says why no automated test applies). Passes automatically when the repo states no PR asks. Fails if any stated ask is visibly ignored or answered with boilerplate. | required |
| policy-honored | The repo-facts contribution policy (AI-use rules, CLA, vouch or discussion-first gates, stated technical rules) and every OWNER, MEMBER, or COLLABORATOR comment in the thread highlights, read against the PR description and diff | Passes if the PR complies with every stated policy and engages any explicit maintainer direction: an AI-use disclosure that names the tool and its extent when the policy requires one, description text that reads as the contributor's own words when the policy requires it, a stated gate or technical rule respected. Passes automatically when no policy or maintainer direction applies; mentioning AI help in a repo with no AI policy is not a failure. Fails if a required disclosure is missing or vague, a stated gate or rule is broken, or a maintainer's direction is contradicted or ignored. | required |
| title-describes-change | The PR title, read against the diff | Passes if the title states what the change does in plain words (no "fixed it!!", no "WIP", no issue number alone). | preferred |
| commits-descriptive | The commit list | Passes if each commit message says what that commit changes (no "fix", "wip", "hopefully"). | preferred |

## Verdict rule

Accept (ready to submit) only if every `required` check grades
`pass`. Any `required` check graded `fail` or `unclear` means reject
(hold): `unclear` counts exactly like `fail`, because a PR that cannot
be verified from the package is not ready to submit. Use `unclear`
only when the evidence genuinely points both ways; when the evidence a
required check needs is simply absent from the package (no test
evidence, no description), grade it `fail`. `preferred` checks are
reported but never change the verdict, whatever they grade. A
shortfall that is honestly disclosed in a deviation note or the
description is graded by the check's own condition, which lets it
pass; only an undisclosed shortfall fails.
