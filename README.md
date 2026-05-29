## Term Definitions

### Evaluation Tasks
*   **IOI (Indirect Object Identification)**: A linguistic task used to evaluate a model's ability to identify the indirect object in a sentence (e.g., predicting "Mary" in "John gave a book to Mary").
*   **Arithmetic**: A task evaluating basic addition and subtraction of integers. 
*   **Tool Selection (Function Calling)**: An agentic task where the model must predict the correct tool name token (e.g., `<tool_call> {"name": "calculator"}`) when provided with a user query and a list of available functions/tools.

### Attribution & Circuit Discovery
*   **EAP-IG (Edge Attribution Patching with Integrated Gradients)**: An interpretability method that identifies task-critical networks by calculating path-level gradients between clean and corrupted inputs, measuring information flow along *edges* (connections) rather than just nodes.
*   **LRP (Layer-wise Relevance Propagation)**: An attribution framework that propagates the model's final output logit backward through the layers. We implement **LRP-$\epsilon$** to enforce mathematical conservation of relevance across linear projections.
*   **Jaccard Similarity**: A statistical metric used to measure structural overlap between two sets (e.g., EAP-IG vs. LRP circuits, or FP16 vs. quantized circuits), calculated as the size of their intersection divided by the size of their union.
*   **Faithfulness (Normalized)**: A metric evaluating how well a task circuit preserves model behavior when isolated. It is scaled such that $1.0$ represents the performance of the full model ($b$) and $0.0$ represents the performance under complete corruption ($b'$).

### Compression Strategies
*   **Sparsity**: The proportion of parameters or components in a neural network that have been deactivated (ablated or zero-masked).
*   **Magnitude Pruning**: A weight-space compression baseline that prunes attention heads and MLP neurons based on the lowest $L_2$ norm of their weights.
*   **Circuit-Locked Pruning (Proposed)**: Our interpretability-guided pruning strategy that protects the task-critical circuit ($0\%$ pruned) and concentrates all pruning target requirements onto the remaining non-circuit components.
*   **Quantization (INT8/INT4)**: The process of reducing the numerical precision of weights and activations from 16-bit floating-point (FP16/BF16) to 8-bit or 4-bit integers.
*   **Mixed-Precision (Circuit-Guided) Quantization**: A strategy that protects the task-critical circuit in FP16/BF16 precision while quantizing the rest of the model's weights to INT8/INT4.
*   **Cross-Scale Circuit Transfer**: A technique that maps a circuit discovered on a small model (270M) to a larger target model (1B/2B) using depth-normalized layer and head mapping, bypassing expensive discovery runs on larger scales.

### Model Architecture & Performance
*   **MLP (Multi-Layer Perceptron) Layers**: The feed-forward layers within a transformer block that process token features independently, mapping representations to a higher-dimensional space and back.
*   **KL Divergence (Kullback-Leibler Divergence)**: A measure of how much the output probability distribution of a compressed model deviates from the reference distribution of the original uncompressed model.
*   **Perplexity**: A metric indicating how well a language model predicts a sequence. Lower perplexity corresponds to better preservation of general language generation capabilities.
