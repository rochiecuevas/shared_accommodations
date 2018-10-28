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
The raw datasets were cleaned using jupyter notebooks found inside each raw data folder. The cleaned data for each accommodation type was then written as a csv file and saved in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder.

<details><summary>More info</summary>
<p>
    
#### Hotel data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/hotel_indicators.csv) contains average daily rates, occupancy rates, and revenue per available room. The [`hotel_rates.ipynb`](https://github.com/rochiecuevas/shared_accommodations/tree/master/Hotel%20Data) jupyter notebook was used to pre-process the data. Pre-processing involved changing the format of the date and the retention of two variables: `Average Daily Rate` and `Hotel Occupancy`. Click [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/README.md) to see a detailed description of how the hotel indicators were pre-processed.

#### Peer-to-peer short-term rental data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20) is organised into 28 csv files. The data was cleaned and the relevant metrics were merged into one csv file using the [`Airbnb_listings.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/Airbnb_listings.ipynb) jupyter notebook. A detailed description can be found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/README.md).

#### Long-term rental data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/rent_raw.csv) is composed of one csv file that contains monthly rental rates from November 2010 to September 2018. The [`Rent_Analysis.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/Rent_Analysis.ipynb) jupyter notebook is used to clean the data as described [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/README.md). 

#### Home prices data
The dataset is not uploaded because it exceeds the file size set by GitHub. It is, however, downloadable as a csv file from [DataDF](https://data.sfgov.org/Housing-and-Buildings/Assessor-Historical-Secured-Property-Tax-Rolls/wv5m-vpq2). `Assessed Land Value` was used as a proxy for home price. Only the columns of interest were included in a dataframe, using the [`DataHome.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Home%20Prices/DataHome.ipynb) jupyter notebook. The procedure for cleaning the dataset is detailed [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Home%20Prices/README.md).

</p>
</details>

### Data processing/analyses
Pandas and NumPy were used, unless otherwise stated.

<details><summary>More info</summary>
<p>
    
#### Hotel data
The processed hotel data was stored in [`hotel_dailyrates.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hotel_dailyrates.csv). To find more insights from the data, it was important to convert the daily rate to monthly rate. Calculations were made more realistic by using hotel occupancy rates as a factor in correcting the monthly rates; without this correction factor, it is assumed that hotels are consistently 100% occupied. The code in `hotel_rate_analysis.ipynb` was used the calculations following these general steps:
1. Classify the months based on number of days;
2. Calculate the monthly rates by multiplying the `average daily rate` with the number of days (__Note:__ For February, further classify the entries to those in regular or in leap years);
3. Correct the monthly rates by multiplying these with the `hotel occupancy` rate.

To determine if there were trends across years, the yearly rates were calcuated by summing up the corrected monthly rates per year. Monthly rates and yearly rates were then saved into different csv files.

#### Peer-to-peer short-term rental data
The output file [`Airbnb_listings.csv`](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20) was loaded as a dataframe. An extra data cleaning step was conducted prior to data analysis because the output file was saved with index not set to "False" (an extra column called "Unnamed:0" was added to the dataframe). This column was removed. And the dataframe was renamed.

```python
# Remove the Unnamed:0 column
airbnb_rate = airbnb_df.drop(['Unnamed: 0'], axis=1)

# Rename the dataframe 
data = airbnb_rate
```

The steps followed for data cleaning were:
1. Extract the year substring from the values in the `date` column.
2. For each year of Airbnb data, extract the relevant rows from the daframe, put the rows in a separate dataframe(e.g., " and then group the data based on neighbourhood and get the mean.
3. Rename the "Average annual rate" for clarity.
4. Merge the year dataframes on `neighbourhood`, and rename the columns.
5. Create column entitled `District`.
6. Populate the `District` column by filtering through lists of San Francisco districts.
7. Save the dataframe as a csv file.

#### Long-term rental data
Data from [`rent_raw.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/Rent_Analysis.ipynb) was loaded as the `rent_df` dataframe. Calculating the yearly rate for each neighbourhood was done as follows:
1. Create a user-defined function (`totals`) that automatically calculates yearly totals per neighbourhood.
2. Create a list of months in two-digit combinations. 
3. Use a list comprehension that uses the `totals` function.
4. Create the dataframe `year_rent_df` which has each year and neighbourhoods as keys.
5. Calculate the mean of Airbnb rental for each year-neighbourhood pair.
6. Add geolocation data extracted from Google Maps Geocoding API.
7. Save the dataframe as a csv file, `yearly_rent.csv`.
.

#### Merged home price and rental data
Two csv files were merged ([`combine_updated.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/combine_updated.csv) and [`yearly_rent.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/yearly_rent.csv)) in an attempt to look at trends side-by-side for these two housing sectors. The description of the merging is found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Merged/README.md).

</p>
</details>

