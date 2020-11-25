### 1.	Introduction/Business Problem
#### 1.1	Background
London is the capital and also the most popular city in United Kingdom. It is a thriving financial centre with rich cultural flavour, attracting many tourists as well as residents. Its population keeps on growing at a fast pace, and it has almost increased by a million since the year 2010. With various venues that enrich people’s living experience, London holds the title of third most populated city in Europe.  
#### 1.2 Target Audience  
Potential residents.  
#### 1.3 Problem  and Overview
Accommodation prices in central and urban London is very high, and this may be daunting to people who are looking to move into London. House prices in suburban areas of London are dramatically cheaper, and thus it maybe more appealing to specific groups of investors or people looking to move into London. Properties in the suburb are cheap, but this comes with a tradeoff of having less access and venues. In this project, I would like to examine how the living environment of each London suburbs differ, from the perspective of average house price, available venues, crime rates and unemployment rates in the area. The main goal of this project is to separate the boroughs of London into clusters of different living environments.

### 2. Methodology
Metrics used to cluster boroughs of London will include:
- Average house price
- Venue density
- Venue type diversity
- Crime rate
- Unemployment rate

Based on the above metrics, boroughs of London were categorised using K-means clustering.
Since there are only 32 sets of data (boroughs), clustering using all of the indicators at once would not provide meaningful results. Therefore, multiple clusterings were executed with different combinations of feature sets. In total, three sets of K-means clustering were executed on the following feature combinations: 1. Venue category frequency 2. employment rate and crime rate 3. Average house price and Venue density.
Venue category frequency was clustered alone as it contained several values per borough (clustered based on one-hot encoded values).
Employment rate and crime rate was clustered together as a general measure of security in the area.
Average house price and venue density was clustered together as there is a common perception that house prices in more busy areas are more expensive.
How the data were collected and processed is explained in the next section.

### 3.	Data  
The main data I will be using for this project are listed below:  
-	Table containing list of London areas  
-	London crime rates by borough    
-	London unemployment rates by borough  
-	Average house prices in London by borough  
- List of available venues by borough (From Foursquare API)

I will describe the content of each data, examples and its sources.  

#### Table containing list of London areas  
This table will be scraped from the following Wikipedia page: https://en.wikipedia.org/wiki/List_of_areas_of_London
This will be used as it is a useful table sorting different areas in London by borough. It is necesary to retain the data for individual areas and not compile them into their boroughs immediately, as venue information has to be acquired using neighbourhood coordinates. 
The scraped dataframe after cleansing (scraping and cleansing process would be in shown in appendix):

![](/images/screenshot(61).png)  

To be able to use Foursquare API data, latitude and longitude of the neighbourhoods were obtained using OSgridconverter.
OSgridconverter allows conversion of OSgridereference data to latitude and longitude. The dataframe containing latitude and longitude information is shown below (process of conversion in appendix):

![](/images/screenshot(65).png)  

Now the dataframe is ready for Foursquare API retrieval.  

#### London crime rates by borough  
This data shows crime rate in London by borough. It will be merged to the dataframe made in previous section. Crime occurence data is obtained from the following website: https://www.finder.com/uk/london-crime-statistics  
The data from this source is slightly not up to date, with the latest statistics coming from the year 2018/19, but it was the only source listing crime occurrences in London by borough. This table will be scraped by beautiful soup, and crime rates for each borough will be calculated by dividing it with borough population.
The scraped table data was added to the dataframe containing neighbourhood data (process in appendix):  

![](/images/screenshot(68).png)  

CrimeOccurence was divided by borough population to get the crime rate. Population data by borough is acquired from the following link as csv:'https://data.london.gov.uk/dataset/land-area-and-population-density-ward-and-borough'  
The dataframe with crime rate information (process in appendix):  

![](/images/screenshot(78).png)  

#### Average house prices in London  
The following page contains average house prices in London by borough: https://data.london.gov.uk/dataset/average-house-prices  
One sheet of the excel file will be downloaded as csv. Only the most recent dataset (2017, December) will be used. This data would be ultimately merged into the London area dataframe.   
Reading csv file:  

![](/images/screenshot(79).png)  

Dropping every column except for 2017 December:  

![](/images/screenshot(80).png)  

