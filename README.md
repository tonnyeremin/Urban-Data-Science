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

The step of cleaning data includes a few steps: (1) Joining  by the name of the districts the population dataset and geo-data of the municipal districts borders. (2) Thought ArcGis API retrieving  geographical coordinates of POIs. As a result we have to datasets: 

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



## 4. Methodology



## 5. Results



## 6. Discussion and Conclusions



## 7. References

1.  Living Streets (The Pedestrians’ Association)   [A LIVING STREETS REPORT](https://www.livingstreets.org.uk/media/3618/ls_school_run_report_web.pdf)
2. [Criterion distances and correlates of active transportation to school in Belgian older adolescents.](https://ijbnpa.biomedcentral.com/articles/10.1186/1479-5868-7-87) Delfien Van Dyck, Ilse De Bourdeaudhuij, Greet Cardon & Benedicte Deforche 





 







##  8. Copyright information

 © Anton Eremin 2019

**Open Access**

This article is distributed under the terms of the Creative Commons Attribution 4.0 International License (http://creativecommons.org/licenses/by/4.0/), which permits unrestricted use, distribution, and reproduction in any medium, provided you give appropriate credit to the original author(s) and the source, provide a link to the Creative Commons license, and indicate if changes were made.  

