# Challenges Log — Kalpa Health Growth Analysis

| ID | Challenge | What the analysis shows / limitation | Follow-up |
|---|---|---|---|
| C01 | Plan versus actual gap | Actual growth was about 5.0% versus planned 18.2%; gap 57,738 tests. | Review assumptions behind the plan. |
| C02 | Superseded bookings | Counting superseded bookings changes the growth result. | Preserve the exclusion rule and document exceptions. |
| C03 | Reporting-date choice | `created_at` produces a different result from `slot_at`. | Confirm the agreed KPI date with stakeholders. |
| C04 | Peer benchmark | The 74%/26% split depends on four internal peer cities. | Describe it as directional, not causal proof. |
| C05 | Unknown acquisition cause | New-patient counts fell about 27% in Chennai and 34% in Jaipur from January. | Rebuild the funnel by city, clinic, and source; review what changed around 1 January. |
| C06 | Nova cutover timing | The decline began before the 1 April cutover. | Do not blame Nova for the full decline; investigate system data quality separately. |
| C07 | Package step-change | Four package products contributed about 87% of net growth; standard tests grew 0.8%. | Verify what changed in January and whether packages displaced standard tests. |
| C08 | Retention interpretation | Measured retention was 32.6% in peer cities and 33.6% in Chennai plus Jaipur. | Avoid claiming retention has no effect; validate cohort definitions if needed. |
| C09 | Capacity and operations | Two of 31 clinics reached at least 90% peak utilisation; no-show and delay measures were broadly stable. | Check individual clinic constraints before ruling out local issues. |
| C10 | Campaign incrementality | Home-collection volume rose, but no holdout group estimates causal impact. | Run a controlled pilot and calculate cost per incremental test. |
| C11 | Missing business-driver data | Competitor activity, detailed marketing spend, pricing changes, and staffing changes are not established. | Collect city-month-level supporting data. |
| C12 | Data quality | Nova records have identity/source linkage gaps, missing names, and more missing collection records than legacy records. | Improve master patient ID, patient name, source capture, and collection reconciliation. |
| C13 | Financial impact | Volume results alone do not establish revenue, margin, or campaign ROI. | Link incremental volume to realised revenue and variable costs. |
| C14 | Limited history | Analysis notes 18 months of data. | Treat trend findings cautiously and refresh as more periods become available. |

## Priority follow-ups
1. Identify what changed in Chennai and Jaipur around 1 January.
2. Validate package launch, coding, and promotion history.
3. Design an acquisition pilot with held-out control clinics.
4. Fix Nova identity, source, and collection-record quality.
5. Rebuild the plan bottom-up and monitor forecast error.
