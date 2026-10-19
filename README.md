
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/annnamma/uav-energy-consumption-prediction/blob/main/UAV_Energy_Consumption_Prediction.ipynb)

# UAV Energy Consumption Prediction using Linear Regression and Decision Tree

Micro project (CO5, M.Tech AI) by Annamma Thomas.

## What this project does

Delivery drones run on batteries, so it helps to know how much energy a flight will use. This project predicts the energy used in a drone flight from its **speed, payload and altitude** (plus mean wind speed, which is measured during the flight):

- **Linear Regression** predicts the energy in watt-hours (Wh).
- **Decision Tree Classifier** predicts whether the flight will use **Low, Medium or High** energy.
- Both models are evaluated and compared on the same held-out test flights.

## Dataset

Rodrigues, T. A., Patrikar, J., Choudhry, A., et al. "In-flight positional and energy use data set of a DJI Matrice 100 quadcopter for small package delivery." *Scientific Data* 8, 155 (2021). 

Dataset download: https://doi.org/10.1184/R1/12683453 (Carnegie Mellon University, "Data collected with package delivery quadcopter drone").

`flights.csv` has 257,896 sensor readings (28 columns) from 209 flights. It is about 100 MB, so it is **not included in this repository**. Download it from the link above and set `DATA_PATH` in the first notebook cell to where you saved it.

## Method

1. Load and check the data (no missing values, no duplicate timestamps).
2. Compute each flight's energy by integrating power (voltage × current) over time and converting to Wh, as described in the dataset paper. **Sign convention:** positive battery current was treated as discharge. The paper's variable table does not state a sign convention, so this is an assumption supported by the data (it gives positive energies of about 13-38 Wh for cruise flights, and the three A1 ground tests give values near zero) and not a documented fact.
3. Keep the 196 cruise flights (routes R1-R7); drop ground and hover tests. Two flights with text altitude profiles are dropped, leaving 194.
4. Split the flights 80/20 (155 train, 39 test) with a fixed random seed.
5. Fit Linear Regression (standardised inputs) and evaluate with MAE, RMSE and R².
6. Make Low/Medium/High classes from the 33rd and 66th percentiles of the **training** energies, tune a Decision Tree with 5-fold cross-validation, and evaluate with accuracy, precision, recall, macro F1 and a confusion matrix.
7. Compare both models on the same test flights.

## Results

These come from **one 80/20 split** (39 test flights). They show how the models did on this particular split, not how they will perform on other flights.

| Model | Result |
|---|---|
| Linear Regression | MAE 1.600 Wh, RMSE 1.900 Wh, R² 0.809 |
| Decision Tree Classifier | Accuracy 0.692, macro F1 0.710 |
| Linear Regression (converted to classes) | Accuracy 0.718, macro F1 0.716 |

On the classification task the two models scored almost the same; the accuracy difference is one flight out of 39, which is too small to say either is better.

Observed relationships (cruise flights, average energy): energy increases with payload and with altitude, and is lower for higher speeds (27.0 Wh at 4 m/s down to 18.5 Wh at 12 m/s). Flight duration was not used as a model input, and this project does not test why energy falls with speed.

## Limitations

- 182 of the 196 cruise flights are on route R1, so results mainly reflect that route.
- Only 39 test flights from a single split, so the scores are noisy.
- Mean wind speed is measured during the flight, so it is not known before takeoff (its effect in the model is about zero).
- Flight duration is excluded because it is not known in advance.
- The current sign convention is an assumption (see Method, step 2).

## Files

- `UAV_Energy_Consumption_Prediction.ipynb`: the notebook with saved outputs.
- `uav_test_results.csv`, `uav_classification_comparison.csv`: test-set predictions and comparison (saved by the last notebook cell; add them after downloading from Colab).

## How to run

Open the notebook in Google Colab, upload `flights.csv`, set `DATA_PATH` in the first cell, and run the cells from top to bottom. It needs pandas, numpy, matplotlib and scikit-learn (all preinstalled in Colab).
