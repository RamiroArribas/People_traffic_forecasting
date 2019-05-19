# KSchool project: People traffic forecasting

This is my project for the Data Science Master in KSchool. The purpose of it is to demonstrate the knowledge and skills acquired during the Master's. I wanted it to be industry related, so I decided to look for companies willing to share their data. 

Eventually I contacted with one company dedicated to the analysis of customer information using biometric technologies. They offer their clients services of customer analysis, supplying them with the information they need to make informed decisions and optimize their business strategies to make them more profitable. They are looking to widen the services offered in their catalogue and they proposed me to try and find a predictor of people traffic using the data they already gather.

So that became the purpose of this project: to establish the foundations of a generic predictor of people traffic for a given time window, compatible with their current systems, using machine learning algorithms .

During the development, the aim has been to be able to answer the following questions:

      - Is it preferable to keep the original (high) frequency or would it be better to downsample the data to a daily frequency to get better results?
      - Do the external data and possible engineered features add relevant information to the models or just introduce noise resulting in a poorer performance?
      - Which of the models to be tested yields better results?
      - Do the assumptions made for one time series holds for the others?
      

The following are a list of services and software tools I have used to develop the project:
· Python has been the programming language used for exploring & cleaning data and for the creation and analysis of prediction models.
· The linux command line and Github for the control of the versions.

Because of privacy reasons the original data is not available on the repository, just the external weather data used, provided by Meteocat: http://www.meteocat.com/

All the python notebooks used are stored on the different folders of the repository and can be consulted in order to follow the steps taken during the project.


#### Data

The raw data provided by the company consist of 3 time series of different length, belonging to 3 public health buildings located in different places of the Barcelona province: 'Guinardó' and 'Gòtic' quarters, of the city of Barcelona, and Canyelles, a town at 50 km distance south-west of Barcelona. Concretely, the name of the buildings (which we will use when referring to its pertinent time series) are 'CAP Maragall', 'CAP Raval Nord' and 'CAP Jaume I'. 

**Insert map picture**


Each time series contains 48 observations per day (at a frequency of 30 minutes) of the agreggated total amount of entries per time stamp (which will be the target feature), time stamp in unix time stamp format (UTC time zone) and other information that includes the SitedId, genre, different age intervals, exit traffic and total accesses (as a sum of entries plus exits).

The weather data from meteocat was provided on demand, as they limit the data available on their website. The granularity of the weather data is the same than the company data, a record every 30 minutes (that sums up to 48 observations per day). The original fields of their files are:

EMA: code of the meteorological station
DATA: time stamp in local time
T: average temperature
TX: max temperature
TN: min temperature
PPT: precipitation


#### Exploratory analysis and data cleanig

Before starting the exploratory analysis, there is a notebook created to adjust the meteorological data to the UTC time zone. That was a previous step needed to merge the data from both sources in order to analyze it properly.

The data of each site has been analyzed separately. The purpose of the exploratory process has been to check the consistency of each timeseries (gaps have been found on the Maragall and Jaume I time series), studying the presence of outliers and extracting information to be used in the forecasting models. At the end of this phase, different csv files of each location have been prepared for two different scenarios: original frequency forecasting and daily-grouped frequency forecasting. 

Bearing in mind that the purpose of the company is to come up with a generic predictor, there's not much room for domain specific features. Therefore, just 2 binary features have been created, from patterns detected on the exploratory analysis: Weekday / Weekend and Open / Closed (to specify the schedule of each building). For the datasets of grouped data daily, there's no 'Open /closed' feature. From the Maragall dataset, another feature has been created, given the existence of relevant outliers: specifically, the outliers detected belong to two relevant dates of the recent political situation in catalonia. One is November 11th of 2017, when a demonstration took place claming for the liberation of the so called 'political prisoners'; the other is September 11th of 2018, date of celebration of the national festivity in Catalonia. The feature has been created for possible future analytical purposes, since it would not be possible to use it for forecasting purposes (a future date should be flagged as outlier beforehand which for general purposes may not make sense).

When analyzing the data, the autocorrelation plot of each time series has shown the same phenomenon: there are weekly cycles of strong correlation of data. Therefore, it has been decided to use lags as features of the forecasting models, including all the records within one week (7 day * 48 observations = 336 lags on the original frequency data, and 7 lags on the down-sampled data).

**Insert autocorrelation plot picture**

When grouping the data by day, the traffic data has been summed, whilst for the meteorological data the average have been calculated for temperature and precipitation. Finally, different csv files have been prepared for each location and frequency.


#### Modeling

As stated in the introduction, the purpose of this project is to find the basis of an accurate predictor to forecast people traffic. Since the Maragall time series is the longer one, it has been used as a reference to test all the models. The machine learning techniques tested have been linear regression, auto-ARIMA and random forest.

When forecasting time series data, it's important to bear in mind one thing: if the purpose is to forecast (unseen data), no future data can be included in the model. This obvious thought has important implications as, for example, that using lags of future observations as input (independent) features introduces a systematic bias on the results that will produce a better performance. That woul be an artifact because we cannot know for certain which values those future lags would take, so the only way of building an honest predictor is to predict new data and along the way, use it as a base for the features of our new predictions. The only future inputs allowed are the independent features, since that information would be previously collected and transformed into a compatible format for the model.

