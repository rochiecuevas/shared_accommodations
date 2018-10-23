# Cleaning the Airbnb Listings Dataset (2015–2017)
## Data Organisation
This dataset was downloaded from [published Airbnb rates](http://insideairbnb.com/san-francisco/?neighbourhood=&filterEntireHomes=false&filterHighlyAvailable=false&filterRecentReviews=false&filterMultiListings=false) within San Francisco, from 2015 to 2017. The data in it was gathered by Murray Cox's Inside Airbnb, a non-commercial and independent set of tools that collects publicly available data from Airbnb.

The dataset is organised into separate csv files based on the date the data was extracted from Airbnb. A jupyter notebook is used to clean the data, `Airbnb_listings.ipynb`.


## Getting Started
Various Python (version 3.6) modules are used in cleaning the data. These are imported prior the start of data cleaning.
- [os](https://docs.python.org/3/library/os.html): view the contents of the folder. 
- [re](https://docs.python.org/3/library/re.html): filter through the contents of the folder and select files based on regular expressions (i.e., commonalities in file names).
- [Pandas](http://pandas.pydata.org/pandas-docs/version/0.15/tutorials.html): convert data into dataframes ([Python object organised into rows and columns](https://towardsdatascience.com/a-quick-introduction-to-the-pandas-python-library-f1b678f34673)) that can be subject to data munging.

```python
# Dependencies
import pandas as pd
import os
import re # for filtering list of files
```

## Method
In the `Airbnb Listings Data` folder, there are 28 csv files. To abide by the "Don't Repeat Yourself" (DRY) principle of software engineering, the files are not read as csv files one by one. Instead, the data from the different csv files are cleaned simultaneously as follows:
1. Place all the csv files into a list

```python
# List down csv files
files = os.listdir() # shows all the files inside the folder
r = re.compile(".*listings.csv") # filters the files to those with endings of the data files
newlist = list(filter(r.match, files)) # creates a list of csv files

# list of csv file contents
Airbnb_dflist = [pd.read_csv(file) for file in newlist] # runs pd.read_csv() for all files at the same time
```

2. Create a list of parsed file names that only include the dates of extraction.

```python
# parsing the names for the dataframes
file_names = [file_name[0:8] for file_name in newlist] # get only the extraction dates
```

3. The two lists, `file_names` and `Airbnb_dflist` are zipped together into a dictionary. __Note:__ Doing so results in a nested dictionary with several sublevels.

```python
# put two lists into a dictionary (first list has names of files; second list has the contents of the csv files)
Airbnb_df_dict = dict(zip(file_names,Airbnb_dflist))
```

4. Because the dictionary has several sublevels, extracting information requires nested for-loops that iterate through each sublevel. The user-function called `extract` uses these nested for-loops to allow us to create a list for each of the metrics of interest: neighbourhoods, property types, daily rates, and dates.

```python
# Create a function that extracts a wanted variable from the dictionary
def extract(dict_name, str): # metric of interest is what is written as string
    x = []
    for key,value in dict_name.items():
        for k,v in dict_name[key].items(): # key = extraction date, k = metric, v = series of values
            if k == str: 
                for i in v: # to extract the values of the metric from the series of metric (each item has an index)
                    x.append(i)
    return x

# Using the extract function, create lists for neighbourhoods, property types, daily rates, and dates
neighbourhoods = extract(Airbnb_df_dict, "neighbourhood_cleansed")
property_types = extract(Airbnb_df_dict, "property_type")
daily_rates = extract(Airbnb_df_dict, "price")
dates = extract(Airbnb_df_dict, "last_scraped")
```

5. The dates are parsed to provide the year and the month.

```python
# convert dates to year-month format
dates2 = []
for i in dates:
    dates2.append(i[0:7])
```

6. The different lists can now be placed into a dataframe.

```python
# create a new dataframe containing the lists of dates, neighbourhoods, property types, and daily rates
Airbnb_df = pd.DataFrame({"date": dates2,
                          "neighbourhood": neighbourhoods,
                          "property type": property_types,
                          "daily rate": daily_rates})
```

7. To make the data comparable to housing prices (which are based on annual assessed values), the daily rates should be converted to yearly rates. But because daily rates are presented as strings (with "$" and ","), these need to be converted first to floats before yearly rates are calculated.

```python
# Convert the price to annual rate from daily rate
daily_rate = Airbnb_df["daily rate"].str.replace("$","") # data is string
daily_rate = daily_rate.str.replace(",","") # data is still string
daily_rate = daily_rate.astype(float)
Airbnb_df["annual rate"] = daily_rate * 365 # calculate the yearly rate
```

## Output

The dataframe can be viewed.

```python
# view file 
Airbnb_df.head()
```

The first five rows in the `Airbnb_df` looks like this:

||date|neighbourhood|property type|daily rate|annual rate|
|---|---|---|---|---|---|
|0|2016-10|Seacliff|House|$105.00|38325.0|
|1|2016-10|Seacliff|House|$300.00|109500.0|
|2|2016-10|Seacliff|Apartment|$175.00|63875.0|
|3|2016-10|Seacliff|House|$90.00|32850.0|
|4|2016-10|Seacliff|Condominium|$400.00|146000.0|

The dataframe is saved as the [`Airbnb_listings.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/Airbnb_listings.csv) file in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder, ready for data analyses and visualisation.

```python
# save as csv in the Data folder
Airbnb_df.to_csv("../Data/Airbnb_listings.csv", sep = ",", encoding = "utf-8")
```