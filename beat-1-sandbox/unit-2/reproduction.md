# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

chrisogonas

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5861363943

Hi, I'd like to work on this one: `verify_password` in `core/security.py` lets passlib's `UnknownHashError` escape when the stored hash isn't a recognizable format, instead of failing closed with `False`. I'm a student working through Path Review as a course assignment, and this would be my first contribution here.

My next step is to reproduce it in a clean environment: call `verify_password` with a plausible password against a malformed stored hash and confirm the exception escapes rather than `False` coming back, then run the `xfail`-marked test in `tests/unit/test_security.py` (manifest H-05) to see the same failure through the suite. I'll post a repro report here - environment, exact steps, and output - before touching the fix. I can see several classmates are already on this; per the course rules I'll reproduce and report independently rather than build on their comments.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5861978570

**Environment:** Windows 11 Pro (10.0.26200), Python 3.14.0, repo at commit `f89c06f` on `main` (my fork of this repo, no local changes — `f89c06f` is the same commit this repo's `main` points at, so cloning either gives the identical tree). Installed into a fresh venv: passlib 1.7.4, bcrypt 4.3.0, python-jose 3.5.0, pydantic 2.13.5, pydantic-settings 2.15.0, pytest 9.1.1 — the versions `pyproject.toml` pins. I did not stand up the Docker/Postgres stack from SETUP.md: every `Settings()` field in `core/config.py` has a default, so `core/security.py` imports and runs in isolation.

**Steps:**

```
git clone https://github.com/chrisogonas/pathreview-ai301-fa26-s1
cd pathreview-ai301-fa26-s1
python -m venv .venv-repro
.venv-repro/Scripts/python -m pip install "passlib[bcrypt]>=1.7.4" "bcrypt>=4.0.1,<5.0.0" "python-jose[cryptography]>=3.3.0" "pydantic[email]>=2.5.0" "pydantic-settings>=2.1.0" "pytest>=7.4.0"
```

1. Direct call with the malformed stored hash the covering test uses:

```
$ .venv-repro/Scripts/python -c "from core.security import verify_password; print(verify_password('password', 'not_a_valid_bcrypt_hash'))"
Traceback (most recent call last):
  ...
  File "...\core\security.py", line 37, in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
  File "...\passlib\context.py", line 1132, in identify_record
    raise exc.UnknownHashError("hash could not be identified")
passlib.exc.UnknownHashError: hash could not be identified
```

2. Control — well-formed bcrypt hashes take the normal path:

```
$ .venv-repro/Scripts/python -c "from core.security import hash_password, verify_password; h = hash_password('password'); print(verify_password('password', h), verify_password('wrong-password', h))"
True False
```

3. The same failure through the suite. Normally the marker absorbs it:

```
$ .venv-repro/Scripts/python -m pytest tests/unit/test_security.py -k wrong_hash_format
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format XFAIL [100%]
```

and with `--runxfail` the raw error surfaces — the raise happens at the `verify_password` call, before the test's `assert result is False` is ever reached:

```
$ .venv-repro/Scripts/python -m pytest tests/unit/test_security.py -k wrong_hash_format --runxfail
>           raise exc.UnknownHashError("hash could not be identified")
E           passlib.exc.UnknownHashError: hash could not be identified
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
```

**Expected:** `verify_password("password", "not_a_valid_bcrypt_hash")` returns `False` (fail closed), and `test_verify_with_wrong_hash_format` passes once the `xfail` marker (manifest H-05) is removed.

**Actual:** `passlib.exc.UnknownHashError: hash could not be identified` escapes from `core/security.py:37` (shown above); the control runs return `True`/`False` correctly, so the escape is specific to unrecognizable stored hashes. One side note for anyone re-running: passlib 1.7.4 with bcrypt 4.x prints a trapped `(trapped) error reading bcrypt version` warning on first use — it's noise, not the bug.

Next, as claimed: a minimal fix catching the error in `verify_password` to return `False`, plus removing the H-05 marker, on a branch from this fork.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run 1: "agreement: 16/20 scored items  (bar: 18/20: below the bar)" — categories line read "clear-accept 4/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4".
2. Partial `--only` re-run (the 4 disagreeing packages plus 5 canaries) after revising four checks: "agreement: 9/9 scored items".
3. Confirming full run (the one saved to `eval-run.txt`): "agreement: 19/20 scored items  (bar: 18/20: PASS)".

**Package analysis**

`pkg-05` (conda/conda#16543): my rubric decided `reject`, the gold label is `accept`. The final run's table row reads "pkg-05  accept  reject   NO     failed: steps-rerunnable, control-run". My `steps-rerunnable` check demands that every input a stranger needs be "shown, quoted, or pinned to an exact public source", and pkg-05's report describes its input file rather than quoting it — "wrote a minimal `env.yml` containing a valid `dependencies:` list plus a `category:` section" — so the grader read the env.yml as unspecified, even though the trigger (the `category:` section) is quoted and any valid dependencies list would hit it. It is a genuine borderline: the same package graded `pass` on the same check in my run 2 (`--only`) and `fail` in run 3, which is grader variance on the check's "immaterial filler may be described" allowance rather than a stable disagreement. The 18/20 bar absorbs it, so I stopped rather than tune the check to chase one flaky package.

**Check rationale**

From `tools/repro-check/rubric.md`, the `policy-followed` check's pass condition, exactly as it reads now: "The comments comply with what the repo's stated policy literally demands of comment text. Treat course packages and drafts as AI-assisted work. If the policy requires disclosing AI use, a disclosure statement must actually appear in the comment. If the policy instead requires comments to be human-written in the contributor's own words, a comment written in the student's own voice satisfies it — no disclosure is owed unless the policy asks for one. Permissive or responsibility-only policies, or no stated policy, pass." It reads that way because my first version treated every stated AI policy as a disclosure requirement, and run 1 falsely rejected `pkg-03` on it: ripgrep's policy says comments "must be written by humans in their own words", which the gold note says a human-voiced comment satisfies. I rejected the simpler rule "any AI policy means disclose" in favour of following what each policy literally demands, keeping the hard disclosure requirement (which `pkg-20`, the disclosure category, still fails) while letting own-words policies be satisfied by own-words comments.

**Trade-offs**

Loosening `policy-followed` (and three other checks) risked flipping packages that already agreed, so before the confirming full run I re-ran the revision with `--only pkg-03,pkg-05,pkg-07,pkg-12,pkg-13,pkg-16,pkg-17,pkg-18,pkg-20` — the four disagreeing packages plus one already-agreeing canary from each category the changes could touch, including `pkg-20` for the single-package `disclosure` category the eval README names as the live case. All canaries held: that run's categories line reads "clear-accept 4/4  disclosure 1/1  no-evidence 1/1  unfollowable-comms 1/1  wrong-target 2/2" and its agreement line "agreement: 9/9 scored items". The trade-off the revised `policy-followed` accepts: a comment that is actually machine-written in a repo with only an own-words rule would pass, because the check cannot verify authorship — it holds the comment to what the policy's text demands, not to what it cannot observe.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