### Data visualisation
Matplotlib.pyplot and seaborn modules were used to plot the data into graphs. Graphs were generated for merged [home and long-term rental data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Rent%20and%20Housing%20Visualization), [hotel trends](https://github.com/rochiecuevas/shared_accommodations/blob/master/hotel_rate_visualisation.ipynb), and [peer-to-peer short-term rental](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Analysis/AirbnbRateVisualisation.ipynb).

<details><summary>More info</summary>
<p>

Documentation of visualising __home prices and long-term rental rates__ are found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20and%20Housing%20Visualization/README.md).

On the other hand, __hotel rate__ trends were visualised with bar graphs and with time series line plots. The seaborn plot style was used. Yearly, monthly, and quarterly average rates were plotted.

```python
# graphing style
plt.style.use("seaborn")
```

__*bar graphs*__ 
The dataframe generated from [`hotel_yrrate.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hotel_yrrate.csv) was sorted by year.

```python
# Sort the dataframe by year
year_df.sort_values("Year", inplace = True)
year_df = year_df.reset_index(drop = True)
```

And incomplete years were taken out of the dataframe.

```python
# Create a subset of complete years (incomplete: 2004 and 2018)
inc = [2004, 2018]

year_subdf = year_df[~year_df["Year"].isin(inc)]
```

The clean data was then plotted into a bar graph.

```python
# Create a bar graph to show trends in hotel yearly rates
plt.bar("Year", "Yearly Rate", data = year_subdf)
plt.xlabel("Year")
plt.ylabel("Annual Hotel Rate (USD)")
plt.ylim(0, max(year_subdf["Yearly Rate"] + 20000))
```

The output image was saved in the [Images](https://github.com/rochiecuevas/shared_accommodations/tree/master/Images) folder.

```python
# Save figure
plt.savefig("Images/hotel_yr_rates.svg")
plt.show()
```

__*difference plots*__
The plotting procedure was adapted from [Pymaceuticals](https://github.com/rochiecuevas/Pymaceuticals/blob/master/pymaceuticals_starter.ipynb)

__*line graphs*__
The data in [`hotel_monthlyrate.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hotel_monthlyrate.csv) was loaded as a dataframe and then sorted by date.

```python
# Sort values by date
mon_rate_df.sort_values("Date", inplace = True)
mon_rate_df = mon_rate_df.reset_index(drop = True)
```

Because years 2004 and 2018 had incomplete data (less than 12 months), data from these years were excluded from the visualisation.

```python
# Exclude data from 2004 and 2018
inc_str = [str(year) for year in inc] # pass the years as string (because the date is in string form)

mon_rate_df = mon_rate_df.loc[~mon_rate_df["Date"].str.contains("|".join(inc_str))] # get the data from 2005–2017
```

`Date` and `Corrected Monthly Rate` were placed into a new dataframe. Because `Date` values were strings, these were converted into datetime and then into period (i.e., the date corresponded to a time interval rather than to an actual date).

```python
# Data is in monthly periods so set period to monthly
# Resource: http://earthpy.org/time_series_analysis_with_pandas_part_2.html

mon_rate_df2["Corrected Monthly Rate"].index = pd.to_datetime(mon_rate_df2["Corrected Monthly Rate"].index)
    # string converted to datetime format
    
mon_rate_df2.index = mon_rate_df2["Corrected Monthly Rate"].to_period(freq = "M").index 
    # convert time stamps to time periods
```

Monthly trends were then plotted using the pandas.DataFrame.plot function and saved as an .svg file.

```python
# Plot time series for corrected monthly rate
time_series01 = mon_rate_df2.plot.line(figsize = (8,5), legend = False)
time_series01.set_ylabel("Corrected Monthly Rate (USD)")

# Save image
plt.savefig("Images/hotel_timeseries01.svg")
```

The monthly data could be aggregated into quarters as well, and their means obtained.

```python
# Get quarterly data
# start the quarter from November so that the data from Q1-Q3 of 2015 are included

q_mean = mon_rate_df2.resample("Q-NOV").mean() 
```

The quarterly data could then be plotted and saved as an svg file.

```python
# Plot data by quarter
time_series02 = q_mean[q_mean.index.quarter == 1].plot(figsize = (8,5), legend = False)
time_series02.set_ylabel("Quarterly Means (USD)")

# Save image
plt.savefig("Images/hotel_timeseries02.svg")
```

Visualisation of __peer-to-peer short-term rental rates__ involved loading the `airbnbdataanalysis.csv` into a dataframe and using the pandas.DataFrame.plot function to generate a plot containing subplots for each year.

