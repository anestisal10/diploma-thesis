# 🎓 Thesis Results Tables — Gemma-3-270m-it

This document contains the consolidated numeric tables compiled from the raw experiment logs, replacing low-density or flat visualizations.

## Table 1a: Task Accuracy under Quantization
This table lists exact-match task accuracies under uniform and mixed quantization, compared with the unquantized baseline.

| Task | Clean Baseline | Uniform | Random Mixed | Mixed (EAP-IG) | Mixed (LRP) | Mixed (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Memory Savings** | **0% (0.00 GB)** | **50% (0.27 GB)** | **35% (0.19 GB)** | **35% (0.19 GB)** | **35% (0.19 GB)** | **35% (0.19 GB)** |
| Arithmetic | **0.680** | 0.680 | 0.680 | 0.700 | 0.670 | 0.680 |
| Ioi | **0.863** | 0.863 | 0.860 | 0.863 | 0.853 | 0.874 |
| Tool Selection | **0.790** | 0.790 | 0.803 | 0.800 | 0.790 | 0.800 |

## Table 1b: Task Accuracy under Pruning
This table consolidates exact-match task accuracies at varying sparsities for different pruning strategies.

| Task | Sparsity | Memory Savings | Magnitude | Locked (EAP-IG) | Locked (LRP) | Locked (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| Arithmetic | 20% | 20% (0.11 GB) | 0.000 | 0.000 | 0.000 | 0.070 |
| Arithmetic | 40% | 40% (0.21 GB) | 0.000 | 0.000 | 0.000 | 0.070 |
| Arithmetic | 60% | 60% (0.32 GB) | 0.000 | 0.000 | 0.000 | 0.010 |
| Arithmetic | 80% | 80% (0.43 GB) | 0.000 | 0.000 | 0.000 | 0.020 |
|--- | --- | --- | --- | --- | --- | --- |
| Ioi | 20% | 20% (0.11 GB) | 0.000 | 0.000 | 0.000 | 0.000 |
| Ioi | 40% | 40% (0.21 GB) | 0.000 | 0.000 | 0.000 | 0.000 |
| Ioi | 60% | 60% (0.32 GB) | 0.000 | 0.000 | 0.000 | 0.000 |
| Ioi | 80% | 80% (0.43 GB) | 0.000 | 0.000 | 0.000 | 0.000 |
|--- | --- | --- | --- | --- | --- | --- |
| Tool Selection | 20% | 20% (0.11 GB) | 0.310 | 0.230 | 0.310 | 0.240 |
| Tool Selection | 40% | 40% (0.21 GB) | 0.300 | 0.290 | 0.290 | 0.310 |
| Tool Selection | 60% | 60% (0.32 GB) | 0.280 | 0.280 | 0.310 | 0.320 |
| Tool Selection | 80% | 80% (0.43 GB) | 0.310 | 0.280 | 0.310 | 0.180 |
|--- | --- | --- | --- | --- | --- | --- |

## Table 2: Circuit Structural Disagreement (EAP-IG vs. LRP)
This table lists the structural overlap at the top-10 components, replacing `01_circuit_disagreement_jaccard.pdf`.

| Task | Attention Heads Jaccard | MLP Neurons Jaccard | Shared Attention Heads |
| :--- | :---: | :---: | :--- |
| Arithmetic | 0.000 | 0.000 | None |
| Ioi | 0.053 | 0.000 | L15_H2 |
| Tool Selection | 0.053 | 0.000 | L11_H2 |

## Table 3a: Pruning Output Distribution Shift (KL Divergence)
This table monitors the KL divergence between the unpruned baseline and the pruned model.

| Task | Sparsity | Memory Savings | Magnitude | Locked (EAP-IG) | Locked (LRP) | Locked (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| Arithmetic | 20% | 20% (0.11 GB) | 28.269 | 8.611 | 10.927 | 9.869 |
| Arithmetic | 40% | 40% (0.21 GB) | 12.113 | 14.002 | 16.398 | 5.975 |
| Arithmetic | 60% | 60% (0.32 GB) | 12.653 | 12.032 | 32.316 | 4.964 |
| Arithmetic | 80% | 80% (0.43 GB) | 18.875 | 16.594 | 10.610 | 6.391 |
|--- | --- | --- | --- | --- | --- | --- |
| Ioi | 20% | 20% (0.11 GB) | 24.365 | 13.730 | 9.736 | 5.049 |
| Ioi | 40% | 40% (0.21 GB) | 17.855 | 13.897 | 17.282 | 7.410 |
| Ioi | 60% | 60% (0.32 GB) | 21.262 | 14.127 | 12.171 | 6.807 |
| Ioi | 80% | 80% (0.43 GB) | 20.203 | 12.854 | 33.307 | 8.343 |
|--- | --- | --- | --- | --- | --- | --- |
| Tool Selection | 20% | 20% (0.11 GB) | N/A | N/A | N/A | N/A |
| Tool Selection | 40% | 40% (0.21 GB) | N/A | N/A | N/A | N/A |
| Tool Selection | 60% | 60% (0.32 GB) | N/A | N/A | N/A | N/A |
| Tool Selection | 80% | 80% (0.43 GB) | N/A | N/A | N/A | N/A |
|--- | --- | --- | --- | --- | --- | --- |

## Table 3b: Pruning Language Capability Impact (Perplexity)
This table tracks model perplexity under pruning (baseline unpruned perplexity is shown in parentheses next to the task name).

| Task (Baseline Ppl) | Sparsity | Memory Savings | Magnitude | Locked (EAP-IG) | Locked (LRP) | Locked (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| Arithmetic (201.0) | 20% | 20% (0.11 GB) | 235.2M | 155.2K | 13.2M | 6.0K |
| Arithmetic (201.0) | 40% | 40% (0.21 GB) | 40.4M | 119.2M | 11.8M | 7.1K |
| Arithmetic (201.0) | 60% | 60% (0.32 GB) | 55.1M | 367.4M | 105.2M | 5.3K |
| Arithmetic (201.0) | 80% | 80% (0.43 GB) | 91.4M | 207.4M | 1.8M | 11.4K |
|--- | --- | --- | --- | --- | --- | --- |
| Ioi (201.0) | 20% | 20% (0.11 GB) | 235.2M | 23.7K | 263.3K | 6.1K |
| Ioi (201.0) | 40% | 40% (0.21 GB) | 40.4M | 133.4K | 5.7M | 27.0K |
| Ioi (201.0) | 60% | 60% (0.32 GB) | 55.1M | 96.7K | 124153.5M | 8.2K |
| Ioi (201.0) | 80% | 80% (0.43 GB) | 91.4M | 133.3K | 42766.2M | 25.8K |
|--- | --- | --- | --- | --- | --- | --- |
| Tool Selection (444.3) | 20% | 20% (0.11 GB) | 20580.9M | 21.8M | 5.3M | 5.0M |
| Tool Selection (444.3) | 40% | 40% (0.21 GB) | 616.1M | 462.1M | 84.0M | 903.9M |
| Tool Selection (444.3) | 60% | 60% (0.32 GB) | 101.0M | 1114.8M | 380.3M | 8.2M |
| Tool Selection (444.3) | 80% | 80% (0.43 GB) | 214.5M | 1066.1M | 10450676.7M | 7.6M |
|--- | --- | --- | --- | --- | --- | --- |

## Table 4: Quantization Performance Metrics Matrix
This matrix compares task accuracy, faithfulness, and perplexity across quantization strategies.

| Task / Metric | Uniform | Random Mixed | Mixed (EAP-IG) | Mixed (LRP) | Mixed (UNION) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Memory Savings** | 50% (0.27 GB) | 35% (0.19 GB) | 35% (0.19 GB) | 35% (0.19 GB) | 35% (0.19 GB) |
| **Arithmetic** (Base Ppl: 201.0) | | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp; - Accuracy | 0.680 | 0.680 | 0.700 | 0.670 | 0.680 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Faithfulness | 1.024 | 1.003 | 1.013 | 1.019 | 1.001 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Perplexity | 211.6 | 206.4 | 204.7 | 204.6 | 203.6 |
|--- | --- | --- | --- | --- | --- |
| **Ioi** (Base Ppl: 201.0) | | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp; - Accuracy | 0.863 | 0.860 | 0.863 | 0.853 | 0.874 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Faithfulness | 1.002 | 0.999 | 0.999 | 0.999 | 1.002 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Perplexity | 211.6 | 210.6 | 206.6 | 208.9 | 207.2 |
|--- | --- | --- | --- | --- | --- |
| **Tool Selection** (Base Ppl: 444.3) | | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp; - Accuracy | 0.790 | 0.803 | 0.800 | 0.790 | 0.800 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Faithfulness | 0.898 | 0.986 | 1.002 | 0.979 | 0.925 |
| &nbsp;&nbsp;&nbsp;&nbsp; - Perplexity | 463.4 | 476.0 | 479.5 | 469.3 | 484.6 |
|--- | --- | --- | --- | --- | --- |

## Table 5: Discovered Circuit Stability under Quantization (Jaccard Similarity)
This table summarizes how much weight quantization shifts the discovered circuits, replacing `07_quantized_circuit_stability.pdf`.

| Task | Method | Attention Jaccard (INT8) | Attention Jaccard (INT4) | MLP Jaccard (INT8) | MLP Jaccard (INT4) |
| :--- | :--- | :---: | :---: | :---: | :---: |
| Arithmetic | EAP-IG | 0.968 | 0.270 | 1.000 | 0.833 |
| Arithmetic | LRP | 0.213 | 0.222 | 0.833 | 0.769 |
|--- | --- | --- | --- | --- | --- |
| Ioi | EAP-IG | 1.000 | 0.644 | 1.000 | 0.714 |
| Ioi | LRP | 0.277 | 0.240 | 0.909 | 0.833 |
|--- | --- | --- | --- | --- | --- |
| Tool Selection | EAP-IG | 1.000 | 0.381 | 1.000 | 0.909 |
| Tool Selection | LRP | 0.193 | 0.320 | 0.714 | 0.714 |
|--- | --- | --- | --- | --- | --- |

## Table 6: Cross-Scale Circuit Transfer Performance (270m → 1B / 2B)
This table summarizes transfer pruning and transfer quantization results, replacing `08_transfer_comparison.pdf`.

### Part A: Transfer Pruning (Fails)
| Task | Strategy | Sparsity | Memory Savings | Accuracy | KL Divergence | Perplexity |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| Arithmetic | Magnitude | 40% | 40% (0.21 GB) | 0.040 | 12.079 | 63.4M |
| Arithmetic | Circuit Locked | 40% | 40% (0.21 GB) | 0.000 | 12.078 | 12.4M |
|--- | --- | --- | --- | --- | --- | --- |
| Ioi | Magnitude | 40% | 40% (0.21 GB) | 0.000 | 21.958 | 63.4M |
| Ioi | Circuit Locked | 40% | 40% (0.21 GB) | 0.000 | 22.859 | 23.3M |
|--- | --- | --- | --- | --- | --- | --- |

### Part B: Transfer Quantization (Succeeds)
| Task | Strategy | Memory Savings | Accuracy | KL Divergence | Base Perplexity | Quantized Perplexity |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| Arithmetic | Uniform | 50% (0.27 GB) | 0.950 | 0.045 | 60.4 | 59.7 |
| Arithmetic | Random Mixed | 35% (0.19 GB) | 0.960 | 0.016 | 60.4 | 60.5 |
| Arithmetic | Mixed | 35% (0.19 GB) | 0.950 | 0.012 | 60.4 | 59.7 |
|--- | --- | --- | --- | --- | --- | --- |
| Ioi | Uniform | 50% (0.27 GB) | 0.874 | 0.010 | 60.4 | 59.7 |
| Ioi | Random Mixed | 35% (0.19 GB) | 0.863 | 0.003 | 60.4 | 60.1 |
| Ioi | Mixed | 35% (0.19 GB) | 0.863 | 0.002 | 60.4 | 60.0 |
|--- | --- | --- | --- | --- | --- | --- |