#### Area of each London Boroughs  
The area of each London Boroughs (in square miles) were used to acquire the venue density for each boroughs (total venues divided by borough area). This is to even out the size difference between boroughs. Larger boroughs will naturally have more venues, and this does not mean that borough is busier than others. This data will also be merged to the london area dataframe.  
The following page contains list of London Boroughs and their statistical information: https://en.wikipedia.org/wiki/List_of_London_boroughs  
London area dataframe after merging borough area data (process in appendix):  

![](/images/screenshot(84).png)  

#### London employment rates by borough  
Employment rate for each borough will be used as one of the indicator for security.  
This data is obtained from the following website: https://www.gmblondon.org.uk/news/16-boroughs-in-london-have-employment-rate-below-uk-average.html#:~:text=In%20Sutton%2C%2082.4%25%20of%20the,Richmond%20upon%20Thames%20with%2078.9%25.  
Like the crime occurrence data and average house prices, this data will also be slightly not up to date (2017).
Data scraped and merged into the london area dataframe (process in appendix):

![](/images/screenshot(91).png)  

#### List of available venues by borough (From Foursquare API)
Foursquare API data will be used to obtain the list of venues and categories for each neighbourhoods listed in the london area dataframe.   
The ‘explore’ endpoint will be used to obtain the list of venues. After this, the list would be reorganized so that it will become a table showing frequencies of different categories of venues.The total number of venues acquired from each borough will also be stored, and get venues per square mile (totalvenues divided by borough area) for comparison between boroughs. The frequencies of venue categories will also be displayed per borough, since the goal is to compare different boroughs, and not the specific areas within a borough.  

The final dataframe after cleansing and processing (process in appendix):  

![](/images/screenshot(123).png)  

### 3.	Analysis
The two dataframe below contains all the data required for the analysis:

![](/images/screenshot(99).png)
![](/images/screenshot(123).png)
The first dataframe contains one hot encoded venue categories by borough. The second dataframe contains other statistical information of each borough that will be used for analysis.
Each borough will clustered with K-means clustering method based on different features each run. In total there will be three clustering runs: 1. K-means clustering of boroughs based on venue category frequency.   2. K-means clustering of boroughs based on employment rate and crime rates.  3. K-means clustering of boroughs based on average house prices and venue density. K-means clustering will be used as it could group boroughs based on given features, and this will make the process of finding boroughs with specific traits easier. Clustering is also useful when visualising spatial trends of a feature, if there is any. 

#### 3.1	K-means clustering based on venue category frequency

The first dataframe will be used in this section (London_grouped). To prepare for k-means clustering I will make a dataframe containing one-hot-encoded venue category frequency data, while dropping the columns containing borough names. This dataframe will be named “london_grouped_clustering_venue_diversity”. This dataframe will initially be casted on k-means clustering with a for loop, changing the number of clusters every loop. This is done to draw a graph showing distortions of each clustering process to identify the optimal number of clusters using elbow method.  

![](/images/screenshot(124).png)  

As we can see from the graph, the rate of change in distortion seems to be fairly stable across different number of clusters. Usually the pattern we see is distortion dropping dramatically over the first few numbers of clusters (e.g 1 - 3), and further increase in number of clusters only causes minimal distortion drops. In such typical cases the optimal number of clusters will be the point where the rate-of-decrease in distortion drops significantly (according to elbow method). This graph displaying uniform rate of decrease may be indication of poor clustering results. 
8 was chosen as the decrease in distortion became less steep after exceeding the number of 8 in the above distortion graph.  

![](/images/screenshot(127).png)  

The cluster labels generated from this k-means clustering is used to create a new dataframe called “venue_diversity”, containing borough names, top 5 common venue categories in the borough. Latitude and longitude values of the borough is added onto this as well to make cluster labels on choropleth map. Implications of the results would be discussed in the results section.

#### 3.2	K-means clustering based on employment rate and crime rate

In this set of clustering I will be using the second dataframe (Londonframe). I will be filtering out the crime rate and employment rate data from londonframe to a new dataframe called ‘london_grouped_clustering_security’. Like in 3.1, I will be running k-means clustering with multiple cluster numbers in a for loop to identify what is the optimal cluster number value. Unlike venue category frequency, the distortion graph of these datasets are straightforward, with clearly identifiable elbow point at cluster number of 4. After this point decrease in distortion becomes significantly narrower, indicating that further increase in number of clusters would not benefit k-means accuracy.  
![](/images/screenshot(128).png)  
With optimal cluster number identified, k-means clustering will be re-run with this value (4). New dataframe called ‘security’ was made, containing: cluster labels, borough names, employment rate and crime rates of each borough. Borough latitude and longitudes are also added for choropleth maps.  
![](/images/screenshot(129).png)

