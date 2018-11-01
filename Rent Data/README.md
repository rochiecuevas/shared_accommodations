## Data Collection

The dataset was downloaded from the real estate database company [Zillow](https://www.zillow.com/san-francisco-ca/home-values/). It was published as a graph, with the availability to download raw data. The dataset was obtained through the download function and was saved as the [`rent_raw.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/rent_raw.csv). This dataset contains the Names of San Francisco Neighborhoods, Region Type, Property Type and Monthly Rent Rates (Nov 2010-Sep 2018).

## Getting Started

Two Python (version 3.6) modules were used to prepare the data for further analyses and visualisation:
- [Pandas](https://pandas.pydata.org/): to create data structures that could be processed and manipulated in Python
- [NumPy](http://www.numpy.org/): to create an array

```python
# Dependencies
import pandas as pd
import numpy as np
```

## Data cleaning

The data from the `rent_raw.csv` file is loaded into a dataframe.

```python
#Create a dataframe from the csv file
rent_df = pd.read_csv("rent_raw.csv")
rent_df.head()
```

The columns named `Region Type` and `Data Type` are not relevant to the analyses and are dropped from the `rent_df` dataframe.

```python
rent_df = rent_df.drop(["Region Type", "Data Type"], axis = 1)
```

To stay consistent with the other csv files in the repository, the `Region Name` is renamed as `Neighborhood`.

```python
# Rename the columns
rent_df = rent_df.rename(columns = {"Region Name": "Neighborhood"})
```

The neighbourhoods are extracted from the dataframe as series.

```python
# Get a series of neighbourhoods
neighborhood = rent_df["Neighborhood"]
```

To prevent repetitive code in performing the maths, a user-defined function is used. It calculates the totals per neighbourhood, per year. This function, called `totals`, filters through the columns to select the last two digits of the year and then conducts a row-wise calculation of sums to get the yearly rate per neighbourhood.

```python
# Create a function that returns the sum per row per year

def totals(df,str): # where str is the last two digits of the year
    year = df.filter(regex = str, axis = 1) # filter the columns based on the str
    
    return year.sum(axis = 1) # get the sum per row
```

The dataframe has monthly rates for seven complete years (i.e., 12 months per year). The last two digits of the years are put into a list and converted into a string. The floats are converted to string because the `totals` function recognises the year in string format. Two years, 2010 and 2018, have incomplete data; hence, the data from these years are dropped. 

```python
# Create a list of years (with data for 12 months)
year_list = list(np.arange(11,18)) # creates a list of floats covering the year range of rent_df
year_list_str = [str(item) for item in year_list] # converts numbers to string
    
year_list_str
```

To get calculate the yearly rent rates, the function `totals` is iterated through the list of years, generating an array.

```python
# Create an array of yearly rates per neighbourhood using the totals function
yearly_rate = [totals(rent_df,year) for year in year_list_str] 
```

The dataframe to be generated needs to have "Neighborhood" and the years (2011–2017) as the keys and the yearly rates as the values. To create a list of keys, the prefix "20" was added to each item in the `year_list_str` list. Then, "Neighborhood" was added at the 0th position in the list of keys.

```python
# Create a list of keys by adding "20" to the last two digits of the year
keys = year_list_str
keys = ["Rent_20" + key for key in keys]

# Insert Neighbourhood as a key
keys.insert(0,"Neighborhood")
```

To create a list of values, the list of neighbourhoods is added to the array of yearly rates.

```python
# Create a list of values by adding the neighbourhood series to the yearly_rate array
values = yearly_rate
values.insert(0,neighborhood)
```

The keys and values lists are then zipped into a dictionary and made into a dataframe called `year_rent_df`.

```python
# Create a dataframe containing the yearly rates from 2011 to 2017 for the 62 neighbourhoods in SF 
year_rent_df = pd.DataFrame(dict(zip(keys, values)))
```

`Avg Price Per Year` column was created and calculated among years 2011-2017 and added into DataFrame. Column `City` was added  to specify the locations of Neighborhoods. 

```python
#Calculating Avg Rent Price Per Year 
#and adding column 'City' to specify the location of neighborhood (San Francisco)

year_rent_df["City"] = "San Francisco"

for row in year_rent_df["Neighborhood"]:
    year_rent_df["Avg Rent"] = round(year_rent_df.mean(axis = 1), 2)
```

## Adding geolocations for each SF district

New dependencies were imported.
```python
#Importing dependencies to make a request for Lat and Lng
import requests
import json
from config import api_key
```
Geocoding was used  to obtain Coordinates(Lat & Lng) for each Neighborhood. 
API key is not provided in this repo due to safety purposes. 

```python
# create a params 
params = {"key": api_key}

# Loop through the year_rent_df and run a lat/long search for each neighborhood
for index, row in year_rent_df.iterrows():
base_url = "https://maps.googleapis.com/maps/api/geocode/json"

neighborhood = row["Neighborhood"]
city = row["City"]

# update address key value
params['address'] = f"{neighborhood},{city}"

# make request
lat_lng = requests.get(base_url, params=params)

# convert to json
lat_lng = lat_lng.json()
#inserting coordinates to assigned columns
year_rent_df.loc[index, "Lat"] = lat_lng["results"][0]["geometry"]["location"]["lat"]
year_rent_df.loc[index, "Lng"] = lat_lng["results"][0]["geometry"]["location"]["lng"]
```

## Output 
The first five lines of the dataframe `year_rent_df` looks like this:

||Neighborhood|2011|2012|2013|2014|2015|2016|2017|Avg Price Per Year|City|Lat|Lng| 
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|Bayview|30723|28821|30433|35338|42870|45681|45747|37087.571429|San Francisco|37.7304|-122.384|
|1|Bernal Heights|34471|35739|38924|43654|53977|54833|53741|45048.428571|San Francisco|37.7389|-122.415|
|2|Buena Vista|42407|45678|49364|53889|61646|65690|61917|54370.142857|San Francisco|37.8065|-122.421|
|3|Corona Heights|41051|44269|48263|52768|61781|64072|59849|53150.428571|San Francisco|37.7618|-122.443|
|4|Cow Hollow|52856|52816|56455|62256|75947|78557|71952|64405.571429|San Francisco|37.798|-122.44|

This dataframe can now be saved as `yearly_rent.csv` in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder.
