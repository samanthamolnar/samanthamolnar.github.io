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
A couple of months ago my colleague [Allie Morgan](http://allisonmorgan.github.io/) approached me about a class project.  Her goal was to analyze timeseries data of google searches.  At the time of writing this, the timeseries data you get when you query the Google API only has ~200 points, where the interval changes depending on your date range.  What Google does is it takes a time period request, figures out a time step which will give ~200 points, then determines which time had the highest search value.  They then hide the raw search value by dividing every other value in the data by the maximum.  

In order to do more meaningful timeseries analysis, a longer timeseries is necessary.   
<details>
	<summary>Here's how we did that.</summary>
	
1. First you need to gather all of the small timeseries over the whole interval that you want to query.  Here is an example of the first column of the file I used:

| 2004 |
| **** |
| 2004-01-01 2004-01-08 |
| 2004-01-08 2004-01-15 |
| 2004-01-15 2004-01-22 |


Notice that there is an overlapping time period for each week.  This is what will allow us to stitch together the timeseries.
	
2. Query the API with each of these date ranges.  I used [pytrends](https://github.com/GeneralMills/pytrends) 
3. Calculate the scaling ratio of the overlapping point between two intervals.
4. Stitch together timeseries.




Here is an example plot when you query "Influenza": [Credit: Allison Morgan]


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