#### 3.3	K-means clustering based on average house price and venue density.

In this set of clustering I will be using the second dataframe (Londonframe). I will be filtering out the average house price and venue density datasets to a new dataframe called ‘london_grouped_clustering’. Venue density here means how many venues are present per square mile of a borough. K-means clustering will be repeated with different ‘number of cluster’ values to draw distortion graph. As we can see from the graph, decrease in distortion becomes less steeper after the cluster number of 4. Therefore we can define number of cluster as 4 and proceed with K-means.  
![](/images/screenshot(130).png)  
After k-means clustering, new dataframe called ‘london_houseprice_venue’ is made. This contains: cluster labels, borough names, average house price and venue density of each boroughs. Borough latitude and longitudes are also added for choropleth maps.
![](/images/screenshot(142).png)


### 4.	Results
In this section I will go over the implications of the clustering results. 

#### 4.1	Venue category frequency
As shown in methodology, the boroughs were separated into 8 clusters when clustered based on venue category frequency. These clusters can be visualized by choropleth map, which would look like this:  

![](/images/screenshot(136).png)  

The code for this map is:  

![](/images/screenshot(135).png)  

We cannot see any special spatial patterns regarding venue diversity from this map. If we take a look at the dataframe venue_diversity, we can see that there isn’t much variation in venue diversity across boroughs. 

![](/images/screenshot(137).png)  

Most of the boroughs contain the following venue categories in the ‘top 5 most common venue types’: Pub, coffee shop, café, grocery store. This high degree of similarity between boroughs was probably the reason why we could not observe a distortion graph with steep drop. This also means that the clustering of boroughs based on venue diversity was not really successful, since we could not see any significant differences between clusters regarding venue diversity. 

#### 4.2 Employment rate and crime rate
As shown in methodology, the boroughs were separated into 4 clusters when clustered based on venue category frequency. These clusters can be visualized by chorolpeth map, which would look like this:  

![](/images/screenshot(139).png)  

The code for this map is:  

![](/images/screenshot(138).png)  

The mean crime rate and employment rate of each cluster is summarized in the following table:  

![](/images/screenshot(140).png)  

Clusters 0 and 2 seems to have better security in terms of employment rate and crime rate, compared to other clusters. Especially cluster 2 has the highest employment rate and lowest crime rate amongst other clusters. When we look at the map, the blue circles represent boroughs from cluster 2, while the red circles represent boroughs from cluster 0. Cluster 0 is generally spread across the North side of London, while cluster 2 is spread
across the south side of London. While in the centre there is cluster 1 solely consisted of Westminster with very high crime rate and the lowest employment rate.  
The security dataframe sorted by cluster label will look like this:  

![](/images/screenshot(141).png)

#### 4.3	Average house price and venue density
As shown in methodology, the boroughs were separated into 4 different clusters when clustered based on average house price and venue density. These cluster can be visualized by choropleth map, which would look like this:  

![](/images/screenshot(143).png)  

The code for this map is:  

![](/images/screenshot(144).png)  

The mean average house price and venue density by clusters can be seen from this dataframe:  

![](/images/screenshot(145).png)  

Cluster 2 has the lowest average house price, but it also has the lowest venue density compared to other clusters. Cluster 1 is on the polar opposite of cluster 2, with highest average house price and highest venue density among the other clusters. Cluster 0 and 3 seems to have acceptable venue density and mid-range house prices. Among these two clusters, cluster 3 has higher venue density and slightly higher house prices. 
If we look at the original dataframe sorted by cluster label,  

![](/images/screenshot(146).png)  

We can see that most of the boroughs belong in cluster 2, where the average house price and venue density is the lowest. There is only 2 boroughs in the very popular cluster 1: Westminster, Kensington and Chelsea. If we look at the choropleth map again, blue circles represents boroughs from cluster 2, and they generally seem to be situated away from the centre, while the purple (cluster 1) and yellow circles (cluster 3) are more closer to the centre of London. Therefore, it could be said that the general trend is as we get closer to the centre, the areas are more urbanized (with higher venue density) with higher house prices. 

### 5.	Discussion

