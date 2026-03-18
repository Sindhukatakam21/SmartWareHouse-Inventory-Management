# 🏭 Smart Warehouse Inventory Management


Modern warehouses bleed money in two ways:
- **Stockouts** → lost sales, unhappy customers, broken trust.
- **Overstock** → capital locked up, storage costs spiraling.

Traditional inventory reviews are periodic and manual — by the time someone notices a problem, the damage is done.

**Smart Warehouse** solves this with a fully automated pipeline: ingest raw data → engineer features → forecast demand → trigger alerts → surface insights on a beautiful Streamlit dashboard. Operations teams go from reactive firefighting to proactive decision-making.

---

## ✨ Features

| Module | What It Does |
|---|---|
| 📊 **Live Dashboard** | Real-time KPIs: inventory value, service level, critical alerts |
| 🔮 **7-Day Demand Forecast** | Confidence intervals, stockout probability, reorder recommendations |
| 🚨 **Alert Engine** | Threshold-based alerts when stock falls below reorder point |
| 🏢 **Supplier Analytics** | Reliability scores, delivery times, top performer rankings |
| 📈 **Historical Trends** | Date-range selectable demand & stock level trend charts |
| 🤖 **Model Insights** | DQN vs baseline policy comparisons, feature importance, cost-service tradeoffs |
| 📥 **Downloadable Reports** | Export any table view as CSV with one click |

---

## 📸 Demo

> Dashboard screenshots from the live deployment:

| Overview Dashboard | Forecasting Page |
|---|---|
| KPIs + Inventory Status + Alert Distribution | 7-day predictions with CI bands |

| Supplier Analytics | Inventory by Warehouse |
|---|---|
| Reliability scores + rating distribution | Bar chart by warehouse + category pie chart |

---

## 🏗 System Architecture

```
Raw Data Sources
      │
      ▼
┌─────────────────────────────────────────────────┐
│               DATA INGESTION LAYER               │
│  FRED Economic │ Google Trends │ UCI Retail II   │
│  M5 Walmart    │ Public Holidays│ Synthetic SC   │
└───────────────────────┬─────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│           PREPROCESSING & FEATURE ENGINEERING   │
│  • Missing value imputation (zero/median)        │
│  • Type conversion & datetime parsing            │
│  • Time features: lag-1/7/14, MA7, day_of_week  │
│  • Supplier features: reliability_score, rating  │
│  • Min-Max scaling for all continuous features   │
└───────────────────────┬─────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│         FORECASTING & DECISION MODELS           │
│  • 7-Day Demand Forecast (supervised regressors) │
│  • Policy Evaluation: EOQ, Fixed-Reorder, DQN   │
│  • 5-Fold CV, 80/20 train-test split            │
│  • Grid Search hyperparameter tuning            │
└───────────────────────┬─────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│         THRESHOLD-BASED ALERT ENGINE            │
│  Fires when: predicted_stock < reorder_point    │
│           or: stockout_probability > threshold  │
└───────────────────────┬─────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│          STREAMLIT DASHBOARD (Plotly)           │
│  KPIs │ Inventory │ Forecast │ Alerts           │
│  Suppliers │ Historical │ Model Insights        │
└─────────────────────────────────────────────────┘
```

---

## 📂 Project Structure

```
smart-warehouse-inventory/
│
├── data/
│   ├── raw/                         # Original source files
│   │   ├── fred_GDP.csv
│   │   ├── fred_CPIAUCSL.csv
│   │   ├── fred_UNRATE.csv
│   │   ├── fred_UMCSENT.csv
│   │   ├── fred_RSXFS.csv
│   │   ├── fred_HOUST.csv
│   │   ├── fred_INDPRO.csv
│   │   ├── google_trends_comprehensive.csv
│   │   ├── public_holidays_2015_2025.csv
│   │   ├── synthetic_suppliers.csv
│   │   ├── synthetic_order_history.csv
│   │   ├── online_retail_II.xlsx
│   │   ├── sales_train_validation.csv
│   │   ├── calendar.csv
│   │   └── sell_prices.csv
│   └── processed/                   # Cleaned, engineered outputs
│
├── notebooks/
│   ├── 01_data_preprocessing.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_forecasting_models.ipynb
│   ├── 04_policy_evaluation.ipynb
│   └── 05_model_insights.ipynb
│
├── src/
│   ├── ingestion.py                 # ETL pipeline
│   ├── preprocessing.py             # Cleaning & type conversion
│   ├── feature_engineering.py       # Feature construction
│   ├── forecasting.py               # 7-day demand forecasting module
│   ├── alert_engine.py              # Threshold-based alerting
│   ├── policy_evaluation.py         # EOQ / DQN policy comparison
│   └── supplier_analytics.py        # Supplier reliability metrics
│
├── dashboard/
│   └── app.py                       # Main Streamlit application
│
├── models/
│   └── saved/                       # Serialized model artifacts (.pkl)
│
├── requirements.txt
├── README.md
└── .gitignore
```

