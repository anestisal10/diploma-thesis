# 🎓 Thesis Results Tables — Qwen3.5-0.8B

This document contains the consolidated numeric tables compiled from the raw experiment logs, replacing low-density or flat visualizations.

## Table 1a: Task Accuracy under Quantization
This table lists exact-match task accuracies under uniform and mixed quantization, compared with the unquantized baseline.

| Task | Clean Baseline | Uniform | Random Mixed | Mixed (EAP-IG) | Mixed (LRP) | Mixed (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| Arithmetic | **1.000** | 0.000 | 0.000 | - | - | 0.000 |
| Ioi | **0.905** | 0.893 | 0.905 | - | - | 0.899 |
| Tool Selection | **0.760** | 0.710 | 0.730 | - | - | 0.730 |

## Table 1b: Task Accuracy under Pruning
This table consolidates exact-match task accuracies at varying sparsities for different pruning strategies.

| Task | Sparsity | Magnitude | Locked (EAP-IG) | Locked (LRP) | Locked (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Arithmetic | 20% | 0.140 | 0.590 | - | 0.590 |
| Arithmetic | 40% | 0.060 | 0.050 | - | 0.590 |
| Arithmetic | 60% | 0.000 | 0.030 | - | 0.130 |
| Arithmetic | 80% | 0.000 | 0.000 | - | 0.310 |
|--- | --- | --- | --- | --- | --- |
| Ioi | 20% | 0.000 | 0.607 | - | 0.833 |
| Ioi | 40% | 0.000 | 0.060 | - | 0.714 |
| Ioi | 60% | 0.000 | 0.000 | - | 0.405 |
| Ioi | 80% | 0.000 | 0.000 | - | 0.131 |
|--- | --- | --- | --- | --- | --- |
| Tool Selection | 20% | 0.790 | 0.860 | - | - |
| Tool Selection | 40% | 0.500 | 0.550 | - | - |
| Tool Selection | 60% | 0.370 | 0.280 | - | - |
| Tool Selection | 80% | 0.280 | 0.330 | - | - |
|--- | --- | --- | --- | --- | --- |

## Table 2: Circuit Structural Disagreement (EAP-IG vs. LRP)
This table lists the structural overlap at the top-10 components, replacing `01_circuit_disagreement_jaccard.pdf`.

| Task | Attention Heads Jaccard | MLP Neurons Jaccard | Shared Attention Heads |
| :--- | :---: | :---: | :--- |
| Arithmetic | 0.000 | 0.000 | None |
| Ioi | 0.000 | 0.000 | L11_H2 |
| Tool Selection | 0.000 | 0.000 | L11_H2 |

## Table 3a: Pruning Output Distribution Shift (KL Divergence)
This table monitors the KL divergence between the unpruned baseline and the pruned model.

| Task | Sparsity | Magnitude | Locked (EAP-IG) | Locked (LRP) | Locked (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Arithmetic | 20% | 1.920 | 1.426 | - | 0.731 |
| Arithmetic | 40% | 4.514 | 3.155 | - | 1.959 |
| Arithmetic | 60% | 6.076 | 4.364 | - | 2.834 |
| Arithmetic | 80% | 5.230 | 3.230 | - | 2.684 |
|--- | --- | --- | --- | --- | --- |
| Ioi | 20% | 5.616 | 0.855 | - | 0.542 |
| Ioi | 40% | 5.272 | 2.536 | - | 0.964 |
| Ioi | 60% | 7.155 | 4.087 | - | 1.740 |
| Ioi | 80% | 9.017 | 4.981 | - | 3.206 |
|--- | --- | --- | --- | --- | --- |
| Tool Selection | 20% | 0.000 | 0.000 | - | - |
| Tool Selection | 40% | 0.000 | 0.000 | - | - |
| Tool Selection | 60% | 0.000 | 0.000 | - | - |
| Tool Selection | 80% | 0.000 | 0.000 | - | - |
|--- | --- | --- | --- | --- | --- |

## Table 3b: Pruning Language Capability Impact (Perplexity)
This table tracks model perplexity under pruning (baseline unpruned perplexity is shown in parentheses next to the task name).

| Task (Baseline Ppl) | Sparsity | Magnitude | Locked (EAP-IG) | Locked (LRP) | Locked (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Arithmetic (22.8) | 20% | 427.1 | 350.6 | - | 137.1 |
| Arithmetic (22.8) | 40% | 1.5K | 698.2 | - | 400.2 |
| Arithmetic (22.8) | 60% | 3.7K | 1.6K | - | 661.5 |
| Arithmetic (22.8) | 80% | 12.9K | 4.6K | - | 1.5K |
|--- | --- | --- | --- | --- | --- |
| Ioi (22.8) | 20% | 427.1 | 168.5 | - | 142.6 |
| Ioi (22.8) | 40% | 1.5K | 294.5 | - | 319.3 |
| Ioi (22.8) | 60% | 3.7K | 523.9 | - | 534.4 |
| Ioi (22.8) | 80% | 12.9K | 669.1 | - | 659.4 |
|--- | --- | --- | --- | --- | --- |
| Tool Selection (22.8) | 20% | 427.1 | 170.0 | - | - |
| Tool Selection (22.8) | 40% | 1.5K | 381.6 | - | - |
| Tool Selection (22.8) | 60% | 3.7K | 760.8 | - | - |
| Tool Selection (22.8) | 80% | 12.9K | 1.1K | - | - |
|--- | --- | --- | --- | --- | --- |

## Table 4: Quantization Performance Metrics Matrix
This matrix compares task accuracy, faithfulness, and perplexity across quantization strategies.

| Task / Metric | Uniform | Random Mixed | Mixed (EAP-IG) | Mixed (LRP) | Mixed (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Arithmetic** (Base Ppl: 22.8) | | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp; - Accuracy | 0.000 | 0.000 | - | - | 0.000 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Faithfulness | 0.533 | 1.067 | - | - | 1.050 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Perplexity | 22.9 | 22.9 | - | - | 22.8 |
|--- | --- | --- | --- | --- | --- |
| **Ioi** (Base Ppl: 22.8) | | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp; - Accuracy | 0.893 | 0.905 | - | - | 0.899 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Faithfulness | 1.002 | 1.001 | - | - | 1.000 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Perplexity | 22.9 | 22.8 | - | - | 22.9 |
|--- | --- | --- | --- | --- | --- |
| **Tool Selection** (Base Ppl: 22.8) | | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp; - Accuracy | 0.710 | 0.730 | - | - | 0.730 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Faithfulness | 1.049 | 1.148 | - | - | 1.136 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Perplexity | 22.9 | 22.8 | - | - | 22.7 |
|--- | --- | --- | --- | --- | --- |

## Table 5: Discovered Circuit Stability under Quantization (Jaccard Similarity)
This table summarizes how much weight quantization shifts the discovered circuits, replacing `07_quantized_circuit_stability.pdf`.

| Task | Method | Attention Jaccard (INT8) | Attention Jaccard (INT4) | MLP Jaccard (INT8) | MLP Jaccard (INT4) |
| :--- | :--- | :---: | :---: | :---: | :---: |
| Arithmetic | EAP-IG | 1.000 | 0.548 | 1.000 | 0.909 |
| Arithmetic | LRP | 0.227 | 0.213 | 0.769 | 0.714 |
|--- | --- | --- | --- | --- | --- |
| Ioi | EAP-IG | 1.000 | - | 1.000 | - |
| Ioi | LRP | 0.227 | 0.151 | 0.909 | 0.667 |
|--- | --- | --- | --- | --- | --- |
| Tool Selection | EAP-IG | 0.909 | 0.292 | 1.000 | 0.833 |
| Tool Selection | LRP | 0.103 | 0.008 | 0.714 | 0.625 |
|--- | --- | --- | --- | --- | --- |

## Table 6: Cross-Scale Circuit Transfer Performance (270m → 1B / 2B)
This table summarizes transfer pruning and transfer quantization results, replacing `08_transfer_comparison.pdf`.

### Part A: Transfer Pruning (Fails)
| Task | Strategy | Sparsity | Accuracy | KL Divergence | Perplexity |
| :--- | :--- | :---: | :---: | :---: | :---: |
| Arithmetic | Magnitude | 20% | 0.000 | 3.785 | 245.5 |
| Arithmetic | Circuit Locked | 20% | 0.000 | 6.868 | 177.9 |
| Arithmetic | Magnitude | 40% | 0.000 | 7.413 | 4.0K |
| Arithmetic | Circuit Locked | 40% | 0.000 | 8.179 | 1.8K |
| Arithmetic | Magnitude | 60% | 0.000 | 8.189 | 11.4K |
| Arithmetic | Circuit Locked | 60% | 0.000 | 8.918 | 8.1K |
| Arithmetic | Magnitude | 80% | 0.000 | 7.029 | 70.4K |
| Arithmetic | Circuit Locked | 80% | 0.000 | 9.076 | 266.5K |
|--- | --- | --- | --- | --- | --- |
| Ioi | Magnitude | 20% | 0.488 | 2.196 | 245.5 |
| Ioi | Circuit Locked | 20% | 0.298 | 2.117 | 221.6 |
| Ioi | Magnitude | 40% | 0.000 | 6.216 | 4.0K |
| Ioi | Circuit Locked | 40% | 0.000 | 8.326 | 4.9K |
| Ioi | Magnitude | 60% | 0.000 | 8.666 | 11.4K |
| Ioi | Circuit Locked | 60% | 0.000 | 10.867 | 12.3K |
| Ioi | Magnitude | 80% | 0.000 | 11.173 | 70.4K |
| Ioi | Circuit Locked | 80% | 0.000 | 11.855 | 22.4K |
|--- | --- | --- | --- | --- | --- |
| Tool Selection | Magnitude | 20% | 0.610 | - | 245.5 |
| Tool Selection | Circuit Locked | 20% | 0.310 | - | 161.5 |
| Tool Selection | Magnitude | 40% | 0.360 | - | 4.0K |
| Tool Selection | Circuit Locked | 40% | 0.450 | - | 3.4K |
| Tool Selection | Magnitude | 60% | 0.400 | - | 11.4K |
| Tool Selection | Circuit Locked | 60% | 0.310 | - | 6.4K |
| Tool Selection | Magnitude | 80% | 0.310 | - | 70.4K |
| Tool Selection | Circuit Locked | 80% | 0.310 | - | 73.6K |
|--- | --- | --- | --- | --- | --- |

### Part B: Transfer Quantization (Succeeds)
| Task | Strategy | Accuracy | KL Divergence | Base Perplexity | Quantized Perplexity |
| :--- | :--- | :---: | :---: | :---: | :---: |
| Arithmetic | Uniform | 1.000 | 0.001 | 15.8 | 15.9 |
| Arithmetic | Random Mixed | 1.000 | 0.001 | 15.8 | 15.9 |
| Arithmetic | Mixed | 1.000 | 0.001 | 15.8 | 15.9 |
|--- | --- | --- | --- | --- | --- |
| Ioi | Uniform | 0.940 | 0.002 | 15.8 | 15.9 |
| Ioi | Random Mixed | 0.940 | 0.002 | 15.8 | 15.9 |
| Ioi | Mixed | 0.940 | 0.001 | 15.8 | 15.9 |
|--- | --- | --- | --- | --- | --- |
| Tool Selection | Uniform | 0.860 | - | 15.8 | 15.9 |
| Tool Selection | Random Mixed | 0.850 | - | 15.8 | 15.9 |
| Tool Selection | Mixed | 0.850 | - | 15.8 | 15.9 |
|--- | --- | --- | --- | --- | --- |

## Table 7: Attention Head Attribution vs. Weight Geometry Correlations
This table lists the Spearman correlation ($ho$) and Pearson correlation ($r$) between attribution scores and geometric properties, replacing `plot11_attribution_geometry_corr.png`.

| Weight-Space Property | Spearman's $\rho$ | Pearson's $r$ | p-value | Significance |
| :--- | :---: | :---: | :---: | :--- |
| Effective Rank | *Pending* | *Pending* | *Pending* | Weight analysis not run for Qwen |
| Spectral Norm | *Pending* | *Pending* | *Pending* | Weight analysis not run for Qwen |
| Projectivity | *Pending* | *Pending* | *Pending* | Weight analysis not run for Qwen |
| Top-1 Concentration | *Pending* | *Pending* | *Pending* | Weight analysis not run for Qwen |
