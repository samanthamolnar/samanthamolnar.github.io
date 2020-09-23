---
layout: post
title:  "Hacking Google Trends"
date:   2017-05-2
categories: personal
---
A couple of months ago my colleague [Allie Morgan](http://allisonmorgan.github.io/) approached me about a class project.  Her goal was to analyze timeseries data of google searches.  However, the biggest problem with this is that the timeseries data you get when you query the Google API only has ~200 points.  This is hardly enough data to do any meaningful analysis.  What Google does is it takes a time period request, figures out a time step which will give ~200 points, then determines which time had the highest search value.  They then turn every data point into a percentage based on this value.  Now it is impossible to figure out what these raw values are, but it is possible to get a much longer time series of this data.  Here's what we did:

1. First you need to gather all of the small timeseries over the whole interval that you want to query.  Here is an example of the first column of the file I used:


	| 2004 | 
	|---------| 
	|2004-01-01 2004-01-08|
	|2004-01-08 2004-01-15|
	|2004-01-15 2004-01-22|


	This will give you hourly data for that week.  Notice that there is an overlapping time period for each week.  This is what will allow us to stitch together the timeseries later.

2. Query the API with each of these date ranges.  I used [pytrends](https://github.com/GeneralMills/pytrends) 

	```python
		from pytrends import request
		import pandas as pd
		import numpy as np

		dfs={} 
		for i in dates: # weekly dates file where column are years
            dfs[i]={}
            for j in dates[i]: #each week in the year
                if type(j) == float and math.isnan(j):
                    continue
                endpoints = j.split(" ")
                key = endpoints[0] + "T00 " + endpoints[1] + "T00"
                p=request.TrendReq(guser, gpas , hl='en-US')
                p.build_payload([keyword],timeframe=key)
                dfs[i][j]=p.interest_over_time()
 ```


3. Stitch together the timeseries.  Since we have an overlapping point, we can calculate the scaling ratio between two different intervals, and then scale one of those intervals to match the other.  


 ```python
    timeseries=pd.DataFrame() # the final timeseries
    for i in dates:
        for j in dates[i]:
            if type(j) == float and math.isnan(j):
                continue
            df1 = timeseries
            df2 = dfs[i][j]
            ratio = float(df2[keyword].iloc[0])/float(df1[keyword].iloc[-1]) 
            df1[keyword]=ratio*df1[keyword]
            timeseries = pd.concat([timeseries,df2])
    m=np.max(timeseries[keyword])
    timeseries[keyword]=100*timeseries[keyword]/m
```



Here is an example plot when you query "Influenza":


<figure>
        <img src="/Images/influenza_hourly.trend.png" {% if include.alt %} alt="{{ include.alt }}" {% endif %} {% if include.width %} width="{{ include.width }}" {% endif %}/>
        <figcaption> <a href="https://github.com/allisonmorgan/google_trends">Hourly Google Trend for 'Influenza'</a></figcaption>
</figure>
