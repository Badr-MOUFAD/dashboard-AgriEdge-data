# dashboard-AgriEdge-data

After performing the clustering of crop years weather data for a given region and generalizing this procedure to multiple regions, the goal now is to gather all these results in a dashboard.

This repository is meant to prepare data to be displayed in the dashboard.


# Table of content

1. <a href="#generate-moroccan-data">Generate Moroccan regions</a>
2. <a href="#fetch-preprocess-data">Fetch and preprocess Moroccan weather data</a>


# <span id="generate-moroccan-data">Generate Moroccan regions</span>

Moroccan regions, namely their locations (latitude and longitude), were generated with a spatial resolution of **0.3°**. 

According to [NASA Power project](https://power.larc.nasa.gov/), the meteorological data has a spatial resolution of **0.5°x0.5°**. I found that **0.3°** is a trade-off between resolution - visual appearance - and the number of regions.

Steps to generate data of Moroccan regions:
1. Firstly, I generated a square grid with resolution **0.3°x0.3°**
2. Secondly, I selected points that belong to a polygon that delimits Morocco. The main reason for that is to reduce the number of points.
3. Finally, using an [API](https://positionstack.com/) that maps coordinates with the name of the location, I was able to refine further the grid by eliminating the points that do not belong to Morocco.

<img src="screenshots/horizontal-steps-map.png">


# <span id="fetch-preprocess-data">Fetch and preprocess Moroccan weather data</span>