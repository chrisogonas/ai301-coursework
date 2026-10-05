# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

---

## Posted upstream

**GitHub username**

chrisogonas

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5989587210

Plan, built from my repro report above (https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5861978570):

**Diagnosis.** My traceback runs from `core/security.py:37` straight into passlib's `identify_record`, which raises `UnknownHashError` because `not_a_valid_bcrypt_hash` matches no configured scheme; `verify_password` has no handling around that call, so the error escapes. The control (`True False` for a real bcrypt hash) shows the comparison path itself is fine.

Several people here reported that a corrupt hash with a bcrypt prefix fails differently, so I checked it in the same venv before choosing the catch:

```
$ .venv-repro/Scripts/python -c 'from core.security import verify_password; verify_password("password", "$2b$notarealhash")'
Traceback (most recent call last):
  ...
ValueError: not enough values to unpack (expected 2, got 1)
$ .venv-repro/Scripts/python -c 'from core.security import verify_password; verify_password("password", "$2b$12$tooShort")'
Traceback (most recent call last):
  ...
ValueError: salt too small (bcrypt requires exactly 22 chars)
```

`UnknownHashError` is itself a `ValueError` subclass, so these are two shapes of one problem: passlib raising on a stored hash it cannot parse.

**Change (one, bounded).** In `verify_password`, wrap only the existing `pwd_context.verify(...)` call in `try` / `except ValueError: return False`, with a one-line docstring note. Remove the strict `xfail` marker (H-05) from `test_verify_with_wrong_hash_format`, per CONTRIBUTING, and add one test for the `$2b$notarealhash` case. Not touching `hash_password`, the JWT functions, the `CryptContext` setup, or the login route: from reading `api/routes/auth.py:80` (not something I ran), its existing `not verify_password(...)` branch already returns 401 once `verify_password` returns `False`, where today the escaped error falls into its generic 500 handler. Not catching `Exception` broadly: anything other than passlib rejecting a malformed hash should still raise.

**Test plan.** On the fix branch: the direct call from my repro should print `False` instead of a traceback; the control should still print `True False`; the two `$2b$` probe hashes above and an empty string should all return `False`; `test_verify_with_wrong_hash_format` should go from `XFAIL` to `PASSED` and the new test should pass. Then the full unit suite and `make check`, and all five CI jobs green on the PR.

**Unknowns.** `ValueError` is broader than `UnknownHashError`; I have not seen passlib raise it for anything but a malformed stored hash (the existing 1000-character password test still returns `True`). If a malformed hash turns up that raises something other than `ValueError`, I will post it as a separate finding rather than widen the catch.

Working on `fix/72-verify-password-unknown-hash` on my fork; I will link the PR here.

---

## Your branch

**Branch**

`fix/72-verify-password-unknown-hash`

