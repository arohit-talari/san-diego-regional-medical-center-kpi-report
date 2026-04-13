# Data Dictionary — San Diego Regional Medical Center

**4 tables · 36,330 total records · 2018–2024**

All field definitions, data types, and source notes across the Patients, Encounters, Procedures, and Claims tables.

---

## `Patients` — 1,500 records

| Field | Data Type | Description |
|---|---|---|
| `patient_id` | ID | Unique patient identifier. Format: P-XXXX |
| `birthdate` | Date | Patient date of birth. Format: YYYY-MM-DD |
| `gender` | Text | Patient biological sex. Values: Male, Female |
| `race` | Text | Patient self-reported race. Values: White, Black or African American, Hispanic, Asian, Other |
| `marital_status` | Text | Patient marital status. Values: Single, Married, Divorced, Widowed |
| `age_group` | Text | Age band assigned at time of analysis. Values: 18–34, 35–49, 50–64, 65–79, 80+ |
| `city` | Text | Patient city of residence. All patients: San Diego |
| `state` | Text | Patient state of residence. All patients: CA |
| `zip` | Text | Patient zip code. Values: 92101, 92103, 92115, 92123, 92131 |

---

## `Encounters` — 9,169 records

| Field | Data Type | Description |
|---|---|---|
| `encounter_id` | ID | Unique encounter identifier. Format: E-XXXXX |
| `patient_id` | FK → Patients | Foreign key linking to the `Patients` table via patient_id |
| `encounter_date` | Date | Date encounter occurred. Format: YYYY-MM-DD. Range: 2018–2024 |
| `age_at_encounter` | Integer | Patient age at time of encounter. Calculated using DATEDIF from birthdate in `Patients` table |
| `year` | Integer | Calendar year of encounter. Extracted from encounter_date. Range: 2018–2024 |
| `encounter_year_period` | Text | Period classification based on year. Values: Pre-COVID (2018–2019), COVID (2020–2021), Post-COVID (2022–2024) |
| `encounter_type` | Text | Category of clinical visit. Values: Inpatient, Outpatient, Emergency, Ambulatory, Urgent Care, Wellness, Elective Procedure |
| `admission_status` | Text | Whether the patient was formally admitted. Values: Admitted, Non-Admitted |
| `primary_diagnosis` | Text | Primary clinical reason for encounter. 10 diagnosis categories including Cardiovascular Disease, Respiratory Illness, Diabetes Management, and others |
| `length_of_stay_hrs` | Decimal | Duration of encounter in hours from admission to discharge. Inpatient encounters average 55 hours across the period; Wellness encounters average under 1 hour |
| `base_encounter_cost` | Currency | Cost to the hospital of delivering care before insurance reimbursement. Inpatient and Emergency encounter costs are elevated during 2020–2021 reflecting COVID-era care complexity |
| `payer` | Text | Insurance provider or coverage type. Values: Medicare, Medicaid, Aetna, Blue Cross Blue Shield, Cigna, Humana, UnitedHealthcare, No Insurance |
| `payer_type` | Text | Payer category grouping. Values: Government (Medicare, Medicaid), Commercial (Aetna, BCBS, Cigna, Humana, UnitedHealthcare), Uninsured (No Insurance) |
| `age_group` | Text | Patient age group at time of encounter. Derived from `Patients` table via XLOOKUP for filter use in analysis |
| `readmission_flag` | Boolean | TRUE if the inpatient encounter occurred within 30 days of a prior inpatient encounter for the same patient. FALSE otherwise. Non-inpatient encounters are always FALSE |

---

## `Procedures` — 16,492 records

