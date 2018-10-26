## Data Collection

The data sets `combine_updated.csv` and  `yearly_rent.csv` were previously obtained and cleaned.

## Getting Started

Two Python (version 3.6) modules were used to prepare the data for further analyses and visualisation:
- [Pandas](https://pandas.pydata.org/): to create data structures that could be processed and manipulated in Python
- [NumPy](http://www.numpy.org/): to create an array
- [CSV](https://docs.python.org/3/library/csv.html): to read csv files


```python
# Import Dependencies
import pandas as pd
import numpy as np
import csv
```

## Data processing

The path to data set was created to obtain them from github repository.  In order to read files cvs module  `pd.read_csv` was used.

```python
# Create a path to the csv and read it into a Pandas DataFrame
csv_path = "../Home Prices/combine_updated.csv"
rent_path = "../Data/yearly_rent.csv"
home_df = pd.read_csv(csv_path, encoding='utf8', engine='python')
rent_df = pd.read_csv(rent_path, encoding='utf8', engine='python')
```
In order to merge DataFrames together, the format of DataFrame  `rent_df`   was modified. New DataFrame  `melted_df`  was created and corresponds the format of  DataFrame  `home_df`.

```python
#Changing from columns to rows
my_col_names = list(rent_df.columns.values)[2:9]
melted_df = pd.melt(rent_df,id_vars=["Neighborhood"],
value_vars=my_col_names, var_name="Year",value_name="Yearly Rent Price")
```

New DataFrame `rent_df2` was created by merging  DataFrames `rent_df` and  `melted_df`  in order to include missing columns.

```python
#merging two data frames
rent_df2 = pd.merge(melted_df,rent_df.loc[:,["Neighborhood","City","Lat","Lng"]])
```

Columns were renamed to correspond with each other and unnecessary for further analysis columns were dropped.

```python
#rename columns to merge on same names
#changing the type of column
home_df = home_df.rename(columns={'Assessor Neighborhood': 'Neighborhood', 
'Closed Roll Year': 'Year'})
rent_df2["Year"] = rent_df2["Year"].astype(int)

home_df = home_df.drop(['Unnamed: 0'], axis=1)
home_df = home_df.drop(['Pct Change'], axis=1)
```

New DataFrame `merged_csv` was created by merging DataFrames `rent_df2` and  `home_df`  based on columns  `Neighborhood` and  `Year`.

```python
#merging
merged_csv = pd.merge(home_df,rent_df2, 
how='inner',
left_on=["Neighborhood","Year"],
right_on=["Neighborhood","Year"])
```

DataFrame `merged_csv` was grouped  on `Neighborhood` in order to count  mean values for  `Yearly Rent Price` and  `Assessed Land Value` for each Neighborhood. Column `Year` was dropped due to unnecessity.

```python
#Grouping DataFrame based on "Neighborhood"
merged_gb = merged_csv.groupby("Neighborhood").mean()

#Dropping column
merged_gb = merged_gb.drop(['Year'], axis=1)
```


## Output 

DataFrames were saved as  `hp_and_rent.csv`  and  `hp_and_rent_grouped.csv`  in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder.

