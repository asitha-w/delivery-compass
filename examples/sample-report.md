# Sample compass report

What a morning triage run looks like for **Cart2Door**, a fictional online-shopping delivery company with four repositories: `checkout`, `courier-app`, `website`, `warehouse-api`. All names and items are invented.

---

## Compass — 2026-06-08

Scope: cart2door/checkout, cart2door/courier-app, cart2door/website, cart2door/warehouse-api. Read-only run. Ages in calendar days.

**Needle: D4 Staleness** — 11 assigned issues with no activity ≥5 days, oldest 6 weeks. All 11 are stuck (>7d).

| Dim | Waiting | Oldest | Hot/Stuck |
|-----|---------|--------|-----------|
| D1 Review queue | 3 | 9d | 1 |
| D2 Author queue | 1 | 4d | 0 |
| D3 Merge queue | 1 | 12d | 1 |
| D4 Staleness | 11 | 42d | 11 |

Bot queues (kept out of the needle): 4 dependency-update PRs across the repos, oldest 18d, no merge owner.

### Where to look first

1. **The "courier notifications revamp" epic is abandoned-in-place** — 9 issues in `courier-app` assigned to @jonas-c2d, untouched 42 days. Board says in-flight; repo says stopped. Decision needed: still planned, or unassign/park explicitly?
2. **courier-app #214 "live map on delivery screen"** — APPROVED 12 days ago, never merged (D3: decision made, action missing). Owner @jonas-c2d. Merge or close.
3. **checkout #389 "discount codes at checkout"** — waiting 9 days for review, **no reviewer ever assigned** (review-routing gap). Blocks the spring-campaign launch (#377).
4. **warehouse-api #98 "batch picking endpoint"** — changes requested 4 days ago, no new commit (D2). Owner @priya-c2d.
5. **website #156** — waiting 2 days for review from @elena-c2d (warm, not urgent yet).

### Reading

The team is fast where it is looking and blind where it isn't. Active work flows well — most PRs this week were reviewed within a day. The problem is the long tail: one parked epic (courier notifications) accounts for 9 of the 11 stale items and inflates apparent work-in-progress from ~5 real items to ~16, which makes the board unreliable as a system of record. Secondary observation: review requests are rarely used in `checkout`, so the reviewer for any given PR is structurally unassigned — review happens by ambient awareness. That works at this team size, but it is exactly the seam that will tear first as AI-generated PR volume grows.

### Suggested actions (no nudges sent — read-only run)

- A 15-minute triage pass over the 11 stale issues: confirm-still-planned / unassign / park explicitly. Most look like "parked epic", not "stuck work".
- Decide fate of courier-app #214 (merge or close — the approval is going stale).
- Assign a reviewer for checkout #389 today; it blocks a dated launch.

### Nudge log

None drafted, none sent (read-only triage run).
