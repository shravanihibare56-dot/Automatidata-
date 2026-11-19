# Exploratory Data Analysis & Fare Estimation Readiness on NYC Yellow Taxi Trips

**Author:** Shravani Satish Hibare  
**Framework:** PACE (Plan, Analyze, Construct, Execute) | **Tools:** Python, Pandas, NumPy, Matplotlib, Seaborn, Jupyter Notebook  

---

## Project Overview

This project presents a comprehensive Exploratory Data Analysis (EDA) and data quality assessment conducted for **Automatidata**, a data consulting firm partnering with the **New York City Taxi and Limousine Commission (NYC TLC)**. Using the 2017 NYC Yellow Taxi trip dataset (**22,699 trip records** across **18 variables**), this analysis examines trip distance distributions, fare structures, payment methods, tipping patterns, and extreme data anomalies to establish a clean, validated foundation for building a predictive taxi fare estimation model.

---

## Business Understanding

* **Stakeholders:** New York City Taxi and Limousine Commission (NYC TLC) leadership, operations teams, and Automatidata project managers (Luana Rodriquez, Deshawn Washington, and Uli King).
* **Business Problem:** NYC TLC plans to build a user-friendly application that estimates taxi fares accurately before a ride starts. However, raw trip logs contain operational anomalies—such as zero-distance fares, disputed/negative charges, flat-rate airport rides, and unrecorded cash tips—that can distort predictive models if left unaddressed.
* **Domain Context:** NYC yellow cabs operate under regulated metered tariffs combining an initial base charge, per-mile and per-minute rates, peak/night surcharges, MTA taxes, tolls, and fixed flat rates (such as JFK Airport trips under `RatecodeID = 2`). Understanding real-world trip behavior and anomalies is critical before applying statistical hypothesis tests or regression algorithms.

---

## Data Understanding

The dataset (`2017_Yellow_Taxi_Trip_Data.csv`) was sourced from NYC TLC trip logs for **2017** and contains **22,699 rows** and **18 columns** with **0 missing (null) values**.

### Dataset Schema & Key Variables
* **Identifiers & Timestamps:** `Unnamed: 0` (Trip ID), `VendorID` (1 = Creative Mobile Technologies, 2 = VeriFone Inc.), `tpep_pickup_datetime`, `tpep_dropoff_datetime`
* **Trip & Rate Details:** `passenger_count`, `trip_distance`, `RatecodeID` (Standard, JFK, Newark, Nassau/Westchester, Negotiated), `store_and_fwd_flag`, `PULocationID`, `DOLocationID`
* **Fare & Payment Breakdown:** `fare_amount`, `extra`, `mta_tax`, `tip_amount`, `tolls_amount`, `improvement_surcharge`, `total_amount`, `payment_type`

### Summary Statistics & EDA Findings (From Python Notebook)
| Variable / Category | Key Statistical Metrics | Key Findings & Data Limitations |
| :--- | :--- | :--- |
| **Data Completeness** | **22,699 rows × 18 columns** \| **0 nulls** | Structurally complete dataset, though logical and operational anomalies require filtering. |
| **Trip Distance (`trip_distance`)** | Mean: **2.91 mi** \| Median: **1.61 mi** \| Max: **33.96 mi** | Heavily right-skewed; **148 trips** recorded **`0.00` miles** despite charging positive fares (likely cancelled trips or stationary meter waits). |
| **Base Fare (`fare_amount`)** | Mean: **$13.03** \| Median: **$9.50** \| Min: **-$120.00** \| Max: **$999.99** | Extreme right skew; top fare (`$999.99`) occurred on a short `2.60`-mile trip (`$1,200.29` total), and second-highest (`$450.00`) had `0.00` miles. |
| **Total Amount (`total_amount`)** | Mean: **$16.31** \| Median: **$11.80** \| Min: **-$120.30** \| Max: **$1,200.29** | **14 records** have negative total amounts (`< $0`), linked to disputed (`payment_type = 4`) or no-charge (`payment_type = 3`) rides. |
| **Payment Type (`payment_type`)** | Credit Card: **15,265 (67.25%)** \| Cash: **7,267 (32.01%)** \| No Charge: **121** \| Dispute: **46** | Credit cards dominate ride volume, followed by cash transactions. |
| **Tip Amount (`tip_amount`)** | Overall Mean: **$1.84** \| Max: **$200.00** \| Credit Card Mean: **~$2.73** | Cash tips are logged as **`$0.00`** in TLC records, creating reporting bias when evaluating overall tipping behavior. |
| **Passenger Count (`passenger_count`)** | Range: **0 to 6** \| Median: **1** \| Mean: **1.64** | Single-passenger trips account for the majority (**~70.9%**); zero-passenger entries require validation. |

### Exploratory Visualizations

<img width="1812" height="752" alt="Screenshot 2026-11-19 180743" src="https://github.com/user-attachments/assets/54dfa3e4-b212-4df7-9c60-bd36ced6244c" />



---

## Modeling and Evaluation

### Phase 1: EDA & Statistical Data Readiness (Completed)
Using Python (`Pandas`, `NumPy`, `Matplotlib`, `Seaborn`), systematic exploratory checks were executed:
* **Distribution & Outlier Analysis:** Generated box plots and histograms for `trip_distance`, `fare_amount`, `tip_amount`, and `total_amount`, confirming right-skewed distributions and isolating extreme monetary outliers (`> $200`) and zero-mile anomalies.
* **Correlation & Feature Relationships:** Verified strong positive linear correlation between `trip_distance`, derived trip duration (`tpep_dropoff_datetime - tpep_pickup_datetime`), and `fare_amount`.
* **Feature Engineering Scope:** Identified trip duration (in minutes), rush-hour indicators, and airport flat-rate flags (`RatecodeID = 2`) as essential features for regression modeling.

### Phase 2: Predictive Modeling Plan (Construct Stage)
* **Target Variable:** `fare_amount` (continuous numerical target for pre-trip fare prediction).
* **Primary Model:** **Multiple Linear Regression** (selected for interpretability and direct coefficient explanation for NYC TLC stakeholders), benchmarked against regularized/tree-based regressors.
* **Evaluation Metrics:** Model accuracy and error spread are evaluated using **$R^2$ (Coefficient of Determination)**, **Mean Absolute Error (MAE)**, and **Root Mean Squared Error (RMSE)** after validating linear regression assumptions.

---

## Conclusion

### Actionable Recommendations for NYC TLC
1. **Clean Invalid & Anomalous Records:** Remove negative fare entries (`< $0`), zero-passenger trips, and unexplained `0.00`-mile rides before training predictive models.
2. **Handle Extreme Fare Outliers & Flat Rates Separately:** Cap or isolate extreme anomalies (such as `$999.99` / `$450.00` outliers) and segment fixed-rate airport rides (`RatecodeID = 2`) from standard metered trips.
3. **Mitigate Cash-Tip Reporting Bias:** Restrict gratuity modeling and tip-rate metrics strictly to credit card transactions (`payment_type = 1`), where tips are reliably captured.

### Future Steps
* Conduct statistical hypothesis testing (A/B testing) on payment types and fare differences across vendors and time windows.
* Train, tune, and validate the **Multiple Linear Regression** fare estimation model using cleaned features (`trip_distance`, engineered `trip_duration`, `RatecodeID`, and rush-hour flags).
* Deploy an interactive fare-estimator prototype using **Streamlit** for stakeholder review.
*
