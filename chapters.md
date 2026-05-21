
#### **Chapter 1: Introduction**
*   **The Problem:** LLMs are powerful but memory/compute-heavy. Standard compression (Uniform INT4, blind $L_2$ pruning) degrades performance because it treats all weights equally, often destroying critical reasoning pathways.
*   **The Solution:** Mechanistic Interpretability allows us to find the "circuits" that matter. We can protect these circuits while aggressively compressing the rest.
*   **Contributions:** (1) Extracting subgraphs via EAP-IG and LRP. (2) Applying *Circuit-Locked Pruning*. (3) Applying *Mixed-Precision TCQ*. (4) Benchmarking against standard uniform baselines.

#### **Chapter 2: Theoretical Background**
*   **2.1 Large Language Models:** Brief overview of Transformer architecture (Attention, MLPs, Residual Stream).
*   **2.2 Mechanistic Interpretability:** Define circuits, features, and subgraphs. 
*   **2.3 Attribution Methods:** Mathematically define **Integrated Gradients (IG)**, **Edge Attribution Patching (EAP)**, and **Layer-wise Relevance Propagation (LRP)**. How do we trace logits back to edges?
*   **2.4 Model Compression:** The theory of Magnitude Pruning ($L_2$ norms) and Post-Training Quantization (PTQ, FP16 vs INT4).

#### **Chapter 3: Related Work**
*   **3.1 Circuit Extraction in LLMs:** Cite papers like *Towards Automated Circuit Discovery for Mechanistic Interpretability* (Conneau et al. / Anthropic).
*   **3.2 LLM Compression:** Cite standard pruning and PTQ papers (e.g., *SparseGPT*, *AWQ*, *Wanda*).
*   **3.3 The Gap:** Point out that while interpretability and compression are heavily researched, using interpretability to *guide* compression is a under-explored field.

#### **Chapter 4: Circuit Extraction & Evaluation (Methodology Part 1)**
*   **4.1 Task Definition:** What specific task are these circuits for? (e.g., Indirect Object Identification, Subject-Verb Agreement, Math reasoning?).
*   **4.2 Subgraph Isolation:** Detail on how I implemented EAP-IG and LRP to find the edges.
*   **4.3 Circuit Quality Metrics:** 
    *   *Faithfulness:* Define KL-Divergence and Logit Difference mathematically.
    *   *Accuracy:* How do we measure the task accuracy for the base model and the circuits.

#### **Chapter 5: Circuit-Aware Compression (Methodology Part 2)**
*   **5.1 Circuit-Locked Pruning:** Explain the algorithm. How do you generate the binary mask that protects the subgraph? Provide the formula for how $L_2$ pruning is applied *only* to the unmasked weights.
*   **5.2 Mixed-Precision Task-Circuit Quantization (TCQ):** Explain the memory management. How do you keep the subgraph at FP16/BF16 while routing the rest of the model through INT4 PTQ? (Maybe discuss some details of the technical implementation).

#### **Chapter 6: Experimental Setup**
*   **6.1 Models Selected:** Which SLMs are you using? (e.g., Phi-3, Qwen-1.5-0.5B, TinyLlama).
*   **6.2 Datasets:** What prompt datasets were used to trigger the task-specific circuits?
*   **6.3 Baselines for Comparison:** Define your baselines clearly:
    *   Baseline 1: Standard $L_2$ Magnitude Pruning (no circuit locking).
    *   Baseline 2: Uniform INT4/INT8 Quantization.
*   **6.4 Hardware & Environment:** GPU, RAM specs etc.

#### **Chapter 7: Results and Comparative Analysis**
*   **7.1 Circuit Extraction Results:** Show that your EAP-IG/LRP methods successfully found faithful, minimal circuits (include visualizations of the graphs/attention heads if possible).
*   **7.2 Pruning results:** Circuit-Locked Pruning vs. Standard $L_2$ Pruning. Show graphs comparing sparsity levels (%) against accuracy/Logit Diff. *Hypothesis to prove: Your method retains accuracy at much higher sparsity levels.*
*   **7.3 Quantization results:** TCQ vs. Uniform INT4. Show the trade-off. Did TCQ maintain near-FP16 accuracy while achieving near-INT4 memory footprints?

#### **Chapter 8: Conclusions and Future Work**
*   Summarize how preserving mechanistic structures allows for "smarter, not harder" compression.
*   **Future Work** 
