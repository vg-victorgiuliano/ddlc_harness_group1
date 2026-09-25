---
name: approve
description: Records that a second person read the SQL before the number leaves the team; refuses to silently skip the read.
---

# Approve

## Purpose
Make explicit a check that today happens only as a habit: "a model is not published until
someone else has read the SQL... in practice this happens when the author remembers to ask."
This skill turns the habit into a recorded fact or a recorded gap, not a silent assumption.

## Entry conditions
- Verify has passed the number's shape tests (or, for a fix returning from Recover, the
  corrected number has been re-verified).

## Required context
- The mart's SQL and the version statement Act produced.
- The name of a second person who is not the author.

## Prohibited context
- None.

## Procedure
1. Ask whether a second person (not the number's author) has read the SQL for this specific run.
2. Record who, and what they confirmed: the version claim, the grain, or nothing beyond a
   read-through.
3. If no second reader is available before the deadline, do not silently proceed as if one had
   read it.

## Evidence produced
- A named second reader and what they confirmed, or an explicit record that none was available.

## Proposed transition
To Handoff (Number sent with definition + version).

## Stop or escalation conditions
- No second reader is available: stop, mark Blocked, and escalate to whichever of Declan Byrne
  or Sofia Marques is not the author.

## Human judgment boundary
Whether a figure is fit to leave the team, and for what audience, is Sofia Marques's call. This
skill records that the read happened; it does not substitute for her judgment.
