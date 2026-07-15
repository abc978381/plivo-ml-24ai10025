## Run 0 – Baseline

### Objective
Establish the baseline performance of the provided starter implementation before making any modifications.

### Hypothesis
The baseline trainer is intentionally conservative and leaves room for improvement in optimization strategy, parameter utilization, and architecture. This run serves as the reference point for all subsequent experiments.

### Configuration
- Model: Starter GPT architecture
- Tokenizer: Raw UTF-8 byte tokenizer (vocab size = 256)
- Optimizer: Adam
- Learning Rate: 3e-4 (constant)
- Weight Decay: None
- Gradient Clipping: None
- Learning Rate Scheduler: None
- Weight Tying: Disabled
- Parameters: 1,339,840
- Training Steps: 2,000

### Results

| Metric | Value |
|--------|------:|
| BPB | **2.3718** |
| Parameters | **1,339,840** |
| Training Steps | **2000** |
| Tokens in Evaluation | **159225** |
| Tokens Scored | **159224** |

### Observations
- Training remained stable throughout all 2000 optimization steps.
- Loss decreased steadily without numerical instability.
- The model uses only ~67% of the allowed parameter budget (2M parameter cap).
- The optimizer and learning-rate policy are intentionally simple, providing several opportunities for improvement.

### Conclusion
This run establishes the baseline against which all future experiments will be compared.

### Evaluation output
{"bpb": 2.3718, "n_params": 1339840, "steps": 2000, "tokens_in_eval": 159225, "tokens_scored": 159224}


## Run 1 – AdamW + Weight Decay + Gradient Clipping

### Hypothesis
Replacing Adam with AdamW while introducing weight decay and gradient clipping should improve optimization stability and generalization, leading to a lower Bits-Per-Byte score.

### Changes
- Optimizer: Adam → AdamW
- Weight Decay: 0.01
- Gradient Clipping: 1.0
- All other settings unchanged.

### Results

| Metric | Baseline | Run 1 |
|--------|---------:|------:|
| BPB | 2.3718 | **2.3531** |
| Final Training Loss | 1.7315 | **1.7141** |

### Observations
- Training remained stable throughout.
- Training loss decreased slightly compared to the baseline.
- Evaluation BPB improved by **0.0187**, indicating better generalization without increasing model size.

### Conclusion
The hypothesis is supported. AdamW with weight decay and gradient clipping provides a measurable improvement over the baseline and will be retained for subsequent experiments.

## Evaluation Output
{"bpb": 2.3531, "n_params": 1339840, "steps": 2000, "tokens_in_eval": 159225, "tokens_scored": 159224}

