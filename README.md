# KSchool project: People traffic forecasting

This is my project for the Data Science Master in KSchool. The purpose of it is to demonstrate the knowledge and skills acquired during the Master's. I wanted it to be industry related, so I decided to look for companies willing to share their data. 

Eventually I contacted with one company dedicated to the analysis of customer information using biometric technologies. They offer their clients services of customer analysis, supplying them with the information they need to make informed decisions and optimize their business strategies to make them more profitable. The purpose of this project is to find a generic predictor of people traffic that could be compatible with their system and eventually be offered as a new service in their catalogue. 

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

Before starting the exploratory analysis, there is a notebook created to adjust the meteorological data to the UTC time zone. That was a previous step needed to merge the data from both sources in order no analyze it properly.

The data of each site has been analyzed separately. The purpose of the exploratory process has been to check the consistency of each timeseries (gaps have been found on the Maragall and Jaume I time series), studying the presence of outliers and extracting information to be used in the forecasting models.

Bearing in mind that the purpose of the company is to come up with a generic predictor, there's not much room for domain specific features. Therefore, just 2 binary features were created, from patterns detected on the exploratory analysis: Weekday / Weekend and Open / Closed (to specify the schedule of each building).
