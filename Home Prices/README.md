# Cleaning the Home Prices dataset
This dataset was downloaded from [DataSF](https://data.sfgov.org/Housing-and-Buildings/Assessor-Historical-Secured-Property-Tax-Rolls/wv5m-vpq2) as a csv file named `Assessor_Historical_Secured_Property_Tax_Rolls.csv`. It contains many columns but only the following are relevant for this project: "Closed Roll Year", "Assessor Neighborhood", and "Assessed Land Value". Assessed land value is treated as the home price since this is the ['valuation of a residence for tax purposes'](https://www.investopedia.com/terms/a/assessedvalue.asp).

__Note:__ This csv file is not uploaded because Github limits file sizes to [100 MB](https://help.github.com/articles/what-is-my-disk-quota/).

## Getting Started
Two Python (version 3.6) modules are used in processing the data:
- Pandas
- datetime

```python
# Import Dependencies
import pandas as pd
import datetime
```

The csv file is loaded into a dataframe.

```python
csv_path = "Assessor_Historical_Secured_Property_Tax_Rolls.csv"
home_df = pd.read_csv(csv_path, encoding='utf-8')
```

## Data cleaning
First, only the relevant columns were retained in the dataframe.

```python
# Create a new dataframe that contains only the relevant columns
home_df2 = home_df[["Closed Roll Year", "Assessor Neighborhood", "Assessed Land Value"]]
```

Second, the data was grouped based on year and neighbourhood.

```python
# Group the data by neighbourhood
home_gb = home_df2.groupby(["Closed Roll Year", "Assessor Neighborhood"])
home_gb_mean = home_gb.mean()
home_gb2 = home_gb_mean.reset_index() # index (year) is treated as a column
```

"Closed Roll Year" is a column containing floats. The values need to be converted to integers (to get rid of the decimal place) and then to the datetime format.

```python
# Convert Closed Roll Year values to integers
home_gb2["Closed Roll Year"].astype(int)

# Convert integers to year and put the data into a new column
home_gb2["Closed Roll Year 2"] = pd.to_datetime(home_gb2["Closed Roll Year"].astype(int), 
                                                format = '%Y', errors = 'coerce').dt.year

# Drop the original Closed Roll Year column and replace with the new one.
home_gb2 = home_gb2.drop(["Closed Roll Year"], axis = 1)
home_gb2 = home_gb2.rename(columns = {"Closed Roll Year 2": "Closed Roll Year"})
```

## Output
The first five rows of the resulting dataframe looks like this:

||Assessor Neighborhood|Assessed Land Value|Closed Roll Year|
|---|---|---|---|
|0|Alamo Square|356275.88|2007|
|1|Anza Vista|249759.91|2007|
|2|Balboa Terrace|308412.95|2007|
|3|Bayview|181096.93|2007|
|4|Bayview Heights|128157.54|2007|

It is saved as a csv file in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder, ready for analyses.

```python
# Save file as a csv file
home_gb2.to_csv("combine_updated.csv")
```