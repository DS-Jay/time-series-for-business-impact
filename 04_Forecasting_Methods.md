

### IV. Forecasting Methods

---

#### Simple Forecasting Methods

**1. Average Methods**

- **Description**: The average forecasting method calculates the mean of historical data and uses this value as the forecast for all future periods. It assumes that the fluctuations around the historical average will average out, particularly reliable when the data exhibits minimal variability and no discernible trends or seasonal patterns.
  
- **Use Case**: Ideal for businesses with stable demand or metrics where long-term averages provide a sufficient forecast basis, like utility consumption in stable demographic areas.

- **Implementation**:

  ```python
  # Calculate the average forecast
  average_forecast = df_power['Global_active_power'].mean()
  
  # Forecast future values
  future_forecast = [average_forecast] * 10  # Example for next 10 periods
  print(f"Average Forecast: {average_forecast}")
  ```

**2. Naive Methods**

- **Description**: Uses the last observed value as the forecast for all future periods. It is straightforward and reflects the most recent outcome into the future without adjusting for trends or seasonality, often effective when recent data points are the best predictors.

- **Use Case**: Useful for products or services where change is minimal and the most recent data points are the best predictors, such as predicting the next day's demand based on the most recent day's data in stable environments.

- **Implementation**:

  ```python
  # Calculate the naive forecast
  naive_forecast = df_power['Global_active_power'].iloc[-1]
  
  # Forecast future values
  future_forecast = [naive_forecast] * 10  # Example for next 10 periods
  print(f"Naive Forecast: {naive_forecast}")
  ```

**3. Seasonal Naive Method**

- **Description**: Extends the naive approach by assuming that the seasonal pattern will continue, using the last observed value from the same season to forecast the future. It assumes seasonal patterns repeat regularly over time.

- **Use Case**: Effective for seasonal businesses or products, such as forecasting holiday sales based on the previous years' data, allowing for better stock and staff management.

- **Implementation**:

  ```python
  # Calculate the seasonal naive forecast for a daily pattern
  seasonal_period = 365  # Assuming daily seasonality for one year
  seasonal_naive_forecast = df_power['Global_active_power'].shift(seasonal_period)
  
  # Forecast future values
  future_forecast = seasonal_naive_forecast[-10:]  # Example for next 10 periods
  print(f"Seasonal Naive Forecast: {future_forecast}")
  ```

[Link to Jupyter Notebook 7: Simple Forecasting Methods](notebooks/07_Simple_Forecasting_Methods.ipynb)


---

#### Exponential Smoothing Methods

**1. Simple Exponential Smoothing**

- **Description**: Applies exponentially decreasing weights to past observations, giving more importance to recent data and providing a smoothed level of the series. Useful for time series data with no trend or seasonality.

- **Use Case**: Useful for smoothing out fluctuations in demand or cost metrics to better understand the underlying level of a metric, aiding in stable decision-making processes.

- **Implementation**:

  ```python
  from statsmodels.tsa.holtwinters import SimpleExpSmoothing

  # Fit the model
  model = SimpleExpSmoothing(df_power['Global_active_power'].astype(float)).fit(smoothing_level=0.2, optimized=False)

  # Forecast future values
  future_forecast = model.forecast(steps=10)
  print("Simple Exponential Smoothing Forecast:", future_forecast)
  ```

**2. Holt’s Linear Trend Method**

- **Description**: An extension of exponential smoothing that accounts for trends in the data by using two equations: one for the level and one for the trend, allowing the forecast to adjust over time with increasing or decreasing trends.

- **Use Case**: Suitable for products experiencing growth or decline, helping adjust inventory or resource allocation in line with trend direction.

- **Implementation**:

  ```python
  from statsmodels.tsa.holtwinters

  # Fit the model
  model = ExponentialSmoothing(df_power['Global_active_power'].astype(float), trend='add').fit()

  # Forecast future values
  future_forecast = model.forecast(steps=10)
  print("Holt's Linear Trend Method Forecast:", future_forecast)
  ```

**3. Holt-Winters’ Seasonal Method**

- **Description**: Captures both trend and seasonality in the time series data. This method extends Holt’s method by adding seasonal smoothing, making it robust for handling data with both trends and complex seasonal variations.

- **Use Case**: Ideal for complex seasonal patterns, such as those found in retail sales or energy consumption, where both trend and seasonal impact need to be forecasted accurately.

- **Implementation**:

  ```python
  # Fit the model with seasonal components
  model = ExponentialSmoothing(df_power['Global_active_power'].astype(float), trend='add', seasonal='add', seasonal_periods=365).fit()

  # Forecast future values
  future_forecast = model.forecast(steps=10)
  print("Holt-Winters' Seasonal Method Forecast:", future_forecast)
  ```


[Link to Jupyter Notebook 8: Exponential Smoothing Methods](notebooks/08_Exponential_Smoothing_Methods.ipynb)


---

#### ARIMA Models

**1. Understanding AR, I, and MA Components**

- **Autoregressive (AR)**: Relies on the relationship between an observation and a number of lagged observations.
- **Integrated (I)**: Involves differencing the observations to make the series stationary, removing trends and seasonality.
- **Moving Average (MA)**: Models the error of the series as a combination of past errors and lagged observations.

**2. Selecting Appropriate ARIMA Models**

- **Description**: ARIMA combines AR, I, and MA components to fit data that exhibits trends and seasonality, making it powerful for forecasting non-stationary time series once they have been differenced to stationarity.

- **Use Case**: Best for detailed time series analysis where data behavior changes over time due to underlying trends and cycles.

- **Implementation**:

  ```python
  from statsmodels.tsa.arima.model import ARIMA

  # Fit the ARIMA model
  model = ARIMA(df_power['Global_active_power'].astype(float).dropna(), order=(1, 1, 1)).fit()

  # Forecast future values
  future_forecast = model.forecast(steps=10)
  print("ARIMA Model Forecast:", future_forecast)
  ```

[Link to Jupyter Notebook 9: ARIMA Models](notebooks/09_ARIMA_Models.ipynb)


---
 