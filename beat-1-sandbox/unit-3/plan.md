# Plan: `verify_password` fails closed on malformed stored hashes (#72)

- Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72
- My repro report: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5861978570
- Repo state: `main` at `f89c06f` (same commit as upstream `main`)
- Branch: `fix/72-verify-password-unknown-hash` on my fork

## Repro evidence this plan relies on

Quoted from my posted repro report (Windows 11 Pro, Python 3.14.0,
passlib 1.7.4, bcrypt 4.3.0, fresh venv, commit `f89c06f`):

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

2. Control, well-formed bcrypt hashes take the normal path:

   ```
   $ .venv-repro/Scripts/python -c "from core.security import hash_password, verify_password; h = hash_password('password'); print(verify_password('password', h), verify_password('wrong-password', h))"
   True False
   ```

3. Through the suite: the marker absorbs it (`... test_verify_with_wrong_hash_format XFAIL`),
   and with `--runxfail` the raw error surfaces before the test's
   `assert result is False` is reached:

   ```
   >           raise exc.UnknownHashError("hash could not be identified")
   E           passlib.exc.UnknownHashError: hash could not be identified
   FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
   ```

Expected: `verify_password("password", "not_a_valid_bcrypt_hash")`
returns `False`. Actual: `UnknownHashError` escapes from
`core/security.py:37`.

### Follow-up probe (run 2026-10-05, same venv and commit, unchanged code)

Classmates on the thread reported that a hash that *looks* like
bcrypt but is corrupt raises a different error. I checked it myself
before committing to the catch:

```
$ .venv-repro/Scripts/python -c "
import passlib.exc as e
print('UnknownHashError subclasses ValueError:', issubclass(e.UnknownHashError, ValueError))
from core.security import verify_password
for h in ['not_a_valid_bcrypt_hash', '\$2b\$notarealhash', '\$2b\$12\$tooShort', '']:
    try: print(repr(h), '->', verify_password('password', h))
    except Exception as x: print(repr(h), '->', type(x).__module__ + '.' + type(x).__name__ + ':', x)
"
UnknownHashError subclasses ValueError: True
'not_a_valid_bcrypt_hash' -> passlib.exc.UnknownHashError: hash could not be identified
'$2b$notarealhash' -> builtins.ValueError: not enough values to unpack (expected 2, got 1)
'$2b$12$tooShort' -> builtins.ValueError: salt too small (bcrypt requires exactly 22 chars)
'' -> passlib.exc.UnknownHashError: hash could not be identified
```

So there are two failure shapes, and passlib's `UnknownHashError` is
a subclass of `ValueError`.

## Diagnosis

`verify_password` (`core/security.py:27-37`) returns
`bool(pwd_context.verify(plain_password, hashed_password))` with no
error handling, and passlib signals a malformed stored hash by raising
rather than returning a boolean:

- When the string matches no configured scheme (here only `bcrypt`),
  `CryptContext` fails at scheme identification and raises
  `UnknownHashError` from `identify_record` (repro step 1; probe rows
  `not_a_valid_bcrypt_hash` and `''`).
- When the string carries the bcrypt `$2b$` prefix, it is identified
  as bcrypt, and the bcrypt handler then fails parsing the malformed
  body with a plain `ValueError` (probe rows `$2b$notarealhash` and
  `$2b$12$tooShort`).

Nothing in `verify_password` catches either, so both propagate to the
caller. The control (repro step 2) shows well-formed hashes, matching
or not, return `True`/`False` normally, so the comparison path itself
is fine: the defect is limited to stored hashes passlib cannot parse.

From reading the code (not something I ran): the only production
caller is the login route, `api/routes/auth.py:80`. Its `except
Exception` block would turn either escaped error into a 500 "Login
failed" rather than the 401 "Invalid email or password" a wrong
credential gets, which is why failing closed matters here.

## Scope

One bounded change: make `verify_password` return `False` when passlib
rejects the stored hash as malformed, and remove the issue's xfail
marker.

In scope:
- Catch `ValueError` around the existing `pwd_context.verify(...)`
  call in `verify_password` and return `False`. `ValueError` covers
  both failure shapes above, since `UnknownHashError` subclasses it.
  The `try` wraps only that one call.
- One line in the `verify_password` docstring's `Returns:` noting that
  a malformed or unrecognized stored hash also returns `False` (the
  repo requires Google-style docstrings on public functions).
- Delete the `@pytest.mark.xfail(strict=True, reason="issue #72
  (manifest H-05): ...")` decorator on `test_verify_with_wrong_hash_format`,
  as `docs/CONTRIBUTING.md` requires for seeded bugs.
