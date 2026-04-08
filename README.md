<h1 align="center">San Diego Regional Medical Center — KPI Report</h1>

---

<h1 align="center">Client Background</h1>

As an Analytics Consultant for San Diego Regional Medical Center (SDRMC), I was tasked with developing a high-level KPI report for the executive leadership team, specifically the Chief Financial Officer and Chief Operating Officer. This report comprehensively analyzes **36,330 records** across four interrelated tables — **1,500 patients** (`Patients`), **9,169 clinical encounters** (`Encounters`), **16,492 procedures** (`Procedures`), and **9,169 claims** (`Claims`) — covering 2018 to 2024 across seven encounter types and eight payer categories.

The goal was to move beyond a standard operational snapshot and build a complete revenue cycle narrative — tracing performance from the moment a patient arrives to the moment the hospital collects, or fails to collect, payment for care delivered. Unlike a standard clinical operations report, this analysis extends into the revenue cycle — examining how clinical patterns translate into financial outcomes through the claims and reimbursement pipeline. The analysis is organized across three acts that mirror how hospital leadership actually thinks about performance:

- **Clinical Operations**: patient admissions, readmissions, length of stay, and cost of care across encounter types and payer segments.
- **Claims and Reimbursement**: claim denial rates, payer mix concentration, days in accounts receivable, and net collection performance.
- **The Revenue Diagnosis**: connecting the clinical patterns from Clinical Operations to the financial outcomes in Claims and Reimbursement, and surfacing revenue recovery opportunities for billing leadership through improved resubmission practices.

---

<h1 align="center">Executive Summary</h1>

SDRMC delivered **9,169 patient encounters** between 2018 and 2024 — but the revenue story underneath it becomes harder to ignore. Of **$22.6M** billed across the period, **$2.6M was written off as uncollectable**, representing **14.7%** of total billed revenue lost. That gap is not random — it is concentrated within specific encounter types, specific payer segments, and a billing pipeline that consistently leaves recoverable revenue behind.

The core finding is this: SDRMC's highest-acuity encounter types — Emergency and Inpatient — are simultaneously the most expensive to deliver and the least efficiently collected. Emergency encounters carry a **19.7%** denial rate, nearly **5x** the Wellness rate of **3.7%**, while collecting just **66 cents** per dollar of care delivered. Inpatient encounters follow the same pattern. These two encounter types represent only **13.5%** of total encounter volume but drive a disproportionate share of the hospital's revenue leakage — and closing that gap through improved documentation and resubmission practices is the clearest financial lever this analysis surfaces.

Clinically, SDRMC maintained stable admission volume averaging **1,310 encounters per year**. Across the full period, **349** were classified as inpatient encounters — a **3.8%** inpatient rate — representing the highest-acuity and highest-cost visit category in the data. The 30-day readmission rate averaged **10.9%** — peaking at **15.5%** pre-COVID before dropping to **6.0%** in 2021 as admission volumes contracted during the pandemic. Average inpatient length of stay surged **43%** during the COVID period, from a **54-hour** pre-pandemic baseline to **78 hours** in 2021, before stabilizing at approximately **55 hours**. The hospital hasn't fully returned to pre-pandemic operational efficiency — at **349 inpatient encounters** per period, reclaiming that **2%** gap returns meaningful bed capacity to an already constrained inpatient pipeline."

The uninsured patient segment runs through every dimension of this report as its most persistent pressure point. At **15.9%** of all encounters, uninsured patients average **$42,768** per inpatient encounter — **69%** above the insured average of **$25,254** — while collecting at just **25.4%** of billed charges compared to **79.6%** for Medicare. Their claims average **120** days to payment, more than **5x** the **23-day** government payer average. The cost burden is concentrated in the **18–64** age group, where uninsured rates are highest and the opportunity for coverage intervention is greatest.

On the revenue cycle, the blended claim denial rate of **8.6%** has improved from a **9.1%** peak in 2020 to **7.7%** by 2024 — a positive trend that has not yet translated into full revenue recovery. Of **$3.3M** in denied claims across the period, **44.7%** were resubmitted and **61.9%** of those resubmissions were ultimately paid — leaving an estimated **$713K** recoverable through more consistent resubmission practices. The pipeline is not broken — it is leaking, and the leak has a precise location.

---

<h1 align="center">Insights Deep Dive</h1>

<h2 align="center">Clinical Operations</h2>

**Inpatient encounters represent the smallest share of volume — but carry the highest clinical and financial weight**
* SDRMC recorded **9,169 total encounters** between 2018 and 2024, averaging **1,310** per year. Of those, **349** were classified as inpatient encounters — a **3.8% inpatient rate** — representing the highest-acuity and highest-cost visit category in the data
* The 30-day readmission rate averaged **10.9%** across the period (**38 readmissions, 349 inpatient encounters**), peaking at **15.5%** in 2018–2019 before dropping to **6.0%** in 2021 as admission volumes contracted during the pandemic
*  Post-pandemic readmission rates have stabilized in the **10–11%** range — above the COVID-era low but below the pre-pandemic peak — signaling an opportunity to sustain the operational discipline demonstrated during 2020–2021. For a COO focused on care quality and patient flow, closing the gap between the current **10.9%** rate and the **6.0%** COVID-era low is the most direct lever available.

**The COVID period exposed the fragility of inpatient efficiency — and full recovery hasn't arrived**
* Average inpatient length of stay held at **54 hours** in 2018–2019 before surging **43%** to **78 hours** at its 2021 peak — driven by the complexity and intensity of COVID-era inpatient care
* By 2022, LOS returned to approximately **56 hours** and has hovered near **55 hours** through 2024 — still **2% above** the pre-pandemic baseline of **54 hours**
* At **349 inpatient encounters** per period, each additional hour of average LOS represents **349 bed-hours of capacity**. For a COO evaluating inpatient efficiency, the **1-hour gap** from the pre-pandemic baseline represents recoverable capacity that the hospital has not yet reclaimed.

**Uninsured patients cost significantly more — and the gap concentrates in the working-age population**
* Uninsured inpatient encounters average **$42,768** per visit — **69% above** the insured inpatient average of **$25,254**. Across all encounter types, uninsured patients average **$2,608** versus **$2,238** for insured patients — a **16.5% cost premium** that compounds across **15.9%** of total encounters
* The cost burden is not evenly distributed by age. Patients aged **50–64** carry the highest average encounter cost at **$2,891**, followed by **18–34** at **$2,832**. The **80+** cohort averages just **$1,281** — reflecting near-universal Medicare coverage in that age group
* **74.4%** of all procedures performed at SDRMC are covered by insurance — measured at the individual procedure level, not the encounter level. Government payers cover **90%** of procedures, commercial payers cover **85%**, and uninsured patients carry **0%** procedure coverage by definition. Every uncovered procedure in this dataset belongs to an uninsured patient — there are no exceptions.
