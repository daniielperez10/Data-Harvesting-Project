---
title: "Final Project - Data Harvesting"
author: "Andrea Baños and Daniel Pérez"
date: "2024-03-11"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Load the main libraries

The first code chunk displays the libraries that will be used in this data harvesting project, focusing on the "Theatre" category, showing different shows from the website tomaticket.es.

The "rvest" library helps us to perform web scraping and navigate HTML documents, making data extraction easier. Moreover, the "xml2" library assists us in analyzing XML and HTML documents and navigating through them to extract the necessary information. Lastly, the "tidyverse" library encompasses many R packages essential for data analysis.

## Extracting the data

First, the URL of the page with the "Theater" category will be loaded, and then the read_html(url) function reads the content of the tomaticket webpage, displaying the children of the HTML document with the xml_child() function to access the content needed to carry out the project.

## Navigating through the extracted data - Generating the variables

In order to obtain the "show" variables, the xml_find_all() command is used to search within the XML document for all <div> elements with id='list-search-event' and class='search-events-normal', and finally selects the <a> elements within these <div> elements. However, due to the pipe operator, indicating that the extraction of this data does not end here, the xml_attr(attr = "href") command extracts the "href" attribute of each of the <a> elements mentioned earlier. Subsequently, to obtain the links of the shows and exclude the default advertisement links, those links starting with "https:" within urls_clean are ignored. Finally, to obtain the URLs of the shows, "https://www.tomaticket.es/" is concatenated in front of each show link (urls_clean).

A vector of theaters is initialized as empty. In order to populate this vector, a loop is generated to read each link within show_links. Using the command html_node(xpath = "//div[@id='EventHeadContenido' and @class='white-space']//p[@class='nombre-recinto']//a"), it searches for the <div> elements with id='EventHeadContenido' and class='white-space'. Within this, it searches for a <p> element with class='nombre-recinto', and finally, it looks for an <a> element within the selected <p>. Similarly to the creation of the "show_links" variable, which indicates the names of the shows available in Spain through the Tomaticket website, the command html_attr("href") extracts the "href" attribute belonging to the aforementioned <a> element (now using html instead of xml). Finally, the link of each "theater" is added to the theaters vector to obtain all the theaters related to each event on the website.

We proceed in the same way to obtain the coordinates of the theaters.

Moreover, the variables price, datetime, and location have been extracted in a similar manner. In order to obtain the price variable (only_prices), the xml_find_all() command is used to search within the XML document for all <p> elements with class='bottommargin-precio', itemprop='offers', itemscope='', and itemtype='https://schema.org/AggregateOffer', and within this, it looks for <meta> elements with itemprop='price'. To obtain the "datetime" variable, the same process is followed, but instead of finding <meta> elements, it searches for <time> elements. Similarly, to obtain the "location" variable at a province level, the xml_find_all() command is used to search within the XML document for all <p> elements with class='lugar', itemprop='location', itemscope='', and itemtype='https://schema.org/Place', and within this, it searches for all <meta> elements. These variables are not yet cleaned or combined into a data frame; these steps will be performed in the next section.

## Data Cleaning and Final Data Frame Creation

Throughout this process of creating data frames and cleaning variables, the "id" variable has been created for each individual data frame, for its subsequent merging with other data frames using the left_join() command.

Due to the use of the sub() function, first, any text appearing before the date format is removed, and then, the resulting date with the pattern (YYYY-MM-DD) is finally converted into date format, cleaning all the dates obtained into objects of class "Date" with the as.Date command.

Furthermore, to obtain the name of the theaters, we extract the information that appears after the "/recintos/" structure from the "theater" column of the df_theaters data frame, and replace "-" with spaces using the gsub() command. Similarly, to obtain the name of the shows, we extract the information that appears after "/entradas-" from the "show_links" column of the df_shows data frame, and replace "-" with spaces using the gsub() command. In order to obtain the location of the show, we search for the pattern 'address' within "location" and use the sub() command to collect the information after the second "=". To get the prices, we also take the information after the second "=" using the sub() command, and replace "," with "." using the gsub() command. As a newly generated variable in the final_df, we have created the date of the day of the presentation of this project using the as.Date("2024-03-22", format = "%Y-%m-%d") command to consider the number of days the show has been active until the presentation date. The final_df shown at the end displays all these variables.

