# Data Dictionary
## Cardiovascular Disease Dataset
**Source:** [Kaggle — Sulianova (2019)](https://www.kaggle.com/datasets/sulianova/cardiovascular-disease-dataset)  
**Records:** 70,000 (raw) → 68,489 (after preprocessing)  
**Format:** CSV, semicolon-separated (`;`)  
**Task:** Binary classification — CVD present (1) or absent (0)

---

## Original Features

| Column | Type | Values | Description |
|--------|------|--------|-------------|
| `id` | Integer | Unique | Record identifier — dropped in preprocessing |
| `age` | Integer | Days | Patient age in days — converted to years |
| `gender` | Binary | 1=Female, 2=Male | Biological sex |
| `height` | Integer | cm | Patient height |
| `weight` | Float | kg | Patient weight |
| `ap_hi` | Integer | mmHg | Systolic blood pressure |
| `ap_lo` | Integer | mmHg | Diastolic blood pressure |
| `cholesterol` | Ordinal | 1=Normal, 2=Above Normal, 3=Well Above Normal | Cholesterol level category |
| `gluc` | Ordinal | 1=Normal, 2=Above Normal, 3=Well Above Normal | Glucose level category |
| `smoke` | Binary | 0=No, 1=Yes | Current smoker status |
| `alco` | Binary | 0=No, 1=Yes | Alcohol intake |
| `active` | Binary | 0=No, 1=Yes | Physically active |
| `cardio` | Binary | 0=Absent, 1=Present | **Target variable** — CVD presence |

---

## Engineered Features

| Column | Type | Values | Description | Method |
|--------|------|--------|-------------|--------|
| `age_years` | Float | 29.6–64.9 | Age converted from days to years | `age / 365.25` |
| `bmi` | Float | Continuous | Body Mass Index | `weight / (height/100)²` |
| `bmi_category` | Ordinal | 0–3 | WHO BMI classification | See table below |
| `bp_category` | Ordinal | 0–4 | AHA 2017 blood pressure staging | See table below |
| `age_group` | Ordinal | 0–2 | Age subgroup for stratified analysis | Tercile-based — see note |
| `lifestyle_score` | Ordinal | 0–3 | Composite unhealthy lifestyle burden | `smoke + alco + (1 - active)` |

---

## Engineered Feature Value Mappings

### BMI Category (WHO Classification)
| Value | Label | BMI Range |
|-------|-------|-----------|
| 0 | Underweight | < 18.5 |
| 1 | Normal | 18.5 – 24.9 |
| 2 | Overweight | 25.0 – 29.9 |
| 3 | Obese | ≥ 30.0 |

### BP Category (AHA 2017 Guidelines)
| Value | Label | Systolic | Diastolic |
|-------|-------|----------|-----------|
| 0 | Normal | < 120 mmHg | AND < 80 mmHg |
| 1 | Elevated | 120–129 mmHg | AND < 80 mmHg |
| 2 | Stage 1 Hypertension | 130–139 mmHg | OR 80–89 mmHg |
| 3 | Stage 2 Hypertension | ≥ 140 mmHg | OR ≥ 90 mmHg |
| 4 | Hypertensive Crisis | > 180 mmHg | OR > 120 mmHg |

### Age Group (Tercile-Based)
| Value | Label | Age Range | Note |
|-------|-------|-----------|------|
| 0 | Younger Adult | ≤ 50 years | ~33rd percentile boundary |
| 1 | Middle-Aged Adult | 51–57 years | 33rd–67th percentile |
| 2 | Older Adult | > 57 years | ~67th percentile boundary |

> **Why tercile-based?** The dataset age range is 29.6–64.9 years
> with heavy concentration in middle-to-older adult ranges.
> The WHO "Young Adult" category (18–39 years) represents
> less than 0.1% of records, making it statistically
> inadequate for subgroup analysis. Tercile boundaries
> ensure approximately equal subgroup sizes for valid
> age-stratified SHAP comparison.

### Lifestyle Score
| Value | Label | Meaning |
|-------|-------|---------|
| 0 | Healthiest | Non-smoker + No alcohol + Physically active |
| 1 | Moderate risk | One unhealthy behavior |
| 2 | High risk | Two unhealthy behaviors |
| 3 | Most unhealthy | Smoker + Alcohol + Physically inactive |

---

## Preprocessing Steps Applied

| Step | Details | Records Affected |
|------|---------|-----------------|
| Outlier removal | ap_hi: kept 80–250 mmHg | |
| | ap_lo: kept 50–150 mmHg | |
| | Removed if diastolic ≥ systolic | |
| | height: kept 140–210 cm | |
| | weight: kept 30–180 kg | ~1,400 rows removed |
| Deduplication | Exact duplicate rows removed | ~111 rows removed |
| **Final dataset** | | **68,489 records** |

---

## Train / Test Split

| Set | Records | Proportion |
|-----|---------|------------|
| Training set | 54,791 | 80% |
| Test set | 13,698 | 20% |
| **Total** | **68,489** | **100%** |

> Stratified split (`random_state=42`) preserving
> 50/50 class balance in both sets.

---

## Class Balance

| Class | Label | Train Count | Test Count | Proportion |
|-------|-------|-------------|------------|------------|
| 0 | No CVD | 27,397 | 6,849 | ~50% |
| 1 | CVD Present | 27,394 | 6,849 | ~50% |

> Near-perfect balance — no resampling (SMOTE etc.) required.

---

## Feature Set Used for Modeling

```python
FEATURES = [
    'age_years',       # continuous
    'gender',          # binary
    'bmi',             # continuous (derived)
    'bmi_category',    # ordinal (derived)
    'ap_hi',           # continuous — key clinical
    'ap_lo',           # continuous — key clinical
    'bp_category',     # ordinal (derived, AHA staging)
    'cholesterol',     # ordinal
    'gluc',            # ordinal
    'smoke',           # binary lifestyle
    'alco',            # binary lifestyle
    'active',          # binary lifestyle
    'lifestyle_score', # ordinal (derived, composite)
    'age_group',       # ordinal (derived, stratification)
]
TARGET = 'cardio'
```

---

## Scaling Applied

| Feature | Scaler | Reason |
|---------|--------|--------|
| `age_years` | StandardScaler | Continuous, wide range |
| `bmi` | StandardScaler | Continuous, wide range |
| `ap_hi` | StandardScaler | Continuous, wide range |
| `ap_lo` | StandardScaler | Continuous, wide range |
| All binary/ordinal | None | Already on bounded scale |

> Scaler fitted on **training set only** then applied
> to test set — prevents data leakage.

---

## Citation

```bibtex
@dataset{sulianova2019cardiovascular,
  author    = {Sulianova},
  title     = {Cardiovascular Disease Dataset},
  year      = {2019},
  publisher = {Kaggle},
  url       = {https://www.kaggle.com/datasets/sulianova/cardiovascular-disease-dataset}
}
```
