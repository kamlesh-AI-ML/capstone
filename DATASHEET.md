# Datasheet: BBO Capstone Dataset

## 🎯 Motivation
This dataset was developed to benchmark **Black-Box Optimization (BBO)** performance under a strictly constrained evaluation budget. 

Unlike standard machine learning datasets, the goal here isn't to train a predictive model for deployment. Instead, it serves as a record for studying **sequential decision-making under uncertainty**, specifically utilizing Bayesian Optimization to navigate unknown objective landscapes.

---

## 🏗️ Dataset Composition
The data comprises observations from **eight independent black-box functions**. Each function represents a unique optimization challenge with varying complexity.

### Technical Specifications
| Feature | Details |
| :--- | :--- |
| **Input Space ($x$)** | Continuous-valued vectors, bounded within $[0, 1]$. |
| **Dimensionality** | Ranges from **2D to 8D** across the eight functions. |
| **Output ($y$)** | A single scalar response representing the objective value. |
| **Characteristics** | Likely **noisy**, **non-smooth**, and non-convex. |

### Data Files
* **`initial_inputs.npy`**: 10 baseline points per function used to seed the models.
* **`initial_outputs.npy`**: Corresponding responses for the seed points.
* **Weekly Folders**: Incremental updates tracking the chronological evolution of the optimization strategy.

---

## 🔄 Collection & Growth
Data was generated through an iterative "proposal-evaluation" loop via the **Imperial College BBO Platform**.

1.  **Proposal**: An optimization algorithm suggests a new coordinate ($x$).
2.  **Evaluation**: The point is evaluated by the external black-box function.
3.  **Expansion**: The resulting ($y$) value is appended to the dataset, increasing coverage by one point per function per week.

> [!IMPORTANT]
> **Known Gaps:** We have zero visibility into the internal function structures, gradients, or the true evaluation metrics. Coverage in the 6D–8D functions is intentionally sparse due to the "curse of dimensionality" and our limited query budget.

---

## 🛠️ Preprocessing
To maintain the integrity of the black-box challenge, the data remains in its **raw numeric state**:
* **No Feature Engineering:** Inputs are kept in their original $[0, 1]$ scale.
* **Model-Level Handling:** Noise estimation and scaling are handled internally by the Gaussian Process (GP) models, not via manual preprocessing.

---

## 🚦 Usage Guidelines

### Intended Uses
* Demonstrating Bayesian Optimization workflows.
* Analyzing exploration–exploitation trade-offs in high-dimensional spaces.
* Portfolio projects focused on probabilistic modeling.

### Inappropriate Uses
* **Supervised Learning:** Not intended for standard regression benchmarks.
* **Ground Truth:** Outputs should not be treated as absolute "truth" but as noisy observations.
* **Production:** Do not use for live systems without extensive external validation.

---

## ⚠️ Limitations
* **Budget Constraints:** The dataset is intentionally small, reflecting real-world high-cost evaluation scenarios.
* **No Optimality Guarantee:** There is no certainty that the global optimum has been reached or even approached.
* **Sparsity:** Data density decreases significantly as dimensionality increases.

---

## 📄 Terms & Attribution
* **License:** Educational and demonstrative use only.
* **Ethical Note:** This dataset contains no personal, sensitive, or identifiable information.
* **Maintenance:** Updated weekly by the project author to reflect the current state of the optimization campaign.

---

## Ethical Considerations
The dataset contains **no personal, sensitive, or identifiable information**.
