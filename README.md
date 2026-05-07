# Covid-19-UK-Demand-Forecasting
COVID-19 demand forecasting using Python &amp; Power BI. Built solo — covers full data science lifecycle: cleaning, EDA, XGBoost regression modelling, and interactive dashboard. Trained on UK regional case data (2020–2022) with lag features, MAE/RMSE evaluation, and actual vs predicted visualisation.
# 🦠 COVID-19 Demand Forecasting Dashboard

> End-to-end machine learning project using **Python (XGBoost)** and **Power BI** to analyse and forecast COVID-19 case trends across UK regions (2020–2022).

![Power BI Dashboard](dashboard.png)

---

## 📌 Project Overview

This project was built entirely **solo** as a personal data science project. It covers the complete data science lifecycle — from raw data ingestion and cleaning, through exploratory analysis and machine learning, to an interactive Power BI dashboard for non-technical stakeholders.

The goal was to forecast daily COVID-19 case numbers for London using historical case data and machine learning, then visualise both actual and predicted trends alongside regional comparisons in a professional dashboard.

---

## 📊 Dataset

| Detail | Info |
|--------|------|
| **Source** | Kaggle — UK COVID-19 Data Scrape |
| **URL** | https://www.kaggle.com/code/vascodegama/uk-covid-19-data-scrape |
| **Original API** | UK Government COVID-19 API |
| **Time Period** | 2020 – 2022 |
| **Regions Covered** | London, North West, Yorkshire, East Midlands, South West, East of England |

**Key columns used:**
- `date` — date of record
- `areaName` — UK region
- `newCasesByPublishDate` — daily new cases
- `cumCasesByPublishDate` — cumulative cases
- `newDeaths28DaysByPublishDate` — daily deaths
- `cumDeaths28DaysByDeathDateRate` — cumulative death rate

---

## 🗂️ Project Structure

```
covid19-demand-forecasting/
│
├── covid19.csv                    # Raw dataset (download from Kaggle)
├── cleanedcovid19_data.csv        # Cleaned dataset (output of preprocessing)
├── forecast_results.csv           # Model predictions vs actuals
│
├── model.py                       # Main Python script
│
├── dashboard.png                  # Power BI dashboard screenshot
├── COVID19_Dashboard.pbix         # Power BI dashboard file
│
└── README.md                      # Project documentation
```

---

## ⚙️ How It Works

### 1. Data Preprocessing
The raw dataset had several quality issues that were addressed systematically:

- **Date conversion** — `date` column converted from string to `datetime` using `pd.to_datetime()`
- **Missing values** — `newCasesByPublishDate` and `newDeaths28DaysByPublishDate` filled with `0` (no report = no cases)
- **Forward fill** — `cumCasesByPublishDate` forward-filled to preserve cumulative continuity
- **Dropping nulls** — remaining null rows removed after targeted fills
- **Deduplication** — duplicate records identified and dropped
- **Sorting** — entire DataFrame sorted by date ascending for correct time-series order

### 2. Exploratory Data Analysis (EDA)
Several analyses were performed before modelling:

- 📈 **Daily cases trend** — time-series plot showing all three major COVID-19 waves
- 🔄 **7-day rolling average** — smoothed trend line to reduce daily reporting noise
- 📅 **Day-of-week patterns** — bar chart revealing lower weekend reporting
- 🗺️ **Regional comparison** — cumulative cases grouped by area
- 💀 **Cases vs deaths** — dual-line comparison of infection and mortality trends

### 3. Feature Engineering
Two lag features were created from London's daily case series:

| Feature | Description |
|---------|-------------|
| `lag1` | Previous day's case count |
| `lag7` | Case count from 7 days prior (captures weekly seasonality) |

### 4. Machine Learning — XGBoost
An **XGBoost Regressor** was trained on the lag features using an 80/20 chronological train/test split (no shuffling — temporal order preserved).

```python
from xgboost import XGBRegressor

model = XGBRegressor(
    n_estimators=200,
    learning_rate=0.05,
    max_depth=5,
    random_state=42
)

model.fit(X_train, y_train)
y_pred = model.predict(X_test)
```

