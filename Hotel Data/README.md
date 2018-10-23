# Pre-processing of hotel data (2004–2018)

## Data Collection
The dataset was extracted from the [SF City Performance Scorecard](https://sfgov.org/scorecards/tourism) for tourism. It is published as a [Tableau](https://www.tableau.com/about) graph. The dataset was obtained through the download function of Tableau and was named as `hotel_indicators.csv`. This dataset contains the Date, the Seasonal Adjustment, the Average Daily Rate, the Hotel Occupancy Rate, and the Revenue per Available Room.

## Getting Started
Two Python (version 3.6) modules are used in preparing the data for further analyses and visualisation:
- [Pandas](https://pandas.pydata.org/): organise the data into structures that can be manipulated
- [dateutil](https://pypi.org/project/python-dateutil/): the `parser` function is used to convert date from the 'bb-dd-yyyy hh:mm' format (where bb denotes month) to  'yyyy-bb'.

```ruby
# Dependencies
import pandas as pd
from dateutil import parser # to convert date strings to datetime format
```

## Data processing


## Output