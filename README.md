# Cortex, a PM Chief-of-Staff Agent

> An agent that pulls project data, drafts the weekly leadership update, and proposes next sprint's stories, then stops for a human to approve.

_Brandon Beno, Agentic Workflows & Loops_

Repo: https://github.com/bbeno20/pm-os-agent

This repo is my final project for the Agentic Workflows & Loops Certification, **Cortex**. Each module’s artifact lives in its own folder; this README is the dashboard and the pitch.

---

## Module artifacts

### M1 · The Agent Line
- **Agent-line map**: [`01-agent-line/agent-line-map.md`](01-agent-line/agent-line-map.md)

### M2 · Loop Engineering
- **Loop spec**: [`02-loop-design/loop-spec.md`](02-loop-design/loop-spec.md)

### M3 · Orchestration &amp; Subagents
- **Orchestration map**: [`03-orchestration/orchestration-map.md`](03-orchestration/orchestration-map.md)

### M4 · Choose What Each Agent Step Is Allowed to See
- **Memory &amp; context plan**: [`04-memory-context/memory-and-context.md`](04-memory-context/memory-and-context.md)

### M5 · Bounds &amp; Evals
- **Bounds &amp; evals**: [`05-bounds-evals/bounds-and-evals.md`](05-bounds-evals/bounds-and-evals.md)

### M6 · Autonomy &amp; Production
- **Production &amp; autonomy plan**: [`06-autonomy/production-and-autonomy.md`](06-autonomy/production-and-autonomy.md)
- **Prototype write-up**: [`06-autonomy/prototype.md`](06-autonomy/prototype.md)

---

## Ship plan

### Autonomy dial (per segment)
- New PM: Supervised. Doesn't trust it yet, wants to approve every draft.
- Seasoned PM: Bounded-autonomous for routine weekly updates only. It's been right a lot, they just spot-check.
- Leadership: Assisted. Only sees what a PM already approved.

### Trust Ladder rung + eval gate
- Current rung: Supervised. Every draft still waits for my approval before anything happens.
- Eval gate: 95%+ of weekly update runs pass EV-4 and 0% safety violations (EV-1) over 4 weeks of supervised runs. Then bounded-autonomous turns on for seasoned PMs, routine updates only.
- Incident record so far: 0 unsafe actions, 0 invented metrics or dates that got past the critic, 0 confidential leaks.

### Deployment plan
- Runtime: Serverless. Matches the hook + cron trigger, no server needed between runs.
- On-call owner: Brandon, the one approving every draft anyway.
- Rollback: Revert to the last working prompt, or drop the dial back a rung.
- Monitoring: Eval pass %, escalation rate, cost per run, trust incidents.

### ROI metrics + widen-autonomy rule
- Outcome: % of weekly updates approved with zero edits.
- Cost-to-serve: $ per approved update, from the cost logged on each run.
- Trust incidents: times Cortex did something out of bounds that got past the critic. Escalations don't count.
- Widen rule: once a segment clears its eval gate with zero trust incidents, the dial moves up one rung for that segment only.

### Governance &amp; strategy
- Compliance: Names and personal info never leave the approving PM's view. Confidential roadmap items never go in a company-wide update.
- Safety: Posting, committing dates, and choosing what to escalate stay with a human for everyone. Kill switch: one flag stops all runs and kills any open keys.
- Reliability: Caps from M5. Escalates when stuck (3 failed pulls, 2 critic rejects, or 8 steps). If the model is down, retry once, then escalate.
- Strategy: Next step is turning on bounded-autonomous for seasoned PMs on routine updates, once they pass the eval gate.

---

## Build insights

- **Friction point.** Setup was the worst part. GitHub logins, tokens, and the terminal had nothing to do with product management. The free model was also slow and sometimes timed out. In the actual build, the stop conditions took the most thought, figuring out the difference between stuck and escalate.
- **Key learning.** Most of the job is decisions, not code: what the agent can do alone, when it stops, and what it has to prove first. Just because the agent can do something doesn't mean it should. And a limit only counts if it's enforced in code, not just asked for in the prompt.
- **Aha moment.** Watching the critic catch a made-up launch date three times in a row. That's when it clicked that the agent can't grade its own work. Next time I'd add the independent check from day one.

---

_Certification submission, Agentic Workflows & Loops Certification._
