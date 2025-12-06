#  Food Delivery Time Prediction and Optimization

## Project Overview

The objective of this project was to build a predictive model to classify food deliveries as "Fast" or "Delayed" (relative to the median delivery time of $72.78$ minutes) and use clustering techniques to derive actionable insights for optimizing delivery operations.

The analysis involved three key phases: **Data Preprocessing and Feature Engineering**, **Clustering Analysis**, and **Predictive Modeling (Neural Networks / Logistic Regression)**.

***

## Phase 1: Data Preprocessing and Feature Engineering

The raw data was transformed to create valuable input features:

1.  **Geospatial Feature:** Calculated the accurate $\mathbf{Delivery\ Distance\ (km)}$ between the restaurant and customer locations using the **Haversine formula**.
2.  **Time Feature:** Created the $\mathbf{Is\_Rush\_Hour}$ binary feature, assuming 'Evening' times corresponded to peak congestion.
3.  **Target Creation:** The target variable ($\text{Delivery\_Status}$) was created by classifying orders above the median delivery time as **Delayed (1)** and those below as **Fast (0)**, resulting in a perfectly balanced dataset.
4.  **Scaling and Encoding:** All numerical features were standardized ($\text{StandardScaler}$), and categorical features ($\text{Weather}$, $\text{Traffic}$, $\text{Time}$) were One-Hot Encoded.

***

## Phase 2: Clustering and Insights

Clustering was performed on the preprocessed training data to find natural groupings of orders. Both K-Means and Hierarchical Clustering confirmed $\mathbf{k=4}$ as the optimal number of segments.

### K-Means Cluster Analysis ($\mathbf{k=4}$)

The analysis of the cluster centroids revealed clear drivers for delivery speed:

| Cluster | Description | Avg. Distance (Scaled) | Avg. Rainy Weather Prop. (Scaled) | Delayed Order Proportion ($\%$) |
| :---: | :---: | :---: | :---: | :---: |
| **Cluster 1** | **Fastest (Low Congestion)** | $\mathbf{+0.932}$ (High) | $\mathbf{0.222}$ (Low Traffic/Rain) | $\mathbf{37.04\%}$ (Lowest Delay) |
| **Cluster 2** | **Slowest (Weather-Dependent)** | $-0.074$ (Average) | $\mathbf{+0.318}$ (Highest Rain/Traffic) | $\mathbf{61.36\%}$ (Highest Delay) |
| **Cluster 0** | **Fast (Short Distance)** | $\mathbf{-0.753}$ (Lowest) | $+0.324$ (High Traffic) | $41.18\%$ |

### Clustering Insights

* **Weather Impact:** **Cluster 2** clearly isolates orders where bad **Weather Conditions** (Rainy/Snowy) combined with high traffic are the **dominant factor** leading to delays ($\text{61\%}$ delay rate).
* **Distance vs. Congestion Trade-off:** **Cluster 1** demonstrates that even **long-distance deliveries** can be fast if they successfully avoid peak congestion and bad weather, highlighting the importance of route quality over sheer distance.

***

## Phase 3: Predictive Modeling and Comparison

The final predictive task used a traditional machine learning model (Logistic Regression) as a comparison benchmark, since the initial Neural Network execution struggled.

### Model Comparison Summary

| Model | Test Accuracy | F1-Score | Conclusion |
| :--- | :--- | :--- | :--- |
| **Logistic Regression** (Traditional) | $\mathbf{53.33\%}$ | $\mathbf{0.5333}$ | Best performing model, provides a strong baseline. |
| **Initial Neural Network** (Untuned) | $43.33\%$ | $0.4333$ | Underperformed, indicating a need for extensive hyperparameter tuning. |

### Neural Network vs. Traditional Model

The Logistic Regression model, despite its simplicity, achieved higher accuracy than the untuned Neural Network. For a real-world application, this highlights the principle that **model complexity must be justified by data size and domain fit**. Given the small dataset, Logistic Regression successfully captured the linear relationship between the engineered features and delivery status.

***

## Phase 4: Actionable Insights and Recommendations

Based on the combined clustering and prediction results, here are key recommendations for optimization:

### 1. Route and Resource Optimization

| Recommendation | Insight Justification |
| :--- | :--- |
| **Dynamic Routing During Bad Weather** | **Cluster 2** ($\text{61\%}$ delay) is defined by high $\text{Rainy/Snowy}$ conditions. Prioritize immediate, localized warnings to riders during these conditions and use traffic-flow APIs to find less-congested alternates. |
| **Delivery Zone Restructuring** | **Cluster 1** ($\text{37\%}$ delay) proves long distance is manageable when traffic is low. Re-evaluate delivery zones based on **time-of-day travel time** instead of Euclidean distance to encourage low-congestion routes. |
| **Vehicle Type Allocation** | Analyze $\text{Vehicle\_Type}$ distribution within Cluster 2. If $\text{Bicycles}$ are overrepresented in high-traffic/rainy delays, restrict their zone access during these times, pushing orders to $\text{Cars/Bikes}$. |

### 2. Incentive and Rating Management

| Recommendation | Insight Justification |
| :--- | :--- |
| **Incentivize Low-Traffic Routes** | Implement surge pricing or bonus pay for delivery drivers who successfully complete orders during peak hours while demonstrating significantly faster-than-average travel times (i.e., operating like Cluster 1). |
| **Set Realistic ETA (Predicted Delays)** | Use the predictive model's output (even the $\text{53\%}$ accuracy) to adjust the Estimated Time of Arrival (ETA) for the customer. If the model predicts a delay, add a buffer to manage customer expectations proactively. |
