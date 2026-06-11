# Delivery Compass

Direct, rapid visibility for project managers and delivery managers in AI-driven development (AIDLC). Reads the system of record (repos + boards) and tells the manager **which dimension needs their attention right now**, then walks them down that lens to the specific artefact and the specific person it's waiting on.

**Working dir:** `~/delivery-compass/` (create it on first run if missing)
**Config:** `~/delivery-compass/repos.txt` (one `org/repo` per line) — the scan scope. If missing, ask the user which repositories to watch and create it.
**Reports:** saved to `~/delivery-compass/reports/YYYY-MM-DD-compass.md` after every run.

---

## Why this skill exists (read this once — it is the operating model)

Software delivery is moving from Scrum/Kanban to **AI-DLC** (AI-Driven Development Lifecycle, per the AWS method definition) and FDE-style embedded delivery. In that world:

- Sprints become **Bolts** — iterations measured in hours/days, not weeks.
- Work decomposes **Intent → Units → Bolts** across three phases (Inception, Construction, Operations), with AI doing decomposition and generation, humans validating at **decision junctures** — where, per the whitepaper, human oversight acts "like a loss function", catching errors before they snowball.
- Time-boxed ceremonies (standup, sprint planning, burndown, velocity) collapse into on-demand working sessions (Mob Elaboration, Mob Construction): when a "story" takes 3 hours, story points and team velocity are noise.

**What survives:** flow metrics. **What's new:** in AI-DLC, truth about progress no longer lives in people's heads (extracted via ceremonies) — it lives in the artefacts themselves: intents, plans, issues, PRs, commits. They are machine-readable and timestamped. A manager doesn't need a standup; they need a reader at the right altitude. This skill is that reader.

**The new #1 bottleneck is decision latency.** When AI generates work in hours, the constraint shifts to the decision junctures — the humans who must approve, review, and validate. Work doesn't stall in development anymore — it stalls *waiting for a decision*. Measuring how long artefacts sit at decision junctures replaces velocity as the primary delivery health signal.

The manager's value shifts accordingly: from extracting and relaying status → to **clearing decision queues, sharpening intents, and managing WIP**. This skill makes those three things visible.

---

## Operating contract — HARD RULES

1. **Default mode is READ-ONLY.** Every run starts read-only. Only `gh` read commands (`list`, `view`, `api` GETs).
2. **Nudges are consent-gated, always.** The skill may *draft* a nudge (a PR/issue comment pinging the decision owner) but must show the exact text and target to the user and get explicit approval **per nudge** before posting. Never batch-approve. Never auto-nudge. This rule does not relax over time.
3. **Never merge, close, edit, assign, or label anything.** The only write this skill is ever permitted (post-consent) is a comment.
4. **Name people, not blame people.** Reports identify *which decision* is waiting and *whose* it is — framed as queue state, not performance judgement. Decision latency is a system property; the report language must reflect that.

---

## The dimensions (the compass needle)

Every run starts with a triage scan across all dimensions, then points the user at the worst one. Don't dump everything — lead with the needle.

| Dimension | Question it answers | v1 status |
|---|---|---|
| **D1 Review queue** | PRs waiting on a reviewer's decision | ✅ active |
| **D2 Author queue** | PRs where changes were requested and the author hasn't acted | ✅ active |
| **D3 Merge queue** | PRs approved but not merged (decision made, action missing) | ✅ active |
| **D4 Staleness** | Issues marked in-progress / assigned with no repo activity — board says moving, repo says stopped | ✅ active |
| D5 Rework loops | PRs/issues cycling through repeated review rounds — upstream signal of a badly-specified intent | ⏳ later |
| D6 WIP explosion | Too many things 80% done (AI makes starting work free) | ⏳ later |
| D7 Intent drift | Scope edited repeatedly after work started | ⏳ later |

