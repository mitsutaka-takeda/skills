---
name: claude-handoff
description: Prepare a conversation handoff, optionally starting a Claude background agent on explicit request.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

Write a handoff summary of the current conversation. By default, save it to the OS temporary directory and return the path, keeping execution in the current agent. Launch a Claude background agent only when the user explicitly requests that launch; invoking this skill alone does not request delegation. A delegated agent prepares the summary directly without launching another agent.

For an explicitly requested launch, use `claude --bg --name "<descriptive name>" "<handoff summary>"` only when the Claude CLI supports it. It starts in the current working directory; the user manages it with `claude agents`. If the CLI is unavailable, return the saved summary and report that launch did not occur. Tell the receiving agent to execute its assigned work directly without further delegation.

When launching, pass `-n`/`--name` with a descriptive name (e.g. `--name "Fix login bug"`); it sets the display name shown in the job list, session picker, and terminal title.

Include a "suggested skills" section in the summary, naming which skills the next agent should call the Skill tool for.

Do not duplicate content already captured in other artifacts (specs, plans, ADRs, issues, commits, diffs). Reference them by path or URL instead.

Redact any sensitive information, such as API keys, passwords, or personally identifiable information, since the summary becomes the agent's prompt.

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the summary accordingly.
