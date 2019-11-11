# City`s Pedestrian Accessibility 

## 1. Abstract

In this study we try to analyze walkability of the city for children from age 10 to 16.  We use a number of data sources and network analysis for building accessibility map of the selected city. With K nearest neighbor technique we perform clustering to select districts  with different walkability scores. We analyze this clutters in terms of walkability scores and POIs density and determine areas which need to be improved in terms of walking accessibility. 

### Keywords

 *Accessibility   Clustering analysis Network analysis Urban Planning* 

## 2. Introduction

Walking is the most reliable, easy and healthy way to travel. Yet today the number of children walking to school is in decline. This has damaging consequences for our children’s health, safety and wellbeing.
Over 70 per cent of today’s parents walked to school when they were children but less than half of children walk to school today. Meanwhile, the number of children being driven to school is growing, with an increasingly negative impact on congestion, health and communities [1].

Walking is an easy, free and healthy way for children to get to school or other activities such as libraries, cultural and hobby centers, playgrounds e. t. c. Walking provides a lot of benefits for every one. 

What we mean under pedestrian accessibility? For us it means the ability to reach particular everyday - life points of interest (POI) in a reasonable time. Under reasonable time we mean time that children need to spend to walk 1300 meters. This distance is recommended as reasonable for selected age range. [2]    Common POIs for children from 10 to 16 years are: schools, hobby and leisure time facilities, libraries, sport facilities and outdoor playgrounds. 

 The objectives of this study therefore are:   (1) Analyze current city`s infrastructure for children in terms of density population and density of POIs. (2) Build network of walkable routes. (3) Define clusters 

With discussions on related literatures, this section has introduced the purpose and scope of this paper. **Study Area and Data** section goes into the study area and data sources. Methods are introduced in **Methods** section. **Results** section presents the results and illustrates how the results can be applied in planning practice. Conclusions and policy recommendations are followed in **Discussion and Conclusions** section. 



## 3. Study Area and Data

Prague is the largest city of Czech Republic with a total area of  298  km2 and a population of 1,3 millions and the 14th largest city in the European Union. It is known as a major economical and cultural hub, located in the central region of Czech Republic. As a city with a more that 14th centuries of history it has classical medieval city center with a lot of historical buildings and small streets. Surroundings of the city center are more modern and on the periphery prevail small townhouses and private residences. The map below shows administrative borders of the municipal districts of Prague and total population of each district.  

For our study we use such types of data as population and geo data of the selected POI. All data was acquired from open data sources: for population we use data from Czech Statistical Office, for educational facilities -  registry of The Ministry of Education, Youth and Sports of Czech Republic. Other datasets was acquired form  Prague`s open data portal and other web resources. 

#### 3.1 Population

