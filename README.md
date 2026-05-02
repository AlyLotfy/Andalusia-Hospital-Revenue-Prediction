#  Andalusia Hospital — Revenue Prediction

**Forecasting realized hospital revenue from clinic workload at the Hospital × Specialty × Date grain.**

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)](#)
[![Notebook](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](#)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?logo=scikit-learn&logoColor=white)](#)
[![pandas](https://img.shields.io/badge/pandas-150458?logo=pandas&logoColor=white)](#)

---

##  Overview

This repository explores the relationship between **operational workload** (working hours, number of clinics, actual booked slots) and **realized revenue** at **Andalusia Hospital Group**. The goal is a predictive model that lets hospital management forecast revenue for the next planning window given the staffing schedule.

The data is structured at the level of **Hospital × Specialty / Sub-specialty × Date**, which is fine-grained enough to capture per-department dynamics without exploding into noisy per-doctor variance.

This work was done in collaboration with the same hospital partner as my graduation project — [Pepper Medical Assistance Robot](https://github.com/AlyLotfy/Pepper-Medical-Assistance-Robot) — and demonstrates the analytical / forecasting side of the same operational dataset.

---

##  Problem Statement

> _Given how many clinics are open, how many working hours are scheduled, and how many slots are actually booked — what revenue should the hospital expect at each (hospital, specialty, date) cell?_

This is a **regression** problem with strong **time-series and panel** characteristics:

- Multiple hospitals (panel dimension)
- Multiple specialties per hospital (panel dimension)
- Daily granularity (time dimension)
- Repeated weekly / monthly seasonality
- Holiday & special-day effects

---

##  Dataset

The dataset links three operational dimensions with the financial outcome:

| Feature | Description |
| --- | --- |
| `Hospital` | Branch identifier |
| `Specialty` / `Sub-specialty` | Medical service line |
| `Date` | Calendar date (daily resolution) |
| `Working_Hours` | Total clinic hours scheduled |
| `Clinics` | Number of distinct clinic sessions |
| `Actual_Slots` | Slots actually filled |
| **`Revenue`** | **Target — realized revenue (currency)** |

> The raw dataset contains real, aggregated hospital data and is **not redistributed** in this repo to respect the partnership agreement. The notebook is reproducible against any dataset matching the schema above.

---

##  Approach

The notebook (`Final.ipynb`) walks through:

1. **Exploratory Data Analysis** — distribution of revenue per hospital and specialty, weekend / weekday patterns, missingness audit
2. **Feature Engineering** — calendar features (day-of-week, month, is-holiday), lag features, rolling-window aggregates, slot-utilization ratios
3. **Baselines** — naive (yesterday's value), seasonal naive (same day last week), linear regression
4. **Tree-based models** — Random Forest and gradient-boosted trees (LightGBM / XGBoost)
5. **Evaluation** — MAE, RMSE, MAPE, plus per-specialty error decomposition to surface where the model is weakest
6. **Error analysis** — residual plots, feature importance, partial dependence

---

##  Tech Stack

- **Python 3.10+**
- **pandas / NumPy** — data wrangling
- **matplotlib / seaborn** — EDA & residual plotting
- **scikit-learn** — preprocessing, baselines, cross-validation
- **LightGBM / XGBoost** — final models
- **Jupyter Notebook** — narrative + analysis

---

##  Reproducing The Results

```bash
git clone https://github.com/AlyLotfy/Andalusia-Hospital-Revenue-Prediction.git
cd Andalusia-Hospital-Revenue-Prediction

python -m venv venv
venv\Scripts\activate         # Windows
pip install pandas numpy scikit-learn lightgbm matplotlib seaborn jupyter

jupyter notebook Final.ipynb
```

---

##  Key Findings

- **Slot utilization** (`Actual_Slots / Capacity`) is the single strongest predictor — more so than raw working hours
- **Day-of-week** dominates seasonality; Egyptian Friday/Saturday weekends shape the curve
- Tree-based models comfortably outperform linear baselines, especially for high-volume specialties
- Per-specialty error analysis reveals that **niche specialties** with thin data are systematically under-forecast — a candidate for hierarchical modelling in future work

_(Concrete numeric results are in the notebook.)_

---

##  Future Work

- Hierarchical / mixed-effects model that pools weak signal across thin-data specialties
- Probabilistic forecasts (quantile regression / conformal prediction) instead of point estimates
- Integrating real-time signal from the Pepper robot's appointment-booking flow
- Productionizing as a Flask endpoint that the hospital ops team can hit on demand

---

##  Related Work

-  [Pepper Medical Assistance Robot](https://github.com/AlyLotfy/Pepper-Medical-Assistance-Robot) — the patient-facing side of the same hospital partnership

---

## 📄 License

Academic project, AAST Computer Engineering. All rights reserved.
The raw dataset is the property of Andalusia Hospital Group and is not redistributed.