As a precautionary measure, we have saved df_final as a database in case Tomaticket removes any links that might prevent us from retrieving that observation again. This allows us to have a secure copy of the data for reference and ongoing analysis, even if the original data source experiences changes or loss of information.

## Descriptive Analysis

We perform different graphics, maps, and clustering techniques to analyze the shows offered on Tomaticket

## Conclusions
The first conclusion we can draw is that the majority of the theater productions offered on Tomaticket take place in theaters located in Madrid. 84% of the shows are held in this city, while 13% are in Barcelona, and only 3% (3 observations) in the rest of Spain, which makes it difficult to conduct a nationwide analysis. Faced with this situation, we have decided to focus our analysis solely on Madrid.


MADRID

The theaters with the highest average theater ticket prices are:

1. Gran Teatro Caixabank Principe Pio Madrid (21€)
2. Teatro Marquina (18.2€)
3. Taberna Flamenca El Cortijo Madrid (18€)	

The theaters with the lowest average theater ticket prices are:

1. Intruso bar Madrid	(5€)		
2. Wit Comedy Club Madrid (7€)	
3. Meltdown Madrid	(8€)


-Number of shows per theater.
There are 27 theaters located in Madrid offering shows at Tomaticket.
Regarding the number of shows hosted, "Teatro Luchana" is the theater that hosts the most shows in Madrid (17), followed by "Teatro Lara" (9), "Teatro Arlequín" (9), and "Teatro Off Latina" (7). This suggests that "Teatro Luchana" has a more diverse and extensive range of shows compared to the other theaters mentioned.

-Days elapsed.
In terms of how long the shows are available in the theater, "Doble o Nada" stands out significantly from the rest, with a run time of over 1000 days. The other shows closely following it have an approximate duration of 500 days each.

-Relationship between days elapsed and price of each show.

There is a certain positive relationship observed between the number of days the show is active and its price in shows priced from 10€ onwards. This suggests that, overall, shows with longer durations tend to have slightly higher prices. However, this relationship is not very strong, indicating that other factors may also influence the price of shows, besides the duration on the billboard. It is important to consider other variables such as the popularity of the show, ticket demand, production quality, among others, to fully understand how the price of a show is determined.

-Grouping theaters

Based on the grouping of theaters according to their price and the number of days their shows are available on the billboard, we can draw the following conclusions:

Group 1 theaters, with relatively high average prices and shows available for more than 350 days, may cater to audiences seeking premium or long-running productions. These theaters may attract patrons willing to pay higher prices for quality or established performances.

Group 2 theaters, characterized by the lowest prices and shows available for less than 300 days, likely offer more budget-friendly options. These theaters may target a broader audience, including those looking for affordable entertainment or shorter-term shows.

Group 3 theaters, with shows available for less than 300 days but relatively high prices, represent a unique niche. These theaters may focus on offering exclusive or limited-run productions, appealing to audiences seeking premium experiences despite shorter availability periods.

-Location

Regarding the location of theaters in Madrid whose shows are offered on Tomaticket, it is notable that all theaters, at least those for which Tomaticket provides location information, are located in the city center. This suggests a concentration of theatrical activities in the heart of Madrid, which could facilitate access and participation by the public.

As for the relationship between location and price, no clear or consistent pattern is observed. This means that the price of shows does not appear to be directly related to the geographical location of the theater in Madrid.
## Instructions
-Download the file in HTML or Rmd format.

-If you are using the Rmd file, make sure to download the [https://github.com/daniielperez10/Data-Harvesting-Project/blob/main/final_df.csv](data/df_final.csv) database attached in the repository to use exactly the same data with which we conducted the descriptive analysis 

-Run the code


