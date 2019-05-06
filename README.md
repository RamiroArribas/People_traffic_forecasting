# KSchool project: People traffic forecasting

This is my project for the Data Science Master in KSchool. The purpose of it is to demonstrate the knowledge and skills acquired during the Master's. I wanted it to be industry related, so I decided to look for companies willing to share their data. 

Eventually I contacted with one company dedicated to the analysis of customer information using biometric technologies. They offer their clients services of customer analysis, supplying them with the information they need to make informed decisions and optimize their business strategies to make them more profitable. The purpose of this project is to find a generic predictor of people traffic that could be compatible with their system and eventually be offered as a new service in their catalogue. During the development, the aim is to be able to answer the following questions:

      - Is it preferable to keep the original (high) frequency or would it be better to downsample the data to a daily frequency to get better results?
      - Do the external data and possible engineered features add relevant information to the models or just introduce noise resulting in a poorer performance?
      - Which of the models to be tested yields better results?
      - Do the assumptions made for one time series holds for the others?
      

The following are a list of services and software tools I have used to develop the project:
· Python has been the programming language used for exploring & cleaning data and for the creation and analysis of prediction models.
· The linux command line and Github for the control of the versions.

Because of privacy purposes the original data is not available on the repository, just the external weather data used, provided by Meteocat: http://www.meteocat.com/

All the python notebooks used are stored on the different folders of the repository and can be consulted in order to follow the steps taken during the project.


#### Data

The raw data provided by the company consist of 3 time series of different length, belonging to 3 public health buildings located in different places of the Barcelona province: 'Guinardó' and 'Gòtic' quarters, of the city of Barcelona, and Canyelles, a town at 50 km distance south-west of Barcelona. Concretely, the name of the buildings (which we will use when referring to its pertinent time series) are 'CAP Maragall', 'CAP Raval Nord' and 'CAP Jaume I'. 

Each time series contains 48 observations per day of the agreggated total amount of entries per time stamp (which will be the target feature), time stamp in unix time stamp format (UTC time zone) and other information that includes the SitedId, genre, different age intervals, exit traffic and total accesses (as a sum of entries plus exits).

The weather data from meteocat was provided on demand, as they limit the data available on their website. The original fields of their files are:

EMA: code of the meteorological station
DATA: time stamp in local time
T: average temperature
TX: max temperature
TN: min temperature
PPT: precipitation


#### Exploratory analysis and data cleanig

Before starting the exploratory analysis, there is a notebook created to adjust the meteorological data to the UTC time zone. That was a previous step needed to merge the data from both sources in order to analyze it properly.

The data of each site has been analyzed separately. The purpose of the exploratory process has been to check the consistency of each timeseries (gaps have been found on the Maragall and Jaume I time series), studying the presence of outliers and extracting information to be used in the forecasting models.

Bearing in mind that the purpose of the company is to come up with a generic predictor, there's not much room for domain specific features. Therefore, just 2 binary features were created, from patterns detected on the exploratory analysis: Weekday / Weekend and Open / Closed (to specify the schedule of each building). From the Maragall dataset, another feature was created, given the existence of relevant outliers: specifically, the outliers detected belong to two relevant dates of the recent political situation in catalonia. One is November 11th of 2017, when a demonstration took place claming for the liberation of the so called 'political prisoners'; the other is September 11th of 2018, date of celebration of the national festivity in Catalonia. The feature was created for possible future analytical purposes, since it would not be possible to use it for forecasting purposes (a future date should be flagged as outlier beforehand which for general purposes may not make sense).


#### Modeling

As stated in the introduction, the purpose of this project is to find an accurate predictor to forecast people traffic. Since the Maragall time series is the longer one, it has been used as a reference to test all the models. The machine learning techniques tested have been linear regression, auto-ARIMA and random forest.

When forecasting time series data, it's important to bear in mind one thing: if the purpose is to forecast (unseen) data, no future data can be included in the model. This obvious thought has important implications as, for example, that using lags of future observations as input (independent) features introduces a systematic bias on the results. We cannot know for certain which values those future lags would take, so the only way of building an honest predictor is to predict new data and along the way, use it as a base for the features of our new predictions.

Some custom functions have been coded prior to the analysis in order to make the process of results extraction and analysis a little easier:
  · train_dev_test: a function that implements the splitting of data in train, dev and test.
  · get_future_predictions: a generic function that allows the user to get results from unseen data. Among other parameters, it accepts a dataframe of 'external data' as a source from which to extract data of different nature (e.g. weather data, or data extracted from engineered features). It makes predictions, includes them as new observations and create new lags as features to forecast the next prediction.
  · ARIMA_future_preds: similar to the generic 'get_future_predictions' function, but adapted to the ARIMA context (where exogenous data can be used or not, etc.).

