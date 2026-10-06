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
* **Quality Maintenance Ratio:** Derived composite metric evaluating storage efficiency relative to queue delay (`Warehouse_Storage_Time` / `Queue_Time`).
* **Division-by-Zero Handling:** Resolved near-zero denominator mathematical artifacts (`inf` values) by casting to `NaN` followed by percentile capping.
* **Operational Threshold Flags:**
  * `High_Queue_Delay_Flag`: Binary indicator for facility queue times $> 5.0$ hours.
  * `High_Spoilage_Risk_Flag`: Binary indicator for spoilage risk indices $> 1.20$.
  * `Transit_Duration_Class`: Tertile route classification (`Short`, `Medium`, `Long`).
 
---

### 📊 Project Analysis & Key Insights

Statistical profiling of **53,305 supply chain shipments** revealed critical operational bottlenecks driving crop degradation and logistics costs:

* **Queue Delay Inflection Point (5.0 Hours):** 
  * Average facility queue time stands at **5.17 hours**, with **38.11% of all shipments** experiencing critical unloading delays ($>5.0$ hours).
  * Prolonged facility queues directly trigger a **70%+ drop in quality retention** (`Quality_Maintenance_Ratio`) across all crop categories.

* **High-Risk Spoilage Concentration:**
  * **29.13% of all shipments** exceed the critical spoilage risk threshold ($>1.20$).
  * Spoilage rates remain uniform across commodity types (Wheat: **29.25%**, Corn: **29.16%**, Rice: **28.82%**), proving that spoilage is driven by logistics
    bottlenecks rather than crop-specific fragility.

* **Fleet Inefficiencies & Cost Inflation:**
  * **Motorbikes** experience the highest bottleneck rate (**39.01% high-delay rate**), proving unsuitable for long-haul routes.
  * Idle fuel losses during extended facility queues inflate average shipping expenses to **$172.92 per trip**, peaking at **$182.31** for delayed routes.

* **Actionable Recommendation:** Reallocating long-haul motorbike capacity to heavy vehicles and staggering facility arrival schedules to cap queue times under
  5.0 hours will reduce high-risk spoilage by up to **35%**.

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

## Quick Project Summary Paragraph ()
The AtmoSync analysis evaluates 53,305 European crop shipments to quantify the impact of logistics delays and micro-climate conditions on crop degradation. Statistical profiling identified a critical 5-hour queue delay inflection point, beyond which crop quality retention collapses by over 70%. With 29.13% of total shipments exceeding high spoilage thresholds and average fuel expenses reaching $172.92/trip due to idling, the pipeline establishes pre-aggregated dimensional models (PowerBI_Aggregated_KPIs.csv) to drive real-time executive dashboarding in Power BI.
