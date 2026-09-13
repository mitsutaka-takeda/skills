# Choosing a feedback loop

Read the case relevant to the missing signal; these are options, not a sequence to exhaust.

## Reproduction methods

| Available evidence | Useful loop |
| --- | --- |
| A failing automated test | Narrow it to the symptom and reuse its setup |
| An HTTP endpoint or CLI | Replay a fixture request or input and assert the expected response |
| A browser interaction | Drive it with a headless browser and check DOM, console or network evidence |
| A captured request, trace or event | Replay the relevant path with secrets removed from shared artifacts |
| A path difficult to reach in the full system | Build a minimal harness with only the dependencies needed to exhibit the failure |
| Input-dependent incorrect output | Use property checks or fuzzing, preserving the failing input and seed |
| Known good and bad versions | Compare the same input or use an automated bisection check |
| An interaction only the human can perform | Adapt [scripts/hitl-loop.template.sh](scripts/hitl-loop.template.sh) to capture their observations |

## Intermittent failures

Pin controllable inputs such as random seeds, time and fixture state. Repeat or stress the suspected trigger in a suitable local environment to improve observability. Choose the trial count from the observed rate and cost, and report failures per trial. One passing run does not establish that a flake is fixed. Keep load within the authorized environment's limits.

## Performance regressions

Measure a baseline under comparable inputs and conditions, then use timing, a profiler or a query plan to locate the regression. Compare before and after using the same workload. Bisect when known good and bad states make it useful. Prefer measurements over adding logs that may distort timing; disclose variability and environment differences.
