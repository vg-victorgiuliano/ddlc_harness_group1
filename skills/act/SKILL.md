---
name: act
description: Builds or extends the model and computes the number, recording which version of the metric's logic the SQL actually implements.
---

# Act

## Purpose
Do the domain work: write or extend a model in `project/models/`, run the build, and read the
number. Record which version of the metric definition the SQL that produced it actually
implements, not only which version the definitions file currently names as current.

## Entry conditions
- Route has chosen a path and surfaced any duplicate-request flag.

## Required context
- The relevant mart's SQL (e.g. `project/models/marts/self_service.sql`).
- The metric version named by Context.

## Prohibited context
- Any destructive operation without a named approver. `project/rebuild.py` drops a mart with no
  approval argument today (`ISSUE-37`, `POLICY-06`); this skill does not invoke it, and the
  absence of that control is noted, not built around, per this module's scope.

## Procedure
1. Run the build (or read the existing mart output, per Route's decision).
2. Read the mart's SQL and state, in plain terms, which metric definition version its logic
   actually matches. For `self_service.sql`, that is version 2 (it sums `is_self_served`, with no
   exclusion for reopened tickets or materially edited answers), regardless of what
   `metric-definitions.yaml` currently names as current.
3. If the mart's implemented version differs from the version Context named as applicable,
   record both, as a contradiction, not a discrepancy to silently reconcile. This is exactly the
   gap behind `INCIDENT-03`: the definitions file moved to v3 on 2026-07-01; the mart's SQL did
   not move with it, per `docs/pr-notes/0088-self-service-v3.md`.
4. Produce the number, tagged with the version its SQL actually implements.

## Evidence produced
- The computed number.
- The version the SQL implements, stated explicitly, alongside the version the definitions file
  currently names, whenever they differ.

## Proposed transition
To Verify (Shape-tested).

## Stop or escalation conditions
- The mart's implemented version cannot be determined by reading its SQL: stop, escalate to
  Declan Byrne (author of the staging and mart models).

## Human judgment boundary
When a model is wrong versus when the data is wrong is Declan Byrne's call. This skill states
what the SQL does; it does not judge whether that is the right thing for it to do.
