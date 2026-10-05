# Rubric: is this plan ready to post and build from?

<!--
Group 17's activity rubric (diagnosis, scope, test), revised after the
calibration packages: the diagnosis check now reads the cause against
the repro evidence (calib-03's wrong cause passed the "names a cause"
version), the test check asks for an observable flip of the repro
instead of automation (calib-01 is ready with a manual repro check;
calib-04's "run the suite" proves nothing about the bug), and new
checks cover executability, symptom-vs-cause, honesty, and comms.
Every check judges the plan's substance, never its headings: a terse
paragraph plan and a sectioned plan are graded the same way.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis-grounded | The plan's stated cause (wherever the plan says why the bug happens), read against every step, timing, artifact, and control in the repro-evidence block | Passes if the stated cause explains all the behavior the repro evidence shows, including its controls (what changes when the bug does and does not appear), and nothing in the repro evidence contradicts it. Fails if the evidence contradicts the cause (e.g. the bug persists with the blamed component removed), if the cause ignores a control that points elsewhere, or if the plan states no cause at all. A cause adopted from a thread comment passes only if the repro evidence also supports it. | required |
| fixes-the-cause | The plan's proposed change, read against its own stated cause | Passes if carrying out the change would remove or correct the stated cause. Fails if the change only hides or works around the symptom (e.g. raising a timeout, catching and swallowing the error, adding a spinner, special-casing the repro input) while the cause stays in place. | required |
| bounded-scope | Everything the plan says it will change (approach, files, areas, follow-on work), read against the stated cause | Passes if all the planned work is one coherent change aimed at the stated cause, plus only the tests and docs that change needs. Fails if the plan bundles unrelated work: refactors, renames, new features, dependency upgrades, style sweeps, or fixes for other issues that the cause does not require. A plan does not need an explicit "out of scope" line to pass. | required |
| executable | The plan's description of what will be done: the code location (file, function, module, or component) and the concrete edit | Passes if a contributor who has never seen the thread could start the work without asking the author anything: the plan names where the change goes and what the change is. Fails if it only says it will investigate, "poke around", or "figure out where" the problem lives, or names no location and no concrete edit. | required |
| test-observes-fix | The plan's test plan (wherever it says how success will be checked), read against the repro-evidence block's steps and expected/actual result | Passes if the test exercises the reproduced behavior and names the observable result that must now differ: re-running the repro steps with the expected result stated, or an automated test that asserts that same behavior. Manual checks pass. Fails if the test cannot observe the bug, e.g. only "run the existing test suite", "make sure nothing regresses", "it works", or a test of something other than the reproduced behavior. | required |
| honest-claims | Claims of certainty in the plan and the plan comment ("I traced", "confirmed", "this fixes"), read against what the repro evidence and thread actually show | Passes unless the plan or comment states something as established that the package does not support: a claim of having traced, confirmed, or verified something when the package shows no such work (e.g. a thread commenter's theory restated as "I traced this"), a claimed verification the repro evidence does not show, or a promise (e.g. "fixes all cases") the plan does not back. A cause inferred from the repro evidence and stated plainly passes; stated unknowns and hedges pass. A plan with no risks section is not a fail. | required |
| comms-fit | The plan comment, read against the thread highlights (especially OWNER, MEMBER, and COLLABORATOR comments) and the repo-facts block (bug-report template asks, contribution policy, AI-use disclosure rules) | Passes if the comment accurately summarizes the plan and respects what the thread and repo ask. Fails if it ignores or contradicts a maintainer's stated direction (e.g. "working as intended", "discuss before a PR", a requested approach), breaks a stated contribution policy (e.g. a required AI-use disclosure is missing, or a policy against unsolicited PRs is ignored), or promises a different change than the plan describes. If the thread is empty and the policy asks nothing specific, a comment that accurately states the plan passes. | required |
| states-out-of-scope | The plan's scope statement | Passes if the plan explicitly names at least one thing it will not change. | preferred |
| names-risks | The plan's risks, unknowns, or caveats, wherever they appear | Passes if the plan names at least one risk, unknown, or affected neighbouring behavior to check. | preferred |

## Verdict rule

Accept (ready) only if every `required` check grades `pass`. Any
`required` check graded `fail` or `unclear` means reject (hold).
`unclear` is for evidence that genuinely points both ways; when the
information a required check needs is simply missing from the package,
grade it `fail`, not `unclear`. `preferred` checks are reported but
never change the verdict, whatever they grade.
