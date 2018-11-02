# Visualization for Long Term Rent and Housing Data


## Getting Started
The following Python (version 3.6) modules are used in preparing the data for further analyses and visualisation:
- [Pandas](https://pandas.pydata.org/): organise the data into structures that can be manipulated
- [CSV](https://docs.python.org/3/library/csv.html/): read csv files
- [Matplotlib](https://matplotlib.org/): Python 2D plotting library 
- [Seaborn](https://seaborn.pydata.org/): data visualization library based on matplotlib
- [Gmaps](https://jupyter-gmaps.readthedocs.io/en/latest/): Google Maps library

```python
#Dependencies
import pandas as pd
import csv
import matplotlib.pyplot as plt
import seaborn as sns
import gmaps
import gmaps.datasets
```

## Data visualization
The data from the [`hp_and_rent.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hp_and_rent.csv) file is loaded into a dataframe.

```python
#Importing csv file 
yearly_price_change_path = "../Data/hp_and_rent.csv"
yearly_price_change = pd.read_csv(yearly_price_change_path, encoding='utf8', engine='python')
yearly_price_change.head()
```

Data for years 2011 and 2016 are selected from the dataFrame `yearly_price_change` in order to build charts that represent the first year of available data and the last year.

Bar graphs showing Annual Rent and Home Price per neighbourhood are plotted for 2011 and saved in the [Images](https://github.com/rochiecuevas/shared_accommodations/tree/master/Images) folder. Thess code are modified to create bar graphs for 2016. 

```python
# Create a bargraph for home prices in 2011
plot2 = plt.subplots(figsize=(16, 8))
sns.barplot(x = "Neighborhood", y = "Home Price", 
            data=yearly_price_change.loc[yearly_price_change["Year"] == 2011], 
            palette="rocket")
plt.title("Home Prices (USD) for 2011")
plt.xticks(rotation=90)
plt.ylabel("Home Price (USD)")
plt.xlabel ("Neighbourhood")  
plt.tight_layout()

# Create a bargraph for rental rates in 2011
plot2 = plt.subplots(figsize=(16, 8))
sns.barplot(x = "Neighborhood", y = "Annual Rent", 
            data= yearly_price_change.loc[yearly_price_change["Year"] == 2011], 
            palette = "rocket")
plt.title("Long-Term Rental Rates (USD) for 2011")
plt.xticks(rotation=90)
plt.ylabel("Long-Term Rental Rate (USD)")
plt.xlabel ("Neighbourhood")
plt.tight_layout()
```

A pointplot (seaborn module) is generated to illustrate how annual rent rates change over the years. The graphs are saved in the [Images](https://github.com/rochiecuevas/shared_accommodations/tree/master/Images) folder. Using a similar code, it is possible to generate another pointplot for home prices.

```python
# Creating a graph that will show yearly change in rent price 
fig, ax = plt.subplots(figsize=(7, 7))

ax = sns.pointplot(x = "Year", y = "Annual Rent", data = yearly_price_change,
                   marker = 'D', linewidth = 2, color = "purple")
 
plt.title("Yearly Rent Price Change", fontsize=15)
plt.ylabel("Annual Rent (USD)") 
```

## HeatMaps

Heat Map for Neighborhoods Rent Rates  was created to show  neighborhoods with highest rent price.

```python
#Creating a HeatMap to show Neighborhoods Rent Rates  
fig = gmaps.figure()
fig = gmaps.figure(map_type="SATELLITE")
locations = yearly_price_change[["Lat", "Lng"]]
weights2 = yearly_price_change["Yearly Rent Price"]
heat_layer = gmaps.heatmap_layer(locations, weights=weights2, 
point_radius = 25, opacity = 0.7)
fig.add_layer(heat_layer) 
fig

```

Heatmaps are also generated to provide a geographic orientation to the neighbourhoods and their costs in San Francisco. This heatmap uses home prices as weights. The code can be modified to plot annual rent rates as weights on the map of the City.

```python
#Creating a HeatMap to show Neighborhoods Home Price
fig = gmaps.figure()
fig = gmaps.figure(map_type="SATELLITE")
locations = yearly_price_change[["Lat", "Lng"]]
weights = yearly_price_change["Home Price"]
heat_layer1 = gmaps.heatmap_layer(locations, weights=weights, point_radius = 25, opacity = 0.7)                                
fig.add_layer(heat_layer1) 
fig
```