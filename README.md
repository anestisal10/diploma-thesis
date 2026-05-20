# Comments

Comments for each result file.

---

## gemma3/ — Gemma-3-270m-it (primary model, fully complete)

### gemma3/findings.md
Full numerical findings and interpretation for all Gemma3 experiments: circuit faithfulness tables, pruning outcomes, quantization results, transfer results, geometric analysis, and open questions. Primary reference document for the thesis results chapter.

---

### gemma3/visualizations/ — PDFs

**00_accuracy_breakdown_All.pdf**
Grouped bar chart of task accuracy (clean baseline, corrupted baseline, ablated circuit) across all three tasks combined. Gives a single-glance overview of how much each task's circuit recovers the model's output.

**00_accuracy_breakdown_arithmetic.pdf**
Same accuracy breakdown for the arithmetic task only. Shows the gap between EAP-IG and LRP circuits at each keep ratio.

**00_accuracy_breakdown_ioi.pdf**
Accuracy breakdown for IOI. IOI yields the cleanest recovery — EAP-IG faithfulness near 0.93 at keep=0.95, matching literature benchmarks.

**00_accuracy_breakdown_tool_selection.pdf**
Accuracy breakdown for tool selection. EAP-IG leads LRP by the widest margin here (0.787 vs 0.453 at keep=0.80).

**01_circuit_disagreement_jaccard.pdf**
Jaccard similarity between EAP-IG and LRP circuits per task, split by component type (attention heads vs MLP layers). Jaccard ~0.0 on MLP and ~0.05 on attention — the two methods find nearly disjoint circuits.

**03_pruning_arithmetic_kl_divergence.pdf**
KL divergence vs. sparsity for arithmetic pruning. All strategies (magnitude, circuit-locked EAP-IG/LRP/union) show KL 5–14 even at sparsity 0.2 — pruning catastrophically disrupts the output distribution.

**03_pruning_arithmetic_log_perplexity.pdf**
Log-scale perplexity vs. sparsity for arithmetic pruning. Union circuit is the least bad (perplexity ~7,000 at sparsity 0.4) vs single-method circuits (~40M–119M). All are catastrophic.

**03_pruning_ioi_kl_divergence.pdf**
KL divergence vs. sparsity for IOI pruning. Similar collapse as arithmetic — no strategy survives meaningful sparsity.

**03_pruning_ioi_log_perplexity.pdf**
Log-scale perplexity for IOI pruning. Confirms the same failure mode as arithmetic.

**03_pruning_tool_selection_kl_divergence.pdf**
KL divergence for tool selection pruning. Negative faithfulness values are a measurement artifact (pruned output below corrupted baseline), not meaningful signal.

**03_pruning_tool_selection_log_perplexity.pdf**
Log-scale perplexity for tool selection pruning. Perplexity reaches billions to trillions — the model has completely collapsed.

**04_quantization_faithfulness.pdf**
Faithfulness (normalized logit difference) for all three quantization strategies (uniform INT8, mixed-precision, random mixed) across all tasks. Circuit-guided mixed is marginally best. All strategies achieve faithfulness ~0.9–1.01 for IOI and tool selection.

**05_quantization_perplexity.pdf**
Perplexity before vs. after quantization per strategy and task. Perplexity increase is modest (3–7%) confirming INT8 is nearly lossless. The arithmetic accuracy collapse to 0.0 despite near-unchanged perplexity is a known anomaly (likely evaluation bug).

**06_faithfulness_vs_keep_ratio.pdf**
Faithfulness curves vs. keep ratio (0.80 / 0.90 / 0.95) for both EAP-IG and LRP, across all tasks. The primary circuit quality plot — shows EAP-IG dominates at low keep ratios.

**07_quantized_circuit_stability.pdf**
Jaccard similarity of discovered circuits before vs. after INT8/INT4 quantization. EAP-IG circuits are stable under INT8 (Jaccard ~1.0); LRP circuits shift substantially (Jaccard 0.05–0.33). INT4 disrupts both.

**08_transfer_comparison.pdf**
Cross-scale transfer results: pruning and quantization strategies applied to Gemma-3-1B using circuits transferred from the 270m model. Transfer pruning fails (KL 12–23); transfer quantization succeeds (accuracy 0.95 for arithmetic, 0.86 for IOI, faithfulness ~1.0).

**09_manifold_geometry_arithmetic.pdf**
Summary manifold analysis for arithmetic: intrinsic dimensionality, effective rank, and ripple amplitude across layers (L0/L5/L11/L17) under three quantization conditions (none/INT8/INT4). The final-layer ripple disappears under INT4.

---

### gemma3/visualizations/geometric/ — PNGs (weight-space + activation-space geometry)

**plot1_pca_scree.png**
PCA scree plot of residual stream activations at each sampled layer. Shows how many principal components are needed to explain 90% of variance — effective rank stays low (1–31 dimensions) across layers.

**plot2_effective_rank_evolution.png**
Effective rank of the residual stream plotted across all 18 layers for the three quantization conditions. Rank grows through the network and drops sharply under INT4 at the later layers.

**plot3_isomap_embedding.png**
3D Isomap embedding of residual stream activations at L17, colored by arithmetic answer value. Reveals the low-dimensional manifold geometry the model uses to represent numerical outputs.

