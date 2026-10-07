Following up on my reproduction with a plan.

`PII_PATTERNS["phone_us"]` in `safety/pii_scrubber.py` allows an optional dash or dot at each
separator position but not a space, which is exactly why `(555) 123-4567` doesn't match — the
space after the closing parenthesis is what breaks it, not the parentheses themselves
(confirmed by testing the pattern directly, and by closing the space to restore the match).

Plan: add a literal space alongside the dash/dot at all three separator positions in that one
pattern, nothing else. Using a literal space rather than `\s`, since `\s` would also match
newlines and could span unrelated lines in prose text.

Test: the issue's own repro snippet plus the four named tests
(`test_us_phone_number_redaction`, `test_us_phone_formats`, `test_detect_phone_pii`,
`test_phone_at_start_of_text`) flipping from `XFAIL` to passing, with the rest of the suite
staying green.

Not touching in this change: the leading `(`/`+` stays unredacted after the fix (a
pre-existing property of the pattern's word-boundary placement, not something this change
introduces — it just never surfaced before since the space-separated case never matched at
all), and `test_mixed_pii_and_text`'s `xfail` marker, whose actual failure is unrelated
over-redaction of ordinary prose, not a phone number — a different defect for a different
issue.
