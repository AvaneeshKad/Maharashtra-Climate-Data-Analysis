# Maharashtra Climate Data Analysis

## Dataset Overview
The dataset contains monthly weather observations for 39 major cities in Maharashtra. 

**Key Features:**
* **Temporal Range:** January 2020 – April 2025 (Monthly Frequency)
* **Core Metrics:** * `tavg`, `tmin`, `tmax`: Average, minimum, and maximum temperatures (°C).
    * `prcp`: Monthly precipitation (mm).
    * `pres`: Sea-level atmospheric pressure (hPa).
    * `wspd`: Wind speed (km/h) — *Identified as sparse.*
    * `tsun`: Sunshine duration (minutes) — *Identified as sparse.*

## Analytical Methodologies

### 1. Seasonal Decomposition (Time-Series Analysis)
To isolate the true climate signal from predictable yearly weather cycles, the `Additive Seasonal Decomposition` method was used on the state-wide average temperature.
* **Trend Component:** Extracted the underlying baseline temperature, revealing a significant non-linear trajectory with a notable peak in late 2023.
* **Seasonal Component:** Captured the precise 12-month wave pattern characteristic of the Indian monsoon and summer cycles.
* **Residuals:** Isolated the "noise" or unpredictable fluctuations, providing a foundation for anomaly detection.

### 2. Climate Anomaly Detection
Instead of viewing raw temperatures, this method calculates the deviation from the historical norm for each specific city and month.
* **Logic:** $\\text{Anomaly} = T_{actual} - T_{historical\_mean\_for\_month}$
* **Insight:** This identified "Black Swan" events, such as uncharacteristic heatwaves in early 2024 and the cooler La Niña influenced periods of 2021.

### 3. Geographical K-Means Clustering
Unsupervised machine learning was used to group the 39 cities into distinct "Climate Zones" based on their multi-dimensional data fingerprints (Temperature, Pressure, and Rainfall).
* **Feature Engineering:** Data was aggregated into city-level profiles and processed through a `StandardScaler`.
* **Clustering Result:** Successfully reconstructed the geographical profile of Maharashtra, distinguishing between the humid Konkan coast, the high-altitude Western Ghats, and the arid Vidarbha/Marathwada plateaus without using GPS coordinates.

## Challenges & Technical Fixes

### Challenge: Data Sparsity (NaN Management)
The raw dataset contained significant missing values, particularly in `wspd` (92% missing) and `tsun` (60% missing).
* **Fix:** High-sparsity columns were dropped to preserve the integrity of the row count. For critical features like `tavg` and `pres`, **Linear Interpolation** was used within city groups to maintain time-series continuity, while `prcp` NaNs were filled with 0 (assumed no rainfall).

### Challenge: Multi-Observation Stitching (The "Barcode" Error)
Initial multi-city time-series plots resulted in vertical "zig-zag" patterns because multiple observations existed for the same timestamp.
* **Fix:** Implemented **Cluster Aggregation**. The data was grouped by both `cluster` and `date` to calculate the mean temperature per zone per month, resulting in clean, interpretable trend lines.

### Challenge: Dimensionality Scaling
K-Means clustering was initially biased by the `pres` (Pressure) variable due to its high magnitude (~1000) compared to temperature (~30).
* **Fix:** Applied **StandardScaler** to ensure every feature contributed equally to the Euclidean distance calculation in the clustering algorithm.

## Technical Stack
* **Language:** Python
* **Data Manipulation:** Pandas, NumPy
* **Statistical Modeling:** Statsmodels (Seasonal Decomposition)
* **Machine Learning:** Scikit-Learn (K-Means, StandardScaler)
* **Visualization:** Matplotlib, Seaborn
