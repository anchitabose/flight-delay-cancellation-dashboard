# US Flight Delay & Cancellation Analysis — Power BI

Interactive Power BI dashboard analyzing 5.8M U.S. domestic flights (USDOT, 2015) to identify which airlines, airports, and time periods carry the highest risk of delay or cancellation — and what's actually driving it.

📹 **[Watch the demo](./flight_dashboard_demo_trimmed.mp4)** · Built with Power BI Desktop, Power Query, DAX

To explore it yourself: download `Airline.pbix` and open it in [Power BI Desktop](https://www.microsoft.com/en-us/power-platform/products/power-bi/downloads) (free).

---

## The question this answers

Airlines and frequent flyers both care about the same thing for different reasons: *where does flight reliability break down, and why?* This dashboard breaks that into three parts:

1. **Where do things stand overall** — across the full year, which airlines and causes dominate delays
2. **Who's actually reliable** — does flying a bigger airline mean a less reliable one?
3. **What drives cancellations specifically** — and could a small operational change meaningfully cut them?

## Key findings

- **63.5% on-time rate** across 5.82M flights, with **late aircraft turnaround** (not weather) as the single largest delay cause — 36% of all delay minutes are self-inflicted scheduling issues, not external factors.
- **Southwest Airlines** operates the highest flight volume (1.26M flights) but ranks among the *least* reliable carriers (55.1% on-time) — scale does not predict reliability. **Alaska Airlines**, by contrast, is both smaller and the most reliable (74.7%).
- **Weather drives 54% of all cancellations**, concentrated almost entirely in **February** — cancellation volume that month (21K) is nearly double any other month.
- **American Eagle Airlines** has a cancellation rate of 5.1%, more than 3x the rate of comparable mid-size carriers — independent of its on-time performance, which is mid-tier.
- A what-if model estimates that a **20% reduction in cancellation rate would save ~17,980 flights/year** from cancellation — used to frame the cost of operational improvements in concrete terms.

## Dashboard structure

**Page 1 — Season Overview**
Year-level KPIs, delay-cause breakdown, flight status split, and a monthly delay trend revealing a clear summer peak (June, 38 min avg delay) and autumn low (October, 27 min).

**Page 2 — Airline Performance**
Full 14-airline leaderboard, plus a volume-vs-reliability scatter plot that's the core insight of this page — directly visualizing that high flight volume and high reliability don't go together. Includes a delay-cause mix for the five worst-performing airlines.

**Page 3 — Cancellations**
Monthly cancellation trend, cause breakdown, and a season-aware airport view (filtered by a minimum flight-count threshold to avoid small-sample distortion — see Data Quality Notes). Includes an interactive what-if parameter for modeling cancellation reduction scenarios.

All three pages share a synced airline filter; Page 3 adds an independent season filter.

## Data quality notes

A few decisions worth flagging, since they affect how the numbers should be read:

- **~8–10% of flight records use numeric airport codes** not present in the airport lookup table (a known quirk of the raw USDOT extract). These rows are excluded from airport-level visuals specifically, but retained in airline-level and overall KPIs where airport identity isn't required.
- **Airport-level rankings are threshold-filtered** (minimum flight volume per period) after an early version of the "worst airport" metric surfaced a regional airport with just 206 annual flights — a rate built on a small sample, not a real reliability signal. The same logic is applied to the seasonal airport view.
- Cancellation cause codes follow the USDOT standard (A = Carrier, B = Weather, C = NAS, D = Security).

## Technical approach

- **Data model:** Star schema — `flights` (fact, 5.8M rows) connected to `airlines`, `airports` (active relationship on origin airport, inactive on destination), and a dedicated `DateTable` for time intelligence.
- **DAX:** 25+ measures, including dynamic TOPN-based rankings (best/worst airline, peak month, worst airport) that recalculate under any filter context rather than hardcoded values.
- **Interactivity:** Airline and season slicers cross-filter all visuals; bookmark-based navigation between pages; a what-if parameter drives a live cancellation-savings estimate.
- **Cleaning:** Power Query used to remove redundant columns, fix data types, handle nulls, and standardize airline names prior to modeling.

## Dataset

[2015 Flight Delays and Cancellations](https://www.kaggle.com/datasets/usdot/flight-delays) — U.S. Department of Transportation, via Kaggle. ~5.82M flight records across 14 airlines and 322 airports.

## Files

- `Airline.pbix` — full Power BI file (open in Power BI Desktop)
- `flight_dashboard_demo_trimmed.mp4` — short demo walkthrough
- `Overview.png`, `AirlinePerformance.png`, `CancellationsPage.png` — page-by-page screenshots
