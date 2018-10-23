# Effects of peer-to-peer short-term rental to the housing market in San Francisco
## Introduction
## Method
### Data sources
Information about the housing options in San Francisco are available online. For this study, raw data on housing prices (based on assessed property value), hotel rates, house rental rates (long-term), and peer-to-peer home rental rates were obtained from the sites shown in Table 1.

Table 1. Sources of raw data for accommodation costs in San Francisco

|Description|Website|Dates covered|Raw data folder|
|---|---|---|---|
|Hotel rates|[SF City Performance Scorecards](https://sfgov.org/scorecards/tourism)|Jul 2004–May 2018|[Hotel Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Hotel%20Data)|
|Long-term rental rates|[Zillow](https://www.zillow.com/san-francisco-ca/home-values/)|Nov 2010–Sep 2018|[Rent Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Rent%20Data)|
|Housing prices|[Data SF](https://data.sfgov.org/Housing-and-Buildings/Assessments-by-Year-and-Neighborhood-Code/qxzx-hau5)|2007–2016|[Home Prices](https://github.com/rochiecuevas/shared_accommodations/tree/master/Home%20Prices)|
|Peer-to-peer short-term rental rates|[Inside Airbnb](http://insideairbnb.com/san-francisco/?neighbourhood=&filterEntireHomes=false&filterHighlyAvailable=false&filterRecentReviews=false&filterMultiListings=false)|May 2015–Dec 2017|[Airbnb Listings Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20)|

### Data cleaning
The raw datasets were cleaned using jupyter notebooks found inside each raw data folder. The cleaned data for each accommodation type was then written as a csv file and saved in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder.

#### Hotel data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/hotel_indicators.csv) contains average daily rates, occupancy rates, and revenue per available room. The [`hotel_rates.ipynb`](https://github.com/rochiecuevas/shared_accommodations/tree/master/Hotel%20Data) jupyter notebook was used to pre-process the data. Pre-processing involved changing the format of the date and the retention of two variables: `Average Daily Rate` and `Hotel Occupancy`. Click [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/README.md) to see a detailed description of hoe the hotel indicators were pre-processing.

#### Peer-to-peer short-term rental data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20) is organised into 28 csv files. The data was cleaned and the relevant metrics were merged into one csv file using the [`Airbnb_listings.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/Airbnb_listings.ipynb) jupyter notebook. A detailed description can be found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/README.md).

#### Long-term rental data
#### Housing prices data

### Data analyses
### Data visualisation
## Results
## Conclusions
