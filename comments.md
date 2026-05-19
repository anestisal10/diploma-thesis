# Thesis Update — Supervisor Meeting 2026-05-20

**Thesis:** Mechanistic Interpretability Guided Compression of Small Language Models
**Student:** Anestis Alexiadis
**Models under study:** `google/gemma-3-270m-it` (primary) · `Qwen/Qwen3.5-0.8B` (comparison)
**Tasks:** Indirect Object Identification (IOI) · Arithmetic · Tool Selection

---

## Status at a Glance

| Phase | Gemma3-270m | Qwen3.5-0.8B |
|---|---|---|
| EAP-IG circuit discovery | Complete (3 tasks, multi-seed) | Complete (IOI + Arithmetic) |
| LRP circuit discovery | Complete (3 tasks, multi-seed) | Pending |
| Circuit evaluation (faithfulness) | Complete | Partial (IOI + Arithmetic) |
| Pruning experiments | Complete | Pending |
| Quantization experiments | Complete | Pending |
| Cross-scale transfer | Complete (to Gemma3-1B) | Pending |
| Geometric / manifold analysis | Complete | Pending |
| Thesis writing (ch1-ch2) | Drafted | — |

---

## Part 1 — Gemma3-270m: Full Results

### 1.1 Circuit Discovery: EAP-IG vs LRP

**Visualizations:** `gemma3/visualizations/00_accuracy_breakdown_*.pdf` · `01_circuit_disagreement_jaccard.pdf` · `06_faithfulness_vs_keep_ratio.pdf`

EAP-IG produces consistently more faithful circuits than LRP across all three tasks and keep ratios. At the most practically interesting sparsity (keep=0.80-0.90), EAP-IG leads by a wide margin:

| Task | EAP-IG (k=0.90) | LRP (k=0.90) |
|---|---|---|
| IOI | 0.843 | 0.707 |
| Arithmetic | 0.678 | 0.619 |
| Tool Selection | 0.856 | 0.519 |

**Interpretation:** EAP-IG assigns importance scores that better concentrate the truly critical edges near the top of the ranking. When we keep only 80-90% of edges, we retain more of the network's actual computation. LRP scores are noisier — they encode a mix of attribution magnitude and gradient, which misses some high-impact edges that EAP-IG catches. IOI yields the cleanest circuits by both methods, consistent with IOI being a benchmark designed to have a well-localised circuit.

The two methods find **nearly disjoint sets of components** (Jaccard ~0.0 on MLP; 0.05 on attention for IOI and tool selection). EAP-IG and LRP are not two noisy routes to the same answer — they find different components. Whether this reflects genuine non-uniqueness of circuits, task-specific attribution biases, or methodological sensitivity is an open question.

---

### 1.2 Effect of Quantization on Circuit Identity

**Visualization:** `gemma3/visualizations/07_quantized_circuit_stability.pdf`

| Method | INT8 stability (attn Jaccard) | INT4 stability (attn Jaccard) |
|---|---|---|
| EAP-IG | ~0.94-1.00 | ~0.18-0.67 |
| LRP | ~0.05-0.33 | ~0.11-0.46 |

**Interpretation:** EAP-IG circuits are highly robust to INT8 quantization — the circuit is essentially identical before and after 8-bit weight rounding. LRP circuits shift substantially even under mild perturbations. This reinforces the faithfulness gap: LRP is sensitive to exact weight magnitudes, making it less suitable as a foundation for compression-aware interpretability. INT4 disrupts both methods, with attention heads shifting more than MLP nodes.

---

### 1.3 Pruning

**Visualizations:** `gemma3/visualizations/03_pruning_*_kl_divergence.pdf` · `03_pruning_*_log_perplexity.pdf`

**Result: pruning fails entirely at all tested sparsities (0.2-0.8) for all strategies.**

