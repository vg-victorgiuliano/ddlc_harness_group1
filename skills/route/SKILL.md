---
name: route
description: Chooses ad hoc query vs. the monthly build, and surfaces any unresolved duplicate-request judgment before Act runs.
---

# Route

## Purpose
Decide how the number gets built (an ad hoc query against current marts, or waiting for or
triggering the monthly refresh), and carry forward any duplicate-request flag Intake raised,
without resolving it.

## Entry conditions
- Context has confirmed (or flagged as unknown) the extract's period coverage and named a
  metric version.

## Required context
- Whether the monthly build has already run for the current period (`staging.extract_metadata`,
  or its absence).
- Any duplicate-request flag carried from Intake.

## Prohibited context
- None beyond the standing repository boundary (read-only against the track repository, no
  network).

## Procedure
1. If the monthly build has already run for the requested period, route to Act against the
   existing marts.
2. If it has not, and the deadline allows, route to Act after triggering `project/run.py`.
3. If a duplicate-request flag is present (as with `REQUEST-007` against `REQUEST-011`), do not
   decide it here. Record it as an open judgment call and name who owns it: Sofia Marques, per
   `ISSUE-36`.

## Evidence produced
- The chosen path (ad hoc vs. monthly build) and why.
- An unresolved-duplicate flag, if one exists, addressed to its owner, neither silently dropped
  nor silently resolved.

## Proposed transition
To Act (Model built / number computed).

## Stop or escalation conditions
- The deadline does not allow triggering a fresh build and no current-period build exists:
  escalate to Declan Byrne rather than build against a stale extract.
- A duplicate-request flag has gone unjudged for more than one reporting period: escalate to
  Sofia Marques explicitly, rather than let it age silently the way `ISSUE-36` has.

## Human judgment boundary
Whether two requests are asking for the same number is Sofia Marques's call. Route names the
candidate pair; it does not merge or separate them.
