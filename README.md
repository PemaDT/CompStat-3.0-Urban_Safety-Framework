# CompStat 3.0: A Predictive Urban Safety Framework via Multi-Source Correlation of Digital Disorder Indicators and NYPD Arrest Surges (2021–2025)

**Author:** Pema D. Tamang  
**Academic Affiliation:** New York Institute of Technology (College of Engineering and Computer Science)  
**Course:** DTSC 870 – Non-Thesis Capstone Project  
**Instructor:** Dr. Sandra Kopecky  

---

## 📌 Project Overview & Personal Motivation
As a resident of New York City for 18 years with an undergraduate background in Criminal Justice and Law, I engineered **CompStat 3.0** to bridge a critical "reactive gap" in civic safety management. Traditional municipal frameworks operate retrospectively—allocating resources *after* a criminal incident occurs. 

CompStat 3.0 tests the **Digitized Broken Windows Theory** by treating daily community-generated 311 non-emergency quality-of-life requests as "digital sensors" of neighborhood social friction. By mining these signals, this project provides a validated, reproducible blueprint for forecasting formal law enforcement demand surges before they escalate.

---

## 🛠️ System Architecture & Data Engineering Pipeline

The complete pipeline was developed natively in Python within a cloud-computing environment to handle severe hardware and memory constraints when processing over **13 million records**.

1. **Automated Data Extraction (Milestone 2):** Engineered an asynchronous, token-authenticated ETL pipeline using the Socrata API (`Sodapy`) to pull from the NYC Open Data Portal. Implemented a memory-safe chunked extraction loop (50,000-row bins) converting string fields to compact categorical datatypes, writing directly to serialized Apache Parquet format to prevent cloud-computing memory failures.
2. **Geospatial Point-in-Polygon Joins (Milestone 3):** Because raw 311 records do not possess law enforcement administrative identifiers, I utilized `GeoPandas`, `Shapely`, and `Fiona` to run high-performance spatial indexing (`sindex`). This matched the latitude/longitude coordinates of millions of rows directly into official NYPD Precinct boundary map shapefiles. 
3. **Temporal Aggregation:** Combined the distinct data flows into a unified master time-series dataset consisting of **149,886 daily precinct observations** spanning 78 unique precincts from January 1, 2021, through April 15, 2026.

---

## 🧠 Machine Learning Engine & Statistical Methodology

To provide automated context and mitigate simple correlation pitfalls, a two-stage modeling approach was designed:

* **Stage 1: Unsupervised Precinct Profiling (K-Means):** Applied K-Means clustering ($k=4$) on normalized aggregate historical footprints to group neighborhoods into baseline disorder archetypes (*Low, Moderate, High, Extreme*). This cluster profile was injected back into the master dataset as a static context feature, forcing the predictive model to judge a daily complaint spike relative to that precinct’s historical reputation.
* **Stage 2: Supervised Ensemble Classification (Random Forest):** A Random Forest Classifier (100 estimators, max depth of 15) was trained to execute binary classification. The target variable designated a "High Activity Surge" (1) as any precinct-day that exceeded that specific precinct's historical arrest median. 
* **Data Leakage Mitigation:** The dataset was strictly split chronologically: **2021–2023 for Training** and **2024–2025 for Testing**. Random k-fold splitting was avoided to strictly preserve time-series independence.

---

## 📊 Key Findings & Results

* **Predictive Capability:** The model achieved an evaluation accuracy of **59.41%**, delivering a statistically significant **6.65% lift** over the No Information Rate (NIR) baseline of **52.76%**.
* **Top Digital Sensors:** Feature sensitivity analysis via **Gini Importance** proved that interpersonal physical frictions hold the highest predictive weight, while passive property issues (like graffiti) carry minimal predictive signal.
  * *Noise - Residential:* **15.07%** predictive weight.
  * *Blocked Driveway:* **14.44%** predictive weight.
  * *Illegal Parking:* **13.39%** predictive weight.
* **Ecological Validation:** The pipeline proved robust across two entirely different urban mechanisms. It correctly forecasted surges in high-activity commercial/tourist zones (Precinct 14 - Midtown South) driven by daily commuter density, as well as high-density residential zones (Precinct 75 - East New York and Precinct 40 - Mott Haven) driven by structural socioeconomic stress.

---

## 🔮 Peer Review Adjustments & Future Work

Incorporating critical feedback raised during the peer-review cycle, the development roadmap for CompStat 3.0 highlights the following architectural updates:

1. **Addressing Overdispersion:** Because arrest count data is discrete and exhibits high variance over the mean, future iterations will move away from binary classification to evaluate robust count models, comparing Random Forest performance against **Poisson and Negative Binomial Regression**.
2. **Spatial Lag Modelling:** Crime and urban disorder are geographically fluid. Future iterations will incorporate spatial lag variables to capture neighboring precinct behavior, accounting for real-world spillover dynamics across precinct borders.
3. **Natural Language Processing (NLP):** Integrating text-mining layers using NLP on the raw 311 "Descriptor" columns to automatically isolate high-confrontation dispute text from low-risk anomalies.

---

## 📂 Repository Contents
* `/notebooks/M2_Data_Ingestion_API.ipynb`: Automated data gathering loop utilizing Socrata developer credentials.
* `/notebooks/M3_Spatial_Processing.ipynb`: Vectorized point-in-polygon calculation joining coordinate geometries into polygon layers.
* `/notebooks/M4_Predictive_Modeling.ipynb`: Standard pipeline containing K-Means data normalization, Random Forest modeling, and validation plots.
* `/results/`: Visual artifacts including confusion matrix heatmaps, feature importance rankings, and cluster distribution datasets.
* `/models/rf_v1_model.pkl`: Serialized, ready-to-deploy predictive engine optimized using `joblib`.
* `requirements.txt`: Specified Python library dependencies ensuring full environment reproducibility.
