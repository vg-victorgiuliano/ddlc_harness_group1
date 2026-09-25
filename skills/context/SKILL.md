---
name: context
description: Confirms the extract the number will be built from actually covers the requested period, and names which metric definition version applies.
---

# Context

## Purpose
Assemble what Act will read: a data extract confirmed to cover the requested period, and a
named metric definition version. This is the stage the interview names as the real gap in the
current process: "it does not check that the extract is the right month."

## Entry conditions
- Intake has produced a bounded request: metric, grain, period, deadline.

## Required context
- `data/ops-extract/extract-manifest.yaml`, for the date the current extract was taken and the
  period it states it covers.
- `project/metrics/metric-definitions.yaml`, for every version of the requested metric and which
  is current as of the requested period.
- Whether the requested period is still open (a reporting period in progress) or closed.

## Prohibited context
- Any extract or definition file outside `portwell-analytics`. This skill reads the assigned
  track repository only, read-only.

## Procedure
1. Read the extract manifest. Compare `taken_at` and the manifest's stated coverage against the
   requested period.
2. If no manifest survives for the period in question, record that as unknown. Do not infer a
   date. This is exactly the situation for `REQUEST-004`'s July extract: no manifest for it
   exists in this repository.
3. Read `metric-definitions.yaml` for the requested metric. Name every version whose
   `effective_from` falls on or before the requested period's end, and whether a later version
   has since superseded it.
4. If the request did not state a version, name the version that was current as of the period,
   and flag that the requester was never asked to confirm it.

## Evidence produced
- A statement of which extract was used and whether its period coverage was confirmed or is
  unknown.
- A named metric version, and whether it was requester-confirmed or context-assumed.

## Proposed transition
To Route (Ad hoc vs. monthly-build path decided).

## Stop or escalation conditions
- The extract's period coverage cannot be confirmed: stop, mark Blocked. Do not proceed on an
  extract of unknown vintage.
- The requested period is still open and the metric's current version differs from the version
  used earlier in that same period (the `POLICY-05` mid-period question `INCIDENT-03` raised):
  escalate to the metric owner (Sofia Marques) before Act runs.

## Human judgment boundary
Whether a definition change is safe to apply while a reporting period is open belongs to Sofia
Marques, the metric owner. This skill states the conflict; it does not resolve it.