Perplexity jumps to thousands-hundreds of millions even at 20% sparsity. KL divergence reaches 5-33. The union-circuit-locked strategy is consistently the least bad (perplexity ~7,000 vs ~40M-119M for single-method circuits at sparsity=0.4 on arithmetic), but "least bad" still means catastrophic degradation.

**Interpretation:** Gemma-3-270m is a small, dense model with very little redundancy. Every weight matters. Standard magnitude-based unstructured pruning is fundamentally unsuitable for this model class, regardless of whether circuit nodes are protected. The finding that the union circuit produces far better outcomes than either single-method circuit (despite EAP-IG and LRP finding disjoint components) suggests both methods identify functionally important but complementary regions — protecting only one set still destroys critical pathways.

Tool selection shows negative faithfulness under pruning (a measurement artifact: pruned model output falls below the corrupted baseline), not a meaningful signal.

**Conclusion:** Unstructured pruning is not viable for models at this scale and density. Structured pruning, distillation, or lottery-ticket approaches would need to be explored in future work.

---

### 1.4 Quantization

**Visualizations:** `gemma3/visualizations/04_quantization_faithfulness.pdf` · `05_quantization_perplexity.pdf`

**Result: quantization is highly successful.**

| Task | Faithfulness (mixed) | Perplexity increase | Task accuracy |
|---|---|---|---|
| IOI | ~1.001 | +3% | 87% (maintained) |
| Tool Selection | ~1.001 | +7% | 80% (maintained) |
| Arithmetic | ~1.012 | +1% | 0.0 (anomalous — see below) |

**Interpretation:** INT8 uniform and mixed-precision quantization preserves model behavior almost perfectly for IOI and tool selection. Circuit-guided mixed strategy achieves marginally better faithfulness than uniform INT8, though the practical difference is small.

The arithmetic task accuracy collapse to 0.0 under all quantization conditions is suspicious — INT8 barely affects perplexity but accuracy drops to zero. The transfer experiment (Section 1.5) provides strong evidence this is a **measurement bug** in the quantization evaluation script, not a genuine sensitivity.

---

### 1.5 Cross-Scale Transfer (270m to Gemma3-1B)

**Visualization:** `gemma3/visualizations/08_transfer_comparison.pdf`

Transfer pruning fails — the circuit topology is not scale-portable and the 1B model is destroyed. Transfer quantization succeeds remarkably:

| Task | Accuracy on 1B | KL divergence | Faithfulness |
|---|---|---|---|
| Arithmetic | 0.95-0.96 | 0.012-0.045 | ~0.994 |
| IOI | 0.86-0.87 | 0.002-0.010 | ~1.001 |

The arithmetic accuracy of 0.95 on the 1B model (vs 0.0 in direct 270m quantization) is the key diagnostic: this strongly indicates the 0.0 accuracy in Section 1.4 is an evaluation bug, not a genuine quantization effect.

**Interpretation:** Circuit masks from small models can guide quantization of larger models in the same family without re-running expensive attribution analysis. Depth-normalized layer mapping is a sufficient transfer heuristic for the Gemma-3 family. Pruning transfer fails because the circuit topology is not preserved across architecturally different scales.

---

### 1.6 Geometric Analysis of Circuit Heads

**Visualizations:** `gemma3/visualizations/geometric/plot1_pca_scree.png` through `plot11_attribution_geometry_corr.png`
Also: `gemma3/visualizations/09_manifold_geometry_arithmetic.pdf`

**Weight-space geometry (plots 7-11):**

72 circuit attention heads analyzed via OV-circuit SVD. Mean effective rank ~210/256 (82% of maximum) — circuit heads are not low-rank operators. This means rank-reduction (e.g., LoRA-style) would not preserve circuit behavior.

The write-direction heatmap (plot9) shows clustering of attention heads by task with some shared heads across tasks. Attribution-geometry correlation (plot11): only effective rank shows a marginally significant Spearman correlation with attribution importance (r=0.237, p=0.045). Weight-space geometry is largely a poor predictor of functional importance.

