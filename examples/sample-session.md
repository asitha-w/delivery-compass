# Sample session — a PM's morning and a DM's afternoon with the compass

A complete (fictional) day at **Cart2Door**: triage → drill-down → consent-gated nudge, shown for both personas. Maya is the product manager; Daniel is the delivery manager. Neither is a developer, and neither touches `gh` or the repos directly. Annotations in *[brackets]* explain what the skill is doing.

Terminology (AI-DLC): an **Intent** is a business goal, broken into **Units** of work, built in **Bolts** — PR-sized changes that each pass a human **decision juncture** (review, approval) before shipping.

---

## Part 1 — Maya, the PM: are my Intents healthy?

**Maya:** what needs my attention today?

*[The skill reads `repos.txt`, runs read-only GitHub queries across the four repos in parallel, computes DecisionPoints, and leads with the needle — not a data dump.]*

**Compass:** Good morning. The needle points at **staleness** today: 11 Units are assigned to people but haven't moved in over 5 days.

| Dimension | Waiting | Oldest | Stuck |
|---|---|---|---|
| D1 Review queue (Bolts at juncture) | 3 | 9d | 1 |
| D2 Author queue | 1 | 4d | 0 |
| D3 Merge queue (approved, idle) | 1 | 12d | 1 |
| D4 Staleness (Units stopped) | **11** | 6w | **11** |

Three things worth your attention, in order:

1. **The courier-notifications Intent looks abandoned-in-place** — 9 Units assigned to Jonas, no movement in 6 weeks. Probably a parked Intent rather than stuck work, but the board currently presents it as in-flight.
2. **The "live map" Bolt cleared its review juncture 12 days ago and never shipped.** The decision was made; the action is missing.
3. **The discount-codes Bolt has waited 9 days at its review juncture — with no decision owner ever assigned.** It blocks the spring-campaign Intent.

The full report is saved if you want details.

---

**Maya:** why is the discount codes one stuck? that launch has a date on it.

*[Drill-down: the skill walks the artefact trail for that one item — who opened it, what happened since, what exactly is the pending decision — and ends with the single unblocking action.]*

**Compass:** Here's the trail for checkout #389:

- Priya opened it 9 days ago, linked to the spring-campaign Intent (#377).
- No reviewer was requested when it was opened, and `checkout` has no automatic reviewer assignment — so the Bolt sits at its review juncture with **no decision owner**. It isn't in anyone's queue, and nobody has looked at it.
- Priya pushed two small fixes since (tests, a lint pass), so it's not waiting on her.

The pending decision is simply: *someone reviews it.* The people with review rights on `checkout` are Sam and Elena. That's the one unblocking action — every other Unit in the spring-campaign Intent is queued behind this juncture.

Want me to draft a nudge?

---

**Maya:** yes, show me first.

*[Nudge mode. The skill drafts a neutral, queue-framed comment — no blame — and shows the exact text and exact target before anything is sent. It posts only on an explicit yes, one item at a time. This consent step is a hard rule: it never relaxes, and scheduled runs can never nudge.]*

**Compass:** Draft comment, to be posted on **cart2door/checkout PR #389**:

> @sam-c2d @elena-c2d — this change has been waiting on review for 9 working days and blocks the spring-campaign launch (#377). Could one of you pick it up, or should the review be reassigned?

Post, edit, or skip?

**Maya:** post it.

**Compass:** Posted on checkout #389. Logged in today's report under the nudge log.

---

**Maya:** what about the Jonas pile — is that a problem?

**Compass:** Probably not a *delivery* problem — it looks like a parked Intent, not stuck work: all 9 Units went quiet on the same day (April 27), which usually means priorities shifted rather than work stalled. The cost is honesty, not speed: while these stay assigned and open, your board overstates work-in-progress by 3×, and real stalls can hide among them. The cheap fix is a one-line decision from you: still planned (label the Intent `parked`, unassign the Units), or not (close them). I can draft that as a comment for Jonas to confirm — your call, no rush.

**Maya:** leave it, I'll raise it in my 1:1 with him.

**Compass:** Noted — I left it untouched. It will keep appearing under staleness until the Intent is parked or closed; I'll keep it summarised as one line ("parked Intent, pending your 1:1") instead of repeating all 9 Units.

---

## Part 2 — Daniel, the DM: is the flow healthy?

*[Same skill, same data, different altitude. Daniel doesn't ask about any single item — he asks about the system. The compass answers with flow patterns: juncture latency, queue concentration, WIP truthfulness.]*

**Daniel:** how is our flow this week?

**Compass:** Healthy where work is active, with three structural warnings:

1. **Decision latency on active Bolts is excellent.** The median Bolt cleared its review juncture in 5 hours this week; 8 of 11 shipped same-day. When a decision owner exists, the system is fast.
2. **Review capacity is concentrated.** Sam made 9 of this week's 11 review decisions. One busy week for Sam and every `checkout` queue backs up — that's your single point of queue failure, and it will tighten as AI raises Bolt volume. Elena has review rights but made 1 decision; the routing, not the capacity, is the gap.
3. **WIP is overstated 3×.** 16 Units are open-and-assigned, but only 5 moved this week. Most of the gap is the parked notifications Intent (Maya is handling it). The remaining two — the "live map" Bolt approved-but-unshipped for 12 days, and the batch-picking Bolt waiting 4 days on its author — are real flow leaks, not parking.

**Daniel:** what would you change first?

**Compass:** The review routing. It costs nothing and removes the single point of failure: turn on automatic reviewer assignment for `checkout` (round-robin between Sam and Elena), so every Bolt arrives at its juncture with a decision owner. The other two leaks are one decision each — ship-or-close #214, and a gentle check-in on the batch-picking rework. I can draft both nudges for your review, one at a time.

**Daniel:** draft the #214 one.

**Compass:** Draft comment, to be posted on **cart2door/courier-app PR #214**:

> @jonas-c2d — this change was approved 12 days ago but hasn't shipped. Is anything blocking the merge, or should it be closed if it's been superseded?

Post, edit, or skip?

**Daniel:** post.

**Compass:** Posted on courier-app #214. Both of today's nudges are in the report's nudge log.

---

*End of session. Total manager time: under ten minutes across both roles. No ceremony was held, no status was asked of any engineer, and the two messages sent to the team were each approved word-for-word before posting.*
