# Dashboard-AgriEdge-data

After performing the clustering of crop years weather data for a given region and generalizing this procedure to multiple regions, the goal now is to gather all these results in a dashboard.

This repository is meant to prepare data to be displayed in the dashboard. Follow [this link](https://dashboard-agri-edge-morocco-wheat-weather.vercel.app/) to view a live version of it. Source code can be found in [this repository](https://github.com/Badr-MOUFAD/dashboard-agri-edge-frontend).


# Table of content

1. <a href="#generate-moroccan-data">Generate Moroccan regions</a>
2. <a href="#fetch-preprocess-data">Fetch and preprocess Moroccan weather data</a>
3. <a href="#cluster-moroccan_weather">Clustering of Moroccan weather data</a>
4. <a href="#preparing-data">Preparing the data</a>

# <span id="generate-moroccan-data">Generate Moroccan regions</span>

Moroccan regions, namely their locations (latitude and longitude), were generated with a spatial resolution of **0.3°**. 

According to [NASA Power project](https://power.larc.nasa.gov/), the meteorological data has a spatial resolution of **0.5°x0.5°**. I found that **0.3°** is a trade-off between resolution - visual appearance - and the number of regions.

Steps to generate Moroccan regions with the specified resolution:
1. Firstly, I generated a square grid with resolution **0.3°x0.3°**
2. Secondly, I selected points that belong to a polygon that delimits Morocco. The main reason for that is to reduce the number of points.
3. Finally, using an [API](https://positionstack.com/) that maps coordinates with the name of the location, I was able to refine further the grid by eliminating the points that do not belong to Morocco.

<img src="screenshots/horizontal-steps-map.png">

I gathered these regions in a *dataframe --> .csv file*. The dataframe contains a total of **705** locations where the latitude, longitude, region, and name are specified.


# <span id="fetch-preprocess-data">Fetch and preprocess Moroccan weather data</span>

Weather data was downloaded from [NASA Power project](https://power.larc.nasa.gov/) by requesting directly the server. The Preprocessing of data includes adapting the weather data to the **wheat calendar** and adding the cumulative sum of each weather variable. Further details about data preprocessing can be found in the repository [Multivariate time series clustering of weather data](https://github.com/Badr-MOUFAD/multivariate-time-series-clustering).

The weather data of each location is stored separately in .csv file. All .csv are located in the folder [`raw_data/weather_regions/`](https://github.com/Badr-MOUFAD/dashboard-AgriEdge-data/tree/master/raw_data/weather_regions)


# <span id="cluster-moroccan_weather">Clustering of Moroccan weather data</span>
### Uni and multi variate clustering

After gathering data, I perform a univariate clustering of weather data of each region according to each single weather variable, namely **Precipitation**, **GDD**, **Wind**, and **Humidity**. On the other hand, I perform multivariate clustering. This result can be summarized into a data frame with the following columns


| lat | lon |multi_nb_clusters | cumulative_GDD_score | cumulative_GDD_nb_clusters | cumulative_PRECTOT_score | cumulative_PRECTOT_nb_clusters | cumulative_WS2M_score | cumulative_WS2M_nb_clusters | cumulative_RH2M_score | cumulative_RH2M_nb_clusters |
| --- | --- | ---- | --- | --- | --- | --- | --- | --- | --- | --- |


After doing that, I stacked these data frames, one after the other, to form a summary of the clustering of the region. This can be found in `processed_data/summary_clustering.csv`.

### final clustering setup

To visualize a map of clusters, I retained the uni (according to precipitation) and multi-variate clustering for a number of clusters equal to **3**. Indeed, the analysis of the summary of the clustering suggests the dominance of precipitation as a weather variable. Similarly when computing the mean of the number of regions we find **2.63**. Therefore, I chose **3** as the number of clusters after rounding.


# <span id="preparing-data">Preparing data</span>

### Problem

When gathering this data into `csv` files, namely results of clustering and the weather of each region, I ended up with a directory with a size that exceeds **1.2 GB**`. Therefore, it was impossible to work directly with the data especially when building a dashboard since the app will take an infinite time to load.

### Solution

I resolved to host this data on a server. This will enable me to only fetch the data that I need in a particular moment and therefore reduce drastically the time needed for the app to load. 

Taken into account the form of data, the technology that I am going to use to develop the dashboard, and also the deadline to submit the work, I decided to host data in a `.json` form. In addition, `.json` file is a common way to represent data in both `JavaScript` and `Python` (`dict`).

I conducted a benchmark on the internet for some service providers to pick up the right one. I concluded that [jsonStorage](https://www.jsonstorage.net/) is suitable for my case since it offers free and unlimited storage of `.json` files. Besides, the method of fetching a `.json` is simple as it requires only providing an id that was delivered once the `.json` file was uploaded.

### Implementation

Before uploading, I divided my data into `.json` whose size does not exceeds **2 MB** (to reduce app loading time). In total, I ended up with:

- **39 x 2 = 78**  file that saves the result of clustering (uni and multivariate) of each crop year
- **705** `.json` file that holds data about centroids of clusters of each region
- **705** `.json` file that gathers weather data of each region

Then I uploaded these`.json` files and saved the corresponding ids so that to use them later for fetching them. I stored these ids in a `.json` files that can be found in [`/ready_to_use_data`]((https://github.com/Badr-MOUFAD/dashboard-AgriEdge-data/tree/master/ready_to_use_data)). Finally, I exported those`.json` files to the app and I used them to request the corresponding data.