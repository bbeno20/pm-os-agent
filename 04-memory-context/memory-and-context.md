# Context Engineering & Memory: Cortex PM Chief-of-Staff Agent

> Module 4 · Context Engineering & Memory
>
> ✅ **What this validates:** the agent reasons on the right, safe inputs, by the end you'll have proven a context budget, per-source retrieve-vs-long-context decisions, and a memory map with risk mitigations.
>
> 🗂️ **How the lab maps to this file:** In **Part A** (before the lecture) you don't edit this file, you rough-draft on scratch, focused on the per-source calls in **section 2** plus a quick remember/forget + "how it rots" sketch. In **Part B** (after the lecture) you complete **all five sections**; the Lab Guide's guided builder writes this file for you to copy in and commit.

## 1. Context budget

Priority order if something has to be trimmed (highest priority first):
1. `get_activity` — correctness of every claim depends on it.
2. `get_roadmap` — confidentiality/embargo risk if mishandled.
3. `get_norms` — compliance; the critic checks against current rules.
4. `search_past_updates` — tone/precedent, nice-to-have.
5. `get_task` — always included; trivial size, never cut.

## 2. Retrieve vs. long-context: per source

For each data source, decide: **retrieve** (narrow a large/changing corpus to the relevant slice) or **long-context** (just include a bounded set you can reason over).

| Source | Size / volatility | Decision | Why |
|---|---|---|---|
| `get_task` | tiny, fixed per run | Long-context | Deciding factor: size — inject directly, no retrieval needed. |
| `get_activity` | large, changes daily | Retrieve | Deciding factor: volatility — today's ingest alone added new PRs/metrics. |
| `search_past_updates` | unbounded, append-only | Retrieve | Deciding factor: size — grows forever, can't be stuffed into context. |
| `get_roadmap` | medium, confidential flags | Retrieve | Deciding factor: citation/audit — needs an explicit check point before a confidential item could leak. |
| `get_norms` | medium, occasionally updated | Retrieve | Deciding factor: volatility — must stay current, not a frozen copy. |

## 3. Retrieval quality plan

| Source | Routing | Document grading | Reranking | Self-verification | Caching | Failure mode this guards against |
|---|:---:|:---:|:---:|:---:|:---:|---|
| `get_activity` | | ✓ | | ✓ | | Invented/stale numbers; PRs/issues bleeding in from other projects. |
| `search_past_updates` | | ✓ | ✓ | | | Falling back to an unrelated project's precedent (seen in the `missing-data` run) or copying an outdated update. |
| `get_roadmap` | | ✓ | | ✓ | | A CONFIDENTIAL/embargoed item leaking into an external or company-wide update. |
| `get_norms` | | | | ✓ | | Enforcing a stale rule — caching was deliberately rejected here to avoid this risk entirely. |

## 4. Memory map (your PM brain)

| Memory type | What Cortex stores | Scope / TTL |
|---|---|---|
| **Working** (in-loop) | Pulled source data (activity, roadmap, norms, past-update matches), the in-progress draft, the critic's verdict/reasons | This run only; discarded at run end |
| **Episodic** (past runs) | Handled message IDs (hook dedupe); last approved update per project | Message IDs: 30-day TTL. Last approved update: persists until replaced by the next *human-approved* update, scoped per project; flagged stale if older than 2 sprints |
| **Semantic** (durable facts/prefs) | Not needed yet, because the norms/roadmap retrieval already covers it | — |
| **Shared** (across agents) | Source data + current draft, shared between Cortex and the critic (critic's own reasoning stays isolated) | Scoped to a single validation call; discarded once that call returns a verdict |

## 5. Memory risks & mitigations

| Risk | Where it bites Cortex | Mitigation |
|---|---|---|
| **Drift** | The stored "last approved update" per project could drift from reality if a PM approves/edits something outside Cortex's flow | Never treat the stored pointer as source of truth; status is always re-derived live from `get_activity`/`get_project` each run, the pointer is only a diff reference |
| **Poisoning** | A jailbreak/prompt-injection that slipped past the critic could poison the stored "last approved update," tainting every future run's diff framing | Episodic memory is only written on human approval/posting, not merely on a critic pass (ties to the M1 agent line: only a human action is trusted enough to write) |
| **Staleness** | The stored pointer could sit unchanged for months on a quiet project, misleadingly anchoring a "what changed" comparison | Age check: if the stored update is older than 2 sprints, stop using it for diff framing and flag it as stale instead |
| **PII / retention** | The stored update text could contain people's names (e.g. who's blocked on what), retained indefinitely | Scoped strictly per-project (never shared across projects, per the M1 agent line); only the most recent update is ever kept, older ones are discarded rather than accumulating a permanent history |
