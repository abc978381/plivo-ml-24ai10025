# NOTES

The baseline GPT model was used as the starting point, and all modifications were evaluated using the provided BPB metric while preserving the evaluation interface.  
The first successful improvement replaced Adam with AdamW and introduced weight decay (0.01) together with gradient clipping (1.0), resulting in a measurable reduction in BPB.  
A warmup followed by cosine learning-rate scheduling was evaluated but produced worse validation performance under the fixed 2000-step training budget and was therefore discarded.  
Weight tying was enabled to improve parameter efficiency, allowing the embedding dimension to be increased while remaining below the 2 million parameter limit.  
The final model utilizes approximately 1.85M parameters, significantly improving utilization of the available parameter budget over the baseline.  
GPT-style parameter initialization was incorporated into the final model to provide a more suitable initialization for Transformer training.  
The final submission corresponds to the best-performing checkpoint obtained during experimentation and maintains full compatibility with the provided evaluation pipeline.  
All experiments were documented individually in `RUNLOG.md`, with hypotheses, implementation details, observations, and conclusions for each run.  
Potential future improvements include a byte-level BPE/SentencePiece tokenizer with UTF-8 byte fallback, larger context lengths, alternative learning-rate schedules (OneCycleLR, polynomial decay, tuned cosine), GPT-2 style residual projection initialization, hyperparameter tuning, and architectural enhancements such as RoPE, RMSNorm, SwiGLU, or Flash Attention while maintaining the evaluation interface and parameter budget.