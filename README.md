# 🛣️ Asphalt Pavement Degradation Analysis

A big data project investigating how **traffic loads** and **environmental factors** interact to cause asphalt pavement degradation across U.S. counties, using a 15M+ record dataset from the FHWA LTPP InfoPave system.

> 📄 Full research paper: [The Interplay of Traffic Loads and Environmental Factors in Asphalt Pavement.pdf](https://raw.githubusercontent.com/surthesh25/Pavement-Degradation-Analysis/main/The_Interplay_of_Traffic_Loads_and_Environmental_Factors_in_Asphalt_Pavement.pdf)


---

## 📌 Project Objectives

| # | Objective |
|---|---|
| 1 | Evaluate how AADTT and ESAL influence pavement performance metrics (MRI and cracking extent) |
| 2 | Assess the impact of precipitation and freeze-thaw cycles on regional pavement degradation |
| 3 | Preprocess and clean a 15M+ record dataset using PySpark on Databricks |
| 4 | Explore correlations between variables using descrigit add README.mdptive statistics and visualisations |
| 5 | Build and compare ML models (Linear Regression, Random Forest, Gradient Boosted Trees) |

---

## 📁 Repository Structure

```
Pavement-Degradation-Analysis/
│
├── src/
│   └── Project_Working_System_Databricks.ipynb   # Main PySpark notebook (full pipeline)
│
├── The_Interplay_of_Traffic_Loads_and_Environmental_Factors_in_Asphalt_Pavement.pdf
│
├── requirements.txt
├── .gitignore
└── README.md
```

---

## 📊 Dataset

**Source:** [FHWA LTPP InfoPave](https://infopave.fhwa.dot.gov/Data/DataSelection)
**Scale:** 15M+ records across 2,132+ U.S. pavement test sections

| File | Contents |
|---|---|
| `data_traffic.csv` | AADTT, ESAL, annual truck volume by section and year |
| `data_performance.csv` | MRI, alligator crack area/length by severity (L/M/H) |
| `data_climate.csv` | Annual precipitation, temperature, freeze-thaw cycles |
| `data_pavement_structure.csv` | Surface type, construction year, pavement composition |

### Key Attributes

| Category | Attributes |
|---|---|
| **Traffic Load** | `ANNUAL_ESAL_TREND`, `AADTT_ALL_TRUCKS_TREND`, `ANNUAL_TRUCK_VOLUME_TREND` |
| **Pavement Condition** | `GATOR_CRACK_A_L/M/H`, `BLK_CRACK_A_L/M/H`, `MRI` |
| **Environmental** | `TOTAL_ANN_PRECIP`, `MEAN_ANN_TEMP_AVG`, `FREEZE_THAW_YR` |
| **Identifiers** | `SHRP_ID`, `STATE_CODE`, `YEAR`, `CONSTRUCTION_NO` |

> **Note:** Dataset files are not included in this repository due to their size.
> Download them from: [https://infopave.fhwa.dot.gov/Data/DataSelection](https://infopave.fhwa.dot.gov/Data/DataSelection)
> Upload them to `/FileStore/tables/` in your Databricks workspace.

---

## ⚙️ Setup & How to Run

### 1. Set Up Databricks Environment

1. Go to [https://community.cloud.databricks.com](https://community.cloud.databricks.com) and sign in
2. Navigate to **Compute** → **Create Cluster**
   - Name: `pavement-cluster`
   - Runtime: DBR 12.2 LTS or later
3. Go to **Workspace** → **Users** → your email → **Create** → **Notebook**
   - Name: `PavementAnalysis`
   - Language: Python

### 2. Upload Dataset Files

1. Click **Data** (left sidebar) → **Add Data** → **Upload File**
2. Upload the four CSV files:
   - `data_climate.csv`
   - `data_performance.csv`
   - `data_traffic.csv`
   - `data_pavement_structure.csv`
3. Files will be stored at `/FileStore/tables/`

### 3. Import and Run the Notebook

1. In your Databricks workspace, click **Import**
2. Upload `notebooks/Project_Working_System_Databricks.ipynb`
3. Attach it to your running cluster
4. Click **Run All** to execute the full pipeline

---

## 🔍 Analysis Overview

### 📦 Data Pipeline
```
Load CSVs → Type Cast → Join on (STATE_CODE, SHRP_ID, YEAR)
→ Drop Nulls → Remove Duplicates → Log Transform Skewed Variables
→ Save Master Dataset → EDA → ML Modelling
```

### 🚦 Traffic Analysis (Steps 5–6)

| Analysis | Description |
|---|---|
| Correlation Heatmap | Pearson correlation between traffic variables and cracking types |
| ESAL vs Cracking Bar Chart | Individual impact of Annual ESAL on cracking severities |
| Grouped Bar Chart | Three traffic variables vs six cracking severity types |
| U.S. Choropleth Map | State-level average medium alligator cracking distribution |
| Traffic vs MRI Line Plot | Correlation of traffic variables with pavement roughness |
| ML Models (Spark MLlib) | Linear Regression, Random Forest, GBT — predict `GATOR_CRACK_A_M` |

### 🌦️ Environmental Analysis (Steps 7–9)

| Analysis | Description |
|---|---|
| Correlation Heatmap | Environmental variables vs gator-crack severity levels |
| Cracking by Precipitation Group | Average crack area across rainfall intensity groups |
| Cracking by Temperature Regime | Cold vs mild climate effect on cracking severity |
| Cracking Severity by State | State-level comparison across FL, MD, NY, PA, VA |
| Environmental vs High Cracking | Correlation of temperature, precipitation, freeze-thaw with `GATOR_CRACK_A_H` |
| ML Models (scikit-learn) | Linear Regression, Random Forest, Gradient Boosting — predict `GATOR_CRACK_A_H` |

---

## 📈 Key Results

### Traffic Analysis — ML Model Performance

| Model | RMSE | MAE | R² |
|---|---|---|---|
| Linear Regression | 36.54 | 16.63 | 0.03 |
| Random Forest | 33.62 | 14.39 | 0.18 |
| **Gradient Boosted Trees** | **30.46** | **11.69** | **0.32 ✅ Best** |

### Environmental Analysis — ML Model Performance

| Model | R² | RMSE | MAE |
|---|---|---|---|
| Linear Regression | 0.04 | 19.40 | 9.56 |
| **Random Forest** | **0.81** | **8.51** | **1.95 ✅ Best** |
| Gradient Boosting | 0.80 | 8.78 | 2.58 |

### Feature Importance (Random Forest — Traffic)

| Feature | Importance |
|---|---|
| `AADTT_ALL_TRUCKS_TREND` | 0.3835 |
| `ANNUAL_ESAL_TREND` | 0.3577 |
| `ANNUAL_TRUCK_VOLUME_TREND` | 0.2589 |

### Key Findings
- **Medium alligator cracking (`GATOR_CRACK_A_M`)** has the strongest correlation with traffic variables (ESAL: 0.16, Truck Volume: 0.14)
- **Block cracking** and **MRI** are least responsive to traffic
- **States with heavy routes** (California, Texas, Virginia) show higher pavement deterioration
- **Environmental variables** are stronger predictors of high-severity cracking (R² = 0.81) than traffic alone (R² = 0.32)
- **Freeze-thaw cycles** show a negative correlation (−0.17) with high-severity cracking
- **Florida and Maryland** exhibit unexpectedly high cracking despite low freeze-thaw activity, suggesting material or maintenance differences

---

## 🧰 Tech Stack

| Tool | Purpose |
|---|---|
| [Apache Spark / PySpark](https://spark.apache.org/) | Distributed data processing of 15M+ records |
| [Databricks Community Edition](https://community.cloud.databricks.com/) | Cloud notebook and cluster environment |
| [Spark MLlib](https://spark.apache.org/docs/latest/ml-guide.html) | Gradient Boosted Trees and Random Forest (traffic models) |
| [scikit-learn](https://scikit-learn.org/) | Linear Regression, Random Forest, Gradient Boosting (environmental models) |
| [Pandas](https://pandas.pydata.org/) | Data manipulation for visualisation |
| [Seaborn](https://seaborn.pydata.org/) | Statistical visualisations |
| [Matplotlib](https://matplotlib.org/) | Plotting |
| [Plotly](https://plotly.com/python/) | Interactive U.S. choropleth map |

---

## 👥 Team

**Course:** AIT 614 — Big Data Essentials | George Mason University
**Instructor:** Dr. Duoduo (Lindi) Liao
**Submission:** April 29, 2025

| Name | Department | Email |
|---|---|---|
| Bala Ashrith Reddy Kommareddy | Data Analytics Engineering | bkommare@gmu.edu |
| Mohan Silambarasu Elangkumaran | Applied Information Technology | melangku@gmu.edu |
| Surthesh Velu Samy | Data Analytics Engineering | svelusam@gmu.edu |

---

## 📄 Data License

This project uses open-access federal datasets from the **FHWA LTPP InfoPave** system.
Usage is subject to [FHWA data terms](https://infopave.fhwa.dot.gov/).
Code is available under the [MIT License](LICENSE).