Some custom functions have been coded prior to the analysis in order to make the process of results extraction and analysis a little easier:
  · train_dev_test: a function that implements the splitting of time series data (without randomizing) in train, dev and test.
  · get_future_predictions: a generic function that allows the user to get results from unseen data. Among other parameters, it accepts a dataframe of 'external data' as a source from which to extract data of different nature (e.g. weather data, or data extracted from engineered features). It makes predictions, includes them as new observations and create new lags as features to forecast the next prediction.
  · ARIMA_future_preds: similar to the generic 'get_future_predictions' function, but adapted to the ARIMA context (where exogenous data can be excluded). In this function the model isn't refit in each iteration.
  · get_scoring_measures: a function to build a data frame with the RMSE and R2_adjusted scorings of results extracted from different scenarios.


The plan for each machine learning algorithm used have been to produce and analyze results for different scenarios in order to test different hypothesis (as iterations in a lab experiment):
	- Predict known future or predict unknown future: for comparative reasons, results have been extracted both from lags of known observations (introducing future information in the forecast making predictions using X test) and from lags created along the forecasting process ('building' the X test with each prediction, calculating lags for each iteration and appending the external data from another data frame).
	- Use all the available data for the predictions or use just lags: since high frequency data is available, maybe the lags might have all the information the regressor needs to make accurate predictions and adding external information (such as weather data or the engineered features) would be a source of noise.
	- High frequency vs. daily frequency: since the aim of the project is to forecast people traffic for one day, maybe downsampling the data grouping by day is better than keeping the original resolution.


In general terms, for each algorithm tested there is a batch of results for 8 different scenarios. Both for high frequency and daily data, these are:
	· Predicting known future using all columns (lags are of known observations)
	· Predicting known future using just lags
	· Predicting unknown future using all columns (the lags calculation is updated on the fly with each iteration) 
	· Predicting unknown future using just lags.



Besides the above, the computational cost for each scenario has been measured as a way to check the time each model needs to process the data vs. the scoring it reaches.

Two notebooks have been prepared for each machine learning algorithm used, one for results extraction and other for results analysis. The machine learning algorithms tested have been: linear regression, ARIMA and Random Forest. For all models the same data splitting approach have been used (train, dev and test).

The performance scoring measures used have been Root Mean Square Error and R2 adjusted. The RMSE has two main characteristics worth mentioning: it punishes large errors and it's in the same units as the predictions, which is a plus for interpretation. R2 adjusted is a measure that helps to get an idea of the amount of variance explained by the model, and its main difference compared to R2 is that it adjusts for the number of terms in the model.

**Insert formula or RMSE & R2**


---Linear Regression---

The linear regression algorithm of the scikit-learn python library has been chosen for this section. The results extraction notebook is straightforward, aiming to get results for all scenarios:

**Insert table of performance scoring measures** 

As seen on the table above, when predicting known future, including or not external data doesn't make a big difference. However, when predicting unknown future, including external data dramatically improves the forecasts (both in terms of RMSE and R2 adjusted). 

Linear regression model chosen for futher analysis: the one that incudes all columns.

When predicting a few days into the future, scorings have been calculated for time windows of different span, and the best results are shown when taking into account the first 7 days. Comparisons of the two resolutions have been made, grouping the high frequency forecasts by day. 

**Insert table of scorings for windows of different span**

When analyzing the results of using high frequency data vs. daily grouped data, the R2_adjusted shows poor results in both scenarios but the RMSE is much better using high frequency than using daily data:

**Insert table comparing original an daily data performance scoring measures**

There's a special interest of the project, though, which is to find high accuracies for the next immediate days. When estimating the RMSE for the first 14 days it can be observed that even though in general terms the high frequency curve has smaller values, the daily curve has a better scoring some days:

**Insert graphic of RMSE curves for 14 days**


The calculation cost is much higher when predicting high frequency data than daily data.

**Insert lr runtime_df hourly**



---ARIMA---

The ARIMA algorithm used for this section has been the one offered by the pyramid ARIMA python package, that allows for an automatic adjustment of parameters. The term ARIMA stands for _Autoregressive Integrated Moving Average_, and is a class of model that captures a suite of different standard temporal structures in time series data.

The ARIMA models can be fit in two ways: just using the target variable or fitting the model using the target feature and exogenous data. When using exogenous data, constant values must be avoided.

During the results extraction process, when predicting unknown future using all columns, at some point a runtime error arose so it has not been possible to extract results for that scenario. On the other hand, besides the rest of scenarios, an extra iteration of results extraction has been run without using exogenous data.

The results obtained using ARIMA show the expected behaviour, with a much smaller RMSE when using observations lags, and better scorings using external data for unknown future predictions:

**Insert table of results**

The residuals analysis shows a fairly strong bias towards negative values, and there's a positive correlation of residuals against fitted values. The model that doesn't include exogenous data shows an almost constant oscillation between two values, so judging by that and by the scoring results it doesn't seem a good candidate:

**Insert plot of results in different scenarios**

ARIMA model chosen for further analysis: the one that just includes lags.

For the first few predicted days, the time window that yields the best results is the one that includes 12 days. When comparing both resolutions for the overall test data, the ARIMA algorithm shows a better RMSE performance using the daily resolution than the high frequency resolution.

When we shift the focus to the first days, results differ: for spans of different size, high resolution is better bu when considering specific future days, the daily resolution is the winner.

When considering the computational cost, the most costly algorithm is the high frequency model that just uses lags whilst all the others take almost no time to run. If the aim would be to find good results for the first days, the daily resolution model using just lags would be the winner (as it is much faster to be trained).


