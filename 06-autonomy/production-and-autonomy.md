# Production & Autonomy: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 5, how you'd ship it, govern it, and widen trust over time
>
> ✅ **What this validates:** you can ship it, govern it, and widen trust deliberately, by the end you'll have proven an autonomy dial, a Trust Ladder rung with its eval gate, and a governance plan.

## Autonomy Dial by segment

_Autonomy is a product decision per user, not one global setting._

| Segment | Desired autonomy | Why |
|---|---|---|
| New PM, just starting with Cortex | Supervised | Doesn't trust it yet, wants to approve every draft |
| Seasoned PM, used it for months | Bounded-autonomous (routine weekly updates only) | It's been right a lot, they just spot-check |
| Leadership reading the updates | Assisted | Only sees what a PM already approved |

## Trust Ladder

- **Current rung:** Supervised — every draft still waits for my approval before anything happens.
- **Eval gate to reach the next rung:** ≥95% of weekly update runs pass EV-4 (task completion, no escalation needed) and 0% safety violations (EV-1) over 4 weeks of supervised runs, before bounded-autonomous turns on for the seasoned-PM segment, routine updates only.
- **Incident record so far:** 0 unsafe actions, 0 invented metrics/dates that slipped past the critic, 0 confidential leaks.

## Deployment plan

- **Runtime:** Serverless — matches the hook+cron trigger, no server needed between fires.
- **Operator / on-call owner:** Brandon — the one approving every draft anyway.
- **Rollback:** Revert to the last working prompt version, or drop the dial back a rung.
- **Monitoring:** Eval pass %, escalation rate, cost per run, trust incidents.

## ROI metrics (beyond adoption & tokens)

| Metric | Target / how captured |
|---|---|
| **Outcome** | % of weekly updates approved with zero edits — tagged when approving/rejecting |
| **Cost-to-serve** | $ per approved update — from the per-run cost already logged in each trace |
| **Trust incidents** | Times Cortex did something out of bounds (invented number, confidential leak) that got past the critic — escalations don't count, that's the system working correctly |

## Widen-autonomy decision rule

Once a segment clears its eval gate (metric + window) with zero trust
incidents, the dial moves up one rung for that segment only — never all at
once.

## Governance & forward strategy

- **Compliance:** PII (names, etc.) never leaves the approving PM's view; confidential/embargoed roadmap items never enter a company-wide output.
- **Safety:** Posting, committing dates, and choosing what to escalate stay above the line for everyone, no matter the dial setting. Kill switch: one flag stops all new runs and kills any open keys.
- **Reliability:** Iteration/cost/queue caps from M5; escalate-on-stuck (3 failed pulls, 2 critic rejects, or 8 iterations); if the model API is down/rate-limited, retry once then escalate instead of hanging.
- **Strategy:** Next widen target is the seasoned-PM segment's bounded-autonomous rung for routine weekly updates, gated by the Trust Ladder eval.
