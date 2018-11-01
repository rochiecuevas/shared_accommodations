# Effects of peer-to-peer short-term rental to the housing market in San Francisco
## Introduction
San Francisco is [one of the most visited cities in the USA](https://www.businessinsider.com/most-visited-us-cities-2017-12?r=UK&IR=T). It attracts about [25 million visitors](https://www.sftravel.com/article/san-francisco-travel-reports-record-breaking-year-tourism), with tourism being its biggest industry. With many visitors streaming in, residents have started renting out their spaces for [short durations](https://businessportal.sfgov.org/start/starter-kits/short-term-rental) for extra income and use [peer-to-peer property rental](https://en.wikipedia.org/wiki/Peer-to-peer_property_rental) platforms such as Airbnb to list their available spaces. 

This study aims to determine the impact of peer-to-peer short term rental to the housing market in San Francisco. It is hypothesised that short-term rent have effects on hotel rates, long-term rental rates, and home prices in the City. 

## Method
### Data sources
Information about the housing options in San Francisco are available online. For this study, raw data on home prices (based on assessed property value), hotel rates, long-term rental rates, and peer-to-peer short-term rental rates were obtained from the sites shown in Table 1.

<details><summary>More info</summary>
<p>
    
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
The [dataset](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/hotel_indicators.csv) contains average daily rates, occupancy rates, and revenue per available room. The [`hotel_rates.ipynb`](https://github.com/rochiecuevas/shared_accommodations/tree/master/Hotel%20Data) jupyter notebook was used to pre-process the data. Pre-processing involved changing the format of the date and the retention of two variables: "Average Daily Rate" and "Hotel Occupancy". Click [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/README.md) to see a detailed description of how the hotel indicators were pre-processed.

#### Peer-to-peer short-term rental data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20) is organised into 28 csv files. The data was cleaned and the relevant metrics were merged into one csv file using the [`Airbnb_listings.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/Airbnb_listings.ipynb) jupyter notebook. A detailed description can be found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/README.md).

#### Long-term rental data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/rent_raw.csv) is composed of one csv file that contains monthly rental rates from November 2010 to September 2018. The [`Rent_Analysis.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/Rent_Analysis.ipynb) jupyter notebook was used to clean the data as described [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/README.md). 

#### Home prices data
The dataset is not uploaded because it exceeds the file size set by GitHub. It is, however, downloadable as a csv file from [DataSF](https://data.sfgov.org/Housing-and-Buildings/Assessor-Historical-Secured-Property-Tax-Rolls/wv5m-vpq2). "Assessed Land Value" was used as a proxy for home price. Only the columns of interest were included in a dataframe, using the [`DataHome.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Home%20Prices/DataHome.ipynb) jupyter notebook. The procedure for cleaning the dataset is detailed [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Home%20Prices/README.md).

</p>
</details>

### Data processing/analyses
Pandas and NumPy were used, unless otherwise stated.

<details><summary>More info</summary>
<p>
    
#### Hotel data
The processed hotel data was stored in [`hotel_dailyrates.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hotel_dailyrates.csv). To find more insights from the data, it was important to convert the daily rate to monthly rate. Calculations were made more realistic by using hotel occupancy rates as a factor in correcting the monthly rates; without this correction factor, it is assumed that hotels are consistently 100% occupied. The code in [`hotel_rate_analysis.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/hotel_rate_analysis.ipynb) was used the calculations following these general steps:
1. Classify the months based on number of days.
2. Calculate the monthly rates by multiplying the "average daily rate" with the number of days (__Note:__ For February, further classify the entries to those in regular or in leap years).
3. Correct the monthly rates by multiplying these with the "hotel occupancy" rate.

To determine if there were trends across years, the yearly rates were calcuated by summing up the corrected monthly rates per year. Monthly rates and yearly rates were then saved into different csv files.

#### Peer-to-peer short-term rental data
Analysis was conducted using the [`AirbnbRateAnalysis.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/AirbnbRateAnalysis.ipynb) jupyter notebook. The output file [`Airbnb_listings.csv`](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20) was loaded as a dataframe. 

The steps followed for data analysis followed were: 
1. Extract the year substring from the values in the "date" column.

```python
data['date'] = [e[:4] for e in data['date']]
```

2. Create a dataframe containing the number of listings per neighbourhood per year. Tabulate the number of listings according to year.

```python
# Get the number of listings per year and per neighbourhood
data1 = data.groupby(["date", "neighbourhood"])["daily rate"].nunique()
data1 = data1.reset_index()
data1 = data1.rename(columns = {"daily rate": "No. of listings",
                                "date": "year"})

# Use years as headers
data1 = data1.pivot(index = "neighbourhood", columns = "year")
data1.columns = data1.columns.droplevel(0)
data1 = data1.rename(columns = {"2015": "Listings_2015", 
                                "2016": "Listings_2016", 
                                "2017": "Listings_2017"})
data1 = data1.reset_index()
```

3. Calculate the annual average rent per neighbourhood per year. Tabulate the rental rates (STR) according to year.

```python
# Calculate mean annual rental rates based on years and neighbourhoods
data2 = round(data.groupby(["date", "neighbourhood"]).mean(), 2)
data2 = data2.reset_index()
data2 = data2.rename(columns = {"date": "year"})

# Use years as headers
data2 = data2.pivot(index = "neighbourhood", columns = "year")
data2.columns = data2.columns.droplevel(0)
data2 = data2.rename(columns = {"2015": "STR_2015", 
                                "2016": "STR_2016", 
                                "2017": "STR_2017"})
data2 = data2.reset_index()
```

4. Merge the year dataframes based on "neighbourhood".

```python
Airbnb_avg = pd.merge(data1, data2, on = "neighbourhood")
```

6. Add district information per neighbourhood based on lists of districts (containing neighbourhoods).
7. Save the dataframe as [`airbnbdataanalysis.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/airbnbdataanalysis.csv).

#### Long-term rental data
Data from [`rent_raw.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/Rent_Analysis.ipynb) was loaded as the `rent_df` dataframe. Calculating the yearly rate for each neighbourhood was done as follows:
1. Create a user-defined function ("totals") that automatically calculates yearly totals per neighbourhood.
2. Create a list of years in two-digit combinations. 
3. Use a list comprehension that uses the "totals" function to calculate yearly totals.
4. Create the dataframe "year_rent_df" in which year and neighbourhoods are the column headers.
5. Calculate the mean of long-term rental for each year-neighbourhood pair.
6. Add geolocation data extracted from Google Maps Geocoding API.
7. Save the dataframe as a csv file, [`yearly_rent.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/yearly_rent.csv).
.

#### Merged home price and rental data
Two csv files were merged ([`combine_updated.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/combine_updated.csv) and [`yearly_rent.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/yearly_rent.csv)) in an attempt to look at trends side-by-side for these two housing sectors. The description of the merging is found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Merged/README.md).

</p>
</details>

### Data visualisation
Matplotlib.pyplot and seaborn modules were used to plot the data into graphs. Graphs were generated for merged [home and long-term rental data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Rent%20and%20Housing%20Visualization), [hotel trends](https://github.com/rochiecuevas/shared_accommodations/blob/master/hotel_rate_visualisation.ipynb), and [peer-to-peer short-term rental](https://github.com/rochiecuevas/shared_accommodations/blob/master/AirbnbRateVisualisation.ipynb).

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

Because years 2004 and 2018 had incomplete data (less than 12 months), data from these years were excluded from the graphs.

```python
# Exclude data from 2004 and 2018
inc_str = [str(year) for year in inc] # pass the years as string (because the date is in string form)

mon_rate_df = mon_rate_df.loc[~mon_rate_df["Date"].str.contains("|".join(inc_str))] # get the data from 2005–2017
```

The "Date" and "Corrected Monthly Rate" columns were placed into a new dataframe. Because "Date" values were strings, these were converted into datetime and then into period (i.e., the date corresponded to a time interval rather than to an actual date) formats.

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

The quarterly data was plotted and saved as an svg file.

```python
# Plot data by quarter
time_series02 = q_mean[q_mean.index.quarter == 1].plot(figsize = (8,5), legend = False)
time_series02.set_ylabel("Quarterly Means (USD)")

# Save image
plt.savefig("Images/hotel_timeseries02.svg")
```

Visualisation of __peer-to-peer short-term rental rates__ involved loading the [`airbnbdataanalysis.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/airbnbdataanalysis.csv) into a dataframe. Then, a user-defined function, `bargraphs`, was built to generate bargraphs with subplots corresponding to each year. 

```python
def bargraphs(df, str): 
    ax = df.plot.bar(x = "neighbourhood", 
                 y = [i for i in df.filter(regex = str)], # select column headers with substring
                 subplots = True,
                 figsize = (8,15), title = ["", "", ""])
    return ax
```

This function was used to produce bargraphs of short-term rental rates and of number of listings.

```python
bar_STR = bargraphs(df,"STR")
bar_Listings = bargraphs(df,"Listings")
```

Each bargraph had three subplots. To add y-axis labels for each subplot, a for-loop was used. The code below was for the short-term rental rates bargraph.

```python
for yr in range(0, len(bar_STR)): # loop through all subplots in ax (there are three, corresponding to the years)
    bar_STR[yr].set_ylabel("Average Annual Rate (USD)")
    bar_STR[yr].set_ylim(0, 220000)
```

Since district infomation was available, it was possible to group the data by district and get the mean rates.

```python
# Group the data by neighbourhood
df2 = round(df.groupby("District").mean(),2)
```

The average rates for each year were be plotted in a bar chart by district using a user-defined function called `bargraphs2`.

```python
def bargraphs2(df, str):
    ax2 = df.plot.bar(y = [i for i in df.filter(regex = str)], figsize = (8, 8))
    return ax2

bargr_STR = bargraphs2(df2, "STR")
bargr_Listings = bargraphs2(df2, "Listings")
```

To find the geographic context of the trends, heatmaps were generated for each year in which short-term rental rates and number of listings were the weights. The modules required were requests and json. The Google Maps API key (gkey) was stored in a config file which was not pushed into the repository for security purposes.

```python
# Dependencies
import requests
import json
from config import gkey
```

Columns corresponding to city, latitude, and longitude of each neighbourhood were added to the dataframe `df`.

```python
# Add columns for geolocations per neighbourhood
df["City"] = "San Francisco"
df["Lat"] = ""
df["Long"] = ""

```

The geographical coordinates of the neighbourhoods were obtained using Google Maps Geocoding API.

```python
# Add coordinates per neighbourhood
for index, row in df.iterrows():
    city = row["City"]
    neighborhood = row["neighbourhood"]
    
    url = f"https://maps.googleapis.com/maps/api/geocode/json?address={neighborhood}&{city}&key={gkey}"
    response_json = requests.get(url).json()


    #pprint(response_json)    
    
    df.loc[index,"Lat"] = response_json["results"][0]["geometry"]["location"]["lat"]
    df.loc[index,"Long"] = response_json["results"][0]["geometry"]["location"]["lng"]
```

Heatmaps were then generated using the folium module. The folium.Map location had coordinates for San Francisco (lat = 37.7747, lng = -122.419). In each heatmap, the maximum values for the weights were based on the 2017 data. As the weights increased, the colour turned redder on the map.

```python
#Dependencies
import folium
from folium.plugins import HeatMap

max_amount= float(df["Listings_2017"].max())

hmap_2015 = folium.Map(location=[37.7749, -122.419], zoom_start= 11, )

hm_wide_2015 = HeatMap( list(zip(df["Lat"], df["Long"], df["Listings_2015"])),
                   min_opacity=0.2,
                   max_val=max_amount,
                   radius=25, blur=27, 
                   max_zoom=1, 
                 )

hmap_2015.add_child(hm_wide_2015)
```

The resulting heatmaps were saved as .html files in the [Images](https://github.com/rochiecuevas/shared_accommodations/tree/master/Images) folder.

```python
# Save file
hmap_2015.save(os.path.join('Images', 'heatmap_listings_2015.html'))
```

</p>
</details>

## Results
In 2011 and in 2016, the housing price was highest, on average, in the Financial District while the most affordable neighbourhoods to own a home were Silver Tree and Visitacion Valley (Fig 1, 2). 

<details><summary>More info</summary>
<p>

The skyrocketing price of owning a home in the Financial District can probably be attributed to the low supply of homes and a high demand, particularly with the tech boom in the area. On the other hand, Visitacion Valley features [more affordable housing options](http://sf-planning.org/sites/default/files/FileCenter/Documents/3635-chapter_6-5.pdf), but limited access to commercial and retail establishments. [Silver Terrace](https://zephyrre.com/communities/silver-terrace/) does not have large commercial establishments either.

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Assessed%20Land%20Value%20(per%20Neigborhood)%20for%202011.svg?sanitize=True)<br>
*Fig 1. Home Prices (USD) in San Francisco neighbourhoods in 2011* 

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Assessed%20Land%20Value%20(per%20Neigborhood)%20for%202016.svg)<br>
*Fig 2. Home Prices (USD) in San Francisco neighbourhoods in 2016* 

It is notable that long-term rental rates in the Financial District were not as different as the other neighbourhoods. Three neighbourhoods popped out having higher long-term rental rates in 2011 and in 2016 (Fig 3, 4): Presidio Heights, Sea Cliff, and St. Francis Wood. Sea Cliff and St. Francis Wood are classified as ["residence parks"](https://en.wikipedia.org/wiki/San_Francisco_Residence_Parks). [Presidio Heights](https://www.compass.com/neighborhood-guides/sf/presidio-heights/) took the top spot for rental rates; these could be attributed to its proximity to nature despite its closeness to the hustle and bustle of the City.

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Yearly%20Rent%20Price%20(per%20Neigborhood)%20for%202011.svg?sanitize=True)<br>
*Fig 3. Annual Rent Prices (USD) in San Francisco in 2011*

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Yearly%20Rent%20Price%20(per%20Neigborhood)%20for%202016.svg?sanitize=True)<br>
*Fig 4. Annual Rent Prices (USD) in San Francisco in 2016*

The heatmaps (Fig 5) show the most expensive places (red) to live, providing some geographic context to Fig 1–4.

|Home price|Long-term rent|
|---|---|
|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/house.png)|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/rent.png)|

*Fig 5. Comparison of Geographic Distribution of Home Prices and Long-Term Rental Rates*

The trends in price of accommodations increased from 2011 to 2016 (Fig 6). However, the rates of increases are different. The home price increases were more gradual than that for long-term rental rate. From 2012 to 2015, the increase in long-term rental rates was steep; but the increases seemed to slow down in 2015–2016.

|Home price|Long-term rent|
|---|---|
|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Yearly%20Land%20Value%20Change.svg?sanitize=True)|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Yearly%20Rent%20Price%20Change.svg?sanitize=True)|

*Fig 6. Annual Home Prices and Rent Rates (USD) in San Francisco from 2011 to 2016*

Hotel rates also went up between 2005 and 2017 (Fig 7). There was a drop in hotel rates between 2008 and 2009, but this can probably be attributed to the Recession during that time. Since 2009, however, the hotel rates grew at a rate faster then in 2005–2008. In 2015–2017, the data suggests that the increase in hotel rates is slowing down. 

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/hotel_timeseries01.svg?sanitize=True)<br>
*Fig 7. Monthly Hotel Rates from 2005 to 2017*

The slowing growth trend for both hotel and long-term rental rates *might* be an effect of the entry of peer-to-peer short-term rental options. The short-term rental rates (Fig 8) appeared to by highest in Presidio and Presidio Heights, mimicking patterns of long-term rental rates. The rates were lowest in Crocker Amazon in 2015 and 2016; but by 2017, the rates there have overtaken those of Excelsior, Treasure Island/Yerba Buena Island, and Visitacion Valley. [Crocker Amazon](https://en.wikipedia.org/wiki/Crocker-Amazon,_San_Francisco) and [Excelsior](https://en.wikipedia.org/wiki/Excelsior_District,_San_Francisco) are described as ethnically diverse residential areas, with single-family homes. [Treasure Island/Yerba Buena Island](https://sftreasureisland.org/living-treasure-island), on the other hand, feature only rental housing. In 2018, all short-term rental listings for Treasure Island have been [wiped out](https://www.sfchronicle.com/business/article/SF-short-term-rentals-transformed-as-Airbnb-12617798.php) out because the agency handling rentals does not allow vacation rentals.

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Airbnb_annual_avg.svg?sanitize=True)<br>
*Fig 8. Peer-to-Peer Short-Term Rental Rates (USD) in San Francisco Neighbourhoods*

In terms of districts (Fig 9), Western Addition and Outside Lands experienced a continuous increase in short-term rental rates. In contrast, there was a price decrease from 2016 to 2017 in Downtown, in North of Downtown, and in Southern districts. The number of listings of peer-to-peer short-term rental clearly increased from 2015 to 2017. In 2015 and 2016, Outside Lands had the fewest listings for peer-to-peer short-term property rental. By 2017, however, the district already had the same number of listings as North of Downtown. On the other hand, Western Addition had the most number of listings for the three years. 

|rates|number of listings|
|---|---|
|![Alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Airbnb_annual_avg2.svg?sanitize=True)|![Alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Airbnb_district_listings.svg?sanitize=True)|

*Fig 9. Comparison of Peer-to-Peer Short-Term Rental Rates (USD) and number of listings in San Francisco Districts*

Zooming into the neighbourhood level (Fig 10), it is clear that Mission, South of Market, and Western Addition had the most number of listings for peer-to-peer short-term rental units across the three years. On the other hand, Presidio and Treasure Island had fewest listings.

![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/Airbnb_neighbourhood_listings.svg?sanitize=True)<br>
*Fig 10. Comparison of the Number of Peer-to-Peer Short-Term Rental Listings in San Francisco Neighbourhoods*

Heatmaps (Fig 11) show the progression in the number of short-term rental listings and rates. It is interesting to note that there were two regions of particularly high rates for short-term rental properties(northern part of the City and near Twin Peaks); however, the rates spiked for most neighbourhoods in the next two years. The rates rose faster around the Downtown and North of Downtown areas than around Twin Peaks. On the other hand, there was only one hotspot for short-term rental listings in 2015. It was concentrated around the central part of the City; however, the listings quickly spread in the subsequent years, with a second concentration growing around the Downtown and South of Market areas.

|year|short-term rental rates|number of short-term listings|
|---|---|---|
|2015|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/STR_rates_2015.png)|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/STR_listings_2015.png)|
|2016|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/STR_rates_2016.png)|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/STR_listings_2016.png)|
|2017|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/STR_rates_2017.png)|![alt text](https://github.com/rochiecuevas/shared_accommodations/blob/master/Images/STR_listings_2017.png)|

*Fig 11. Geographical distribution of short-term rental rates and listings*

At this point, it is premature to conduct direct comparisons across years between short-term rental rates and long-term rents or home prices (i.e., there are only three data points for short-term rental). 

</p>
</details>

## Insights
This study originally set out to determine if the rise of peer-to-peer short-term rental have an effect on the housing market in San Francisco. Because there are only three points (based on available annual data), the impact of short-term rentals to the San Francisco housing market cannot be assessed. 

However, an unintended effect of this study was the identification of the neighbourhoods with most affordable housing (Silver Tree and Visitacion Valley), the most expensive housing (Financial District), the most expensive long-term rental rates  and short-term rental rates (both Presidio Heights), and the most affordable short-term rental rates (Visitacion Valley and Treasure Island/Yerba Buena Island in 2017).