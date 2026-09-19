# Working theory and architecture

Updated: 2026-09-19. Status: proposed local baseline, not implemented. This is not a claim about Jev's actual internals.

## Baseline A: pretrained transformer with dynamic option readout

Use a small causal transformer, initially Qwen2.5-0.5B or a comparable small base model. Freeze its original parameters and train LoRA adapters plus a pointer head. Use PyTorch and a shared renderer for training and inference.

Pack the state followed by question branches:

```text
state
  question A + option A1 + option A2 + decision marker
  question B + option B1 + option B2 + option B3 + decision marker
```

Each branch attends to the state and earlier tokens within itself, never a sibling branch. State tokens cannot attend to questions. Restart branch positions after the state so a question's position is independent of siblings. Read the decision marker and each option-end marker from the final hidden layer.

For option i, compute `z_i = dot(Wq h_decide, Wk h_option_i) / sqrt(d_head)` and normalize across that question's options. Application code maps the distribution to a category, yes probability, or expected rubric level. There is no autoregressive answer-generation loop.

This arrangement is implemented by Kev [R4, R5]. The source uses two projections to a default 256-dimensional head. Its dense attention mask enforces isolation but does not automatically exploit sparsity for efficient computation. A masked packed sequence can still allocate memory quadratic in total sequence length.

## Shared primitive hypothesis

Decision (2026-09-19): start with one backbone and one dynamic option-scoring head for Choice, Noul, and Score. All three can use `p = softmax(model(state, question, option_descriptions))`.

| API primitive | Internal representation | Output transformation |
| --- | --- | --- |
| Choice | K described alternatives | Return argmax and full distribution |
| Noul | Two alternatives: proposition false / true | Return probability of true |
| Score | K described rubric levels, retaining their ordinal mapping | Return distribution and sum of level index times probability |

The network learns the conditional distribution; application code performs the transformations. Binary softmax is equivalent to a sigmoid on the difference of its two logits, so Noul does not require a separate binary head.

One mixed training set and cross-entropy on correct alternatives suffice as a baseline. This does not mean arbitrary Choice data will teach every binary or rubric task: include examples covering each intended semantic task. Separate datasets, sampling weights, calibration parameters, or auxiliary losses do not imply separate models or heads.

Score has useful extra structure: levels are ordered. Ordinary categorical cross-entropy does not directly penalize a distant wrong level more than a neighboring wrong level given equal target probability. An ordinal auxiliary loss is a later experiment, not an initial requirement. Do not train only the expected score: distributions concentrated on the middle versus split across extremes can share the same mean.

Evidence boundary: Kev has a shared pointer readout [R5]; TypeSafe documents the Noul/Score transformations [R12]. Jev's exact head sharing and training mixture remain unknown. Similar API mathematics supports this implementation choice but does not establish Jev's architecture. A calibrated distribution still requires empirical verification.

## Proposed training recipe

- Initial budget: 256–512 total tokens for a single-question smoke run, microbatch 1, gradient accumulation, short training subset.
- Train LoRA plus the head with cross-entropy on target options. Start with rank 16 and compare a conservative learning rate such as 5e-5 against a small sweep. These are proposals, not established local settings.
- Profile actual peak VRAM before expanding sequences or number of questions. Mixed precision and checkpointing are candidate controls; do not assume an unmodified Kev training command fits.
- Vary option order, instruction wording, and option descriptions. Include hard distractors and explicit none-of-the-above cases where the correct substantive option is both present and absent.
- Separate training, calibration, development, and locked-test partitions before augmentation. Keep variants of the same source record together.
- Fit temperature on the calibration partition only; report raw and calibrated results on both familiar and held-out tasks.

## Hypotheses and alternatives

| ID | Hypothesis / experiment | Evidence that would change our mind |
| --- | --- | --- |
| H1 | Dynamic pointer scoring transfers to new categories | Failure on held-out labels despite good familiar-task accuracy |
| H2 | Shared-state isolated branches give useful speedups | Packed runs slower or more memory hungry at realistic lengths |
| H3 | Conservative adaptation preserves useful pretrained knowledge | Base next-token choice scoring consistently outperforms adapted readout |
| H4 | Isolating individual options reduces order sensitivity | Accuracy loss outweighs stability benefit |
| H5 | Better rule diversity helps unseen compositions | Improvements limited to known rule forms |

Comparisons to run: frozen-base next-token option scoring; a fixed-label encoder baseline; the pointer model; then option-isolated attention. A small transformer trained from scratch is useful for controlled mechanisms, but has a different knowledge budget.

## What Kev teaches us so far

Author-reported experiments show that more familiar-task data can hurt transfer, and lower adaptation learning rates can reduce loss of base capability. Exact option isolation is possible but reduced 4B accuracy in one later study. These are useful hypotheses for our experiments, not universal conclusions [R6].

RLCD remains unspecified. Our supervised loss and optional temperature scaling must not be called a reproduction of RLCD.

## Change policy

Keep this file as the current proposal. Record why a proposal changed, evidence, and rejected alternatives in the [journal](exploration-journal.md). Pin external code before importing it.
