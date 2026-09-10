# Design It Twice

When the user wants to explore alternative interfaces for a chosen deepening candidate, design alternatives in the current agent by default. Use sub-agents only when the user explicitly requests delegation or multi-agent work for this task; invoking the design skill alone does not request delegation. A delegated designer works directly without further delegation. Based on "Design It Twice" (Ousterhout): your first idea is unlikely to be the best.

Uses the vocabulary in [SKILL.md](SKILL.md): **module**, **interface**, **seam**, **adapter**, **leverage**.

## Process

### 1. Frame the problem space

Before designing alternatives, write a user-facing explanation of the problem space for the chosen candidate:

- The constraints any new interface would need to satisfy
- The dependencies it would rely on, and which category they fall into (see [DEEPENING.md](DEEPENING.md))
- A rough illustrative code sketch to ground the constraints, not a proposal, just a way to make the constraints concrete

Show this to the user, then proceed to the alternatives using the established constraints.

### 2. Design distinct alternatives

Develop at least two meaningfully different interfaces yourself, using different constraints below. Add another only if it exposes a useful trade-off; cosmetic variants do not count.

Reuse the relevant file paths, coupling details, dependency category from [DEEPENING.md](DEEPENING.md), and what sits behind the seam. Give each alternative a different constraint:

- Minimal interface: "Minimize the interface: aim for 1–3 entry points max. Maximise leverage per entry point."
- Flexibility: "Maximise flexibility: support many use cases and extension."
- Common caller: "Optimise for the most common caller: make the default case trivial."
- Cross-seam dependencies (if applicable): "Design around ports & adapters for cross-seam dependencies."

Use both [SKILL.md](SKILL.md) vocabulary and CONTEXT.md vocabulary consistently across the alternatives. When delegation is explicitly requested, assign each independent alternative to one sub-agent with this context and a distinct constraint. Respect the requested agent count and available capacity; produce any remaining alternatives yourself.

For each alternative, provide:

1. Interface (types, methods, params, plus invariants, ordering, error modes)
2. Usage example showing how callers use it
3. What the implementation hides behind the seam
4. Dependency strategy and adapters (see [DEEPENING.md](DEEPENING.md))
5. Trade-offs: where leverage is high, where it's thin

### 3. Present and compare

Present designs sequentially so the user can absorb each one, then compare them in prose. Contrast by **depth** (leverage at the interface), **locality** (where change concentrates), and **seam placement**.

After comparing, give your own recommendation: which design you think is strongest and why. If elements from different designs would combine well, propose a hybrid. Be opinionated: the user wants a strong read, not a menu.
