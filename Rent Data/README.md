## Data Collection

The dataset was downloaded from the real estate database company [Zillow](https://www.zillow.com/san-francisco-ca/home-values/). It was published as a graph, with the availability to download raw data. The dataset was obtained through the download function and was saved as the [`rent_raw.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/rent_raw.csv). This dataset contains the Names of San Francisco Neighborhoods, Region Type, Property Type and Monthly Rent Rates (Nov 2010-Sep 2018).

## Getting Started

Two Python (version 3.6) modules were used to prepare the data for further analyses and visualisation:
- Pandas
- Numpy

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
yearly_rate = [] # leads to a list of series

for year in year_list_str:
    yearly_rate.append(totals(rent_df,year))
```

The yearly rates and the neighbourhoods are then organised into the `year_rent` dataframe.

```python
# Create a dictionary
year_rent = pd.DataFrame(dict({"Neighborhood": neighborhood,
                               "2011": yearly_rate[0],
                               "2012": yearly_rate[1],
                               "2013": yearly_rate[2],
                               "2014": yearly_rate[3],
                               "2015": yearly_rate[4],
                               "2016": yearly_rate[5],
                               "2017": yearly_rate[6]}))
```

## Output
The first five lines of the dataframe `year_rent` looks like this:

||Neighborhood|2011|2012|2013|2014|2015|2016|2017|
|---|---|---|---|---|---|---|---|---|
|0|Bayview|30723|28821|30433|35338|42870|45681|45747|
|1|Bernal Heights|34471|35739|38924|43654|53977|54833|53741|
|2|Buena Vista|42407|45678|49364|53889|61646|65690|61917|
|3|Corona Heights|41051|44269|48263|52768|61781|64072|59849|
|4|Cow Hollow|52856|52816|56455|62256|75947|78557|71952|

This dataframe can now be saved as `yearly_rent.csv` in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder.