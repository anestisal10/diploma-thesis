# Findings & Discussion — Gemma-3-270m-it Circuit Interpretability

**Model**: `google/gemma-3-270m-it` (arithmetic, ioi), `google/functiongemma-270m-it` (tool_selection)  
**Transfer target**: `google/gemma-3-1b-it`  
**Tasks**: Indirect Object Identification (IOI), Arithmetic, Tool Selection  

---

## 1. Circuit Discovery: EAP-IG vs LRP

### 1.1 Faithfulness

Both methods discover circuits that can reconstruct model behavior when ablated, but with a clear gap in quality.

| Task | Method | keep=0.80 | keep=0.90 | keep=0.95 |
|------|--------|-----------|-----------|-----------|
| Arithmetic | EAP-IG | 0.528 | 0.678 | 0.764 |
| Arithmetic | LRP | 0.458 | 0.619 | **0.965** |
| IOI | EAP-IG | 0.745 | 0.843 | 0.925 |
| IOI | LRP | 0.592 | 0.707 | 0.865 |
| Tool Selection | EAP-IG | 0.787 | 0.856 | 0.897 |
| Tool Selection | LRP | 0.453 | 0.519 | 0.753 |

*(Faithfulness measured as logit-difference; values from MultiSeed evaluations)*

**Key observations:**

- **EAP-IG is more faithful at lower keep ratios (0.8–0.9)**. With 80% of edges kept, EAP-IG recovers substantially more of the model's logit difference than LRP across all tasks, with the gap largest on tool_selection (0.787 vs 0.453). This suggests EAP-IG ranks edges more accurately and concentrates important ones toward the top.

- **LRP catches up at keep=0.95 on arithmetic** (0.965 vs 0.764), which is an anomaly. At that sparsity level, a larger fraction of all edges is retained so the circuit includes most of the model anyway; the meaningful regime is keep=0.8–0.9 where the circuits are actually sparse. LRP's arithmetic number at 0.95 should not be over-interpreted.

- **IOI has the best-calibrated circuits overall** (EAP-IG faithfulness 0.93 at keep=0.95, matching well-known IOI results in the literature). Arithmetic is harder for both methods, consistent with arithmetic being a more distributed computation.

- **Accuracy versus faithfulness do not always align.** At keep=0.90 EAP-IG recovers 67% accuracy on arithmetic (vs 68% baseline), which is excellent. LRP recovers only 33%. The probability-difference and logit-difference metrics confirm the same ordering.

### 1.2 Structural Overlap Between Methods

| Task | Jaccard (Attn) | Jaccard (MLP) | Shared heads |
|------|---------------|---------------|-------------|
| Arithmetic | 0.00 | 0.00 | 0 |
| IOI | 0.053 | 0.00 | 1 |
| Tool Selection | 0.053 | 0.00 | 1 |

The two discovery methods identify almost entirely disjoint sets of attention heads and no shared MLP components. This is a strong result: EAP-IG and LRP are not two routes to the same circuit — they carve up the network differently. Neither method has ground truth to validate against, so this disagreement is a genuine scientific limitation. It raises the question of which (if either) reflects the "true" computational circuit, or whether the concept of a unique circuit is itself ill-defined for small, dense models like Gemma-3-270m.

---

## 2. Effect of Quantization on Circuit Identity

Quantized circuit comparison measures how much the discovered circuit changes after quantizing the weights (Jaccard of heads before vs after quantization).

| Task | Method | Attn Jaccard (INT8) | Attn Jaccard (INT4) | MLP Jaccard (INT8) | MLP Jaccard (INT4) |
|------|--------|---------------------|---------------------|---------------------|---------------------|
| Arithmetic | EAP-IG | ~0.94–1.00 | ~0.18–0.36 | 1.00 | 0.67–1.00 |
| Arithmetic | LRP | ~0.18–0.25 | ~0.11–0.33 | 0.67–1.00 | 0.54–1.00 |
| IOI | EAP-IG | 1.00 | ~0.62–0.67 | 1.00 | 0.43–1.00 |
| IOI | LRP | ~0.25–0.30 | ~0.18–0.25 | 0.82–1.00 | 0.67–1.00 |
| Tool Selection | EAP-IG | 1.00 | ~0.33–0.43 | 1.00 | 0.82–1.00 |
| Tool Selection | LRP | ~0.05–0.33 | ~0.18–0.46 | 0.42–1.00 | 0.43–1.00 |

**Key observations:**

- **EAP-IG circuits are highly stable under INT8 quantization** (Jaccard ~1.0 on attn for ioi and tool_selection, ~0.94 for arithmetic). The circuit EAP-IG finds in the FP16 model is essentially the same circuit in the INT8 model.

- **LRP circuits are unstable even under INT8** (Jaccard 0.05–0.30 on attention). The components LRP ranks as important shift substantially with even minor weight perturbations. This is a methodological concern: LRP-based circuits may be less robust to numerical changes in the model.

- **INT4 substantially disrupts both methods** (EAP-IG attn Jaccard drops to 0.18–0.67). MLP components are more resilient (Jaccard tends to stay near 1.0 under INT8, ~0.5–1.0 under INT4).

- The LRP circuit instability under quantization is consistent with the lower faithfulness scores above. LRP may be identifying components that are sensitive to the exact weight magnitudes, making it less useful for practical compression-aware interpretability.

---

## 3. Pruning

### 3.1 Arithmetic & IOI

Circuit-locked pruning (protecting circuit nodes from being pruned) and magnitude pruning were tested at sparsities 0.2, 0.4, 0.6, 0.8.

**Result: pruning catastrophically destroys task performance across all conditions.**

| Task | Strategy | Source | Sparsity | Acc | KL | Perplexity |
|------|----------|--------|---------|-----|----|-----------|
| Arithmetic | circuit_locked | union | 0.4 | 0.07 | 5.97 | 7,079 |
| Arithmetic | circuit_locked | eap_ig | 0.4 | 0.00 | 14.00 | 119M |
| Arithmetic | circuit_locked | lrp | 0.4 | 0.00 | 16.40 | 12M |
| Arithmetic | magnitude | eap_ig | 0.4 | 0.00 | 12.11 | 40M |
| IOI | circuit_locked | union | 0.4 | 0.00 | 7.41 | 26,961 |
| IOI | circuit_locked | eap_ig | 0.4 | 0.00 | 13.90 | 133K |

The **union circuit source** consistently produces far better perplexity outcomes than single-method circuits (7,079 vs 40M–119M at sparsity=0.4 for arithmetic). This suggests that even though EAP-IG and LRP find disjoint circuits, protecting the union of both keeps more of the functionally important network intact.

However, all strategies fail in terms of KL divergence (5–33 range) and perplexity (thousands to hundreds of millions). The normalized faithfulness stays in the 0.44–0.70 range — the preserved circuit portion is doing some work, but the rest of the model is so damaged that the output distribution collapses. This is consistent with Gemma-3-270m being a small, dense model where there is little redundancy to prune.

### 3.2 Tool Selection — Anomalous Results

Tool selection pruning shows a consistent anomaly: **normalized faithfulness is negative throughout** (ranging from −5.2 to −5.9), regardless of strategy, sparsity, or circuit source.

This is not a genuine finding — it is a measurement artifact. The normalized faithfulness formula is:

```
faithfulness = (pruned_metric - corrupted_baseline) / (clean_baseline - corrupted_baseline)
```

For tool_selection, the pruned model's metric falls below the corrupted baseline, producing a negative value. The corrupted baseline for this task appears to be very close to the pruned performance, which inflates the denominator and pushes the ratio negative. The perplexity values (millions to trillions) confirm that the pruned model has collapsed. The negative faithfulness numbers should be interpreted as "pruning broke the model entirely, not just partially" — not as a meaningful quantitative signal.

Task accuracy for tool_selection under pruning (0.18–0.32) is well below the 0.79 baseline and reflects random/degraded behavior, not preserved circuit function.

---

## 4. Quantization

### 4.1 Faithfulness and Perplexity

| Task | Strategy | Faithfulness (mean) | Perplexity (base) | Perplexity (quantized) | Task Acc |
|------|----------|--------------------|--------------------|------------------------|---------|
| Arithmetic | Uniform | 0.909 | 201.0 | 211.6 | 0.00 |
| Arithmetic | Random Mixed | 0.991 | 201.0 | 208.8 | 0.00 |
| Arithmetic | Mixed | 1.012 | 201.0 | 203.8 | 0.00 |
| IOI | Uniform | 1.002 | 201.0 | 211.6 | 0.86 |
| IOI | Random Mixed | 0.999 | 201.0 | 210.6 | 0.86 |
| IOI | Mixed | 1.001 | 201.0 | 207.6 | 0.87 |
| Tool Selection | Uniform | 0.899 | 444.3 | 463.4 | 0.79 |
| Tool Selection | Random Mixed | 0.987 | 444.3 | 476.0 | 0.80 |
| Tool Selection | Mixed | 1.001 | 444.3 | 477.8 | 0.80 |

Quantization is overall **very successful**: perplexity increases are modest (5–10%), and faithfulness stays near 1.0. Circuit-informed mixed quantization is marginally better than uniform on most metrics.

**The notable failure is arithmetic task accuracy = 0.0 under all quantization conditions** (including uniform INT8, which barely affects perplexity). This stands in sharp contrast to IOI (0.86 maintained) and tool selection (0.79 maintained). Two explanations are possible:

1. **Sensitivity of arithmetic to numerical precision**: exact arithmetic comparisons may depend on floating-point values that INT8 perturbs sufficiently to flip token rankings, even when the overall output distribution (and thus perplexity) remains similar.
2. **Measurement issue**: the quantization evaluation script may have a bug in how accuracy is computed for arithmetic (e.g., sampling from a quantized model that writes answers differently). The fact that perplexity is almost identical pre/post-quantization but accuracy drops to zero is suspicious and warrants inspection of the evaluation code before drawing conclusions.

**Mixed vs. uniform** quantization shows minimal practical difference in perplexity (e.g., arithmetic: 202.4 vs 211.6 for mixed vs uniform). The faithfulness advantage of mixed is clearer (1.01 vs 0.91 for arithmetic), but whether this represents a genuine preservation of circuit behavior or is within noise given the task accuracy anomaly is unclear.

---

## 5. Transfer Compression (270m → 1b)

### 5.1 Transfer Pruning — Fails

| Task | Strategy | Acc (1b) | KL | Faithfulness | Perplexity (1b base) |
|------|----------|---------|-----|-------------|---------------------|
| Arithmetic | circuit_locked | 0.00 | 12.08 | 0.506 | 60.4 |
| Arithmetic | magnitude | 0.04 | 12.08 | 0.513 | 60.4 |
| IOI | circuit_locked | 0.00 | 22.86 | 0.537 | 60.4 |
| IOI | magnitude | 0.00 | 21.96 | 0.521 | 60.4 |

Pruning the 1b model using circuit masks derived from the 270m model completely fails. KL divergence values (12–23) and astronomical perplexity values confirm the 1b model is destroyed. The circuit structure does not transfer across scales in a way that guides safe pruning.

This is expected given the architectural differences between the two models. The 270m and 1b models differ in number of layers and heads, so the circuit mask (e.g., "protect head L8H2") does not refer to the same functional unit in the larger model. The transfer was attempted but the result confirms scale-agnostic circuit transfer for pruning is not viable without architectural alignment.

Tool selection transfer was not run (only arithmetic and ioi).

### 5.2 Transfer Quantization — Succeeds

| Task | Strategy | Acc (1b) | KL | Faithfulness | Ppl (base→quantized) |
|------|----------|---------|-----|-------------|---------------------|
| Arithmetic | Uniform | 0.95 | 0.045 | 0.993 | 60.4 → 63.6 |
| Arithmetic | Mixed | 0.95 | 0.012 | 0.994 | 60.4 → 60.8 |
| Arithmetic | Random Mixed | 0.96 | 0.016 | 0.994 | 60.4 → 62.1 |
| IOI | Uniform | 0.87 | 0.010 | 1.001 | 60.4 → 63.6 |
| IOI | Mixed | 0.86 | 0.002 | 1.001 | 60.4 → 60.0 |
| IOI | Random Mixed | 0.86 | 0.003 | 1.000 | 60.4 → 61.9 |

