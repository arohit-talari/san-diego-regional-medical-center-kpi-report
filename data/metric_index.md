# Metric Index — San Diego Regional Medical Center

This document covers every KPI and derived metric referenced in the analysis — what it measures, how I calculated it, and where the nuance lives. For raw field definitions see `data_dictionary.md`.

A few metrics in this report required deliberate methodological decisions that affect how the numbers should be interpreted. I've flagged those directly in the relevant entries rather than burying them in a separate caveats section — if a number requires context to read correctly, that context belongs next to the number.

---

## Clinical Operations

**Total Encounters — 9,169**
The total patient visit count across all seven encounter types and all eight payer categories between 2018 and 2024. This is the base denominator for encounter-level share calculations throughout the report. Simple count of encounter_id in the `Encounters` table.

---

**Inpatient Encounter Count — 349**
The number of encounters where encounter_type = 'Inpatient' in the `Encounters` table. This is the correct denominator for every inpatient-specific KPI in the report — readmission rate, average LOS, and average inpatient cost all use 349 as their base.

This distinction matters more than it might seem. A separate field in the `Encounters` table — admission_status = 'Admitted' — returns 594, which includes Emergency encounters that converted to overnight admissions. I encountered this distinction early in the analysis and it's easy to conflate the two. 349 and 594 are measuring different things. Whenever the report references inpatient performance specifically, 349 is the right number.

---

**Inpatient Encounter Rate — 3.8%**
349 ÷ 9,169. The share of all SDRMC encounters that were classified as inpatient. Small by volume but disproportionately significant by cost, denial rate, and revenue impact — which is the central tension this report is built around.

---

**Readmission Rate — 10.9%**
38 readmissions ÷ 349 inpatient encounters. A readmission was defined as any inpatient encounter where the same patient returned for another inpatient visit within 30 days of their prior discharge, regardless of diagnosis. This definition is consistent with CMS standards and is the most common approach used in hospital performance reporting.

The readmission_flag column in the `Encounters` table captures this at the row level — I aggregated it in Tableau as:
`SUM(IF [Readmission Flag]=TRUE THEN 1 ELSE 0 END) / SUM(IF [Encounter Type]='Inpatient' THEN 1 ELSE 0 END)`

The pre-COVID rate (2018–2019 combined) was 15.5% — 15 readmissions across 97 inpatient encounters. The COVID-era low was 6.0% in 2021. The current post-pandemic stabilization at 10.9% is the finding that drives Recommendation 3.

---

**Average Inpatient Length of Stay — ~60 hours (blended 2018–2024)**
Average of length_of_stay_hrs in the `Encounters` table filtered to encounter_type = 'Inpatient'. The blended figure across the full period is approximately 60 hours but the year-level breakdown tells a more meaningful story:

- Pre-COVID baseline (2018–2019): 54 hours
- COVID peak (2021): 78 hours — a 43% surge
- Post-COVID (2022–2024): ~55 hours — stabilized but still 2% above pre-pandemic baseline

I also built a Tableau calculated field to track each year's LOS relative to the 2018 baseline:
`(AVG([Length Of Stay Hrs]) - 53.7) / 53.7`
This drives the tooltip on the LOS line chart and gives the executive a percentage change frame rather than requiring them to mentally compare absolute hour values year over year.

---

**Uninsured Cost Premium — Inpatient — 69%**
($42,768 - $25,254) ÷ $25,254. The $42,768 is the average base_encounter_cost for inpatient encounters where payer_type = 'Uninsured'. The $25,254 is the same figure for insured inpatient encounters (Government and Commercial combined). Both sourced from the `Encounters` table.

The 69% premium is one of the most striking findings in the Clinical Operations section. It means uninsured patients not only collect at a fraction of the insured rate — they also cost significantly more to treat, compressing the margin on every uninsured encounter from both ends simultaneously.

---

**Procedures Insured Rate — 74.4%**
12,270 ÷ 16,492. Filtered on insured = TRUE in the `Procedures` table. This measures coverage at the individual procedure level — not the encounter level. An insured patient can still have procedures that fall outside their plan's coverage, which is why this figure (74.4%) is lower than the share of encounters belonging to insured patients (84.4%).

Both figures are defensible depending on the question being asked. 74.4% answers: what share of individual procedures are actually covered? 84.4% answers: what share of procedures belong to patients who have insurance? I used 74.4% throughout the report because it's the more precise measure of actual coverage.

---

## Claims and Reimbursement

**Claim Denial Rate — 8.6% (blended)**
Count of claims where denial_status starts with 'Denied' ÷ total claim count. Built in Tableau as:
`SUM(IF STARTSWITH([Denial Status],'Denied') THEN 1 ELSE 0 END) / COUNT([Claim Id])`

The blended 8.6% is the headline figure but the encounter-type breakdown is where the real insight lives. Emergency encounters are denied at 19.7% — nearly 5x the Wellness rate of 3.7%. Inpatient follows at 16.0%. Every other encounter type falls between 6.2% and 11.1%. The denial problem is not evenly distributed.

Year-level trend: peaked at 9.1% in 2020, improving to 7.7% by 2024 — a positive direction that hasn't yet translated into full revenue recovery.

---

**Scatter Average Denial Rate Benchmark — 10.6%**
Simple average of the seven encounter type denial rates: (19.7 + 16.0 + 11.1 + 9.5 + 7.8 + 6.2 + 3.7) ÷ 7 = 10.57%, rounded to 10.6%.

This is specifically the reference line value used in the Denial Rate vs Collection Efficiency scatter plot — where each encounter type is treated as one equal data point regardless of volume. It is not the same as the 8.6% blended denial rate, which is volume-weighted across all 9,169 claims. Using these two figures interchangeably would be analytically incorrect and I want to be explicit about that distinction here.

