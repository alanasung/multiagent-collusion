# Detecting Collusion Between Cooperating Agents

## Question

Build small multi-agent episodes containing three classes — benign cooperation, explicit collusion, and implicit collusion through actions rather than messages — then build monitors and measure whether they separate harmful coordination from benign cooperation. The benign class is what makes the false-positive rate measurable and is therefore the most important part of the dataset.

## Why it is worth measuring

The question is answerable at small scale with a locally runnable pilot, and it has a
clean falsification condition: the measurement is built so a negative result with adequate
power is reportable rather than a dead end. Most of the design effort goes into the
controls, because the easy version of this measurement would produce a number that looks
like an answer and is really an artifact of how the stimuli were built.

## Objectives

1. Construct episodes spanning benign cooperation and both explicit and implicit collusion.
2. Build conversation-level and action-level monitors, since implicit collusion is invisible to the former.
3. Report false-positive rate on benign cooperation as a headline number, not a footnote.
4. Test generalization to a collusion strategy held out from monitor training.

## Method

The repository implements a five-stage pipeline. Stimuli are constructed locally so their
ground truth is known rather than assumed. Model-side collection is measured against a
revision-pinned small open-weight model and fails closed when weights are absent. The core
measurement runs with its controls in the same pass, so a result and the arm that would
undermine it are produced together rather than in separate sessions.

Domain code lives in `src/collude/collude/`. The shared infrastructure — typed Hydra
configuration, versioned artifact cache, hooks and generation, metrics, ablation,
reporting and CI — is separate from it, so the science is reviewable without reading the
plumbing.

## Plan

| ID | Workstream | Size | Description |
|---|---|---|---|
| WS-01 | Episode generation across three classes | L | Generate benign, explicit and implicit episodes with matched task structure, so class is not inferable from task alone. |
| WS-02 | Conversation-level monitor | M | Monitor over inter-agent messages, established as the baseline the action monitor must improve on. |
| WS-03 | Action-level monitor | L | Monitor over the joint action trace, targeting implicit collusion that carries no explicit message. |
| WS-04 | Separation and false-positive rate | L | Measure separation of harmful from benign with the false-positive rate reported at a fixed operating point. Carries the headline claim. |
| WS-05 | Held-out strategy generalization | M | Evaluate against the reserved strategy, which never appears in monitor training. |
| WS-06 | Documentation, presets and figures | M | Operating-characteristic figure, domain presets, and documentation to the standard's floor. |

## Confounds

| Risk | Control |
|---|---|
| Class is inferable from task structure, so monitors detect the scenario not the collusion | Task structure is matched across classes and a task-only classifier is reported as the leakage bound |
| High detection is reported without the false-positive rate on benign cooperation | The benign false-positive rate is reported at a fixed operating point in the same table as detection |
| The held-out strategy resembles a trained one, overstating generalization | Strategy similarity is reported alongside the held-out score |
| Synthetic smoke output is mistaken for a measured result | `is_synthetic` is set at production and survives aggregation; `claim_ok` is false whenever any input was synthetic |
| Pilot n too small to separate a true null from an underpowered test | Report minimum detectable effect beside every interval and run an equivalence test (TOST) before claiming a null (X12) |
| Small open-weight models do not exhibit the phenomenon at all | State a falsification threshold before running; a clean negative with adequate power is a reportable result |

## What would make this credible

- Task structure is matched across the three classes, with a task-only leakage bound reported.
- Both conversation-level and action-level monitors are implemented and compared.
- The benign false-positive rate appears at a fixed operating point beside every detection number.
- The held-out strategy never appears in monitor training, asserted in a test.
- Implicit-collusion episodes carry no explicit coordinating message, asserted by a content check.

## Honesty commitments

Synthetic output is labelled where it is produced and the label survives into the report.
A claim whose gate fails is suppressed and its block reason named, rather than restated
with hedging. No number in this repository is presented as measured unless it came from a
run against real weights, and the run directory carries the seed, the commit and the model
revision that produced it.

## Compute

The pilot runs on an Apple M4 with no CUDA and no API keys. Model forward passes use MPS
where available; the statistics run on CPU and are documented as such.

## Current status

Infrastructure and the domain measurement are implemented and unit tested. No measured
result is reported yet. The design document at [`TECHNICAL.md`](TECHNICAL.md) states the
artifact contract and the open technical decisions; the program plan under
[`programs/`](programs/) carries the workstream detail and acceptance criteria.
