Air Quality Prediction for Siltara (Raipur) - PM2.5 Forecasting
Project Objective

This project aims to develop predictive models for PM2.5 concentrations in the Siltara industrial area of Raipur, using historical air quality data from 2022 to 2025. The focus is on robust data preprocessing, feature engineering to capture 'industrial fingerprints,' and time-series-appropriate model evaluation.
Data Sources

    CECB Raipur Siltara 2022, 2023 2024 2025(1).xlsx: An Excel file containing monthly air quality data for various pollutants and meteorological parameters.

Methodology & Key Findings
Phase 1: Advanced Data Pre-processing

    Data Consolidation & Unit Cleaning:
        Loaded raw data from the Excel file, handling non-standard headers and unit rows to create a clean, unified DataFrame.
        Converted 'Date & Time' to datetime objects and all pollutant columns to numeric types, coercing errors to NaN.
        Sorted the data chronologically and dropped invalid date entries.

    Advanced Imputation (MICE):
        Utilized sklearn.impute.IterativeImputer (MICE) to fill missing values in pollutant columns. This method leverages correlations between features (e.g., NO2 and NOx) for more accurate, variance-preserving imputation, which is crucial for small datasets.

    Cyclical Month Encoding:
        Transformed the 'month' component of the 'Date' column into sine (Month_Sin) and cosine (Month_Cos) features. This allows models to perceive the cyclical nature of seasonality (e.g., December and January being seasonally close) rather than as a discontinuous jump.

    Engineering Industrial Features:
        Pollutant Ratios: Created SO2_NOX_Ratio and CO_PM25_Ratio to infer industrial processes.
        Weather Interactions: Introduced Heat_Index (TEMP * HUM) to account for local meteorological effects on PM2.5 dispersion.
        1-Month Lag: Generated PM2.5_LastMonth as a strong temporal predictor, acknowledging that past values often influence current and future conditions.

Phase 2: Model Development & Evaluation

    Time-Series Split:
        Employed sklearn.model_selection.TimeSeriesSplit (with n_splits=5) for model validation. This ensures that models are always trained on past data and tested on future data, preventing data leakage and providing a realistic performance assessment for time-series forecasting.

    Model Selection & Initial Evaluation (SVR & XGBoost):
        SVR Strategy: Used SVR with an RBF kernel, suitable for finding smooth prediction lines in smaller datasets.
        XGBoost Strategy: Implemented XGBRegressor with a shallow max_depth=3 to mitigate overfitting.
        Initial cross-validation results showed overall negative R2 scores for both models, indicating challenges in generalization due to the small dataset size.

    Hyperparameter Tuning:
        GridSearchCV was used with TimeSeriesSplit to optimize hyperparameters for both SVR and XGBoost.
        Best SVR Parameters: {'C': 100, 'epsilon': 1, 'gamma': 'scale'} (Average MSE: 161.70)
        Best XGBoost Parameters: {'colsample_bytree': 1.0, 'learning_rate': 0.1, 'max_depth': 2, 'n_estimators': 100, 'subsample': 1.0} (Average MSE: 169.43)

    Evaluation of Best Models on Last Test Set:
        Best SVR Model: MSE: 245.1681, R2 Score: -0.7297
        Best XGBoost Model: MSE: 0.8000, R2 Score: 0.9944

    Investigation into XGBoost's High Performance:
        The exceptional R2 score for XGBoost on the last test set was primarily attributed to the strong predictive power of the PM2.5_LastMonth feature within that specific, recent data segment.

    Analysis of Feature Importance (XGBoost):
        Average feature importances across all folds identified CO, HUM, NO2, Heat_Index, and NO as the most consistently influential features.
        PM2.5_LastMonth showed relatively low average importance, suggesting its high influence was localized to certain periods (e.g., the last test set) rather than being universally dominant.

Limitations of the Models

    Extremely Limited Dataset Size (48 Monthly Data Points): This is the most significant constraint. The small volume of data severely restricts the models' ability to learn stable, generalized patterns for robust time-series forecasting.
    Poor Generalizability (Negative Average R2): The overall negative average R2 scores indicate that, on average, both models struggle to generalize beyond the training data, performing worse than simply predicting the mean. This is a direct consequence of data scarcity.
    Sensitivity to Specific Data Segments: The drastic difference in XGBoost's performance between its average R2 (negative) and its R2 on the last test set (highly positive) highlights its sensitivity to localized data characteristics. Its success on the last fold was largely due to a strong, localized correlation with the lagged PM2.5 value.
    Risk of Overfitting: Despite measures like shallow tree depths and TimeSeriesSplit, the inherent scarcity of data makes any model susceptible to memorizing training examples rather than truly generalizing.
    Limited External Variables: The models lack access to broader contextual information (e.g., specific industrial production levels, regulatory changes, more granular weather, or specific event data) that could significantly influence air quality and improve predictive accuracy.

Conclusion

While advanced techniques for preprocessing and modeling were applied, the primary limitation remains the extremely small dataset. For more reliable and consistently accurate air quality predictions, a significantly larger and potentially more granular dataset would be essential to enable models to capture a broader range of patterns and generalize more effectively.
