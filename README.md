# Bayesian Decision Network for Heart Disease Diagnosis

A Python implementation of a Bayesian Decision Network (BDN) applied to the clinical problem of whether to recommend diagnostic testing for heart disease, given observed patient characteristics.

## Background and Attribution

The factor algebra at the core of this project — factor product, marginalization, evidence observation, and Expected Utility Factor calculation — is adapted from programming assignments in the course **Probabilistic Graphical Models** (Daphne Koller, Stanford University / Coursera). The original assignments were written in MATLAB; this project re-implements those algorithms from scratch in Python/NumPy.

Beyond the algorithmic foundation, the following components are original contributions applied to a real-world clinical dataset:

- Empirical CPT estimation from a real-world dataset (Heart Disease UCI, n = 1,025)
- Feature discretization pipeline with clinically motivated bin boundaries
- A custom utility table encoding realistic asymmetric costs of clinical decisions
- DAG visualization using NetworkX
- Patient-specific conditional MEU queries based on observed evidence
- Expected Value of Perfect Information (EVPI) calculation
- Posterior probability heatmaps across joint risk factor combinations

## Problem Statement

A clinician observes patient characteristics (age, cholesterol level, maximum heart rate, exercise-induced angina) and must decide whether to order further diagnostic testing. The decision carries asymmetric consequences: missing a diseased patient is far more costly than unnecessarily testing a healthy one. The goal is to identify the decision that maximises expected utility under uncertainty about the true disease state.

## Network Structure

The influence diagram encodes the following causal assumptions:

```
Age (A) ──► Chol (C) ──►
Age (A) ──► HR (H)   ──► Disease (D) ──► Utility (U)
Age (A) ──► Exang (E)──►                      ▲
                              Decision (S) ────┘
```

| Node | Type | States |
|------|------|--------|
| Age (A) | Random | 3 bins: <40, 40–55, >55 |
| Cholesterol (C) | Random | 3 bins: <200, 200–240, >240 mg/dL |
| Max Heart Rate (H) | Random | 3 bins: <120, 120–160, >160 bpm |
| Exercise-induced Angina (E) | Random | Binary: No / Yes |
| Disease (D) | Random | Binary: No / Yes |
| Decision (S) | Decision | Binary: No Test / Test |
| Utility (U) | Utility | Defined by table below |

### Utility Table

| Disease | Decision | Utility |
|---------|----------|---------|
| No disease | No test | +10 |
| No disease | Test | −20 |
| Disease | No test | −100 |
| Disease | Test | +80 |

## Results

| Quantity | Value |
|----------|-------|
| MEU — unconditional (population prior) | 30.35 |
| Optimal decision — unconditional | **Test** |
| MEU — high-risk evidence profile | 0.04 |
| Optimal decision — conditional | **No Test** |
| Expected Value of Perfect Information (EVPI) | 14.90 |

The unconditional analysis recommends testing as the default policy. However, specific patient evidence can reverse this recommendation — demonstrating that the optimal action is not fixed but evidence-dependent. The EVPI of 14.9 sets an upper bound on the utility gain from acquiring perfect diagnostic information before deciding.

## Dataset

**Heart Disease UCI** via Kaggle (`ketangangal/heart-disease-dataset-uci`)
1,025 patient records with features: age, sex, chest pain type, resting blood pressure, cholesterol, fasting blood sugar, resting ECG, maximum heart rate, exercise-induced angina, ST depression (oldpeak), slope, vessels colored by fluoroscopy, thalassemia, and binary disease target.

## Repository Structure

```
.
├── Decision_Making.ipynb    # Main notebook
└── README.md
```

## Dependencies

```
numpy
pandas
matplotlib
networkx
kagglehub
```

## Usage

Open `Decision_Making.ipynb` in Jupyter or Google Colab and run cells sequentially. The notebook fetches the dataset automatically via `kagglehub`.

## Reference

Koller, D. & Friedman, N. (2009). *Probabilistic Graphical Models: Principles and Techniques*. MIT Press.

Koller, D. (2012). Probabilistic Graphical Models (Course). Stanford University / Coursera.
