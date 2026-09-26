# Multi-Site Solar PV Generation Forecasting

## Overview

This project develops one-hour-ahead solar PV generation forecasts across 42 PV sites using the UNISOLAR dataset.

Three forecasting approaches are evaluated:

- Persistence baseline
- XGBoost
- Long Short-Term Memory (LSTM) neural network

The project focuses on building a complete forecasting pipeline, including data preprocessing, exploratory analysis, feature engineering, chronological model validation, hyperparameter tuning, and site-level evaluation.

## Dataset

The project uses the UNISOLAR dataset, which contains photovoltaic generation, solar irradiance, weather, and site information from multiple university campuses.

The dataset contains more than 2.5 million 15-minute observations across 42 PV sites.

Dataset source:
[UNISOLAR — CDAC Lab](https://github.com/CDAC-lab/UNISOLAR/tree/main) [1]


## Forecasting Objective

The objective is to predict PV generation exactly one hour ahead for each site.

Targets are constructed using timestamp matching rather than simple row shifting so that forecasts are only created when an observation exists exactly one hour later.

## Data Preprocessing

The preprocessing pipeline includes:

- Timestamp and timezone alignment
- Integration of generation, irradiance, and weather datasets
- Treatment of missing PV generation
- Irradiance resampling to 15-minute resolution
- Handling of short gaps in meteorological variables
- Physical plausibility checks
- Site-level normalization for exploratory analysis

## Exploratory Data Analysis

The analysis examines:

- Daily PV generation patterns
- Seasonal variation
- Differences in generation scale across sites
- Relationships between PV generation and irradiance/weather
- Data continuity and missing observations

GHI showed the strongest relationship with PV generation.

## Models

### Persistence Baseline

The persistence forecast assumes that PV generation one hour ahead is equal to the current generation.

### XGBoost

The XGBoost model uses:

- Current PV generation
- Solar irradiance
- Weather variables
- 15-, 30-, and 60-minute lag features
- Cyclical time features
- Site identity

Feature selection and hyperparameter tuning were performed using the validation period only.

### LSTM

The LSTM learns temporal patterns directly from sequences of recent observations.

The final input features include:

- SolarGeneration
- GHI
- Hour of day
- Day of year

A learned site embedding allows one global model to represent differences between the 42 PV installations.

## Train / Validation / Test Split

A chronological split was used to prevent future information from leaking into model development.

- Training: before October 2021
- Validation: October–December 2021
- Test: January–April 2022

The test period was held out until final evaluation.

## Results

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| Persistence | 1.4639 | 3.8214 | 0.8303 |
| XGBoost | **0.6185** | 1.9940 | 0.9538 |
| LSTM | 0.6484 | **1.8729** | **0.9584** |

Both machine-learning models substantially outperformed persistence.

XGBoost achieved the lowest average absolute error, while LSTM achieved the lowest RMSE and highest R².

Compared with persistence:

- XGBoost reduced MAE by approximately 57.8%
- LSTM reduced RMSE by approximately 51.0%

## Key Findings

- Recent PV generation and solar irradiance contain most of the predictive information for one-hour-ahead forecasting.
- Additional weather variables provided little validation improvement for the LSTM.
- XGBoost and LSTM produced similar overall forecasting accuracy but showed different strengths across evaluation metrics.
- A global model can successfully forecast generation across sites with substantially different generation scales.

## Limitations

The models use contemporaneous weather observations rather than numerical weather forecasts. Consequently, sudden changes in irradiance occurring within the one-hour forecast horizon may be difficult to anticipate.

Rated PV capacities were also unavailable, so observed site maximum generation was used where normalized site-level evaluation was required.

## Tools

Python, Pandas, NumPy, Matplotlib, Scikit-learn, XGBoost, TensorFlow/Keras

## References

[1] S. Wimalaratne, D. Haputhanthri, S. Kahawala, G. Gamage, D. Alahakoon, and A. Jennings, "UNISOLAR: An Open Dataset of Photovoltaic Solar Energy Generation in a Large Multi-Campus University Setting," 2022 15th International Conference on Human System Interaction (HSI), 2022, pp. 1–5, doi: 10.1109/HSI55341.2022.9869474.