D1–D4 are all faces of **decision latency** — the v1 metric. In AI-DLC terms: D1–D3 are queues at decision junctures on Bolt-scale changes (a PR ≈ a Bolt's build–validate cycle); D4 detects Units (issues) that stopped moving. Until trackers grow native AI-DLC artefacts, the GitHub mapping is: Intent ≈ milestone/epic-cluster, Unit ≈ issue, Bolt ≈ PR, decision juncture ≈ review/approval/merge gate.

---

## Provider-neutral metric model

The unit of measurement is a **DecisionPoint**:

```
DecisionPoint {
  artefact:       <url + title>          — the thing that is waiting
  dimension:      D1..D4
  decision_owner: <person or role>       — who can unblock it
  waiting_since:  <timestamp>            — when it entered the waiting state
  age:            <now - waiting_since>  — in days
  severity:       fresh (<1d) | warm (1–3d) | hot (3–7d) | stuck (>7d)
}
```

Adapters map provider concepts onto this model. **Metric definitions never reference a provider.** Severity bands are defaults — tune per team once baseline data exists.

### GitHub adapter (v1)

| DecisionPoint | GitHub mapping | How to detect |
|---|---|---|
| D1 review wait | Open non-draft PR with no review submitted | `gh pr list --json number,title,url,createdAt,updatedAt,reviewRequests,reviews,isDraft,author,reviewDecision` — reviews empty, not draft. If reviewRequests is empty too, decision_owner = **"unassigned reviewer"** — report that as its own finding (review-routing gap), don't skip the PR. waiting_since = createdAt (or last push) |
| D2 author wait | Open PR with `CHANGES_REQUESTED` and no newer commit | reviews contain CHANGES_REQUESTED, no push after that review. owner = PR author |
| D3 merge wait | Open PR with `APPROVED`, mergeable, unmerged | reviewDecision == APPROVED. owner = author/maintainer |
| D4 staleness | Open issue, assigned or in-progress-labelled, no timeline events for N days (default 5) | `gh issue list --json number,title,url,assignees,labels,updatedAt` + recent activity check. owner = assignee |

Drill-down: `gh pr view <n> --json ...`, `gh api repos/{org}/{repo}/issues/{n}/timeline`.

**Bot traffic:** PRs authored by bots (dependabot, release-please/release bots) are real decision points but drown the signal — tally them in a separate "bot queues" line, never in the needle. **Ages** are calendar days in v1; switch to working days once baseline is tuned.

### Future adapters (Jira / Azure DevOps)

Same DecisionPoint contract. Jira: D1≈"In Review" column age, D4≈in-progress issue with no linked commit activity. ADO: PR reviewer votes, work-item state age. Add as a new section here — nothing else changes.

---

## How to run

### Step 1 — Triage scan (always first)

Read `repos.txt`. For each repo, collect DecisionPoints for D1–D4 (read-only `gh` calls, run repos in parallel). Compute per-dimension: count, oldest age, hot+stuck count.

### Step 2 — The compass report

Lead with the needle — the dimension with the most hot/stuck items (tiebreak: oldest item). Format:

```
## Compass — <date>

**Needle: D1 Review queue** — 4 PRs waiting, oldest 6 days.

| Dim | Waiting | Oldest | Hot/Stuck |
|-----|---------|--------|-----------|
| D1 Review queue | 4 | 6d | 2 |
| D2 Author queue | 1 | 2d | 0 |
| D3 Merge queue  | 2 | 1d | 0 |
| D4 Staleness    | 3 | 12d | 3 |

### Where to look first
1. <repo>#<n> "<title>" — waiting on @<owner> for 6d  <url>
2. ...
```

Then a one-paragraph reading of what the pattern *means* (e.g. "review capacity is concentrated on one person" / "approved work isn't being merged — decision made, action missing"). The manager should learn the model from using the tool.

Save the report to `reports/`, then offer drill-down and (only if user asks) nudge mode.

### Step 3 — Drill-down (on request)

"Why is X stalled?" → walk the artefact trail: who was asked, when, what happened since, what exactly is the pending decision. End with the single concrete unblock action.

### Step 4 — Nudge mode (explicit consent, per item)

For a chosen DecisionPoint, draft a short, neutral, queue-framed comment:

> @owner — this PR has been waiting on review for 6 working days and is blocking <thing>. Still on your radar, or should the review be reassigned?

Show draft + exact target. Ask: post / edit / skip. Post only on explicit yes, via `gh pr comment` / `gh issue comment`. Log every posted nudge (and skipped drafts) at the bottom of the day's report.

---

## Cadence

Designed for a daily or twice-daily pulse run (morning: triage; afternoon: nudge pass on anything still hot). Can be wired to a schedule later — but **scheduled runs are still read-only**: a scheduled run produces a report, never a nudge.
