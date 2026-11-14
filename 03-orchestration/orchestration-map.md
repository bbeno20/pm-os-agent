# Orchestration Map: Cortex PM Chief-of-Staff Agent

> Module 3 · Orchestration & Subagents, ★ Deliverable 3
>
> ✅ **What this validates:** nothing advances unchecked, by the end you'll have proven a justified topology, a roster, and a validator with a defined fail action.
>
> Builds on your M2 Loop Spec. Only split one agent into a team when there's a real reason, coordination has a cost.

## 1. Why split? (or why not)

Cortex stays a single agent for pulling data, drafting the update, and proposing
stories — separation of concerns, parallelism, and context-window pressure don't
apply at this scale. The one reason that holds is the independent validator:
Cortex can't reliably catch its own invented numbers or promises, so it needs a
separate critic that never saw how the draft was written. That's why Cortex
splits into exactly one agent + one validating subagent, not a full team.

## 2. Topology

**Pattern:** single + subagents

```
[Hook: new PM task] or [Cron: pre-meeting sweep]
        → [Cortex: pulls data, drafts update + stories]
        → [Validator] —fail→ back to Cortex (max 2 revisions) —exhausted→ escalate to human
                      —pass→ [PM review checkpoint] → queued, nothing posted
```

## 3. Roster

| Agent / subagent | Responsibility | Runs which Loop Spec |
|---|---|---|
| Cortex | Pulls project data, drafts the status update + proposed stories | The M2 loop (hook primary, cron backup) |
| Critic / Validator | Checks Cortex's draft against the 5 checks before it reaches the PM | One-shot validation call per draft — no loop/trigger of its own, invoked by Cortex each time a draft is produced |

## 4. Communication & hand-offs

- **Cortex → Critic:** the drafted output text, plus the full source log (every
  tool call + result Cortex used), so the critic can check claims against real
  data.
- **Critic → Cortex (on fail):** just the rejection reasons (e.g. "invented
  metric", "unconfirmed date"), not the critic's own reasoning process.
- **Critic → PM (on pass/exhausted):** the verdict plus the draft, surfaced in
  the HITL CHECKPOINT or ESCALATE EXIT banner.
- **Protocol:** no MCP/A2A — a plain in-process Python function call
  (`critic.review(...)`), not a separate service.

## 5. The validator

- **What the critic checks:**
  1. Every number and status call traces to pulled data.
  2. The update uses the right project and real PR/issue IDs.
  3. No unconfirmed promises (e.g. firm launch dates).
  4. Nothing marked confidential/embargoed appears.
  5. The story batch stays within the cap.
- **Fail action:** Tiered — revise first (send back to Cortex with the reason,
  up to 2 tries), then escalate to the human with the last draft + reasons if
  revisions run out. More tries just burn cost and time on a draft that's
  probably stuck.
- **Revision cap:** 2.
- **Pass action:** advances to the PM review checkpoint; never auto-sends
  (posting stays above the agent line from M1).

## 6. State: shared vs isolated

Shared: the source data (tool pull results) and the current draft — both agents
need these to do their job. Isolated: the critic's own reasoning never flows
back into Cortex, only the structured verdict + reasons do — so Cortex has to
actually fix the draft instead of arguing its way past the critic.

## 7. Cost & latency budget

Per attempt, the critic adds exactly +1 model call (1 draft call → 1 draft + 1
critic call). Worst case (revision cap = 2): 3 draft attempts × (draft +
critic) = up to 6 model calls before it escalates, vs. 1 call if there were no
validator at all — a real forced-failure run that hit the cap cost ≈$0.0059
across 3 rounds. Added latency: each extra round is one more API round-trip (a
few seconds) before a draft reaches the PM; worst case adds roughly 2 extra
round-trips over the no-critic path.
