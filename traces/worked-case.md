# Worked lifecycle case

## Case

| Field | Value |
| :- | :- |
| Work item | `REQUEST-004` (self-service per account, July packs), reconstructed historically. `REQUEST-007` (self-service, August packs), run live against the newly designed Handoff skill. |
| Intended outcome | Reconstruct what actually happened to `REQUEST-004`, and show whether the proposed lifecycle's one designed refusal (Handoff, `POLICY-13`) would catch the same failure mode recurring in `REQUEST-007`. |
| Starting state | Intake (Request received), `REQUEST-004`, 2026-07-28 |
| Final state | Recover, terminal at Delivered-then-disputed-unresolved (`REQUEST-004`); Correction required (`REQUEST-007`, live run, left open, not concluded) |

## Trace

Rows 1-9 reconstruct `REQUEST-004` as it actually happened, using only what the fixture records.
Row 7 is split in two: 7a is the historical event (no version attached, sent anyway, because no
refusal existed at the time); 7b is **not a historical event** — it states what the newly
designed Handoff skill would do if applied to that same artifact today. Rows 10-14 are a live
run of the full lifecycle against `REQUEST-007`'s real, current, unresolved data.

**This pass was actually run, not narrated from memory of an earlier pass.** Each skill's
`Required context` files were re-read directly for this run: `project/models/marts/self_service.sql`,
`project/models/staging/stg_suggestions.sql`, `project/tests/self_service_rate_in_range.sql`,
`project/tests/not_null_self-service_account.sql`, `data/ops-extract/ticket.csv`,
`data/ops-extract/account.csv`, and `data/ops-extract/extract-manifest.yaml`, alongside the
narrative documents used in the first pass. Two things this surfaced that the first, narrative-only
pass had not caught are called out inline below (row 4 and row 8), and a third (row 11) sharpens
a claim the first pass stated too generously.

