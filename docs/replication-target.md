# Replication target

Build a small, local model that takes text and questions with predefined options, and returns useful probabilities quickly and accurately.

## Inputs

- Shared text to evaluate.
- One or more questions about that text.
- A set of possible answers for each question. Option descriptions are optional.

Questions and options are supplied with each request; changing them should not require retraining. Input is text-only for this project.

## Core behavior

- Answer multiple questions in parallel against the same text.
- Evaluate each question independently of the other questions.
- Return a probability for every option, with probabilities summing to one within each question.
- Include every supplied option in the output, including options with zero probability.

## Output

For each question, return the entire probability distribution: every supplied option paired with its probability. Do not select a winner or omit lower-probability options.

This distribution is the only output in scope. Any selection, score, or confidence summary can be derived by the caller.

## Reference results

Published results reviewed 2026-09-19; not reproduced locally.

| Model | Familiar-task accuracy | Transfer accuracy | Transfer Brier score (lower is better) |
| --- | ---: | ---: | ---: |
| Kev 0.5B | 71.2% | 57.5% | — |
| Kev 0.6B | 80.5% | 59.8% | 0.521 |
| Kev 4B | 84.3% | 75.9% | 0.346 |
| Kev 8B | 86.9% | 77.4% | 0.339 |
| Jev | 84.5% | 85.7% | 0.211 |

These are Kev's author-reported v4 development-suite results. Transfer sources/structures were held out from Kev fine-tuning; Jev's exposure is unknown. Accuracy measures the highest-probability answer for evaluation only; our output remains the full distribution. Brier measures probability error, not calibration alone. [Kev results](https://github.com/jaredpalmer/kev#comparison-with-jev)

**Speed references:** TypeSafe reports 70–500 ms end-to-end for hosted Jev. Kev reports roughly 160 ms for 0.5B serving on an Apple M5. Different hardware and workloads make these reference points, not a direct speed comparison or a prediction for our laptop. [Jev report](https://typesafe.ai/blog/introducing-system-one-models-and-jev), [Kev report](https://github.com/jaredpalmer/kev#download-the-weights)

## Goals for this version

Proposed targets, not measured results:

| Area | Goal |
| --- | --- |
| Complete output | Return every option and its probability for every question; no selected answer or additional summaries |
| Valid probabilities | All probabilities finite and between 0 and 1; sum within 0.00001 of 1 per question |
| Accuracy | Aim for 80% familiar-task and 60% transfer accuracy if evaluated on the same Kev v4 development suites; establish separate targets if our evaluation differs |
| Probability quality | Match or improve Kev 0.6B's transfer Brier score of 0.521 on the same suite; also inspect calibration separately on familiar and unseen tasks |
| Latency | Warm median at most 300 ms and p95 at most 1 second for short requests on the RTX 4050; include text processing and returning probabilities |
| Initial workload | 1–3 questions, 2–5 options each, up to 512 total input tokens including questions and options |
| Resources | Fit inference within 6 GB dedicated GPU memory |
| Consistency | Adding or reordering sibling questions changes corresponding probabilities by at most 0.0001; measure option-order sensitivity separately |

Latency goals apply to the initial workload; larger accuracy benchmarks are evaluated separately without silently truncating them to fit. Measure with the model already loaded and report startup separately. Freeze the evaluation before tuning and reserve a separate test set. No local performance is established yet.

[Reference register](references.md)
