# Notebook Architecture - ISYS2047 Assignment 3
## Complete Restructure for Two-Track Modelling Approach

**Date:** 31 May 2026  
**Client:** Crédit Nationale Azur  
**Status:** ✅ RESTRUCTURED & READY FOR EXECUTION

---

## Notebook Structure (9 Total)

### **Foundation Notebooks** (Phases 1-3)
| # | Notebook | Phase | Purpose | Status |
|---|----------|-------|---------|--------|
| 1 | `01_EDA.ipynb` | 2 | Data Understanding & Exploratory Analysis | ✅ Keep as-is |
| 2 | `02_Data_Preparation.ipynb` | 3 | Data Cleaning (produces cleaned_data.pkl) | ✅ Keep as-is |

---

### **TRACK A: SELECTED FEATURES (k=7 using SelectKBest)** 
*Pipeline: Split → Encode → **SelectKBest(chi2, k=7)** → StandardScaler*

| # | Notebook | Phase | Purpose | Status |
|---|----------|-------|---------|--------|
| 3 | `03-rf-selected-features.ipynb` | 4 | RF Baseline (selected features) | ✅ **NEW** |
| 4 | `04-svm-selected-features.ipynb` | 4 | SVM Baseline (selected features) | ✅ **NEW** |
| - | `tune-params-selected-features.ipynb` | 4 | GridSearchCV tuning (selected features) | ✅ **NEW** |

---

### **TRACK B: ALL FEATURES (No Feature Selection)**
*Pipeline: Split → Encode → StandardScaler (NO SelectKBest)*

| # | Notebook | Phase | Purpose | Status |
|---|----------|-------|---------|--------|
| 5 | `05-rf-all-features.ipynb` | 4 | RF Baseline (all features) | ✅ **Renamed** |
| 6 | `06-svm-all-features.ipynb` | 4 | SVM Baseline (all features) | ✅ **Renamed** (duplicate removed) |
| - | `tune-params-all-features.ipynb` | 4 | GridSearchCV tuning (all features) | ✅ **Renamed** |

---

### **Final Evaluation** (Phase 5)
| # | Notebook | Phase | Purpose | Status |
|---|----------|-------|---------|--------|
| 7 | `07-champion-evaluation.ipynb` | 5 | Champion Model (tuned RF with selected features) | ✅ **Renamed** |

---

## Key Changes Made

### ✅ **Created (3 NEW Notebooks)**
1. **`03-rf-selected-features.ipynb`**
   - Random Forest baseline with SelectKBest(chi2, k=7)
   - Full data pipeline: Split → Encode → Select → Scale
   - Outputs baseline F1-Score for selected features track

2. **`04-svm-selected-features.ipynb`**
   - SVM baseline with SelectKBest(chi2, k=7)
   - Includes StandardScaler (required for distance-based algorithm)
   - Outputs baseline F1-Score for selected features track

3. **`tune-params-selected-features.ipynb`**
   - GridSearchCV for both RF and SVM on selected features
   - 5-fold cross-validation, F1-scoring
   - Reports best_params_ for each algorithm

### ✅ **Renamed (4 EXISTING Notebooks)**
1. `03-assign3-model1.ipynb` → `05-rf-all-features.ipynb`
2. `04-assign3-model2.ipynb` → `06-svm-all-features.ipynb` (duplicate cell removed ✓)
3. `tune-params.ipynb` → `tune-params-all-features.ipynb`
4. `05-assign3-model1-feature-selection.ipynb` → `07-champion-evaluation.ipynb`

---

## Execution Order (CRISP-DM Phases 1-5)

```
Phase 2 (Data Understanding):
  01_EDA.ipynb
         ↓
Phase 3 (Data Preparation):
  02_Data_Preparation.ipynb → creates cleaned_data.pkl
         ↓
Phase 4 (Modelling):
  ┌─────────────────────────────────────────────────────┐
  │  TRACK A: SELECTED FEATURES (k=7)                   │
  │                                                      │
  │  03-rf-selected-features.ipynb (baseline RF)        │
  │  04-svm-selected-features.ipynb (baseline SVM)      │
  │  tune-params-selected-features.ipynb (tuning)       │
  └─────────────────────────────────────────────────────┘
         ↓
  ┌─────────────────────────────────────────────────────┐
  │  TRACK B: ALL FEATURES (No Selection)               │
  │                                                      │
  │  05-rf-all-features.ipynb (baseline RF)             │
  │  06-svm-all-features.ipynb (baseline SVM)           │
  │  tune-params-all-features.ipynb (tuning)            │
  └─────────────────────────────────────────────────────┘
         ↓
Phase 5 (Evaluation):
  07-champion-evaluation.ipynb → Final champion model
```

