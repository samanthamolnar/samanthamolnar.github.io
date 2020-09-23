---
layout: page
title: Projects
permalink: /projects/
---
***
***
## SDGE Dashboard - August 2019
In this project I worked with a team of [NRELians](https://www.nrel.gov/workingwithus/partners/partnerships-advanced-metering-sdge.html) to analyze and visualize time series data from a distribution system under the purview of San Diego Gas & Electric (SDGE).  The data comes from Advanced Metering Infrastructure (AMI) which is the collection of voltage and power data from meters on the distribution system such as your house or a school.  My specific role in this project was to design and create a prototype dashboard.  

***
***

## Solar Eclipse - August 2017
While working at NREL, I created visualizations for the [Eclipse](https://www.nrel.gov/news/press/2018/nrel-researchers-measure-impact-of-eclipse-on-electrical-grid.html) project.  The goal of this project was to understand the impact the solar eclipse might have on power flows and system stability.  I used R to create the visualization and it was a lot of fun to use data from NREL and dabble with NASA data for the shadow.
<table><tr>
<td><iframe src="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2Fnationalrenewableenergylab%2Fvideos%2F10156406035619897%2F&show_text=0&width=560" width="560" height="315" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allowFullScreen="true"></iframe> </td>
<td><img src="/Images/me_eclipse.png" max-width="70%" max-height="70%"/></td>
</tr></table>

***
***

## Hacking Google Trends - May 2017
A couple of months ago my colleague [Allie Morgan](http://allisonmorgan.github.io/) approached me about a class project.  Her goal was to analyze timeseries data of google searches.  However, the biggest problem with this is that the timeseries data you get when you query the Google API only has ~200 points.  This is hardly enough data to do any meaningful analysis.  What Google does is it takes a time period request, figures out a time step which will give ~200 points, then determines which time had the highest search value.  They then turn every data point into a percentage based on this value.  Now it is impossible to figure out what these raw values are, but it is possible to get a much longer time series of this data.  



<details>
	<summary>Here's how we did it</summary>
	
1. First you need to gather all of the small timeseries over the whole interval that you want to query.  Here is an example of the first column of the file I used:

| 2004 |
| **** |
| 2004-01-01 2004-01-08 |
| 2004-01-08 2004-01-15 |
| 2004-01-15 2004-01-22 |



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

</details>

***
***

## HPC Midterm -- Fall 2016
For my high performance computing class we all had to implement a parallel version of [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).  Here's a little animation of mine.
![Aw :(](/Images/cgol.gif)