| # | Skill invoked | Context read | Evidence produced | Proposed state | Result | Human intervention |
| -: | :- | :- | :- | :- | :- | :- |
| 1 | Intake (`REQUEST-004`) | `data/requests/inbox.csv` | Bounded restatement: `self_service_rate`, per account, July period, needed 2026-08-05 | Context | Advanced | None recorded |
| 2 | Context (`REQUEST-004`) | `data/ops-extract/extract-manifest.yaml` (no manifest survives for the July extract; only the 2026-08-28 one is on file); `project/metrics/metric-definitions.yaml` | Extract period coverage recorded as **unknown**. Requester never stated a version; applying Context's own rule ("name the version whose `effective_from` falls on or before the period's end") to July names **version 3** (`effective_from: 2026-07-01`) as context-assumed current | Route | Advanced, version ambiguity carried forward, not resolved | None recorded historically |
| 3 | Route (`REQUEST-004`) | Prior-cycle build state; `inbox.csv` for other open requests | Path = monthly build, already run for July. No duplicate flagged (`REQUEST-011` did not exist yet; filed 2026-08-18) | Act | Advanced | None |
| 4 | Act (`REQUEST-004`) | `project/models/marts/self_service.sql`, `project/models/staging/stg_suggestions.sql` | Number computed. **Confirmed by reading the SQL, not just PR-note 0088's summary:** `self_service.sql` sums `is_self_served`, and `stg_suggestions.sql` derives `is_self_served` as `CAST(sent AS BOOLEAN)` — exactly version 2's rule, no exclusion for a materially edited answer or a ticket reopened within 48 hours. Applying Context's own rule to July (`self_service_rate` v3's `effective_from: 2026-07-01` precedes all of July) would name **v3** as current for the period. **The contradiction is therefore visible right here, mechanically, not only in hindsight at Recover:** Context's context-assumed version (v3) and Act's actually-implemented version (v2) disagree, and per the Act skill's own procedure this should have been recorded as a contradiction and escalated, not silently carried forward | Verify | Advanced (historically; nothing recorded the mismatch at the time) | None |
| 5 | Verify (`REQUEST-004`) | `project/tests/self_service_rate_in_range.sql`, `not_null_self-service_account.sql` | Shape tests pass; explicit note that passing does not confirm correctness | Approve | Advanced | None |
| 6 | Approve (`REQUEST-004`) | The mart SQL | Whether a second person read the SQL for this run: **unknown**; no record survives | Handoff | Advanced, over an unrecorded gap | None recorded (matches the interview: "this happens when the author remembers to ask") |
| 7a | Handoff, **as it actually happened** | The computed number | A CSV sent to Lucia Ferreira; no metric name or version attached | Observe | Sent anyway | None |
| 7b | Handoff, **proposed skill, applied retrospectively — not a historical event** | The same CSV; Act's version record | Refusal: no version attached | Correction required | Would have refused, had this skill existed in 2026-07 | None (analytical, not a replay claim) |
| 8 | Observe (`REQUEST-004`) | `TICKET-004424` (read directly in `data/ops-extract/ticket.csv`, not only via `INCIDENT-03`'s citation of it) | Dispute logged: Sunder Retail Supply's figure moved. **New finding:** the ticket's actual record is `TICKET-004424, ACCOUNT-1008, reporting, opened_at 2026-08-20T07:19:00Z, "Self-service number changed", "...dropped by six points this month with no change on our side."` This opened_at is **two weeks after** `INCIDENT-03`'s stated write date of 2026-08-06 — the incident cannot have been written about this specific ticket on the date it claims. Recorded as a contradiction, not resolved (see below) | Recover | Advanced (disputed) | Sunder Retail Supply raised the ticket |
| 9 | Recover (`REQUEST-004`) | `INCIDENT-03.md`; `docs/pr-notes/0088-self-service-v3.md`; `metric-definitions.yaml` | Two conflicting candidate causes named and left open (definition changed vs. mart never updated); explanation given ~2026-08-08; `ISSUE-30` opened 2026-08-06, no structural fix undertaken | Delivered-then-disputed-unresolved | **Terminal, unresolved.** `ISSUE-30` remains open and unowned in the latest tracker snapshot in this fixture | Sofia Marques gave the explanation |
| 10 | Intake (`REQUEST-007`, live) | `data/requests/inbox.csv` (`REQUEST-007`, received 2026-08-11, needed 2026-09-04); `docs/backlog.md` (`ISSUE-36`) | Bounded restatement: self-service, per account, August packs. Duplicate flagged against `REQUEST-011`, unjudged | Context | Advanced, duplicate flag carried forward exactly as `ISSUE-36` records it today | None |
| 11 | Context (`REQUEST-007`, live) | `extract-manifest.yaml` (2026-08-28, the most recent on file); `metric-definitions.yaml` | **Corrected from the first pass, which called this "confirmed usable":** the manifest states it covers "tickets opened before 2026-08-28." `REQUEST-007` asks for the full August period, which runs through 2026-08-31. The extract is short by three to four days of the period it would answer for — partial coverage, not full confirmation. No version stated by the requester either (tracker note: "Which definition version was not stated") | Route | Advanced, with **two** open flags now, not one: partial extract coverage, and the unstated version, the same precondition behind `INCIDENT-03` | None |
| 12 | Route (`REQUEST-007`, live) | `data/requests/inbox.csv` (`REQUEST-011`) | Duplicate-request flag escalated to Sofia Marques by name, per `ISSUE-36`, rather than left to age | Act | Advanced; duplicate question escalated, not resolved; does not block this request's own path | Flag addressed to Sofia Marques; not yet acted on as of this trace |
| 13 | Act through Approve (`REQUEST-007`, live, condensed) | `marts/self_service.sql` | Number computed under the mart's implemented version (v2, same reading as row 4); no version confirmed against the requester | Handoff | Advanced | None |
| 14 | Handoff (`REQUEST-007`, live) | The computed number; Context's record that no version was stated or confirmed | **Refusal.** No confirmed metric version attached. | Correction required | **Refused.** The one point in this trace where a skill actually stops and does not proceed. | None yet; addressed to Lucia Ferreira (requester) or Sofia Marques (if the definitions themselves are in question); left open, `REQUEST-007` due 2026-09-04, not concluded |

## Failure, refusal, or ambiguity

**What happened:** Three things, two of them found only by actually running these skills against
the real files rather than trusting the narrative documents about them.

First, historically, `REQUEST-004`'s number left the team with no attached metric version, and
its figure changed with no announcement, triggering a customer dispute (`INCIDENT-03`). Second,
applying the newly designed Handoff skill to `REQUEST-007` today produces an actual refusal: the
August self-service number cannot be sent because no metric version was confirmed for it,
reproducing the exact precondition that caused `INCIDENT-03`. Third, two contradictions surfaced
in this run that the first, narrative-only pass had not caught:

- **The version mismatch is detectable at Act, not only in hindsight at Recover.** Reading
  `marts/self_service.sql` and `stg_suggestions.sql` directly confirms the mart implements
  version 2. Mechanically applying Context's own rule to the July period names version 3 as
  current. The two skills' outputs disagree with each other in row 4 of this trace, well before
  Handoff, Observe, or Recover ever run.
- **`TICKET-004424`'s actual record contradicts `INCIDENT-03`'s timeline.** The ticket
  (`data/ops-extract/ticket.csv`) has `opened_at: 2026-08-20T07:19:00Z`. `INCIDENT-03` states it
  was written 2026-08-06, two weeks earlier, citing this ticket as the incident. One of the two
  dates is wrong, or the write-up folded a later, separate complaint into an earlier incident
  record. This was not visible from reading `INCIDENT-03.md` alone; it only surfaced by reading
  the operational ticket record it cites.

**Why the lifecycle did not advance:** Historically (`REQUEST-004`), nothing checked for a
version at Handoff, so nothing stopped it; the process "worked" and still produced harm, and
nothing checked the version mismatch at Act either, though the fixture data to catch it was
already sitting there for anyone who cross-referenced Context's and Act's outputs. For
`REQUEST-007`, the newly designed Handoff skill's stop condition is unconditional per
`POLICY-13`; the number does not carry a fact a consumer would need before trusting it, so it is
refused rather than allowed to repeat history.

**Recovery or escalation:** For `REQUEST-004`/`INCIDENT-03`: an explanation was given, but the
underlying cause is itself contradicted between two records. `INCIDENT-03` attributes the move to
the definition changing v2→v3. `docs/pr-notes/0088-self-service-v3.md` records that the mart's
SQL was deliberately **not** updated to match. Both are named here; neither is picked as more
plausible, per `docs/architecture-rules.md` rule 5. Layered on top, `INCIDENT-03`'s own timeline
now conflicts with the ticket it cites as its evidence, which is reported here and also left open,
for the same reason. `ISSUE-30`, opened the same day as the incident, remains open and unowned in
the latest tracker snapshot in this fixture; the recovery never actually completed. For
`REQUEST-007`: the refusal escalates to Sofia Marques (the version question) and separately
carries the `ISSUE-36` duplicate flag and the partial-extract-coverage flag found at Context; as
of this trace, none of the three has been resolved, and the request remains open ahead of its
2026-09-04 deadline.

## Limitations and next improvement

- This trace does not resolve the `INCIDENT-03` vs. PR-note-0088 contradiction (definition change
  vs. unchanged mart), or the newly found `INCIDENT-03` vs. `TICKET-004424` timeline
  contradiction. Resolving the first would require `marts/self_service.sql`'s revision history,
  which this fixture does not preserve. Resolving the second would require asking whoever wrote
  `INCIDENT-03` directly; nothing in the fixture settles it.
- An earlier pass at this trace narrated what each skill would find, from the narrative documents
  alone, without re-reading the SQL and operational extracts directly. That pass stated Context's
  August extract as "confirmed usable" and did not surface the Act-stage version mismatch or the
  ticket-date contradiction. Both were only caught once the skills were actually run against
  `project/models/`, `project/tests/`, and `data/ops-extract/` directly. That gap is itself worth
  noting: a lifecycle description that only reads the docs about a process, and never the data the
  process actually touches, will miss exactly this kind of thing.
- The Handoff refusal, as designed, blocks on a missing version but does not yet distinguish
  "requester never stated one" from "the definitions themselves are in conflict." That
  distinction currently has to be made by whoever picks up Correction required.
- `REQUEST-007` has not concluded. This trace does not know, and does not guess, how it will be
  resolved.
- The next improvement this module would recommend: build the consumer list `ISSUE-30` has been
  waiting on since 2026-08-06. Without it, `POLICY-05`'s announcement requirement can never
  actually be met, refusal gate or not.
- No control was built for `POLICY-06` (`rebuild.py`'s missing approver) or for the `ISSUE-36`
  dedup judgment beyond flagging it. Per this course's own guidance against building ahead of the
  module, that is recorded as an observation for Module 3, not acted on here.
