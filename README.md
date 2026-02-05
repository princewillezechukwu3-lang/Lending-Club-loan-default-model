# Honest Credit Systems: A Real-World ML Audit

This project is a deep dive into the "Lending Club (2007–2014)" dataset, moving beyond high-scoring notebook metrics to build a machine learning system that reflects reality.

## 🛠 Project Evolution

### Phase 1: The Integrity Audit & Baseline
I started by establishing a baseline, but instead of a random split, I used an **Out-of-Time (OOT) Split** (Training: pre-2014; Testing: 2014) to simulate real deployment.

* **Leakage Prevention:** Dropped all columns unavailable at loan origination (e.g., payment history, recoveries).
* **The Honesty Check:** Discovered the initial model was dangerously underconfident (Brier Score: 0.2120), predicting ~20% risk when reality was ~70%.
* **Probability Calibration:** Applied Isotonic Regression, which corrected the "lie" and brought the **Brier Score down to 0.0763**.
* **Temporal Analysis:** Analyzed performance across quarterly chunks, proving that while volume increased, the model's "honesty" held steady.

### Phase 2: Exposing the "Crutch" (Ablation Study)
I ran an ablation study to see which features were actually driving the model.
* **The Discovery:** The model was almost entirely dependent on `int_rate`. 
* **The Verdict:** Including `int_rate` meant the model wasn't learning borrower risk—it was just reverse-engineering Lending Club's internal pricing model. All other borrower features were essentially "noise" in the presence of that one dominant variable.

### Phase 3: The Pure Risk Model (Current)
I am currently rebuilding the system to be a **Pure Risk Model**, intentionally excluding `int_rate` and `grade`. The goal is to see if we can provide a "second opinion" to the bank based strictly on raw borrower behavior:
* **Interaction Features:** Engineering new signals like Installment-to-Income and Debt-per-Account.
* **Robustness:** Focusing on a diversified feature set that doesn't collapse if one data source is lost.

## 📈 Success Metrics
* **Brier Score:** Primary metric for "Honesty" (Calibration).
* **PR-AUC:** Evaluation for imbalanced classes (Defaults).
* **Temporal Stability:** Tracking metric decay over time (Concept Drift).

---
*If the final model performs worse than a naive baseline but is honest, that’s still a win — because it reflects reality.*
