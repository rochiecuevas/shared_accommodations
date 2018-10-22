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
In the `Airbnb Listings Data` folder, there are 28 csv files. To abide by the "Don't Repeat Yourself" (DRY) principle of software engineering, the files are not read as csv files one by one. Instead, the data from the different csv files are placed together as follows:
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

4. Because the dictionary has several sublevels, extracting the neighbourhoods, property types, and daily rates and putting them in separate lists required nested for-loops. This is made possible through a function called `extract`:

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
```

## Output