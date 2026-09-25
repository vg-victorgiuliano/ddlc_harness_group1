---
name: intake
description: Accepts a data request from another team and bounds it to a named metric, grain, and period before it enters the lifecycle.
---

# Intake

## Purpose
Accept a request for a figure (from Reporting, Product, or Portal engineering) and turn it into
a bounded work item: a named metric, a grain, a period, and a deadline. Nothing is computed here.

## Entry conditions
- A request has arrived, in a message, a meeting, or already logged in `data/requests/inbox.csv`.
- The request has not already been logged as a `REQUEST-NNN` item.

## Required context
- The request as stated, in the requester's own words.
- `data/requests/inbox.csv`, to check whether an equivalent request is already open (`ISSUE-36`:
  `REQUEST-007` and `REQUEST-011` were never checked against each other).
- `docs/identifiers.md`, to assign the next `REQUEST-NNN`.

## Prohibited context
- The operational database or any extract. Intake bounds the ask; it does not answer it.
- Any other team's tracker or fixture data outside this repository.

## Procedure
1. Restate the request as: metric or question, grain (account, account-day, account-period),
   period, and deadline.
2. Check whether an open, unclosed request in `data/requests/inbox.csv` asks a similar question.
   If one does, do not judge whether they are the same; flag it for Route.
3. Assign a `REQUEST-NNN` identifier per `docs/identifiers.md` and log the row.
4. If metric, grain, or period cannot be stated after restating the request once, stop.

## Evidence produced
- A logged `data/requests/inbox.csv` row with a `REQUEST-NNN` identifier.
- A one-paragraph restatement of what is being asked, in terms Context can act on.
- A flag naming any other open request that looks similar, unjudged.

## Proposed transition
To Context (Source and definition assembled), once the request is bounded.

## Stop or escalation conditions
- The request has no answerable metric or grain even after asking back once: stop, do not log
  it as bounded. This is the fate `REQUEST-005` never met; its three ambiguities were never
  resolved and it was answered anyway.
- A similar open request already exists: proceed, but do not silently merge or silently treat
  them as different. Carry the flag to Route.

## Human judgment boundary
Whether a request is specified well enough to answer, or needs to go back to the requester, is a
human call (Declan Byrne, per the pattern in the tracker). This skill prepares the restatement;
it does not decide.
