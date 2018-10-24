## Data Collection

The dataset was downloaded from the real estate database company [Zillow](https://www.zillow.com/san-francisco-ca/home-values/). It was published as a graph, with the availability to download raw data. The dataset was obtained through the download function and was named as `raw_raw.xlsx`. This dataset contains the Names of San Francisco Neighborhoods, Region Type, Property Type and Monthly Rent Rates (Nov 2010-Sep 2018).

## Getting Started

Several Python (version 3.6) modules were used for preparing the data for further analyses and visualisation:
- Pandas
- Csv
- Matplotlib.pyplot
- Seaborn
- Requests
- json
- gmaps/gmaps.datasets

```python
# Dependencies
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import csv
import requests
import json
import gmaps
import gmaps.datasets
```

## Data processing

Dataset `rent_raw.xlsx` was converted into CSV file `rent_raw.csv`. The data from the `rent_raw.csv` file is loaded into a dataframe.

```python
#Create a dataframe from the csv file
rent_df = pd.read_csv("rent_raw.csv")
rent_df.head()
```
`Monthly Rates` were converted into `Yearly Rates` and were added into a new DataFrame .

```python
#Create new DataFrame 
#Convert Monthly Rent Rates to Yearly Rent Rates
rent_short_df = pd.DataFrame({"Region Name": rent_df["Region Name"],
                              "Region Type": rent_df["Region Type"],
                              "Data Type": rent_df["Data Type"]})

rent_short_df['2010'] = rent_df.apply(lambda row: row["Nov-10"] + row["Dec-10"], axis=1)
rent_short_df['2011'] = rent_df.apply(lambda row: row["Jan-11"] + row["Feb-11"]+ row["Mar-11"]
                                      + row["Apr-11"]+ row["May-11"]+ row["Jun-11"]+ row["Jul-11"]
                                      + row["Aug-11"]+ row["Sep-11"]+ row["Oct-11"]+ row["Nov-11"] +
                                      row["Dec-11"], axis=1)
rent_short_df.head()
```

Columns `2010 ` and `2018`  do not include 12 month period and were removed; only the relevant colums were retained in the  dataframe, `rent`. 

```python
rent = rent_short_df.drop(['2010','2018'], axis=1)
```
Average Rent Rate was calculated among years 2011-2017 and added into column `Avg Rent Rate`. Column  `City` was added  to specify the locations of Neighborhoods. 

```python
rent["Avg Rent Rate"] = ""
rent["City"] = ""
for row in rent["Region Name"]:
rent["Avg Rent Rate"]=rent.mean(axis=1)
rent["City"] = "San Francisco"
rent.head()
```

Coordinates(Lat & Lng) for each Neighborhood were obtained through Geocoding.
```python
# Extracting Lat and Lng for each Neighborhood
rent["Lat"] = ""
rent["Lng"] = ""
# create a params dict
params = {"key": "AIzaSyAeEnQ_KhM7iD3A92bkFRr9lHJVg1Z6CeQ"}

# Loop through the rent dataframe and run a lat/long search for each neighborhood
for index, row in rent.iterrows():
base_url = "https://maps.googleapis.com/maps/api/geocode/json"

neighborhood = row["Region Name"]
city = row["City"]

# update address key value
params['address'] = f"{neighborhood},{city}"

# make request
lat_lng = requests.get(base_url, params=params)

# convert to json
lat_lng = lat_lng.json()

rent.loc[index, "Lat"] = lat_lng["results"][0]["geometry"]["location"]["lat"]
rent.loc[index, "Lng"] = lat_lng["results"][0]["geometry"]["location"]["lng"]
```
New DataFrame `rent` with all performed cleaning and data scopping was written as csv file `rent.csv`.

## Data Visualization

Data Visualization tools such as  - Matplotlib.pyplot , - Seaborn and -Gmaps.heatmap_layer were used to make a visual analysis.

"Average Rent Rate (per Neigborhood)" graph was created to show Neighborhood with highest and lowest Rent Rates.

```python
#Using Seaborn to create a graph that will show Average Rent Rate (per Neigborhood)
plot = plt.subplots(figsize=(30, 10))
sns.barplot(x = rent["Region Name"], y = rent["Avg Rent Rate"], palette="rocket")
plt.xticks(rotation=90)
plt.title("Average Rent Rate (per Neigborhood)", fontsize=30)
```

Data for average yearly change was used to creat a "Rent Price(by Year)" graph, which shows dynamic in Rent Price changes.

```python
#Importing csv file 
year_df = pd.read_csv("Yearly_Avg.csv")
#Creating a graph that will show yearly change in price 
sns.lineplot(x = year_df["Year"], y = year_df["Year Avg ($)"], 
marker ='D', linewidth=2, color = "purple")
plt.title("Rent Price(by Year)", fontsize=15)
```

Gmaps module was used to create a HeatMap. HeatMap was used to show the location of Neighborhoods correlated with their Average Rent Rate.

```python
#Creating a HeatMap to show Neighborhoods Rent Rates  
locations = rent[["Lat", "Lng"]]
weights = rent["Avg Rent Rate"]
fig = gmaps.figure(map_type="SATELLITE")
fig.add_layer(gmaps.heatmap_layer(locations, weights=weights, point_radius = 25))
fig
```
