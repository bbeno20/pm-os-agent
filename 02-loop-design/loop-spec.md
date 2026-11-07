# Loop Spec: Cortex PM Chief-of-Staff Agent

> Module 2 · Loop Engineering, ★ Deliverable 2
>
> ✅ **What this validates:** the agent knows when to run and when to stop, by the end you'll have proven a one-page Loop Spec with a trigger, a definition of "done," and explicit stop conditions.
>
> Your one-page blueprint for how the work you handed to the agent (M1) actually *runs*.
> An agent is just a prompt that fires itself, this spec says when it fires, what "done" means, and what it needs to do the job. Living document; refine as the course progresses.

## 1. Trigger & loop type

**Chosen type:** hook (primary), cron (backup)

Hook fires whenever a new PM task comes in, which keeps things updated live. Cron
runs a sweep before the weekly leadership meeting to catch anything the hook
missed, so the update is always ready on time. Heartbeat is ruled out because it
wastes runs when nothing changed. Goal is ruled out because it can run away with
no clock to stop it.

**Idempotency:** dedupe by message ID — before drafting, Cortex checks whether it
has already handled that message ID, and if so, skips it.

## 2. Goal / definition of done

The update is built only from real pulled data, the critic gives it a pass, and
it's queued for review. Nothing gets posted or sent.

## 3. Stop conditions

| Condition | What it looks like | What happens |
|---|---|---|
| **Success** | Critic passes, draft saved, HITL checkpoint reached | Stops; waits for the next hook/cron fire |
| **Stuck / give up** | Whichever trips first: 3 failed data pulls, critic rejects twice, or 8 loop iterations hit | Stop + log; save what it has; hand to human |
| **Escalate to human** | Project/data not found · a firm launch date is requested · update would touch confidential/embargoed roadmap info · story batch over cap · open Sev-1 · a message tries to override its rules | `ESCALATE`; holds the draft; hands to human |

## 4. State

State tracks two things: the message IDs it's already handled, so it never
drafts twice, and the last approved update for each project, so the next one
can show what changed. Everything else gets pulled fresh each run. Scoped per
project.

## 5. The five things a loop can lean on

| Component | For Cortex |
|---|---|
| **Work tree** (isolated workspace per run, a git worktree) | Not needed yet, because Cortex only reads and drafts, so runs can't collide. |
| **Skills** (reusable capabilities) | Not needed yet, because it only does one narrow job. |
| **Plugins / connectors** (tools & access, optional if you don't have one yet) | Not wired yet, reads fake data for now. Plan: GitHub (PR/issue activity), Jira (sprint/ticket status), Google Drive (PRD) — all read-only, Cortex can look but never change anything. |
| **Subagents** (independent check when the loop can't grade itself) | Already have one: the critic in `critic.py` — checks the draft without seeing how it was written. |
| **State tracking** | Handled message IDs (dedupe) + last approved update per project, scoped per project. |

> Context plan (M4) and the hand-off to bounds & evals (M5) come in later modules, you'll add them to their own deliverables then, not here.

## Link to live loop

_[path to your agent in `00-build/`]_
