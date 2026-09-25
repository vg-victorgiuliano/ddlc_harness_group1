---
name: handoff
description: Sends the number to its consumer. Refuses to send a figure that does not name its metric definition and version.
---

# Handoff

## Purpose
Transfer the approved number to whoever asked for it, carrying its metric name and definition
version (`POLICY-13`). This is the one point in this lifecycle that refuses outright rather than
merely flagging: a figure with no stated version does not leave the team.

## Entry condition
Approve has recorded a second reader, or an explicit record that none was available and the
item was escalated instead of silently proceeding.

## Required context
- The number, from Act/Verify.
- The metric name and version Context named and Act confirmed the SQL actually implements.
- The requester and the artifact form they need (CSV, message, pack row).

## Required evidence
The outgoing artifact states, in the same place the number appears, the metric name and its
version, e.g. `self_service_rate v2` rather than a bare percentage. Historically, this was never
produced: the CSV Declan sent for `REQUEST-004` carried a bare number, which is exactly what let
`INCIDENT-03` happen unannounced.

## Prohibited context
- None beyond the standing repository boundary.

## Procedure
1. Confirm the number carries a stated metric name and version internally (from Act).
2. If it does not, or if Act flagged a contradiction between the version the SQL implements and
   the version currently named as current, **refuse to send.** Return the item to Correction
   required, addressed to whoever can supply the missing version: the requester, if none was
   ever stated; Sofia Marques, if the contradiction is in the definitions themselves.
3. If the version is present and unambiguous, send the number with its version attached, in
   whatever form the requester needs.
4. Log the handoff: what was sent, to whom, when, and under which version.

## Exit condition
The outgoing artifact names the metric and version it was computed from, and the handoff is
logged.

## Evidence produced
- The sent artifact, with its version attached.
- The handoff log entry (recipient, date, version).
- Where refused: a Correction required record naming exactly what is missing and who must supply
  it.

## Owner
Declan Byrne sends. Sofia Marques owns the definition a figure must cite and is the escalation
point when Handoff refuses because the definitions themselves are in conflict.

## Proposed transition / Allowed transitions
To Observe, on a successful send. To Correction required, on refusal. Correction required
returns to Context (to re-name the version) or to Act (to recompute under the confirmed
version); never directly back to Handoff.

## Stop or escalation conditions
- No version can be attached: refuse, as above. This is not a stop condition to be judged case
  by case; it is unconditional. `POLICY-13` states it should always be true, and nothing today
  enforces it.

## Failure path
A refused handoff becomes Correction required. If Correction required cannot be resolved before
the deadline, as it may well not be, since resolving `REQUEST-007`'s missing version and its
possible duplication with `REQUEST-011` are both judgment calls owned by Sofia Marques, the item
is escalated to her explicitly rather than left to age silently, which is what happened to
`ISSUE-36` and `ISSUE-30`.

## Human judgment boundary
Whether a figure is fit to leave the team, and for what audience, was already recorded at
Approve. Handoff adds a second, narrower boundary: whether the version attached is the right
one, which is Sofia Marques's call whenever Act's implemented-version and the definitions file's
current-version disagree.
