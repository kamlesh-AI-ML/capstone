# 🧠 Model Card: Bayesian Optimization Framework

## 📋 Overview
**Model Name:** Adaptive Bayesian Optimization with Gaussian Process (GP) Surrogate  
**Model Type:** Sequential Decision-Making / Black-Box Optimization  
**Core Objective:** Maximize unknown objective functions under a strict evaluation budget ($N \le 23$ per function).

This model is not a static predictor; it is an **evolving optimization strategy**. It utilizes a probabilistic surrogate to map the objective landscape and an acquisition function to navigate the exploration-exploitation trade-off.

---

## 🛠 Technical Specifications
| Component | Implementation Details |
| :--- | :--- |
| **Surrogate Model** | Gaussian Process Regression (GPR) |
| **Primary Kernel** | **Matern ($v=2.5$)** (chosen for realistic non-smoothness) |
| **Acquisition Functions** | Expected Improvement ($\text{EI}$), Upper Confidence Bound ($\text{UCB}$) |
| **Hyperparameters** | Exploration parameter ($\xi$), Confidence parameter ($\kappa$) |
| **Optimization Loop** | Scipy-based L-BFGS-B for acquisition maximization |

---

## 🎯 Intended Use

### ✅ Ideal Scenarios
* **Expensive Evaluations:** Problems where querying the objective involves high cost, time, or computational resources.
* **Unknown Landscapes:** Functions where gradients are unavailable and the internal structure is a "black box."
* **Hyperparameter Tuning:** Optimizing ML model parameters (e.g., learning rates, dropout).

### ❌ Out-of-Scope
* **High-Dimensional Scaling:** Not recommended for $D > 20$ without dimensionality reduction.
* **Real-time Systems:** Not intended for sub-millisecond decision-making.
* **Supervised Learning:** This is not a regressor for static datasets; it requires iterative feedback.

---

## 📈 Optimization Strategy (Timeline)

The strategy evolved across three distinct phases to manage the 13-round query budget:

### Phase 1: Global Exploration (Rounds 1–4)
* **Goal:** Map the high-level topology of the 8 functions.
* **Method:** High-variance $\text{UCB}$ ($\kappa \approx 2.5$) to prioritize uncertain regions.
* **Specific Logic:** For Function 5 (suspected unimodal), we transitioned early to $\text{PI}$ (Probability of Improvement) to exploit the peak.

### Phase 2: Hybrid Refinement (Rounds 5–10)
* **Goal:** Balance discovery with local optimization.
* **Techniques:**
    * **Clustered GPs:** K-Means clustering of inputs to fit local GPs on promising sub-regions.
    * **Kernel Switching:** Experimented with varying length-scales to capture local volatility.
    * **LLM-Augmented Analysis:** Utilized LLMs to critique query history and suggest candidate coordinates in sparse 8D spaces.

### Phase 3: Pure Exploitation (Rounds 11–13)
* **Goal:** Converge on the maximum.
* **Method:** Low-variance $\text{EI}$ and manual extrapolation.
* **Decision Logic:** Queries were placed strictly within "anchored" high-value regions to squeeze out final improvements.

---

## ⚖️ Decision Surface Interpretation
The model interprets the search space through two primary lenses:
1. **Anchors (High-Value):** Observed peaks pull the GP mean upward, creating "attraction zones."
2. **Repellers (Low-Value):** Poor results reduce uncertainty in those regions but signal the model to steer clear.

---

## ⚠️ Assumptions & Limitations

### Assumptions
* **Smoothness:** We assume the objective function obeys the Matern kernel's prior (i.e., small changes in $x$ result in bounded changes in $y$).
* **Stationarity:** The underlying function does not change over the course of the 13 rounds.

### Limitations
* **Curse of Dimensionality:** Performance in 6D–8D functions is significantly limited by the query budget.
* **Edge Optimism:** The GP naturally exhibits higher uncertainty at the bounds $[0, 1]$, which can trick acquisition functions into "boundary-hugging" behavior.
* **Local Optima:** The model may converge prematurely if the initial 10 points fail to capture the global basin of attraction.

---

## 🛡️ Ethical & Responsible AI
* **Transparency:** All decision heuristics and weekly reflections are documented to ensure reproducibility.
* **Data Privacy:** This framework processes purely numerical objective values; no PII (Personally Identifiable Information) or sensitive data is involved.
* **Bias:** The model is "biased" only by its kernel choice and prior, which are explicitly stated.

---

## 💬 Author’s Reflection
The strength of this model lies in its **probabilistic humility**. Rather than forcing a single curve-fit, it maintains a distribution over possible functions. The transition from automated BO (Phase 1) to "Human-in-the-loop" refinement (Phase 3) was essential to navigate the sparsity of the 8D search space where pure GP models often struggle.
