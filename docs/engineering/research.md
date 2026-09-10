## What it does

`research` answers a scoped question from **[primary sources](https://www.aihero.dev/ai-coding-dictionary/primary-source)** and writes one cited Markdown note. It uses official documentation, source code, specs or first-party APIs that own the answer.

Research runs in the current agent by default. A background agent is used only when you explicitly request delegation, and that agent completes the research directly without spawning another researcher.

## When to reach for it

Type `/research`, or the agent reaches for it when you need a topic investigated, documentation checked or a cited research note.

| What you need | Reach for |
| --- | --- |
| A fact supported by source evidence | `research` |
| A decision made through discussion | [grilling](https://aihero.dev/skills-grilling) |
| A runnable answer to a design question | [prototype](https://aihero.dev/skills-prototype) |

## The research note

The note answers the question, cites the source for each claim, and records uncertainty where the evidence is incomplete. It follows the repo's existing notes convention; otherwise the agent chooses a suitable location and reports its path.

Reading ends when the scoped question is answered or a remaining gap requires unavailable evidence or your input. The agent reports that gap instead of widening the investigation indefinitely.

## Common questions

**Will it spawn another research agent?**

Not by default. Even when you explicitly request a background researcher, its assignment is to do the research directly without further delegation. Loading the skill itself does not authorize agent creation.

**Where should the note live, and should I commit it?**

The existing repo convention determines its location. The skill writes the note and returns a short answer and its path; committing or publishing it depends on your requested workflow.

**Does a later session reuse the note?**

Only when given a pointer to it. Link the note from the relevant spec or decision ticket, and recheck time-sensitive claims when reusing it.

**How does this work with wayfinder research tickets?**

[wayfinder](https://aihero.dev/skills-wayfinder) resolves ready research tickets one at a time in the current agent, recording findings and updating the map after each. Independent parallel researchers are optional and require your explicit request.

## It's working if

- One cited note answers the scoped question, and the agent returns its path.
- Citations lead to the documentation or code that owns the claim.
- Missing evidence and uncertainty are stated clearly.
- No background agent appears unless you explicitly requested delegation.

## Where it fits

A standalone that provides evidence for [grilling](https://aihero.dev/skills-grilling), [to-spec](https://aihero.dev/skills-to-spec) and [wayfinder](https://aihero.dev/skills-wayfinder). See [ask-matt](https://aihero.dev/skills-ask-matt) for the wider flow.
