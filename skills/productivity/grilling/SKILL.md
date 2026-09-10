---
name: grilling
description: Grill the user relentlessly about a plan, decision, or idea. Use when the user wants to stress-test their thinking, or uses any 'grill' trigger phrases.
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask the whole frontier in one round: number each question and give your recommended answer. Then wait for the user's answers before the next round.

Format a round like so:

```
❓ **Q1** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>
```

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Finding _facts_ is your job. Look up the environmental facts a frontier question needs yourself, using targeted reads or searches. Independent tool calls can run together without creating other agents. Use a sub-agent only when the user explicitly requests delegation for this task; a delegated agent resolves its assigned question directly. While facts are pending, ask only questions whose prerequisites are already settled. The _decisions_ are the user's: put each to them and wait.

The session is done when the in-scope frontier is empty and the decisions are recorded. Reuse answers already given; reopen a settled decision only when new evidence changes it. Continue into implementation when the user has already requested it and the relevant decisions are settled. Otherwise, finish with the agreed decisions and remaining next step.