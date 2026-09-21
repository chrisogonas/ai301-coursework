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
| maintainer-active | The last 5 default-branch commit dates and the maintainer first-response sample in the repo-facts block. | At least one default-branch commit is within 90 days of the bundle capture date. A maintainer response within 30 days in the sample is additional positive evidence, but unanswered newly opened reports do not fail this check. | required |
| repo-in-use | The `archived` flag, latest release date, and last push date in the repo-facts block. | The repository is not archived, and either its latest release or its last push is within 90 days of the bundle capture date. | required |
| newcomer-scope | The issue body, labels, and comment thread. | The issue has a concrete, bounded outcome: one localized reproducible bug or compatibility regression, a bug with enumerated causes or diagnostic paths and no abandoned attempts, or a clearly specified code, documentation, or test change. Several named edits that deliver one outcome pass, as does a `good first issue` label. Fail support questions, umbrella/tracking issues, unresolved design debates, changes explicitly requiring core internals, or issues with several abandoned implementation attempts. | required |
| unclaimed | The issue assignees, linked PRs, and comment thread in the repo-facts block and bundle. | The issue has no assignee, no open linked PR, and no commenter has said they are actively working on an implementation. | required |
| ai-policy-compatible | The contribution policy line in the repo-facts block. | The contribution policy does not explicitly prohibit AI-generated or AI-assisted contributions. Disclosure, testing, and personal-understanding requirements pass. | required |

## Verdict rule

Accept only when every required check passes. A required check graded
unclear counts as fail. Preferred checks, if added later, may rank
accepted issues but never change the verdict.
