
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/annamma/uav-energy-consumption-prediction/blob/main/UAV_Energy_Consumption_Prediction.ipynb)

# UAV Energy Consumption Prediction using Linear Regression and Decision Tree

Micro project (CO5, M.Tech AI) by Annamma Thomas.

## What this project does

Delivery drones run on batteries, so it helps to know how much energy a flight will use before it takes off. This project predicts a drone flight's energy use from its planned **speed, payload and altitude**:

- **Linear Regression** predicts the energy in watt-hours (Wh).
- **Decision Tree Classifier** predicts whether the flight will use **Low, Medium or High** energy.
- Both models are evaluated and compared on the same held-out test flights.

## Dataset

CMU in-flight positional and energy-use data set of a DJI Matrice 100 quadcopter for small package delivery (Rodrigues et al., *Scientific Data*, 2021, doi: 10.1038/s41597-021-00930-x).

`flights.csv` has 257,896 sensor readings (28 columns) from 209 flights. It is about 100 MB, so it is **not included in this repository**. Download it from the dataset page and put it where the first notebook cell expects it.

## Method

1. Load and check the data (no missing values, no duplicate timestamps).
2. Compute each flight's energy: integrate power (voltage × current) over time and convert to Wh. Positive battery current is discharge, confirmed because it gives realistic positive energies of about 13-38 Wh per flight.
3. Keep the 196 cruise flights (routes R1-R7); drop ground and hover tests. Two flights with text altitude profiles are dropped, leaving 194.
4. Split the flights 80/20 (155 train, 39 test).
5. Fit Linear Regression (standardised inputs) and evaluate with MAE, RMSE and R².
6. Make Low/Medium/High classes from the 33rd and 66th percentiles of the **training** energies, tune a Decision Tree with 5-fold cross-validation, and evaluate with accuracy, precision, recall, macro F1 and a confusion matrix.
7. Compare both models on the same test flights.

## Results (test set, 39 flights)

| Model | Result |
|---|---|
| Linear Regression | MAE 1.600 Wh, RMSE 1.900 Wh, R² 0.809 |
| Decision Tree Classifier | Accuracy 0.692, macro F1 0.710 |
| Linear Regression (converted to classes) | Accuracy 0.718, macro F1 0.716 |

The two models perform about the same on the classification task (a difference of one flight out of 39). Energy rises with payload and altitude and falls with speed, because faster flights finish sooner.

## Limitations

- 182 of the 196 cruise flights are on route R1, so results mainly reflect that route.
- Only 39 test flights, so the scores are noisy.
- Mean wind speed is measured during the flight, so it is not known before takeoff (its effect in the model is about zero).
- Flight duration is excluded because it is not known in advance.

## Files

- `UAV_Energy_Consumption_Prediction.ipynb` — the full notebook with a plain-language explanation above each step and the saved outputs.
- `uav_test_results.csv`, `uav_classification_comparison.csv` — test-set predictions and comparison (saved by the last notebook cell; add them after downloading from Colab).

## How to run

Open the notebook in Google Colab, upload `flights.csv`, and run the cells from top to bottom. It needs pandas, numpy, matplotlib and scikit-learn (all preinstalled in Colab).
