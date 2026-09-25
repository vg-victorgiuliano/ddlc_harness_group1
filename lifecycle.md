# Lifecycle worksheet

## Selected work item

| Field | Value |
| :- | :- |
| Track | DDLC |
| Process | The month's figures are produced |
| Work item | `REQUEST-004` (self-service per account, July packs), chained with `INCIDENT-03`. `REQUEST-007` (self-service, August packs, still open) is carried as live corroborating evidence, not as the traced item. |
| Intended outcome | A per-account self-service percentage for the July service review packs, matching what `REQUEST-004` asked Reporting for. |
| Current owner | Declan Byrne built the number. Sofia Marques owns the metric definition and the policies it touches (inferred to be "the analytics lead" `docs/how-we-work-today.md` and `INCIDENT-03` quote without naming; see `CONTEXT.md`). |

## Current process

Reconstructed from `docs/interviews/2026-08-12 Declan Byrne, where the numbers come from.docx`,
`docs/incidents/INCIDENT-03.md`, `docs/pr-notes/0088-self-service-v3.md`, `data/requests/inbox.csv`,
and `data/tracker.csv`. Unknowns are recorded as unknown, not estimated.

| Step | Actor | System or artifact | Input | Output | Decision | Evidence | Problem or unknown |
| -: | :- | :- | :- | :- | :- | :- | :- |
| 1 | Lucia Ferreira (Reporting) | `data/requests/inbox.csv` | A verbal/written ask, "the same self-service number we had in July" | `REQUEST-004` logged, received 2026-07-28, needed 2026-08-05 | Logged as received | inbox.csv row | Whether it was logged promptly, or only once someone remembered, is not recorded. |
| 2 | Whoever received it (unnamed) | none; a judgment call | The request summary | An unwritten understanding: metric = self-service, grain = per account | Which metric, which grain | None. "This is the hard part and it leaves no record" (interview). | Which definition **version** was intended is not stated anywhere and was never asked. |
| 3 | Support, "whoever is around... usually Joao" | Operational database → CSV extract | A drifted query, pasted by hand | CSV files in a shared folder | None; mechanical export | In principle, an extract manifest | No manifest for this specific extract survives in this repository. The one manifest on file (`extract-manifest.yaml`) is dated 2026-08-28, a later cycle. Whether the July extract's period was ever checked against the requested period is unknown; Declan's own account is that he "checks the manifest by eye now," a habit, not a record. |
| 4 | Declan Byrne | `project/run.py` → staging → `marts/self_service.sql` | The extract from step 3 | `marts.self_service` output | None; "one command... that part is fine" | Build completes; shape tests pass | **Verified by reading the SQL directly, not just PR-note 0088's summary of it:** `marts/self_service.sql` computes `self_served_tickets` from `is_self_served`, which `stg_suggestions.sql` derives as `CAST(sent AS BOOLEAN)`, i.e. exactly version 2's rule (every sent suggestion counts, no exclusion for a materially edited answer or a ticket reopened within 48 hours). Meanwhile, mechanically applying Context's own rule ("name the version whose `effective_from` falls on or before the period's end") to the July period yields version 3 (`effective_from: 2026-07-01`), since all of July postdates it. So the contradiction in step 6 is not something only Recover discovers after the fact: reading Context's and Act's outputs side by side already disagrees, at Act, before Verify or Handoff ever run. |
| 5 | Declan Byrne | mart output → CSV | Mart output | A CSV sent to Lucia Ferreira | None recorded | The CSV itself (not preserved in this fixture) | No metric name or version travelled with the number. `POLICY-13` requires this; nothing enforces it. |
| 6 | PR-notes / metric definition | `project/metrics/metric-definitions.yaml`, via PR 0088 | The observation that v2 over-counted edited answers as self-service | `self_service_rate` v3 merged 2026-06-27, effective 2026-07-01; v2 marked superseded | The new definition is better; ship it | `metric-definitions.yaml`, `docs/pr-notes/0088-self-service-v3.md` | **Contradiction, left open, not resolved:** PR-note 0088 states the mart was deliberately *not* updated to v3 ("still sums `is_self_served`, which is the version 2 rule"). `INCIDENT-03` states the figure moved *because* the definition changed v2→v3. If the mart's SQL never changed, the computed number should not have moved from the definition change alone. Both are genuine written records; per `docs/architecture-rules.md` rule 5, both are reported and neither is picked as more plausible. |
| 7 | Lucia Ferreira / Reporting | July service review pack | The CSV from step 5 | Pack published to accounts including Sunder Retail Supply | Out of Analytics's hands once handed off | The pack itself (not in this repository) | Unknown whether Reporting knew a definition change had occurred. |
| 8 | Sunder Retail Supply | `TICKET-004424` | Comparing June's pack figure to July's | A question the team could not answer for two days | None yet; a dispute raised | `TICKET-004424`, `INCIDENT-03.md` | The account's behaviour did not change (per `INCIDENT-03`); whether the reported figure's movement is fully explained by the stated cause is exactly the step-6 contradiction. **Second contradiction, found by reading `data/ops-extract/ticket.csv` directly rather than trusting the incident write-up's citation:** `TICKET-004424` (`ACCOUNT-1008`, "Self-service number changed... dropped by six points this month") has `opened_at: 2026-08-20T07:19:00Z`, two weeks *after* `INCIDENT-03`'s own stated write date of 2026-08-06. The incident cannot have been written about this ticket on the date it claims. Either the write date is wrong, the ticket citation is wrong, or the ticket is a distinct, later complaint the write-up folded in. `INCIDENT-03` itself names an unlisted consumer that would fit a later, undated complaint better than the June/July pack comparison it narrates: "the help portal, which serves the metric on request and pins no version." Left open, not resolved. |
| 9 | Sofia Marques (inferred "analytics lead") | none beyond the incident write-up | The dispute | An explanation given, two days later; nothing structurally changed | No structural fix made | `INCIDENT-03.md` | `ISSUE-30` ("no list of who consumes which metric") opened the same day, 2026-08-06, and remains open and unowned in the most recent tracker/backlog snapshot in this fixture. Nobody owns the follow-up. |

