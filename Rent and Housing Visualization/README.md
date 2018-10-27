# Visualization for Long Term Rent and Housing Data


## Getting Started
Python (version 3.6) modules are used in preparing the data for further analyses and visualisation:
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
The data from the `hp_and_rent.csv.csv` file is loaded into a dataframe.

```python
#Importing csv file 
yearly_price_change_path = "../Data/hp_and_rent.csv"
yearly_price_change = pd.read_csv(yearly_price_change_path, encoding='utf8', engine='python')
yearly_price_change.head()
```

Data for years 2011 and 2016 was extracted from original DataFrame  `yearly_price_change` in order to build charts.

```python
rent_year2011 = yearly_price_change.loc[yearly_price_change['Year'].isin(['2011'])]
rent_year2016 = yearly_price_change.loc[yearly_price_change['Year'].isin(['2016'])]
```

Chart `Assessed Land Value (per Neigborhood) for 2011` is showing Assessed Land Value per each Neighborhood in 2011. Image was saved to folder  [Images](.https://github.com/rochiecuevas/shared_accommodations/tree/master/Images)


```python
# Draw a nested barplot to show survival for class and sex
plot2 = plt.subplots(figsize=(30, 10))
sns.barplot(x="Neighborhood", y="Assessed Land Value", data=rent_year2011, palette="rocket")
plt.title("Assessed Land Value (per Neigborhood) for 2011 ", fontsize=30)
plt.xticks(rotation=90)
plt.savefig("../Images/Assessed Land Value (per Neigborhood) for 2011.svg")
plt.show()   
```
Chart `Assessed Land Value (per Neigborhood) for 2016` is showing Assessed Land Value per each Neighborhood in 2016. Image was saved to folder  [Images](.https://github.com/rochiecuevas/shared_accommodations/tree/master/Images)

```python
# Draw a nested barplot to show survival for class and sex
plot2 = plt.subplots(figsize=(30, 10))
sns.barplot(x="Neighborhood", y="Assessed Land Value", data=rent_year2016, palette="Wistia_r")
plt.title("Assessed Land Value (per Neigborhood) for 2016 ", fontsize=30)
plt.xticks(rotation=90)
plt.savefig("../Images/Assessed Land Value (per Neigborhood) for 2016.svg")
plt.show()    
```

Chart `Yearly Rent Price (per Neigborhood) for 2011` is showing Yearly Rent Price per each Neighborhood in 2011. Image was saved to folder  [Images](.https://github.com/rochiecuevas/shared_accommodations/tree/master/Images)

```python
# Draw a nested barplot to show survival for class and sex
plot2 = plt.subplots(figsize=(30, 10))
sns.barplot(x="Neighborhood", y="Yearly Rent Price", data=rent_year2011, palette="rocket")
plt.title("Yearly Rent Price (per Neigborhood) for 2011 ", fontsize=30)
plt.xticks(rotation=90)
plt.savefig("../Images/Yearly Rent Price (per Neigborhood) for 2011.svg")
plt.show()     
```

Chart `Yearly Rent Price (per Neigborhood) for 2016` is showing Yearly Rent Price per each Neighborhood in 2016. Image was saved to folder  [Images](.https://github.com/rochiecuevas/shared_accommodations/tree/master/Images)

```python
# Draw a nested barplot to show survival for class and sex
plot2 = plt.subplots(figsize=(30, 10))
sns.barplot(x="Neighborhood", y="Yearly Rent Price", data=rent_year2016, palette="Wistia_r")
plt.title("Yearly Rent Price (per Neigborhood) for 2016 ", fontsize=30)
plt.xticks(rotation=90)
plt.savefig("../Images/Yearly Rent Price (per Neigborhood) for 2016.svg")
plt.show()    
```

Chart  `"Yearly Rent Price Change"` is showing Yearly Rent Price change from 2011 to 2016. Image was saved to folder  [Images](.https://github.com/rochiecuevas/shared_accommodations/tree/master/Images)

```python

#Creating a graph that will show yearly change in  rent price 
fig, ax = plt.subplots(figsize=(7, 7))
ax.set(yscale="log")
sns.lineplot(x = yearly_price_change["Year"], y = yearly_price_change["Yearly Rent Price"], 
marker ='D', linewidth=2, color = "purple")  
plt.title("Yearly Rent Price Change", fontsize=15)
plt.savefig("../Images/Yearly Rent Price Change.svg")
plt.show()  
```
Chart  `"Yearly Land Value Change"` is showing Land Value change from 2011 to 2016. Image was saved to folder  [Images](.https://github.com/rochiecuevas/shared_accommodations/tree/master/Images)

```python

#Creating a graph that will show yearly change in Land Value
fig, ax = plt.subplots(figsize=(7, 7))
ax.set(yscale="log")
sns.lineplot(x = yearly_price_change["Year"], y = yearly_price_change["Assessed Land Value"], 
marker ='D', linewidth=2, color = "Gold")
plt.title("Yearly Land Value Change", fontsize=15)
plt.savefig("../Images/Yearly Land Value Change.svg")
plt.show()  
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

Heat Map for Neighborhoods Assessed Land Value  was created to show  neighborhoods with highest housing price.

```python

fig = gmaps.figure()
fig = gmaps.figure(map_type="SATELLITE")
locations = yearly_price_change[["Lat", "Lng"]]
weights = yearly_price_change["Assessed Land Value"]
heat_layer1 = gmaps.heatmap_layer(locations, weights=weights, point_radius = 25, opacity = 0.7)                                
fig.add_layer(heat_layer1) 
fig

```

Two Heat Maps were layered to each other to show most expensive neighborhoods.

```python

fig = gmaps.figure()
fig = gmaps.figure(map_type="SATELLITE")
locations = yearly_price_change[["Lat", "Lng"]]
weights = yearly_price_change["Assessed Land Value"]
heat_layer1 = gmaps.heatmap_layer(locations, weights=weights, point_radius = 25, opacity = 0.7)                                
fig.add_layer(heat_layer1) 
fig

```
