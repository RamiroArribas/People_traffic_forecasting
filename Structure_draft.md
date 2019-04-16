## 1.- Construction of the data frame

· Import the original traffic data

· Import the weather data (and data from other relevant sources)

· Set date time as index and concatenate the data frames: the keys for the concatenation will be the index and the SiteId


## 2.- Exploratory analysis of each SiteId (separetely)

· Check for consistency (whether the time series is continuous or not)

· Check for NaNs

· Check for outliers

· Apply a Dickey-Fuller test to check that the time series is non-stationary

· Plot the data in different ways to extract insights: length of the series, frequency of the outliers, series structure, etc.

· Plot traffic against the other variables to check for correlations

· Create a correlation matrix

· (To be tested): decompose the series in a meaningful way. If there are indistinguishable patterns due to the high frequency of the data, aggregate it just to extract insights (even if you keep the data in its original frequency when modelling).


## 3.- Feature engineering

· Limit the range of the data frames for each site in a sensible way (discard non useful dates). 

· Create a dummy variable to distinguish working days from weekend days

· Create a dummy variable to highlight the outliers

· From the results of the exploratory analysis, discuss if there might be benefit from transforming the data (apply a Box-Cox Transformation, find the lambda and discuss it).

· Create lags from previous steps

· (To be tested): create a binay feature to distinguish open and close service (schedule).


## 4.- Modelling

· Develop a test harness: data extraction, resampling technique and performance measure to be used (RMSE, R^2 and R^2-Adjusted)

· Wirte the code for the walk-forward validation

· Try different models: baseline (naïve forecast), multivariate linear regression (autoregression using lags), K-nearest neighbourhood, Random Forest, ARIMA & others (consider SVM, ensemble methods such as AdaBoost, etc.).

· Perform a systematic analysis for each model used:
  - Compare results of using traffic data only vs. using external data
  - Grid search in an optimized way for the best parameters to be used in each model.
  - Plot the predicted values along with the observed values to get a visual estimation of the results.
  - Perform a thorough analysis of the residuals:
    + Scatter plot, autocorrelation plot, histogram (to see if they are normally distributed)
    + Plot residuals against predictors to check if there is an underlying relationship
    + Plot residuals against fitted values: if a pattern is observed, might be a sign that the forecast variable need to be transformed
  - Merge train+dev data to make predictions on the test data
  - Analyze the performance measures for each model
  - Save the predictions both in unique results and confidence intervals
  - Make predictions for the next day, 3 days and 7 days. Plot predicted vs. observed and check that the error of the 7 days forecast is larger than 1 day 
  - Apply walk-forward validation vs. classical train-test: compare which one yields the best performing scores and plot predictions of both sampling methods vs. the observed data

· Prepare code to save and load each of the models.

· (To be tested): compare the results obtained by each model vs. the results with aggregated data by day
· (To be tested): compare the results obtained using regular data vs. the results using transformed data with the Box-Cox method. Considerations:
  - The predictions need to be back-transformed
  - Usually, the back-transformed point forecast will be the median of the forecast distribution instead of the mean (so we might need to bias-adjust the forecast). To be taken into account when adding up the predictions to present a result for one day.


## 5.- Conclusions

· Make a point on the traffic data: does it contain enough information by itself or the models perform better with weather data?

· Is it feasible to apply walk-forward validation for each model?

· How would you implement it on production? 
  - How much historical data do we need to implement the predictive algorithm?

· Even though we are looking for a generic algorithm, discuss the advantages of adapting the algorithm and its features for each specific domain (different business schedules, different forecasting applications, etc.) 
  

## 6.- Appendices

· Create a notebook specifying all the libraries needed (and a file containing the virtual environment in which the project was developed).
· Create the dashboard in which to showcase the project