As population dataset we use census data from Czech Statistical Office. The entire dataset of [Czech Statistical Office]( https://www.czso.cz) contains data of population, gender and age of  citizens of Czech Republic. Futures names are coded.

| typuz_naz | nazev              | uzcis | uzkod | u01     |
| --------- | ------------------ | ----- | ----- | ------- |
| kraj      | Hlavní město Praha | 100   | 3018  | 1268796 |

As our study area is Prague, we are  only interested in population of the administrative districts with ***uzcis = 44***.  We  filtered data from original data set according to this parameter type, removed unnecessary columns and renamed left

#### 3.2 Geo-shapes

The administrative districts borders data set was acquired from [IPR Praha](http://www.geoportalpraha.cz). This is json data that contains an array of district's`s properties , such us Name, Shape, Area e.t.c. 

```json
"type" : "FeatureCollection",
	"name" : "TMMESTSKECASTI_P",
	"features" : [
		{
			"type" : "Feature",
			"geometry" : {
				"type" : "Polygon",
				"coordinates" : [
					[
						[ 14.535485616000074, 50.01175081800005 ],
						...
						[ 14.535067366000021, 50.011918405000074 ]
				]
			},
			"properties" : {
				"OBJECTID" : 1,
				"DAT_VZNIK" : "20171204150607",
				"DAT_ZMENA" : "20171204154726",
				"PLOCHA" : 3703180.2199999997,
				"ID" : 34,
				"KOD_MC" : 539791,
				"NAZEV_MC" : "Praha-Újezd",
				"KOD_MO" : 43,
				"KOD_SO" : "116",
				"TID_TMMESTSKECASTI_P" : 34,
				"POSKYT" : "HMP-IPR",
				"ID_POSKYT" : 43,
				"STAV_ZMENA" : "U",
				"NAZEV_1" : "Újezd",
				"Shape_Length" : 0.11393431835892182,
				"Shape_Area" : 3703180.22185
			}
		},						
```

From this data we interested in area and geometry, Also we get a district`s names as unique key. 

We compare population data set and districts borders datasets by the name of district. We get 57 unique records in these two datasets.  We perform joining by *name* this to sets into resulting one. Resulting data set was saved to IBM Cloud Storage as *prague_district_population.csv*. Also it can be accessed on our [GitHub repository](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/data/prague_district_population.csv)

|      | Name     | Geometry                                 | Area        | Total    | Kids    |
| :--- | :------- | :--------------------------------------- | :---------- | :------- | :------ |
| 0    | praha 1  | [[14.410891049000043, 50.078674687000046], [14... | 5538443.86  | 30561.0  | 2391.0  |
| 1    | praha 10 | [[14.531321086000048, 50.072240288000046], [14... | 18599366.98 | 113200.0 | 12213.0 |
| 2    | praha 11 | [[14.54355294800007, 50.03618763800006], [14.5... | 9793679.84  | 75741.0  | 8688.0  |

###### prague_district_population.csv

#### 3.3 Points of interest

In our study we interested for a few types of point such as: schools, educational and hobby centers, libraries, sport facilities and playgrounds. We decided to use only these 5 types, because these types are the most common types for children age 10 - 16. Data was acquired from  numerous data sources. We don't uses any types of geo API such as Google Places or Foursquare, because we need a full coverage of studying area for our research. All well known API don't provide such facilities for free accounts.    

##### Schools and  educational centers 

We get schools and educational centers data from The Ministry of Education, Youth and Sports. This xml data sets contains information about all state educational facilities in Prague. For our study we interested only in POIs types, districts and geographical coordinates. 

From original educational data sets we extracted  addresses and types of educational facilities, with 2273 rows in total. Types are coded. As we didn't find any futures description to this data set, we have to decided what types of educational facilities are suitable for out research. We get a unique types form retrieved data:

```python
Types in XML file
A00 Mateřská škola
L11 Školní jídelna
L13 Školní jídelna - výdejna
B00 Základní škola
M60 Přípravný stupeň základní školy speciální
G21 Školní družina
G22 Školní klub
F10 Základní umělecká škola
C00 Střední škola
D00 Konzervatoř
M20 Školní knihovna
E00 Vyšší odborná škola
M79 Jiné účelové zařízení
H22 Domov mládeže
G11 Dům dětí a mládeže
G40 Zařízení pro další vzdělávání pedagogických pracovníků
M40 Středisko praktického vyučování
H21 Internát
K20 Speciálně pedagogické centrum
G12 Stanice zájmových činností
K10 Pedagogicko-psychologická poradna
F20 Jazyková škola s právem státní jazykové zkoušky
J12 Dětský domov se školou
J21 Středisko výchovné péče
J14 Diagnostický ústav
J11 Dětský domov
J13 Výchovný ústav
L12 Školní jídelna - vývařovna
H10 Škola v přírodě
A15 Mateřská škola (lesní mateřská škola)
Schools and educational centers count 560
Unique types ['school' 'educatioanal center']
```

We can see that types that are interested for us are:

| Type code     | Czech name              | English name                |
| ------------- | ----------------------- | --------------------------- |
| **Education** |                         |                             |
| B00           | Základní škola          | Primary school              |
| F10           | Základní umělecká škola | Elementary School of art    |
| C00           | Střední škola           | High school                 |
| **Hobby**     |                         |                             |
| H22           | Domov mládeže           | Youth home                  |
| G11           | Dům dětí a mládeže      | House of children and youth |

For modeling step we don`t interested in actual venue address but in coordinates instead. We retrieve latitude and longitude with ArcGis api. The resulting data sets for schools and educational center contains 560 rows

| Type   | District_Name | latitude  | longitude |
| ------ | ------------- | --------- | --------- |
| school | praha 4       | 50.008620 | 14.448992 |
| school | praha 1       | 50.080344 | 14.415264 |

##### Libraries

Libraries' data was scrapped from [web](https://cs.wikipedia.org/wiki/M%C4%9Bstsk%C3%A1_knihovna_v_Praze). As data cleaning process we extracted addresses and districts name from original html page. For html parsing  we use  ***BeautifulSoup*** library. Addresses  was converted to  latitude and longitude with ArcGis api. The resulting data sets contains 41 libraries.

##### Sport centers, clubs and playgrounds.

Data with sport sport facilities and playgrounds were retrieves for Prague Open Data Portal. For this data we perform the same steps as for previous data sets: address and district`s names extracting, converting  address to  latitude and longitude.

We join all five data sets to a resulting one with 1623 rows. Data set is accessible on out [GitHub Repository](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/data/prague_pois.csv)

|      | District_Name | Type       | latitude  | longitude |
| :--- | :------------ | :--------- | :-------- | :-------- |
| 5    | praha 4       | playground | 50.008620 | 14.448992 |
| 22   | praha 1       | school     | 50.080344 | 14.415264 |
| 25   | praha 1       | school     | 50.090834 | 14.428853 |
| 26   | praha 1       | sport      | 50.090834 | 14.428853 |
| 30   | praha 4       | library    | 50.001684 | 14.413886 |

###### prague_pois.csv

##### Streets network 

Streets network data obtained from Open Street Map (OSM)The OSM data contains Prague road networks and surrounding connected roads, which are line geometries characterized by length. These can be used to construct a routable topology graph (directed, weighted and connected), which consists of nodes and edges, so that any well-known route search algorithm can be applied [3]. Pedestrian network for Prague contains 140 822 nodes and 204 575 links. Pre-proceeds data we store at IBM Cloud Object Storage and [GitHub](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/data/network_14.224437012000067_49.94190007000003_14.706787572000053_50.17742967400005.h5)

## 4. Exploratory Data Analysis 

Total Prague`s population is approximately 1.3 millions of people. The highest values is at south districts - form 110K to 130K. All districts with population higher then mean are situated around historical center districts. The averages population of these districts is70 K people. This is expected. Most people in such types of cities as Prague leaves close to center but not inside it. Two largest districts in the north with population from 90 to 100K  shows us new development areas of Prague. 

![Prague](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Walking_access_Prague.png?raw=true)

Analyzing Prague's population  from other point of view, we can notice that distribution of children per 1000 adults is correlated with previous picture. In this diagram we can clearly see districts that a mainly liked by  families. The central districts and as suburbs has the lowest values while districts around center but not far from it has average values, approximately form160 to 180 children per 1000 adults.![Ch_per_1000.png](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Ch_per_1000.png?raw=true)

Top 10 districts with highest children population are repeat top districts form previous pictures. Maximum we have 8 percent in Prague 4. 

![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Ch_from_overall.png?raw=true)

Points of interest is also concentrated around historical center. Distribution by districts is correlated with districts population. This is expected values.  We have approximately equal number of points in all districts from top 10 by population, with an anomaly height values at the south.

![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Poi_total.png?raw=true)

Total number of POIs doesn't show a full view of how good each district is for chidden. For us more interesting to look at how number of POIs is related to 1000 children. For example if we look at the histogram of  Schools/1000. We notice that Top values are not correlated with children`s population. We can see that Prague 1 have the  highest values but if we look at previous graphs we can see that by the percent of children Prague not even in top 10. From the other side Prague 4 have the highest values of population and it also have average values of Schools/1000.

![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/School_1000.png?raw=true)

The same picture we can see on next histograms. Central districts have the highest value  of POIs/1000 while suburbs have the lowest. From the one side this is because of low children's population in the city center, form the other cities are growing from it`s historical centers and concertation of schools and other facilities is often higher there.



![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Edu_1000.png?raw=true)

![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Lib_1000.png?raw=true)

![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Sport_1000.png?raw=true)

![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Play_1000.png?raw=true)



## 5. Methodology



### 5.1 Network Analysis 

The spatial frame of this study necessitates the use of point-based accessibility measures. The commonly used spatial units are administrative division and grid cell. As study area ranges from a city to a country, even the whole world, the type, shape and size of spatial unit differ in research purposes and there is no consensus over them.  In our study with use a bounding box of Prague as spatial frame. We can`t use smaller frames such as administrative divisions for our research because we have to deal with the boundary conditions. If children leave in own district and nearest POI is located in another. In this case very often parents of course decide to go to this nearest POI.

In first step we converted OSM street network to graph objects. For building graph we use Pandana framework. The main use case of Pandana is to perform an aggregation along the network - i.e. a buffer query. The api is designed to perform the aggregations for all nodes in the network at the same time in a multi-threaded fashion (using an underlying C library). Most walking-scale accessibility queries can be performed in well under a second, even for hundreds of thousands of nodes. [4]

Under cleaning we mean removing  points that don’t represent actual intersections (hence are not nodes in the graph theory sense). 

![Pedestrian Network]()

As the second step is to located POIs form previous step to network graph and calculate accessibility  matrix. Under accessibility  matrix we mean an array of distances to top 3 POIs  from the array of predefined POI acquired at data acquisition step.  As we want to research average values of accessibility we calculate average walking distance for every type of POIs: school, library, other children`s facilities. And store it to dataset. This dataset later will be used for clustering. We get  140877 edges in total

|     id |   1_school |   2_school |   3_school | 1_educatioanal center |
| -----: | ---------: | ---------: | ---------: | --------------------: |
|        |            |            |            |                       |
| 172508 | 218.384003 | 452.865997 | 502.253998 |            124.689003 |
| 172510 |  42.796001 | 326.665985 | 347.582001 |             50.898998 |
| 172512 | 226.128006 | 290.959991 | 300.862000 |            421.157990 |
| 172513 | 353.912994 | 393.170990 | 442.434998 |            627.351990 |
| 172514 | 270.234985 | 443.700989 | 492.393005 |            711.030029 |

![Average distances]()

We will use K-Means clustering algorithm.  This is a method of vector quantization, originally from signal processing, that is popular for cluster analysis in data mining. k-means clustering aims to partition n observations into k clusters in which each observation belongs to the cluster with the nearest mean, serving as a prototype of the cluster. This results in a partitioning of the data space into Voronoi cells. k-Means minimizes within-cluster variances (squared Euclidean distances). Do determine the optimal number of clusters we use and Elbow method. 

The Elbow method is a heuristic method of interpretation and validation of consistency within cluster analysis designed to help finding the appropriate number of clusters in a dataset. It is often ambiguous and not very reliable, and hence other approaches for determining the number of clusters such as the Silhouette method are preferable.

This method looks at the percentage of variance explained as a function of the number of clusters: One should choose a number of clusters so that adding another cluster doesn't give much better modeling of the data. More precisely, if one plots the percentage of variance explained by the clusters against the number of clusters, the first clusters will add much information (explain a lot of variance), but at some point the marginal gain will drop, giving an angle in the graph. The number of clusters is chosen at this point, hence the "elbow criterion". This "elbow" cannot always be unambiguously identified.[1] Percentage of variance explained is the ratio of the between-group variance to the total variance, also known as an F-test. A slight variation of this method plots the curvature of the within group variance.[2]

![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Elbow%20(2).png?raw=true)

### 5.2 Walkability Clusters

As a result we have 4 clusters. We calculate avveages values of disctance, walkability core and time for exch cluster. Under reasonable time we mean time that children need to spend to walk 1300 meters. Calculated walkability score  for each type in each cluster. **SCORE = Actual Distance /1300**. As average walking speed we get 4 km/h [4].  

|             Cluster No |    0 |    1 |    2 |    3 |
| ---------------------: | ---: | ---: | ---: | ---: |
|      Walkability Score |  1.3 |  2.3 |  1.0 |  1.7 |
| Walking time (minutes) |      |      |      |      |
|                Schools |    9 |   44 |    6 |   19 |
|                  Hobby |   36 |   45 |   20 |   44 |
|                Library |   30 |   45 |   23 |   44 |
|       Sport facilities |    8 |   43 |    6 |   14 |
|            Playgrounds |   45 |   45 |   45 |   45 |

By redefining weights of edges on walking graph we can visualize cluster on the Prague`s map.

![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_14.224437012000067_49.94190007000003_14.706787572000053_50.17742967400005.png?raw=true)

Also we build walkability heat map for every type of our POIs.

| Schools                                  | Hobby                                    |
| ---------------------------------------- | ---------------------------------------- |
| ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Dschool_avg.png?raw=true) | ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Deducatioanal%20center_avg.png?raw=true) |
| **Sport**                                | **Library**                              |
| ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Dsport_avg.png?raw=true) | ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Dlibrary_avg.png?raw=true) |
| **Playgrounds**                          |                                          |
| ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Dplayground_avg.png?raw=true) |                                          |

## 5. Results

As a result we have 4 clusters of walkability in Prague. Cluster 2 have optimal walkability score 1. It means that from every point inside this cluster children can reach all necessary POIs approximately in 15 minutes average. Schools are most reachable inside this cluster, which is very good result. From the other side Cluster 2 mainly covers central historical region and we know that in this districts population of children's is lower than at surrounding districts. The second cluster is number 0. It have walkability score around 1,3. schools and sport facilities can reached inside this cluster less then 10 minutes. This is very good results. 

The lowest values of accessibility score inside this two cluster have libraries and hobby centers. It takes approximately 30 minutes in average. 

| Cluster 0                                                    | Cluster 2                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/cluster_hist%20(8).png?raw=true) | ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/cluster_hist%20(7).png?raw=true) |

The lowest value we get at cluster 3.  This cluster covers mainly small suburbs and district that are very far from center, mainly this is small villages. 

Cluster 1 we decided to  count as noise values, because inside this cluster we mainly have parks, cemeteries and industrial areas.









## 7. Discussion and Conclusions



## 8. References

1.  Living Streets (The Pedestrians’ Association)   [A LIVING STREETS REPORT](https://www.livingstreets.org.uk/media/3618/ls_school_run_report_web.pdf)
2.  [Criterion distances and correlates of active transportation to school in Belgian older adolescents.](https://ijbnpa.biomedcentral.com/articles/10.1186/1479-5868-7-87) Delfien Van Dyck, Ilse De Bourdeaudhuij, Greet Cardon & Benedicte Deforche 
3.  Naumann, S., & Kovalyov, M. Y. (2017). Pedestrian route search based on OpenStreetMap. In *Intelligent Transport Systems and Travel Behaviour (pp. 87-96)*. Cham: Springer.
4.  Pandana https://udst.github.io/pandana/introduction.html#introduction
5.  THE MECHANICS OF WALKING IN CHILDREN  https://physoc.onlinelibrary.wiley.com/doi/pdf/10.1113/jphysiol.1983.sp014895 













##  9. Copyright information

 © Anton Eremin 2019

**Open Access**

This article is distributed under the terms of the Creative Commons Attribution 4.0 International License (http://creativecommons.org/licenses/by/4.0/), which permits unrestricted use, distribution, and reproduction in any medium, provided you give appropriate credit to the original author(s) and the source, provide a link to the Creative Commons license, and indicate if changes were made.  

