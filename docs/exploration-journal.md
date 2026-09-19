# Exploration journal

Append dated entries. Distinguish local observations, author-reported results, hypotheses, and planned experiments. Preserve negative results and superseded conclusions.

## 2026-09-19 — E000: research and hardware inventory

**Question:** What Jev-like behavior is worth replicating, and what can this machine support?

**Actions completed:** Reviewed TypeSafe's announcement and documentation; inspected Kev documentation, source, model cards, public repository metadata, and experiment log. Queried local GPU memory. Created the four research documents.

**Local observation:** `nvidia-smi --query-gpu=name,memory.total --format=csv,noheader` returned `NVIDIA GeForce RTX 4050 Laptop GPU, 6141 MiB`.

**No local model runs:** No weights downloaded, training performed, Jev API queried, or Kev benchmarks reproduced. No runtime estimate for this laptop is established.

### Kev provenance

- Author: Jared Palmer; repository credits Devin assistance.
- Repository created: 2026-09-17T20:49:39Z (GitHub metadata).
- Earliest commit returned in the 93-entry history: `d0e2b1fc4f9e410137b6b4ab7f7153fc52868a16`, authored 2026-09-17T20:45:44Z.
- HEAD reported during review: `29d71c78368657b3a522729a01c748ea15272abc`.
- Original model card dates the 0.5B training run to 2026-09-17.
- Documentation was read from moving `main`; pin and recheck before attempting reproduction.

### Author-reported performance snapshot

Current README comparison: common v4 development suites. These are not our measurements. Out-of-domain refers to exclusion from Kev fine-tuning; base-model pretraining exposure and Jev training exposure are unknown.

| Model | Familiar-source accuracy | Transfer accuracy |
| --- | ---: | ---: |
| Kev 0.5B | 71.2% | 57.5% |
| Kev 0.6B preview | 80.5% | 59.8% |
| Kev 4B preview | 84.3% | 75.9% |
| Kev 8B preview | 86.9% | 77.4% |
| Hosted Jev reference | 84.5% | 85.7% |

Source: [Kev README](https://github.com/jaredpalmer/kev#comparison-with-jev). Keep this table distinct from the original 0.5B evaluation and the previews' locked tests.

Original 0.5B model card: 79.9% aggregate accuracy on 1,350 questions drawn from held-out splits of the six training sources. Reported training: 9,000 records, two epochs, about 1h45m on Apple M5 with 32 GB unified memory. Those timings do not predict RTX 4050 performance.

The 4B card reports 85.2% familiar-source and 79.4% transfer accuracy on a separate locked test. Jev's table result above is development-only, so do not compare those numbers directly.

### Findings to carry forward

- A functioning typed-decision interface is achievable using a standard pretrained transformer plus a small readout.
- Good familiar-task performance does not establish general workflow competence.
- The 4B card reports 62% both-correct accuracy on held-out policy pairs, below its 70% release criterion, and raw transfer ECE 0.096. The preview is explicitly not a versioned release.
- Its concrete failure example assigns only 0.22 to a billing-problem question in a ticket mentioning two charges. Larger backbones do not guarantee better answers on every prompt.
- Readme and model cards contain differing option-order metrics and counts. Do not combine their values without tracing the exact suite, subset, and raw result. The original model card also has historical statements superseded by newer evaluations.
- A locked-test score exceeding development does not by itself prove the absence of selection overfitting. Maintain our own independent evaluation discipline.

**Decision:** Begin with a small pretrained decision model and preserve an unadapted baseline. Investigate readout, attention, and dataset diversity before inventing an RL objective.

**Next planned experiment (not run):** Pin a Kev revision; inspect dependencies and data conversion; profile a small CUDA forward/backward pass; compare frozen-base option scoring with a trainable pointer head on a fixed subset.

## 2026-09-19 — E001: clarify the behavioral target

**Completed:** Expanded the replication target with current product naming, supported inputs, primitive semantics, parallel-question behavior, and example capability families. Reviewed the official model catalog and primitive pages [R11, R12].

**Clarifications:** Current catalog lists one version, Jev 1.13, with two aliases pointing to it. Inputs are text-only. Choice gives per-option probabilities and a separate answer-level confidence statistic; Noul has no separate confidence. Score is a probability-weighted rubric index. Questions need not describe executable actions.

**Decision:** Define a small behavioral example set before settling architecture. Existing architecture proposal remains provisional. No model or API experiment run.

## 2026-09-19 — E002: unify primitive implementation

**Question:** Do Choice, Noul, and Score need separate models or training?

**Decision:** Propose one dynamic option head and mixed supervised training. Treat binary probability and expected rubric level as output transformations. Keep ordinal losses and task-specific calibration as optional later comparisons. Distinguish task coverage in training from architectural head sharing.

**Evidence:** Reviewed Kev model source and TypeSafe Noul/Score definitions [R5, R12]. Jev's internal sharing is still unknown. No experiment run.

## Entry template

```text
Date / experiment ID:
Question and hypothesis:
Status: planned / running / completed / failed
Code commit and configuration:
Model, tokenizer, dataset revisions and split hashes:
Hardware, precision, seed, sequence lengths, batch settings:
Change versus baseline:
Metrics (include uncertainty where relevant):
Peak VRAM, wall time, inference timing protocol:
Artifacts / exact commands:
Unexpected behavior and failure examples:
Interpretation and limitations:
Decision and next experiment:
```
