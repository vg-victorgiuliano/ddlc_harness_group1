---
name: observe
description: Watches for a consumer disputing a handed-off figure and logs whether one did.
---

# Observe

## Purpose
Look at what happened after handoff: did any consumer question the figure, and if so, on what
basis. Today this happens only when a customer asks (`TICKET-004424`, two days after the July
pack went out); there is no proactive check.

## Entry conditions
- Handoff has sent a versioned figure and logged it.

## Required context
- Any support ticket, message, or meeting referencing the handed-off figure.
- The prior period's figure for the same metric and account, where one exists, to compare
  against.

## Prohibited context
- None.

## Procedure
1. For a defined observation window (this lifecycle does not currently state one; see
   `lifecycle.md`'s open questions), watch for any consumer question against the figure.
2. If a question arrives, log it and capture exactly what is being disputed: the number itself,
   or a change from a prior period's number for the same account.
3. If no question arrives within the window, close to Delivered-and-confirmed.

## Evidence produced
- Either a logged dispute naming the ticket or message that raised it, or a statement that the
  window closed with no dispute.

## Proposed transition
To Delivered-and-confirmed (terminal), or to Recover, on a dispute.

## Stop or escalation conditions
- A dispute names a period-over-period change in the number: escalate directly to Recover with
  both periods' figures and versions attached, so Recover is not starting from nothing the way
  `INCIDENT-03`'s investigation had to.

## Human judgment boundary
None new. Observe reports; it does not judge whether a raised dispute is well-founded.