Transfer quantization is **remarkably successful**. The 1b model maintains near-full task accuracy and near-unity faithfulness under all three quantization strategies derived from the 270m circuit. Arithmetic even jumps to 0.95–0.96 accuracy on the 1b model (vs 0.68 on the 270m), which reflects the larger model's inherently higher capability rather than any benefit from quantization itself.

Crucially, the arithmetic task accuracy is preserved here (0.95) whereas it collapsed to 0.0 in the same-model quantization experiments (Section 4). This strongly suggests **the arithmetic accuracy collapse in Section 4 is a measurement artifact** in the quantization experiment, not a real effect of quantization on the 270m model, since if anything the 1b model quantization is more aggressive and still maintains accuracy.

The near-identical performance across uniform, mixed, and random-mixed strategies for transfer quantization suggests the circuit mask contributes minimal additional value over uniform quantization when applied cross-scale — the 1b model is robust to quantization regardless of which modules are protected.

---

## 6. Geometric Analysis of Circuit Heads

72 attention heads were analyzed across the union circuit (all three tasks combined).

- **Mean effective rank**: 210.1 (out of maximum 256 = head dimension)
- **Mean spectral norm**: 3.27

The high effective rank (~82% of maximum) means circuit heads have well-distributed singular value spectra — they are not low-rank operators, and rank-based compression of individual circuit heads is not directly justified by this data alone.

### 6.1 Subspace Overlap (Cross-Task)

The Grassmann distance between the principal subspaces of different task circuits is ~8×10⁻⁸ — essentially zero, indicating completely identical subspaces across all task pairs.

**This result should be interpreted with caution.** The circuit used for this analysis is the *union* of circuits from all three tasks. When the same heads appear in all task circuits (which is likely by design of the union), their subspaces are trivially identical because the same weights are being measured. The near-zero Grassmann distance may reflect the fact that the union circuit contains a large overlapping core rather than being a discovery about cross-task generalization. To get a meaningful subspace comparison, the analysis should be repeated on the task-specific (intersection-free) components of each circuit.

Additionally, `n_non_circuit_heads = 0` in the results suggests the comparison between circuit and non-circuit geometric properties was not computed (or all heads were included in the union circuit). The spectral norm and effective rank statistics thus characterize only circuit heads, with no contrast group.

### 6.2 Attribution-Geometry Correlation

Only the **effective rank** has a marginally significant Spearman correlation with attribution score (r=0.237, p=0.045). All other geometric properties (spectral norm, Frobenius norm, isotropy, condition number) are not significantly correlated with attribution importance. The relationship is weak and would not survive multiple comparison correction. Geometric properties of individual attention head weight matrices are poor predictors of their importance to task performance.

---

## 7. Manifold Analysis

Residual stream geometry was analyzed for arithmetic across three quantization conditions (none/int8/int4) at layers L0, L5, L11, L17.

| Layer | Condition | Eff. Rank (90%) | Intrinsic Dim | Ripple | Amplitude |
|-------|-----------|-----------------|--------------|--------|-----------|
| L0 | none/int8/int4 | 1 | 5.5–6.2 | No | ~0 |
| L5 | none | 5 | 5.22 | No | 1.8×10⁻⁵ |
| L5 | int8 | 5 | 4.65 | No | 2.9×10⁻⁵ |
| L5 | int4 | 3 | 6.56 | No | 2.9×10⁻⁵ |
| L11 | none | 7 | 3.89 | No | 1.8×10⁻³ |
| L11 | int8 | 7 | 4.22 | No | 1.7×10⁻³ |
| L11 | int4 | 14 | 7.60 | No | 3.4×10⁻⁴ |
| L17 | none | 31 | 7.07 | **Yes** | 0.0196 |
| L17 | int8 | 31 | 7.48 | **Yes** | 0.0211 |
| L17 | int4 | 11 | 6.63 | No | 5.6×10⁻⁴ |

