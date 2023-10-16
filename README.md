# data-512-homework_2

## Goal
The goal of this assignment is to examine potential data bias in Wikipedia articles. Specifically, we will be checking to see which states have the highest and lowest coverage compared to their populations, highest and lowest porportion of high quality articles, and a ranking of Census Divisions by articles/person and proportion of high quality articles. There is a reflection at the end of this README which discusses findings further.


## Licenses & API Information

The code used in this project is licensed under the MIT license - more information can be found in the license file in the top-level repo.

The article source data comes from English Wikipedia, the text of which is licensed under "Creative Commons Attribution Share-Alike license" (https://en.wikipedia.org/wiki/Wikipedia:Text_of_the_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)

We will be using the MediaWiki REST API for English Wikipedia. To get more information on the API please use the following link: https://www.mediawiki.org/wiki/API:Main_page. The following link may also be helpful when looking for more documentation: https://www.mediawiki.org/wiki/API:Info

We will also be using the ORES API. Information on the API itself can be found at https://www.mediawiki.org/wiki/ORES, with original API documentation from https://ores.wikimedia.org/docs and new LiftWing documentation from https://wikitech.wikimedia.org/wiki/Machine_Learning/LiftWing/Usage.

We will leverage code developed by Dr. David W. McDonald for use in Data 512  which is provided under Creative Commons CC-BY license. (https://creativecommons.org/ and https://creativecommons.org/licenses/by/4.0/). The file can be found at this link: https://drive.google.com/drive/folders/1FtvWV31DHE8HIMdEsPGuCXPz0PMvShfl


## Data File Information

### Input data
We will input 3 sources of raw data: NST-EST2022-POP.xlsx, US States by Region - US Census Bureau - Sheet1.csv, and us_cities_by_state_SEPT.2023.csv. Download information can be found in the "Analysis Reproduction Steps" of this document below.

NST-EST2022-POP.csv has the following variables and definitions:
    Geographic Area: Total US, Northeast/Midwest/South/West regions, and per-state divisions of population
    April 1, 2020 Estimates Base: The base population used for future estimates
    Population Estimate (as of July 1):
        2020: Estimated population for 2020
        2021: Estimated population for 2021
        2022: Estimated population for 2022
        
US States by Region - US Census Bureau - Sheet1.csv has the following variables and definitions:
    Region: Regions are set by the US Census Bureau and are the highest level of US deaggregation
    Division: Multiple Divisions make up a Region, and they are themselves composed of states
    State: These are the typical 50 US states 

us_cities_by_state_SEPT.2023.csv was given to us by Professor McDonald and contains the results from crawling The Wikipedia Category:Lists of cities in the United States by state. The variables are:
    state: US State the city is from
    page_title: Typically the name of the city, state
    url: Link to the city's wikipedia page
    

### Intermediary data files 
clean_us_cities_by_state.csv - This file contains the same information as us_cities_by_state_SEPT.2023.csv, but duplicate rows have been removed, as well as page titles not related to US cities ('2020 United States census', '2010 United States census', 'County (United States)', 'Population')

page_info.json - This file stores the intermediate page information for each article in our cities by state list. The articles are from us_cities_by_state_SEPT.2023.csv, and the page information comes from the MediaWiki REST API. It is structured as a JSON with article_title as the key and the lastrevid (last revision id) as the value.

states_by_region.csv - This file contains the cleaned data from "US States by Region - US Census Bureau". The attributes are the same as described in "Input data" above, and were cleaned as detailed in step 4 of "Analysis Reproduction Steps".

ores_scores.json - This file contains the article titles (keys) and ORES scores (values) for files which successfully processed through ORES scores the first time.

new_ores_scores.json - This file contains the article titles (keys) and ORES (values) scores for files which produced a key error when run through the ORES model.

final_ores_scores.json - This file contains the article titles (keys) and ORES scores (values) for the originally run files and the files contained in new_ores_scores.

wp_scored_city_articles_by_state.csv - This file contains the final, merged data on states, their regions, populations, article titles, article revision ids, and article qualities. It comes from merging the intermediary and raw data files above. See the notebook for more information.
    state: 50 US states with names in standard formats (e.g., New Hampshire not New_Hampshire)
    regional_division: The Census Division the state belongs to.
    population: Total 2022 population of the state.
    article_title: Title of an article about a city in the state noted
    revision_id: Last revision id of the articles
    article_quality: Estimated quality ranking for each article produced by ORES

### Final outputs
top_10_coverage.csv - This file contains the top 10 states by article coverage (# articles / population). The attributes are as follows:
    state: State in question
    regional_division: Regional Division as established by the US Census
    population: Population of the state
    count_articles: Count of articles written about cities in the state based on our scraping
    art_per_pop: How many articles per capita are there for the state

bottom_10_coverage.csv - This file contains the bottom 10 states by article coverage (# articles / population). The attributes are as follows:
    state: State in question
    regional_division: Regional Division as established by the US Census
    population: Population of the state
    count_articles: Count of articles written about cities in the state based on our scraping
    art_per_pop: How many articles per capita are there for the state

census_reg_by_tot_coverage.csv - This file shows the Census regions in descending order of coverage.
    regional_division: Regional Division as established by the US Census
    population: Population of the region (constituient state populations summed)
    count_articles: Count of articles written about cities in the region based on our scraping
    art_per_pop: How many articles per capita are there for the region

top_10_quality.csv - These are the top 10 states by quality articles/population
    state: State in question
    regional_division: Regional Division as established by the US Census
    population: Population of the state
    count_articles: Count of articles written about cities in the state based on our scraping where ORES ranked the article FA or GA.
    art_per_pop: How many articles per capita are there for the state

bottom_10_quality.csv - These are the bottom 10 states by quality articles/population
    state: State in question
    regional_division: Regional Division as established by the US Census
    population: Population of the state
    count_articles: Count of articles written about cities in the state based on our scraping where ORES ranked the article FA or GA.
    art_per_pop: How many articles per capita are there for the state

census_reg_by_quality_coverage.csv - These are the census regions by quality of articles/population
    regional_division: Regional Division as established by the US Census
    population: Population of the region (sum of state populations in region)
    count_articles: Count of articles written about cities in the region based on our scraping where ORES ranked the article FA or GA.
    art_per_pop: How many articles per capita are there for the region

## Known Issues and Special Considerations

### Consideration 1
Professor McDonald noted, "Crawling Wikipedia categories to identify relevant page subsets can result in misleading and/or duplicate category labels. Just skimming the category page (listed above), you can see that sub-categories have limited consistency in their naming conventions. A data crawl can result in possible duplicate articles linked from differently named sub-categories. Naturally, the data crawl attempts to resolve some of these problems, but not all may have been caught."

In our data_acquisition code we check to see if all the page titles are unique and we find that they are not. To reduce later processing time we remove duplicate entries (same state, page_title, and url) from the file, eliminating ~638 rows. Then we find there are page titles which do not represent cities (e.g., 'Population', '2020 United States census') and we remove these entries from the eligible articles to be analyzed.

### Consideration 2
The data in the data_acquisition script takes significant time to pull. Obtaining the page information will take ~2 hours, and running the ORES script can take ~5 hours. 

### Consideration 3
There are articles which ORES may not be able to return a score for - this is indicated by the API return value being "None". The list of articles is stored and printed out in the Python Notebook. When we ran the script, ORES could score all articles except for Corsicana, Texas.

### Consideration 4
There are states which do not have any articles written about their cities, per our data. Given we can look up city information from Connecticut and Nebraska on Wikipedia, we believe this is an issue with our scraping, rather than a lack of articles. We will proceed without the CT and NE data in our analysis, though this isn't ideal.


## Anaysis Reproduction Steps

1. Download the us_cities_by_state_SEPT.2023.csv file from this link (https://drive.google.com/file/d/1khouDmMaZyKo0y5WkFj4lu7g8o35x_98/view) and add it to the raw_data folder.

2. Download population estimates for each US state from the Census Bureau website. The excel is named "Annual Estimates of the Resident Population for the United States, Regions, States, District of Columbia and Puerto Rico: April 1, 2020 to July 1, 2022 (NST-EST2022-POP)" and can be found at this link (https://www.census.gov/data/tables/time-series/demo/popest/2020s-state-total.html). Save the downloaded file as a csv to the raw_data folder.

3. Download the states in each region from the course website (https://docs.google.com/spreadsheets/d/14Sjfd_u_7N9SSyQ7bmxfebF_2XpR8QamvmNntKDIQB0/edit#gid=0). The file is named "US States by Region - US Census Bureau" and should be downloaded as a csv and saved to the raw_data folder.

4. The "US States by Region - US Census Bureau" file requires some processing before it can be used. Namely, it needs to be reorganized into a table with a region and division for each state rather than the raw, human-readable, indented format it comes in. While we could make these changes programmatically, we believe they are faster to make by hand. The user should "fill" all the blank cells with the REGION or DIVISION at the top of the blank section. As an example, the file should transition from figure 1 to figure 2. Values should not be filled in to the left or right. Rows which contain blank values should be removed (figure 3). Save the new datafile to the raw_data folder as "states_by_region.csv".

REGION      | DIVISION    | STATE
Northeast   |             |
            | New England |
            |             | Connecticut
            |             | Maine
            |             | Massachusetts
            |             | New Hampshire
            |             | Rhode Island
            |             | Vermont
Figure 1 - Raw data in "US States by Region - US Census Bureau"

REGION      | DIVISION    | STATE
Northeast   |             |
Northeast   | New England |
Northeast   | New England | Connecticut
Northeast   | New England | Maine
Northeast   | New England | Massachusetts
Northeast   | New England | New Hampshire
Northeast   | New England | Rhode Island
Northeast   | New England | Vermont
Figure 2 - Values are "filled" into the blank spaces. Values are not filled left to right e.g., in row 1 and 2 for "Northeast" and "New England"

REGION      | DIVISION    | STATE
Northeast   | New England | Connecticut
Northeast   | New England | Maine
Northeast   | New England | Massachusetts
Northeast   | New England | New Hampshire
Northeast   | New England | Rhode Island
Northeast   | New England | Vermont
Figure 3 - Rows with blanks are removed. The final output is saved to /clean_data/states_by_region.csv

5. Set up your access token for use in the ORES API if you don't have one already. Per Professor McDonald, there is a guide which may be helpful (https://api.wikimedia.org/wiki/Authentication), but in the event that it is not, use the below information:

"The documentation talks about using a "dashboard" for managing authentication tokens. That's a rather generous description for what looks like a simple list of token things. You might have a hard time finding this "dashboard". First, on the left hand side of the page, you'll see a column of links. The bottom section is a set of links titled "Tools". In that section is a link that says [Special pages](https://api.wikimedia.org/wiki/Special:SpecialPages) which will take you to a list of ... well, special pages. At the very bottom of the "Special pages" page is a section titled "Other special pages" (scroll all the way to the bottom). The first link in that section is called [API keys](https://api.wikimedia.org/wiki/Special:AppManagement). When you get to the "API keys" page you can create a new key.

The authentication guide suggests that you should create a server-side app key. This does not seem to work correctly - as yet. It failed on multiple attempts when I attempted to create a server-side app key. BUT, there is an option to create a [Personal API token](https://api.wikimedia.org/wiki/Authentication) that should work for this course and the type of ORES page scoring that you will need to perform.

Note, when you create a Personal API token you are granted the three items - a Client ID, a Client secret, and a Access token - you shold save all three of these. When you dismiss the box they are gone. If you lose any one of the tokens you can destroy or deactivate the Personal API token from the dashboard and then create a new one.

The value you need to work the code below is the Access token - a very long string."

**All scripts can be found in the "scr" folder, per the recommended file structure in the week 1 reading. Please ignore any ".ipynb_checkpoints" as these are an artifact of using Jupyter with github.**

6. Run the data_acquisition script, following the markdown comments, to get the ORES ranking data for each page.

7. Run the data_processing script, following the markdown comments.

8. Run the data_analysis script to output final analysis and rankings.


## Research Implications

Examining the data, we find that the Census Divisions with the most articles/population appear to be located in the Northeastern United States. This is relatively unsurprising given the number of people in the Northeastern US to write articles, as well as the historical significance of many of the cities. I was surprised to see that the Pacific Division had the fewest articles per person given that Wikipedia is a digital entity and there are many tech-savvy people on the West Coast. However, it's possible there are fewer towns in this part of the country relative to the number of people. I found it amusing that the top articles/state by density were located in Vermont, North Dakota, and Maine. My guess would be that these states' relatively low populations are offset by some passionate article builders.
    With respect to quality/population I am happy to see that the more sparsely populated "West North Central" has such high-quality articles per population. It is no surprise that the Middle Atlantic and New England Divisions would come next, given the historical richness and national interest in their cities and states. Being from the Southern US myself, I was unsurprised to see that many Southern states came in lower in article quality. If ORES is simply checking off a list of features to determine article quality (e.g., titles, figures, numbers of paragraphs) then I would think people in the South may have less interest in building articles per town or there are fewer towns. However, I have seen first-hand the bias surrounding the percieved education-levels of Southern people and wouldn't be surprised if that had woven into the model.
    (1) Given this dataset was to be divided by states I assumed that the bias would present itself in a North vs. South fashion. This stems from the stereotype that people in the North are better educated than people in the South and thus would be more interested, willing, or capable of writing many high-quality articles. (2) In the course of processing and analysis I found it interesting that Connecticut and Nebraska had no scraped articles about their towns. I would assume that this gap came from a formatting issue, but it's interesting that it would happen to two states with such different characteristics as Connecticut and Nebraska. Both removing and including these states in our analysis would have implications for the average articles/person or quality/person. We chose to remove them given we don't truly believe there are no articles written for either state - but this is introducing a form of selection bias. (6) A researcher could remove this selection bias by writing separate programs to scrape Connecticut and Nebraska data with approximately equal efficacy to attempt to normalize the article distribution across states.
