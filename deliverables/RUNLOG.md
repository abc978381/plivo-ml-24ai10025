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

## Run 2 – Warmup + Cosine Learning Rate Scheduler

### Hypothesis
Introducing a linear warmup followed by cosine learning rate decay would improve convergence within the fixed 2000-step budget by stabilizing early optimization and enabling finer parameter updates towards the end of training.

### Changes
- Added 100-step linear warmup.
- Added cosine annealing schedule reducing the learning rate to 10% of the peak value.
- Retained all Run 1 optimization changes (AdamW, weight decay, gradient clipping).

### Results

| Metric | Run 1 | Run 2 |
|--------|-------:|-------:|
| BPB | **2.3531** | **2.5493** |

### Observations
- Training started considerably slower due to the warmup.
- Although the training process remained stable, the final evaluation BPB degraded significantly.
- Under the strict 2000-step training limit, the scheduler reduced the effective optimization progress.

### Conclusion
The hypothesis was **not supported**. The warmup and cosine schedule negatively impacted evaluation performance within the limited training budget. This modification was reverted, and subsequent experiments continue from the Run 1 configuration.

### Evaluation Output:
{"bpb": 2.5493, "n_params": 1339840, "steps": 2000, "tokens_in_eval": 159225, "tokens_scored": 159224}

## Run 3 – Weight Tying + Increased Model Capacity

### Hypothesis
Weight tying improves parameter efficiency by sharing the input embedding and output projection weights. The resulting parameter savings can be reinvested into increasing model capacity while remaining below the 2M parameter limit, improving language modeling performance.

### Changes
- Enabled weight tying.
- Increased embedding dimension, resulting in a total model size of 1,853,568 parameters.
- Retained Run 1 optimization improvements (AdamW, weight decay = 0.01, gradient clipping = 1.0).

### Results

| Metric | Run 1 | Run 3 |
|--------|-------:|-------:|
| BPB | 2.3531 | **2.3489** |
| Parameters | 1,339,840 | **1,853,568** |
| Final Training Loss | 1.7141 | **1.6851** |

### Observations
- Increasing model capacity produced a lower final training loss.
- Evaluation BPB improved further while remaining within the parameter budget.
- Weight tying allowed better utilization of the available parameter budget without increasing inference complexity.

### Conclusion
The hypothesis is supported. Increasing model capacity together with weight tying produced the best validation performance so far and becomes the new baseline for subsequent experiments.

## Evaluation Output: 
{"bpb": 2.3489, "n_params": 1853568, "steps": 2000, "tokens_in_eval": 159225, "tokens_scored": 159224}