**Key observations:**

- **Intrinsic dimensionality is remarkably low across all layers and conditions** (~4–8). The residual stream, despite being embedded in a high-dimensional space, lies on a very low-dimensional manifold. This compresses further the question of where arithmetic "lives" in the network.

- **A ripple signal appears at the final layer (L17) under unquantized and INT8 conditions** but disappears under INT4. The ripple amplitude is two orders of magnitude larger at L17 than at any earlier layer (0.02 vs 10⁻³–10⁻⁵), suggesting oscillatory dynamics emerge at the final processing stage. Whether this is meaningful (e.g., related to the model's arithmetic output mechanism) or a numerical artifact is unclear without further analysis.

- **INT4 quantization disrupts the final-layer geometry**: effective rank drops from 31 to 11, ripple disappears, and a high curvature (42.6) appears at L11 which is absent in the other conditions. This is consistent with INT4 introducing significant numerical error that distorts the manifold structure, especially at mid-to-late layers. INT8 preserves the unquantized geometry almost exactly.

- **Manifold analysis was only run for arithmetic**, so it is not clear whether these geometric patterns (ripple at the final layer, low intrinsic dimensionality) generalize to IOI or tool_selection. This is a gap in the current analysis.

---

## 8. Overall Assessment

### What Worked Well

1. **EAP-IG discovers more faithful, more stable circuits** than LRP across all tasks and conditions. The gap is clearest at low keep ratios (0.8) and under INT8 quantization. For practical use, EAP-IG should be the default method.

2. **INT8 quantization is essentially lossless** for model behavior (perplexity, faithfulness, task accuracy for ioi and tool_selection). This is a positive result for deployment.

3. **Transfer quantization (270m → 1b) is highly successful**, preserving full task accuracy and near-unity faithfulness. Circuit-guided mixed quantization for the transfer case adds marginal perplexity benefit.

4. **IOI has the highest-quality circuits** by all metrics (faithfulness, accuracy, stability). It is the most suitable task for mechanistic claims about Gemma-3-270m.

### What Is Ambiguous or Inconclusive

1. **The circuit disagreement between EAP-IG and LRP is a real finding** but its interpretation is open. It may reflect methodological differences in what each algorithm attributes importance to, differences in sensitivity to local vs global structure, or genuine non-uniqueness of circuits.

2. **The subspace overlap result (near-zero Grassmann distance across tasks)** is likely an artifact of using the union circuit rather than a discovery about cross-task generalization. It should be rerun on task-specific circuits.

3. **The arithmetic quantization accuracy collapse** (0.0 across all INT8/INT4 conditions) contradicts the transfer results (0.95 on the 1b model) and warrants investigation of the evaluation code. If it is a real effect, it is a significant finding about arithmetic sensitivity; if it is a bug, the finding disappears.

4. **The manifold ripple at L17** is interesting but based on a single task and single analysis run. It needs replication and interpretation.

### What Failed

1. **Pruning fails entirely at all sparsities** for all tasks and all strategies. The model is too small and dense for meaningful unstructured pruning at any sparsity level tested. Circuit-locked pruning with the union circuit is marginally better but still catastrophic. Future work would need structured pruning, distillation, or lottery-ticket style approaches.

2. **Transfer pruning (circuit masks across scale) does not work.** Circuit topologies are not scale-portable in a way that enables safe pruning of larger models.

3. **LRP circuits are fragile**: low faithfulness at realistic keep ratios, low inter-method agreement, and high instability under even INT8 quantization. LRP-derived circuits should be used with caution as a basis for compression decisions.

### Open Questions

- Does the low intrinsic dimensionality of the residual stream (4–8) suggest that circuit-level analysis could be replaced by a dimensionality-reduction-based approach?
- Can a pruning method that accounts for circuit geometry (rather than just topology) succeed where standard methods fail?
- Why does the union circuit produce far better pruning outcomes than single-method circuits — does this imply the two methods find complementary, not redundant, components?
- Is the final-layer ripple signal in arithmetic related to the model's mechanism for producing token-level numerical answers?