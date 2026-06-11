# Sample compass report

What a morning triage run looks like for **Cart2Door**, a fictional online-shopping delivery company with four repositories: `checkout`, `courier-app`, `website`, `warehouse-api`. All names and items are invented.

Terminology (per the [AI-DLC method definition](https://prod.d13rzhkk8cj2z0.amplifyapp.com/)): an **Intent** is a business goal, broken into **Units** of work (tracked as issues), built in **Bolts** (PR-sized changes, hours/days each). Every Bolt passes a human **decision juncture** — a review, an approval — before it ships. The compass measures how long things wait at those junctures.

---

## Compass — 2026-06-08

Scope: cart2door/checkout, cart2door/courier-app, cart2door/website, cart2door/warehouse-api. Read-only run. Ages in calendar days.

**Needle: D4 Staleness** — 11 Units assigned with no activity ≥5 days, oldest 6 weeks. All 11 are stuck (>7d).

| Dim | Waiting | Oldest | Hot/Stuck |
|-----|---------|--------|-----------|
| D1 Review queue — Bolts waiting at a review juncture | 3 | 9d | 1 |
| D2 Author queue — juncture cleared with "changes requested", author hasn't acted | 1 | 4d | 0 |
| D3 Merge queue — juncture cleared with approval, Bolt never shipped | 1 | 12d | 1 |
| D4 Staleness — Units that stopped moving | 11 | 42d | 11 |

Bot queues (kept out of the needle): 4 dependency-update PRs across the repos, oldest 18d, no decision owner.

### Where to look first

1. **The "courier notifications revamp" Intent is abandoned-in-place** — 9 Units in `courier-app` assigned to @jonas-c2d, untouched 42 days. Board says in-flight; repo says stopped. Decision needed: still planned, or unassign/park the Intent explicitly?
2. **courier-app Bolt #214 "live map on delivery screen"** — cleared its review juncture 12 days ago (APPROVED), never merged (D3: decision made, action missing). Owner @jonas-c2d. Ship or close.
3. **checkout Bolt #389 "discount codes at checkout"** — waiting at its review juncture 9 days, **no decision owner ever assigned** (review-routing gap). Blocks the spring-campaign Intent (#377).
4. **warehouse-api Bolt #98 "batch picking endpoint"** — juncture verdict was "changes requested" 4 days ago, no new commit since (D2). Owner @priya-c2d.
5. **website Bolt #156** — at its review juncture 2 days, decision owner @elena-c2d (warm, not urgent yet).

### Reading

The team is fast where it is looking and blind where it isn't. Active Bolts clear their decision junctures well — most reviews this week landed within a day. The problem is the long tail: one parked Intent (courier notifications) accounts for 9 of the 11 stale Units and inflates apparent work-in-progress from ~5 real items to ~16, which makes the board unreliable as a system of record. Secondary observation: review requests are rarely used in `checkout`, so Bolts arrive at their review juncture with **no decision owner** — review happens by ambient awareness. That works at this team size, but it is exactly the seam that will tear first as AI raises Bolt volume.

### Suggested actions (no nudges sent — read-only run)

- A 15-minute triage pass over the 11 stale Units: confirm-still-planned / unassign / park the Intent explicitly. Most look like "parked Intent", not "stuck work".
- Decide fate of courier-app Bolt #214 (ship or close — the approval is going stale).
- Assign a decision owner for checkout Bolt #389 today; it blocks an Intent with a date on it.

### Nudge log

None drafted, none sent (read-only triage run).
