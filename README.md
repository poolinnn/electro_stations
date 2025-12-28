# Dataset Preprocessing for SARIMAX Model

## Introduction

This report describes the data preprocessing stage for a dataset containing hourly time series related to energy and weather. The goal of this stage is to prepare the "raw" data, obtained from open sources, for subsequent analysis and training of a SARIMAX model for electricity generation forecasting.

## Data Sources

Two main datasets were used for the research:

1.  **Energy Data:** Contains hourly information on consumption, generation (by type: solar, wind), and installed capacity of power plants, aggregated by country for the European Union and adjacent territories.
2.  **Weather Data:** Includes hourly measurements of temperature and solar irradiance, aggregated by European regions, based on NASA MERRA-2 reanalysis.

## Preprocessing Steps

### 1. Data Loading

The data was loaded from CSV files into a Jupyter Notebook environment using `pandas` and `numpy` libraries.

```python
# Example loading
import pandas as pd
import numpy as np

# df_energy = pd.read_csv('energy_data.csv')
# df_weather = pd.read_csv('weather_data.csv')
```

### 2. Feature Selection


Columns unrelated to the task of forecasting electricity generation were removed from both datasets. Specifically, consumption data, market prices, and solar irradiance (as a potentially redundant feature if the target is generation) were dropped. Only time series for generation by type and relevant weather features (temperature) were retained.

### 3. Region Selection
To simplify the analysis, a limited set of regions was chosen: France and Austria, which were present in both datasets and had sufficiently long and complete time series.

### 4. Type Conversion and Structure Check
It was ensured that columns containing date and time had the datetime type. An initial check of table sizes, data types, and overall appearance was performed.

```python
# Example type conversion
# df['datetime'] = pd.to_datetime(df['datetime'])
```

### 5. Dataset Merging
Energy and weather data were merged into a single dataframe based on matching time stamps (date and hour).

```python
# merged_df = pd.merge(df_energy, df_weather, on='datetime', how='inner')
```


### 6. Missing Value Handling
The number of missing values in each column was checked. Their proportion was extremely low. It was decided to fill them using the forward-fill method, where the missing value is replaced by the previous known value.

```python
# Example filling missing values
# merged_df.fillna(method='ffill', inplace=True)
```


### 7. Time Series Diagnostics (Example: AT_solar_generation_actual)
A specific time series (AT_solar_generation_actual - solar generation in Austria) was selected for detailed analysis to prepare for SARIMAX modeling.

* Stationarity Check: Using the Augmented Dickey-Fuller (ADF) test, it was confirmed that the series is stationary (p-value < 0.05), which is a key requirement for ARIMA/SARIMAX models.
* Trend and Seasonality Analysis: Visual analysis of the series plot with a moving average showed the presence of an annual seasonality (generation is higher in summer) and a long-term growth trend.
* Autocorrelation Analysis: Plots of ACF (Autocorrelation Function) and PACF (Partial Autocorrelation Function) were constructed, confirming the presence of strong autocorrelation and seasonal structure, typical for a series suitable for SARIMA modeling.


```python
# from statsmodels.tsa.stattools import adfuller
# result = adfuller(merged_df['AT_solar_generation_actual'].dropna())
# print('ADF Statistic:', result[0])
# print('p-value:', result[1])
```

## Conclusion
As a result of the performed preprocessing, a clean, structured, and prepared dataset was obtained. It contains hourly time series of electricity generation and corresponding weather conditions for the selected regions, cleaned of missing values and converted to the required format. This dataset is ready for use in training a SARIMAX model, which will take into account seasonality, trends, and the influence of external factors (temperature) for electricity generation forecasting.
