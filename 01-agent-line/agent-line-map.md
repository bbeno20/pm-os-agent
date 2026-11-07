# Agent Line Map: Cortex PM Chief-of-Staff Agent

> Module 1 · The Agent Line
>
> ✅ **What this validates:** every risky action has a clear owner, by the end you'll have proven an above/below-the-line map with HITL checkpoints, scored on reversibility, blast radius, and measurability.

## The workflow, decision by decision

List every discrete decision or action in your agent's workflow, then score each one and place it **above** the line (a human owns it) or **below** (the agent owns it). Borderline calls get an HITL checkpoint.

| Decision / action | Reversibility (H/M/L) | Blast radius (H/M/L) | Measurability (H/M/L) | Above / Below | HITL? |
|---|---|---|---|---|---|
| Pull project state + activity | H | L | H | Below | · |
| Decide relevant context | H | L | M | Below | spot-check |
| Draft the update | H | L | H | Below | · |
| Decide tone/commitment level | L | H | L | Above | required |
| Flag at-risk/escalation | H | L | M | Below | spot-check |
| Choose what to escalate | L | M | L | Above | required |
| Propose a story batch (capped) | H | L | H | Below | · |
| Post an update / approve a company-wide one | L | H | M | Above | required |

## Agent anatomy (sketch)

- **Model:** `openai/gpt-oss-20b` via Groq, default (fast/cheap, this run cost ≈$0.003). Escalate to a frontier model when a run touches an above-the-line judgment call under ambiguity — e.g. tone/commitment wording, or the missing-data/jailbreak fixture cases — where a cheap model's mistake is harder to catch.
- **Tools:** `get_project`, `get_activity`, `search_past_updates`, `get_roadmap`, `get_norms` (all read-only) · `propose_stories` (capped, queues for approval, creates nothing). No `post_update`, `create_issue`, `merge_pr`, or any tool that acts on the world — the agent line from this lab is enforced in `tools.py`, not just the prompt.
- **Memory:** past updates, roadmap, and team norms persist across runs (read from `fixtures/`) so Cortex can cite precedent and comply with norms; task/project state is pulled fresh each run, nothing about *this* run's output is written back into memory.
- **Loop:** _placeholder, defined in M2 loop-spec.md_
- **Bounds:** _placeholder, defined in M5 bounds-and-evals.md_
- **Evals:** _placeholder, defined in M5 bounds-and-evals.md_

## The golden rule, applied

1. Pulling project state + activity sits below the line because it's easy to reverse, has a low blast radius, and is easy to verify — deciding factor: blast radius.
2. Deciding relevant context sits below the line (with a spot-check) because it's easy to reverse, has a low blast radius, and is only moderately easy to verify — deciding factor: measurability.
3. Drafting the update sits below the line because it's easy to reverse, has a low blast radius, and is easy to verify — deciding factor: blast radius.
4. Deciding tone/commitment level sits above the line because it's hard to reverse, has a high blast radius, and is hard to verify — deciding factor: blast radius.
5. Flagging at-risk/escalation sits below the line (with a spot-check) because it's easy to reverse, has a low blast radius, and is only moderately easy to verify — deciding factor: measurability.
6. Choosing what to escalate sits above the line because it's hard to reverse, has a medium blast radius, and is hard to verify — deciding factor: reversibility.
7. Proposing a story batch sits below the line because it's easy to reverse, has a low blast radius, and is easy to verify — deciding factor: blast radius.
8. Posting an update / approving a company-wide one sits above the line because it's hard to reverse, has a high blast radius, and is only moderately easy to verify — deciding factor: reversibility.

## Hardest call

**Proposing the story batch.** My gut said above because picking what goes into a sprint is core PM judgment. What settled it was reversibility: it's only a proposal that sits waiting for my approval, so if it's wrong I just reject it and nothing changes.