```python
# Plot data for 2015–2017 in subplots
ax = df.plot.bar(x = "neighbourhood", subplots = True,
                 figsize = (8,15), title = ["", "", ""])
ax[0].set_ylabel("Average Annual Rate (USD)")
ax[1].set_ylabel("Average Annual Rate (USD)")
ax[2].set_ylabel("Average Annual Rate (USD)")

ax[0].set_ylim(0, 220000)
ax[1].set_ylim(0, 220000)
ax[2].set_ylim(0, 220000)


# Save file as svg
plt.tight_layout()
plt.savefig("../Images/Airbnb_annual_avg.svg")
plt.savefig("../Images/Airbnb_annual_avg.png")
```

Since district infomation was available, it was possible to group the data by district and get the mean rates.

```python
# Group the data by neighbourhood
df2 = round(df.groupby("District").mean(),2)
```

The average rates for each year could then be plotted in a bar chart by district, and then saved.

```python
# Plot annual average rate of Airbnb
ax2 = df2.plot.bar(figsize = (8, 8))
ax2.set_ylabel("Average Annual Rate (USD)")

# Save file as svg
plt.tight_layout()
plt.savefig("../Images/Airbnb_annual_avg2.svg")
plt.savefig("../Images/Airbnb_annual_avg2.png")
```

</p>
</details>

## Results
In 2011 and in 2016, the housing price was highest, on average, in the Financial District while the most affordable neighbourhoods to own a home were Silver Tree and Visitacion Valley (Fig 1, 2). The skyrocketing price of owning a home in the Financial District can probably be attributed to the low supply of homes but a high demand, particularly with the tech boom in the area. On the other hand, Visitacion Valley and Silver Tree attract lower-income people because of the [presence of low-cost housing](http://sf-planning.org/sites/default/files/FileCenter/Documents/3635-chapter_6-5.pdf). 

<details><summary>More info</summary>
<p>
    
![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Assessed%20Land%20Value%20(per%20Neigborhood)%20for%202011.svg?sanitize=True)
*Fig 1. Home Prices (USD) in San Francisco neighbourhoods in 2011* 

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Assessed%20Land%20Value%20(per%20Neigborhood)%20for%202016.svg)
*Fig 2. Home Prices (USD) in San Francisco neighbourhoods in 2016* 

A look into the yearly trend at the district level indicates that the Downtown area of San Francisco is the most expensive neighbouhood from 2007 to 2016. This is most likely driven by the home prices in the Financial District.

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/home_price_district.svg?sanitize=True)
*Fig 3. 2011 Home Prices (USD) in San Francisco neighbourhoods, grouped into districts*

It is notable that long-term rental rates in the Financial District was not as different as the other neighbourhood. Three neighbourhoods popped out having higher long-term rental rates in 2011 and in 2016 (Fig 4, 5): Presidio Heights, Sea Cliff, and St. Francis Wood. Sea Cliff and St. Francis Wood are classified as ["residence parks"](https://en.wikipedia.org/wiki/San_Francisco_Residence_Parks).

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Yearly%20Rent%20Price%20(per%20Neigborhood)%20for%202011.svg?sanitize=True)

*Fig 4. Annual Rent Prices (USD) in San Francisco in 2011*

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Yearly%20Rent%20Price%20(per%20Neigborhood)%20for%202016.svg?sanitize=True)

*Fig 5. Annual Rent Prices (USD) in San Francisco in 2016*

The trends in price of accommodations increased from 2011 to 2016 (Fig 6). However, the rates of increases are different. The home price increases were more gradual than that for long-term rental rate. From 2012 to 2015, the increase in rental rates was steep; but the increases seemed to slow down in 2015–2016.

|Home price|Long-term rent|
|---|---|
|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Yearly%20Land%20Value%20Change.svg?sanitize=True)|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Yearly%20Rent%20Price%20Change.svg?sanitize=True)|

*Fig 6. Annual Home Prices and Rent Rates (USD) in San Francisco from 2011 to 2016*

The heatmaps (Fig 7) show the most expensive places (red) to live, providing some geographic context to the bar graphs.

|Home price|Long-term rent|
|---|---|
|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/house.png)|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/rent.png)|

*Fig 7. Comparison of Geographic Distribution of Home Prices and Long-Term Rental Rates*

Hotel rates are also going up (Fig 8). There was a drop in hotel rates between 2008 and 2009, but this can probably be attributed to the Recession during that time. Since 2009, however, the hotel rates have grown very fast. In 2015–2017, the data suggests that the increase in hotel rates is slowing down. 

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/hotel_timeseries01.svg?sanitize=True)
*Fig 8. Monthly Hotel Rates from 2005 to 2017*

The peer-to-peer short-term rental rates appeared to by highest in Presidio and Presidio Heights, mimicking patterns with long-term rental rates.

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Airbnb_annual_avg.svg?sanitize=True)
*Fig 9. Peer-to-Peer Short-Term Rental Rates (USD) in San Francisco Neighbourhoods*


</p>
</details>

## Conclusions