---

## Data Pipeline Specifications

### **TRACK A: SELECTED FEATURES**
```python
# Order is CRITICAL (per weekly notes):
1. train_test_split(stratify=y)    # Stratified 80/20
2. pd.get_dummies()                # One-hot encode categoricals
3. SelectKBest(chi2, k=7)          # Feature selection
4. StandardScaler()                # Standardisation (after selection)
```

**Why this order?**
- Chi-square requires non-negative values (✓ after one-hot)
- StandardScaler introduces negatives (✓ applied last)
- Feature selection BEFORE scaling preserves chi2 validity

### **TRACK B: ALL FEATURES**
```python
# No feature selection step:
1. train_test_split(stratify=y)    # Stratified 80/20
2. pd.get_dummies()                # One-hot encode categoricals
3. StandardScaler()                # Standardisation only
```

---

## Key Methodology Points (Per CRISP-DM + Weekly Notes)

| Concept | Implementation | Justification |
|---------|---|---|
| **Leakage Prevention** | Split FIRST, then encode/select/scale | Test set untouched by feature engineering |
| **Feature Selection** | SelectKBest(chi2, k=7) selected-only | Chi2 = non-negative requirement |
| **Standardisation Timing** | After feature selection | Negative values would break chi2 |
| **Evaluation Metric** | F1-Score (not Accuracy) | Imbalanced classes (reject >> accept) |
| **Hyperparameter Tuning** | GridSearchCV, cv=5, scoring='f1' | Systematic parameter search on training data |
| **Continuous Features Scaled** | age, yrs_experience, family_size, income, mortgage_amt, credit_card_spend | Required for SVM (distance-based) |
| **Categorical Encoding** | pd.get_dummies() | One-hot encoding for multi-class categories |

---

## Next Steps for Execution

1. **✅ Run Notebooks in Order:**
   - Execute 01_EDA.ipynb (data understanding)
   - Execute 02_Data_Preparation.ipynb (generates cleaned_data.pkl)

2. **Run TRACK A (Selected Features):**
   - Execute 03-rf-selected-features.ipynb → note baseline F1
   - Execute 04-svm-selected-features.ipynb → note baseline F1
   - Execute tune-params-selected-features.ipynb → extract best_params_

3. **Run TRACK B (All Features):**
   - Execute 05-rf-all-features.ipynb → note baseline F1
   - Execute 06-svm-all-features.ipynb → note baseline F1
   - Execute tune-params-all-features.ipynb → extract best_params_

4. **Final Evaluation:**
   - Execute 07-champion-evaluation.ipynb with tuned params from Track A
   - Document F1, precision, recall, confusion matrix
   - Generate business metrics for technical report (Section 5)

---

## File Locations
```
/notebooks/
├── 01_EDA.ipynb
├── 02_Data_Preparation.ipynb
├── 03-rf-selected-features.ipynb          ← NEW
├── 04-svm-selected-features.ipynb         ← NEW
├── 05-rf-all-features.ipynb               ← RENAMED
├── 06-svm-all-features.ipynb              ← RENAMED (fixed duplicate)
├── 07-champion-evaluation.ipynb           ← RENAMED
├── tune-params-all-features.ipynb         ← RENAMED
└── tune-params-selected-features.ipynb    ← NEW

/data/
└── cleaned_data.pkl                       (produced by 02_Data_Preparation.ipynb)
```

---

## Architecture Summary

✅ **Total Notebooks:** 9 (2 foundation + 3 selected + 3 all + 1 final)  
✅ **Baseline Models:** 4 (2 RF + 2 SVM)  
✅ **Tuning Notebooks:** 2 (1 selected + 1 all)  
✅ **Final Models:** 1 champion (tuned RF with selected features)  
✅ **Data Pipelines:** 2 tracks (feature selection vs. no selection)  
✅ **Leakage Prevention:** Strict split-first discipline  
✅ **Methodology:** CRISP-DM Phases 1-5 + Weekly Notes compliance  

**Status: READY FOR EXECUTION** 🚀
