# Data

This folder contains the dataset used in the diabetes 30-day hospital readmission prediction project.

## Dataset

The data come from a large multi-hospital study of patients with diabetes in the United States. The dataset includes hospital encounters from **130 U.S. hospitals between 1999 and 2008** and contains approximately **101,766 admissions**. 

Each row represents a hospital admission and includes demographic, clinical, diagnostic, medication, and prior healthcare utilization information. 

---

## Outcome Variable

The original `readmitted` variable contains three categories:

- `<30` — readmitted within 30 days
- `>30` — readmitted after 30 days
- `NO` — not readmitted

For modeling, the outcome was converted to a binary target:

- `1` = readmitted within 30 days
- `0` = not readmitted within 30 days

Approximately **11% of encounters resulted in a 30-day readmission**, creating a highly imbalanced classification problem. 

---

## Predictor Categories

The dataset includes variables from several areas:

### Demographics
- Race
- Gender
- Age group

### Prior Healthcare Utilization
- `number_inpatient`
- `number_emergency`
- `number_outpatient`

### Current Hospital Encounter
- `time_in_hospital`
- `num_lab_procedures`
- `num_procedures`
- `num_medications`
- `number_diagnoses`

### Laboratory Results
- `max_glu_serum`
- `A1Cresult`

### Diagnoses
- Primary diagnosis category
- Secondary diagnosis category
- Third diagnosis category

### Medications
Examples include:
- Metformin
- Glipizide
- Glyburide
- Pioglitazone
- Rosiglitazone
- Insulin

### Admission and Discharge Information
- Admission source
- Admission type
- Discharge disposition 

---

## Data Preparation

The modeling workflow includes the following preprocessing steps:

1. Fill missing values in `max_glu_serum` and `A1Cresult` with `"None"`.
2. Convert the readmission outcome into a binary target.
3. Remove identifier variables including:
   - `encounter_id`
   - `patient_nbr`
4. One-hot encode categorical predictors.
5. Standardize features for logistic regression and LASSO.
6. Split the data into:
   - **60% training**
   - **20% testing**
   - **20% validation** 

After dummy encoding, the dataset expands to approximately **123 predictors**. 

---

## Notes

The dataset is historical and covers encounters from **1999–2008**, so some clinical practices, medications, and hospital policies may differ from current standards. 

The data are also observational, so relationships identified by the predictive models should be interpreted as **associations rather than causal effects**. 
