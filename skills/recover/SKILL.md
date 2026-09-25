---
name: recover
description: Investigates a disputed figure, gives an explanation, and names what remains unfixed rather than closing the item quietly.
---

# Recover

## Purpose
Handle a figure Observe flagged as disputed: find out why it changed, explain it to whoever
asked, and state plainly what, if anything, was structurally corrected as a result. This is
where `REQUEST-004` actually sits today, by way of `INCIDENT-03`: explained, not fixed, and the
follow-up (`ISSUE-30`) still has no owner.

## Entry condition
Observe has logged a dispute, or Verify/Handoff has returned a failure that could not be
resolved within Context or Act alone.

## Required context
- Both periods' figures and the version each was computed under (carried from Observe).
- The metric's definition history in `project/metrics/metric-definitions.yaml`.
- The mart SQL's actually-implemented version, from Act's record.
- `docs/pr-notes/` and `docs/incidents/` for any prior change that could explain the movement.

## Required evidence
A stated cause for the disputed change, and, where two records disagree on the cause, both
records are named and the disagreement is left open. `INCIDENT-03` and
`docs/pr-notes/0088-self-service-v3.md` disagree exactly this way here: one attributes the move
to the definition changing, the other records that the mart's SQL was deliberately left on the
old rule. Recover does not pick the more plausible story.

## Prohibited context
- Editing `docs/incidents/` or `docs/pr-notes/` to make them consistent. Correcting the system
  that produced the disagreement is the work; editing the record of what happened is not
  (`docs/incidents/README.md`).

## Procedure
1. Compare the disputed periods' figures against the versions Act recorded as actually
   implemented for each, not only the version the definitions file names as current for each
   period.
2. If the versions differ, state that as the candidate cause. If the mart's implemented version
   did not in fact change between the two periods, state that too, and name the contradiction
   rather than silently preferring one account.
3. Give the requester an explanation, dated, naming what is known and what is not.
4. State whether anything is being structurally changed as a result: a consumer list started, a
   mart updated to match its definitions file, an announcement made. If nothing is, say so
   explicitly, and name who now owns that gap, following `ISSUE-30`'s own pattern: an issue with
   a named owner, not a closed ticket.
5. Do not mark the underlying request Delivered-and-confirmed. It is not confirmed; it is
   disputed and explained.

## Exit condition
An explanation has been given and dated, and either a structural fix has an owner and a next
step, or the absence of one is recorded as an open, owned gap rather than left implicit.

## Owner
Sofia Marques, as the metric owner and the person `POLICY-05` and `POLICY-13` sit with.

## Allowed transitions
To Escalated, if a structural fix (e.g. a consumer list, or updating `marts/self_service.sql` to
match its definitions file) is actually undertaken and owned. To
Delivered-then-disputed-unresolved (terminal), if the explanation is given and nothing further is
undertaken, which is `REQUEST-004`'s real, current end state.

## Failure path
If Recover itself produces no owner for the residual gap, the item does not advance; it sits in
Recover indefinitely. This is not a hypothetical failure mode. It is where `ISSUE-30` (opened
2026-08-06, the same day as `INCIDENT-03`) stands as of the most recent tracker snapshot in this
fixture: open, owned by Sofia Marques on paper, not picked up in practice.

## Human judgment boundary
Whether a definition change is safe to apply mid-period, and whether a figure's cause has been
genuinely found versus merely asserted, are Sofia Marques's calls. Recover states the evidence
for and against each candidate cause; it does not adjudicate between them where the evidence
itself conflicts.
