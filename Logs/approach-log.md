# Approach Log — Kalpa Health Growth Analysis

## Business question
Why did H1 2026 booked tests grow by only 5.0% against an 18.2% plan, and what should Kalpa Health do about it?

## Chosen approach
1. **Define the KPI:** Use booked test lines on non-superseded bookings, dated by `slot_at`, for H1 (January–June) comparisons.
2. **Rebuild headline figures:** Compare H1 2025 actuals, H1 2026 actuals, and the H1 2026 plan.
3. **Compare city-month performance:** Review six cities across six months, giving 36 city-month combinations.
4. **Use an internal peer benchmark:** Compare Chennai and Jaipur with Ahmedabad, Bengaluru, Hyderabad, and Pune. Treat the benchmark as descriptive, not causal.
5. **Decompose actual growth:** Separate changes in active patients, bookings per active patient, and tests per booking.
6. **Check product mix:** Compare package lines with standard test lines.
7. **Investigate acquisition:** Review new-patient trends by city and month; Chennai and Jaipur declined from January.
8. **Test alternatives:** Examine retention, Nova cutover timing, capacity/operations, and the free-home-collection campaign.
9. **State limitations:** Document uncertainty about the January decline, campaign incrementality, and missing competitor/spend/staffing data.
10. **Recommend actions:** Diagnose the acquisition decline, use controlled pilots before scaling spend, and rebuild the plan bottom-up.

## Headline calculations
- H1 2025 booked tests: 436,302
- H1 2026 booked tests: 458,002
- Net increase: `458,002 - 436,302 = 21,700`
- Actual growth: `(21,700 / 436,302) × 100 ≈ 5.0%`
- H1 2026 plan: 515,740
- Gap to plan: `515,740 - 458,002 = 57,738`
- Plan attainment: `(458,002 / 515,740) × 100 ≈ 88.8%`

## Gap decomposition
Four internal peer cities delivered pooled H1 growth of approximately 8.4%. The analysis splits the 57,738-test gap into:
- 42,798 tests (74%): plan above peer-city growth
- 14,940 tests (26%): Chennai and Jaipur below peer-city growth

This is a descriptive benchmark split, not proof that the plan was wrong or that the cities caused exactly these amounts.

## Growth-driver decomposition
The +21,700 net change is allocated as:
- Active patients: +16,418
- Bookings per patient: −4,415
- Tests per booking: +9,697

Check: `16,418 - 4,415 + 9,697 = 21,700`.

Four package products contributed +18,801 of the +21,700 increase (about 87%); standard tests grew about 0.8%.

## Options considered
- Copy slide totals without rebuilding them: not selected; figures should be traceable to source tables.
- Use one company-wide explanation: not selected; city and month trends differ.
- Treat the peer benchmark as causal proof: not selected; peers are not a controlled counterfactual.
- Blame Nova for the whole decline: not selected; the acquisition decline began before the 1 April cutover.
- Increase marketing spend immediately: not selected; the cause and incremental return are not established.

## Limitations
The analysis is descriptive. The exact cause of the January acquisition decline is unknown, there is no campaign holdout group, and competitor activity, detailed marketing spend, pricing, and staffing data are not established in the analysis.
