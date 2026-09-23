# Workforce & Client-Fulfillment Analytics

An analytical read-out of a Power BI staffing dashboard, turning a static
dashboard export into a written report with findings, a data-quality catch,
and concrete next steps.

## What this is

The source material (`A_DASHBOARD.pdf`) is a one-page Power BI export
covering a field-service / home-visit operation: 4 locations, 10 staff
members, and 160 client-service records logged between January and
April 2020. It has four KPI cards, a donut chart, two bar charts, a trend
line, and a detail table.

This project reads that dashboard the way an analyst would — not just
restating the numbers, but checking whether the *metrics themselves* are
well-formed, spotting the pattern in the monthly trend, and turning both
into a short list of things worth fixing or asking about.

## Key finding

Two of the four headline KPI cards (`Sum of Billable Rate %` and
`Sum of Client Fulfillment %`) are summed percentages — a common Power BI
modeling mistake where a ratio field gets aggregated with `SUM` instead of
`AVERAGE` or a proper `DIVIDE()` measure. The result (350.71K, 586.48K) is
mathematically real but practically meaningless. The report walks through
why, shows the same artifact showing up in the per-staff billable-rate
chart, and proposes the DAX fix.

## Contents

| File | Description |
|---|---|
| `report.pdf` | The full analytical report (6 sections, 4 figures, 1 data table) |
| `build_pdf.py` | Generates `report.pdf` with ReportLab |
| `make_charts.py` | Generates the four chart images used in the report with Matplotlib |
| `chart_location.png` | Donut chart — total hours by location |
| `chart_staff.png` | Bar chart — total hours by staff member |
| `chart_trend.png` | Line chart — total vs. billable hours by month |
| `chart_billrate.png` | Bar chart — reproduction of the flagged "Sum of Billable Rate %" metric |

## Report structure

1. **Executive Summary** — three headline findings in plain language
2. **Distribution of Work** — location and staff-level balance
3. **Monthly Trend** — the Jan–Mar plateau and the April drop-off
4. **A Modeling Caveat Worth Fixing** — the summed-percentage issue and the DAX fix
5. **Data & Methodology** — an exact/estimated confidence table for every figure used
6. **Open Questions & Recommendations** — what to confirm against the live data model, and what to build next

## Methodology & honesty about the data

No live Power BI connection or underlying dataset was available — only the
static PDF export. Every number in the report is one of two kinds, and
Section 5 of the report tags each one explicitly:

- **Exact** — taken directly from the dashboard's own total row or legend
  labels (e.g., Total Hours = 31,823; location shares to one decimal place).
- **Estimated** — read from a chart's visual proportions where the source
  dashboard did not print a data label (e.g., per-staff hour counts,
  monthly Jan–Apr values). These are called out as directional, not
  audit-grade, everywhere they appear.

This distinction matters more than any single number in the report — it's
the difference between a report you can act on and one you can only skim.

## Reproducing this report

```bash
pip install reportlab matplotlib --break-system-packages
python3 make_charts.py   # generates the four chart PNGs
python3 build_pdf.py     # assembles report.pdf from the charts + narrative
```

## Suggested next iteration

If the underlying Power BI dataset (or a data export of the full 160-row
table) becomes available, re-run this analysis against real row-level data
to replace every "Estimated" row in Section 5 with an exact figure, and to
resolve the two open questions about the April drop-off and the
Clients Assigned vs. Clients Served discrepancy.
