# Agricultural Climate Time-Series Forecasting for Jabalpur

This project explores monthly climate time-series forecasting for Jabalpur, Madhya Pradesh, using TerraClimate data. It performs seasonal and stationarity analysis, fits a seasonal SARIMA model, and compares its workflow with LSTM and GRU neural networks.

> **Scope:** The current notebook forecasts monthly mean temperature (`tmean`). It does not predict famine, crop yield, food insecurity, or agricultural drought events. Treat its forecasts as an educational climate-analysis exercise, not as an operational agricultural early-warning system.

## Project goals

- Inspect long-run monthly climate patterns for Jabalpur.
- Decompose the selected series into trend, seasonal, and residual components.
- Test stationarity with the Augmented Dickey-Fuller test.
- Fit a SARIMA model and generate a five-year forecast with confidence intervals.
- Train LSTM and GRU sequence models and compare their predictions on a chronological holdout set.
- Save forecast tables, evaluation metrics, and plots for review.

## Data

The notebook was run with a monthly TerraClimate CSV named `TerraClimate_Jabalpur_1958_2023_monthly.csv`, covering January 1958 through December 2023. The notebook prompts the user to upload a CSV in Google Colab; the dataset itself is not included here.

The input needs either:

- a `date` column and the selected target column (default: `tmean`), or
- `year`, `month`, and the selected target column.

The example dataset contains `date`, `pdsi`, `pet`, `ppt`, `soil`, `tmmn`, `tmmx`, and `tmean`. The current modeling code selects only `tmean`; other columns are not currently used as model features. Confirm the units and any scale factors in the source dataset documentation before interpreting values.

## Methods

### SARIMA

- Seasonal decomposition uses an additive model with a 12-month period.
- An Augmented Dickey-Fuller test is reported for the selected series.
- The notebook uses SARIMA `(1, 1, 1) × (1, 1, 1, 12)`.
- If the dataset has more than 120 monthly observations, it uses the earliest 80% for training and the latest 20% for testing.
- It saves a five-year (60-month) forecast and its confidence interval.

### LSTM and GRU

- The notebook scales the selected series and builds sequences using a 36-month lookback.
- It trains LSTM and GRU neural networks with early stopping.
- It reports MSE, RMSE, MAE, MAPE, R², and mean bias error (MBE), and saves holdout predictions.

## Requirements

The notebook was written for Google Colab and installs packages in notebook cells. Typical dependencies include:

- Python 3
- pandas, NumPy, Matplotlib, scikit-learn
- statsmodels
- TensorFlow / Keras
- Jupyter Notebook or Google Colab

Google Colab is the easiest way to run the notebook as written because it uses `google.colab.files.upload()` to select the CSV. To run locally, install the dependencies and replace the Colab upload cell with a local CSV path.

## Run the project

1. Open `TSA_project.ipynb` in Google Colab or Jupyter.
2. Ensure the climate CSV has the required date fields and target column.
3. Run the first code cell and upload the CSV when prompted.
4. Run the next cells in order. The SARIMA analysis must run first because the LSTM/GRU cells reuse variables created there.
5. Review the printed metrics and plots, then inspect the generated output files.

The selected target is set in the notebook as:

```python
target_col = "tmean"
```

Changing this to another available numeric column, such as `ppt` or `pdsi`, will change the forecast target. Confirm units and missing-value handling before interpreting results.

## Generated outputs

The notebook writes SARIMA results to `tsa_climate_outputs/`, including files such as:

- `decompose_<target>.png` — seasonal decomposition plot
- `diagnostics_<target>.png` — SARIMA residual diagnostics
- `metrics_<target>.csv` — SARIMA holdout metrics
- `test_forecast_<target>.png` — SARIMA test-period predictions
- `forecast_<target>.csv` — future forecast and confidence bounds
- `forecast_plot_<target>.png` — historical series and future forecast

The neural-network cells save prediction and metric CSV files under the same output directory. In the notebook, those CSV paths are constructed under `OUT` (`tsa_climate_outputs`).

## Interpretation and limitations

- A temperature forecast alone cannot establish drought conditions or predict famine.
- Agricultural risk assessment should use relevant variables such as precipitation, soil moisture, drought indicators, crop calendars, and ideally crop-yield or impact records.
- The notebook scales the complete series before the LSTM/GRU split. For a rigorous evaluation, fit the scaler on training data only to avoid information leakage.
- MAPE is unreliable when actual values are zero or near zero and is difficult to interpret for Celsius temperatures, which may be negative. Prefer MAE/RMSE in meaningful units, and consider additional scale-aware metrics.
- The report shows temperature values with negative numbers. Check TerraClimate units and scale factors before interpreting or presenting forecasts.
- Forecast accuracy does not establish that a model is suitable for farm-level decisions. Validate with domain experts and independent historical periods before operational use.

## Next steps toward an agricultural drought early-warning project

1. Verify the Jabalpur dataset's provenance, units, and missing-data treatment.
2. Model precipitation (`ppt`), soil moisture (`soil`), or a drought index such as `pdsi`, not just temperature.
3. Define an explicit drought-risk outcome and threshold with agricultural domain guidance.
4. Add crop calendar, crop yield, and impact data if available.
5. Compare against simple seasonal baselines and validate with rolling-origin time-series evaluation.
6. Prevent preprocessing leakage by fitting scalers and imputers on training windows only.
7. Report uncertainty and limitations; avoid describing the project as famine prediction unless it is trained and validated against famine or food-security outcomes.

## References

- TerraClimate dataset documentation, University of Idaho.
- Hyndman, R. J., & Athanasopoulos, G. (2021). *Forecasting: Principles and Practice*.
- Hochreiter, S., & Schmidhuber, J. (1997). “Long Short-Term Memory.”
- Cho, K. et al. (2014). “Learning Phrase Representations using RNN Encoder–Decoder for Statistical Machine Translation.”
