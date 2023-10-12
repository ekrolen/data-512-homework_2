# data-512-homework_2

## Goal


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
Geographic Area:
April 1, 2020 Estimates Base:
Population Estimate (as of July 1):
    2020:
    2021:
    2022:


### Intermediary data files 
data description, attribute description, provenance information, description of relevant resources and documentation w/ hyperlinks

### Final outputs
final outputs - describe values of fields

## Known Issues and Special Considerations
##### HOW DO I HANDLE ANY DATA INCONSISTENCIES WHICH COME UP?

## Anaysis Reproduction Steps

1. Download the us_cities_by_state_SEPT.2023.csv file from this link (https://drive.google.com/file/d/1khouDmMaZyKo0y5WkFj4lu7g8o35x_98/view) and add it to the raw_data folder.

2. Download population estimates for each US state from the Census Bureau website. The excel is named "Annual Estimates of the Resident Population for the United States, Regions, States, District of Columbia and Puerto Rico: April 1, 2020 to July 1, 2022 (NST-EST2022-POP)" and can be found at this link (https://www.census.gov/data/tables/time-series/demo/popest/2020s-state-total.html). Save the downloaded file to the raw_data folder.

3. Download the states in each region from the course website (https://docs.google.com/spreadsheets/d/14Sjfd_u_7N9SSyQ7bmxfebF_2XpR8QamvmNntKDIQB0/edit#gid=0). The file is named "US States by Region - US Census Bureau" and should be downloaded as a csv and saved to the raw_data folder.

4. Run the data_acquisition script to get the ORES ranking data for each page.

## Research Implications