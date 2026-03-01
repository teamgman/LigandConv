# 🧬 LigandConv: Multi-Scale 1D-CNNs for High-Throughput Protein Binding Prediction

### **MAIS 202 Final Project — AI for Drug Discovery**

**Team:** Gavin Xiong, Karen Li, William Zheng

---

## 🏗️ Technical Development Log

### **📅 Feb 18, 2026: Architectural Pivot & Research Spike**

* **Original Plan:** Dual-view Siamese Network (SMILES 1D-CNN + Fingerprint MLP) using InfoNCE loss.
* **Status:** `Archived` (Pre-implementation Research)

**Decision Rationale:**

* **Technical Direction:** Following a design review with our TPM, the team pivoted from unsupervised Contrastive Learning to a supervised pipeline. This ensured focus on the core binding classification task and avoided the "cold-start" problem of training a dual-encoder on unlabelled data.
* **Data Scale vs. Pipeline Latency:** Preprocessing 100k+ molecules for the original plan was projected to exceed our dev timeline. By switching to the **BELKA (Leash Bio)** dataset, we gained access to 133M standardized rows, allowing us to focus on **high-throughput modeling** rather than data engineering.
* **Hardware Efficiency (M4 Optimization):** While the **24GB Unified Memory on the M4** provides significant headroom, stable Contrastive Learning (InfoNCE) requires extremely high negative sample density ($>512$ batch size). A supervised **1D-CNN** allows for faster gradient convergence and better utilization of the **Metal Performance Shaders (MPS)** backend for sequence processing.

---

### **📅 Mar 1, 2026: 1D-CNN Implementation Strategy**

* **Strategy:** Sequence-to-Binding classification via 1D signal processing.
* **Status:** `Active / Optimization Phase`

The current iteration of **LigandConv** treats SMILES strings as 1D signals. The architecture is designed to detect hierarchical chemical motifs through a series of parallel convolutional kernels.

**Current Technical Focus:**

* **Backend:** Optimization for **Apple Silicon (MPS)** to leverage M4-specific memory bandwidth.
* **Encoder:** High-dimensional embedding layer for SMILES tokenization.
* **Feature Extraction:** Multi-scale 1D-CNN blocks designed to capture varying bond lengths and aromatic structures.
* **Imbalance Handling:** Weighted Binary Cross-Entropy ($1:20$ ratio) to address the sparse binder distribution in the BELKA set.

---

## 💻 Hardware & Environment

* **Platform:** M4 Mac (**24GB Unified Memory**)
* **Compute:** Metal Performance Shaders (MPS)
* **Data Scale:** 133M rows (BELKA Subset)

| Module | Version | Role |
| --- | --- | --- |
| `torch` | 2.2+ | MPS Accelerated Training |
| `rdkit` | 2023.09 | SMILES Tokenization |
| `pandas` | 2.1.0 | Parquet Data Handling |