Venue density, venue diversity and security (employment rate and crime rate) were used as metrics for of living environment in this project. The way the boroughs were clustered differed a lot depending on which of these features were taken into account. Clustering by venue diversity did not provide much information regarding borough-wise living environment, probably because the area was too large. Considering venue diversity per borough may have been redundant, as it is very likely that a borough will have large venue diversity due to its large size. This indicator should have been used when investigating more narrow areas such as neighborhoods or districts within a borough. Clustering by venue density and security did differentiate the boroughs more vividly. 

Preferences for high or low venue densities and house price depends on personal preference, but high employment rates and low crime rates are commonly desirable trait for residential areas. The clusters with relatively decent employment rate and crime rates were cluster number 2 (employment = 79.0%, crime = 8.1%) and 0 (employment = 73.3%, crime = 9.9%). The crime rate of these cluster is below median when compared with other clusters, and employment rate is also above median.
Cluster with high employment rate and low crime rate:

![](/images/screenshot(151).png)  

Although preference for venue density is arbitrary, urban environment often comes with higher house prices, so we will try and identify urban areas with more affordable house prices. 
Cluster that meets this requirement will be:

![](/images/screenshot(152).png)  

The boroughs that belong in the filtered clusters for both of the clustering conditions are shown below:  

![](/images/screenshot(153).png)  

The results were filtered for boroughs with low crime rate, high employment rate, high venue density and low (relatively) house prices. Boroughs with very low venue density (single digit) such as Harrow and Merton is still included, and this is probably because the clustering of venue density was done together with average house price.   

From the filtered dataframe, we can see that Islington has outstanding venue density (188.5), with average house price at 615000 GBP, which is still lower than Hammersmith and Fulham (777475 GBP) that has significantly lower venue density (87.5). Security indicator seems decent as well for Islington, with its employment rate (72.0%) and crime rate (12.6%) both roughly around the mean values (73.7% and 10.4%) of all the 32 boroughs. The next best option could be Hackney, where the venue density is much lower compared to Islington but still decent (84.6), as it is near the 90th percentile among the filtered boroughs. Traded with venue density, Hackney has cheaper average house price (530000 GBP) compared to Islington. These two boroughs can be a good place to start for people who are seeking for an urban environment with relatively affordable house prices.

Clustering has gave some useful insights in how different boroughs can be categorised in terms of certain living environment metrics. However, there are many confounds and areas of improvement.
Venue diversity information did not really come in handy when comparing large areas such as boroughs.
A more comprehensive approach could have been taken, such as separating venue frequency based on broader categorisations (e.g. entertainment, sports, parks, restaurants, office, etc) to cluster areas based on venue diversity.
In this project employment rate, crime rate, venue density and average house price were used as indicators of living environment, however it would have been better if additional datasets were included. These can be socioeconomic data, health index, quality of life measures. 


### Appendix

Specific data scraping and cleansing process will be listed here.

