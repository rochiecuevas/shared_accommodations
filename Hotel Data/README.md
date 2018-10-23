# Pre-processing of hotel data (2004–2018)

## Data Collection
The dataset was extracted from the [SF City Performance Scorecard](https://sfgov.org/scorecards/tourism) for tourism. It is published as a [Tableau](https://www.tableau.com/about) graph. The dataset was obtained through the download function of Tableau and was named as `hotel_indicators.csv`. This dataset contains the Date, the Seasonal Adjustment, the Average Daily Rate, the Hotel Occupancy Rate, and the Revenue per Available Room.

## Getting Started
Two Python (version 3.6) modules are used in preparing the data for further analyses and visualisation:
- [Pandas](https://pandas.pydata.org/): organise the data into structures that can be manipulated
- [dateutil](https://pypi.org/project/python-dateutil/): the `parser` function is used to convert date from the 'bb-dd-yyyy hh:mm' format (where bb denotes month) to  'yyyy-bb'.

```python
# Dependencies
import pandas as pd
from dateutil import parser # to convert date strings to datetime format
```

## Data processing
The data from the `hotel_indicators.csv` file is loaded into a dataframe.

```python
# Create a dataframe from the csv file
hotel_df = pd.read_csv("hotel_indicators.csv")
```

The resulting dataframe, `hotel_df`, contains columns that are not needed in the analyses; only the relevant ones are retained in the next dataframe, `hotel_df2`.

```python
# Choose only Date, Average Daily Rate, and Hotel Occupancy
hotel_df2 = hotel_df[["Date", "Average Daily Rate", "Hotel Occupancy"]]
```

The `Date` column contains the date and the time. Only the year and the month will be used in subsequent analyses (to stay consistent with the date format in the other datasets); hence, the dates are reformatted as follows:
1. the forward slashes are converted to dashes

```python
periods = hotel_df2["Date"].str.replace("/","-")
```

2. the time stamps are excluded from the dates

```python
periods = [date[0:10] for date in periods] # excludes the time stamps 
```

3. the dates are converted from 'bb-dd-yyyy' to 'yyyy-bb' using `parser`.

```python
periods = [parser.parse(date).strftime('%Y-%m') for date in periods]
```

The reformatted dates are added to the `hotel_df2` dataframe while the original `Date` column is dropped from the dataframe.

```python
# Insert the new date format into the dataframe and drop the original Date column
hotel_df2 = hotel_df2.drop(["Date"], axis = 1)
hotel_df2["Date"] = periods
```

## Output
The first five rows of the resulting `hotel_df2` dataframe appears as such:

||Average Daily Rate|Hotel Occupancy|Date|
|---|---|---|---|
|0|141.6038|0.738484|2004-07|
|1|143.1187|0.719571|2004-08|
|2|137.4212|0.750757|2004-09|
|3|141.5987|0.778466|2004-10|
|4|148.4632|0.682347|2004-11|

The dataframe is then saved as the [`hotel_rates.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hotel_rates.csv) file in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder.

```python
# Save the file in the Data folder, ready for analyses and visualisation
hotel_df2.to_csv("../Data/hotel_rates.csv", sep = ",", encoding = "utf-8")
```