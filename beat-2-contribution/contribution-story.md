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
**Pull Request:** https://github.com/open-telemetry/opentelemetry-dotnet/pull/8096

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

**Unit 7: Installed the ship-gate with a four-check manifest, patched it for PowerShell on Windows, and opened my PR through it**

My manifest runs the Release build, `OpenTelemetry.Tests` on net11.0, `dotnet format whitespace` and markdownlint, each tested first in `cmd` (the shell the gate uses on Windows) on unchanged `main`. The sanity check went into the excluded table, because it fails on `main` with 1,493 CRLF errors from my `core.autocrlf=true` checkout, and markdownlint got `--ignore-path .git/info/exclude` so my local working files don't fail it. Three things fought me: the hook only watched the Bash tool, so the step 4 test ran `gh` through PowerShell and skipped the gate until I widened the matcher and the script's tool check; the gate crashed printing its summary on a cp1252 console (fixed with `PYTHONIOENCODING=utf-8`); and pr-precheck rejected my branch once with "issue thread absent", because the gate only finds issues from `Fixes #N` and my PR deliberately doesn't close a `keep-open` issue, so I put the issue URL in `plan.md`. After that it passed all four repo checks and all nine pr-precheck checks by hand and again through the hook when I opened #8096, and its advice made me add a line offering to switch to a code change.

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

With `SetErrorStatusOnException()` turned on, an activity is marked as an
error even though nothing failed in it. This happens when code inside the
activity awaits a call that throws, and then catches and handles that
exception. The span that succeeded is exported with `StatusCode: Error`, so
error dashboards and alerts count failures that never happened.

**Expected Behavior**

Only an activity that an exception actually leaves should get `Error`. If the
code inside the outer activity catches the exception, the outer activity
should end with status `Unset`, the way the same code written without `await`
does.

**Current Behavior**

The outer activity ends with `StatusCode: Error`. In my repro, OutsideBar is
`Error` in the async run and `Unset` in the sync run, and my regression test
fails with `Expected: Unset, Actual: Error`. It was reported on 1.1.0 and
`net6.0`; users confirmed it on .NET 10, and I reproduced it on 1.20.1 with
`net9.0` and on `main` with `net11.0`. The docs for the feature say nothing
about async code.

**Affected Components**

- `src/OpenTelemetry/Trace/Processor/ExceptionProcessor.cs`: `OnStart` saves
  `Marshal.GetExceptionPointers()`, and `OnEnd` sets `Error` when the value is
  non-zero and different from the saved one.
- `SetErrorStatusOnException()` in `TracerProviderBuilderExtensions.cs`, then
  `TracerProviderBuilderSdk` and `TracerProviderSdk`, which put the processor
  first in the chain.
- `docs/trace/reporting-exceptions/README.md`, Option 2, which documents the
  feature.
- Underneath, the .NET runtime: `AsyncTaskMethodBuilder.SetException` runs the
  awaiting code inline, inside the faulted method's catch block (see Analysis).

**Toolkit**

- Tool: Claude Code with my `CLAUDE.local.md` repo memory from unit 6, which
  pointed straight at `ExceptionProcessor` and its call chain. I then had it
  write two small probe programs that print `Marshal.GetExceptionPointers()`
  and the stack at each step of an async throw and catch.
- What it produced: a stack trace showing the caller's code running inside
  the faulted method's compiler-generated catch
  (`ThrowAsync -> SetException -> RunContinuations -> Main`), and pointer
  values that showed the exception was still live, not left over.
