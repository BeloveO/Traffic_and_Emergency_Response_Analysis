# Traffic and Emergency-Response_Analysis

End-to-end analysis of a synthetic road-traffic and emergency-dispatch dataset: data wrangling, exploratory analysis with group comparisons, and an interactive dashboard. The central question is how **actual emergency response times compare with the expected baseline**, and under which conditions the gap is largest.

## Repository contents

| File | Description |
|---|---|
| `synthetic_traffic_benchmark_200k_cleaned.csv` | Cleaned dataset (199,403 rows × 30 columns) |
| `Group4Project-Traffic_Analysis.ipynb` | Main notebook: data wrangling → EDA (statistics, group analysis, visualizations) → interactive Dash dashboard |
| `Group4_Project_Report.docx` | Short report: dataset chosen, key insights, challenges faced, lessons learned |

Two standalone companion notebooks, `Traffic_Benchmark_EDA.ipynb` (charts with step-by-step reasoning) and `Traffic_Benchmark_Group_Analysis_and_Questions.ipynb` (text-only group-by tables and key questions), cover the same EDA in a lighter form.

## Dataset

- **Source:** [Emergency Dispatch and Traffic Physics Dataset](https://www.kaggle.com/datasets/razanihababdellatif/emergency-dispatch-and-traffic-physics-dataset) on Kaggle (see the dataset page for licence terms).
- **Nature:** synthetic. 200,000 simulated observations from 1 Jan 2022 to 31 Dec 2025; 199,403 remain after duplicate removal.
- **Granularity:** one row per observation. 8,213 rows (4.12%) are accidents.

| Group | Columns |
|---|---|
| Identifier | `Record_ID` |
| Time | `Timestamp`, `Date`, `Hour`, `Month`, `Year`, `Day_of_Week`, `Time_of_Day`, `Is_Holiday` |
| Road & environment | `Area_Type`, `Road_Type`, `Intersection_Type`, `Speed_Limit_kmh`, `Weather`, `Road_Condition`, `Visibility_m`, `Congestion_Index` |
| Driver & vehicle | `Vehicle_Type`, `Vehicle_Age_yrs`, `Driver_Age`, `Driver_Experience_yrs`, `Recorded_Speed_kmh` |
| Accident & response | `Accident_Occurred`, `Collision_Type`, `Vehicles_Involved`, `Accident_Severity`, `Dispatch_Priority`, `Baseline_Expected_Response_Time_min`, `Actual_Response_Time_min`, `Road_Closure_Duration_min` |

> **Scoping note:** on the 95.88% of rows with no accident, `Collision_Type` and `Accident_Severity` are `No Accident`, `Dispatch_Priority` is `Not Applicable`, and `Vehicles_Involved`, `Actual_Response_Time_min` and `Road_Closure_Duration_min` are `0`. Filter to `Accident_Occurred == 1` before averaging any accident or response field.

## Workflow

1. **Data wrangling** — separated structural nulls (e.g. no `Dispatch_Priority` without an accident) from genuine gaps; standardised inconsistent `Weather` text; removed 597 exact duplicates; imputed `Visibility_m` (median by weather) and `Driver_Experience_yrs` (median by age group); corrected impossible 999.0 km/h speeds; validated accident and response fields against `Accident_Occurred`; exported and reloaded the cleaned file to verify it.
2. **Basic statistics & group analysis** — group-by tables for congestion, speeding, accident rate, vehicle type (frequency vs. severity) and response-time gap.
3. **Visualizations** — charts for each group comparison, a weather × time-of-day accident heatmap and a correlation matrix.
4. **Interactive dashboard** — Plotly Dash app with KPI cards, five tabs (heatmap with drill-down, weather & roads, congestion vs. speeding, emergency response, closures), filters, light/dark theme and filtered CSV export.

## Key findings

- **Response benchmark (core finding):** actual response misses the baseline in **97.8%** of accidents, by **9.94 min** on average (12.0 → 22.0 min). The gap is worst in Rural areas (12.99 min) and in Fog (14.58 min).
- **Triage works directionally:** higher-severity and higher-priority incidents show smaller gaps (High priority 8.07 min vs. Standard 11.83 min).
- **Road type:** Interstate accident rate is 7.93% vs. 2.01% on Local roads (~4×).
- **Weather and time:** Snow 5.93% > Rain 4.98% > Fog 3.78% > Clear 3.48%; peak hours ≈ 5.2% vs. Night 1.88%. Snow during Morning Peak is the worst combination (7.88%).
- **Frequency vs. severity:** motorcycles have only a slightly higher accident rate (4.36%) but 39.0% of their accidents are severe or fatal, vs. under 1% for sedans and SUVs.
- **Driver behaviour:** 30.8% of drivers exceed the limit in clear weather vs. 0.1% in snow — drivers slow down in bad weather, yet accident rates still rise.
- **Congestion:** Urban 67.8 > Suburban 53.9 > Rural 34.6; peak hours ≈ 70 vs. Night 20.
- **Day of week (accidents only):** response is slowest on Friday (22.78 min) and fastest on Sunday (20.41 min).
- **Disruption:** mean road closure grows about 8× from Minor (121 min) to Fatal (959 min) accidents.

## Quick start

1. Install Python 3.9+ and the dependencies:
   ```bash
   pip install pandas numpy matplotlib seaborn plotly dash jupyter
   ```
2. Put `synthetic_traffic_benchmark_200k_cleaned.csv` in the same folder as the notebook.
3. Open `Group4Project-Traffic_Analysis.ipynb` and run it top to bottom. The wrangling section reads the raw Kaggle file `synthetic_traffic_benchmark_200k.csv` (download it from the dataset page) and writes the cleaned CSV. To skip wrangling, start from the EDA section.
4. **Dashboard:** the notebook was developed in Google Colab, so `DATA_PATH` points to `/content/synthetic_traffic_benchmark_200k_cleaned.csv`. Change it to your local path, then run the dashboard cells; `app.run(debug=True)` serves the app locally (Dash default: http://127.0.0.1:8050).

## Notes & known issues

- **Synthetic data:** patterns are good for practising method, but magnitudes (including the 12-minute baseline) should not be cited as real-world standards.
- **Day-of-week chart:** "Average Actual Response Time by Day of Week" averages `Actual_Response_Time_min` over *all* records, so its axis shows ~0.6–1.0 min. Filter to `Accident_Occurred == 1` for the true ~20–23 min values; the Friday-high / Sunday-low pattern holds either way.

## Team

Contributors by stage, as credited in the notebook:

| Stage | Authors |
|---|---|
| Data wrangling | Preston Yuen, Funmbi Ojobe |
| Basic statistics & group analysis | Belove Olusola, Mohammed A Hajar |
| Visualizations | Mohammed Hossain, Akeem Adejumo, Belove Olusola |
| Interactive dashboard | Prabhu Raj Samraj, Aleena Mathew |

Dataset credit: Kaggle user `razanihababdellatif`.