## Proposed lifecycle

Stages renamed for "the month's figures are produced." The activity's common shape
(Intake → Context → Route → Act → Verify → Approve → Handoff → Observe, with Recover
reachable from Act onward and feeding back to Handoff) is preserved throughout.

| Common stage | Track-specific state | Entry conditions | Evidence produced | Next states | Owner | Stop or escalation condition |
| :- | :- | :- | :- | :- | :- | :- |
| Intake | Request received | A request has arrived, by message, meeting, or already in `inbox.csv` | Logged `REQUEST-NNN` row; bounded restatement (metric, grain, period, deadline); any similar open request flagged, unjudged | Context, or Rejected | Declan Byrne | No answerable metric/grain after asking back once → Rejected |
| Context | Source and definition assembled | Request bounded at Intake | Extract period coverage confirmed or recorded unknown; metric version named (requester-confirmed or context-assumed) | Route, or Blocked | Declan Byrne | Extract period cannot be confirmed → Blocked. Metric version conflicts mid-open-period → escalate to Sofia Marques |
| Route | Ad hoc vs. monthly-build path decided | Context assembled | Chosen build path and why; any duplicate-request flag carried forward, not resolved | Act | Declan Byrne (path); Sofia Marques (dedup judgment) | Deadline does not allow a fresh build and none exists for the period → escalate to Declan Byrne. Duplicate flag unjudged past one reporting period → escalate to Sofia Marques by name |
| Act | Model built / number computed | Path decided | The number; the metric version the **SQL actually implements**, stated even when it disagrees with the definitions file's current version | Verify | Declan Byrne | Implemented version cannot be determined from the SQL → escalate to Declan Byrne |
| Verify | Shape-tested | Number computed | Pass/fail per shape test; explicit statement of what shape tests do **not** check (correctness) | Approve | Declan Byrne | Any shape test fails → Recover |
| Approve | SQL read by a second person | Verified | Named second reader and what they confirmed, or an explicit record that none was available | Handoff | Whichever of Declan Byrne / Sofia Marques did not author the change | No second reader available before deadline → Blocked, escalate; never silently skipped |
| Handoff | Number sent with definition + version | Approved | Outgoing artifact names metric + version; handoff logged (recipient, date, version) | Observe, or Correction required | Declan Byrne (sends); Sofia Marques (definition owner, escalation point) | **No version can be attached → refuse, unconditionally.** This is the one designed refusal in this lifecycle. |
| Observe | Consumer discrepancy surfaces | Handed off | Logged dispute (naming the ticket/message) or a statement that no dispute arrived in the observation window | Delivered-and-confirmed (terminal), or Recover | Whoever fields the consumer question; Sofia Marques notified | A dispute names a period-over-period change → escalate directly to Recover with both periods' figures and versions attached |
| Recover | Explained, escalated, or left open | Dispute or unresolved failure observed | Stated cause; where two records disagree on the cause, both are named and the disagreement is left open; explanation dated; structural-fix status stated explicitly | Escalated (fix owned and underway), or Delivered-then-disputed-unresolved (terminal) | Sofia Marques | No owner emerges for the residual gap → item sits in Recover indefinitely. This is where `REQUEST-004` actually stands today, via `ISSUE-30`. |

## Human judgment boundaries