- Add one test, `test_verify_with_malformed_bcrypt_hash`, asserting
  `verify_password("password", "$2b$notarealhash") is False`, so the
  second failure shape stays covered (CONTRIBUTING: "every code change
  should include or update relevant tests").

Not in scope:
- `hash_password`, `create_access_token`, `decode_access_token`, and
  the `CryptContext` configuration.
- The login route in `api/routes/auth.py`: once `verify_password`
  returns `False`, its existing `not verify_password(...)` branch
  already produces the 401, so it needs no change.
- A bare `except Exception`: errors that are not passlib rejecting a
  malformed hash (bugs, import or configuration problems) should keep
  raising.
- Logging malformed hashes, and any lint/type baseline cleanup (per
  CONTRIBUTING's "do not bulk-fix" rule).

## Files I will touch

- `core/security.py`: the body and docstring of `verify_password`
  (lines 27-37). No new import is needed for `ValueError`.
- `tests/unit/test_security.py`: remove the xfail decorator above
  `test_verify_with_wrong_hash_format` (lines 218-221), leaving its
  body as is, and add `test_verify_with_malformed_bcrypt_hash` next to
  it in the same `TestSecurity` class.

## Approach

1. Branch from `main`: `git checkout -b fix/72-verify-password-unknown-hash`.
2. In `core/security.py`, change the body of `verify_password` to:

   ```python
   try:
       return bool(pwd_context.verify(plain_password, hashed_password))
   except ValueError:
       # passlib raises UnknownHashError (a ValueError subclass) for an
       # unrecognized hash, and ValueError for a malformed bcrypt hash.
       return False
   ```

   and add the docstring line.
3. Remove the xfail decorator from `test_verify_with_wrong_hash_format`
   and add `test_verify_with_malformed_bcrypt_hash`.
4. Run the test plan below, then `make check && make test-unit` (or
   the underlying `ruff`, `black --check`, `mypy`, `pytest tests/unit`
   commands if `make` is unavailable on my Windows machine).
5. Commit as `fix(api): return False from verify_password on malformed hashes`
   with `Fixes #72`, push to my fork, and open the PR with the repo's
   PR template; confirm all five CI jobs (`lint`, `typecheck`,
   `test-unit`, `test-integration`, `frontend`) are green.

## Test plan

Re-run my repro steps and the probe on the fix branch, same venv:

1. Direct call:
   `.venv-repro/Scripts/python -c "from core.security import verify_password; print(verify_password('password', 'not_a_valid_bcrypt_hash'))"`
   Expect: prints `False`, no traceback (was: `UnknownHashError`).
2. Control:
   `.venv-repro/Scripts/python -c "from core.security import hash_password, verify_password; h = hash_password('password'); print(verify_password('password', h), verify_password('wrong-password', h))"`
   Expect: still `True False`, so the working path is untouched.
3. Covering test, marker removed:
   `.venv-repro/Scripts/python -m pytest tests/unit/test_security.py -k wrong_hash_format`
   Expect: `PASSED` (was: `XFAIL`, and `FAILED` under `--runxfail`).
4. Probe re-run: each of `$2b$notarealhash`, `$2b$12$tooShort`, and
   `''` through `verify_password('password', ...)`.
   Expect: `False` for all three (was: `ValueError`, `ValueError`,
   `UnknownHashError`), and the new
   `test_verify_with_malformed_bcrypt_hash` `PASSED`.
5. Whole file and suite:
   `.venv-repro/Scripts/python -m pytest tests/unit/test_security.py`, then
   `pytest tests/unit`. Expect: all pass, no `XPASS(strict)` anywhere.

## Risks and unknowns

- **Breadth of `ValueError`.** Catching `ValueError` instead of only
  `UnknownHashError` could also swallow a `ValueError` passlib raises
  for some reason other than a malformed stored hash. I have not seen
  one: the 1000-character password test already in the file returns
  `True`, so long secrets do not raise here. The `try` wraps only the
  single `pwd_context.verify` call to keep that surface small.
- **Other malformed shapes.** I probed four malformed values; other
  corruptions could in principle raise an exception type that is not a
  `ValueError`. If one shows up during the build, I will report it
  here as a separate finding rather than widen the catch without
  evidence.
- **Silent failure.** Returning `False` hides a corrupted stored hash
  from operators: the user just cannot log in. Logging is outside this
  issue's scope; I will mention it in the PR as a possible follow-up.
- **Environment.** My repro ran on Windows with Python 3.14; CI may run
  a different Python. The change is a plain try/except, so I do not
  expect a difference, but CI is the check.
- **passlib warning noise.** passlib 1.7.4 with bcrypt 4.x prints a
  trapped `error reading bcrypt version` warning on first use. It is
  unrelated to this bug, and I will not touch it.

## Deviations

None. The build matched the plan: commit `aa020a4` on
`fix/72-verify-password-unknown-hash` makes the exact `except ValueError`
change in `verify_password` with the docstring line, removes the H-05
xfail marker, and adds `test_verify_with_malformed_bcrypt_hash`, touching
only the two files listed above.

Every test-plan expectation held on the branch: the direct call prints
`False`, the control still prints `True False`, the three probe hashes
all return `False`, both tests pass, and `test_security.py` is 26 passed.
The only adjustment was environmental, which step 4 of the approach
already allowed for: `make` is not installed on my Windows machine, so I
ran the underlying commands in a Python 3.11 venv matching CI. `ruff
check` passed, `black --check` left all files unchanged, `mypy` reported
no issues, and `pytest tests/unit` gave 377 passed and 52 xfailed (other
seeded bugs), with no XPASS. My posted plan comment is still accurate, so
no follow-up comment is needed.
