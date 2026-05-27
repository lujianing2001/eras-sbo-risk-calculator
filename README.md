# ERAS Failure Risk Calculator - GitHub Pages Deployment Guide

## Overview
This package contains two tools for predicting ERAS (Enhanced Recovery After Surgery) failure risk in emergency small bowel obstruction (SBO) surgery:

1. **Precision Risk Calculator** (`index.html`) - Full multivariate logistic regression model with 8 predictors
2. **ABCDE Screening Tool** (`abcde-tool.html`) - Simplified bedside screening mnemonic

## Files
- `index.html` - Main calculator (English interface)
- `abcde-tool.html` - ABCDE screening tool (linked to calculator)
- `README.md` - This file

## Deployment to GitHub Pages

### Step 1: Create GitHub Repository
1. Go to https://github.com and create a new repository
2. Name it: `eras-sbo-risk-calculator` (or your preferred name)
3. Make it **Public** (required for free GitHub Pages)

### Step 2: Upload Files
```bash
git init
git add index.html abcde-tool.html README.md
git commit -m "Initial deployment of ERAS risk calculator"
git remote add origin https://github.com/YOUR_USERNAME/eras-sbo-risk-calculator.git
git push -u origin main
```

### Step 3: Enable GitHub Pages
1. Go to repository **Settings** → **Pages**
2. Source: Deploy from a branch
3. Branch: `main` / `root`
4. Save
5. Your calculator will be available at: `https://YOUR_USERNAME.github.io/eras-sbo-risk-calculator/`

## Technical Specifications

### Model Coefficients (Multivariate Logistic Regression)
| Variable | Coefficient (β) | Input Format |
|----------|-----------------|--------------|
| Intercept | -9.659714 | — |
| Age (years) | 0.0506770685 | Integer |
| ASA Grade III-IV | 1.7789876534 | Binary (0=I-II, 1=III-IV) |
| aCCI | 0.3350471249 | Integer (score) |
| Serum albumin (g/L) | -0.0500661185 | Decimal |
| Decreased mural enhancement | 2.3110615562 | Binary (0=No, 1=Yes) |
| Shock index | 1.9642234967 | Decimal |
| Serum lactate (mmol/L) | 1.1585602154 | Decimal |
| Electrolyte disturbance | 0.9360609543 | Binary (0=No, 1=Yes) |

**Note**: The intercept (-9.659714) is from the original training cohort (N=640) multivariate logistic regression output, confirmed by R `summary()`.

### Model Performance
| Dataset | AUC (95% CI) |
|---------|-------------|
| Training (N=640) | 0.924 (0.904-0.945) |
| Internal Validation (N=274) | 0.935 (0.908-0.962) |
| External Validation (N=313) | 0.907 (0.873-0.941) |
| Category | Probability Range | Clinical Action |
|----------|------------------|-----------------|
| Low Risk | < 20% | Standard ERAS protocol |
| Intermediate Risk | 20% - 40% | Enhanced monitoring |
| High Risk | ≥ 40% | Intensive management |

### ABCDE Screening Cutoffs (Youden Index Optimized)
| Letter | Domain | Variable(s) | Abnormal Cutoff | Points |
|--------|--------|-------------|-----------------|--------|
| A | Age / Albumin | Age ≥ 71 years OR Albumin ≤ 38 g/L | Either condition | 1 |
| B | Biochemistry | Serum lactate ≥ 2.0 mmol/L | Elevated | 1 |
| C | Comorbidity | ASA Grade III-IV OR aCCI ≥ 6 | Either condition | 1 |
| D | Disturbance | Shock index ≥ 0.915 OR Electrolyte disturbance | Either condition | 1 |
| E | Enhancement | Decreased mural enhancement (CT) | Present | 1 |

**Score Interpretation**:
- 0-1 points: Low Risk (< 20% failure rate)
- 2-3 points: Intermediate Risk (20-40% failure rate)
- 4-5 points: High Risk (≥ 40% failure rate)

### Variable Importance Ranking (by |β|)
1. Decreased mural enhancement: 2.311
2. Shock index: 1.964
3. ASA Grade III-IV: 1.779
4. Serum lactate: 1.159
5. Electrolyte disturbance: 0.936
6. aCCI: 0.335
7. Age: 0.051
8. Serum albumin: -0.050

## Citation
If you use this calculator in research, please cite:
> [Your Paper Citation - To be updated upon publication]

## Disclaimer
This calculator is for research and educational purposes only. It should not replace clinical judgment. The model was developed for adult patients undergoing emergency laparotomy for acute small bowel obstruction with planned ERAS implementation.

## Contact
For questions or issues, please contact the corresponding author of the original study.

---

**Deployment Date**: 2026-05-27
**Version**: 1.0
**Target Journal**: JAMA Surgery
