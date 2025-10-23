# Auto-car-rental
regression model



# Auto-Rental
Predict &amp; prevent ride cancellations at Auto Rental

# Auto Rental — Ride Cancellation Prediction

**Predict cancellations early and trigger targeted interventions to keep rides on track and customers confident.**  
High-recall **logistic regression** with engineered **time / distance / channel** features; threshold tuned for proactive ops.

---

## TL;DR
- Dataset: ~**10,000** rides, **19** attributes; target = **Car_Cancellation (0/1)**.  
- Models compared: **Decision Tree, Logistic Regression, Random Forest**.  
- Chosen model: **Logistic Regression** for the best **recall** and **balanced accuracy** to catch real cancellations.  
- Threshold tuned to **0.40** → **Sensitivity ≈ 80.4%**, **Specificity ≈ 66.8%**, **Accuracy ≈ 67.9%**, **NPV ≈ 97.5%**.  
- Key drivers: **booking channel (online/mobile ↑), time-of-day, booking hour, month, trip distance (shorter trips ↑)**.  
- Ops playbook: **re-confirmations**, **peak-hour staffing**, **driver incentives**, **dispatch tweaks**.

---

## Business Problem
Driver cancellations leave riders without service, hurt trust/reputation, and reduce revenue. We need to **predict which bookings will cancel** and **intervene early** (incentives, re-assignments, confirmations).

### Goals
- **Business:** (1) Flag likely cancellations in advance, (2) understand *why* to design countermeasures.  
- **Analytical:** Build a robust classifier and identify the most influential predictors.

---

## Data
- **Records/Fields:** ~10k rows, 19 attributes (IDs, geo, timestamps, booking channels, cancellation).  
- **Target:** `car_cancellation` (0/1).  
- **Types:** integer/character/numeric; timestamps normalized to date/hour components.

### Missingness & Scope
- Major missing values for destination/geo on **hourly** and **long-distance** rides → to avoid bias, analysis/modeling focus on **point-to-point** rides where key fields are complete.  
- Dropped high-missing columns (e.g., city IDs, `to_date`) and redundant ones.

---

## Feature Engineering & Transformation
- **Distance** (`distance_travelled`) computed from lat/long (geosphere).  
- Time features from timestamps: **from_hour**, **from_month**, **booking_hour**, **booking_month**.  
- Booking channel flags: **online_booking**, **mobile_site_booking**, and an inferred **in_person_booking**.  
- Removed high-cardinality or redundant fields (IDs, raw lat/long after distance, one of the perfectly correlated month columns, etc.).

---

## EDA — What Drives Cancellations
- **Channel effect:** **Online** and **mobile** bookings cancel more often than in-person.  
- **Ride start time (from_hour):** cancellations peak **5–9 PM**.  
- **Booking time (booking_hour):** higher risk in **early-morning** hours (e.g., 1–2 AM).  
- **Seasonality:** higher cancellation rates around **Oct–Nov**.  
- **Trip distance:** **shorter trips** cancel more frequently (lower mean distance for cancelled rides).

---

## Modeling Approach
1. **Partition:** 70% train / 15% validation / 15% test.  
2. **Imbalance handling:** Oversampling the minority class (cancellations) on the training set.  
3. **Models:**  
   - **Decision Tree** (interpretable rules)  
   - **Logistic Regression** (probability outputs, explainable coefficients)  
   - **Random Forest** (nonlinear capacity, feature importance)  
4. **Metrics:** Accuracy, **Sensitivity (Recall)**, Specificity, Precision, Balanced Accuracy, Confusion Matrix.

### Validation Summary (headline)
- **Decision Tree:** Accuracy ~71.9%, **Recall ~77.1%**, lower precision.  
- **Logistic Regression:** Accuracy ~75.2%, **Recall ~78.1%**, best **balanced accuracy**.  
- **Random Forest:** Accuracy ~85.1%, high specificity/precision but **lower recall (~65.6%)** → misses more real cancels.

### Final Choice & Thresholding
- **Chosen:** **Logistic Regression** (best trade-off for *catching* cancellations).  
- Tuned decision threshold to **0.40** (instead of 0.50) to increase recall:  
  - **Sensitivity ~80.41%**, **Specificity ~66.75%**, **Accuracy ~67.88%**, **NPV ~97.45%** on test.  
  - Rationale: **missing a real cancellation costs more** than a false alert.

---

## Significant Predictors (Logistic Regression)
- **online_booking ( + )**, **mobile_site_booking ( + )**  
- Many **from_hour** and **booking_hour** indicators (time-of-day effects)  
- **booking_month** indicators (seasonality)  
- **distance_travelled ( − )** (shorter trips more likely to cancel)




