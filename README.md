# data-512-homework_2

## Goal
The goal of this assignment is to examine potential data bias via Wikipedia articles. Specifically, we will be checking to see which states have the highest and lowest coverage compared to their populations, highest and lowest porportion of high quality articles, and a ranking of regions by articles/person and proportion of high quality articles. There is a reflection at the end of this README which discusses findings further.


## Licenses & API Information

The code used in this project is licensed under the MIT license - more information can be found in the license file in the top-level repo.

The article source data comes from English Wikipedia, the text of which is licensed under "Creative Commons Attribution Share-Alike license" (https://en.wikipedia.org/wiki/Wikipedia:Text_of_the_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)

We will be using the MediaWiki REST API for English Wikipedia. To get more information on the API please use the following link: https://www.mediawiki.org/wiki/API:Main_page. The following link may also be helpful when looking for more documentation: https://www.mediawiki.org/wiki/API:Info

We will also be using the ORES API. Information on the API itself can be found at https://www.mediawiki.org/wiki/ORES, with original API documentation from https://ores.wikimedia.org/docs and new LiftWing documentation from https://wikitech.wikimedia.org/wiki/Machine_Learning/LiftWing/Usage.

We will leverage code developed by Dr. David W. McDonald for use in Data 512  which is provided under Creative Commons CC-BY license. (https://creativecommons.org/ and https://creativecommons.org/licenses/by/4.0/). The file can be found at this link: https://drive.google.com/drive/folders/1FtvWV31DHE8HIMdEsPGuCXPz0PMvShfl


## Data File Information

### Input data
We will input 3 sources of raw data: NST-EST2022-POP.xlsx, US States by Region - US Census Bureau - Sheet1.csv, and us_cities_by_state_SEPT.2023.csv. Download information can be found in the "Analysis Reproduction Steps" of this document below.

NST-EST2022-POP.xlsx has the following variables and definitions:
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
page_info.json - This file stores the intermediate page information for each article in our cities by state list. The articles are from us_cities_by_state_SEPT.2023.csv, and the page information comes from the MediaWiki REST API. It is structured as a JSON with article_title as the key and the lastrevid (last revision id) as the value.

states_by_region.csv - This file contains the cleaned data from "US States by Region - US Census Bureau". The attributes are the same as described in "Input data" above, and were cleaned as detailed in step 4 of "Analysis Reproduction Steps".

ores_scores.json - This file contains the article titles (keys) and ORES scores (values) for files which successfully processed through ORES scores the first time.

new_ores_scores.json - This file contains the article titles (keys) and ORES (values) scores for files which produced a key error when run through the ORES model.

final_ores_scores.json - This file contains the article titles (keys) and ORES scores (values) for the originally run files and the files contained in new_ores_scores.

### Final outputs
final outputs - describe values of fields


## Known Issues and Special Considerations

### Consideration 1
Professor McDonald noted, "Crawling Wikipedia categories to identify relevant page subsets can result in misleading and/or duplicate category labels. Just skimming the category page (listed above), you can see that sub-categories have limited consistency in their naming conventions. A data crawl can result in possible duplicate articles linked from differently named sub-categories. Naturally, the data crawl attempts to resolve some of these problems, but not all may have been caught."

In our data_acquisition code we check to see if all the page titles are unique and we find that they are not. To reduce later processing time we remove duplicate entries (same state, page_title, and url) from the file, eliminating ~638 rows. Then we find there are page titles which do not represent cities (e.g., 'Population', '2020 United States census') and we remove these entries from the eligible articles to be analyzed.

We could further clean the files e.g., by checking to see if each state in the "state" column matches the state in the "page_title" column, but we choose not to do this in favor of saving time to pull the data.

### Consideration 2
The data in the data_acquisition script takes significant time to pull. Obtaining the page information will take ~2 hours, and running the ORES script can take ~5 hours. 

### Consideration 3
There are articles which ORES may not be able to return a score for - this is indicated by the API return value being "None". The list of articles is stored and printed out in the Python Notebook.


## Anaysis Reproduction Steps

1. Download the us_cities_by_state_SEPT.2023.csv file from this link (https://drive.google.com/file/d/1khouDmMaZyKo0y5WkFj4lu7g8o35x_98/view) and add it to the raw_data folder.

2. Download population estimates for each US state from the Census Bureau website. The excel is named "Annual Estimates of the Resident Population for the United States, Regions, States, District of Columbia and Puerto Rico: April 1, 2020 to July 1, 2022 (NST-EST2022-POP)" and can be found at this link (https://www.census.gov/data/tables/time-series/demo/popest/2020s-state-total.html). Save the downloaded file to the raw_data folder.

3. Download the states in each region from the course website (https://docs.google.com/spreadsheets/d/14Sjfd_u_7N9SSyQ7bmxfebF_2XpR8QamvmNntKDIQB0/edit#gid=0). The file is named "US States by Region - US Census Bureau" and should be downloaded as a csv and saved to the raw_data folder.

4. The "US States by Region - US Census Bureau" requires some processing before it can be used. Namely, it needs to be reorganized into a table with a region and division for each state rather than the raw, human-readable, indented format it comes in. While we could make these changes programmatically, we believe they are faster to make by hand. The user should "fill" all the blank cells with the REGION or DIVISION at the top of the blank section. As an example, the file should transition from figure 1 to figure 2. Values should not be filled in to the left or right. Rows which contain blank values should be removed (figure 3). Save the new datafile to the clean_data folder as "states_by_region.csv".

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

6. Run the data_acquisition script, following the markdown comments, to get the ORES ranking data for each page.


## Research Implications