---

**Average Days in A/R — 39.7 days (blended)**
Average of days_to_payment across all claims in the `Claims` table. Measures the time between claim submission and payment — a direct indicator of cash flow efficiency.

The blended figure masks the most important finding in this section:
- Uninsured: 119.9 days
- Commercial: 26.9 days
- Government: 22.6 days

The uninsured segment takes more than 5x longer to pay than government payers. At 15.9% of all encounters that gap creates a sustained cash flow drag that compounds throughout the fiscal year.

---

**Net Collection Rate — 75.7% (accepted claims) / 69.0% (all claims)**
Amount paid ÷ amount billed. The `Claims` table carries this as a pre-calculated field (net_collection_rate) at the claim level — I averaged it in Tableau across the relevant filter context.

The distinction between 75.7% and 69.0% matters. 75.7% is the rate on claims the hospital actually processed and collected on. 69.0% is what happens when written-off denials are pulled back into the denominator — it reflects what the hospital actually collected as a share of everything it billed. The 6.7 percentage point gap between those two figures is the measurable cost of the denial volume.

By payer, the spread runs from Medicare at 79.6% down to No Insurance at 25.4% — a 54 percentage point gap that no amount of billing optimization closes without addressing the underlying coverage issue.

---

## The Revenue Diagnosis

**Cost to Collection Ratio — by encounter type**
SUM(amount_paid) ÷ SUM(base_encounter_cost). Built in Tableau as:
`SUM([Amount Paid]) / SUM([Base Encounter Cost])`

This is the metric that connects the clinical story to the financial one. It measures how many cents the hospital collects for every dollar it spends delivering care — not as a share of what was billed, but as a share of what the care actually cost. That distinction makes it a more honest measure of financial efficiency than collection rate alone.

The encounter-type breakdown is the analytical centerpiece of Act 3:

| Encounter Type | Collection Ratio |
|---|---|
| Wellness | 81.0% |
| Elective Procedure | 78.8% |
| Ambulatory | 78.4% |
| Urgent Care | 76.8% |
| Outpatient | 76.6% |
| Inpatient | 67.5% |
| Emergency | 65.7% |

Emergency and Inpatient are the only two encounter types below the 75% blended benchmark — and they're also the two with the highest denial rates. That convergence in the scatter plot is the core diagnostic finding of the report.

---

**Total Denied Claims — 788**
Count of claims where denial_status starts with 'Denied' — including both Denied - Written Off and Denied - Recovered. The denial reason breakdown across these 788 claims is where the resolution pathway splits:

| Denial Reason | Count | Share | Resolution |
|---|---|---|---|
| Medical Necessity Not Established | 250 | 31.7% | Clinical |
| Prior Authorization Required | 193 | 24.5% | Clinical |
| Coding or Documentation Error | 157 | 19.9% | Administrative |
| Patient Not Covered / Eligibility Issue | 111 | 14.1% | Administrative |
| Duplicate Claim | 77 | 9.8% | Administrative |

56.2% of denials require physician involvement to resolve. 43.8% can be actioned by the billing team directly. That split determines who needs to be in the room to address the denial problem — and it's the basis for Recommendation 2.

---

**Revenue Written Off — $2,575,189**
SUM(amount_billed) where denial_status = 'Denied - Written Off'. This is the dollar value of claims that were denied and never resubmitted — the realized financial cost of the hospital's denial volume. Built in Tableau as:
`SUM(IF [Denial Status]='Denied - Written Off' THEN [Amount Billed] ELSE 0 END)`

---

**Resubmission Rate — 44.7%**
Count of denied claims where resubmission_flag = TRUE ÷ total denied claims. Built in Tableau as:
`SUM(IF [Resubmission Flag]=TRUE THEN 1 ELSE 0 END) / SUM(IF STARTSWITH([Denial Status],'Denied') THEN 1 ELSE 0 END)`

44.7% means the hospital pursued recovery on fewer than half of its denied claims. The other 55.3% were written off without a resubmission attempt — which is the gap the $713K recovery opportunity is built on.

---

**Recovery Rate — 61.9%**
Count of claims with denial_status = 'Denied - Recovered' ÷ count of resubmitted claims. Built in Tableau as:
`SUM(IF [Denial Status]='Denied - Recovered' THEN 1 ELSE 0 END) / SUM(IF [Resubmission Flag]=TRUE THEN 1 ELSE 0 END)`

61.9% of the claims SDRMC chose to resubmit were ultimately paid. That's a strong recovery rate — the hospital is effective at recovering claims when it pursues them. The problem is it only pursues 44.7% of them.

---

**Recoverable Opportunity — $712,537**
This is the most methodologically important metric in the report and the one most likely to be probed in a technical review — so I want to be explicit about exactly how I arrived at it.

The calculation: $2,575,189 (written-off claim value) × 44.7% (historical resubmission rate) × 61.9% (historical recovery rate) = $712,537.

Built in Tableau as:
`SUM(IF [Denial Status]='Denied - Written Off' THEN [Amount Billed] ELSE 0 END) * 0.447 * 0.619`

The core assumption is that written-off claims are as recoverable as the denied claims SDRMC already chose to resubmit — and that the same 61.9% recovery rate would hold on those claims. That may not be true for every denial reason or payer, which is why I characterize this as a directional estimate rather than a guaranteed recovery amount. The methodology is disclosed fully in the Assumptions and Caveats section of the README.

What makes this figure credible despite the assumption is that it's entirely grounded in SDRMC's own track record. I'm not projecting an industry benchmark or an aspirational target — I'm applying what the hospital has already demonstrated it can do to the claims it hasn't yet tried to recover.

---

*For raw field definitions across all four source tables see `data_dictionary.md`. For the full analytical narrative and recommendations see the README.*
