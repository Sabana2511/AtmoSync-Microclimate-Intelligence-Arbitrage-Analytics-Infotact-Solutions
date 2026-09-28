# INFOTACT-PROJECT
# 🚚 AtmoSync: Agricultural Supply Chain & Spoilage Analytics Engine

An end-to-end data engineering and analytics pipeline designed to detect micro-climate anomalies, evaluate facility queue bottlenecks, and quantify crop quality degradation across European commodity supply chains.

---

## 📌 Executive Summary

Traditional supply chain metrics rely on static transit times and macro-weather forecasts, missing localized micro-climate shifts and facility unloading delays. **AtmoSync** processes multi-modal IoT sensor telemetry and logistics data across **53,305 shipment records** to uncover operational bottlenecks, quantify quality decay curves, and optimize fleet efficiency.

### Key Analytical Highlights
* **5-Hour Queue Inflection Point:** Facility unloading delays exceeding 5.0 hours trigger a **>70% collapse** in quality maintenance across Corn, Wheat, and Rice.
* **Extreme Quality Decay:** Quality maintenance retention drops by **90.7%** when total queue time exceeds 10 hours.
* **Fleet Inefficiencies:** Long-haul motorbike routes suffer from a **39.85% high-delay rate**, inflating fuel costs up to **$182.31/trip** due to excessive engine idling.

---

## 🛠️ Data Pipeline & Engineering Methodology

### 1. Ingestion & Preprocessing
* **Raw Dataset:** 53,305 row-level records capturing vehicle telematics, storage conditions, transit times, and spoilage risk.
* **Temporal Parsing:** Derived temporal features (`Year`, `Month_Name`, `Day_Of_Week`, `Is_Weekend`) for time-series aggregation.

### 2. Non-Destructive Outlier Handling (Winsorization)
To prevent extreme mathematical skew while preserving 100% of raw observations, 1st and 99th percentile capping was applied to:
* `Delivery_Time_Capped`
* `Fuel_Costs_Capped`
* `Storage_Temperature_Capped`
* `Vibration_Level_Capped`

### 3. Feature Engineering & Artifact Resolution
* **Quality Maintenance Ratio:** Derived composite metric evaluating storage efficiency relative to queue delay ($\text{Warehouse\_Storage\_Time} / \text{Queue\_Time}$).
* **Division-by-Zero Handling:** Resolved near-zero denominator mathematical artifacts (`inf` values) by casting to `NaN` followed by percentile capping.
* **Operational Threshold Flags:**
  * `High_Queue_Delay_Flag`: Binary indicator for facility queue times $> 5.0$ hours.
  * `High_Spoilage_Risk_Flag`: Binary indicator for spoilage risk indices $> 1.20$.
  * `Transit_Duration_Class`: Tertile route classification (`Short`, `Medium`, `Long`).

---

## 📊 Analytics & BI Architecture (Star Schema Outputs)

The pipeline exports two structured datasets optimized for direct import into **Power BI** / **Tableau**:

1. **`EURO_Crops_Transformed.csv` (Row-Level Fact Table)**
   * *Dimensions:* 53,305 rows × 35 columns
   * *Contains:* Full raw fields, winsorized variables, temporal attributes, and engineered KPI flags.
2. **`PowerBI_Aggregated_KPIs.csv` (Pre-Aggregated Dimensional Table)**
   * *Dimensions:* 27 grouped metric rows
   * *Groupings:* `Crop_Type` × `Vehicle_Type` × `Transit_Duration_Class`
   * *Purpose:* Optimized pre-computed table for fast executive dashboard rendering.

---

## 📈 Power BI Dashboard Layout Specification

The dashboard is structured across three core reporting views:

* **View 1: Executive Overview:** High-level KPI cards (Total Shipments, Mean Queue Delay, High Spoilage Rate, Average Fuel Expense) and monthly trend lines.
* **View 2: Bottleneck & Fleet Profiling:** Heatmaps and matrix grids mapping `Vehicle_Type` against `Transit_Duration_Class` to identify idle fuel loss and queue bottlenecks.
* **View 3: Quality Degradation & Loss Analysis:** Decomposition trees tracking quality maintenance drops by crop type across delay severity bins (`0-2h` to `>10h`).

---

## 🚀 How to Run the Pipeline

### Prerequisites
* Python 3.8+
* Required Libraries: `pandas`, `numpy`, `matplotlib`, `seaborn`

### Execution

```bash
# 1. Clone the repository
git clone [https://github.com/your-username/AtmoSync-Analytics.git](https://github.com/your-username/AtmoSync-Analytics.git)
cd AtmoSync-Analytics

# 2. Run the main processing and aggregation script
python main_pipeline.py