**Activation-space manifold (plots 1-6):**

Residual stream activations for arithmetic at layers L0, L5, L11, L17 lie on a remarkably low-dimensional manifold (intrinsic dimensionality ~4-8, despite 2048-dimensional ambient space).

A **ripple signature appears at the final layer (L17)** under full precision and INT8, disappearing under INT4. Ripple amplitude is two orders of magnitude larger at L17 than earlier layers — potentially consistent with oscillatory structure at the final numerical computation stage. INT4 substantially disrupts manifold geometry: effective rank at L17 drops from 31 to 11. INT8 preserves geometry almost exactly.

---

## Part 2 — Qwen3.5-0.8B: Preliminary Results

Qwen3.5-0.8B is a hybrid architecture (24 layers: 18 GatedDeltaNet linear-attention + 6 standard softmax attention, every 4th layer). This required significant infrastructure adaptation. The pipeline is now fully ported and verified.

### 2.1 EAP-IG Circuits (IOI + Arithmetic)

**IOI** (clean logit-diff = 9.06, clean accuracy = 90.5%):

| keep_ratio | faithfulness | accuracy |
|---|---|---|
| 0.80 | 1.001 | 77.4% |
| 0.90 | 0.984 | 90.5% (= full model) |
| 0.95 | 0.993 | 85.7% |

**Arithmetic** (clean logit-diff = 7.29, clean accuracy = 100%):

| keep_ratio | faithfulness | accuracy |
|---|---|---|
| 0.80 | 0.897 | 36% |
| 0.90 | 0.904 | 95% |
| 0.95 | 0.936 | 100% (= full model) |

**Interpretation:** Strong preliminary results. IOI achieves near-unity faithfulness at keep=0.80 with full accuracy recovery at keep=0.90. The IOI circuit is concentrated in the 6 full softmax-attention layers (top heads at L23, L19), confirming the DeltaNet layers play a secondary role for this task. Arithmetic shows a brittle accuracy cliff at keep=0.80 — multi-step distributed computation needs a larger retained fraction for correct outputs.

**Caveat:** All Qwen3.5 results are single-seed. Statistical claims require at least 3 seeds. LRP, pruning, quantization, and geometric analysis are all pending.

---

## Part 3 — Discussion Points for Meeting

### Established Findings (Gemma3)

1. **EAP-IG > LRP** for faithfulness and stability under quantization.
2. **INT8 quantization is essentially lossless** for IOI and tool selection.
3. **Unstructured pruning fails completely** for small dense models — this is a negative result but a rigorous one.
4. **Cross-scale transfer quantization works** (270m circuit guides 1B quantization) — a potentially publishable positive result.
5. **Circuit heads are full-rank operators** — weight geometry alone is not a proxy for functional importance.
6. **The residual stream lies on a ~5-dimensional manifold** with a ripple signal at the final layer for arithmetic.

### Open Questions

- What is the correct interpretation of EAP-IG and LRP finding disjoint circuits?
- The near-zero Grassmann subspace distance across tasks needs to be rerun on task-specific (non-union) components.
- The arithmetic accuracy collapse under direct quantization — is it an evaluation bug or real sensitivity?
- The manifold ripple at L17 needs replication on IOI and tool selection.
- Qwen3.5 comparison is incomplete — LRP and compression experiments still pending.

### Next Steps

1. Debug arithmetic quantization accuracy measurement in `run_quantization_experiment.py`
2. Run multi-seed EAP-IG for Qwen3.5 (IOI + Arithmetic)
3. Run Qwen3.5 Tool Selection on Colab
4. Begin Qwen3.5 LRP, pruning, and quantization sweeps
5. Draft Chapter 3 (Pruning) and Chapter 4 (Quantization) results sections

---

*Plots referenced above are in `gemma3/visualizations/` and `qwen3.5/visualizations/`. Detailed numerical findings for Gemma3 are in `gemma3/findings.md`.*
