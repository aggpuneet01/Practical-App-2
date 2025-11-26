# Project Summary: What Drives the Price of a Car?
This project aimed to understand the key drivers of used car prices using a dataset of 426,000 car listings, ultimately providing recommendations to a used car dealership on consumer valuation.

1. Business & Data Understanding
Business Problem: Identify factors influencing used car prices to help a dealership fine-tune its inventory.
Data Problem: Build a predictive regression model to estimate car prices based on various features.
Initial Data Exploration: Loaded the dataset, inspected data types (df.info()), and examined descriptive statistics (df.describe()).

2. Data Preparation & Cleaning
Outlier Handling: Initial filtering of car prices between $500 and $100,000 was performed, reducing the dataset size. Note: Further price filtering was implicitly handled by the logarithmic transformation.
Missing Value Imputation:
Columns with high missingness (size, VIN) were dropped.
Numerical columns (year, odometer) were imputed with their medians.
Categorical columns were imputed with their modes or 'Unknown' (for condition, cylinders).
Duplicate Check: Confirmed no duplicate rows.
Target Variable Transformation: Before removing outliers (<500 and >1000), price column was highly skewed. But after removing those outliers, histogram showed much better results. Price column then was transformed using np.log1p(price) to create price_log for modeling, aiming for a more normal distribution.

3. Feature Engineering Stages

Basic Engineered Features

car_age: Derived as current_year - year, handling non-negative values.
mileage_per_year: Calculated as odometer / car_age.
is_luxury: Binary (0/1) indicator based on manufacturer.
age_odometer_interaction: Product of car_age and odometer.

4. Data Splitting and Encoding
The dataset was split into training and testing sets (80/20 ratio).
The id column was dropped from feature sets.
Categorical features were handled:
Low-cardinality features were One-Hot Encoded.
High-cardinality features (region, model, state) were Target Encoded.

5. Modeling and Evaluation
Two types of linear models (Linear Regression and Lasso Regression) were trained and evaluated at different stages of feature engineering, using price_log as the target variable. Their performance was also assessed on the original price scale after inverse transformation.

A. Models with car_age, mileage_per_year, is_luxury, age_odometer_interaction, (and original features):

![results.jpeg] (Practical-App-2/results.jpeg)

Lasso Coefficients (Key Price Drivers) for model without engineered features:
For this model, the features with non-zero coefficients indicating their importance were predominantly model, year, and odometer. (Specifically: model (positive), year (positive), odometer (negative)).

Lasso Coefficients (Key Price Drivers) for model with engineered features:
For this model, the features with non-zero coefficients were manufacturer_toyota (positive), type_sedan (positive), and manufacture_volkswagen (negative). This highlights the importance of specific manufacturers and car types, alongside usage.

6. Visualizations
Distribution of Price: Initial histogram showed a heavily right-skewed distribution, confirming some data cleanup. After removing outliers and log1p transformation, got much better distribution.
Actual vs. Predicted Plots (Log-Transformed): These plots showed a positive correlation, indicating that predictions generally followed the actual values, though with considerable scatter. The plots with all engineered features were generated with legends and distinct colors.
Actual vs. Predicted Plots (Original Scale): After inverse transformation, these plots revealed significant dispersion, with predictions far from the ideal y=x line, especially for higher prices. This visually confirmed the very low R-squared and high errors on the original price scale.
Residual Plots: For log-transformed predictions, these plots generally showed no strong patterns, though heteroscedasticity was observed, indicating varying error magnitudes across the prediction range.
7. Key Findings & Recommendations
Linear Models are Insufficient for Direct Price Prediction: Despite thorough data cleaning, transformation, and feature engineering, simple linear regression models struggle to accurately predict used car prices on the original dollar scale. While they show some predictive power on the log-transformed scale (R-squared up to 0.57 in initial models, and 0.22 after comprehensive FE), the error magnification during inverse transformation leads to poor performancefor real-world price estimates.
Lasso Highlights Key Price Drivers: The Lasso Regression models, through their ability to shrink less important coefficients to zero, identified specific features influencing price. In the initial model, model, year, and odometer were prominent. After adding more engineered features, manufacturer_toyota, type_sedan, and manufacturer_volkswagen emerged as key indicators.

Shift to Advanced, Non-Linear Models: Given the limitations of linear models, it is crucial to explore more sophisticated machine learning algorithms capable of handling non-linear relationships and complex interactions. Tree-based models (e.g., Random Forest, Gradient Boosting Machines like XGBoost or LightGBM) are highly recommended. These models can better leverage the binned and interaction features and potentially provide much more accurate price predictions on the original scale.
Refine Feature Engineering with Advanced Models: Once a more powerful model is chosen, re-evaluate feature engineering strategies to ensure they are optimally designed for the chosen model type. The current binned features might be more effective with tree-based models.
In conclusion, while we've identified some key price drivers through Lasso Regression, the current linear models are not robust enough for precise price prediction. Moving to advanced models and leveraging their ability to interpret complex features will be essential to provide actionable, data-driven insights for inventory management.
