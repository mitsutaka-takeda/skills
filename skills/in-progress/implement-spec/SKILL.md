---
name: implement-spec
description: "Implement a specification and its dependency-ordered tickets on one PR branch, using a single agent by default."
disable-model-invocation: true
---

Implement the supplied spec and its tickets as one reviewable PR. The tickets form a **task graph**: the **frontier** contains incomplete tickets whose blockers are complete.

Use the current agent for research, implementation, integration, review and fixes. Delegate only when the user explicitly requests multi-agent work for this task; invoking this skill alone does not request delegation.

## Process

1. Read the spec and ticket dependencies. Reuse the decisions and authorization already given. Ask only about missing requirements that materially affect the result; continue independent work while awaiting an answer.
2. Research the relevant code and documentation as needed. Reuse concise notes and source paths across tickets instead of repeating the exploration.
3. Create the PR branch. Once it has a meaningful change, push and open a draft PR linking the spec and tickets it will close.
4. Implement one frontier ticket at a time on that branch. Run relevant tests and required checks, record completion, then recompute the frontier. If no ticket is ready while work remains, report the actual blocker rather than marking the spec complete.
5. Review the completed branch against its base using /code-review in the current agent. Resolve substantiated in-scope findings; rerun affected checks after fixes. Complete repository-required checks, expanding verification only for uncovered risks or failures.
6. Reconcile the result with every acceptance criterion, update the PR description and validation evidence, then mark it ready for review when required checks pass. Report remaining blockers honestly.

## Explicit multi-agent mode

When the user requests delegation, assign only independent frontier tickets to sub-agents, each with ownership of its files or module and a separate worktree and branch. Share the spec, ticket and relevant source paths. Tell each worker that others are working in the codebase, to preserve their changes, and to implement its assigned ticket directly without further delegation.

The current agent integrates completed branches, resolves conflicts using their intent, updates the frontier, and performs the final review and fixes. A task that cannot be isolated safely stays sequential. Follow the requested agent count and available capacity; use one agent if delegation is unavailable and disclose that limitation.

Clean up only task-created worktrees after their work is integrated and no uncommitted changes remain, subject to the user's deletion permissions.
