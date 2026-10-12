# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/141

**Branch**

`fix/72-verify-password-unknown-hash`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run: `agreement: 19/20 scored items  (bar: 18/20: PASS)`. pkg-19 disagreed
   (`failed: repro-verified`).
2. Partial run (`--only pkg-19,pkg-04,pkg-07,pkg-10,pkg-14,calib-04 --include-calibration`)
   after loosening `repro-verified`: `agreement: 5/5 scored items`.
3. Full run: `agreement: 19/20 scored items  (bar: 18/20: PASS)`. This time pkg-11
   disagreed (`failed: description-truthful`). It had agreed in run 1.
4. Partial run (`--only pkg-11,pkg-03,pkg-06,pkg-09,pkg-17,calib-03 --include-calibration`)
   after tightening the wording of `description-truthful`: `agreement: 5/5 scored items`.
5. Full run, saved with `--save-run`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`,
   `categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.

Two earlier attempts crashed before grading anything (Python 3.8 could not parse the
harness's type hints, and then a Windows cp1252 encoding error). They produced no score, and I
re-ran with `PYTHONUTF8=1 py -3.12`.

**Package analysis**

pkg-19 (category `clear-accept`, gold `accept`). In run 1 my rubric decided `reject`, failing
only `repro-verified`. The grader's evidence line was: "Primary repro is shown with
command+before/after, but test-plan item 'the two controls unchanged' is only asserted with no
command or observable output, and not disclosed as skipped."

My rubric read it that way because the first version of `repro-verified` required "every
distinct failure mode or behavior the plan's test plan names" to be shown with an observable
before and after, and it made no difference between the bug itself and its controls. pkg-19's
plan says "the two controls unchanged", and the PR reports them in one sentence: "Controls
(double-quoted twin; single-quoted without the trailing blank line) unchanged." So the rubric
counted a control as a silently missing test-plan item. The gold label is right. The bug's own
repro is shown decisively (before/after output and a byte-identical diff), and calib-01, a
clean accept, reports its smoke check in a sentence the same way. calib-04's real failure is
different: a whole planned failure mode (`-j 200000 -x echo`) is neither shown nor mentioned.
After the revision, run 5 accepted pkg-19 with the evidence: "controls reported in a sentence
as the rubric allows."

**Check rationale**

| repro-verified | The test evidence, read against the plan's test plan and the reproduction steps in the plan context | Passes if the test evidence re-runs the reproduction (or an automated test asserting that same behavior) on the changed code path and shows an observable before and after, with the after matching the expected result the plan or issue states; AND every distinct failure mode or bug behavior the plan's test plan names is shown the same way, or explicitly disclosed as not run with a reason. Controls, smoke checks, and neighbouring-behavior checks the test plan names may be reported in a sentence ("the -j 64 control is unchanged"); they need not show output. Fails if there is no test evidence, if it only says "works" or "tests pass" with no observable result for the bug itself, if it exercises a path the diff did not change, or if a test-plan item is silently missing (neither shown, reported, nor disclosed). | required |

It reads this way because of pkg-19. The first version required every test-plan item,
controls included, to be "shown" with output, which rejected a clean accept over a control
reported in a sentence. I split the test plan into two tiers. Bug items (the repro and each
failure mode the plan names) still need an observable before and after. Supporting items
(controls, smoke checks) may be reported in a sentence. I rejected the simpler fix of dropping
the "every item" clause entirely, because that clause is what catches calib-04 and the
not-tested packages: a PR that shows one failure mode and silently drops the other. The
"disclosed as not run with a reason" path is there for the honest-outcome rule: a PR that says
what it could not test can still be ready.

**Trade-offs**

Loosening `repro-verified` could let a not-tested PR through if its only proof of the bug is
written as a "control" sentence. That changed pkg-19 (reject to accept), so I re-ran every
not-tested package as a canary, plus the calib-04 trap:
`--only pkg-19,pkg-04,pkg-07,pkg-10,pkg-14,calib-04 --include-calibration` gave
`not-tested 4/4` and calib-04 still `reject`. The confirming full run kept `not-tested 4/4`.
The case I accept it will miss is a PR whose control matters as much as the bug and is
asserted without output. The rubric now trusts that sentence, so the grader would accept
on the strength of the bug's own repro alone.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