**plot4_principal_curve.png**
Hastie-Stuetzle principal curve fitted through the Isomap embedding. Shows the 1D skeleton of the manifold and discrete curvature at each control point.

**plot5_ripple_profiles.png**
Pairwise cosine similarity of activations binned by label distance (|answer_i − answer_j|). The ripple signal at L17 (oscillation above a monotone-decay baseline) is visible for full precision and INT8 but absent for INT4.

**plot6_ripple_layer_heatmap.png**
Heatmap of ripple amplitude across all sampled layers and quantization conditions. Confirms the ripple is concentrated at L17 and two orders of magnitude larger than at earlier layers.

**plot7_sv_spectra.png**
Singular value spectra of W_OV = W_O @ W_V for a sample of circuit attention heads. Spectra are broad and well-distributed — circuit heads are not low-rank operators.

**plot8_effective_rank_violin.png**
Violin plot of effective rank distribution across all 72 circuit attention heads. Mean effective rank ~210/256 (82% of maximum), confirming full-rank structure.

**plot9_write_direction_heatmap.png**
72×72 pairwise cosine similarity matrix of principal write directions (top-1 left singular vector of W_OV per head), hierarchically clustered. Reveals clusters of heads with similar write directions, loosely organized by task.

**plot10_subspace_overlap.png**
Principal angles between the k-dimensional write-subspaces of each task's circuit heads (IOI vs Arithmetic vs Tool Selection). Near-zero Grassmann distance — likely an artifact of the union circuit sharing the same heads across tasks.

**plot11_attribution_geometry_corr.png**
Spearman and Pearson correlation between EAP-IG+LRP attribution scores and geometric properties (effective rank, spectral norm, projectivity, isotropy, condition number). Only effective rank is marginally significant (r=0.237, p=0.045); all others are uncorrelated.

---

## qwen3.5/ — Qwen3.5-0.8B (comparison model, partially complete)

Qwen3.5-0.8B is a hybrid architecture: 24 layers (18 GatedDeltaNet linear-attention + 6 standard softmax attention). Only EAP-IG circuit discovery and evaluation for IOI and Arithmetic have been run. All other experiments (LRP, pruning, quantization, geometric analysis, tool selection) are pending — the corresponding plots below exist but may be empty or contain only partial data.

### qwen3.5/visualizations/ — PDFs

**00_accuracy_breakdown_All.pdf**
Accuracy breakdown across tasks. Only IOI and Arithmetic have real data; tool selection circuit is pending.

**00_accuracy_breakdown_arithmetic.pdf**
Arithmetic circuit accuracy by keep ratio. Strong result: full accuracy (100%) recovered at keep=0.95; brittle cliff at keep=0.80 (36% accuracy despite faithfulness 0.897).

**00_accuracy_breakdown_ioi.pdf**
IOI circuit accuracy. Near-unity faithfulness at all keep ratios; full accuracy (90.5%) recovered at keep=0.90. IOI circuit is concentrated in the 6 standard self-attention layers.

**00_accuracy_breakdown_tool_selection.pdf**
Tool selection circuit accuracy — pending data (circuit discovery not yet run for this task).

**01_circuit_disagreement_jaccard.pdf**
EAP-IG vs LRP Jaccard similarity — pending (LRP discovery not yet run for Qwen3.5).

**03_pruning_arithmetic_kl_divergence.pdf**
Arithmetic pruning KL divergence — pending.

**03_pruning_arithmetic_log_perplexity.pdf**
Arithmetic pruning perplexity — pending.

**03_pruning_ioi_kl_divergence.pdf**
IOI pruning KL divergence — pending.

**03_pruning_ioi_log_perplexity.pdf**
IOI pruning perplexity — pending.

**03_pruning_tool_selection_kl_divergence.pdf**
Tool selection pruning KL divergence — pending.

**03_pruning_tool_selection_log_perplexity.pdf**
Tool selection pruning perplexity — pending.

**04_quantization_faithfulness.pdf**
Quantization faithfulness by strategy — pending.

**05_quantization_perplexity.pdf**
Quantization perplexity by strategy — pending.

**06_faithfulness_vs_keep_ratio.pdf**
Faithfulness vs. keep ratio for EAP-IG circuits (IOI + Arithmetic only). Primary completed result: IOI faithfulness ~1.0 at all keep ratios; Arithmetic needs keep=0.90+ for high accuracy.

**07_quantized_circuit_stability.pdf**
Circuit stability under INT8/INT4 quantization — pending.

**08_transfer_comparison.pdf**
Cross-scale transfer results — pending.

**09_manifold_geometry_arithmetic.pdf**
Manifold geometry analysis — pending.

---

### qwen3.5/visualizations/geometric/ — PNGs

All 11 geometric plots (plot1 through plot11) are pending — the geometric analysis experiment has not yet been run for Qwen3.5. Files may be empty or contain placeholder output.

**plot1_pca_scree.png** — pending
**plot2_effective_rank_evolution.png** — pending
**plot3_isomap_embedding.png** — pending
**plot4_principal_curve.png** — pending
**plot5_ripple_profiles.png** — pending
**plot6_ripple_layer_heatmap.png** — pending
**plot7_sv_spectra.png** — pending
**plot8_effective_rank_violin.png** — pending
**plot9_write_direction_heatmap.png** — pending
**plot10_subspace_overlap.png** — pending
**plot11_attribution_geometry_corr.png** — pending
