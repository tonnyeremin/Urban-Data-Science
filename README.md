# City`s Pedestrian Accessibility 

## 1. Abstract



### Keywords

 *Accessibility*   *Clustering analysis*  

## 2. Introduction

Walking is the most reliable, easy and healthy way to travel. Yet today the number of children walking to school is in decline. This has damaging consequences for our children’s health, safety and wellbeing.
Over 70 per cent of today’s parents walked to school when they were children but less than half of children walk to school today. Meanwhile, the number of children being driven to school is growing, with an increasingly negative impact on congestion, health and communities [1].

Walking is an easy, free and healthy way for children to get to school or other activities such as libraries, cultural and hobby centers, playgrounds e. t. c. Walking provides a lot of benefits for every one. 

What we mean under pedestrian accessibility? For us it means the ability to reach particular everyday - life points of interest (POI) in a reasonable time. Under reasonable time we mean time that children need to spend to walk 1300 meters. This distance is recommended as reasonable for selected age range. [2]    Common POIs for children from 10 to 16 years are: schools, hobby and leisure time facilities, libraries, sport facilities and outdoor playgrounds. 

 The objectives of this study therefore are:   (1) Analyze current city`s infrastructure for children in terms of density population and density of POIs. (2) Build network of walkable routes. (3) Define clusters 

With discussions on related literatures, this section has introduced the purpose and scope of this paper. **Study Area and Data** section goes into the study area and data sources. Methods are introduced in **Methods** section. **Results** section presents the results and illustrates how the results can be applied in planning practice. Conclusions and policy recommendations are followed in **Discussion and Conclusions** section. 



## 3. Study Area and Data

Prague is the largest city of Czech Republic with a total area of  298  km2 and a population of 1,3 millions and the 14th largest city in the European Union. It is known as a major economical and cultural hub, located in the central region of Czech Republic. As a city with a more that 14th centuries of history it has classical medieval city center with a lot of historical buildings and small streets. Surroundings of the city center are more modern and on the periphery prevail small townhouses and private residences. The map below shows administrative borders of the municipal districts of Prague and total population of each district.  

![Prague](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Walking_access_Prague.png?raw=true)

For our study we use such types of data as population and geo data of the selected POI. All data was acquired from open data sources: for population we use data from Czech Statistical Office, for educational facilities -  registry of The Ministry of Education, Youth and Sports of Czech Republic. Other datasets was acquired form  Prague`s open data portal and other web resources. 

The step of cleaning data included a few steps: (1) Joining  by the name of the districts the population dataset and geo-data of the municipal districts borders. (2) Thought ArcGis API retrieving  geographical coordinates of POIs. As a result we have to datasets:  Prague population by districts with 57 rows and POIs dataset with 1623 rows.

***Prague population by districts***

