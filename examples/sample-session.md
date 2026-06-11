# Sample session — a manager's morning with the compass

A complete (fictional) session at **Cart2Door**: triage → drill-down → consent-gated nudge. Maya is the product manager; she is not a developer and never touches `gh` or the repos directly. Annotations in *[brackets]* explain what the skill is doing.

---

**Maya:** what needs my attention today?

*[The skill reads `repos.txt`, runs read-only GitHub queries across the four repos in parallel, computes DecisionPoints, and leads with the needle — not a data dump.]*

**Compass:** Good morning. The needle points at **staleness** today: 11 issues are assigned to people but haven't moved in over 5 days.

| Dimension | Waiting | Oldest | Stuck |
|---|---|---|---|
| D1 Review queue | 3 | 9d | 1 |
| D2 Author queue | 1 | 4d | 0 |
| D3 Merge queue | 1 | 12d | 1 |
| D4 Staleness | **11** | 6w | **11** |

Three things worth your attention, in order:

1. **The courier notifications revamp looks abandoned-in-place** — 9 issues assigned to Jonas, no movement in 6 weeks. This is probably a parked epic rather than stuck work, but the board currently presents it as in-flight.
2. **The "live map" PR was approved 12 days ago and never shipped.** The decision was made; the action is missing.
3. **The discount-codes PR has waited 9 days for review — and no reviewer was ever assigned.** It blocks the spring-campaign launch.

The full report is saved if you want details.

---

**Maya:** why is the discount codes one stuck? that launch has a date on it.

*[Drill-down: the skill walks the artefact trail for that one item — who opened it, what happened since, what exactly is the pending decision — and ends with the single unblocking action.]*

**Compass:** Here's the trail for checkout #389:

- Priya opened it 9 days ago, linked to the spring-campaign issue (#377).
- No reviewer was requested when it was opened, and `checkout` has no automatic reviewer assignment — so it isn't in anyone's queue. It hasn't been looked at by anyone.
- Priya pushed two small fixes since (tests, a lint pass), so it's not waiting on her.

The pending decision is simply: *someone reviews it.* The people with review rights on `checkout` are Sam and Elena. That's the one unblocking action — everything else on the campaign is waiting behind it.

Want me to draft a nudge?

---

**Maya:** yes, show me first.

*[Nudge mode. The skill drafts a neutral, queue-framed comment — no blame — and shows the exact text and exact target before anything is sent. It posts only on an explicit yes, one item at a time. This consent step is a hard rule: it never relaxes, and scheduled runs can never nudge.]*

**Compass:** Draft comment, to be posted on **cart2door/checkout PR #389**:

> @sam-c2d @elena-c2d — this PR has been waiting on review for 9 working days and blocks the spring-campaign launch (#377). Could one of you pick it up, or should the review be reassigned?

Post, edit, or skip?

**Maya:** post it.

**Compass:** Posted on checkout #389. Logged in today's report under the nudge log.

---

**Maya:** what about the Jonas pile — is that a problem?

**Compass:** Probably not a *delivery* problem — it looks like a parked epic, not stuck work: all 9 issues went quiet on the same day (April 27), which usually means priorities shifted rather than work stalled. The cost is honesty, not speed: while these stay assigned and open, your board overstates work-in-progress by 3x, and real stalls can hide among them. The cheap fix is a one-line decision from you: still planned (label it `parked`, unassign), or not (close them). I can draft that as a comment on the epic for Jonas to confirm — your call, no rush.

**Maya:** leave it, I'll raise it in my 1:1 with him.

**Compass:** Noted — I left it untouched. It will keep appearing under staleness until it's parked or closed; I'll keep it summarised as one line ("parked epic, pending your 1:1") instead of repeating all 9 items.

---

*End of session. Total manager time: about four minutes. No ceremony was held, no status was asked of any engineer, and the one message sent to the team was written with the manager's explicit approval, word for word.*
