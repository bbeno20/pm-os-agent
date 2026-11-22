# Build Insights: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 4, what you learned building it
>
> ✅ **What this validates:** you can reflect on what building it taught you, by the end you'll have proven the friction, the learning, and the aha that changes how you'd design your next agent.

## Friction

Setup was the worst part. GitHub logins, tokens, and the terminal had nothing
to do with product management. The free model was also slow and sometimes
timed out. In the actual build, the stop conditions took the most thought,
figuring out the difference between stuck and escalate.

## Learning

Most of the job is decisions, not code: what the agent can do alone, when it
stops, and what it has to prove first. Just because the agent can do
something doesn't mean it should. And a limit only counts if it's enforced
in code, not just asked for in the prompt.

## Aha moment

Watching the critic catch a made-up launch date three times in a row. That's
when it clicked that the agent can't grade its own work. Next time I'd add
the independent check from day one.

## What you'd do differently

I'd add the timeout in code from the start, since it's still just on paper.
I'd also use a better paid model so runs don't stall. And I'd hook it up to
real data like Jira sooner, so I'm testing on something real, not just fake
projects.
