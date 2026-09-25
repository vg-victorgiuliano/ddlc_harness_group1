---
name: verify
description: Runs the shape tests and states plainly what they do and do not check.
---

# Verify

## Purpose
Run the repository's shape tests against the computed number and state, without overstating it,
what passing them actually proves.

## Entry conditions
- Act has produced a number and named the version its SQL implements.

## Required context
- `project/tests/` (e.g. `self_service_rate_in_range.sql`, `not_null_self-service_account.sql`,
  `no_null_self-service_for_pilot.sql`).

## Prohibited context
- None.

## Procedure
1. Run the shape tests relevant to the metric produced.
2. Record pass or fail for each.
3. State explicitly: these tests confirm the number is the kind of thing a number should be
   (non-null, in range, no duplicate keys). They do not confirm the number is correct, and they
   do not detect the version contradiction Act may have surfaced.

## Evidence produced
- Pass/fail result per shape test.
- An explicit statement of what was, and was not, checked.

## Proposed transition
To Approve (SQL read by a second person).

## Stop or escalation conditions
- Any shape test fails: stop, route to Recover. Do not proceed to Approve on a failing shape
  test.

## Human judgment boundary
Whether a number that passes every shape test is nonetheless wrong is not something this skill
can answer; it belongs with whoever reads the SQL next (Approve) and, ultimately, with Declan
Byrne.
