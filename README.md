# Kalpa Health: Where Is the Business Short of Plan, and Why?

**A plan-vs-actual, root-cause, billing-reconciliation, no-show and campaign analysis for a six-city diagnostics network (Jan–Jun 2026 vs Jan–Jun 2025).**

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/OmRohamare/Kalpa-Medical-Study/blob/main/Notebook/data_analysis.ipynb)

Reference case: *Dr Lal PathLabs: Sustaining Growth in a Changing Diagnostic Landscape* (IIM Ahmedabad, IIMA/ECO0372).
Primary notebook: [`Notebook/data_analysis.ipynb`](Notebook/data_analysis.ipynb)

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Business Context and Problem Statement](#2-business-context-and-problem-statement)
3. [Project Overview](#3-project-overview)
4. [Reference Case Study and Project Alignment](#4-reference-case-study-and-project-alignment)
5. [Objectives and Analytical Questions](#5-objectives-and-analytical-questions)
6. [Dataset Overview and Data Dictionary](#6-dataset-overview-and-data-dictionary)
7. [Technology Stack](#7-technology-stack)
8. [Methodology and Analytical Workflow](#8-methodology-and-analytical-workflow)
9. [Key Metrics and KPIs](#9-key-metrics-and-kpis)
10. [Analysis Performed](#10-analysis-performed)
11. [Key Output Tables](#11-key-output-tables)
12. [Key Findings and Business Insights](#12-key-findings-and-business-insights)
13. [Business Recommendations](#13-business-recommendations)
14. [Results and Business Impact](#14-results-and-business-impact)
15. [Repository Structure](#15-repository-structure)
16. [Installation and Setup](#16-installation-and-setup)
17. [How to Run the Project](#17-how-to-run-the-project)
18. [Limitations and Assumptions](#18-limitations-and-assumptions)
19. [Future Enhancements](#19-future-enhancements)
20. [References and Acknowledgements](#20-references-and-acknowledgements)
21. [Author and Contact](#21-author-and-contact)

---

## 1. Executive Summary

**Business problem.** Kalpa Health runs diagnostic clinics and home-collection services in six cities. Leadership could see that the 2026 test-volume plan was not being met but could not say *which part of the business* was short, or why. Three further questions sat alongside it: billing and collections disagreed, one clinic looked very poor on no-shows, and marketing asked for its offer budget to be doubled on a lift it had measured itself.

**Approach.** One notebook loads and cleans the merged data pack (568,007 bookings; 470,776 invoices; 473,507 payments) and answers the questions in two stages: *where* the shortfall is (Part 1) and *why* (Part 2), followed by three focused briefs on billing (C), a clinic's no-shows (D) and the marketing offer (E). Part 4 exports 23 tidy tables for a Tableau dashboard.

**Evidence-backed findings** (all figures from the notebook's saved outputs; "booked tests" = test lines on non-rescheduled bookings):

- **The network booked 458,002 tests in Jan–Jun 2026 against a plan of 515,740 (88.8% attainment, a gap of 57,738 tests).** Growth was +5.0% year on year; the plan implied +18.2%. All 36 city-month cells were below plan (best: 98.6%).
- **The gap is broad but not even.** Chennai, Bengaluru and Jaipur account for 65.3% of it. Jaipur is the outlier: 76.8% attainment, −10.9% year-on-year growth, and below 90% of plan in all six months.
- **Even the best-performing cities missed a plan that asked for 16–20% growth.** The four cities not in trouble grew 7.4–10.6%. The plan asked each city for one fixed growth rate in every month (standard deviation across months: 0.0), and attainment slipped slightly over the window (90.9% in the first two months, 87.9% in the last two).
- **Chennai and Jaipur have a patient-acquisition problem, not a retention problem.** Retention is 32–34% in every city, but new patients per 100 prior-year active patients are 29.2 (Chennai) and 26.5 (Jaipur) against 46.4–47.8 elsewhere. New-patient signups were down 17–39% year on year in every month of 2026 in these two cities.
- **Clinic volume fell in every city while home volume rose.** Clinic tests were down 6–25% and home tests up 16–43% by city. The Q1 2026 home-collection offer reached 100% of home bookings, so there was no untreated comparison group.
- **Capacity, lead time, post-booking leakage and the new booking-system cutover are not supported as main causes** (with the caveats in [Section 18](#18-limitations-and-assumptions)).
- **Billing vs settlement (Brief C).** Rs 73.70 crore was billed and Rs 69.57 crore settled. The Rs 4.13 crore difference reconciles fully (unexplained: Rs 0.00): unpaid invoices (Rs 3.48 crore, 4.63% of invoices), fees and tax on settlements (Rs 1.68 crore), partly offset by captured payments with no matching invoice (Rs 0.73 crore) and duplicate captures (Rs 0.30 crore).
- **Clinic CHE-03 no-shows (Brief D).** 17.2% against a 14.4% network rate; none of 20,000 simulated average clinics of the same size was this bad. Patient mix explains part of it (expected 15.1%); data artefacts do not.
- **Marketing offer (Brief E).** The offer cost Rs 73.3 lakh. Compared with the post-offer trend, home gained about 22,942 tests but clinics lost about 18,328, leaving roughly 4,590 net extra tests: about Rs 1,597 of waiver per net extra test.

**Principal implications.** Re-base the plan with city- and channel-specific targets; focus diagnosis on acquisition in Chennai and Jaipur; chase unpaid and unmatched payments; investigate CHE-03 operationally; and test the offer with a control group before scaling it. Part 2 narrows candidate causes; **it cannot prove any one of them**.

---

## 2. Business Context and Problem Statement

**Setting.** Kalpa Health serves patients through clinics (walk-in, app and phone bookings) and home collection in Ahmedabad, Bengaluru, Chennai, Hyderabad, Jaipur and Pune (31 clinics in total). The 2026 operating plan is a monthly target for *booked test volume* per city (`test_volume_target_2026`), covering January to June 2026.

**Why it matters.** A network-level shortfall of roughly 11% against plan (88.8% attainment) is a headline number, not a diagnosis. Corrective action differs completely depending on whether the problem is plan realism, patient acquisition, retention, visit frequency, test mix, capacity, channel shift, a data or system change, or leakage after booking.

**Decisions this analysis supports.**

- Whether to re-base the 2026 plan, and how (by city, by channel).
- Where to point acquisition and clinic-level effort.
- Whether finance should chase unpaid invoices and unmatched payments.
- Whether a specific clinic's no-show rate warrants an operational intervention.
- Whether the home-collection offer budget should be doubled.

**Scope of the current implementation.** The notebook analyses one data pack for one network over Jan 2025 – Jun 2026 (bookings run to 4 Jul 2026). It does **not** study market entry, acquisitions, pricing strategy, profitability or imaging expansion, which are the strategic questions of the reference case (see [Section 4](#4-reference-case-study-and-project-alignment)).

---

## 3. Project Overview

| Item | Detail |
|---|---|
| **Purpose** | Locate the shortfall against the 2026 volume plan, test candidate causes, and answer three adjacent business briefs (C, D, E) with the same cleaned data |
| **Main workstreams** | (A) Plan vs actual, channel gap, revenue drivers, funnel, rupee value of the gap, city ranking; (R) root-cause drill-down R1–R10; (C) billing-to-settlement bridge; (D) clinic no-show diagnosis; (E) offer lift and cost; (Export) tidy tables for Tableau |
| **Outputs** | Printed result tables in the notebook (the notebook produces no charts) and 23 CSV exports prefixed `p1_`–`p4_` for a four-page dashboard |
| **Audience** | Business and operations leaders; finance; marketing; analytics reviewers |
| **What it is not** | A forecasting model, a causal study or a production system. No model is trained and no deployment is claimed |

The notebook is written in a deliberately simple style (flat small cells, plain DataFrame variables, no classes or helper functions). Scope cuts relative to an earlier, more complex version are listed in the notebook's opening cell: no `Report` class, a simple growth-% split instead of a log-mean (Divisia) decomposition, and a simpler plan-realism test than a fitted trend.

---

## 4. Reference Case Study and Project Alignment

**Reference.** Jain, T., Manchanda, O., & Sam, S. (2026). *Dr Lal PathLabs: Sustaining Growth in a Changing Diagnostic Landscape* (Case IIMA/ECO0372). Indian Institute of Management Ahmedabad. Prepared by Professor Tarun Jain (IIM Ahmedabad), Dr Om Manchanda (former Managing Director, Dr Lal PathLabs) and Ms Shreya Sam (Research Assistant, IIM Ahmedabad). © 2026 IIM Ahmedabad; the case is a class-discussion case and is cited here, not reproduced.

**What the case is about.** By 2026 Dr Lal PathLabs (DLPL) was one of India's largest diagnostic networks, built on a hub-and-spoke model with a franchise ecosystem and home collection. The case poses a strategic dilemma: grow organically, inorganically, or in a sequenced hybrid; serve both prescription-driven and self-initiated (preventive, digital) demand; and decide whether to stay a pathology specialist or integrate imaging (CT, MRI). It contains no prescribed answer.

**Important:** the case contains no dataset used in this notebook. Kalpa Health is a different organisation with its own data pack; the case informs the *business framing and the choice of questions*, not the data or the results.

| Dimension | Reference case study (DLPL) | Current project (Kalpa Health) |
|---|---|---|
| Business context | National pathology network; hub-and-spoke labs with patient service centres and home collection; strong in north and east India, weaker in west and south | Six-city network of 31 clinics plus home collection (Ahmedabad, Bengaluru, Chennai, Hyderabad, Jaipur, Pune) |
| Business problem | Strategic: how to sustain growth (organic vs inorganic expansion; prescription vs self-initiated demand; pathology vs integrated imaging) | Operational: which part of the business is short of a 2026 volume plan, and why; plus billing, no-show and campaign questions |
| Analytical approach | Narrative case analysis with exhibits; no quantitative method prescribed | Descriptive analytics on transaction data: attainment, driver decomposition, patient-flow groups, peer gaps, difference-in-differences, bridge reconciliation, simulation test, period comparison |
| Metrics and KPIs | Revenue, EBITDA % and PAT % (Exhibit 2); revenue share by geography (Exhibit 3); labs, PSCs, pick-up points, patients, samples (Exhibit 4); market size and CAGR (Exhibits 5–6) | Booked tests, plan attainment, patients, bookings per patient, tests per booking, invoiced revenue, no-show rate, settled cash, offer cost per net extra test (see [Section 9](#9-key-metrics-and-kpis)) |
| Findings | Qualitative: DLPL's scale, margins and hub-and-spoke advantages; new patient acquisition described as hard | Independently observed from Kalpa data ([Section 12](#12-key-findings-and-business-insights)) |
| Business implications | A dilemma left open for discussion | Evidence-based recommendations with stated validation needs ([Section 13](#13-business-recommendations)) |

**Concepts that informed the project (conceptual similarity only).**

- *Home collection as a volume driver.* The case describes home collection as a driver of volumes and brand visibility. The project measures clinic vs home volumes and tests a home-collection offer (Brief E).
- *Difficulty of reaching new patients.* The case quotes leadership on new-patient acquisition being hard. The project's most distinctive finding is an acquisition gap in two cities (Finding 4). This is a thematic echo, not validation of the case.
- *Patients and samples as operating KPIs.* The case reports patients and samples (Exhibit 4). The project works with patients and booked tests, at city level and over six-month windows.
- *Digital booking and consumer-driven demand.* The case discusses app-based booking and self-initiated testing. The project distinguishes booking `source` (walk-in, app, phone) and examines a booking-system cutover, but does not model self-initiated vs prescription demand.

**Adapted, simplified or out of scope.**

| Case theme | Status in this project |
|---|---|
| Organic vs inorganic geographic expansion | Not examined |
| Prescription vs self-initiated demand | Not examined (no prescription indicator in the notebook) |
| Pathology vs imaging (CT/MRI) | Not examined (data contains test codes, not modalities) |
| EBITDA, PAT, margins | Not calculated (revenue only; no cost data) |
| Competitors, pricing, regulation | Not testable (no such data, as noted in the notebook) |
| Hub-and-spoke network design | Not modelled; `clinic_has_lab` exists in the data but is not analysed |

**Comparability.** DLPL reports 28.8 million patients and 85.6 million samples in FY25 (Exhibit 4); this project covers roughly 98,000 active patients and 458,000 booked tests over six months. The two are not comparable in scale, definition or period, and no result of one validates or contradicts the other.

---

## 5. Objectives and Analytical Questions

| # | Business question | Answered by |
|---|---|---|
| 1 | Which cities and months are short of plan, and by how much? | A1–A3, A8 |
| 2 | Is the shortfall in clinic or home volume? | A4, R5 |
| 3 | Why did revenue grow faster than volume? | A5 |
| 4 | Do booked tests leak after booking (no-shows, uncollected, uninvoiced)? | A6 |
| 5 | What is the volume gap worth in rupees (indicative)? | A7 |
| 6 | Was the plan itself realistic? | R1 |
| 7 | Which lever fell short: patients, visit frequency or tests per booking? | R2, R4 |
| 8 | Is the issue acquisition, retention or returning patients? | R3, R3b |
| 9 | Are capacity, lead time, clinic closures or test mix responsible? | R6, R7, R10 |
| 10 | Did the new booking system ("nova") cutover hurt volumes, or distort patient counts? | R8, R9 |
| 11 | By how much do billing and collections disagree, and is the gap timing or permanent? | C1–C6 |
| 12 | Is one clinic's no-show rate bad luck, patient mix, or a data artefact? | D1–D6 |
| 13 | Did the Q1 2026 offer create tests the network would not otherwise have had, and at what cost? | E1–E5 |

---

## 6. Dataset Overview and Data Dictionary

The notebook reads seven compressed CSV files from a `merged/` folder (in this repository: `data/merged/`). Row counts below are as printed by the notebook; row-level data (which includes patient identifiers) is deliberately not reproduced in this README.

| Dataset / Table | Grain and size | Key columns used | Purpose | Business significance |
|---|---|---|---|---|
| `plan_long.csv.gz` | City × month plan targets, Jan–Jun 2026 (6 cities × 6 months; no missing cell) | `city`, `metric` (`test_volume_target_2026`), `month`, `target` | The plan: **booked** tests per city-month | Benchmark for all attainment and gap calculations |
| `bookings.csv.gz` | One row per booking; 568,007 rows × 23 columns; 0 duplicate `booking_id` | `booking_id`, `city`, `clinic_id`, `channel` (clinic / home), `source` (walkin / app / phone), `patient_id`, `created_at`, `slot_at`, `source_system` (legacy / nova), `rescheduled_from`, `patient_signup_date`, `clinic_slots_per_day` | Demand: who booked, where, how, when | Basis of booked volume, patient flow, capacity, lead time |
| `booking_lines.csv.gz` | One row per test on a booking | `booking_id`, `test_code` (1,311 lines have no code) | Counts tests per booking and test mix | Plan is on a booked-test (line) basis |
| `collections.csv.gz` | One row per sample-collection outcome | `booking_id`, `status` (`collected` / `no_show`), `phlebotomist_id` | Post-booking outcome | No-show and funnel analysis. Not to be confused with payment collections |
| `invoices.csv.gz` | One row per invoice; 470,776 rows (unique `invoice_id`) | `invoice_id`, `booking_id`, `city`, `channel`, `invoice_date`, `amount`, `patient_id` | Billing | Revenue and billing side of the bridge |
| `payments.csv.gz` | One row per payment attempt; 473,507 rows (455,469 captured; 18,038 failed) | `payment_id`, `invoice_id` (cleaned), `invoice_id_raw`, `booking_id`, `payment_status`, `method`, `amount_rs`, `created_at`, `settlement_id`, `settled_at`, `settlement_amount_rs`, `fees_rs`, `tax_rs` | Money actually received and settled | Collections side of the bridge |
| `campaigns.csv.gz` | One row per campaign booking; 48,869 rows × 7 columns | `booking_id`, `waiver_rs` | Offer `HC-FREE-2026Q1` (Rs 150 home-collection fee waived, 1 Jan – 31 Mar 2026) | Cost and reach of the offer (Brief E). The file is optional in the code |

**Files in the repository that this notebook does not use:** `data/merged/orphans_*.csv.gz` and the raw `data/datapack/` subfolders `patients`, `results` and `reference`. See `data/datapack/README.md` and `data/datapack/MANIFEST.json` for the data pack's own documentation, and `datadictionary/` for the cleaned and uncleaned data dictionaries.

### Key derived variables (created in the notebook)

| Variable | Definition |
|---|---|
| `tests` | Number of `booking_lines` rows for a booking (0 if none; none occur) |
| `month` | Month of `slot_at` (service date) by default; `BASIS` can be switched to `created_at` |
| `superseded` | `True` if the booking id appears in another booking's `rescheduled_from` (1,500 bookings). Removed from booked volume because the replacement carries the same tests |
| `coll_status` | `collected`, `no_show`, or `unknown` (no collection record, 3.2% of non-superseded bookings). `unknown` is never treated as a no-show |
| `period` | `current` (Jan–Jun 2026), `prior` (Jan–Jun 2025) or `other` |
| `compare_month` | For a prior row, the current month it lines up with (2025-01 → 2026-01) |
| `lead_days` | Days between `created_at` and `slot_at` |
| `campaign` | `True` if the booking is in the campaigns file |
| `group` (patients) | `retained`, `lost`, `new`, `returning_inactive_prior` (rules in [Section 8](#8-methodology-and-analytical-workflow)) |

### Data-quality observations (from the notebook's own checks)

| Check | Result |
|---|---|
| Duplicate `booking_id` | 0 of 568,007 |
| Test lines without a booking / bookings without lines / invoices without a booking | 0 / 0 / 0 |
| Lines with missing `test_code` | 1,311 (kept in volume; labelled `MISSING` in test mix) |
| Bookings with no collection record | 3.2% (labelled `unknown`) |
| Plan cities vs booking cities | Match exactly |
| Last plan month complete in bookings | Yes (latest `slot_at` is 2026-07-04) |
| Payments whose `invoice_id` is not in invoices (all with no `booking_id`) | 4,765 |
| Payment rows whose raw invoice id differs from the cleaned id | 139,620 (format differences such as `INV2025000002`; a naive join on the raw id would lose these) |
| Negative lead times | Present: the minimum `lead_days` is −2.0 (not corrected; see limitations) |
| Source-system schema | `patient.is_new` and `status_at_export` are populated for exactly 26,543 rows and `patient_name` is missing for exactly 26,543 rows, the same count as `nova` bookings (the nova rows follow a different schema; row-by-row overlap was not checked) |

---

## 7. Technology Stack

| Technology | Role in this project |
|---|---|
| **Python 3** | Language of the whole analysis |
| **pandas** | Loading `.csv.gz` files, cleaning, `merge`, `groupby`, `pivot_table`, date handling, all result tables |
| **NumPy** | `np.where` labelling, binomial simulation (`np.random.binomial`, seed 42), percentiles |
| **pathlib** (standard library) | File paths to the data folder |
| **Jupyter Notebook / Google Colab** | Execution environment. The notebook's first code cell mounts Google Drive via `google.colab`, and its saved outputs come from a Colab run |
| **Tableau** (downstream, not in the notebook) | The Part 4 exports are designed for a four-page Tableau dashboard. No Tableau workbook was among the files reviewed for this README |

No plotting library, statistics package or machine-learning library is imported. The notebook does not train or evaluate any model.

---

## 8. Methodology and Analytical Workflow

The workflow follows the notebook's order. Stages that the notebook does not implement (for example model training or formal hypothesis tests beyond a simulation) are intentionally absent.

### Part 0. Setup and cleaning

| Stage | What was done | Why |
|---|---|---|
| Load | Seven `.csv.gz` files read directly; `patient_phone` kept as text so no digit is lost; campaigns file optional | Avoid unzipping and phone-number corruption |
| Clean text keys | `str.strip()` on `month`, `city`, `booking_id` in every table | A hidden space silently breaks joins |
| De-duplicate | `drop_duplicates("booking_id")` on bookings and collections (0 duplicates removed from bookings) | One row per booking |
| Parse dates and amounts | `pd.to_datetime(..., errors="coerce")`, `pd.to_numeric(..., errors="coerce")`; invoices with a bad date or amount dropped (0 dropped) | Bad values become blanks rather than errors |
| Supersede reschedules | Flag every id found in `rescheduled_from` (1,500). Exclude from booked volume | The replacement booking carries the same tests, so counting both double-counts |
| Tests per booking | `groupby("booking_id").size()` on lines, merged onto bookings | Plan is on booked tests, not invoiced tests |
| Collection status | Left-join; missing → `unknown` | `unknown` ≠ collected ≠ no-show |
| Reschedule pairs | For the funnel only, replacements inherit their original's status and invoice flag | The visit and invoice sit on the original, so a pair counts once |
| Checks | Orphan lines/invoices, zero-line bookings, missing test codes, share with no collection record, city match between plan and bookings, last plan month completeness | Fail early on silent data problems |

**Analysis setup.** `cur_months` are the plan months (2026-01…06); `prior_months` are the same months a year earlier. `df_booked` (superseded originals removed) and `df_compare` (prior and current rows only) feed almost every table. Totals are computed as a ratio of totals, not an average of city percentages, and distinct patients are counted once in TOTAL rows by stacking a `city = "TOTAL"` copy of the data before the `pivot_table`.

### Part 1. Locate the shortfall (A1–A8)

Attainment = booked ÷ plan, by city, by city-month and for the network. Channel gap uses a pro-rata assumption (the plan has no channel split). Revenue growth is split into growth of patients, tests per patient and revenue per test (they multiply to total growth). A funnel assigns each booked test to exactly one outcome. The volume gap is valued in rupees using each city's booked-to-invoiced conversion and revenue per invoiced test. Cities are ranked by test gap and by growth gap.

### Part 2. Root-cause drill-down (R1–R10)

Each branch states what the data shows **and what it cannot show**.

| Branch | Method |
|---|---|
| R1 plan realism | Plan growth by city and month; correlation of plan growth with actual growth across the six cities; attainment of first two vs last two months |
| R2 levers | Booked tests = patients × bookings per patient × tests per booking. Growth of each lever, and how much each lever alone would need to rise to close the gap |
| R3 patient flow | Each patient labelled **retained** (tests in both windows), **lost** (prior only), **new** (current only and signed up on or after 1 Jan 2026) or **returning_inactive_prior** (current only, signed up earlier). Rates per 100 prior-year active patients. Signup month falls back to first booking date if blank |
| R4 peer gap | City value minus the median of the *other* five cities, for each metric |
| R5 channel/campaign | Clinic vs home year-on-year growth, first three months vs last three; share of home tests from campaign bookings |
| R6 clinics | Per-clinic growth, share of the city's decline, capacity use (bookings per day ÷ slots per day; mean and 95th percentile; "capacity-bound" if p95 ≥ 90%), opened/stopped flags |
| R7 test mix | Growth per `test_code`, and gap versus *uniform* plan growth (an assumption, as the plan has no per-test targets) |
| R8 cutover | Difference-in-differences: nova cities vs other cities, before vs after go-live (first nova booking month) |
| R9 patient identity | For nova cities: share of patient ids found in legacy; ids absent from legacy that claim a pre-go-live signup; ids whose phone matches a legacy patient (counts only) |
| R10 lead time | Median days from booking to service by city and month |
| R0 scorecard | One printed line per hypothesis plus a LIMITS line. Hypotheses to weigh, not findings |

### Part 3. Briefs C, D, E

- **C (billing vs collections).** Join check; per-invoice state (matched / underpaid / overpaid / multiple captures / unpaid); settlement batches collapsed to one row per `settlement_id` and tested for gross − fees − tax = net; a 13-step bridge from billed to settled rupees; monthly timing view; unpaid share by city, channel and payment method.
- **D (one clinic's no-shows).** No-show rate = no-show ÷ (no-show + collected), unknowns excluded; z-score against the network rate; a 20,000-draw binomial simulation at the network rate; expected-vs-observed rate from booking mix (channel × source × lead bucket × weekday); monthly and within-clinic breakdowns; data-artefact checks.
- **E (offer).** Reach and cost; tests per month by channel; lift measured three ways (home, clinic, network) as the difference between year-on-year growth during and after the offer; cost per net extra test; revenue per extra booked test; break-even share of new bookings.

### Part 4. Export

Twenty-three tidy CSVs (`p1_` plan vs actual, `p2_` billing vs collections, `p3_` clinic no-shows, `p4_` the offer) written at full precision to `tableau_exports/` next to the data. Rows with `is_total = 1` are network totals; `p2_billing_monthly` carries a `full_month` flag.

---

## 9. Key Metrics and KPIs

**Calculated in the notebook.** Unless stated, "current" = Jan–Jun 2026, "prior" = Jan–Jun 2025, and tests are booked test lines excluding superseded originals.

| Metric / KPI | Definition or formula | Analytical purpose | Business interpretation |
|---|---|---|---|
| Booked tests | Rows in `booking_lines` for non-superseded bookings, by `slot_at` month | Volume on the plan's own basis | Network: 436,302 prior → 458,002 current |
| Plan attainment % | Booked ÷ plan × 100 | Where the plan is missed | Network 88.8%; Jaipur 76.8%; Ahmedabad 93.7% |
| Actual growth % | Current ÷ prior − 1 | Year-on-year momentum | Network +5.0% |
| Plan growth % | Plan ÷ prior booked − 1 | What the plan asked for | Network +18.2%; 16–20% by city, constant across months |
| Growth gap (pp) | Actual growth − plan growth | Size of miss in growth terms | Network −13.2 pp; Jaipur −26.9 pp |
| Gap tests | Plan − booked | Absolute miss | Network 57,738 tests |
| Share of total gap % | City gap ÷ network gap | Concentration | Chennai 22.8%, Bengaluru 21.5%, Jaipur 21.0% |
| Months below 90% | Count of months with attainment < 90% | Persistence | Jaipur 6 of 6; Ahmedabad 1 of 6 |
| Pro-rata channel gap | Prior × (1 + city plan growth) − current, by city × channel | Indicative clinic/home split of the gap (assumes equal growth ask for both channels) | Clinics exceed 100% of the gap in 5 of 6 cities |
| Revenue (invoiced) | Sum of `invoices.amount` by invoice month | Commercial outcome | Rs 23.96 crore → Rs 26.83 crore (+12.0%) |
| Patients growth % | Distinct `patient_id` current ÷ prior − 1 (invoice basis in A5; booking basis in R2) | Customer-base driver | +4.2% (invoices), +3.7% (bookings) |
| Tests per patient growth % | Invoiced tests per invoiced patient | Depth driver | +1.4% |
| Revenue per test growth % | Revenue ÷ invoiced tests | Price/mix driver | +6.0% |
| Frequency growth % | Bookings per patient, current vs prior | Visit-frequency lever | −1.0% |
| Basket growth % | Tests per booking, current vs prior | Basket-size lever | +2.2% |
| Funnel outcome share % | Share of booked tests ending as `collected_invoiced`, `no_show`, `unknown_invoiced`, `unknown_not_invoiced` | Post-booking leakage | Current: 83.4% / 13.6% / 0.1% / 3.0% |
| Value of gap (Rs crore) | Gap tests × booked-to-invoiced conversion × revenue per invoiced test, per city | Indicative cost of the miss | About Rs 3.39 crore. Assumes missing tests convert and earn like average tests |
| Retention rate % | Retained ÷ prior-year active patients | Retention | 32.3–34.0% across cities (same-window return rate) |
| New per 100 prior active | New patients ÷ prior-year active × 100 | Acquisition intensity | Chennai 29.2, Jaipur 26.5; other cities 46.4–47.8 |
| Peer gap | City metric − median of other cities | Relative weakness | Chennai: −13.1 pp patients growth, −17.6 new per 100 |
| Capacity use | Daily clinic bookings ÷ `clinic_slots_per_day` (mean, p95) | Whether clinics are full | 6 of 31 clinics have p95 ≥ 90% in either period |
| Median lead days | Median of `slot_at − created_at`, by city and month | Slot scarcity signal | About 2.4–3.1 days throughout |
| DiD (pp) | (Nova cities' pre→post YoY change) − (other cities' change) | Cutover signal (descriptive) | +1.9 pp |
| Invoice state shares | matched / unpaid / multiple captures / over- / underpaid | Billing quality | 94.9% matched; 4.6% unpaid |
| Billed-to-settled bridge | 13 steps from billed to settled rupees | Reconcile billing with cash | Rs 73.70 crore → Rs 69.57 crore, unexplained Rs 0.00 |
| Fee rate % | Fees ÷ gross settlement batch | Cost of collection | 2.0% in all 562 batches |
| Payment fail rate % | Failed ÷ attempts, by method | Payment leakage | 3.8–3.9% for every method |
| No-show rate % | No-show ÷ (no-show + collected) | Clinic quality | Network 14.4% |
| z vs network | (Clinic rate − network rate) ÷ binomial standard error | Is a clinic's rate unusual? | CHE-03: 10.86 |
| Expected rate from mix | Mean of segment-level network no-show rates over the clinic's bookings | Mix adjustment | CHE-03: 15.1% expected vs 17.2% observed |
| Offer lift (pp) | YoY growth during offer − YoY growth after offer | Offer effect vs trend | Home +30.4 pp; clinic −12.6 pp; network +2.1 pp |
| Net extra tests | Prior-year Q1 tests × lift | What the offer added | About 4,590 (network) |
| Cost per net extra test | Waiver cost ÷ net extra tests | Offer efficiency | About Rs 1,597 |
| Break-even new-booking share | Waiver per booking ÷ revenue per home booking | Offer hurdle | At least 12% of waived bookings must be genuinely new |

**Discussed in the notebook or reference case but not calculated:** EBITDA, PAT or any margin; cost of service; marketing spend; price per test as a separate series; market share; patient lifetime value; revenue by geography; labs/PSC/pick-up-point counts. **Proposed future metrics** are in [Section 19](#19-future-enhancements).

**Definition cautions.** (1) The funnel's 13.6% no-show is a share of *booked tests* (current period); Brief D's 14.4% is a share of *bookings with a known outcome* across all 18 months. They answer different questions. (2) A5 uses invoiced patients and tests; R2 uses booked patients and tests. (3) Revenue per invoiced test is about Rs 700 in A7 (current period, by city) and Rs 676 in E4 (all invoices matched to bookings).

---

## 10. Analysis Performed

### 10.1 Plan vs actual (A1–A3, A8, R1)

- **Question.** Where, and when, is the plan missed? Was it realistic?
- **Method.** Attainment by city, by city-month and for the network; ranking by test gap and growth gap; plan growth by month; correlation across cities; first-two vs last-two month attainment.
- **Findings.** Network 88.8% attainment (458,002 vs 515,740). By city: Ahmedabad 93.7%, Hyderabad 91.0%, Pune 90.9%, Bengaluru 89.7%, Chennai 86.0%, Jaipur 76.8%. Network attainment by month ranged 87.1–92.8%. No city-month reached 100%. Plan growth was constant within each city (16, 17, 18, 18, 19, 20%). Network attainment fell from 90.9% (first two months) to 87.9% (last two); Chennai −9.5 pp and Bengaluru −6.0 pp, Jaipur +6.2 pp. The correlation between plan growth and actual growth across the six cities was 0.79.
- **Interpretation.** The plan looks top-down (a flat percentage per city) and above what any city delivered. The 0.79 correlation rests on six points and leans on Jaipur, which had both the lowest planned and the lowest actual growth (dropping Jaipur lowers it to 0.53, a derived check on five points). It does not show that the plan tracked city reality.
- **Relevance.** Supports re-basing the plan and separating "plan too high" from "city under-delivering".

### 10.2 Channel view (A4, R5)

- **Method.** City × channel growth against a pro-rata plan; year-on-year clinic and home growth in the first and last three months; campaign share of current home tests.
- **Findings.** Clinic tests fell in all six cities (−5.9% to −24.7%); home tests rose (+15.7% to +42.7%). Network clinic growth improved from −18.1% (Jan–Mar) to −5.5% (Apr–Jun), while home growth slowed from +52.4% to +22.0%. All 115,028 current-period home tests in Jan–Mar came from campaign bookings; none in Apr–Jun (89,853 home tests).
- **Interpretation.** The shortfall is a clinic-volume shortfall partly masked by home growth. The channel-split plan gap is an *indication only* because the plan has no channel targets.

### 10.3 Revenue, funnel and value of the gap (A5–A7)

- **Findings.** Revenue grew +12.0% against +5.0% booked tests. Jaipur revenue fell 4.0%; every other city grew 7.4–17.2%. Revenue per test rose 5.3–6.8% in every city. About 83.4% of booked tests end as collected and invoiced, with city shares within about 0.8 pp of each other, and the no-show share fell 0.6 pp year on year. The 57,738-test gap is worth about Rs 3.39 crore (indicative), roughly 12.6% of current-period invoiced revenue (derived).
- **Interpretation.** Post-booking leakage is uniform and stable, so the shortfall is *upstream*, at the booking stage. Revenue growth is carried by price/mix per test, not volume. The notebook does not separate price from mix.

### 10.4 Root-cause drill-down (R2–R10)

| Branch | What the data shows | What it cannot show |
|---|---|---|
| R2 levers | Network: patients +3.7%, frequency −1.0%, basket +2.2%. Patients alone would need +12,369 extra active patients to close the gap. Chennai (−5.0%) and Jaipur (−12.2%) lose patients; the other four gain 7.1–9.7% | Why patients are not growing |
| R3 patient flow | 63,492 prior patients lost, 31,090 retained; 39,287 new and 27,743 returning-inactive. Retention 32.3–34.0% everywhere. New per 100 prior active: Chennai 29.2, Jaipur 26.5 vs 46.4–47.8 | Whether acquisition *worsened* (only 18 months of data) |
| R3b signups | New-patient signups vs the same month a year earlier were negative in every 2026 month for Chennai (−17.3% to −30.5%) and Jaipur (−29.4% to −38.8%) and positive for the other four | See limitation on signup-month counts |
| R4 peers | Chennai −13.1 pp and Jaipur −20.3 pp on patients growth versus peers; frequency and basket within about 5 pp | Causes |
| R6 clinics | 7 of 31 clinics declined (3 in Chennai, 4 in Jaipur); in Jaipur the top three declining clinics make up 85.1% of the city decline; 6 of 31 clinics capacity-bound (none in Jaipur); one new clinic (Jaipur); none stopped | Local reasons for decline |
| R7 test mix | PK01–PK04 grew 33.6–36.0%; the 20 T-coded tests moved −2.9% to +3.4% | What PK codes are (see Finding 7) |
| R8 cutover | Nova cities (Chennai, Jaipur) were already 9.8 and 9.9 pp behind other cities in Jan–Feb 2026, before the first nova booking in April. DiD +1.9 pp | Causal effect of the cutover (3 months pre/post only) |
| R9 identity | 28.5% of Chennai and 27.8% of Jaipur nova patient ids are not in legacy; 520 and 251 of these claim a pre-go-live signup; 0 share a phone with a legacy patient | Whether any patients were re-keyed (phone match is only one test) |
| R10 lead time | Median 2.4–3.1 days in every city and month, no upward drift | Capacity beyond booking lead time |

### 10.5 Brief C: billing vs collections

Billed Rs 737,020,069 (470,776 invoices) → settled Rs 695,674,389. The bridge, in rupees crore: billed 73.70; less unpaid invoices 3.48; plus duplicate captures 0.30; single-payment differences ≈ 0.00; = captured on known invoices 70.52; plus captured payments with no matching invoice 0.73; = all captured 71.25; captured-but-unsettled 0.00; less fees 1.42; less tax 0.26; = settled 69.57; unexplained 0.00. Of 562 settlement batches, none fails gross − fees − tax = net. Unpaid invoices are 4.4–4.8% in every city-channel cell. Payment failure rates are 3.8–3.9% in every method. The monthly billed-minus-captured difference was positive in 18 of 18 full months (Jan 2025 – Jun 2026), so the cumulative gap grew rather than netting out. June 2026 shows the largest monthly gap since January 2025 (Rs 48.9 lakh), while July 2026 captures Rs 30.4 lakh of earlier billing, consistent with a cut-off effect for the latest month.

### 10.6 Brief D: clinic no-shows

CHE-03 (Chennai): 3,136 no-shows out of 18,200 known outcomes = 17.2% (network 14.4%; z = 10.86; simulated 95% range for an average clinic of that size: 13.9–14.9%). Mix-expected rate 15.1%, leaving +2.1 pp unexplained. The excess was present in every month from January 2025 to June 2026 (gap +0.8 to +5.2 pp; smaller in 2026), with no step change, and across all six channel × source cells (+0.5 to +2.9 pp). CHE-03's unknown share (3.8%), superseded share (0.95%) and nova share (16.5%) are similar to its neighbours. Counting unknown as no-show would inflate the rate to 20.4%, which the notebook treats as the careless-analyst error. The second-highest raw rate (JAI-05, 17.9%) is a clinic with only 145 known outcomes (z = 1.21), so it is not distinguishable from chance.

### 10.7 Brief E: the offer

The offer waived Rs 150 on 48,869 bookings (Rs 7,330,350 = Rs 73.3 lakh) and covered 100% of home bookings in Jan–Mar 2026. Home share of tests was about 34% throughout 2025, 49% in Jan–Mar 2026 and 40% in Apr–Jun 2026. Year-on-year, home tests grew +52.4% during the offer and +22.0% after; clinic tests −18.1% and −5.5%; network +6.0% and +3.9%. Net extra tests against the post-offer trend: home +22,942, clinic −18,328, network about +4,590. Revenue per invoiced test Rs 676; 82.9% of booked tests are invoiced; revenue per extra booked test about Rs 560, so the net extra tests earn about Rs 25.7 lakh against a Rs 73.3 lakh waiver. A home booking carries 2.32 tests (about Rs 1,300 revenue), so at least 12% of waived bookings must be genuinely new to break even on revenue (revenue, not profit). By a derived calculation (4,590 ÷ 2.32 ÷ 48,869) the offer produced the equivalent of about 4% new bookings.

---

## 11. Key Output Tables

The notebook produces **tables only**; it contains no charts, so no image assets are referenced. The tables that carry the story:

| Table (variable) | Shows | Reading and caveat |
|---|---|---|
| `city_table` (A1) | Prior, current, plan, growth and attainment by city plus TOTAL | The anchor table. TOTAL is a ratio of totals |
| `attainment_cm` (A2) | Attainment % by city and month | Persistent weakness (Jaipur) vs month-specific dips (Chennai and Bengaluru in March) |
| `channel_table` (A4) | Clinic vs home growth and pro-rata gap by city | Uses an assumption; shares above 100% mean home over-delivered |
| `driver_table` (A5) | Revenue growth split into patients, tests per patient, revenue per test | Revenue and test growth differ because revenue per test rose about 6% |
| `share_current` / `share_change_pp` (A6) | Funnel shares and year-on-year change | Near-identical across cities |
| `rates` (R3) | Retention, new and returning per 100 prior active | The acquisition contrast between cities |
| `peer_gap` (R4) | City minus median of other cities | Highlights Chennai and Jaipur |
| `bridge` (C4) | 13-step billed-to-settled bridge | Fully reconciled |
| `state_table` (C2) | Invoice states: matched, unpaid, multiple captures | 94.9% matched |
| `clinic_ns` (D2) | No-show rate, unknown share and z by clinic | CHE-03 is the only clinic with a large z |
| `df_lift` (E3) | Lift with the offer measured for home, clinic and network | Net, not home-only, is the relevant number |

---

## 12. Key Findings and Business Insights

Findings are ordered by importance and strength of evidence. Every number comes from the notebook's saved outputs unless marked "derived".

### Finding 1: The shortfall is network-wide, and the plan was above what any city delivered

**Finding.** The network booked 458,002 tests against a plan of 515,740 (88.8%). No city-month reached plan.
**Evidence.** All 36 city-month attainment cells are below 100% (maximum 98.6%). Plan growth was a single fixed rate per city (16–20%) in every month. Actual growth: Ahmedabad +10.6%, Pune +8.1%, Bengaluru +7.6%, Hyderabad +7.4%, Chennai +0.6%, Jaipur −10.9%.
**Interpretation.** Even the four cities with healthy patient acquisition fell 7–12 pp short of their growth ask. Part of the shortfall is therefore the plan itself, independent of any operating problem.
**Business implication.** Judging city teams against these targets will mislabel healthy cities as failing. Targets need re-basing before performance conversations.

### Finding 2: Three cities carry two-thirds of the gap, and Jaipur is structurally weak

**Finding.** Chennai, Bengaluru and Jaipur account for 65.3% of the 57,738-test gap (derived from 13,140 + 12,424 + 12,136).
**Evidence.** Gap shares: Chennai 22.8%, Bengaluru 21.5%, Jaipur 21.0%. Jaipur has the lowest attainment (76.8%), the worst growth gap (−26.9 pp) and was below 90% in all six months, yet is only about 10% of the prior-year network (derived: 45,149 of 436,302).
**Interpretation.** Bengaluru's large gap mainly reflects a 20% growth ask on a large base; its growth is in line with peers (+0.2 pp vs the median of other cities). Chennai and Jaipur differ from peers.
**Business implication.** Rank by both absolute gap and growth gap; the two lists point to different priorities (Chennai/Bengaluru for volume, Jaipur for severity).

### Finding 3: Revenue grew more than twice as fast as volume, driven by revenue per test

**Finding.** Invoiced revenue rose 12.0% (Rs 23.96 crore → Rs 26.83 crore) while booked tests rose 5.0%.
**Evidence.** Patients +4.2%, tests per patient +1.4%, revenue per test +6.0%, with revenue per test up 5.3–6.8% in every city. Jaipur is the only city with falling revenue (−4.0%).
**Interpretation.** Commercial results look healthier than volume results, and price or mix per test is the main reason. The notebook does not separate price from test mix (see Finding 7 for a candidate).
**Business implication.** A volume miss is currently cushioned by realisation per test; that cushion may not persist, so the volume problem should not be dismissed because revenue is up.

### Finding 4: Chennai and Jaipur have an acquisition gap, not a retention gap

**Finding.** New patients per 100 prior-year active patients are far lower in Chennai (29.2) and Jaipur (26.5) than elsewhere (46.4–47.8). Retention is nearly identical everywhere (32.3–34.0%).
**Evidence.** Patients growth: Chennai −5.0%, Jaipur −12.2% vs +7.1% to +9.7% elsewhere. Signups versus the same month a year earlier were negative in every 2026 month in both cities (Chennai −17.3% to −30.5%; Jaipur −29.4% to −38.8%) and positive in the other four. Peer gap on patients growth: −13.1 pp (Chennai), −20.3 pp (Jaipur).
**Interpretation.** The differentiator is how many new patients arrive, not how many stay. At network level, closing the gap through patients alone would take about 12,369 extra active patients (the R2 sizing, with 39,287 new patients acquired in the window); the notebook does not size the Chennai- and Jaipur-specific requirement.
**Business implication.** Direct diagnosis at acquisition in these two cities (lead sources, local competitors, marketing reach). The data pack has no marketing-spend, competitor or price data, so causes cannot be tested here. The reference case also describes new-patient acquisition as difficult, a thematic parallel only.

### Finding 5: Clinic volume is falling everywhere while home volume rises

**Finding.** Clinic tests fell in all six cities and home tests rose in all six.
**Evidence.** Clinic growth −5.9% to −24.7%; home growth +15.7% to +42.7%. Against a pro-rata plan, clinics carry more than 100% of the city gap in five of six cities (Jaipur: 99.7%) and home is above its pro-rata plan in five of six. Seven clinics declined (three in Chennai, four in Jaipur); in Jaipur, three clinics make up 85.1% of the city's decline.
**Interpretation.** Home volume is covering part of a clinic shortfall. Whether individual patients switched channels is **not** supported by the patient-flow output: retained patients lost tests in *both* channels (clinic −65,931; home −12,467), so the notebook's "opposite signs" switching test is not met. At network level, the offer analysis shows clinic losses equal to about 80% of home gains against the after-offer trend (derived: 18,328 ÷ 22,942), consistent with substitution in aggregate.
**Business implication.** Plans, capacity and staffing should be set by channel, not city total.

### Finding 6: Post-booking leakage, capacity, lead time and the system cutover do not explain the gap

**Finding.** Four commonly suspected causes do not stand out in the data.
**Evidence.**
- *Leakage.* Each city has about 83% collected-and-invoiced, 13–14% no-show and about 3% unknown, and year-on-year shifts are about 1 pp or less.
- *Capacity.* 6 of 31 clinics have busy-day utilisation at or above 90% in either period, none in Jaipur. Two of Chennai's clinics qualify, but their utilisation fell year on year.
- *Lead time.* Median booking-to-service time stays about 2.4–3.1 days in every city and month. (The July 2026 figures for Chennai and Jaipur, 5.1 and 5.8 days, rest on a handful of bookings in a part-month.)
- *Nova cutover.* Chennai and Jaipur (the nova cities) were already 9.8–18.5 pp behind other cities in each of Jan–Mar 2026, before the first nova booking in April. The difference-in-differences is +1.9 pp (nova cities' growth fell by 0.8 pp after go-live against 2.7 pp elsewhere). None of the nova patient ids missing from legacy shares a phone number with a legacy patient.
**Interpretation.** None of these four branches explains why Chennai and Jaipur lag. The nova result is descriptive only: three months before and after, no randomisation.
**Business implication.** Resources should not go to capacity expansion or system rollback on this evidence. Unresolved: 771 nova patients (520 Chennai, 251 Jaipur) are missing from legacy but claim a pre-go-live signup, which a phone match did not resolve.

### Finding 7: Volume growth is concentrated in four PK-coded test lines

**Finding.** Four test codes (PK01–PK04) grew 33.6–36.0% year on year while the 20 T-coded tests were nearly flat (−2.9% to +3.4%).
**Evidence.** PK-coded lines: 54,112 → 72,913 (+34.7%, derived), which is 86.6% of the 21,700-test net increase (derived). Excluding PK lines, volume grew about +0.8% (derived: 382,190 → 385,089). The T-coded growth was negative in Chennai (−0.8% to −8.6% for most codes) and Jaipur (−6.3% to −22.6%) and mostly positive in the other four cities.
**Interpretation.** Almost all growth against a plan of +18.2% came from one product family, while core T-coded volume barely moved. The notebook does not define PK codes (new products, bundles, recoding of existing tests?). Whether PK growth substitutes for T-line volume, and how it relates to the 6% rise in revenue per test, is untested.
**Business implication.** Understanding the PK family is the highest-value follow-up for explaining both the volume miss and the price/mix cushion.

### Finding 8: Billed revenue and settled cash reconcile exactly, and the gap is mostly unpaid invoices

**Finding.** Rs 73.70 crore billed vs Rs 69.57 crore settled: a Rs 4.13 crore (5.6%) difference (derived), explained to the rupee.
**Evidence.** Unpaid invoices: 21,820 (4.63% of invoices; Rs 3.48 crore). Duplicate captures: 1,925 invoices, Rs 0.30 crore over-captured. Captured payments with no matching invoice (4,765 payments with no booking id): Rs 0.73 crore. Fees Rs 1.42 crore (a flat 2.0% in all 562 settlement batches) and tax on fees Rs 0.26 crore. All captured money is in a settlement. Unpaid shares are 4.4–4.8% in every city-channel cell; failure rates by payment method are 3.8–3.9%.
**Interpretation.** Because every city, channel and method looks alike, the leak is systemic rather than local. The monthly billed-minus-captured gap was positive in all 18 full months, so it is not simply timing, although the latest month includes cut-off effects. Fees and tax are expected deductions, not losses to chase.
**Business implication.** About Rs 3.5 crore of billed revenue has no captured payment; duplicates (possible refunds) and unmatched captures (possible mis-posted receipts) need review.

### Finding 9: CHE-03's no-show rate is real, partly mix, and not a data artefact

**Finding.** CHE-03 (Chennai) no-shows run at 17.2% against a 14.4% network rate.
**Evidence.** 3,136 of 18,200 known outcomes; z = 10.86; 0 of 20,000 simulated average clinics of the same size were as bad (their 2.5–97.5% range was 13.9–14.9%). Booking mix would predict 15.1%. The excess appears in all 18 months and all six channel-source cells. Unknown, superseded and nova shares resemble the neighbouring clinics.
**Interpretation.** "Bad luck" is rejected. Mix accounts for roughly 0.7 pp of the 2.8 pp excess (derived), leaving about 2.1 pp unexplained. The cause is not identifiable from the pack (no staffing, reminder or traffic data).
**Business implication.** CHE-03 merits an operational review. The home-visit phlebotomist table shows only the top eight of 59 individuals (16.0–19.3% no-show), so concentration in a few phlebotomists is not established.

### Finding 10: The offer's real lift is small relative to its cost, and there is no control group

**Finding.** On the network total, the Q1 2026 offer added roughly 4,590 tests at a waiver cost of Rs 73.3 lakh.
**Evidence.** Every home booking in the quarter received the offer. Network year-on-year growth was +6.0% during vs +3.9% after (+2.1 pp). Home +22,942 and clinic −18,328 tests against the after-offer trend. Cost per net extra test about Rs 1,597; revenue from those tests about Rs 25.7 lakh. Break-even needs at least 12% of waived bookings to be genuinely new (derived estimate of actual: about 4%).
**Interpretation.** A home-only lift (+52% growth) overstates the effect because much of it is clinic substitution. The estimate depends on using Apr–Jun growth as the counterfactual; home share stayed near 40% after the offer vs about 34% before, so the offer may have left a lasting channel shift that the baseline absorbs. The notebook does not reproduce marketing's 9% figure.
**Business implication.** The evidence does not support doubling the budget. Doubling cannot reach new home bookings (coverage was already 100%); it could only mean a larger waiver or a longer run. A controlled test is needed first.

---

## 13. Business Recommendations

Expected benefits are **hypotheses**, not measured outcomes. "Supported" means the current analysis motivates the action; "needs investigation" means more data is required first.

| # | Recommendation | Supporting finding | Expected business benefit (hypothesis) | Priority | Validation needed |
|---|---|---|---|---|---|
| 1 | Re-base the 2026 plan with city-specific, channel-split targets instead of one flat growth rate per city | F1, F2, F5 | Attainment that reflects achievable performance; fairer city comparisons | High | Re-run attainment on the revised targets for the next period; check that city rankings remain stable |
| 2 | Run an acquisition diagnosis and recovery plan in Chennai and Jaipur (signup sources, local competition, marketing reach) | F4 | Moving new-patient intake toward peer levels (the gap is about 17–20 new patients per 100 prior-year actives) | High | Needs data not in the pack (marketing spend, referral sources, competitors); track new per 100 prior active monthly |
| 3 | Review declining clinics in Jaipur (4 of 5) and Chennai (3 of 5) individually | F2, F5 | Targeted fixes where decline is concentrated | Medium–High | Clinic-level root causes (staffing, local competition, closures); monitor clinic-level year-on-year growth |
| 4 | Define and analyse the PK-coded test family; investigate why T-coded volume is flat | F3, F7 | Explains volume and price/mix; informs product and pricing decisions | High (needs investigation) | Product definitions and `test_price` analysis; split revenue-per-test growth into price vs mix |
| 5 | Chase unpaid invoices (21,820; Rs 3.48 crore), review duplicate captures (1,925 invoices) and map 4,765 unmatched captured payments (Rs 0.73 crore) | F8 | Recovery or correction of billing/cash differences | High (supported) | Rupees recovered or corrected within a set period; clear definition of why an invoice has no capture |
| 6 | Launch an operational review of CHE-03, including reminders and scheduling, and test any intervention against a comparison clinic | F9 | Narrowing the roughly 2–3 pp no-show excess | Medium | Before/after no-show rate vs peer clinics; examine staffing and appointment data not in the pack |
| 7 | Do not double the offer budget on current evidence; run a controlled (hold-out) test of the waiver | F10 | Avoids repeating a spend whose measured return is below its cost | High (supported) | Randomised or matched hold-out of home bookings; measure net network tests, not home-only |
| 8 | Add data-quality controls: nova-to-legacy patient mapping, collection-record gaps (3.2% unknown), negative lead times, missing test codes (1,311 lines), payment-invoice id formats | F6, data checks | More reliable patient counts and reconciliation | Medium | Rerun R3 and R9 after mapping; track exception counts |

**Supported by the current analysis:** 1, 5, 7 (and the direction of 2, 3, 6). **Require further investigation before action:** 4, the causes behind 2, 3 and 6, and 8's impact.

---

## 14. Results and Business Impact

| Type | Content |
|---|---|
| **Measured by the project** | Plan attainment and gaps by city, month and channel; revenue and volume growth; patient-flow groups and rates; clinic-level declines and utilisation; invoice states and the billed-to-settled bridge; no-show rate and z-score for CHE-03; offer lift, cost and net extra tests |
| **Insights derived from the measurements** | Plan above deliverable growth; acquisition gap in two cities; clinic-to-home shift; growth concentrated in PK-coded lines; unpaid invoices as the main billing leak; real, partly mix-driven no-show excess at CHE-03; weak return on the offer |
| **Potential applications** | Re-basing targets (strategy/finance); acquisition diagnostics (marketing/city heads); receivables and reconciliation follow-up (finance); a clinic review (operations); controlled offer testing (marketing). Tableau-ready exports exist for a dashboard |
| **Untested** | Whether any recommended action raises volume, recovers cash or lowers no-shows; the causes behind the acquisition gap and CHE-03's excess; profit impact of any change. **No production deployment, organisational adoption or realised financial impact is claimed** |

**Stakeholders.** *Executives:* plan realism and where to focus. *City and operations heads:* clinic-level and channel-level views. *Finance:* the bridge and unpaid/unmatched items. *Marketing:* offer return and test design. *Analytics:* reusable cleaning rules (supersede, `unknown`, stacked TOTAL, id cleaning).

---

## 15. Repository Structure

The tree below follows the repository listing (folder and file names are reproduced as they appear in the repository, including existing spellings).

```
Kalpa-Medical-Study/
├── Logs/
│   ├── approach-log.md
│   ├── challenges-log.md
│   └── decisions-log.md
├── Notebook/
│   ├── 5_vs_18_comparision .ipynb
│   ├── Data_Integrity.ipynb
│   ├── data_analysis.ipynb          # analysis documented in this README
│   └── data_cleaning_.ipynb
├── Video/
│   └── Video.mp4
├── Writeups/
│   ├── Adwin_Writeup.md
│   └── PRITAM_CHAVHAN_Kalpa_Individual_Writeup.md
├── data/
│   ├── datapack/
│   │   ├── bookings/  campaigns/  collections/  invoices/  patients/
│   │   ├── payments/  plan/  reference/  results/
│   │   ├── MANIFEST.json
│   │   └── README.md
│   └── merged/                      # inputs read by data_analysis.ipynb
│       ├── booking_lines.csv.gz
│       ├── bookings.csv.gz
│       ├── campaigns.csv.gz
│       ├── collections.csv.gz
│       ├── invoices.csv.gz
│       ├── orphans_collections.csv.gz
│       ├── orphans_invoices.csv.gz
│       ├── orphans_results.csv.gz
│       ├── payments.csv.gz
│       └── plan_long.csv.gz
├── datadictionary/
│   ├── cleaned_data_dictionary.xlsx
│   └── uncleaned_data_dictionary.md
├── referance/                       # reference case study material (see Section 20)
└── README.md
```

| Path | Purpose |
|---|---|
| `Notebook/data_analysis.ipynb` | Cleaning, Part 1 (where), Part 2 (why), Briefs C, D, E and the Tableau export. Documented in full here |
| `Notebook/5_vs_18_comparision .ipynb`, `Data_Integrity.ipynb`, `data_cleaning_.ipynb` | Additional notebooks in the repository; they are not described in this README |
| `Logs/` | Approach, challenges and decisions logs |
| `Writeups/` | Write-ups (`Adwin_Writeup.md`, `PRITAM_CHAVHAN_Kalpa_Individual_Writeup.md`) |
| `Video/Video.mp4` | A video file (contents not described here) |
| `data/datapack/`, `data/merged/` | Raw data pack and the merged files the notebook reads |
| `datadictionary/` | Cleaned (`.xlsx`) and uncleaned (`.md`) data dictionaries |

The repository listing shows no `requirements.txt`, `pyproject.toml` or environment file, and no `tableau_exports/` folder.

---

## 16. Installation and Setup

**Prerequisites.** Python 3 with `pandas` and `numpy`, and Jupyter (or a Google account for Colab). No other third-party library is imported. Because no dependency file exists in the repository, the list below is derived from the notebook's imports.

```bash
git clone https://github.com/OmRohamare/Kalpa-Medical-Study.git
cd Kalpa-Medical-Study

python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

pip install numpy pandas jupyterlab
```

**Data.** The input files are already in `data/merged/`. No credentials or environment variables are needed.

---

## 17. How to Run the Project

### Option A: Google Colab (as the notebook was originally run)

1. Upload the files from `data/merged/` to a Google Drive folder at `MyDrive/merged`.
2. Open the notebook with the Colab badge at the top of this README.
3. Run all cells in order. The first code cell mounts Drive and `DATA_DIR` is `/content/drive/MyDrive/merged`.

### Option B: Local Jupyter

1. Start Jupyter from the repository root: `jupyter lab Notebook/data_analysis.ipynb`.
2. **Skip or delete the first code cell** (`from google.colab import drive ...`); `google.colab` is not available locally.
3. Change `DATA_DIR` in the settings cell to the local folder, for example `DATA_DIR = Path("../data/merged")` (the notebook sits in `Notebook/`).
4. Run all cells top to bottom.

**Run order and dependencies.** Run sequentially. Part 2 reuses Part 1 tables. Part 3 needs Part 0 and the "1.1 Analysis setup" block (it uses `df_booked` and the `campaign` flag). Part 4 (export) needs everything before it and should be run last.

**Expected outputs.** Printed tables for each section, a printed line for each R0 scorecard hypothesis, and 23 CSV files written to `DATA_DIR/tableau_exports/` (note: with the settings above this creates a folder inside `data/merged/`; change `OUT_DIR` if you prefer a different location).

**Reproducibility notes.**

- The only random element is the Brief D simulation, which uses `np.random.seed(42)`.
- `BASIS = "slot_at"` (service date) puts a booking in a month. Switching to `"created_at"` changes every table; results in this README use `slot_at`.
- Saved outputs in the notebook were produced on Colab; package versions were not recorded in the notebook.
- The notebook reads the whole bookings file (568,007 rows × 23 columns, about 100 MB in memory per the notebook's `info()` output), so a laptop with a few GB of free memory should suffice (an estimate, not a tested requirement).

---

## 18. Limitations and Assumptions

**Confirmed from the notebook (evidence in outputs or code).**

| Area | Limitation |
|---|---|
| Descriptive, not causal | Part 2 narrows candidate causes; it does not prove one. The nova difference-in-differences uses three months before and after go-live. The channel gap assumes the plan's growth ask applied equally to clinic and home (the plan has no channel split). The test-mix gap assumes uniform plan growth across tests (the plan has no per-test targets) |
| No testable drivers in the data | No price, competitor, marketing-spend, staffing or phlebotomist-capacity data, so those causes cannot be examined (stated in the notebook's LIMITS line) |
| Window | Bookings span Jan 2025 – Jun 2026 (plus 107 tests in early July 2026). Retention and acquisition can be shown to *differ* between cities but not to have *worsened*. Each comparison rests on one prior-year half-year, so seasonality cannot be separated from trend |
| Small number of cities | Six cities; the 0.79 plan-vs-actual correlation and all peer comparisons rest on six points. No confidence intervals or significance tests are reported across cities |
| Signup-month counts | New-patient signup counts (R3b) include only patients active in the Jan–Jun 2025 or Jan–Jun 2026 windows, which is why Jul–Dec 2025 signup months look low. A 2025 signup can be counted in either window and a 2026 signup in only one, which biases year-on-year signup comparisons downward in every city. The contrast *between* cities is less affected; the absolute year-on-year percentages should not be read at face value |
| "Retention" | A same-window return rate (patients active in Jan–Jun 2025 who are active again in Jan–Jun 2026), not an annual or cohort retention curve |
| Rupee value of the gap | Indicative: assumes missing tests would convert and earn like average tests. Revenue only; there is no cost data, so no profit or margin claim |
| Reschedules | Pairs are collapsed to the original's outcome for the funnel; all 1,500 superseded originals are nova-system bookings (derived: 26,543 nova rows − 25,043 non-superseded nova rows = 1,500), so reschedule handling touches Chennai and Jaipur only |
| Different bases | A5 uses invoiced patients and tests; R2 uses booked ones; the funnel no-show share is test-weighted (13.6%) while Brief D's rate is booking-weighted (14.4%). Compare like with like |
| Test codes | 1,311 lines have no `test_code`. PK01–PK04 are not defined in the notebook |
| Data quality | 3.2% of non-superseded bookings have no collection record (labelled `unknown`); negative `lead_days` values exist (minimum −2.0) and were not corrected; payment invoice ids needed cleaning (139,620 rows); 4,765 payments have no matching invoice |
| Identity check | R9 uses phone number as the only linking key; non-matches do not rule out re-keyed patients, and 771 nova patients with a pre-go-live signup remain unexplained |
| Brief D | The z-score and simulation treat bookings as independent. Only the top eight phlebotomists are shown in the notebook output, so concentration cannot be judged |
| Brief E | No control group; the post-offer quarter is used as the counterfactual, and it may itself carry a lasting effect of the offer (home share about 40% vs about 34% before). The revenue test is revenue, not profit. The notebook does not reproduce marketing's 9% figure |
| Partial tables | Several notebook displays are truncated with `.head()`; the full tables are in the Part 4 exports |
| Reproducibility | The first code cell is Colab-specific; the data path is a Google Drive path; no dependency file or package versions are recorded; Part 4 writes into the data folder by default |

**Potential concerns requiring further investigation (not confirmed).**

- Whether PK-coded lines are new products, bundles or recoded tests, and whether they substitute for T-coded volume.
- Whether the clinic-to-home shift reflects the offer, a lasting preference, or both.
- Whether the data pack's provenance and any usage restrictions allow public redistribution of row-level data. The raw files contain patient identifiers (name, phone, date of birth); this README reproduces none of them. Check `data/datapack/README.md` and `MANIFEST.json` for the data pack's own terms.

---

## 19. Future Enhancements

*Proposed work only; none of this exists in the repository.*

| Priority | Enhancement | Addresses |
|---|---|---|
| High | Define PK codes and split revenue-per-test growth into price vs product-mix effects using `test_price` | Findings 3 and 7 |
| High | Cohort-based acquisition and retention (signup cohort curves) with explicit handling of right-censoring | Finding 4; signup-month limitation |
| High | Add acquisition-side data (lead source, marketing spend, local competitor openings) for Chennai and Jaipur | Finding 4 |
| High | Hold-out or geo-matched test of the home-collection waiver, with network-level net-test and contribution-margin readout | Finding 10 |
| Medium | Confidence intervals or bootstrap ranges for city and peer comparisons; permutation test for the difference-in-differences | Six-city and three-month limits |
| Medium | Channel-specific and test-family-specific plan targets and re-forecast | Finding 1 and 5 |
| Medium | Automated data-quality checks (id formats, negative lead time, missing codes, orphan payments) run before analysis | Data-quality limitations |
| Medium | Patient identity resolution beyond phone matching (name, date of birth, with privacy controls) | R9 limitation |
| Medium | Extended history (before Jan 2025) for seasonality and trend | Window limitation |
| Low | Finalise the Tableau dashboard from the `p1_`–`p4_` exports and add a requirements file and a path-agnostic settings cell | Usability and reproducibility |
| Low | Optional extension towards the reference case's themes (prescription vs self-initiated demand; imaging) if suitable data become available | Case alignment |

---

## 20. References and Acknowledgements

**Reference case study**

Jain, T., Manchanda, O., & Sam, S. (2026). *Dr Lal PathLabs: Sustaining Growth in a Changing Diagnostic Landscape* (Case no. IIMA/ECO0372). Indian Institute of Management Ahmedabad. Prepared by Professor Tarun Jain (IIM Ahmedabad), Dr Om Manchanda (former Managing Director, Dr Lal PathLabs) and Ms Shreya Sam (Research Assistant, IIM Ahmedabad). © 2026 Indian Institute of Management Ahmedabad.

The case is copyrighted and licensed for individual use; it is **not reproduced** in this README, and readers should obtain it from IIM Ahmedabad. The case's exhibits cite Privatecircle, DLPL annual and investor materials, Reedser Consulting/Chiratae/AWS, and CRISIL MI&A; none of those sources was used directly in this project.

**Project sources**

- Kalpa Health data pack, as merged in `data/merged/` (documentation: `data/datapack/README.md`, `data/datapack/MANIFEST.json`, `datadictionary/`).
- Notebook: `Notebook/data_analysis.ipynb` (this repository).

**Acknowledgements.** The reference case informed the business framing, the choice of operating themes (home collection, new-patient acquisition, patients and samples as KPIs) and the case-study alignment in [Section 4](#4-reference-case-study-and-project-alignment). The analysis, calculations and findings in this README come from the notebook's own implementation on the Kalpa Health data. The project does not reproduce the case, does not use its data, and does not validate or contradict its conclusions. Individual write-ups are in `Writeups/`.

---

## 21. Author and Contact

**Om Rohamare** · GitHub: [@OmRohamare](https://github.com/OmRohamare) · Repository: [`Kalpa-Medical-Study`](https://github.com/OmRohamare/Kalpa-Medical-Study)
