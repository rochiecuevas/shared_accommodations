## Getting Started

The data sets [`home_prices.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/home_prices.csv) and  [`yearly_rent.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/yearly_rent.csv) are merged into one dataframe using the Python (version 3.6) module [Pandas](https://pandas.pydata.org/). Merging the data allows for direct comparison of trends between home prices and long-term rental rates.

```python
# Import Dependencies
import pandas as pd
```

## Data processing

The paths to the two .csv files were called to access.  The .csv files were then loaded to separate dataframes.

```python
# Create a path to the csv and read it into a Pandas DataFrame
csv_path = "../Data/home_prices.csv"
rent_path = "../Data/yearly_rent.csv"
home_df = pd.read_csv(csv_path, encoding='utf8', engine='python')
rent_df = pd.read_csv(rent_path, encoding='utf8', engine='python')
```
Because the rental rates of each year are presented in separate columns in `rent_df`, it is [melted](https://pandas.pydata.org/pandas-docs/version/0.23.4/generated/pandas.melt.html) from wide form to long form, resulting in a new dataframe designated as `melted_df`. The format of this dataframe now matches that of `home_df`.

```python
# Changing from columns to rows
melted_df = pd.melt(rent_df,id_vars=["Neighborhood"],
                    value_vars=[i for i in list(rent_df.columns.values[1:8])],
                    var_name = "Year", value_name = "Annual Rent")

# Parse "Year" values to reflect year
melted_df["Year"] = melted_df["Year"].str.replace("Rent_", "")
melted_df["Year"] = melted_df["Year"].astype(int)
```

`rent_df2` is created by merging `rent_df` and  `melted_df` so that geographic coordinates are included.

```python
#merging two data frames
rent_df2 = pd.merge(melted_df,rent_df.loc[:,["Neighborhood","City","Lat","Lng"]])
```

Column names in `home_df` are renamed to match the column names in `rent_df2`.

```python
#rename columns to merge on same names
home_df = home_df.rename(columns={'Assessor Neighborhood': 'Neighborhood', 
                                  'Assessed Land Value': 'Home Price',
                                  'Closed Roll Year': 'Year'})
```

Also, the spelling and name variants in `home_df` are made uniform with those in `rent_df2`. In instances where the names led to two entries with the same name (e.g., "Financial District North" and "Financial District South" converted to "Financial District") are grouped and the average of their home prices are calculated.

```python
#rename home prices neighbourhoods for consistency with rent neighbourhoods
home_df["Neighborhood"] = home_df["Neighborhood"].replace({"Buena Vista Park": "Buena Vista",
                                                             "Bayview Heights": "Bayview",
                                                             "Croker Amazon": "Crocker Amazon",
                                                             "Forest Hill Extension": "Forest Hill",
                                                             "Lake --The Presidio": "Lake", 
                                                             "Lake Shore": "Lakeshore", 
                                                             "Jordan Park/Laurel Heights": "Laurel Heights",
                                                             "Mission Dolores": "Mission", 
                                                             "Sea Cliff": "Seacliff",
                                                             "Financial District North": "Financial District",
                                                             "Financial District South": "Financial District"})
# Group by the same neighbourhood name and use the means
home_gb = home_df.groupby("Neighborhood").mean()
home_gb = home_gb.reset_index()
home_gb["Year"] = home_gb["Year"].astype(int)
```

A new dataframe, `merged_csv`, is created by merging `rent_df2` and `home_df` based on columns `Neighborhood` and `Year`.

```python
#merge home price and rent dataframes
merged_df = pd.merge(home_gb,rent_df2, on = ["Neighborhood", "Year"])
```

DataFrame `merged_csv` is grouped  on `Neighborhood` in order to count  mean values for `Annual Rent` and `Home Price` for each neighbourhood. Column `Year` is dropped.

```python
#Grouping DataFrame based on "Neighborhood"
merged_gb = merged_df.groupby("Neighborhood").mean()

#Dropping column
merged_gb = merged_gb.drop(['Year'], axis=1)
merged_gb = merged_gb.reset_index()
```


## Output 

`merged_df` is saved as [`hp_and_rent.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hp_and_rent.csv) and `merged_gb` as [`hp_and_rent_grouped.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hp_and_rent_grouped.csv) in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder.