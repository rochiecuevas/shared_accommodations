# Effects of peer-to-peer short-term rental to the housing market in San Francisco
## Introduction
## Method
### Data sources

<details><summary>More info</summary>
<p>
    
Information about the housing options in San Francisco are available online. For this study, raw data on home prices (based on assessed property value), hotel rates, house rental rates (long-term), and peer-to-peer home rental rates were obtained from the sites shown in Table 1.

Table 1. Sources of raw data for accommodation costs in San Francisco

|Description|Website Source|Dates covered|Raw data folder|
|---|---|---|---|
|Hotel rates|[SF City Performance Scorecards](https://sfgov.org/scorecards/tourism)|Jul 2004–May 2018|[Hotel Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Hotel%20Data)|
|Long-term rental rates|[Zillow](https://www.zillow.com/san-francisco-ca/home-values/)|Nov 2010–Sep 2018|[Rent Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Rent%20Data)|
|Home prices|[Data SF](https://data.sfgov.org/Housing-and-Buildings/Assessor-Historical-Secured-Property-Tax-Rolls/wv5m-vpq2/data)|2007–2016|[Home Prices](https://github.com/rochiecuevas/shared_accommodations/tree/master/Home%20Prices)|
|Peer-to-peer short-term rental rates|[Inside Airbnb](http://insideairbnb.com/san-francisco/?neighbourhood=&filterEntireHomes=false&filterHighlyAvailable=false&filterRecentReviews=false&filterMultiListings=false)|May 2015–Dec 2017|[Airbnb Listings Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20)|

</p>
</details>

### Data cleaning

<details><summary>More info</summary>
<p>
    
The raw datasets were cleaned using jupyter notebooks found inside each raw data folder. The cleaned data for each accommodation type was then written as a csv file and saved in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder.

#### Hotel data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/hotel_indicators.csv) contains average daily rates, occupancy rates, and revenue per available room. The [`hotel_rates.ipynb`](https://github.com/rochiecuevas/shared_accommodations/tree/master/Hotel%20Data) jupyter notebook was used to pre-process the data. Pre-processing involved changing the format of the date and the retention of two variables: `Average Daily Rate` and `Hotel Occupancy`. Click [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/README.md) to see a detailed description of how the hotel indicators were pre-processed.

#### Peer-to-peer short-term rental data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20) is organised into 28 csv files. The data was cleaned and the relevant metrics were merged into one csv file using the [`Airbnb_listings.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/Airbnb_listings.ipynb) jupyter notebook. A detailed description can be found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/README.md).

#### Long-term rental data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/rent_raw.csv) is composed of one csv file that contains monthly rental rates from November 2010 to September 2018. The [`Rent_Analysis.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/Rent_Analysis.ipynb) jupyter notebook is used to clean the data as described [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/README.md). 

#### Home prices data
The dataset is not uploaded because it exceeds the file size set by GitHub. It is, however, downloadable as a csv file from [DataDF](https://data.sfgov.org/Housing-and-Buildings/Assessor-Historical-Secured-Property-Tax-Rolls/wv5m-vpq2). Only the columns of interest were included in a dataframe, using the [`DataHome.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Home%20Prices/DataHome.ipynb) jupyter notebook. The procedure for cleaning the dataset is detailed [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Home%20Prices/README.md).

</p>
</details>

### Data processing/analyses

<details><summary>More info</summary>
<p>
    
Pandas and NumPy were used, unless otherwise stated.

#### Hotel data
The processed hotel data was stored in [`hotel_dailyrates.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hotel_dailyrates.csv). To find more insights from the data, it was important to convert the daily rate to monthly rate. Calculations were made more realistic by using hotel occupancy rates as a factor in correcting the monthly rates; without this correction factor, it is assumed that hotels are consistently 100% occupied. The code in `hotel_rate_analysis.ipynb` was used the calculations following these general steps:
1. Classify the months based on number of days;
2. Calculate the monthly rates by multiplying the `average daily rate` with the number of days (__Note:__ For February, further classify the entries to those in regular or in leap years);
3. Correct the monthly rates by multiplying these with the `hotel occupancy` rate.

To determine if there were trends across years, the yearly rates were calcuated by summing up the corrected monthly rates per year. Monthly rates and yearly rates were then saved into different csv files.

#### Peer-to-peer short-term rental data
#### Long-term rental data
#### Home prices data

</p>
</details>

### Data visualisation

<details><summary>More info</summary>
<p>

Matplotlib.pyplot and seaborn modules were used to plot the data into graphs.

</p>
</details>

## Results
## Conclusions
