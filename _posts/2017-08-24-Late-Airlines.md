---
layout: post
title: Late Airlines
category: Data Science
---

This will be a quick forray in to which airlines are most commonly delayed. It was inspired by me being frustrated by delays as well as my suprise as to the quality of data on the matter. All data was downloaded from here: https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time

The first 6 months of 2017 were used as these seem the most relevant and contain a healthy amount of data. Very little technical buggery is going on here, its more of a gentle example of how you can use pandas and seaborn to quickly discover useful information.

First lets import the libraries, our data, and join the lookup table so that we can can build a list of the airlines mentioned in the dataset.


```python
import pandas as pd
import glob as glob
files = glob.glob('flights/*.csv')

df = pd.concat((pd.read_csv(f) for f in files),ignore_index=True)
lookup = pd.read_csv('flights/Download_Lookup.asp')
df = df.merge(lookup,left_on='AIRLINE_ID',right_on='Code')

df.head()
```

||FL_DATE|AIRLINE_ID|CARRIER|ORIGIN_AIRPORT_ID|DEST_AIRPORT_ID|CRS_DEP_TIME|DEP_TIME|DEP_DELAY|TAXI_OUT|WHEELS_OFF|...|CANCELLED|CANCELLATION_CODE|CARRIER_DELAY|WEATHER_DELAY|NAS_DELAY|SECURITY_DELAY|LATE_AIRCRAFT_DELAY|Unnamed: 22|Code|Description|
|--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |
|0|2017-04-01|19805|AA|14107|13930|1218|1258.0|40.0|19.0|1317.0|...|0.0|NaN|0.0|0.0|15.0|0.0|40.0|NaN|19805|American Airlines Inc.: AA|
|1|2017-04-01|19805|AA|14908|14107|1002|957.0|-5.0|9.0|1006.0|...|0.0|NaN|NaN|NaN|NaN|NaN|NaN|NaN|19805|American Airlines Inc.: AA|
|2|2017-04-01|19805|AA|13830|14107|2115|2100.0|-15.0|11.0|2111.0|...|0.0|NaN|NaN|NaN|NaN|NaN|NaN|NaN|19805|American Airlines Inc.: AA|
|3|2017-04-01|19805|AA|11433|14107|1100|1053.0|-7.0|17.0|1110.0|...|0.0|NaN|NaN|NaN|NaN|NaN|NaN|NaN|19805|American Airlines Inc.: AA|
|4|2017-04-01|19805|AA|14107|14893|1335|1328.0|-7.0|18.0|1346.0|...|0.0|NaN|NaN|NaN|NaN|NaN|NaN|NaN|19805|American Airlines Inc.: AA|

 *sorry for the formatting*


DEP_DELAY is the variable we will look at first. Now we will make a list of airlines represented in the dataset.

```python
airlines = df['Description'].unique()
print airlines
```
array(['American Airlines Inc.: AA', 'United Air Lines Inc.: UA',
       'Hawaiian Airlines Inc.: HA', 'Frontier Airlines Inc.: F9',
       'Spirit Air Lines: NK', 'SkyWest Airlines Inc.: OO',
       'Virgin America: VX', 'JetBlue Airways: B6',
       'ExpressJet Airlines Inc.: EV', 'Delta Air Lines Inc.: DL',
       'Alaska Airlines Inc.: AS', 'Southwest Airlines Co.: WN'], dtype=object)


Good, that seems like most of the relevant airlines. Now we filter our dataframe by each airline and take the mean of the DEP_DELAY variable - delay in minutes, and average them.

```python
barplot_df = pd.DataFrame(columns=['airline','delay'])
data = []
for airline in airlines:
    airline_df = df[df['Description'] == airline]
    data.append([airline, airline_df['DEP_DELAY'].mean()])
import seaborn as sns
import numpy as np
barplot_df = pd.DataFrame(data = data, columns=['airline','delay'])
%matplotlib inline

ax = sns.barplot(x="airline", y="delay", data=barplot_df)
ax.set_xticklabels(ax.get_xticklabels(), rotation=90)
```

![Delays]({{ site.url }}{{ site.baseurl }}/assets/img/airline-delays-graph.png)

So I would say there are two airlines that are much better - Alaska and United. 2 or three that are much worse - Virgin and Jetblue, and many of the rest are around the same.

This is pretty much as basic as it gets. The point of this is to get better understanding of your risk of delay when picking a particular airline. Because you are going to be choosing flights at somewhat random times of the year, it doesn't seem worthwhile to inspect delay differences according to time of year. Of course 1 useful takeaway is that around a 10 minute delay is average for all airlines.

The next question we might ask is whether theres a difference between international and domestic flights.
