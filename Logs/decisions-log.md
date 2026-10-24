# Decisions Log — Kalpa Health Growth Analysis

This log records the analytical and data-cleaning choices used in the analysis.

| ID | Decision | Reason | Status |
|---|---|---|---|
| D01 | Use booked test lines as the primary KPI. | Aligns with the business question about booked-test growth. | Applied |
| D02 | Use `slot_at` as the reporting date. | Keeps the comparison tied to the scheduled service date. | Applied |
| D03 | Exclude superseded bookings. | Avoids counting replaced bookings as current volume. | Applied |
| D04 | Compare H1 2026 with H1 2025. | Provides a like-for-like six-month comparison. | Applied |
| D05 | Use Ahmedabad, Bengaluru, Hyderabad, and Pune as peer cities. | Provides a directional internal benchmark. | Applied with caveat |
| D06 | Treat the 74%/26% gap split as descriptive. | The benchmark is not a controlled counterfactual. | Applied |
| D07 | Decompose growth into patients, bookings per patient, and tests per booking. | Separates patient base, frequency, and basket size. | Applied |
| D08 | Analyse packages separately from standard tests. | Package growth may mask a nearly flat standard-test trend. | Applied |
| D09 | Do not attribute the January decline to Nova. | The decline began before Nova went live on 1 April. | Applied |
| D10 | Do not claim the home-collection campaign caused all volume growth. | No holdout group establishes the campaign's incremental impact. | Applied |
| D11 | Diagnose acquisition before scaling spending. | The root cause and incremental return remain unknown. | Recommended |
| D12 | Run controlled pilots before scaling interventions. | A control group helps estimate incremental impact. | Recommended |
| D13 | Rebuild future plans bottom-up by city, channel, and driver. | Makes assumptions explicit and monitorable. | Recommended |

## Headline reconciliation
- `458,002 - 436,302 = 21,700` additional tests year-on-year.
- `515,740 - 458,002 = 57,738` tests below plan.
- `21,700 / 436,302 × 100 ≈ 5.0%` actual growth.
- `458,002 / 515,740 × 100 ≈ 88.8%` plan attainment.

## Counting-basis sensitivity
The presentation notes:
- Using `created_at` gives approximately 3.5% growth and a 63,954-test gap.
- Counting superseded bookings gives approximately 5.8% growth.

The main analysis uses `slot_at` and excludes superseded bookings. If the counting basis changes in future work, record the reason and its impact on the headline results.
