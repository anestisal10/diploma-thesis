# Comments

Comments for each result file.

---

## gemma3/ — Gemma-3-270m-it (primary model, fully complete)

---

### gemma3/visualizations/ — PDFs


**00_accuracy_breakdown_ioi.pdf**
Accuracy breakdown for IOI. IOI yields the cleanest recovery — EAP-IG faithfulness near 0.93 at keep=0.95.

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
KL divergence for tool selection pruning.

**03_pruning_tool_selection_log_perplexity.pdf**
Log-scale perplexity for tool selection pruning. Perplexity reaches billions to trillions — the model's ability for simple language generation has completely collapsed.

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

