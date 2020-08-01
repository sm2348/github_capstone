Data
The data I will be using for this project are listed below:
-	Table containing list of London areas
-	London crime rates by borough
- London population by borough
- Area of each London boroughs
-	London unemployment rates by borough
-	Average house prices in London by borough
- Foursquare API data

I will describe the content of each data, examples and its sources.

Table containing list of London areas
This table will be scraped from the following Wikipedia page: https://en.wikipedia.org/wiki/List_of_areas_of_London
This will be used as it is a useful table sorting different areas in London by borough. The scraping process is shown below
With BeautifulSoup, the table is scraped from the URL and originally would look like the one in the image below:
![](/images/スクリーンショット (85).png)
This table will then be cleaned by dropping unnecessary columns, renaming columns, removing unnecessary letters.
Some rows contains multiple boroughs, so they will be separated and stacked on another dataframe, which will be joined back to make duplicate rows in the original dataframe.
![](/images/スクリーンショット (59).png)
![](/images/スクリーンショット (60).png)
The original dataframe after joining:
![](/images/スクリーンショット (61).png)
Borough column will be dropped.

To be able to use Foursquare API data, I will obtain Latitude and Longitude of the locaiton through OSgridconverter.
I will test out whether it works or not by using the data from first row: Abbey Wood.
Enter the OSgridreference stored in the previous dataframe and get the lat,lng values.
Use reverse method to get location from lat lng and check if it matches the original location.
![](/images/スクリーンショット (63).png)
Success.
Now apply this to all datasets (set the array as 562 instead of 566 as there were 4 datasets with missing OSgridreferences):
![](/images/スクリーンショット (64).png)
This gives
![](/images/スクリーンショット (65).png)
Now the dataframe is ready for Foursquare API retrieval.
But before that I will add on more data to this dataframe.

London crime rates by borough
This is a data showing crime rate in London by borough. Will ultimately be merged to the original dataframe shown in the beginning of this document. Obtained from the following website: https://www.finder.com/uk/london-crime-statistics
The data from this source is slightly not up to date, with the latest statistics coming from the year 2018/19, 
but it was the only source I could find that sorted crime occurrences in London by borough. This table will be scraped by beautiful soup, and crime rates
for each borough will be calculated by dividing it with borough population.
The scraped table data would look like this:
![](/images/スクリーンショット (66).png)
Turned to dataframe:
![](/images/スクリーンショット (86).png)
Merged to original dataframe:
![](/images/スクリーンショット (68).png)

CrimeOccurence would be divided by borough population to get the crime rate. Population data by borough is acquired from the following link as      csv:'https://data.london.gov.uk/dataset/land-area-and-population-density-ward-and-borough'
A sheet from the excel file is downloaded as csv, and it looks like this:
![](/images/スクリーンショット (74).png)
Filter  necessary columns and necessary row(Year): 
![](/images/スクリーンショット (75).png)
![](/images/スクリーンショット (76).png)
Merge it into the original dataframe:
![](/images/スクリーンショット (77).png)
Divide crimeOccurence by Population to get rate:
![](/images/スクリーンショット (78).png)

Average house prices in London
The following page contains average house prices in London by borough: https://data.london.gov.uk/dataset/average-house-prices
One sheet of the excel file will be downloaded as csv. The most dataset is from 2017 December, so I will be dropping every column except for that. This data would be ultimately merged into the original dataframe.
Average house prices will not be used for the clustering process, but it is important data necessary for 
discussion and making a conclusion about which boroughs are potentially worth investing in. 
Reading csv file:
![](/images/スクリーンショット (79).png)
Dropping every column except for 2017 December:
![](/images/スクリーンショット (80).png)

Area of each London Boroughs
I will be using the area of each London Boroughs (in square miles) to acquire venue density for each boroughs (total venues/borough area). This is to even out the size difference between boroughs. Larger boroughs will naturally have more venues, and this does not mean that borough has better living environment than other boroughs. This data will ultimately be merged to the original dataframe.
The following page contains list of London Boroughs and their statistical information: https://en.wikipedia.org/wiki/List_of_London_boroughs
I am going to scrape the table to acquire borough areas. Information about 32 london boroughs were on one table and information about city of london (which I included into this project but is not a borough) was on another, so I scraped them in separate tables and merged later on:
![](/images/スクリーンショット (81).png)
Dropped unneeded columns and joined the borough and city tables together:
![](/images/スクリーンショット (82).png)
Cleaning (stripping) unneeded letters:
![](/images/スクリーンショット (83).png)
Merging it into the original dataframe:
![](/images/スクリーンショット (84).png)

London employment rates by borough
This will ultimately be merged to the original dataframe, showing employment rate for each borough as a indicator for living environment.
This data is obtained from the following website: https://www.gmblondon.org.uk/news/16-boroughs-in-london-have-employment-rate-below-uk-average.html#:~:text=In%20Sutton%2C%2082.4%25%20of%20the,Richmond%20upon%20Thames%20with%2078.9%25.
I will be scraping the data from the table in this website.
Like the crime occurrence data and average house prices, this data will also be slightly not up to date (2017).
Scraping the table:
![](/images/スクリーンショット (87).png)
Cleaning table (removing unnecessary rows, columns, letters and assigning new column names):
![](/images/スクリーンショット (88).png)
Transposing dataframe and merging it to original dataframe:
![](/images/スクリーンショット (89).png)
![](/images/スクリーンショット (90).png)
![](/images/スクリーンショット (91).png)

Foursquare API data
Foursquare API data will be used to obtain the list of venues and categories for each London areas listed in the table scraped from above Wikipedia page. 
The ‘explore’ endpoint will be used to obtain the list of venues. After this, the list would be reorganized so that it will become a table showing
frequencies of different categories of venues. 



