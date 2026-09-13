## What it does

`diagnosing-bugs` investigates a hard bug or performance regression using a **tight feedback loop**: a repeatable observation that distinguishes the reported symptom from correct behaviour. Code inspection and hypotheses help build that loop; a plausible explanation is not treated as a verified fix.

It reuses evidence already available, including prior triage results, and scales investigation to what remains uncertain. A diagnosis-only request ends with findings and their limits. A fix request continues through the relevant verification and cleanup.

## When to reach for it

Type `/diagnosing-bugs`, or the agent reaches for it automatically when the task involves a hard-to-reproduce bug or performance regression.

| Your situation | What fits |
| --- | --- |
| A bug that resists a first look or fails intermittently | This skill |
| A timing regression with a specific symptom | This skill, using comparable measurements |
| A simple error explanation | A direct answer; a full investigation is unnecessary |
| A proactive search for bottlenecks without a reported regression | A separately scoped performance review |
| A raw incoming report that needs categorisation | [triage](https://aihero.dev/skills-triage) |
| An unresolved design question | [prototype](https://aihero.dev/skills-prototype) |

## The tight loop

A useful loop checks the exact symptom. An existing failing test may be enough; other cases need a fixture request, browser interaction, trace replay or small harness. Detailed reproduction methods are consulted only when the investigation needs them.

Tight means sufficiently fast and repeatable to distinguish causes. Intermittent bugs need an observed failure rate and comparable conditions, not a single lucky passing run. Performance regressions need before-and-after measurements under the same workload. Reduction is useful when it makes the cause clearer or the regression check practical; it is not a mandatory search for the smallest possible input.

## Evidence and completion

Hypotheses state predictions that can be tested. Their number depends on the uncertainty. Targeted probes distinguish them, and temporary logs carry a unique tag so cleanup is reliable.

When fixing is requested, a regression test should exercise the real bug pattern at a suitable interface. The agent checks the original scenario as well as any reduced reproduction and runs affected checks. If no suitable test seam exists, it explains the missing coverage rather than presenting a shallow test as proof.

## Common questions

**Will it still build a reproduction for a quick question?**

Its automatic trigger is now limited to hard-to-reproduce bugs and performance regressions. A direct explanation does not need this workflow. If you explicitly invoke it for diagnosis only, it reports findings without treating that as a request to edit code.

**What if the agent cannot reproduce the bug?**

It continues useful read-only investigation, distinguishes hypotheses from confirmed causes, and identifies the specific artifact or access needed next. It cannot claim a verified fix without supporting evidence. A missing reproduction does not prohibit reading the code that might explain how to build one.

**I already ran triage. Does it start over?**

It reuses the observations and reproduction from that work when they capture the same symptom. Further checks address remaining uncertainty rather than repeating completed investigation by default.

**Will captured output expose secrets?**

The instructions require redacting commands, output and captured artifacts before sharing them, keeping credentials in environment variables and quoting only relevant lines. Production instrumentation requires authorization. Raw captures should not be treated as ready to publish.

**What happened to `/diagnose`?**

It was renamed to `/diagnosing-bugs` in v1.0.0. Update saved prompts or wrappers that still name the old skill.

## It's working if

- The reproduced failure matches the symptom you reported.
- Each hypothesis has a prediction and the report distinguishes it from an observed cause.
- The agent reuses a useful reproduction you already supplied.
- Verification checks the original scenario, with intermittent rates or timing variability disclosed where relevant.
- Temporary instrumentation is gone, and the report explains the cause, change and any coverage gap.
- When access is missing, you receive a specific request for evidence and an honest account of what remains unverified.

## Where it fits

`diagnosing-bugs` is a standalone investigation with no tracker setup requirement. [triage](https://aihero.dev/skills-triage) can supply the initial report and evidence. If the investigation reveals a missing test seam, [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) is possible follow-up work; it does not expand the bug fix automatically. [ask-matt](https://aihero.dev/skills-ask-matt) maps the wider flows.