- Difficulties: my unit 6 reading ("the pointer stays set on the
  continuation thread") was close but wrong. The probe showed the pointer
  after the catch is a different value from the one inside the catch, which
  a leftover value would not be.
- What changed: I rewrote the cause in Analysis from the probe output, and
  that moved my fix from code to docs (see Solution Approach).

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

`SetErrorStatusOnException()` (`TracerProviderBuilderExtensions.cs`) sets a
flag that makes `TracerProviderSdk` add the internal `ExceptionProcessor`
first in the processor chain. `ExceptionProcessor.OnStart` saves
`Marshal.GetExceptionPointers()` on the activity when it is non-zero.
`OnEnd` calls it again and sets `Status.Error` when the value is non-zero and
different from the saved one. The idea is that a non-zero value at `OnEnd`
means an exception is leaving the activity's `using` block.

The cause, confirmed on 2026-11-06 with a probe program and a stack trace for
the case where the caller resumes inline, on x64 Windows (not checked on
x86, Linux or macOS):
when an awaited async method throws, its compiler-generated `MoveNext` catches
the exception and calls `AsyncTaskMethodBuilder.SetException` **inside that
catch block**. `SetException` completes the task and often runs the awaiting
method's continuation synchronously on the same thread. So the caller's own
`catch`, and the end of its outer `using`, run nested inside the faulted
method's catch handler:

```text
at Program.<Main>$(...)                          <- caller, after its catch
at ...AsyncStateMachineBox`1.MoveNext(...)
at System.Threading.Tasks.Task.RunContinuations(...)
at ...AsyncTaskMethodBuilder`1.SetException(...)
at Program.<<Main>$>g__ThrowAsync|0_0() line 10   <- inside the generated catch
```

On that thread the original exception is still being handled, so
`GetExceptionPointers()` is non-zero when the outer activity ends (my probe:
`0x3DF5EFB300` inside the caller's catch, `0x3DF5EFD900` after it, so the
value is not a leftover of the exception the caller caught). The outer
activity saved `0` at `OnStart`, so `OnEnd` marks it `Error`. After
`await Task.Yield()` the value is `0x0`.

I don't think this can be fixed reliably inside `ExceptionProcessor`. At
`OnEnd`, a real exception leaving the `using` block gives the same signal
(non-zero and different from the saved value), and that case has to keep
working. The processor only sees the newest exception pointer on the thread,
not whether that exception is passing through the activity's scope. I tried
one code idea: take a per-thread baseline whenever an async flow resumes,
using an `AsyncLocal` change handler with `ThreadContextChanged`. A probe
shows it fails. The handler last fires when `ThrowAsync` resumes *before* it
throws (pointers `0x0`). The inline resume of `Outer` inside the catch reuses
a context with the same value, so the handler does not fire, and the baseline
stays `0x0` while `Outer` ends with `0x4F4227D550`:

```text
handler: ThreadContextChanged=True  prev=null  cur=outer tid=5 ptrs=0x0
Outer ends: tid=5 ptrs=0x4F4227D550
```

Probe of the behaviours the docs note will describe (`OpenTelemetry` 1.20.1,
`net9.0`, `SetErrorStatusOnException()`, each line a fresh process; repeated
three times with the same statuses):

```text
raw: Ok then Error -> Activity.Status=Error
baseline  ptrs-before-end=0xA320B7D950   Activity.Status=Error  GetStatus()=Error  otel.status_code=ERROR
ok        ptrs-before-end=0x1D09BFD4A0   Activity.Status=Error  GetStatus()=Error  otel.status_code=ERROR
yield     ptrs-before-end=0x0            Activity.Status=Unset  GetStatus()=Unset  otel.status_code=-
uncaught  ptrs-before-end=0x0            Activity.Status=Error  GetStatus()=Error  otel.status_code=ERROR
```

`baseline` is the bug. `ok` sets `ActivityStatusCode.Ok` after the catch, and
it does **not** help: `Activity` lets a later `Error` replace `Ok` (`raw`),
and the processor sets `Error` at the end. `yield` adds `await Task.Yield()`
after the catch, and the outer activity stays `Unset`. `uncaught` lets an
async exception leave the `using` block, and the outer activity is still
`Error`, so the feature keeps working for real failures (its pointer column
is `0x0` only because that line is never reached).

The note's own code sample, compiled as written, plus each workaround it
names (fresh process per line; three rounds, same result each time):

```text
note-example     Foo status: Error
yield            Foo status: Unset
ok-in-catch      Foo status: Error
ok-both-in-catch Foo status: Error
option1          Foo status: Unset
```

`ok-both-in-catch` is the workaround BenHewins reported in the thread: both
`SetStatus(Status.Ok)` and `SetStatus(ActivityStatusCode.Ok)`. It still ends
`Error`, because `ExceptionProcessor.OnEnd` calls `SetStatus(Status.Error)`
whatever the status is. `option1` is Option 1 with
`SetErrorStatusOnException` turned **off**. The feature applies to the whole
`TracerProvider`, so Option 1 on one code path cannot help while it is on.

**Proposed Solution**

Document the limitation where users meet the feature. Under Option 2 in
`docs/trace/reporting-exceptions/README.md`, I will add a short subsection
that:

- shows the pattern that triggers it (a caught exception from an awaited call);
- explains why in two or three sentences (the code after the `await` runs
  inside the faulted method's catch block);
- links to #2753;
- gives two workarounds, each with an output row above: add
  `await Task.Yield()` after the `catch` and before the activity ends
  (`yield` row), or turn off `SetErrorStatusOnException` and set the status
  yourself as in Option 1 (`option1` row). It will also say that setting the
  status to `Ok`, with either call, does not help (`ok-in-catch` and
  `ok-both-in-catch` rows), because BenHewins reported in the thread that
  setting both worked for them.

What I will not change: `ExceptionProcessor` and its tests, any public API,
the example `Program.cs`, and `src/OpenTelemetry/CHANGELOG.md`
(CONTRIBUTING.md asks for a CHANGELOG entry for behaviour changes, and this
PR changes none). My claim offered maintainers this docs fix as the
alternative to a code change. If they later want a code change, that will be
a separate PR.

**Implementation Plan**

1. Create branch `docs/2753-async-error-status-note` from upstream `main`
   (`025ead795`).
2. In `docs/trace/reporting-exceptions/README.md`, after the "platform
   dependent" note in Option 2, add a subsection "Caught exceptions in async
   code" with a short example, the cause, a link to #2753 and the two
   workarounds. ASCII only, lines under 80 characters, as markdownlint and
   the sanity check require.
3. Re-run the probe above against the finished note, one line per claim it
   makes: the note's example without a workaround gives `Error` (before); the
   same code with `await Task.Yield()` after the catch gives `Unset` (after);
   `ActivityStatusCode.Ok` gives `Error`; an uncaught async exception still
   gives `Error`. Then compile the note's code samples in a scratch project
   so they are not just text.
4. Run the manifest checks (markdownlint, Release build,
   `OpenTelemetry.Tests` on net11.0, `dotnet format whitespace`), plus
   `git diff --check` and a non-ASCII check on my changed lines in place of the
   sanity check.
5. No new unit test. Nothing in the code changes, and the failing test on my
   repro branch would either fail (asserting `Unset`) or lock the bug in
   (asserting `Error`), so it stays on that branch as evidence.

Deviation (2026-11-06): in my claim and my unit 6 notes, the first option was
a code change to `ExceptionProcessor`, with docs as the fallback. The probe in
Analysis showed a code change cannot tell the two cases apart, so I switched
to the docs fix before writing any code.

Deviation (2026-11-06, after the first plan-check): my first plan listed
"set `ActivityStatusCode.Ok`" as a verified workaround. plan-check asked for
the output, and re-running each case in a fresh process showed `Ok` is
overwritten by `Error`. My first run had looped all cases in one process,
where later cases did not resume inline. I replaced it with
`await Task.Yield()` and added the output to Analysis.

Deviation (2026-11-07, after the second plan-check): my note said "use
Option 1 for this code path", but the feature applies to the whole provider,
so the workaround is to turn the feature off. The plan also said two people
in the thread tried `Ok`; only BenHewins did, with both calls, and said it
worked. I ran both calls in fresh processes (still `Error`) and Option 1 with
the feature off (`Unset`), added both rows above, and fixed the note in a
second commit (3679b012e).

Risk: maintainers may not want docs to close the issue (it carries the
`keep-open` label), so the PR will reference #2753 and leave closing it to
them.

**Toolkit**

- Tool: plan-check (my unit 3 skill with the unit 6 wild scope), run headless
  from my clone on `plan.md` and `plan-comment.md`. The first run could not
  read its own skill files from a non-interactive session, so I re-ran it
  with `--add-dir` for the skills folder and read-only tools.
- What it produced: **reject** on the first real run. `honest-claims` failed
  because I called two workarounds "verified" with no output in the plan, and
  `fixes-the-cause` failed because a docs-only change leaves the cause in
  place. It also flagged that `ExceptionProcessor.OnEnd` sets `Error` without
  checking the current status, so the `Ok` workaround might not hold, and it
  pointed out the issue's `keep-open` label (so "Refs", not "Fixes").
- Difficulties: the `Ok` warning was right. When I re-ran each case in a fresh
  process, `Ok` was overwritten by `Error`; my first probe had looped every
  case in one process and only the first resumed inline. `fixes-the-cause`
  has no path for a cause that sits in the .NET runtime, where the honest fix
  is documentation.
- What changed: I pasted the probe output into Analysis, dropped the `Ok`
  workaround for `await Task.Yield()`, rewrote the comment to show values and
  name a fallback, and patched `fixes-the-cause` with a narrow
  documented-limitation path (evidence that the cause is outside the
  project's code, an argument for why a code change cannot separate the
  cases, and only workarounds whose output is shown). The second run, with
  the patched rubric, was still **reject**: my "Option 1 for this code path"
  workaround had no output (and cannot work while the provider-wide feature
  is on), and I had misquoted the thread on who tried `Ok`. After I ran both
  cases in fresh processes and fixed the plan, comment and docs note, the
  third run was **accept** on all 7 required checks, with one wording note
  that I took (limit "confirmed" to the inline case on x64 Windows).

---

## Testing Strategy

The change is documentation, so "the fix works" means every claim the note
makes is true when run, and the repo's checks still pass.

1. **Before:** my reproduction on OpenTelemetry 1.20.1 / `net9.0` (OutsideBar
   ends `Error` in the async run and `Unset` in the sync run), and my
   regression test on `main` / `net11.0` (`Expected: Unset, Actual: Error`).
   At this point Option 2 in the doc said nothing about async code.
2. **After:** I compiled the note's code sample exactly as written in a
   scratch project against 1.20.1 and ran it plus each workaround the note
   names, one fresh process per case, three rounds. Every round gave
   `note-example Error`, `yield Unset`, `ok-in-catch Error`,
   `ok-both-in-catch Error` and `option1 Unset` (feature off), which is what
   the note says. A control with an async exception that
   leaves the `using` block still gave `Error`, so the `Task.Yield`
   workaround does not hide real failures.
3. **Repo checks** from my ship manifest, on the branch: markdownlint over the
   repo (exit 0), Release build of `OpenTelemetry.Tests` (0 warnings, 0
   errors), `OpenTelemetry.Tests` on net11.0 (2006 total, 2001 passed, 5
   skipped, 0 failed), and `dotnet format whitespace --verify-no-changes`
   (exit 0). The repo's sanity check fails on unchanged `main` here because of
   CRLF checkouts (`core.autocrlf=true`), so I covered my lines with
   `git diff --check` and a byte scan: 0 non-ASCII added lines, longest line
   79 characters.

Not tested: x86, Linux, macOS, `net472`. The note says the code "often" runs
inline, not always, because inlining depends on how the task completes.

**Toolkit**

- Tool: Claude Code wrote three throwaway probe projects in my scratchpad
  (pointer and stack probe, workaround probe, and the note's sample compiled
  verbatim), and ran the manifest checks through `cmd`, the shell the
  ship-gate uses on Windows.
- What it produced: the before/after tables and the check output in
  `test_evidence.md`, which pr-precheck later matched line for line against
  the PR description.
- Difficulties: running every case in one process gave a false "Ok works"
  result, because only the first case resumed inline. The repo's sanity
  check could not run cleanly on a Windows CRLF checkout.
- What changed: one fresh process per case, repeated three times; a byte scan
  and `git diff --check` stand in for the sanity check, and the PR says so.

---

## Implementation Notes

**2026-11-06 (evening): setup**

Installed the ship-gate and wrote the ship manifest. I tested each check in
`cmd` on unchanged `main` first: the Release build (1m25s), `OpenTelemetry.Tests`
on net11.0 (2001 passed), `dotnet format whitespace` and markdownlint all
passed. markdownlint failed only on my local `CLAUDE.local.md`, so I pointed
its `--ignore-path` at `.git/info/exclude`, which already lists every file I
keep out of commits. The sanity check failed on 1,493 files for CRLF line
endings, so it went into the excluded table with the date and reason.

**2026-11-06: the cause, and a change of plan**

Before planning, I tested my unit 6 explanation with a probe. The pointer
after the caller's catch was a different value from the one inside it, and
the stack showed the caller running inside `ThrowAsync`'s generated catch.
So the exception is live, not left over. A code change in
`ExceptionProcessor` cannot tell that apart from a real exception leaving the
`using` block, and the one idea I tried (an `AsyncLocal` resume baseline)
does not fire on the inline resume. I chose the docs fix my claim had
offered as the alternative. I did not write any code for the code-fix
route.

**2026-11-06: wrong turn on the workaround**

My first workaround probe said `SetStatus(ActivityStatusCode.Ok)` worked.
plan-check doubted it, and when I re-ran each case in its own process, it did
not: `Activity` lets a later `Error` replace `Ok`. The single-process run had
been misleading. I swapped in `await Task.Yield()` and added a sentence to the
note saying `Ok` does not help, since two people in the thread tried it.

**2026-11-06: built and checked**

Branch `docs/2753-async-error-status-note` from upstream `main` 025ead795,
one commit (f7d6e0487), +41 lines in `docs/trace/reporting-exceptions/README.md`.
I compiled the note's sample verbatim and ran every claim three times, ran
the manifest checks, and the ship-gate passed by hand (four repo checks and
nine pr-precheck checks). I kept the regression test on my repro branch: with
no code change it would fail, and flipping it to expect `Error` would lock
the bug in.

**2026-11-07: second plan-check, second commit**

After I patched `fixes-the-cause`, plan-check still rejected the plan, and it
was right twice. My "Option 1 for this code path" workaround had no output,
and it cannot work while the provider-wide feature is on. I had also written
that two people tried `Ok`, but only BenHewins did, with both `Status.Ok` and
`ActivityStatusCode.Ok`. I ran both in fresh processes (still `Error`) and
Option 1 with the feature off (`Unset`), and fixed the note in commit
3679b012e (+44 lines in total now).

**Toolkit**

- Tool: Claude Code on a branch in my fork clone, guided by `plan.md`; my
  voice guard (`check_voice.py --draft`) on the PR text; and the ship-gate by
  hand.
- What it produced: one docs commit, `test_evidence.md` and `pr_draft.md`;
  the voice guard passed the draft on the first run.
- Difficulties: on Windows the Claude Code session could not edit files
  under `~/.claude` (the hook settings and my rubric), so I applied those
  patches myself. The ship-gate printed PASS and then crashed with a cp1252
  `UnicodeEncodeError` while printing its summary; the pass was already
  logged.
- What changed: I scoped every change to the one docs file in the plan, and
  recorded both plan deviations in Solution Approach as they happened.

---

## Pull Request

**PR Link:** https://github.com/open-telemetry/opentelemetry-dotnet/pull/8096
(opened 2026-11-07, `chrisogonas:docs/2753-async-error-status-note` into
`main`, commits f7d6e0487 and 3679b012e, +44 lines in one docs file)

**PR Description:** Title: "[docs] Note that SetErrorStatusOnException can
flag caught async exceptions". The description follows the repo's PR
template and covers:

- **Changes:** a "Caught exceptions in async code" subsection under Option 2
  in `docs/trace/reporting-exceptions/README.md`; no code, API, test or
  CHANGELOG changes.
- **Why:** the stack trace showing the caller's continuation running inside
  the faulted method's generated catch, and why `ExceptionProcessor.OnEnd`
  cannot tell that apart from an exception leaving the `using` block. It
  also mentions the `AsyncLocal` idea that failed, and that I tested on x64
  Windows only.
- **Before and after:** my repro (async `Error`, sync no status), then the
  note's own sample and each workaround run in fresh processes
  (`note-example Error`, `yield Unset`, `ok-in-catch Error`,
  `ok-both-in-catch Error`, `option1 Unset`).
- **Checks:** markdownlint, Release build, 2006 tests (2001 passed, 5
  skipped, 0 failed), the format check, and why the sanity check was replaced
  with `git diff --check` and a byte scan.
- **The issue link:** the template's "Fixes #" line is answered "N/A" with a
  reason, and the PR links #2753 as the design discussion issue. I used a
  reference instead of `Fixes #2753` on purpose: the issue carries the
  `keep-open` label and this PR documents the bug without fixing the code, so
  `Fixes` would wrongly close it on merge.
- **Open question:** it says I asked whether maintainers prefer a code change
  and haven't heard back, and that I will switch if they do.
- **AI use:** "AI-assisted" is ticked. I used Claude Code to write the probe
  programs, draft the note and help write the description; I ran every probe,
  checked the output, and edited the text.

**Maintainer Feedback:**

- 2026-11-07: none yet. The welcome bot and the labeler ran; EasyCLA
  reported "Missing CLA Authorization", so signing the CLA is my next step.

**Status:** Awaiting review

**Toolkit**

- Tool: the ship-gate (my four-check manifest plus pr-precheck in live mode),
  run by hand three times and then by the hook when Claude Code ran
  `gh pr create`, with my voice guard checking the description.
- What it produced: run 1 PASS (4 repo checks, 9 pr-precheck checks), then a
  crash printing the summary; run 2, after my second commit, **reject** on
  `policy-honored` ("the issue thread is absent"); run 3 PASS; the hook's
  fresh run when I opened #8096, PASS. pr-precheck also suggested a line
  saying I'd switch to a code change, which I added.
- Difficulties: the hook watched only Bash, so a PowerShell `gh pr create`
  skipped it; the cp1252 console crash; and the gate finds the issue only
  from `Fixes/Closes/Resolves #N`, which my PR leaves out on purpose.
- What changed: I widened the hook to `Bash|PowerShell` in `settings.json`
  and `check_gate.py`, set `PYTHONIOENCODING=utf-8`, and put the issue URL in
  `plan.md`'s title line so the gate could fetch the thread. A fix I would
  make upstream: have the gate also read "Refs #N" or the template's issue
  line.

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