Carried over, near-verbatim, from the five judgment calls `docs/how-we-work-today.md` names as
currently undocumented, each now assigned an owner and the evidence a skill prepares for it
without deciding it.

| Decision | Why it is not mechanical | Decision owner | Evidence prepared by a skill |
| :- | :- | :- | :- |
| Whether two requests are asking for the same number (`REQUEST-007`/`REQUEST-011`) | Requires reading both requests' intent, not just their wording | Sofia Marques | Route lays the two request summaries side by side (`ISSUE-36`) |
| Whether a request is specified well enough to answer, or needs to go back | No mechanical test for "well enough specified" | Declan Byrne (per tracker ownership) | Intake checks for metric, grain, period, audience present |
| Whether a definition change is safe to apply while a reporting period is open | Requires weighing comparability against correctness | Sofia Marques (owns `POLICY-05`) | Route/Context flags an open-period conflict |
| Whether a figure is fit to leave the team, and for what audience | Requires judging audience and context, not just data quality | Sofia Marques | Approve records the second-read confirmation |
| When a model is wrong versus when the data is wrong | Shape tests cannot distinguish the two | Declan Byrne | Verify surfaces shape-test output for triage |

## Failure and recovery

| Failure or blocked state | Detection signal | Recovery path | Retry limit | Escalation owner |
| :- | :- | :- | :- | :- |
| Blocked | Context cannot confirm the extract covers the requested period | Wait for, or take, a validated extract | None recorded in this fixture today | Declan Byrne |
| Correction required | Handoff refuses — no metric version declared or confirmed | Returned to Context (re-name version) or Act (recompute under confirmed version); never directly back to Handoff | None recorded | Sofia Marques |
| Escalated | Observe detects a consumer discrepancy (e.g. `TICKET-004424`) | Escalated to the metric owner for investigation | n/a | Sofia Marques |
| Rejected | Request stays underspecified after being asked back once (cf. `REQUEST-005`'s three unresolved ambiguities) | Returned to requester; item does not proceed | One round of clarification | Whoever intook it |
| Retired | Metric definition version superseded (`self_service_rate` v2 → v3) | Old version stays readable but flagged `status: superseded`; new figures use the current version | n/a | Sofia Marques |

## Terminal states

| State | Business outcome represented | Required final evidence |
| :- | :- | :- |
| Delivered-and-confirmed | Number is in the pack, unquestioned through the reporting period | Handoff artifact; no linked dispute |
| Delivered-then-disputed-unresolved | Delivered once, disputed afterward, explained, not structurally fixed. **This is `REQUEST-004`'s real end state**, via `INCIDENT-03` and the still-open `ISSUE-30`. | Incident write-up; the still-open, still-unowned backlog item |
| Rejected-at-intake | Never specified well enough to proceed (cf. `REQUEST-005`, answered anyway with its caveats dropped, illustrating the cost of not stopping here) | No evidence produced; request stays open indefinitely |

## Open questions

- The `INCIDENT-03` vs. PR-note-0088 contradiction over what actually moved the self-service
  figure is not resolved by this lifecycle. Resolving it would require reading
  `marts/self_service.sql`'s history, which this fixture does not preserve.
- Whether "the analytics lead," quoted twice in `docs/how-we-work-today.md` and credited with
  writing `INCIDENT-03`, is in fact Sofia Marques is an inference (see `CONTEXT.md`), not a
  stated fact.
- `ISSUE-36` (`REQUEST-007` vs. `REQUEST-011`) remains an unjudged duplicate as of this writing;
  this lifecycle surfaces it at Route and Handoff but does not resolve it.
- Observe has no stated observation window. How long the lifecycle waits before closing to
  Delivered-and-confirmed is undecided.
- `POLICY-06` (a named approver for destructive transformations, e.g. `project/rebuild.py`) is a
  real gap (`ISSUE-37`) this lifecycle does not build a control for. Per this course's own
  guidance against building ahead, that is recorded as an observation for Module 3, not acted on
  here.
- **Found only by actually running Context against `REQUEST-007`'s live data, not by narrating
  it:** the one extract manifest in this repository states it covers "tickets opened before
  2026-08-28." `REQUEST-007` asks for the full August period, which runs through 2026-08-31.
  The manifest does not cover the last three to four days of the month it would be used to
  answer. Context's exit condition ("extract period coverage confirmed") does not currently
  distinguish "confirmed to fully cover the period" from "confirmed to partially cover it,"
  and should. As written, this extract would today be waved through as "coverage confirmed"
  when it is actually incomplete.
- `TICKET-004424`'s `opened_at` postdating `INCIDENT-03`'s stated write date (see step 8 in the
  current-process table) is a fixture-level contradiction this lifecycle surfaces but does not
  resolve.
