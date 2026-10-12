# Evidence guide: where evidence lives in a PR package

<!--
The map for rubric.md's checks. Each family says WHERE the evidence
lives (in an eval package, then live) and WHAT GOOD LOOKS LIKE. The
eval package layout, top to bottom: header (source, captured date),
`## Repo facts`, `## Issue`, `## Thread highlights`, `## Plan context`
(the repro evidence plus the plan: change, scope, "Not in scope",
files, test plan), then `## Candidate PR` with `### Title`,
`### Description`, `### Commits`, `### Diff`, `### Test evidence`.
Live, the same parts come from: `plan.md` (with deviation notes), the
branch's diff (`git diff main...HEAD`) and commits (`git log
main..HEAD`), `test_evidence.md`, `pr_draft.md` (title and
description), the issue thread on GitHub, and the repo's PR template
and `docs/CONTRIBUTING.md`. Find content by what it says, not by its
heading: a plan may be one paragraph and a description may answer a
template ask in prose.
-->

## Plan fidelity (harness category: silent-drift)

Checks: plan-fidelity, description-truthful.

Where it lives:
- The plan's boundary: in `## Plan context`, the "Plan:" paragraph
  (the change), "Scope", "Not in scope", "Files:", and the test plan.
  Live: `plan.md`, including its deviation notes section.
- What actually changed: `### Diff`, every `--- a/` / `+++ b/` file
  header and every `@@` hunk. Live: `git diff main...HEAD`.
- Deviation notes: in the plan context or `### Description` ("deviates
  from the plan:", "instead of", "dropped X because"). Live: the
  deviation notes in `plan.md` and in `pr_draft.md`.
- Fidelity and inclusion claims: `### Title` and `### Description`
  ("implements the plan exactly", "no changes beyond it", "tests and
  changelog included"). Live: `pr_draft.md`.

What good looks like: every hunk carries out the plan's change or adds
the test, doc, or changelog that change needs, every planned piece is
present, and the description says only what the diff does (calib-01:
one hunk deleting the shadowed line in `themes.gitconfig`, the only
file the plan names). A mismatch re-tied by a deviation note that
says what changed and why is honest and passes. Silent drift, more:
calib-03 adds a `property.js` refactor the plan never mentions while
the description claims "exactly, with no changes beyond it"; calib-02
replaces the planned gocui fix with a tcell bump. Silent drift, less:
a planned file, failure mode, or test missing with no note.

## Test evidence (harness category: not-tested)

Checks: repro-verified, repo-checks-run.

Where it lives:
- The proof: `### Test evidence`, the commands shown and their
  before/after output. Live: `test_evidence.md` (the Unit 2 repro
  steps re-run before and after the fix, plus the template's checks).
- What it must prove: in `## Plan context`, the repro evidence (steps
  and observed failure) and the plan's test plan (each repro or
  failure mode to re-run, its expected result, controls, suite
  commands). Live: `plan.md`'s test plan and the posted repro comment.
- Which checks the repo asks for: `## Repo facts` "pull requests" and
  "contribution policy" lines (test suite, `cargo fmt`, clippy,
  pre-commit, `go generate`). Live: the PR template's testing section
  and `docs/CONTRIBUTING.md`.

What good looks like: each test-plan item has a command and an
observable before and after, with the after matching the expected
result, on the code path the diff changed; and each check the repo
asks for is reported by name with its outcome (calib-01: the
configparser repro shows the `DuplicateOptionError` before and
`parsed OK` after, plus the `git config --get-all` check). Bad: no
evidence at all (calib-02), "tests pass" with no command named, a run
that exercises an unchanged path, or a test-plan item silently left
out (calib-04 shows repro 1 but never shows or mentions the
`-j 200000 -x echo` failure mode the plan named). An item disclosed
as not run, with a reason, is honest and does not fail on its own.

## Diff quality (harness category: unreviewable)

Check: diff-reviewable.

Where it lives:
- `### Diff`, every hunk, and `### Commits`. Live: `git diff
  main...HEAD` and `git log main..HEAD`; also `git status` for stray
  untracked files the author may be about to add.

What good looks like: every line in the diff is the fix, its test, or
its doc or changelog entry, so a reviewer sees the change at a glance
(calib-04: a value parser, one test, one changelog line). Debris tells:
`print`/`console.log`/`dbg!` left in, commented-out blocks, unused
helpers, whitespace or formatting churn on lines the fix does not
need (calib-02's gofmt rewrite of `gui.go` assignments), drive-by
refactors (calib-03's `property.js`), stray files (`.orig`, `.swp`,
local config), and dependency or lockfile churn the plan does not
call for (calib-02's `go.mod`/`go.sum`).

## Standards and comms (harness category: standards-wall)

Checks: template-honored, policy-honored (and preferred
title-describes-change, commits-descriptive).

Where it lives:
- The asks: `## Repo facts`, the "pull requests" line (template
  sections, checklist items, "closes #N", changelog or whatsnew entry,
  title convention, tests with fixes) and the "contribution policy"
  line (AI-use disclosure, own-words rule, CLA, vouch or
  discussion-first gates, technical rules). Live: the repo's
  `.github/pull_request_template.md` and `docs/CONTRIBUTING.md`.
- Maintainer direction: `## Thread highlights`, comments tagged OWNER,
  MEMBER, or COLLABORATOR. Live: the issue thread on GitHub.
- Where the PR answers them: `### Title`, `### Description`, and the
  diff (a changelog or whatsnew file is an ask met in the diff). Live:
  `pr_draft.md` and the branch's diff.

What good looks like: every stated ask is met in substance, under a
heading or in prose, with real content or "n/a" plus a reason
(calib-03's description answers prettier's checklist line by line and
the diff contains `changelog_unreleased/javascript/19727.md`), and a
required AI-use disclosure names the tool and the extent of help.
Bad: a required checklist or section missing or left as boilerplate,
a required "closes #N" or changelog entry absent, a required
disclosure missing, or a maintainer's direction ignored. When the repo
states no PR asks and no policy (calib-01), these checks pass on an
accurate description; mentioning AI help in a repo with no AI policy
is not a failure. Whether the description's claims match the diff is
plan fidelity, above.
