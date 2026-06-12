# Fix triage

10 break reports were filed and all reproduce. They collapse to **five root-cause
defects** (several issues are the same defect via a different path/property):

| Root cause | Issues | Fix |
|------------|--------|-----|
| IDOR — `GET /notes/<id>` has no login/ownership check | #1, #5, #10 | Require a session and `note.owner_id == current user` before returning a note |
| SQL injection — queries built with string formatting | #2, #6, #8, #4 (SQLi half) | Use parameterized queries (`?` placeholders) everywhere |
| Stored XSS — user content interpolated into HTML unescaped | #3, #7 | HTML-escape all user-supplied values (`markupsafe.escape`) |
| Debug mode — `debug=True` exposes the Werkzeug console + stack traces | #4, #8, #9 (info-leak half) | Run with `debug=False`; bad input now returns a clean 4xx instead of a traceback |
| Hardcoded `secret_key` — predictable session-signing key | #9 | Load the key from `SECRET_KEY` env or generate a random one at startup |

## We are fixing (this PR)

1. **#1 / #5 / #10 — IDOR / canary disclosure.** Highest severity (full P1 bypass, canary
   leaked to anonymous clients). One ownership check closes all three.
2. **#2 / #6 — SQLi auth bypass on `/login`.** Complete authentication bypass; parameterizing
   the query closes both.
3. **#8 — `create_note` crash / SQLi on apostrophe.** Same string-formatting defect as the
   login SQLi; parameterizing the INSERT fixes the crash (P3) and the injection (P4).
4. **#3 / #7 — Stored XSS.** Escape user content on output.
5. **#4 / #9 — debug console + hardcoded key.** Set `debug=False`, add error handling, and
   stop hardcoding the signing key.

After this PR, every one of the 10 reports should no longer reproduce against the running app,
and `tests/build_check.py` stays green.

## We are not fixing (yet)

- Nothing deferred — all confirmed reports are addressed in this PR. Plaintext passwords in the
  seed data remain (not reported as a break; would hash them next with `werkzeug.security`).
