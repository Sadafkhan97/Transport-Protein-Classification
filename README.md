
This repository presents a Transport protein classification pipeline that combines:

- A custom **xLSTM-based sequence encoder**
- Token-level representation learning
- Sliding-window feature extraction
- Cosine similarity–based feature aggregation
- Class balancing via SMOTE
- A classical **SVM (RBF kernel)** classifier

The model is designed for binary classification of protein sequences:

- Class 0: Primary
- Class 1: Secondary

The system follows a **two-stage learning paradigm**:
1. Deep sequence representation learning (xLSTM)
2. Classical machine learning classification (SVM)

---

## Methodology

### 1. Dataset

Protein sequences are stored in FASTA format and organized as:


ActTransData/
├── train/
│ ├── primary/
│ └── secondary/
└── test/
├── primary/
└── secondary/


Each sequence is parsed by removing FASTA headers and retaining only amino acid strings.

---

### 2. Sequence Representation

#### 2.1 Tokenization

- Character-level vocabulary is constructed from the training corpus.
- Each amino acid is mapped to a unique integer index.
- Sequences are padded to uniform length for batching.

Let:
- \( s = (a_1, a_2, ..., a_T) \)
- \( a_i \in \Sigma \) (amino acid alphabet)

Then:
\[
x_i = \text{Embedding}(a_i)
\]

---

#### 2.2 xLSTM Encoder

A custom **xLSTM architecture** is used for contextual sequence modeling:

- Embedding layer
- xLSTMCell (gated recurrent update)
- Residual xLSTMBlocks
- Layer normalization
- Adaptive pooling

Hidden state update:

\[
c_t = f_t \odot c_{t-1} + i_t \odot g_t
\]
\[
h_t = o_t \odot \tanh(c_t)
\]

Output:
- Token-level embeddings: \( \mathbb{R}^{N \times T \times D} \)

---

### 3. Feature Engineering

#### 3.1 Sliding Window Representation

To capture local structural motifs:

- Window size: 50
- Stride: 1

For each sequence embedding:

\[
W_i = \{x_{t:t+50}\}
\]

Each window is mean-pooled:

\[
\hat{w}_i = \frac{1}{50} \sum_{t=1}^{50} x_t
\]

Output:
\[
\mathbb{R}^{N \times K \times D}
\]

---

#### 3.2 Similarity-Based Feature Construction

Pairwise cosine similarity is computed over windows:

\[
S_{ij} = \frac{w_i \cdot w_j}{\|w_i\| \|w_j\|}
\]

The similarity matrix is:

- Standardized using z-score normalization
- Aggregated via mean pooling

Final representation:

\[
X \in \mathbb{R}^{N \times D}
\]

---

### 4. Class Imbalance Handling

To address dataset imbalance:

- SMOTE (Synthetic Minority Over-sampling Technique) is applied on feature space

\[
X_{res}, y_{res} = \text{SMOTE}(X, y)
\]

Note: SMOTE is applied after feature extraction, not in raw sequence space.

---

### 5. Classification Model

A Support Vector Machine is used:

- Kernel: RBF
- \( C = 1.0 \)
- \( \gamma = 0.1 \)
- Probability estimation enabled

The decision function is:

\[
f(x) = \sum_i \alpha_i K(x_i, x) + b
\]

---

## Evaluation Protocol

### Metrics

The model is evaluated using:

- Accuracy
- Precision
- Recall (Sensitivity)
- Specificity
- F1-score
- Matthews Correlation Coefficient (MCC)
- ROC-AUC

---

### Confusion Matrix

\[
\begin{bmatrix}
TN & FP \\
FN & TP
\end{bmatrix}
\]

From which:

- Sensitivity = \( \frac{TP}{TP + FN} \)
- Specificity = \( \frac{TN}{TN + FP} \)

---

## Results Visualization

The following analyses are included:

- Confusion Matrix heatmap
- ROC curves (class-wise and binary)
- Precision–Recall curves
- Sensitivity–Specificity curves
- Dimensionality reduction:
  - PCA
  - t-SNE
  - UMAP

---

## Experimental Setup

- Framework: PyTorch
- Optimizer: AdamW
- Loss: CrossEntropyLoss
- Batch size: 64
- Epochs: 10
- Device: CUDA (if available)

---

## Output Artifacts


outputs/
├── test_metrics.json
├── confusion_matrix.png
├── roc_curves.png
├── pr_curves.png
├── clustering_visualization.png
├── trained_svm.pkl
├── xLSTM_encoder.pth


---

## Key Technical Contributions

- Custom xLSTM-based protein sequence encoder
- Windowed representation for local motif extraction
- Similarity-driven feature aggregation (unsupervised signal enrichment)
- Hybrid deep + classical ML pipeline
- Comprehensive evaluation framework

---

## Limitations

- Sliding-window representation increases computational complexity
- SMOTE may introduce synthetic artifacts in embedding space
- No joint optimization between xLSTM and SVM stages
- Feature aggregation is heuristic (mean-based)
- Limited biological interpretability of learned embeddings

---

## Future Work

- Replace SVM with learnable neural classification head
- Introduce attention-based pooling instead of mean aggregation
- Replace xLSTM with protein-specific transformers (ESM / ProtBERT)
- End-to-end training pipeline
- Graph-based protein structure modeling
- Contrastive learning for representation refinement

---

## Reproducibility

To reproduce results:

```bash
pip install torch scikit-learn imbalanced-learn matplotlib seaborn umap-learn numpy pandas
```
Run pipeline in order:

Data loading & preprocessing
xLSTM training
Token embedding extraction
Sliding window feature construction
SMOTE balancing
SVM training
Evaluation & visualization
Citation

If you use this work, please cite:

Sadaf Khan. Protein Sequence Classification using xLSTM + Feature Engineering + SVM Pipeline.
License

For academic and research use only.

Author

Sadaf Khan


--- 

If you want next level improvement, I can upgrade this further into:
- **:contentReference[oaicite:0]{index=0}**
- or **:contentReference[oaicite:1]{index=1}**