(issue #72; pushed to my fork at https://github.com/chrisogonas/pathreview-ai301-fa26-s1/tree/fix/72-verify-password-unknown-hash, commit `aa020a4`)

**Evidence**

My Unit 2 repro steps (1–3), plus the three probe hashes from my plan, re-run with the same
commands in the same `.venv-repro` (Windows 11 Pro, Python 3.14.0, passlib 1.7.4, bcrypt
4.3.0). The before matches the output I posted in Unit 2
(https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5861978570).
Both blocks are unedited terminal output from one script run against each commit. The
`(trapped) error reading bcrypt version` block is passlib 1.7.4's known warning with
bcrypt 4.x on first backend load, noted as noise in my Unit 2 report.

Before — commit `f89c06f` (unchanged `main`):

```text
## git: HEAD @ f89c06f

$ .venv-repro/Scripts/python -c "from core.security import verify_password; print(verify_password('password', 'not_a_valid_bcrypt_hash'))"
Traceback (most recent call last):
  File "<string>", line 1, in <module>
    from core.security import verify_password; print(verify_password('password', 'not_a_valid_bcrypt_hash'))
                                                     ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\security.py", line 37, in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
                ~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\context.py", line 2343, in verify
    record = self._get_or_identify_record(hash, scheme, category)
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\context.py", line 2031, in _get_or_identify_record
    return self._identify_record(hash, category)
           ~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\context.py", line 1132, in identify_record
    raise exc.UnknownHashError("hash could not be identified")
passlib.exc.UnknownHashError: hash could not be identified

$ .venv-repro/Scripts/python -c "from core.security import hash_password, verify_password; h = hash_password('password'); print(verify_password('password', h), verify_password('wrong-password', h))"
(trapped) error reading bcrypt version
Traceback (most recent call last):
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\handlers\bcrypt.py", line 620, in _load_backend_mixin
    version = _bcrypt.__about__.__version__
              ^^^^^^^^^^^^^^^^^
AttributeError: module 'bcrypt' has no attribute '__about__'
True False

$ .venv-repro/Scripts/python -m pytest tests/unit/test_security.py -k wrong_hash_format -p no:cacheprovider -q -rxX
x                                                                        [100%]
============================== warnings summary ===============================
core\config.py:7
  C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\config.py:7: PydanticDeprecatedSince20: Support for class-based `config` is deprecated, use ConfigDict instead. Deprecated in Pydantic V2.0 to be removed in V3.0. See Pydantic V2 Migration Guide at https://errors.pydantic.dev/2.13/migration/
    class Settings(BaseSettings):

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
=========================== short test summary info ===========================
XFAIL tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format - issue #72 (manifest H-05): password verify raises UnknownHashError instead of returning False
24 deselected, 1 xfailed, 1 warning in 0.31s

$ .venv-repro/Scripts/python -m pytest tests/unit/test_security.py -k wrong_hash_format --runxfail -p no:cacheprovider -q --tb=line
F                                                                        [100%]
================================== FAILURES ===================================
E   passlib.exc.UnknownHashError: hash could not be identified
C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\context.py:1132: passlib.exc.UnknownHashError: hash could not be identified
============================== warnings summary ===============================
core\config.py:7
  C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\config.py:7: PydanticDeprecatedSince20: Support for class-based `config` is deprecated, use ConfigDict instead. Deprecated in Pydantic V2.0 to be removed in V3.0. See Pydantic V2 Migration Guide at https://errors.pydantic.dev/2.13/migration/
    class Settings(BaseSettings):

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
=========================== short test summary info ===========================
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
1 failed, 24 deselected, 1 warning in 0.22s

$ .venv-repro/Scripts/python -c 'from core.security import verify_password; print(verify_password("password", "$2b$notarealhash"))'
Traceback (most recent call last):
  File "<string>", line 1, in <module>
    from core.security import verify_password; print(verify_password("password", "$2b$notarealhash"))
                                                     ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\security.py", line 37, in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
                ~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\context.py", line 2347, in verify
    return record.verify(secret, hash, **kwds)
           ~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\utils\handlers.py", line 788, in verify
    self = cls.from_string(hash, **context)
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\handlers\bcrypt.py", line 174, in from_string
    rounds_str, data = tail.split(u("$"))
    ^^^^^^^^^^^^^^^^
ValueError: not enough values to unpack (expected 2, got 1)

$ .venv-repro/Scripts/python -c 'from core.security import verify_password; print(verify_password("password", "$2b$12$tooShort"))'
Traceback (most recent call last):
  File "<string>", line 1, in <module>
    from core.security import verify_password; print(verify_password("password", "$2b$12$tooShort"))
                                                     ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\security.py", line 37, in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
                ~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\context.py", line 2347, in verify
    return record.verify(secret, hash, **kwds)
           ~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\utils\handlers.py", line 788, in verify
    self = cls.from_string(hash, **context)
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\handlers\bcrypt.py", line 179, in from_string
    return cls(
        rounds=rounds,
    ...<2 lines>...
        ident=ident,
    )
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\utils\handlers.py", line 1149, in __init__
    super(HasManyIdents, self).__init__(**kwds)
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\utils\handlers.py", line 1794, in __init__
    super(HasRounds, self).__init__(**kwds)
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\utils\handlers.py", line 1411, in __init__
    salt = self._parse_salt(salt)
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\utils\handlers.py", line 1421, in _parse_salt
    return self._norm_salt(salt)
           ~~~~~~~~~~~~~~~^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\handlers\bcrypt.py", line 237, in _norm_salt
    salt = super(_BcryptCommon, cls)._norm_salt(salt, **kwds)
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\utils\handlers.py", line 1466, in _norm_salt
    raise ValueError(msg)
ValueError: salt too small (bcrypt requires exactly 22 chars)

$ .venv-repro/Scripts/python -c 'from core.security import verify_password; print(repr(verify_password("password", "")))'
Traceback (most recent call last):
  File "<string>", line 1, in <module>
    from core.security import verify_password; print(repr(verify_password("password", "")))
                                                          ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\security.py", line 37, in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
                ~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\context.py", line 2343, in verify
    record = self._get_or_identify_record(hash, scheme, category)
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\context.py", line 2031, in _get_or_identify_record
    return self._identify_record(hash, category)
           ~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\context.py", line 1132, in identify_record
    raise exc.UnknownHashError("hash could not be identified")
passlib.exc.UnknownHashError: hash could not be identified

$ .venv-repro/Scripts/python -m pytest tests/unit/test_security.py -p no:cacheprovider -q
.....................x...                                                [100%]
============================== warnings summary ===============================
core\config.py:7
  C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\config.py:7: PydanticDeprecatedSince20: Support for class-based `config` is deprecated, use ConfigDict instead. Deprecated in Pydantic V2.0 to be removed in V3.0. See Pydantic V2 Migration Guide at https://errors.pydantic.dev/2.13/migration/
    class Settings(BaseSettings):

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
24 passed, 1 xfailed, 1 warning in 5.12s
```

After — commit `aa020a4` on `fix/72-verify-password-unknown-hash`:

```text
## git: fix/72-verify-password-unknown-hash @ aa020a4

$ .venv-repro/Scripts/python -c "from core.security import verify_password; print(verify_password('password', 'not_a_valid_bcrypt_hash'))"
False

$ .venv-repro/Scripts/python -c "from core.security import hash_password, verify_password; h = hash_password('password'); print(verify_password('password', h), verify_password('wrong-password', h))"
(trapped) error reading bcrypt version
Traceback (most recent call last):
  File "C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\.venv-repro\Lib\site-packages\passlib\handlers\bcrypt.py", line 620, in _load_backend_mixin
    version = _bcrypt.__about__.__version__
              ^^^^^^^^^^^^^^^^^
AttributeError: module 'bcrypt' has no attribute '__about__'
True False

$ .venv-repro/Scripts/python -m pytest tests/unit/test_security.py -k wrong_hash_format -p no:cacheprovider -q -rxX
.                                                                        [100%]
============================== warnings summary ===============================
core\config.py:7
  C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\config.py:7: PydanticDeprecatedSince20: Support for class-based `config` is deprecated, use ConfigDict instead. Deprecated in Pydantic V2.0 to be removed in V3.0. See Pydantic V2 Migration Guide at https://errors.pydantic.dev/2.13/migration/
    class Settings(BaseSettings):

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
1 passed, 25 deselected, 1 warning in 0.23s

$ .venv-repro/Scripts/python -m pytest tests/unit/test_security.py -k wrong_hash_format --runxfail -p no:cacheprovider -q --tb=line
.                                                                        [100%]
============================== warnings summary ===============================
core\config.py:7
  C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\config.py:7: PydanticDeprecatedSince20: Support for class-based `config` is deprecated, use ConfigDict instead. Deprecated in Pydantic V2.0 to be removed in V3.0. See Pydantic V2 Migration Guide at https://errors.pydantic.dev/2.13/migration/
    class Settings(BaseSettings):

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
1 passed, 25 deselected, 1 warning in 0.19s

$ .venv-repro/Scripts/python -c 'from core.security import verify_password; print(verify_password("password", "$2b$notarealhash"))'
False

$ .venv-repro/Scripts/python -c 'from core.security import verify_password; print(verify_password("password", "$2b$12$tooShort"))'
False

$ .venv-repro/Scripts/python -c 'from core.security import verify_password; print(repr(verify_password("password", "")))'
False

$ .venv-repro/Scripts/python -m pytest tests/unit/test_security.py -p no:cacheprovider -q
..........................                                               [100%]
============================== warnings summary ===============================
core\config.py:7
  C:\CoopNET\genai\CodePath\ai301\pathreview-ai301-fa26-s1\core\config.py:7: PydanticDeprecatedSince20: Support for class-based `config` is deprecated, use ConfigDict instead. Deprecated in Pydantic V2.0 to be removed in V3.0. See Pydantic V2 Migration Guide at https://errors.pydantic.dev/2.13/migration/
    class Settings(BaseSettings):

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
26 passed, 1 warning in 5.08s
```

Summary: step 1 went from `UnknownHashError` to `False`; the step 2 control stayed
`True False`; the H-05 covering test went from `XFAIL` (and `FAILED` under `--runxfail`) to
passed; the `$2b$notarealhash`, `$2b$12$tooShort` and `''` probes went from
`ValueError` / `ValueError` / `UnknownHashError` to `False`; `test_security.py` went from
24 passed + 1 xfailed to 26 passed. CI-equivalent checks on the branch (Python 3.11 venv,
`pip install -e ".[dev]"`): `ruff check .` all passed, `black --check .` 110 files
unchanged, `mypy` no issues in 76 files, `pytest tests/unit` 377 passed / 52 xfailed (other
seeded bugs), no XPASS.

## Eval iterations

**Run history**

1. First full-run attempt: crashed before grading any package, so no score. On Windows,
   Python 3.8 wrote the prompt to the `claude` subprocess as cp1252 and hit
   `UnicodeEncodeError: 'charmap' codec can't encode characters`. Fixed by running the
   harness in Python's UTF-8 mode (`PYTHONUTF8=1`); no rubric, guide, procedure, or
   harness change.
2. Full run with the same files: `agreement: 19/20 scored items  (bar: 18/20: PASS)`,
   `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.
   This is the run saved in `eval-run.txt`.

Final score: **19/20**, matching the agreement line in `eval-run.txt`. I made no
`--only` re-runs and no revisions after it, because it passed the bar and the category floor
on the first scored run (see Package analysis for why I chose not to chase pkg-14).

**Package analysis**

**pkg-14** (zellij: raw OSC color-query responses leak into the pane on SSH reattach). Gold
label: **accept** (clear-accept). My rubric decided: **reject**, failing `diagnosis-grounded`
and `honest-claims`. All other required checks passed.

Why my rubric read it that way, quoting the skill's evidence lines:

- `honest-claims` fail: "'which I have working (the leak's origin is visible in zellij
  --debug output)' claims confirmed tracing the package shows no evidence of." The plan
  does say that, and my pass condition fails "a claim of having traced, confirmed, or
  verified something when the package shows no such work". So the check applied its words
  literally to an aside. The rest of the plan is careful about what it does not know
  ("exact functions to be pinned in the PR after tracing"), and the repro evidence
  (0.44.1 clean, 0.44.2+ leaking, fresh attach clean) supports the diagnosis without that
  aside. My condition can't tell a borrowed root cause dressed up as "I traced it" (the
  wrong-cause pattern) from a supporting claim that the evidence already makes
  unnecessary.
- `diagnosis-grounded` fail: "Plan's own fix for Control B is self-contradictory: cache
  repopulated after one clean attach should stop further queries/leaks by its own logic,
  yet repro evidence shows the very next launch leaks again." I think this is a misread.
  The plan says the empty cache sends the *next* attach down the fresh-attach path, which
  is clean, and the repro shows exactly that: one clean attach, then the following
  *reattach* leaks. My evidence guide tells the grader to weigh controls most heavily,
  which is usually right (it is what catches calib-03 and the wrong-cause packages). Here
  it made the grader hunt for a contradiction in a control the plan had already explained.

So the miss comes from two strict readings stacking up, not from a missing check. Loosening
`honest-claims` alone would not flip it, because `diagnosis-grounded` also failed. With
19/20 and every category matched, I kept both checks strict rather than risk flipping a
wrong-cause package for one more point.

**Check rationale**

From `tools/plan-check/rubric.md`, exactly as it reads:

```text
| test-observes-fix | The plan's test plan (wherever it says how success will be checked), read against the repro-evidence block's steps and expected/actual result | Passes if the test exercises the reproduced behavior and names the observable result that must now differ: re-running the repro steps with the expected result stated, or an automated test that asserts that same behavior. Manual checks pass. Fails if the test cannot observe the bug, e.g. only "run the existing test suite", "make sure nothing regresses", "it works", or a test of something other than the reproduced behavior. | required |
```

Why it reads that way: in the in-class activity my group's version was "Test (required):
look in the testing section; pass if there exists an automated test which checks the bugs
and makes sure the fixes works properly." In Phase 3 that check held calib-01, whose gold
label is ready, because calib-01's test plan is the repro steps re-run by hand ("at step 3
the color must flip without leaving the view"). Automation was the wrong signal. What
makes a test plan decisive is whether it can *see the bug flip*. calib-04 shows the other
side: "Run the full test suite (`cargo test --workspace`) and make sure nothing regresses"
sounds rigorous and is automated, but nothing in it exercises the absolute-path glob from
the repro. So I rewrote the pass condition around the observable outcome measured against
the repro evidence. I added "Manual checks pass" explicitly so the grader can't bring the
automation requirement back. I also dropped "look in the testing section": my group's
debrief found that Claude goes looking for named sections, and calib-01 has none, so the
check now says "wherever it says how success will be checked".

**Trade-offs**

What `test-observes-fix` gives up: because manual checks pass, a plan that only re-runs the
repro by hand and adds no regression test still comes out ready. Nothing in my rubric asks
a plan to leave a test behind that would catch the bug coming back, and I accept missing
that case. Many repos (Path Review's CONTRIBUTING included) expect a test with every
change, and my rubric leaves that to review.

How I know it changed nothing elsewhere: in the saved run, `test-observes-fix` failed on
only two packages, pkg-10 and pkg-17. Both are gold-reject unbuildable packages that also
fail `executable` and four other required checks. It never failed a gold-accept package,
and it was never the only failing check (pkg-20, by contrast, is held by `comms-fit`
alone). So in this eval set the check decides no verdict on its own. It is insurance for
plans like calib-04, whose diagnosis, scope and comms are all sound and whose only flaw is a
test that can't see the bug. That gold call is debatable, and it is the one case where this
check alone decides the outcome.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
