

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

#### Advanced Forecasting Techniques

**1. Dynamic Regression Models**

- **Description**: Integrates ARIMA models with exogenous variables to account for external influences, significantly improving forecasting accuracy by incorporating additional relevant information.

- **Use Case**: Extremely useful when external factors such as economic indicators, weather conditions, or marketing activities significantly influence the forecast variable.

- **Implementation**:

  ```python
  # Example: Using temperature as an exogenous variable
  temperature = df_power['Voltage']  # Assume voltage impacts power consumption as an example

  # Fit the model
  model = ARIMA(df_power['Global_active_power'].astype(float).dropna(), order=(1, 1, 1), exog=temperature).fit()

  # Forecast future values
  future_forecast = model.forecast(steps=10, exog=[temperature[-10:]])
  print("Dynamic Regression Model Forecast:", future_forecast)
  ```

**2. Hierarchical and Grouped Time Series Forecasting**

- **Description**: Uses aggregation of time series data at different levels (e.g., product, regional, national) to ensure consistency and accuracy across hierarchical forecasts, essential for organizations with multi-level structures.

- **Use Case**: Critical for businesses with operations at multiple levels or locations, helping align forecasts from the product level up to the national sales forecasts.

- **Implementation**:

  ```python
  # Example: Using sub-metering data for hierarchical forecasting
  from hierarchies import AggregateTS

  # Aggregate data
  df_power['total_power'] = df_power[['Sub_metering_1', 'Sub_metering_2', 'Sub_metering_3']].sum(axis=1)

  # Aggregate time series
  agg_ts = AggregateTS(df_power['total_power'])

  # Forecast
  forecast = agg_ts.forecast()
  print("Hierarchical Forecast:", forecast)
  ```

**3. Vector Autoregression (VAR)**

- **Description**: A multivariate forecasting algorithm that models each variable within a multivariate time series as a function of past values of itself and the other variables, capturing the interdependencies among multiple time series.

- **Use Case**: Excellent for economic and financial sectors where multiple variables like prices, interest rates, and demand levels are interrelated.

- **Implementation**:

  ```python
  from statsmodels.tsa.vector_ar.var_model


  # Select variables
  df_vars = df_power[['Global_active_power', 'Global_reactive_power']].astype(float).dropna()

  # Fit the VAR model
  model = VAR(df_vars)
  model_fit = model.fit()

  # Forecast future values
  future_forecast = model_fit.forecast(df_vars.values[-10:], steps=10)
  print("VAR Model Forecast:", future_forecast)
  ```


[Link to Jupyter Notebook 10: Advanced Forecasting Techniques](notebooks/10_Advanced_Forecasting_Techniques.ipynb)

---

#### Additional Advanced Models

**1. Prophet**

- **Description**: Developed by Facebook, Prophet is tailored for handling time series data that reflects strong seasonal effects and various irregularities over time, such as missing data and major shifts in trends. It automatically detects and adapts to changes in trend and seasonality, making it highly adaptive.

- **Use Case**: Particularly effective for businesses that deal with promotional forecasts, holiday sales, and other calendar-driven fluctuations.

- **Implementation**:

  ```python
  from fbprophet import Prophet

  # Prepare the data
  df_prophet = df_power[['Global_active_power']].reset_index()
  df_prophet.columns = ['ds', 'y']

  # Fit the model
  model = Prophet()
  model.fit(df_prophet)

  # Forecast future values
  future = model.make_future_dataframe(periods=10, freq='D')
  forecast = model.predict(future)
  print("Prophet Forecast:", forecast[['ds', 'yhat', 'yhat_lower', 'yhat_upper']])
  ```

**2. Long Short-Term Memory (LSTM) Neural Networks**

- **Description**: LSTM networks are a type of recurrent neural network designed to learn long-term dependencies. They are particularly effective for time series data where past information is crucial for predicting future events, managing to remember information for long periods as part of the model's memory.

- **Use Case**: Highly suitable for complex and non-linear time series data such as financial markets, telecommunications data, or sophisticated demand forecasting where patterns are intricate and influenced by long-term historical contexts.

- **Implementation**:

  ```python
  from keras.models import Sequential
  from keras.layers import LSTM, Dense
  import numpy as np

  # Prepare data for LSTM
  def prepare_data(series, n_lags):
      X, y = [], []
      for i in range(n_lags, len(series)):
          X.append(series[i - n_lags:i])
          y.append(series[i])
      return np.array(X), np.array(y)

  data = df_power['Global_active_power'].values
  X, y = prepare_data(data, 5)

  # Define model
  model = Sequential([
      LSTM(50, activation='relu', input_shape=(5, 1)),
      Dense(1)
  ])
  model.compile(optimizer='adam', loss='mse')

  # Reshape data for (sample, timesteps, features)
  X = X.reshape((X.shape[0], X.shape[1], 1))

  # Fit model
  model.fit(X, y, epochs=20, verbose=0)

  # Forecast future values
  x_input = data[-5:].reshape((1, 5, 1))
  yhat = model.predict(x_input, verbose=0)
  print("LSTM Forecast:", yhat)
  ```

[Link to Jupyter Notebook 11: Advanced Forecasting Techniques](notebooks/11_Additional_Advanced_Models.ipynb)
