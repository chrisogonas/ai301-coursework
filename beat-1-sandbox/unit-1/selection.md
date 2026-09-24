# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72

**Verdict output**

[Your skill's live-mode output for this issue, pasted verbatim and ending with the
fenced JSON verdict block. A summary does not satisfy this field.]

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

maintainer-active: pass — the main branch's latest commit is from last week, which is within the rubric's 90-day threshold.
repo-in-use: pass — the repository is not archived and its latest main-branch commit is from last week.
newcomer-scope: pass — the issue is labeled `good first issue` and names two files, `core/security.py` and `tests/unit/test_security.py`, with an estimated effort of 1–2 hours.
unclaimed: pass — GitHub lists “No one assigned” and “No branches or pull requests”; Path Review's house rule says classmates' claim comments do not block an issue.
ai-policy-compatible: pass — the contribution guide requires green CI and tests, but does not prohibit AI-assisted contributions.

```json
{
   "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72",
   "checks": [
      {
         "name": "maintainer-active",
         "grade": "pass",
         "evidence": "The repository main branch's latest commit is from last week, within 90 days."
      },
      {
         "name": "repo-in-use",
         "grade": "pass",
         "evidence": "The repository is public, active last week, and not archived."
      },
      {
         "name": "newcomer-scope",
         "grade": "pass",
         "evidence": "Issue #72 is labeled good first issue, identifies core/security.py and tests/unit/test_security.py, and estimates 1–2 hours."
      },
      {
         "name": "unclaimed",
         "grade": "pass",
         "evidence": "GitHub shows no assignee and no linked branches or pull requests; Path Review permits classmates to share an issue."
      },
      {
         "name": "ai-policy-compatible",
         "grade": "pass",
         "evidence": "The contribution guide requires testing and green CI but states no prohibition on AI-assisted work."
      }
   ],
   "verdict": "accept"
}
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

Final full run: “agreement: 18/20 scored items  (bar: 18/20: PASS)”

**Issue analysis**

`issue-01`: my rubric rejected it, while the gold label was `accept`: “issue-01  accept  reject   NO     failed: newcomer-scope”. Its proposed work was broad documentation work: “Create a new task page,” “Update `manage-pkgs.rst`,” “Update `pip-interoperability.rst`,” and “Update `new-features.md`.” I treated this as more than one localized newcomer task under my current `newcomer-scope` rule, which caused the rejection.

**Check rationale**

Quoted check: “The issue has a concrete, bounded outcome: one localized reproducible bug or compatibility regression, a bug with enumerated causes or diagnostic paths and no abandoned attempts, or a clearly specified code, documentation, or test change.” I used this check to make scope explicit rather than relying on issue labels or a vague sense that an issue looks easy. It accepts reproducible bugs and well-specified changes while screening out support questions, tracking issues, unresolved design work, and tasks known to require core internals.

**Trade-offs**

the point in full when the reason follows.]
This check is conservative about issues that describe several documentation edits. It changed the outcome for `issue-01`: the saved run records “failed: newcomer-scope,” even though the gold label is accept. The same pattern appears in `issue-19`, the other mismatch: “issue-19  accept  reject   NO     failed: newcomer-scope.” The trade-off is fewer false accepts for multi-part work, at the cost of rejecting some clearly planned work that is still suitable for a newcomer.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Issue #72 fits my Python background and the available time because it is a small backend bug with a focused test update and a stated estimate of 1–2 hours.

2. The verdict correctly identified that the repository is active, the issue is bounded, and the project permits an AI-assisted workflow that is reviewed and tested. Beyond the rubric, I weighed that the behavior is security-related: returning `False` for an unrecognized hash is a focused fail-closed change, so I will keep the implementation minimal and verify the affected unit test.

3. The main difficulty is coordinating with classmates who have already posted reproduction notes. The Path Review house rule allows shared claims, but I will state my intended small scope clearly and avoid duplicating unrelated work.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
