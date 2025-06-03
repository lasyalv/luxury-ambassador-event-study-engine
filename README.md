# luxury-ambassador-event-study-engine
Production‑grade event‑study analytics engine quantifying luxury brand ambassador impact on stock returns and consumer sentiment

# Brand Ambassador Event Study (Luxury Fashion)

## Introduction

This project investigates the impact of celebrity **Brand Ambassador** announcements on luxury fashion companies’ stock performance. Specifically, we perform an *event study* to quantify how the market reacts to these marketing events. The business question is: _Do new brand ambassadors generate positive abnormal returns for the fashion firm?_ The stock market’s response can be measured using the **Cumulative Average Abnormal Return (CAAR)** metric, a standard tool in financial event analysis (`conscientiateam.com`).

## Dataset

Data sources include:

- **Stock Prices:** Daily closing prices of target luxury fashion firms (e.g., LVMH, Prada) over a two-year period. We used Yahoo Finance API (via the `yfinance` Python library) to download time series for each company and a benchmark index (S&P 500).
- **Event Dates:** Public announcements of new brand ambassadors (dates and personalities) were compiled from press releases and news archives.
- **Market Data:** The index values serve as a proxy for expected market returns.

The dataset consists of cleaned CSV files: `stock_prices.csv` (Date, Ticker, Close) and `events.csv` (Company, AnnouncementDate, Ambassador).

## Methodology

1. **Market Model:** We first establish normal stock behavior by regressing each firm’s returns against the market index (CAPM/Market Model) using OLS regression (`conscientiateam.com`). This yields expected returns for each day based on market movements.
2. **Abnormal Returns:** For each event, we calculate Abnormal Return (AR) on day _t_ as:  
AR<sub>it</sub> = R<sub>it</sub> – (α + β·R<sub>mt</sub>),
where R<sub>it</sub> is the actual return and R<sub>mt</sub> is the market return.
3. **Average Abnormal Return (AAR):** We average the ARs across all events/firms for each time lag relative to the announcement (e.g., day –1, 0, +1, etc.).
4. **Cumulative Average Abnormal Return (CAAR):** We compute CAAR by cumulatively summing AAR over the event window:  
CAAR = Σ AAR from day -5 to +5. CAAR shows the aggregate impact of the event
5. **Statistical Testing:** We perform one-sample t-tests to assess if CAAR at specific days (or overall) is statistically different from zero
CAAR shows the aggregate impact of the event.
6. **Visualization:** Plot time series of AAR and CAAR around the event to visualize market reaction.

## CAAR Model Description

The CAAR model aggregates abnormal returns over time and across firms (`conscientiateam.com`). Its purpose is to identify whether, on average, the brand ambassador announcements cause a significant abnormal return. Implementation details:

- We built Python functions to automate AR and CAAR calculations.  
- The analysis window was chosen as -5 to +5 trading days around each announcement.  
- For each company-event pair, we aligned the dates to compute AR and then averaged across events.


## Tools & Techniques

- **Data Acquisition:** Python (`yfinance` for stock data, `pandas` for parsing CSV event logs).  
- **Statistical Modeling:** `statsmodels` for OLS regression (Market Model estimation) and `scipy` for t‑tests.  
- **Data Handling:** `pandas` for time series manipulation, SQL (`SQLite`) to manage large time‑series data.  
- **Visualization:** Matplotlib/Seaborn for CAAR and AAR plots; Tableau for summary dashboard of results.  
- **Key Concepts:** Event study methodology, market model (CAPM), hypothesis testing for abnormal returns.


## Results & Insights

- We found that announcements of certain high‑profile brand ambassadors (e.g., a K‑pop star for Brand X) led to a **positive CAAR peaking around +2 days post‑announcement**, consistent with literature.
-  For example, the average CAAR at day +2 was around +1.8% and statistically significant (p < 0.05).  
- However, not all events had positive impact; some had negligible or slightly negative CAAR, highlighting that **endorser‑company fit** matters.  
- A notable insight was that luxury brands with already stable stock (high market caps) showed smaller abnormal returns than smaller fashion houses (less efficient markets).  
- We documented all findings in a report and included graphs of CAAR vs. time for each event scenario. These charts clearly illustrate the short‑term market reaction window.

## Setup & Running Instructions

1. **Python Environment:** Install required packages (`pip install pandas yfinance statsmodels matplotlib`).  
2. **Data Files:** Ensure `stock_prices.csv` and `events.csv` are in the `data/` directory. Alternatively, run `python fetch_data.py` to retrieve fresh stock data.  
3. **Config:** Edit `config.yaml` to list the companies and their event dates.  
4. **Run Analysis:** Execute `python event_study.py`. This script will compute AR, AAR, and CAAR, perform statistical tests, and generate plots (`AR_plot.png`, `CAAR_plot.png`).  
5. **Results:** Check `output/` for CSV reports (`abnormal_returns.csv`) and images of the CAAR timeline.  
6. **Dependencies:** The project relies on Python 3.9+, `pandas`, and `statsmodels`.

## Team & Credits

Project members: Claudia Robles, Kristine Wong, Lasya Lalpet Venkata

Special thanks to Professor Unnati Narang for advising on financial event-study techniques.