| Field | Data Type | Description |
|---|---|---|
| `procedure_id` | ID | Unique procedure identifier. Format: PR-XXXXX |
| `encounter_id` | FK → Encounters | Foreign key linking to the `Encounters` table via encounter_id. One encounter may have multiple procedure records |
| `procedure_date` | Date | Date procedure was performed. Matches the parent encounter date |
| `procedure_category` | Text | Clinical category of procedure. Values: Diagnostic, Surgical, Therapeutic, Preventive |
| `procedure_description` | Text | Plain language procedure name. 19 unique procedures across the 4 categories |
| `base_cost` | Currency | Cost of the individual procedure independent of encounter cost |
| `insured` | Boolean | TRUE if the procedure was covered by the patient's insurance plan. Uninsured patients (No Insurance) are always FALSE. 74.4% of all procedures are TRUE |
| `payer` | Text | Insurance provider for the parent encounter. Added via XLOOKUP from `Encounters` table using encounter_id |

---

## `Claims` — 9,169 records

| Field | Data Type | Description |
|---|---|---|
| `claim_id` | ID | Unique claim identifier. Format: C-XXXXX |
| `encounter_id` | FK → Encounters | Foreign key linking to the `Encounters` table. One claim per encounter — relationship is one-to-one |
| `patient_id` | FK → Patients | Foreign key linking to the `Patients` table via patient_id |
| `date_submitted` | Date | Date the claim was filed with the payer. Typically 1–7 days after the encounter date |
| `date_paid` | Date | Date the claim was paid or closed by the payer |
| `amount_billed` | Currency | Total amount billed to the payer. Includes standard markup above base encounter cost |
| `amount_allowed` | Currency | The amount the payer contractually agreed to reimburse. Government payers typically allow less than the billed amount due to fixed fee schedules |
| `amount_paid` | Currency | Amount actually collected from the payer. Zero for claims with Denial Status = Denied - Written Off |
| `cost_to_collection_ratio` | Decimal | Amount paid divided by base encounter cost. Measures how many cents the hospital collected per dollar of care delivered. Calculated in Excel as amount_paid ÷ XLOOKUP(base_encounter_cost from Encounters) |
| `denial_status` | Text | Outcome of the claim. Values: Accepted, Partial, Denied - Recovered, Denied - Written Off |
| `denial_reason` | Text | Reason for denial where applicable. Values: Medical Necessity Not Established, Prior Authorization Required, Coding or Documentation Error, Duplicate Claim, Patient Not Covered / Eligibility Issue. NULL for accepted claims |
| `revenue_recovery_opportunity` | Currency | Amount billed for claims with Denial Status = Denied - Written Off. Zero for all other denial statuses. Used to calculate the $712,537 recoverable opportunity |
| `days_to_payment` | Integer | Number of days between date_submitted and date_paid. Primary driver of the Days in A/R KPI. Uninsured claims average 119.9 days; government payer claims average 22.6 days |
| `days_to_payment_bucket` | Text | Banded grouping of days_to_payment. Values: 0–14 Days, 15–30 Days, 31–60 Days, 61–120 Days, 120+ Days |
| `payer` | Text | Insurance provider for the encounter. Added via XLOOKUP from `Encounters` table using encounter_id. Mirrors the payer field in `Encounters` |
| `resubmission_flag` | Boolean | TRUE if a denied claim was resubmitted to the payer for reconsideration. 44.7% of denied claims have this flag set to TRUE |
| `net_collection_rate` | Decimal | Amount paid divided by amount billed. Measures the percentage of billed charges actually collected. Blended rate across accepted claims: 75.7%. Drops to 69.0% when all claims including written-off denials are included |
| `encounter_type` | Text | Category of the parent encounter. Added via XLOOKUP from `Encounters` table using encounter_id. Values match encounter_type in `Encounters` |
| `payer_type` | Text | Payer category of the parent encounter. Added via XLOOKUP from `Encounters` table using encounter_id. Values: Government, Commercial, Uninsured |
| `base_encounter_cost` | Currency | Cost to the hospital of delivering care for the parent encounter. Added via XLOOKUP from `Encounters` table using encounter_id. Used as the denominator in cost_to_collection_ratio |
| `year` | Integer | Calendar year of claim submission. Derived using YEAR formula from date_submitted. Range: 2018–2024 |
| `age_group` | Text | Patient age group. Added via XLOOKUP from `Encounters` table using encounter_id. Used for age-level segmentation in claims analysis |

---
