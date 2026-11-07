# Contribution: SetErrorStatusOnException with async Exceptions works incorrectly

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Chrisogonas Odhiambo
**GitHub Username:** chrisogonas
**Issue:** https://github.com/open-telemetry/opentelemetry-dotnet/issues/2753
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Turned my issue-select skill into issue-scout, with a written search strategy, and patched two rubric checks after the first real scan**

I installed the scout over my Unit 1 skill and wrote a search strategy: four `gh search issues` queries (good first issues in Python, TypeScript, JavaScript and C#), plus ranking rules that drop assigned, stale, crowded and sandbox issues and put bugs first. The first version fell short twice before any money was spent. Plain label searches returned mostly one-person repos, and sorting by interactions fixed that but returned only crowded threads, so a hand dry run kept just 5 of 400 results; adding a `--comments "<=8"` cap raised that to 71. The real scan accepted an issue a collaborator had already said was fixed, and passed another on its `good first issue` label alone although a maintainer had asked for a design, so I patched `unclaimed` to fail fixed issues and `newcomer-scope` to discount the label when a design request is still open, and logged each change in scan-notes.md.

**Unit 6: Installed the voice guard over one canonical voice guide, made it work on Windows, and wrote a verified repo-memory file for opentelemetry-dotnet**

I folded my three voice-guide copies into one guide at `~/.claude/voice-guard/`, added Upstream rules for claims, questions and pings, and installed the guard as a Claude Code hook, but on my Windows machine it broke three ways: `python3` resolved to Python 3.8, which crashes the checker; symlinks are not permitted, so the skills now point to the guide with one-line pointer files; and the hook only watched the Bash tool, so I extended it to the PowerShell tool and tested that both stop a body-less `gh issue comment`. The guard then passed my claim twice (manual check and hook), and the posting attempt that the headless session's permissions blocked showed me the same Bash/PowerShell split from the other side. For the repo-memory file, I ran one scoped orientation over `src/OpenTelemetry/Trace` and distilled it into a 66-line `CLAUDE.local.md` that imports the repo's `AGENTS.md`; the first test command I wrote targeted `net9.0`, which the test projects do not build for, so I fixed it to `net11.0` only after running it, and I added the `AGENTS.md` rule that I must post issue replies myself.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

This issue came from my third batch of picks, after my scout's first scan (unit 5) and two rounds of Issue Selection picks fell through. After the unit 6 scope patch, I re-graded my three picks: the scout accepted apache/beam#19968 (ambitious) and OWASP/Nest#3483 (straightforward) and rejected MudBlazor#11799 for an open PR. Nest had eleven assignment requests, so it was taken. I chose Beam, then found while reading its code that PR #37360 had already fixed it in January 2026 without ever linking the issue, so I dropped it. I then graded seven more picks (beam#27038, nanocoder#1745, otel-arrow#4397, opentelemetry-dotnet#2753 and #5064, weaver#501, swift-org-website#763). Every one except #2753 turned out to have an open PR or a contributor saying the bug no longer reproduces. The scout accepted opentelemetry-dotnet#2753 with the difficulty note **ambitious**: the bug may be a limit of .NET itself, and no maintainer has commented on the thread in nearly five years.

The issue fits most of my profile: C#, an established library that many people use, and a clear repro that several users have confirmed, most recently on .NET 10 in May 2026. It goes against one line of my profile, "avoid issues that wait on a design decision", and I chose it knowing that. My reproduction showed the cause is concrete and testable (`ExceptionProcessor` reads a per-thread exception pointer that stays set after an async catch), and if the maintainers decide a code fix is wrong, documenting the limitation is still a bounded PR. So my claim leads with that design question instead of a promised fix. I hope to learn how .NET async continuations interact with thread state, how the OpenTelemetry SDK's processor pipeline works, and how to move a five-year-old issue forward by asking maintainers the right question. My honest read of the scout: its `scope-feasible` patch helped, because under my old `newcomer-scope` check an issue with an open design question would have been rejected, and the patch turned that into the "ambitious" note that told me what to ask. But its `unclaimed` check missed two existing fixes (Beam#19968's unlinked PR #37360, and weaver#501's open PR #1889, which it accepted). Both times I caught the miss by hand with a PR search, so the fix I will make is to add `gh search prs --repo OWNER/REPO "<issue number>"` to the evidence that check reads. My claim comment: https://github.com/open-telemetry/opentelemetry-dotnet/issues/2753#issuecomment-6699493699

**Toolkit**

- Tool: issue-scout, in direct mode on 10 issues over four rounds, after I patched its rubric (replaced `newcomer-scope` with `scope-feasible` and added the difficulty note). I read every accepted issue's thread myself before choosing.
- What it produced: an accept for opentelemetry-dotnet#2753 with the note "ambitious: the bug may be a .NET limitation; raise this with a maintainer before you claim it", and the read-outs and notes for all 10 picks in `scan-output.md` and `scan-notes.md`.
- Difficulties: `unclaimed` only reads the issue's linked PRs and comments, so it missed a merged fix that never linked the issue (Beam#19968) and an open PR not yet cross-referenced (weaver#501). It also passed dotnet#5064 although a contributor said the bug no longer reproduces, because the "fixed" clause counts only maintainers.
- What changed: the scope patch (logged in `scan-notes.md`); every miss is logged there with a proposed fix: search PRs by issue number before accepting, and flag any evidenced "no longer reproduces" comment. Until I patch the rubric, I run that PR search by hand on every accepted issue.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

- OS: Windows 11 Pro 10.0.26300, x64.
- Standalone repro: .NET SDK 9.0.320 (runtime 9.0.22, target `net9.0`), NuGet
  `OpenTelemetry` 1.20.1 and `OpenTelemetry.Exporter.Console` 1.20.1, the
  latest stable versions on 2026-11-06, added with `dotnet add package`.
- Repo: I forked open-telemetry/opentelemetry-dotnet to
  `chrisogonas/opentelemetry-dotnet` with `gh repo fork --clone`, based on
  upstream `main` at `025ead795`. The repo pins .NET SDK
  11.0.100-rc.3.26522.148 in `global.json`, which I already had installed.
- Difference from the issue: it was filed against OpenTelemetry 1.1.0 on
  `net6.0`. I did not test those versions; I tested 1.20.1 on `net9.0` and
  `main` on `net11.0`.
- Challenges: my first test command used `--framework net9.0` and failed with
  a misleading "using VSTest test runner" error. The test projects only target
  `net11.0`, `net10.0` and (on Windows) `net472`, so I switched to
  `--framework net11.0`. My first regression test also raised analyzer warning
  xUnit1051, which would fail a Release build, so I passed
  `TestContext.Current.CancellationToken` to `Task.Delay`.

**Steps to Reproduce**

1. Standalone app: `dotnet new console -f net9.0`, add the two packages, and
   use the thread's second repro (OutsideBar/InsideBar) as `Program.cs`. I added
   a `sync` mode (InsideBar throws and catches with no `await`) and a line that
   prints `Marshal.GetExceptionPointers()` just before OutsideBar ends.
2. Run `dotnet run -- async`, then `dotnet run -- sync` as the control.
3. In the repo: on branch `repro/2753-async-caught-exception`, I added the test
   `ActivityStatusNotSetOnOuterActivityWhenAsyncExceptionIsCaught` to
   `test/OpenTelemetry.Tests/Trace/ExceptionProcessorTests.cs` and ran
   `dotnet test test/OpenTelemetry.Tests/OpenTelemetry.Tests.csproj --framework net11.0 --filter "FullyQualifiedName~ExceptionProcessor"`.
4. Observed: in the async run, OutsideBar ends with `StatusCode: Error`
   although the exception was caught inside `Bar()`; in the sync run it has no
   status (Unset). The new repo test fails with `Expected: Unset`,
   `Actual: Error` on the outer activity.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/chrisogonas/opentelemetry-dotnet/commit/ecc916e392f7c188309265cb362bd77f49b6f4b4
  (the failing regression test only, no fix)
- **Screenshots/logs:**

  ```text
  ===== async
  Activity.DisplayName:        InsideBar
  StatusCode: Error
  [async] thread before OutsideBar ends: 4, exception pointers: 0xCB6977D6B0
  Activity.DisplayName:        OutsideBar
  StatusCode: Error
  ===== sync
  Activity.DisplayName:        InsideBar
  StatusCode: Error
  [sync] thread before OutsideBar ends: 2, exception pointers: 0x0
  Activity.DisplayName:        OutsideBar

  failed OpenTelemetry.Trace.Tests.ExceptionProcessorTests.ActivityStatusNotSetOnOuterActivityWhenAsyncExceptionIsCaught (41ms)
    Expected: Unset
    Actual:   Error
    total: 3, failed: 1, succeeded: 2
  ```

- **My findings:** the bug is real today on the latest stable package and on
  `main`. `ExceptionProcessor.OnEnd` marks an activity as Error when
  `Marshal.GetExceptionPointers()` is non-zero and differs from the value saved
  at OnStart. In the async run that value is still non-zero after the
  exception was caught; in the sync run it is `0x0`. My reading is that the
  pointer belongs to the thread and stays set on the thread that runs the
  `await` continuation. That is an interpretation of the output, not a
  confirmed cause. Not tested: x86 (an existing test notes that x86 clears the
  pointers before the catch runs), Linux, macOS, `net472`.

**Toolkit**

- Tool: repro-check, with the unit 6 wild scope, run on my claim alone before
  posting, then on the claim and repro report together.
- What it produced: an accept on the claim (it also found that the repo's
  `AGENTS.md` asks for AI disclosure on comments, which my claim already had).
  The first combined grade was hold: `env-faithful` failed because I never
  said I tested 1.20.1 instead of the issue's 1.1.0 on `net6.0`, and
  `steps-rerunnable` failed because my `Program.cs` changes were only
  described and the fork branch was not pushed yet. The second grade accepted
  all 7 required checks plus the control run.
- Difficulties: none in the tool itself; both fails were real gaps a stranger
  would have hit. It graded the claim as "already posted" on the second run,
  which was right, but it means the claim check now confirms a comment instead
  of guarding one.
- What changed: I added the version-difference line, the full `Program.cs`,
  the quoted test, a pushed commit link and a disclosure line to the report,
  then re-graded.

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
