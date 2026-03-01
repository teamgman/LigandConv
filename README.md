# 🧬 LigandConv: Multi-Scale 1D-CNNs for High-Throughput Binding

**MAIS 202 Final Project** | **Team:** Gavin Xiong, Karen Li, William Zheng

---

## 🏗️ Implementation Log

### **📅 Mar 1, 2026: Current Architecture**

We are treating SMILES strings as 1D signals to detect chemical motifs through parallel convolutional kernels. This approach is optimized for high-throughput screening on the **133M row BELKA dataset**.

* **Feature Extraction:** Multi-scale 1D-CNN blocks (kernels: 3, 5, 7). Parallelizing kernel sizes allows the model to capture hierarchical pharmacophores, ranging from local atomic bonds to macro-molecular aromatic structures.
* **Backend:** Native **MPS (Metal Performance Shaders)** integration. The pipeline is tuned to saturate the **24GB Unified Memory bandwidth** of the M4 chip, minimizing CPU-GPU latency.
* **Data Pipeline:** High-performance Parquet I/O. We utilize memory-mapped file access to handle 133M row subsets without overflowing the local heap.
* **Objective Function:** Weighted Binary Cross-Entropy ($1:20$ ratio). This specific weighting compensates for the extreme sparsity of the binder class (0.5% prevalence) in the Leash Bio dataset.

---

### **📅 Feb 18, 2026: Architectural Pivot**

The original proposal for a Siamese Network using InfoNCE loss was archived following a feasibility review.

* **Numerical Stability:** Mathematical analysis of InfoNCE suggested a high risk of **latent space collapse** given the low sample density ($N \approx 5k$) of early-stage BBB datasets.
* **Scale Shift:** Transitioned to the **BELKA (Leash Bio)** schema to prioritize model robustness over complex data engineering.
* **Convergence:** Switched to a supervised 1D-CNN to guarantee gradient stability within the 10-week development cycle.

---

## 📂 Project Structure

* **`src/`**: Model definitions and MPS-accelerated training loops.
* **`data/`**: Configuration for Parquet ingestion and SMILES tokenization.
* **`research/`**:
    * [Phase 1 Proposal: BBB Permeability](https://github.com/teamgman/LigandConv/blob/main/research/Deliverable_1_Project_Proposal.pdf) (Deliverable 1 Archive).


---

## 💻 Environment & Hardware

* **Compute:** M4 Mac (**24GB Unified Memory**)
* **Acceleration:** `torch.device("mps")`
* **Dependencies:** `torch` 2.2+, `rdkit` 2023.09, `pandas` (FastParquet).