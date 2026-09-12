---
name: diagnosing-bugs
description: "Diagnose hard-to-reproduce bugs and performance regressions with a focused feedback loop."
---

# Diagnosing Bugs

Diagnose a specific hard bug using a **tight feedback loop**: a repeatable observation that distinguishes the reported failure from correct behaviour. Start with the evidence already available, including prior triage results. Scale the investigation to what remains uncertain.

Use the relevant `CONTEXT.md` for domain terms and ADRs when a hypothesis or fix touches a recorded decision. Keep credentials in environment variables; redact commands, output and captured artifacts before sharing them. Quote only the lines needed to explain the evidence.

## Establish the signal

Identify the user's exact symptom and the expected behaviour. Read code, logs and relevant history to locate the failing path and construct a useful reproduction. Hypotheses can guide this work; distinguish them from confirmed causes.

Prefer an existing failing test or runnable reproduction. Build a focused harness when needed. For alternative reproduction methods, intermittent failures or performance measurements, read [FEEDBACK-LOOPS.md](FEEDBACK-LOOPS.md) only for the applicable case.

Run the reproduction and record the command and relevant redacted result. Check that it catches this symptom, rather than a nearby error. Aim for a fast, repeatable signal; for intermittent bugs, record the observed failure rate and conditions. Reduce inputs and setup while doing so helps distinguish causes or makes the regression check practical.

If reproduction is unavailable, continue useful read-only investigation. Report what was tried, what remains hypothetical, and which missing artifact or access would let you test it. Ask only for that missing evidence or permission; production changes still require authorization. A plausible explanation alone does not establish a verified fix.

## Distinguish causes

Choose falsifiable hypotheses from the evidence. Consider alternatives when the cause is uncertain; there is no required number. State what observation would support or reject each, and choose probes that separate them.

Use a debugger, targeted logs or measurements as appropriate. Change one relevant variable at a time. Tag temporary debug logs with a unique prefix, such as `[DEBUG-a4f2]`, so they can be removed reliably. Share meaningful findings without waiting for approval to perform already-authorized investigation.

## Fix and verify

When a fix is in scope, make the smallest change that addresses the supported cause. Where a suitable test seam exists, first turn the reproduction into a failing regression test that exercises the actual bug pattern, then verify it passes with the fix. A test at a shallower seam that cannot catch the reported failure is not regression coverage; report that limitation if no suitable seam exists.

Check the fix against the original scenario as well as the reduced reproduction, reusing a result when they are the same check. Run affected checks and required repository validation. Expand or repeat verification only when new changes, failures or unresolved concerns justify it.

## Completion

For a diagnosis-only request, return the supported cause and evidence, or the unresolved hypotheses and specific evidence needed next. For a fix request, continue through verification and cleanup before finishing:

- The original symptom is resolved with recorded evidence, or the remaining verification gap is explicit.
- Regression coverage exercises the real failure, or its absence is explained.
- Task-added instrumentation is removed and temporary artifacts are handled within the user's cleanup permissions.
- The final report, and commit or PR description when applicable, explains the cause, change and verification limits.

If a missing test seam prevented regression coverage, note the architectural limitation as follow-up work without expanding the fix automatically.
