# Reference register

Reviewed: 2026-09-19 unless noted. “Used” means used for research/design reasoning, not that code or data has been incorporated or a result reproduced.

## Product and implementation evidence

| ID | Reference | What we use it for | Evidence limits |
| --- | --- | --- | --- |
| R1 | [TypeSafe: Introducing System One Models & Jev](https://typesafe.ai/blog/introducing-system-one-models-and-jev) | Launch, stated parallel-output behavior, RLCD claims | Vendor announcement, not an architecture paper |
| R2 | [TypeSafe introduction](https://docs.typesafe.ai/introduction) and [System One](https://docs.typesafe.ai/concepts/system-one) | State, questions, typed output contract | Public behavior does not uniquely determine internals |
| R3 | [TypeSafe AI primer](https://docs.typesafe.ai/introduction/machine-learning-primer) and [confidence](https://docs.typesafe.ai/confidence) | Separate calibration goals from a distribution-derived confidence statistic | No reproducible RLCD recipe in these pages |
| R4 | [Kev repository](https://github.com/jaredpalmer/kev) | Candidate reference implementation, family evaluation, serving limitations | Author-reported results; moving main; not Jev source |
| R5 | [Kev model source](https://github.com/jaredpalmer/kev/blob/29d71c78368657b3a522729a01c748ea15272abc/kev/model.py) | Attention mask, positions, pointer head, memory implications | Static code review only |
| R6 | [Kev research log](https://github.com/jaredpalmer/kev/blob/29d71c78368657b3a522729a01c748ea15272abc/PLAN.md) | Learning-rate sensitivity, capability loss, rule-coverage and option-isolation experiments | Exploratory studies; conclusions depend on suite and recipe |
| R7 | [Kev original model card](https://github.com/jaredpalmer/kev/blob/29d71c78368657b3a522729a01c748ea15272abc/MODEL_CARD.md) | Historical 0.5B recipe, data conversions, original evaluation | Historical card; newer results supersede some statements |
| R8 | [Kev 4B card](https://github.com/jaredpalmer/kev/blob/29d71c78368657b3a522729a01c748ea15272abc/docs/model-cards/kev-4b.md) | Preview status, failure examples, transfer and locked-test distinction | Some subset counts/metrics differ from README; trace raw artifacts before reuse |
| R9 | [Kev GitHub metadata](https://api.github.com/repos/jaredpalmer/kev) and [first commit](https://github.com/jaredpalmer/kev/commit/d0e2b1fc4f9e410137b6b4ab7f7153fc52868a16) | Creation date and provenance | Repository creation is not the start of all private work |
| R10 | [Qwen2.5-0.5B model card](https://huggingface.co/Qwen/Qwen2.5-0.5B) | Small pretrained backbone candidate and architecture facts | Local training fit has not been measured |
| R11 | [TypeSafe model catalog](https://docs.typesafe.ai/models) | Current version and aliases, text-only input, same weights across accounts | Catalog snapshot dated 2026-09-19; aliases can change |
| R12 | [Choice](https://docs.typesafe.ai/primitives/choice), [Score](https://docs.typesafe.ai/primitives/score), [Noul](https://docs.typesafe.ai/primitives/noul) | Exact meaning of selections, distributions, rubric scores, and binary probabilities | Interface documentation, not independently measured accuracy |

## Papers

| ID | Paper | Intended use | Status |
| --- | --- | --- | --- |
| P1 | [On Calibration of Modern Neural Networks — Guo et al., 2017](https://arxiv.org/abs/1706.04599) | Temperature-scaling baseline and calibration evaluation | Abstract reviewed; method not implemented here |
| P2 | [LoRA: Low-Rank Adaptation of Large Language Models — Hu et al., 2021](https://arxiv.org/abs/2106.09685) | Adaptation with a small trainable parameter budget | Abstract reviewed; implementation not selected |

## Candidate data

| ID | Source | Proposed use | Current status |
| --- | --- | --- | --- |
| D1 | [PolyAI Banking77](https://huggingface.co/datasets/PolyAI/banking77) | Fine-grained dynamic category choices | Card reviewed; no data downloaded; Kev uses a modern-format mirror |
| D2 | [CLINC150 / out-of-scope evaluation](https://github.com/clinc/oos-eval) | Unknown-intent and abstention experiments | Documentation reviewed; no data downloaded |
| D3 | [Google GoEmotions](https://github.com/google-research/google-research/tree/master/goemotions) | Multiple binary emotion questions on shared text | Candidate only; conversion and annotation semantics need review |

## Reading queue

- [Archer Hume: Jev's Architecture Unmasked](https://archerhume.com/posts/jevs-architecture-unmasked): Kev's cited inspiration. Not yet independently reviewed here; treat reverse-engineering conclusions as hypotheses.
- Hydragen, DeFT, and FIRST: cited by Kev as related work. Retrieve and inspect the actual papers before using them to justify design choices.

## Maintenance

Add a source when it changes a design decision, evaluation, or interpretation. Record the specific use, evidence quality, and revision/date. Preserve superseded sources and explain what replaced their claims. Before importing code/data, record its license and immutable revision. Author-reported numbers remain labelled until locally reproduced.
