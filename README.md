# CVD Risk Classification with Explainable AI 🫀

> Comparing Random Forest and XGBoost for cardiovascular 
> disease prediction, with SHAP-based age-stratified 
> explainability analysis across 68,489 patient records

## 📊 Key Results

| Model | Accuracy | F1-Score | ROC-AUC |
|-------|----------|----------|---------|
| Random Forest | 0.7347 | 0.7341 | 0.8030 |
| XGBoost | 0.7335 | 0.7329 | 0.8025 |

> XGBoost selected for SHAP analysis based on superior 
> CV stability (F1 = 0.7353 ± 0.0052 vs 0.7328 ± 0.0058)

## 🎯 Research Questions
1. Can ensemble ML achieve clinically meaningful CVD 
   classification (ROC-AUC > 0.80)?
2. Which features dominate — clinical biomarkers or 
   lifestyle factors?
3. Do risk factor contributions shift across age groups?

## 🔍 Key Findings

### 1. Both Models Exceed Clinical Threshold
[Insert fig9_roc_curves.png]

### 2. Systolic BP Dominates Globally
[Insert fig12_shap_global_bar.png]

### 3. Lifestyle Importance Increases With Age
[Insert fig18_shap_clinical_vs_lifestyle.png]

The lifestyle-to-clinical SHAP ratio increases 
monotonically across age groups:
- Younger Adult (≤50 yrs): 0.085
- Middle-Aged (51–57 yrs): 0.097  
- Older Adult (>57 yrs):   0.119

## 🛠️ Tech Stack
- Python (pandas, scikit-learn, XGBoost, SHAP)
- Google Colab (GPU-accelerated training)
- Matplotlib / Seaborn (visualization)

## 📁 Dataset
[Cardiovascular Disease Dataset](https://www.kaggle.com/datasets/sulianova/cardiovascular-disease-dataset)  
N = 70,000 | Source: Kaggle (Sulianova, 2019)

## 📄 Paper
This project was written up as a conference paper.  
See `IN PROGRESS`

## 🚀 How to Run
# 1. Clone the repo
git clone https://github.com/ewiw24/cvd-risk-classification-shap

# 2. Install dependencies
pip install -r requirements.txt

# 3. Run notebooks in order (01 → 04)
# Or open directly in Google Colab
