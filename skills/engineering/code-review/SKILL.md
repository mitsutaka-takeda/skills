---
name: code-review
description: "Review a PR, branch, or working-tree diff against repository standards and the supplied requirements."
---

Two-axis review of the changes the user asks to assess:

- **Standards**: does the code conform to this repo's documented coding standards?
- **Spec**: does the code faithfully implement the originating issue / spec?

Use one agent by default: review Standards, then Spec, keeping separate notes and findings. Use sub-agents only when the user explicitly requests delegation or multi-agent work for this task. Invoking this skill alone is not a request for delegation. A delegated reviewer performs its assigned pass directly without spawning further agents.

Use `docs/agents/issue-tracker.md` when available to locate linked issues. A supplied spec, local file, or conversation can provide the requirements without tracker setup.

## Process

### 1. Pin the review scope

Use the comparison the user supplied or already established in the conversation. For a PR, obtain its base and head from PR metadata and resolve the matching commits locally; ask only if the intended range remains ambiguous or cannot be obtained. Record the resolved SHAs so a moving branch name does not silently change the review.

Choose the diff that includes the requested work:

- **Committed branch or PR changes:** use `git diff <base-sha>...<head-sha>` and `git log <base-sha>..<head-sha> --oneline`.
- **All uncommitted tracked changes:** use `git diff HEAD`; for staged-only or unstaged-only requests, use `git diff --cached` or `git diff` respectively.
- **Branch changes plus working-tree changes:** resolve the merge-base of the base and HEAD, then use `git diff <merge-base-sha>`.

For working-tree reviews, inspect `git status --short` and read in-scope untracked files separately, since diff commands omit them. Preserve a snapshot of the reviewed diff or recheck it if the working tree changes during review. Report unavailable refs or an empty scope before proceeding.

### 2. Identify the spec source

Use the requirements the user explicitly supplied, including an agreed conversation, file or issue. When no source is given, look for linked requirements in the PR or commit messages and relevant spec files under `docs/`, `specs/` or `.scratch/`. Use the configured issue-tracker workflow when needed.

Ask only when the source is still missing or sources materially conflict. Continue the Standards pass while awaiting clarification. If no spec is available, mark Spec as "no spec available" rather than inventing requirements.

### 3. Identify the standards sources

Anything in the repo that documents how code should be written, such as `CODING_STANDARDS.md` or `CONTRIBUTING.md`.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below: a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation. Like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name**: a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code**: the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy**: a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps**: the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession**: a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches**: the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery**: one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change**: one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality**: abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains**: long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man**: a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest**: a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

### 4. Review both axes

Read the selected diff, any in-scope untracked files and the applicable commit list once, then perform these passes using the sources gathered above:

- **Standards:** report documented violations with the standards file and rule, and possible baseline smells with their name and relevant hunk. Distinguish violations from judgement calls; repo standards override the baseline. Skip rules enforced by tooling.
- **Spec:** report missing or partial requirements, unrequested behaviour, and incorrectly implemented requirements. Cite the requirement and the affected code for each finding. If no spec is available, mark this pass as skipped.

Keep each pass concise, normally under 400 words, while retaining evidence needed to assess each finding. A clean pass is a valid result; do not manufacture findings to fill a quota.

If the user explicitly requests parallel reviewers, assign one pass to each of at most two sub-agents. Supply the resolved review scope, diff or snapshot, applicable commit list, relevant source paths and pass criteria. Each reviewer reads only what its pass needs and reports directly; it must not invoke this skill recursively or delegate further. If delegation is unavailable, perform both passes yourself and disclose that they were not independently reviewed.

### 5. Aggregate

Verify each finding against the actual diff and cited source, then present the two reports under `## Standards` and `## Spec` headings. Do **not** merge or rerank findings, because the two axes are deliberately separate (see _Why two axes_).

End with a one-line summary: total findings per axis, and the worst issue _within each axis_ (if any). Don't pick a single winner across axes: that's the reranking the separation exists to prevent.

## Why two axes

A change can pass one axis and fail the other:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the issue asked but breaks the project's conventions → **Spec pass, Standards fail.**

Reporting them separately stops one axis from masking the other.
