# QSAR Modeling of Androgen Receptor Inhibitors

## Question
Can molecular descriptors predict whether a compound actively 
inhibits the Androgen Receptor (AR) which is a critical drug target 
in prostate cancer?

## Background
The Androgen Receptor (CHEMBL1871) mediates the effects of 
androgens like testosterone and is a primary therapeutic target 
in prostate cancer. Identifying structural features that 
distinguish active from inactive inhibitors can accelerate 
drug discovery by enabling virtual screening of large compound 
libraries before costly experimental testing.

## Dataset
- **Source:** ChEMBL database (CHEMBL1871)
- **Compounds:** 2,224 AR inhibitors (IC50 measurements)
- **Classes:** 1,618 active (≤1,000 nM), 606 inactive (≥10,000 nM)
- **Features:** 135 molecular descriptors after variance filtering
  - Lipinski descriptors: MW, LogP, NumHDonors, NumHAcceptors
  - MACCS structural fingerprints: 167 binary keys

## Methods
1. **Data Curation**: Retrieved IC50 records from ChEMBL, 
   filtered for valid SMILES and nM units, removed duplicates
2. **pIC50 Transformation**: Converted IC50 to pIC50 scale 
   to normalize skewed bioactivity distributions
3. **Feature Generation**: Computed Lipinski descriptors 
   via RDKit and MACCS fingerprints. Removed low-variance 
   features (VarianceThreshold = 0.01)
4. **Modeling**: Trained Random Forest and Logistic Regression 
   classifiers with balanced class weights
5. **Validation**: Stratified 5-fold cross-validation to 
   ensure reliable performance estimation across imbalanced classes

## Results

| Model | Test AUC | CV AUC (5-fold) | Accuracy |
|-------|----------|-----------------|----------|
| Random Forest | 0.919 | 0.917 ± 0.009 | 88% |
| Logistic Regression | 0.868 | — | 83% |

**Key findings:**
- Random Forest outperforms linear baseline by 5 AUC points, 
  confirming non-linear relationships between molecular 
  descriptors and AR inhibitor activity
- MW and LogP are the strongest predictors and biologically 
  consistent with the AR's hydrophobic ligand binding pocket
- NumHAcceptors ranks 5th, consistent with known hydrogen 
  bonding requirements of the AR pharmacophore
- Unstratified CV significantly underestimates performance 
  with imbalanced datasets. Stratified CV is essential for 
  reliable QSAR evaluation

## Repository Structure
AR-Inhibitor-QSAR/
├── README.md
├── notebooks/
│   ├── 01_data_curation.ipynb
│   ├── 02_EDA_and_descriptors.ipynb
│   └── 03_modeling.ipynb
└── results/
├── roc_curve.png
└── feature_importance.png

## Tools and Libraries
Python, RDKit, ChEMBL Web Resource Client, scikit-learn, 
pandas, numpy, matplotlib, seaborn

## Limitations
- Dataset limited to IC50 measurements. Other assay types 
  (Ki, Kd) excluded to ensure comparability
- MACCS fingerprints capture structural patterns but lose 
  3D conformational information relevant to binding
- Model trained on historical ChEMBL data. Performance on 
  novel scaffold classes may differ
- Class imbalance (73% active) addressed via balanced class 
  weights but may still affect inactive compound prediction

## Future Directions
- Incorporate 3D descriptors and molecular docking scores
- Test gradient boosting models (XGBoost, LightGBM)
- Extend pipeline to additional AR-related targets 
  (e.g., AR splice variants in castration-resistant 
  prostate cancer)
