# ESBL-PE Predictor — EDA Master Summary
**Run by:** Henry Zamarano (Mbarara Regional Referral Hospital)  
**Dataset:** data/16 RAW DATA ESBL PE.xlsx  
**Full cohort:** 1,569 rows × 81 columns  
**Labelled for modelling:** 660 rows (281 ESBL-positive, 379 ESBL-negative)

---

## Dataset Structure
- 3-row header: row 0 = group labels, rows 1–2 = feature names
- Row 3 onwards = actual patient data
- Target column: `ESBL Production (Both Enterobacterales and Non Enterobacterales)`

## Class Distribution
| Group | N | % of full cohort |
|-------|---|----------------|
| ESBL-Positive | 281 | 17.9% |
| ESBL-Negative | 379 | 24.2% |
| Not Tested (No isolate) | 909 | 57.9% |
| **Total** | **1,569** | 100% |

Among labelled patients (660): **42.6% ESBL-positive** — near-balanced.

---

## Data Quality Issues Found

| Column | Issue | Action |
|--------|-------|--------|
| Weight (Study ID 1391) | Weight = 731 kg — data entry error | Correct to 73.1 kg, recalculate BMI |
| Anemia | Values: 'No', 'Yes', 'yes' (lowercase) | Standardise to Title Case |
| Vaginal discharge | Values include 'no' (lowercase) | Standardise |
| Pain, itching, vagina | Values include 'no' (lowercase) | Standardise |
| Itchy skin genitals | Values include 'no' (lowercase) | Standardise |
| Burning when urinating | Values include 'no' (lowercase) | Standardise |
| `Comorbidity` column | 77.2% missing — it is a group header label | Exclude from model features |
| `Coinfection` column | 76.9% missing — it is a group header label | Exclude from model features |
| `unnamed` column | 100% missing | Drop entirely |

---

## Key Clinical Findings

### Target Variable
- Column 4 (`ESBL Production — Both Enterobacterales and Non Enterobacterales`) is confirmed target
- 281 Yes (ESBL+), 379 No (ESBL-), 909 Not applicable
- Both columns have identical Yes rows (same 281 patients)

### Age
- Range: 1–88 years (includes paediatric patients — intentional, per study design)
- Mean: 43.5 ± 20.9 years | Median: 45 years
- Similar age distribution across ESBL+ and ESBL- groups

### BMI / Weight / Height
- **ONLY 1 data error:** Study ID 1391, Weight=731 kg → should be 73.1 kg
- Low BMI values (8.9–14) in 1–3 year olds are **real paediatric measurements, not errors**
- Median BMI: 22.0 (normal range for Ugandan population)

### Isolate Species
| Species | ESBL+ Rate | N |
|---------|-----------|---|
| GIST | 85.7% | 7 |
| Klebsiella aerogenes | 100% | 21 |
| Proteus vulgaris | 100% | 41 |
| Enterobacter cloacae | 67.6% | 37 |
| Proteus mirabilis | 69.2% | 39 |
| Escherichia coli | 43.2% | 190 |
| Klebsiella pneumoniae | 35.0% | 157 |
| Salmonella Typhi | 0% | 107 |

> Note: Isolate species is a **post-culture feature** — unavailable for empirical treatment prediction. Exclude from web app form; include in research model.

### Specimen Type
- Sputum: 50.6% ESBL+ rate (highest)
- CSF: 48.8%
- Urine: 44.2%
- Blood: 34.5% (lowest)

### Cancer Type (top predictors)
| Cancer | ESBL+ Rate | N |
|--------|-----------|---|
| GIST | 85.7% | 17 |
| Pancreatic cancer | 75.0% | 68 |
| Wilms' tumor | 66.7% | 72 |
| NHL | 64.0% | 173 |
| Breast cancer | 28.9% | 74 |
| ALL | 25.6% | 61 |
| Cervical cancer | 36.3% | 204 |

### Cancer Stage
- 62.9% of patients are Stage IV
- ESBL+ rates: Stage II=39.6%, Stage III=36.5%, Stage IV=45.7%

### Clinical Risk Factors (strongest predictors)
| Factor | ESBL+ if Yes | ESBL+ if No | Difference |
|--------|-------------|------------|-----------|
| Prolonged hospitalisation >3 weeks | **50.2%** | 27.7% | **+22.5%** |
| Indwelling devices | **49.6%** | 30.6% | **+19.0%** |
| Previous antibiotic use | **48.2%** | 34.3% | **+13.9%** |
| Outpatient status | 49.2% | 40.0% (Inpatient) | +9.2% |

### Comorbidities (ranked by discriminative power)
| Comorbidity | Prevalence | ESBL+ if Yes | ESBL+ if No | Diff |
|-------------|-----------|-------------|------------|------|
| Liver disease | 0.8% | **83.3%** | 42.2% | **+41.1%** |
| Anemia | 5.9% | **63.8%** | 40.5% | **+23.3%** |
| CKD | 2.5% | **62.5%** | 41.8% | **+20.7%** |
| Diabetes | 6.2% | **56.9%** | 41.1% | **+15.8%** |
| Heart disease | 2.5% | 48.0% | 42.4% | +5.6% |
| Hypertension | 13.7% | 41.3% | 42.9% | -1.6% (no signal) |

### Symptoms (top discriminative)
| Symptom | Prevalence | ESBL+ if Yes | Diff |
|---------|-----------|-------------|------|
| Pain/tenderness in stomach | 7.5% | 92.1% | **+58.5%** |
| Pain in lower abdomen/pelvis | 3.3% | 96.2% | **+58.2%** |
| Urine leakage | 1.5% | 95.8% | **+55.2%** |
| Difficulty breathing | 6.4% | 85.9% | **+49.7%** |
| Cough | 7.1% | 82.0% | **+45.4%** |
| Discomfort urinating | 3.7% | 81.1% | **+40.8%** |
| Malaise | 5.8% | 73.4% | **+34.1%** |
| Feeling tired/fatigue | 69.9% | 43.9% | +4.3% (weak) |
| Fever | 57.9% | 41.9% | -1.6% (no signal) |
| Nausea/Vomiting/Diarrhea/Headache | 30-32% each | ~40% | ~0% (no signal) |

---

## Preprocessing Decisions (for Task 4)

1. **Fix data error:** Study ID 1391, Weight 731 → 73.1 kg, recalculate BMI
2. **Standardise Yes/No casing** across all binary columns
3. **Drop for modelling:** `unnamed`, `Comorbidity` (group label), `Coinfection` (group label)
4. **Drop for empirical prediction form:** `Isolate species`, `Specimen`, `Gram reaction`, all antibiogram columns (unavailable at time of empirical treatment)
5. **Keep for research model:** All 660 labelled rows; exclude 909 Not Tested
6. **Note on Salmonella Typhi:** All 107 are ESBL-negative — consider as a special case or exclusion in modelling
7. **Feature engineering opportunity:** `Prolonged hospitalisation`, `Indwelling devices`, and `Previous antibiotics` are the three strongest individual predictors

---

## Files saved
All individual column HTML reports are in: `output/column_analysis/`
