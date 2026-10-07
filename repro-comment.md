Reproduced as described.

Environment: macOS 15.7.7 (arm64, Apple Silicon), Python 3.14.5, my fork of codepath/pathreview-ai301-fa26-s1 at commit f89c06f (main, clean tree).

Setup deviation: I did not run the documented make setup path (Docker, Postgres, Redis, alembic upgrade head, frontend install). safety/pii_scrubber.py is pure regex with no DB or API dependency, so I created a venv and installed the dev extra directly, then ran only this module's tests:

The four named tests:

All four xfail, matching the issue.

Observed — the issue's own snippet, run verbatim:

Matches the issue exactly: in the same string the dashed number is redacted and the parenthesized one is not, and detect() returns [] for the parenthesized format.

Control — dashed format alone, same build:

The control redacts and detects normally, so phone redaction works in general and the failure is specific to the parenthesized format, not to the feature as a whole.

Expected: scrub() redacts (555) 123-4567 the same way it redacts 555-123-4567, and detect() reports it.

Actual: reproduced as described — the parenthesized format passes through scrub() unredacted and detect() finds nothing for it, while the dashed format is handled correctly, on f89c06f in the environment above.

I have not diagnosed the cause in the pattern or proposed a fix; this report covers the reproduction only.