---

## ⚙️ Installation

### Prerequisites
- Python 3.8+
- pip or conda

### Steps

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/smart-warehouse-inventory.git
cd smart-warehouse-inventory

# 2. Create a virtual environment (recommended)
python -m venv venv
source venv/bin/activate        # On Windows: venv\Scripts\activate

# 3. Install all dependencies
pip install -r requirements.txt

# 4. Add your data files into data/raw/

# 5. Run preprocessing + feature engineering
python src/preprocessing.py
python src/feature_engineering.py

# 6. Train forecasting models
python src/forecasting.py

# 7. Launch the dashboard
streamlit run dashboard/app.py
```

The dashboard will be available at `http://localhost:8501`

---

## 🖥️ Usage

Once the dashboard is running:

1. **Dashboard Tab** — View live KPIs: Total Products, Inventory Value, Critical Alerts, Service Level.
2. **Inventory Tab** — Filter by warehouse/category, inspect critical items table, download CSV.
3. **Forecasting Tab** — Select a product, view 7-day demand forecast with confidence intervals and recommended reorder quantity.
4. **Alerts Tab** — Filter alerts by type/severity, view timeline of recent events.
5. **Suppliers Tab** — Compare supplier reliability scores, delivery times, and ratings.
6. **Historical Tab** — Select a date range to explore demand and stock level trends.
7. **Model Insights Tab** — Compare DQN vs baseline policies on cost vs service level charts.

---

## 📊 Results & KPIs

Observed metrics from the development demo:

| Metric | Value |
|---|---|
| Total Products Tracked | 250 |
| Total Inventory Value | $30.72M |
| Service Level | 100.0% |
| Average Stock Level | 489 units |
| Average Days of Stock | 44.4 days |
| Items Needing Attention | 55 |
| Critical Alerts | 5 |

**Model performance:**
- Forecast accuracy (MAPE & RMSE) tracked per SKU; high-volume SKUs yield the most accurate predictions due to richer training history.
- DQN-derived policy outperforms naive baselines on cost-service tradeoffs in simulated evaluations.
- 5-fold cross-validation used for robust model selection; results averaged across multiple random seeds.

---

## 🧠 Models & Methodology

### Forecasting
- Supervised regressors trained on engineered time-series features.
- Features: `lag_1`, `lag_7`, `lag_14`, `MA7`, `day_of_week`, `is_weekend`.
- Output: `predicted_demand`, `predicted_stock`, confidence interval bounds, `stockout_probability`, `recommended_order`.
- Validation: 80/20 train-test split, 5-fold CV, grid search hyperparameter tuning.
- Data augmentation: rolling-window samples, seasonal bootstraps, additive noise.

### Inventory Policy Evaluation
Policies compared:
- **Fixed Reorder Point** — Classic (s, Q) policy.
- **EOQ Heuristic** — Economic Order Quantity.
- **Random Baseline** — Lower bound reference.
- **DQN Policy** — Deep Q-Network trained in simulation (from prior phase).

Metrics: total cost, average inventory level, service level (fill rate).


---

## 🛠 Tech Stack

| Layer | Tools |
|---|---|
| Language | Python 3.8+ |
| Dashboard | Streamlit, Plotly |
| ML/Forecasting | scikit-learn, NumPy, pandas |
| RL Policy | (DQN — from prior phase) |
| Visualization | Plotly, Matplotlib |
| Data Sources | FRED, Google Trends, UCI Retail II, M5 Walmart |
| Dev Environment | Google Colab / Local (Intel i7, 16 GB RAM) |

---

## 📚 References

- Sutton, R. S., & Barto, A. G. — *Reinforcement Learning: An Introduction*
- [Streamlit Documentation](https://streamlit.io)
- [Plotly Python Documentation](https://plotly.com/python/)
- pandas, NumPy, scikit-learn official documentation
- McKinsey & Company — *Supply Chain Inventory Optimization*
- UCI Machine Learning Repository — Online Retail II Dataset
- M5 Forecasting Competition — Walmart Sales Data

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -m 'Add: your feature description'`
4. Push to the branch: `git push origin feature/your-feature-name`
5. Open a Pull Request

---



## 👤 Author

Sindhu Katakam

Smart Warehouse Inventory Management — RL & AI/ML Project

---

<div align="center">
⭐ If this project helped you, give it a star — it helps others discover it!
</div>
