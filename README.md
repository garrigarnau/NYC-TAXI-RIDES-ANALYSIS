# NYC Taxi Big Data Analysis with Spark SQL

## Project Overview

This project performs a comprehensive analysis of New York City taxi ride data to identify high-value trip patterns, traffic flow, and anomalous driver behavior. Using **PySpark**, we processed raw GPS telemetry data to uncover profitability insights and visualize complex routing networks.

The core goal was to move beyond simple aggregations and perform **spatial discretization** and **origin-destination flow analysis** on a large-scale dataset.

See current kepler session here: [Link to kepler play map](https://drive.google.com/file/d/1_dHrl5YtJRhlGhT0JQwBgZ9l6Es70Wpp/view?usp=sharing)
-----

## 🛠 Tech Stack & Tools

  * **Core Engine:** Apache Spark (PySpark)
  * **Data Processing:** Spark SQL, DataFrames, Window Functions
  * **Visualization:** Kepler.gl (Geospatial), Matplotlib & Seaborn (Statistical)
  * **Environment:** Google Colab / Jupyter Notebooks
  * **Format:** Parquet/CSV processing

-----

## 📊 Key Analyses & Methodology

### 1\. Spatial Discretization (Grid-Based Aggregation)

**The Challenge:** Raw GPS data consists of continuous floating-point coordinates, making it impossible to group by exact location.
**The Solution:** We implemented a custom mathematical grid system ($300 \times 300$ resolution) over the NYC bounding box.

  * **Technique:** Used `floor()` operations on longitude/latitude deltas to map continuous coordinates to discrete integer **Grid IDs**.
  * **Benefit:** Allowed us to aggregate millions of unique points into manageable 500m $\times$ 500m "zones" for density and fare analysis.

### 2\. High-Value Route Identification

Instead of looking at raw total fares (which biases results toward long airport trips), we calculated **Fare Per Mile**.

  * **Logic:** Identified specific grid cells where the revenue-to-distance ratio is highest.
  * **Flow Analysis:** We used **Spark Window Functions** (`partitionBy` destination, `orderBy` frequency) to reverse-engineer the most frequent "Origin" points for the top 10 most expensive "Destinations."

### 3\. Anomaly Detection & Driver Profiling

We analyzed driver behavior to flag potential fraud or outlier behavior.

  * **Technique:** Calculated Z-scores for metrics like `speed`, `fare_per_mile`, and `tip_percentage`.
  * **Visualization:** Used Parallel Coordinates to cluster drivers based on multidimensional anomaly scores.

-----

## 🗺️ Visualizations

### 3D Geospatial Heatmaps (Kepler.gl)
<img width="703" height="580" alt="Screenshot 2025-12-06 at 15 13 49" src="https://github.com/user-attachments/assets/4834fb40-5703-436a-8e8f-465ff3df1261" />
<img width="788" height="614" alt="Screenshot 2025-12-06 at 15 23 07" src="https://github.com/user-attachments/assets/7f39ee11-15b3-4716-82f0-0ec8b0ea9577" />


### Origin-Destination Arc Flows
<img width="843" height="626" alt="Screenshot 2025-12-06 at 16 00 08" src="https://github.com/user-attachments/assets/28096d3a-e248-4679-89d3-270a2f85e30b" />

<img width="1016" height="620" alt="Screenshot 2025-12-06 at 16 07 25" src="https://github.com/user-attachments/assets/b7ba7d51-fb9b-45b6-a069-5685a9d5e7c6" />

### Driver Anomaly Profiles


-----

## 🚀 Technical Challenges & Solutions

| Challenge | Solution |
| :--- | :--- |
| **Continuous GPS Data** | Implemented a custom coordinate-to-grid mapping algorithm using Spark transformations. |
| **Self-Loops** | Filtered out intra-zonal trips (Origin Grid == Destination Grid) to ensure flow visualizations represented actual movement. |
| **Ambiguous Joins** | Resolved column name collisions (e.g., `avg_time_mins`) during self-joins by strictly aliasing and selecting columns before joining. |
| **Visualization Output** | Used `coalesce(1)` to merge Spark partitions into single CSV files formatted specifically for Kepler.gl ingestion. |

-----

## 💻 How to Run

1.  **Prerequisites:** Ensure you have Python installed with `pyspark`, `pandas`, `matplotlib`, and `seaborn`.
2.  **Data:** The script automatically downloads the 1% sample dataset (`taxi_rides_1pc.csv.gz`).
3.  **Execution:**
    ```bash
    python presentation_report_sys1.py
    ```
4.  **Visualization:**
      * The script generates CSV files (e.g., `kepler_grid_heatmap.csv`, `kepler_flow_arcs.csv`).
      * Upload these files to [Kepler.gl](https://kepler.gl/demo) to interact with the geospatial data.
        