### 5. Model Evaluation
The model was evaluated against both standard metrics and a **naive baseline** (previous day's value as the prediction):

| Metric | Description |
|--------|-------------|
| **MAE** | Mean Absolute Error — primary evaluation metric |
| **RMSE** | Root Mean Squared Error — penalises large errors more heavily |
| **Baseline MAE** | Naive predictor (lag-1 shift) for comparison |
| **Improvement** | `Baseline MAE - Model MAE` — must be positive to confirm value |

Feature importance was also extracted and visualised — `lag1` had the highest importance, confirming that yesterday's cases are the strongest signal.

### 6. Power BI Dashboard
Results were exported to `forecast_results.csv` and connected to Power BI Desktop, where the dashboard was designed and built with:

- **KPI Cards** — Total cases (8M), Average cases (2.94K), Peak cases (29K)
- **Region Slicer** — Filter by UK region
- **Year & Quarter Slicer** — Drill into 2020, 2021, or 2022
- **Actual vs Predicted Line Chart** — Core ML output visualised for London
- **Total Cases by Area Bar Chart** — Regional comparison
- **Total New Cases Time-Series** — Full pandemic timeline with wave visibility

---

## 📈 Dashboard Preview

![COVID-19 Demand Forecasting Dashboard](dashboard.png)

The dashboard clearly shows:
- The three major UK COVID-19 waves
- The sharp Omicron surge in January 2022 reaching ~100K daily cases
- London and North West as the most affected regions
- The XGBoost model tracking actual case trends through the test period

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Python 3.x | Core programming language |
| pandas | Data loading, cleaning, and transformation |
| XGBoost | Gradient boosting regression model |
| scikit-learn | Train/test split, MAE, RMSE evaluation |
| matplotlib | EDA charts and model output plots |
| numpy | Numerical operations and RMSE calculation |
| Jupyter Notebook | Interactive development environment |
| Power BI Desktop | Interactive dashboard and reporting |

---

## 🚀 Getting Started

### Prerequisites
```bash
pip install pandas numpy xgboost scikit-learn matplotlib
```

### Steps
1. **Clone the repository**
```bash
git clone https://github.com/YOUR_USERNAME/covid19-demand-forecasting.git
cd covid19-demand-forecasting
```

2. **Download the dataset** from [Kaggle](https://www.kaggle.com/code/vascodegama/uk-covid-19-data-scrape) and place `covid19.csv` in the project root.

3. **Run the script**
```bash
python model.py
```

4. **Outputs generated:**
   - `cleanedcovid19_data.csv` — cleaned dataset
   - `forecast_results.csv` — actual vs predicted case numbers

5. **Open Power BI** — load `forecast_results.csv` and `cleanedcovid19_data.csv` into `COVID19_Dashboard.pbix` to refresh the dashboard.

---

## 🧠 Challenges Faced

Building this project solo came with real difficulties:

- **Messy real-world data** — missing values, type errors, and duplicates required careful, targeted handling rather than a single blanket approach.
- **Jupyter cell ordering bug** — the prediction block was accidentally placed before the model training block, causing a `NameError`. Fixed by restructuring the notebook execution order.
- **Feature engineering without data leakage** — creating lag features required careful index alignment to ensure no future data was used during training.
- **Meaningful evaluation** — implementing and comparing against a naive baseline was essential to prove the model added genuine forecasting value beyond a simple heuristic.
- **Dashboard design** — iterating the Power BI layout to present findings clearly for a non-technical audience took multiple redesigns.

---

## 🔮 Future Improvements

- [ ] Add external features — vaccination rates, mobility data, restriction levels
- [ ] Compare with LSTM and Facebook Prophet models
- [ ] Extend forecast horizon beyond single-day predictions
- [ ] Automate data refresh pipeline for near-real-time updates
- [ ] Deploy dashboard to Power BI Service for online sharing

---

## 📁 Key Output Files

| File | Description |
|------|-------------|
| `cleanedcovid19_data.csv` | Preprocessed dataset ready for analysis |
| `forecast_results.csv` | Date, actual cases, and predicted cases for London test period |

---

## 📜 References

- **Dataset:** [UK COVID-19 Data Scrape — Kaggle](https://www.kaggle.com/code/vascodegama/uk-covid-19-data-scrape)
- **Original Data Source:** [UK Government COVID-19 Dashboard API](https://coronavirus.data.gov.uk/)
- **XGBoost Paper:** Chen, T. & Guestrin, C. (2016). *XGBoost: A Scalable Tree Boosting System.* KDD 2016.

---

## 👤 Author

Built independently as a solo data science project.  
All stages — data collection, preprocessing, modelling, evaluation, and dashboard design — completed by one person.

---

> ⭐ If you found this project useful or interesting, feel free to star the repository!
