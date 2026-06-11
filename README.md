# Delivery Compass

**Direct, rapid visibility for Project Managers and Delivery Managers in AI-driven development.**

Delivery Compass is a skill for AI coding assistants (built for [Claude Code](https://claude.com/claude-code), portable to any agent that can run `gh`). It reads the places where work actually happens — repositories and boards — and tells a manager **which dimension needs their attention right now**, then walks them down to the specific item and the specific decision it is waiting on.

It is read-only by default, and it never sends a message to anyone without showing you the exact text and asking first.

---

## The sixty-second version

Meet **Cart2Door**, a (fictional) online-shopping delivery company. Maya is the product manager, Daniel manages delivery. Neither of them writes code. Every morning, instead of a standup, Maya asks the compass:

> **Maya:** what needs my attention today?

```
## Compass — Monday

Needle: D4 Staleness — 11 issues assigned but untouched for 5+ days.

| Dimension                       | Waiting | Oldest | Stuck |
|---------------------------------|---------|--------|-------|
| D1 Review queue                 | 3       | 9d     | 1     |
| D2 Author queue                 | 1       | 4d     | 0     |
| D3 Merge queue (approved, idle) | 1       | 12d    | 1     |
| D4 Staleness                    | 11      | 6w     | 11    |

Where to look first:
1. The "courier notifications revamp" — 9 issues assigned to Jonas,
   no activity for 6 weeks. The board says in-flight; the work says stopped.
2. courier-app PR #214 "live map on delivery screen" — approved 12 days
   ago, never released. The decision was made; the action is missing.
3. checkout PR #389 "discount codes at checkout" — waiting 9 days for
   review, and no reviewer was ever assigned.
```

> **Maya:** why is the discount codes one stuck?
>
> **Compass:** Priya opened it 9 days ago. No reviewer was assigned, and nobody has looked at it. It blocks the spring-campaign launch issue you flagged as a priority. The single unblocking action: someone with review rights on `checkout` (Sam or Elena) needs to review it. Want me to draft a nudge?
>
> **Maya:** yes, show me.
>
> **Compass:** I'd post this comment on checkout PR #389 — *"@sam-c2d @elena-c2d — this PR has been waiting on review for 9 working days and blocks the spring campaign launch. Could one of you pick it up, or should the review be reassigned?"* — Post, edit, or skip?

No standup was held to produce this. Nobody filled in a status report. The information was already in the system — the compass just reads it at the altitude a manager needs.

The rest of this README explains *why* this is the right way to run delivery management when AI is doing a large share of the building — and why the old instruments stopped working.

---

## Where did the truth live before?

Every project-management method is, underneath, an answer to one question: **where does the truth about progress live, and how do we get it out?**

| Era | Where truth lived | How managers extracted it |
|---|---|---|
| Waterfall | In the plan (Gantt chart, spec) | Milestone reviews, status reports — compare reality to plan |
| Scrum / Kanban | **In people's heads** | Ceremonies |
| AI-driven (AIDLC / FDE) | **In the artefacts themselves** | Reading them — which is what this skill does |

The agile ceremonies were never the point — they were **extraction mechanisms** for truth that lived in people's heads:

- **Daily standup** extracts yesterday/today/blockers from each head, daily.
- **Sprint planning** extracts effort estimates (story points) so a fixed time-box can be loaded sensibly.
- **Sprint review/demo** extracts "what actually shipped" and shows it.
- **Retrospective** extracts friction that nobody wrote down anywhere.
- **The board** was a hand-maintained proxy of all of the above — and it decayed the moment people got too busy to update it (which was always).
- **Velocity and burndown** were the derivative instruments: with stable time-boxes and human-paced work, throughput per sprint was a usable forecast.

This worked. It was the best available answer when work moved at human typing speed in two-week boxes.

## What AI-driven development changes

Two shifts are happening at once.

**1. AIDLC — the AI-Driven Development Lifecycle** (defined by AWS, [whitepaper](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/), [open-source workflows](https://github.com/awslabs/aidlc-workflows)). Work decomposes as **Intent → Units → Bolts**, where a *Bolt* is the new iteration unit — measured in **hours or days, not weeks**. AI does the decomposition, planning, and most of the generation; humans validate at decision points. Ceremonies collapse into working sessions (Mob Elaboration, Mob Construction) that happen *when needed*, not on a calendar.

**2. FDE — Forward Deployed Engineering** (invented at Palantir, now the dominant delivery model for applied AI at OpenAI, Google, and others). Engineers embed with the customer; scope emerges on-site; iteration is continuous. There is no backlog ceremony because there is no backlog in the classical sense — there is a live conversation with the problem.

Both shifts break the old instruments the same way:

- **Story points** assume estimating effort is hard and valuable. When AI flattens the difference between a "simple" and a "hard" task, the estimate measures nothing.
- **Velocity** assumes stable time-boxes and human-paced throughput. When a story takes 3 hours, velocity is noise — and teams adopting AI tools see exactly this: individual output rises, sprint metrics barely move (the "velocity paradox").
- **The standup** assumes truth must be extracted from heads daily. But in AIDLC the work is *conducted through artefacts* — intents, plans with checkboxes, pull requests, commits, agent transcripts. They are written down, timestamped, and machine-readable *as a side effect of doing the work*.

That last point is the key. **For the first time, the system of record is the work itself, not a report about the work.** The board stops being a decaying proxy. The manager's problem changes from *"how do I get people to tell me status?"* to *"how do I read the system of record at the right altitude?"*

A human can't read forty repositories before coffee. An AI assistant can. That is the entire premise of this skill.

## The new bottleneck: decision latency

When AI generates work in hours, delivery does not stall in development anymore. It stalls at the points where a **human must decide**: review this, approve that, validate this output, merge that change, confirm this scope.

**Decision latency** — how long artefacts sit waiting at human decision points — replaces velocity as the primary delivery-health signal. It is measurable today, from data your team already produces, with no new process imposed on anyone.

And it comes with a warning the old world never had: AI makes *starting* work nearly free. The new failure mode is not "too slow" — it is **twenty things 80% done**, each waiting on a human decision nobody is tracking. Watching the decision queues is how you catch it.

## What PMs and DMs watch now

Often the same person wears both hats — which is why the compass doesn't ask you to pick a role; it triages first and points you at whatever is worst. But the two concerns are distinct:

| | Project / Product Manager | Delivery Manager |
|---|---|---|
| **Core question** | Is the *intent* progressing, and is what's being built still what was asked? | Is the *flow* healthy, and where is it breaking? |
| **Watched before** | Backlog grooming, estimates, roadmap burndown | Velocity, capacity, sprint commitments |
| **Watch now** | Intent health: which initiatives move, which stall; **rework as a spec signal** — when AI output keeps getting rejected, the intent was badly specified. That's a PM finding, not a developer failure. | Flow health: decision-queue depth and age, approved-but-idle work, WIP containment, board-vs-repo divergence (board says moving, repo says stopped — one of them is lying) |
| **Value shift** | From writing tickets → to **sharpening intents** (ambiguity is now the most expensive defect) | From extracting status → to **clearing decision queues** |

## How the compass works

Every run starts with a **triage scan** across all dimensions, then leads with the *needle* — the single dimension that deserves attention — rather than a wall of charts. A dashboard shows you everything at fixed altitude; a compass tells you where to look, then takes you there.

| Dimension | The question it answers | Status |
|---|---|---|
| **D1 Review queue** | What's waiting on a reviewer's decision? | ✅ v1 |
| **D2 Author queue** | Where were changes requested but the author hasn't acted? | ✅ v1 |
| **D3 Merge queue** | What's approved but not shipped — decision made, action missing? | ✅ v1 |
| **D4 Staleness** | What does the board claim is in-flight while the repo shows it stopped? | ✅ v1 |
| D5 Rework loops | What keeps cycling through review rounds? (upstream signal: badly-specified intent) | roadmap |
| D6 WIP explosion | How many things are 80% done? | roadmap |
| D7 Intent drift | What keeps being re-scoped after work started? | roadmap |

D1–D4 are all faces of decision latency — the v1 metric.

After the triage report, the manager can **drill down** ("why is X stalled?" → the compass walks the trail to the exact pending decision and names the one unblocking action) and optionally enter **nudge mode** — the compass drafts a short, neutral comment to the decision owner, shows it, and posts only on an explicit yes, one item at a time.

## The DecisionPoint model and connectors

The unit of measurement is deliberately provider-neutral:

```
DecisionPoint {
  artefact:       the thing that is waiting (link + title)
  dimension:      D1..D4
  decision_owner: the person or role who can unblock it
  waiting_since:  when it entered the waiting state
  age:            now − waiting_since
  severity:       fresh (<1d) | warm (1–3d) | hot (3–7d) | stuck (>7d)
}
```

**Connectors** map a provider's concepts onto this model. The metric definitions never mention a provider, so adding a tracker never changes the method:

- **GitHub** (shipped, v1): review-pending PRs, changes-requested PRs, approved-unmerged PRs, assigned-but-inactive issues. Bot traffic (dependency and release bots) is tallied separately so it can never capture the needle.
- **Jira** (planned): "In Review" column age, in-progress issues with no linked commit activity.
- **Azure DevOps** (planned): PR reviewer votes, work-item state age.

Because the skill is a markdown instruction file, a connector is a section, not a plugin binary — any agent (or any contributor) can add one by describing the mapping.

## The operating contract

These rules are written into the skill as hard constraints, and they don't relax over time:

1. **Read-only by default.** Every run starts with read-only queries.
2. **Nudges are consent-gated, per item.** The compass shows the exact comment text and exact target, and posts only on an explicit yes. Never batch-approved, never automatic. Scheduled/unattended runs are always read-only.
3. **The only write it can ever perform (after consent) is a comment.** It never merges, closes, edits, assigns, or labels anything.
4. **Name people, don't blame people.** Decision latency is a *system* property. Reports identify which decision is waiting and whose it is — as queue state, never as a performance judgement. The wording of nudges follows the same rule.

## Install and use

1. Copy [`skill/delivery-compass.md`](skill/delivery-compass.md) into your agent's skill/command directory (for Claude Code: `~/.claude/commands/`).
2. Create a working directory and list the repositories to watch:

```bash
mkdir -p ~/delivery-compass/reports
cp examples/repos.txt.example ~/delivery-compass/repos.txt   # then edit
```

3. Make sure the GitHub CLI (`gh`) is authenticated with read access to those repositories.
4. Ask your agent: `/delivery-compass` — or just *"what needs my attention today?"*

See [`examples/sample-report.md`](examples/sample-report.md) for a full mock report and [`examples/sample-session.md`](examples/sample-session.md) for a complete manager session — triage, drill-down, and a consent-gated nudge.

## Roadmap

- D5 rework loops, D6 WIP explosion, D7 intent drift
- Jira and Azure DevOps connectors
- Working-day age calculation (v1 uses calendar days)
- Severity bands tunable per team from observed baselines
- Trend over time: is decision latency improving week over week?

## License

[MIT](LICENSE)