|      | Name     | Geometry                                          | Area        | Total    | Kids    |
| :--- | :------- | :------------------------------------------------ | :---------- | :------- | :------ |
| 0    | praha 1  | [[14.410891049000043, 50.078674687000046], [14... | 5538443.86  | 30561.0  | 2391.0  |
| 1    | praha 10 | [[14.531321086000048, 50.072240288000046], [14... | 18599366.98 | 113200.0 | 12213.0 |
| 2    | praha 11 | [[14.54355294800007, 50.03618763800006], [14.5... | 9793679.84  | 75741.0  | 8688.0  |

 ***Points of interest.*** 

|      | District_Name | Type   | latitude  | longitude |
| :--- | :------------ | :----- | :-------- | :-------- |
| 5    | praha 4       | school | 50.008620 | 14.448992 |
| 22   | praha 1       | school | 50.080344 | 14.415264 |
| 25   | praha 1       | school | 50.090834 | 14.428853 |
| 26   | praha 1       | school | 50.090834 | 14.428853 |
| 30   | praha 4       | school | 50.001684 | 14.413886 |

Walking network data obtained from Open Street Map (OSM)The OSM data contains Prague road networks and surrounding connected roads, which are line geometries characterized by length. These can be used to construct a routable topology graph (directed, weighted and connected), which consists of nodes and edges, so that any well-known route search algorithm can be applied [3]. Pedestrian network for Prague contains 140 822 nodes and 204 575 links. 

All acquired and pre-proceeds data we store at IBM Cloud Object Storage. 



## 4. Methodology

### 4.1 Network Analysis 

The spatial frame of this study necessitates the use of point-based accessibility measures. The commonly used spatial units are administrative division and grid cell. As study area ranges from a city to a country, even the whole world, the type, shape and size of spatial unit differ in research purposes and there is no consensus over them.  In our study with use a bounding box of Prague as spatial frame. We can`t use smaller frames such as administrative divisions for our research because we have to deal with the boundary conditions. If children leave in own district and nearest POI is located in another. In this case very often parents of course decide to go to this nearest POI.

In first step we converted OSM street network to graph objects. For building graph we use Pandana framework. The main use case of Pandana is to perform an aggregation along the network - i.e. a buffer query. The api is designed to perform the aggregations for all nodes in the network at the same time in a multi-threaded fashion (using an underlying C library). Most walking-scale accessibility queries can be performed in well under a second, even for hundreds of thousands of nodes. [4]

Under cleaning we mean removing  points that don’t represent actual intersections (hence are not nodes in the graph theory sense). 

![Pedestrian Network]()

As the second step is to located POIs form previous step to network graph and calculate accessibility  matrix. Under accessibility  matrix we mean an array of distances to top 3 POIs  from the array of predefined POI acquired at data acquisition step.  As we want to research average values of accessibility we calculate average walking distance for every type of POIs: school, library, other children`s facilities. And store it to dataset. This dataset later will be used for clustering. We get  140877 edges in total

|     id |   1_school |   2_school |   3_school | 1_educatioanal center | 2_educatioanal center | 3_educatioanal center |  1_library |   2_library |   3_library |    1_sport |    2_sport |    3_sport |      1 |      2 |      3 |
| -----: | ---------: | ---------: | ---------: | --------------------: | --------------------: | --------------------: | ---------: | ----------: | ----------: | ---------: | ---------: | ---------: | -----: | -----: | -----: |
|        |            |            |            |                       |                       |                       |            |             |             |            |            |            |        |        |        |
| 172508 | 218.384003 | 452.865997 | 502.253998 |            124.689003 |           1545.234985 |           1629.285034 | 359.911011 |  653.177002 |  905.504028 |  20.875999 |  20.875999 | 105.547997 | 3000.0 | 3000.0 | 3000.0 |
| 172510 |  42.796001 | 326.665985 | 347.582001 |             50.898998 |           1477.159058 |           1512.265015 | 211.785004 |  828.643005 |  973.174011 | 154.315994 | 194.106003 | 194.106003 | 3000.0 | 3000.0 | 3000.0 |
| 172512 | 226.128006 | 290.959991 | 300.862000 |            421.157990 |           1142.005981 |           1463.151978 | 454.751007 | 1161.907959 | 1195.189941 |  80.668999 | 152.044998 | 181.822998 | 3000.0 | 3000.0 | 3000.0 |
| 172513 | 353.912994 | 393.170990 | 442.434998 |            627.351990 |            935.812012 |           1372.583008 | 621.794006 | 1335.552002 | 1347.269043 | 259.713013 | 300.928009 | 326.744995 | 3000.0 | 3000.0 | 3000.0 |
| 172514 | 270.234985 | 443.700989 | 492.393005 |            711.030029 |            852.133972 |           1288.905029 | 672.323975 | 1385.510010 | 1430.947021 | 343.390991 | 377.274994 | 384.605988 | 3000.0 | 3000.0 | 3000.0 |

![Average distances]()

We will use K-Means clustering algorithm.  This is a method of vector quantization, originally from signal processing, that is popular for cluster analysis in data mining. k-means clustering aims to partition n observations into k clusters in which each observation belongs to the cluster with the nearest mean, serving as a prototype of the cluster. This results in a partitioning of the data space into Voronoi cells. k-Means minimizes within-cluster variances (squared Euclidean distances). Do determine the optimal number of clusters we use and Elbow method. 

The Elbow method is a heuristic method of interpretation and validation of consistency within cluster analysis designed to help finding the appropriate number of clusters in a dataset. It is often ambiguous and not very reliable, and hence other approaches for determining the number of clusters such as the Silhouette method are preferable.

This method looks at the percentage of variance explained as a function of the number of clusters: One should choose a number of clusters so that adding another cluster doesn't give much better modeling of the data. More precisely, if one plots the percentage of variance explained by the clusters against the number of clusters, the first clusters will add much information (explain a lot of variance), but at some point the marginal gain will drop, giving an angle in the graph. The number of clusters is chosen at this point, hence the "elbow criterion". This "elbow" cannot always be unambiguously identified.[1] Percentage of variance explained is the ratio of the between-group variance to the total variance, also known as an F-test. A slight variation of this method plots the curvature of the within group variance.[2]

![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/Elbow%20(2).png?raw=true)

### 4.2 Walkability Clusters

As a result we have 4 clusters. We calculate avveages values of disctance, walkability core and time for exch cluster. Under reasonable time we mean time that children need to spend to walk 1300 meters. Calculated walkability score  for each type in each cluster. **SCORE = Actual Distance /1300**. As average walking speed we get 4 km/h [4].  

|             Cluster No |    1 |    2 |    3 |    4 |
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

| Schools                                                      | Hobby                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Dschool_avg.png?raw=true) | ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Deducatioanal%20center_avg.png?raw=true) |
| **Sport**                                                    | **Library**                                                  |
| ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Dsport_avg.png?raw=true) | ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Dlibrary_avg.png?raw=true) |
| **Playgrounds**                                              |                                                              |
| ![](https://github.com/tonnyeremin/Urban-Data-Science/blob/master/Images/map_pois_%7B%7Dplayground_avg.png?raw=true) |                                                              |

Histograms of distances

 

| Cluster 1 | Cluster 2 | Cluster 3 | Cluster 4 |
| --------- | --------- | --------- | --------- |
|           |           |           |           |
|           |           |           |           |
|           |           |           |           |
|           |           |           |           |



## 5. Results













## 6. Discussion and Conclusions



## 7. References

1.  Living Streets (The Pedestrians’ Association)   [A LIVING STREETS REPORT](https://www.livingstreets.org.uk/media/3618/ls_school_run_report_web.pdf)
2. [Criterion distances and correlates of active transportation to school in Belgian older adolescents.](https://ijbnpa.biomedcentral.com/articles/10.1186/1479-5868-7-87) Delfien Van Dyck, Ilse De Bourdeaudhuij, Greet Cardon & Benedicte Deforche 
3. Naumann, S., & Kovalyov, M. Y. (2017). Pedestrian route search based on OpenStreetMap. In *Intelligent Transport Systems and Travel Behaviour (pp. 87-96)*. Cham: Springer.
4. Pandana https://udst.github.io/pandana/introduction.html#introduction
5.  THE MECHANICS OF WALKING IN CHILDREN  https://physoc.onlinelibrary.wiley.com/doi/pdf/10.1113/jphysiol.1983.sp014895 





 







##  8. Copyright information

 © Anton Eremin 2019

**Open Access**

This article is distributed under the terms of the Creative Commons Attribution 4.0 International License (http://creativecommons.org/licenses/by/4.0/), which permits unrestricted use, distribution, and reproduction in any medium, provided you give appropriate credit to the original author(s) and the source, provide a link to the Creative Commons license, and indicate if changes were made.  

