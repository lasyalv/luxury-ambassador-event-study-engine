# luxury-ambassador-event-study-engine
Production‑grade event‑study analytics engine quantifying luxury brand ambassador impact on stock returns and consumer sentiment

## Table of Contents

* [Business Context and Objectives](#business-context-and-objectives)
* [Hypotheses](#hypotheses)
* [Project Scope](#project-scope)
* [Repository Structure](#repository-structure)
* [Data & Methodology](#data--methodology)

  * [Environment and Dependencies](#environment-and-dependencies)
  * [Data Pipeline](#data-pipeline)
  * [Stock Impact Analysis (Event Study)](#stock-impact-analysis-event-study)
  * [Sentiment Analysis](#sentiment-analysis)
  * [Search Interest Analysis](#search-interest-analysis)
* [Key Findings](#key-findings)

  * [Stock Impact (CAAR Analysis)](#stock-impact-caar-analysis)
  * [Sentiment Analysis Results](#sentiment-analysis-results)
  * [Search Interest Insights](#search-interest-insights)
* [Limitations](#limitations)
* [Data Sources and References](#data-sources-and-references)

## Business Context and Objectives

Brands appoint ambassadors to boost credibility, expand reach, and drive sales. Yet, quantifying how endorsements translate into measurable value requires rigorous analysis. This project measures how high-profile brand ambassadors influence three critical metrics:

1. **Stock Returns**
2. **Media Sentiment**
3. **Consumer Search Behavior**

Our goal is to deliver quantitative insights that guide ambassador selection, inform marketing strategy, and demonstrate end‑to‑end business analysis and data‐engineering proficiency.

## Hypotheses

1. **Occupation Effect**
   An ambassador’s profession (actor, athlete, artist) materially influences campaign outcomes.

2. **First‑Time Impact**
   First‐ever ambassadors generate stronger spikes in stock returns, sentiment, and search interest than follow‑up appointments.

3. **Tiered Reach**
   Mega influencers (≥ 1 M followers) create greater business effects than lower‑tier ambassadors.

## Project Scope

* **Brands:** Five luxury fashion houses and their seven sub‑brands (e.g., Gucci, Louis Vuitton, Burberry, Coach, Moncler).
* **Ambassadors:** Fifteen celebrities spanning actors, athletes, and artists, including first‐time and follow‑up appointments.
* **Time Frame:** Five‑year period (2018–2023), analyzing a 10‑trading‑day window (two weeks) around each announcement.

## Repository Structure

A clear, logical folder hierarchy ensures reproducibility and collaboration:

```
/ (root)
├── data/
│   ├── raw/
│   │   ├── stock_prices.csv
│   │   ├── news_articles.csv
│   │   └── google_trends.csv
│   └── processed/
│       ├── stock_abnormal_returns.csv
│       ├── sentiment_scores.csv
│       └── trends_spikes.csv
├── notebooks/
│   ├── 1_data_cleaning.ipynb
│   ├── 2_event_study_stock.ipynb
│   ├── 3_sentiment_analysis.ipynb
│   └── 4_search_trends.ipynb
├── scripts/
│   ├── fetch_stock_data.py
│   ├── scrape_news.py
│   ├── compute_abnormal_returns.py
│   ├── sentiment_scoring.py
│   └── fetch_google_trends.py
├── requirements.txt
├── environment.yml
├── README.md
└── results/
    ├── figures/
    │   ├── stock_caar_plot.png
    │   ├── sentiment_boxplot.png
    │   └── trends_line_chart.png
    └── tables/
        ├── caar_summary_table.csv
        └── sentiment_summary_table.csv
```

* **data/raw:** Original datasets (stock prices, raw news text, Google Trends exports).
* **data/processed:** Cleaned data ready for analysis (abnormal returns, sentiment scores, trend spikes).
* **notebooks:** Jupyter notebooks detailing each analysis step (data cleaning, event study, sentiment, trends).
* **scripts:** Modular Python scripts for data ingestion and transformation, enabling automation.
* **requirements.txt / environment.yml:** Lists Python packages (e.g., `pandas`, `numpy`, `scipy`, `statsmodels`, `textblob`, `pytrends`, `matplotlib`, `nltk`).
* **results:**

  * **figures:** Exported visualizations (CAAR plot, sentiment boxplot, Trends line chart).
  * **tables:** Summary metrics (CAR/CAAR results, sentiment aggregates).

## Data & Methodology

### Environment and Dependencies

* **Python Version:** 3.8+
* **Key Packages:**

  * `pandas` (data manipulation)
  * `numpy` & `scipy` (numeric computations, statistical tests)
  * `statsmodels` (market model regression)
  * `textblob` (sentiment polarity)
  * `nltk` (tokenization, lemmatization)
  * `pytrends` (Google Trends API)
  * `matplotlib` (visualizations)
  * `requests` (data fetching)
* **Setup Commands:**

  ```bash
  # Conda
  conda env create -f environment.yml
  conda activate succession‐planning

  # Or pip
  pip install -r requirements.txt
  ```

### Data Pipeline

1. **Fetch & Store Raw Data**

   * **fetch\_stock\_data.py:** Download historical adjusted‐close prices for each brand from Yahoo Finance.
   * **scrape\_news.py:** Scrape press releases and news articles; store titles, dates, and full text.
   * **fetch\_google\_trends.py:** Retrieve normalized search indices (0–100) for brand and ambassador terms via Google Trends API.

2. **Data Cleaning & Transformation**

   * **Stock Prices:** Merge each brand’s prices with a market index (e.g., S\&P 500) to compute expected returns using linear regression.
   * **News Text:** Clean and label article text by event phase (pre‑announcement, announcement day, post‑announcement).
   * **Trends Data:** Normalize and align dates; extract T–14 to T+14 windows for each event.

3. **Processed Outputs**

   * **stock\_abnormal\_returns.csv:** Daily abnormal returns for each brand around event dates.
   * **sentiment\_scores.csv:** Daily average polarity scores by brand and ambassador across phases.
   * **trends\_spikes.csv:** Indexed search volume with markers for event phases.

### Stock Impact Analysis (Event Study)

* **Market Model Estimation:**

  * Use a 120‑day estimation window (T–140 to T–21) to regress each brand’s daily return against market index returns.
  * Obtain parameters α (alpha) and β (beta).

* **Abnormal Return (AR):**

  $$
    AR_{t} = R_{t} - (\alpha + \beta \, R_{m,t})
  $$

  where $R_{t}$ is the brand’s return on day $t$ and $R_{m,t}$ is the market return.

* **Cumulative Abnormal Return (CAR):**

  * Sum ARs over the event window \[T–2, T+2] to capture immediate impact.

* **Cumulative Average Abnormal Return (CAAR):**

  * Average CAR across all ambassador events to gauge the aggregated effect.

* **Statistical Testing:**

  * Perform t‑tests on CAR distributions to determine significance (p < 0.05).

### Sentiment Analysis

* **Text Preprocessing:**

  * Convert text to lowercase, remove punctuation, tokenize, and lemmatize using `nltk`.

* **Polarity Scoring:**

  ```python
  from textblob import TextBlob

  polarity = TextBlob(article_text).sentiment.polarity
  ```

  * Polarity ranges from –1 (negative) to +1 (positive).

* **Aggregation:**

  * Compute daily mean polarity for each brand–ambassador pairing and categorize by phases:

    * **Pre‑announcement:** T–14 to T–1
    * **Announcement Day:** T
    * **Post‑announcement:** T+1 to T+14

* **Visualization:**

  * Generate boxplots and line charts to compare sentiment distributions across phases and occupations.

### Search Interest Analysis

* **Fetch Google Trends Data:**

  ```python
  from pytrends.request import TrendReq

  pytrends = TrendReq()
  pytrends.build_payload(
      ['BrandName', 'AmbassadorName'],
      timeframe='YYYY-MM-DD YYYY-MM-DD'
  )
  trends_df = pytrends.interest_over_time()
  ```

* **Event Window Alignment:**

  * Extract search indices from T–14 to T+14 and compute:

    * **Peak Index:** Maximum value in window.
    * **Duration Above Baseline:** Number of days index > 50.
    * **Relative Search Ratio:** (Brand + Ambassador queries) ÷ Brand-only queries.

* **Visualization:**

  * Plot line charts highlighting pre‑, during, and post‑announcement search trends.

## Key Findings

### Stock Impact (CAAR Analysis)

* Ambassador announcements **boosted** CAAR by approximately **+2.3 %** over a five‑day window across brands.
* These positive CARs were **statistically significant** (p < 0.05), indicating investors **rewarded** ambassador news.
* **Occupation Effect:** Actors consistently **drove** higher CAAR than artists or athletes, reflecting alignment with luxury brand exclusivity.

> ![Stock CAAR Plot](results/figures/stock_caar_plot.png)

### Sentiment Analysis Results

* **Polarity Shift:** Average daily polarity increased from **0.10** (pre‑event) to **0.30** (post‑event), signifying a **positive** tone shift in media coverage.
* **Occupation Differences:** Athletes **saw** the largest sentiment jump on announcement day, while artists’ coverage remained more neutral.
* **First‑Time vs. Follow‑Up:** First‑ever ambassadors **sparked** sharper immediate sentiment spikes (avg polarity 0.45) compared to follow‑ups (avg polarity 0.28).

> ![Sentiment Boxplot](results/figures/sentiment_boxplot.png)

### Search Interest Insights

* **Peak Spikes:** Google Trends index **surged** to approximately **85** on announcement day (baseline \~ 20), reflecting heightened consumer curiosity.
* **Sustained Interest:** Post‑event interest **remained** elevated (index \~ 30–40) for 7–10 days, indicating lasting brand recall.
* **Ambassador vs. Brand Queries:** Ambassador-name searches often **outpaced** brand-only searches, highlighting the personal draw of influencers.

> ![Trends Line Chart](results/figures/trends_line_chart.png)

## Limitations

* **No True Control Group:** All sampled luxury brands had ambassador programs, limiting direct comparisons to ambassador-free firms.
* **Overlapping Campaigns:** Concurrent product launches and marketing efforts may **confound** isolated ambassador effects.
* **Neutral Media Tone:** Many articles were neutral, making subtle sentiment shifts harder to detect.
* **Data Gaps:** Inconsistent availability of historical data for some sub‑brands or markets restricted sample size and introduced potential bias.

## Data Sources and References

* **Stock Prices:** Yahoo Finance (historical adjusted‑close prices).
* **Sentiment Tools:**

  * **TextBlob:** Polarity scoring (–1 to +1).
  * **NLTK:** Tokenization and lemmatization.
* **Search Trends:** Google Trends API via `pytrends` (normalized 0–100 index).
* **Industry Benchmarks:** Statista for influencer‑market statistics (market size, brand budgets).
* **Methodology References:**

  * Standard event‑study formulas for abnormal returns and CAR/CAAR.
  * Investopedia for market model and AR definitions.


