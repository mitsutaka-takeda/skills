---
name: research
description: "Investigate a scoped question using primary sources and save a cited research note."
---

Research in the current agent by default. Delegate only when the user explicitly requests a background agent or multi-agent work for this task. Invoking this skill alone does not request delegation. A delegated researcher follows the steps below directly and does not spawn another agent.

The task:

1. Investigate the question against **primary sources** (official docs, source code, specs, first-party APIs), not a secondary write-up of them. Follow every claim back to the source that owns it.
2. Write the findings to a single Markdown file, citing each claim's source.
3. Save it where the repo already keeps such notes; match the existing convention, and if there is none, put it somewhere sensible and say where.

Stop when the scoped question is answered with primary-source evidence, or the remaining gap requires unavailable evidence or user input. Record uncertainty and the missing evidence rather than broadening the topic indefinitely. Return the file path and a short answer.