#### Table containing list of London areas
With BeautifulSoup, the table containing list of areas in London will be scraped from the URL (https://en.wikipedia.org/wiki/List_of_areas_of_London). 
The scraped table:

![](images/screenshot(85).png)  

This dataframe will be cleansed and its OSgridcoordinates converted to longitude & latitude so Foursquare API can be applied to retrieve venue information.

This table will then be cleaned by dropping unnecessary columns, renaming columns, removing unnecessary letters.  
Some rows contains multiple boroughs, so they will be separated and stacked on another dataframe, which will be joined back to make duplicate rows in the original dataframe.  

![](/images/screenshot(59).png)  

![](/images/screenshot(60).png)  

The London area dataframe after joining:  

![](/images/screenshot(61).png)  

OSgridreference data has to be converted to longitude&latitude (lat&lng) so Foursquare API can be used to extract venue information. OSgridconverter was used in this process.
First row was used to test the accuracy: Abbey Wood.
Enter the OSgridreference stored in the previous dataframe and get the lat&lng values.
Use reverse method to get location from lat&lng and check if it matches the original location.  

![](/images/screenshot(63).png)  

Success.  
Now apply this to all datasets (set the array as 562 instead of 566 as there were 4 datasets with missing OSgridreferences):  

![](/images/screenshot(64).png)  

This gives  

![](/images/screenshot(65).png)  


#### London crime rates by borough
With BeautifulSoup the table containing crime occurence statistics was scraped from the following URL: https://www.finder.com/uk/london-crime-statistics  
The scraped table data would look like this:  

![](/images/screenshot(66).png)  

Turned to dataframe:  

![](/images/screenshot(86).png)  

Merged to original dataframe:  

![](/images/screenshot(68).png)  

CrimeOccurence would be divided by borough population to get the crime rate. Population data by borough is acquired from the following link as csv:'https://data.london.gov.uk/dataset/land-area-and-population-density-ward-and-borough'  
A sheet from the excel file is downloaded as csv:  

![](/images/screenshot(74).png)  

Filter  necessary columns and necessary row(Year):   

![](/images/screenshot(75).png)  

![](/images/screenshot(76).png)  

Merge it into the original dataframe:  

![](/images/screenshot(77).png)  

Divide CrimeOccurence by Population to get rate:  

![](/images/screenshot(78).png) 


#### Area of each boroughs

The following page contains list of London Boroughs and their statistical information: https://en.wikipedia.org/wiki/List_of_London_boroughs  
The tables were scraped using BeautifulSoup to acquire borough area data.

dataframe binfo(table_data) contains stats for 32 boroughs of London:

![](/images/screenshot(113).png)  

Cleaning (stripping) unneeded letters:  

![](/images/screenshot(83).png)  

Merging it into the London area dataframe:  

![](/images/screenshot(84).png)  


#### London employment rate by borough

This data was scraped using BeautifulSoup, from the following website: https://www.gmblondon.org.uk/news/16-boroughs-in-london-have-employment-rate-below-uk-average.html#:~:text=In%20Sutton%2C%2082.4%25%20of%20the,Richmond%20upon%20Thames%20with%2078.9%25.

Scraping the table:  

![](/images/screenshot(87).png)  

Cleaning table (removing unnecessary rows, columns, letters and assigning new column names):  

![](/images/screenshot(88).png)  

Transposing dataframe and merging it to original dataframe:  

![](/images/screenshot(89).png)  

![](/images/screenshot(90).png)  

![](/images/screenshot(91).png)  

#### List of available venues by borough (From Foursquare API)
Foursquare API data will be used to obtain the list of venues and categories for each neighbourhoods listed in the london area dataframe.   
The ‘explore’ endpoint will be used to obtain the list of venues. After this, the list would be reorganized so that it will become a table showing frequencies of different categories of venues.The total number of venues acquired from each borough will also be stored, and get venues per square mile (totalvenues divided by borough area) for comparison between boroughs. The frequencies of venue categories will also be displayed per borough, since the goal is to compare different boroughs, and not the specific areas within a borough.  

Testing out Foursquare API (Excluded Client Secret and Client ID since its private information):  

![](/images/screenshot(93).png)  

Formatting to a Dataframe:  

![](/images/screenshot(94).png)  

![](/images/screenshot(95).png)  

Define function to only extract category name from venue.category column and applying it to dataframe:  

![](/images/screenshot(111).png)  

Afte confirming validity, function to apply the same operation to all dataset was defined. This time borough name was assigned instead of location name for every extracted venues (so venues can be sorted into boroughs with groupby later on). This function also created a dataframe showing total venues of each location:  

![](/images/screenshot(114).png)  

Outcome:  
lfvenues is the dataframe containing category information and total_venues is the dataframe containing information about how many venues are extracted from one location.  

![](/images/screenshot(115).png)  

Doing one-hot-encoding to find out venue category frequency by location:   

![](/images/screenshot(98).png)  

Meaning the category freqeuncy ratio within boroughs to get a overall category ratio by boroughs:  

![](/images/screenshot(99).png)  

Defining function to retrive most common venues and applying this to each borough. The outcome is added to the dataframe:  

![](/images/screenshot(100).png)  

Calculating total number of venues in each borough:  

![](/images/screenshot(117).png)  

Merging the total venue count per borough to the original dataframe containing various borough stats. This was divided by borough area(square mile) to get venues density:  

![](/images/screenshot(118).png)  

![](/images/screenshot(119).png)  

Merging the dataframe containing most common venue category for each borough to the same dataframe above:  

![](/images/screenshot(120).png)  

Cleaning the dataframe by dropping now unnecessary columns and duplicates:  

![](/images/screenshot(121).png)  

Adding borough latitude and longitude information so I could make cluster circles in folium later on: 

Using geopy to get latitude and longitude information from borough names.  

![](/images/screenshot(108).png)  

The final dataframe:

![](/images/screenshot(123).png)  

