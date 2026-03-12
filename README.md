# Data Harvesting: An Analysis of the Spanish Job Market

**UC3M Data Harvesting Project 2025–2026**

An analysis of the Spanish job market across public and private sectors using the Adzuna API and web scraping.

## Collaborators

- Samantha Lee Riordan
- Adela Solís
- Olivia Blantz

## Project Purpose

This project scrapes both private and public sector jobs in the Spanish job market from two independent sources: Adzuna's Developer API and the Portal de Empleo Público website. The code first runs analysis on the private sector with the API, and then employs web scraping to explore the public sector job postings. The results are two dataframes that can be used to analyze the structure of the Spanish labor market across these sectors, regions, and salary distributions.

## Project Outline

1. Brief Introduction
2. API — Private Sector: data collection, cleaning, and commentary analysis
3. Web Scraping — Public Sector: data collection, cleaning, and commentary analysis
4. Visualizations
5. Findings, Limitations, and Conclusions: lessons learned and acknowledgements of data and code limitations

## To Run Our Scraper

1. Open the file called `Spanish_Labor_Market_Analysis.Rmd`
2. Set your unique user-agent
3. Follow the instructions below for the Adzuna API
4. Ensure all necessary packages from the list below are installed
5. After that, you can run the whole scraper

## Adzuna API: Request Process and Use

1. Go to the [Adzuna Developer Portal](https://developer.adzuna.com/)
2. Sign up for a free account by pressing the "Register" button in the top right corner
3. Create a new application. We put our organization as Universidad Carlos III, and our website as this GitHub page
4. Upon completing the application, you will immediately see your **App ID** and **App Key**. Copy these — they will be your unique identifiers used when running our API scraper
5. In R, run the code:
   ```r
   usethis::edit_r_environ()
   ```
6. Add these two lines to the file that opens (your HOME `.Renviron`):
   ```
   ADZUNA_APP_ID="your_app_id_here"
   ADZUNA_APP_KEY="your_app_key_here"
   ```
7. Save the file and restart R

## Necessary Libraries

```r
# API
library(httr2)
library(tidyverse)
library(jsonlite)
library(xml2)
library(lubridate)

# Scraping
library(selenium)
library(robotstxt)

# Visualizations
library(leaflet)
library(crosstalk)
library(sf)
library(giscoR)
```

## Code Outputs

- **API CSVs** — Private sector job listings by category and AI/ML search phrases
- **Portal de Empleo Público CSV** — Public sector job listings with region, salary group, and qualification data
- **Multiple plots** covering data completeness, salary comparisons and distributions, and regional comparisons

## Tracking Job Listings Over Time (Adzuna API)

The same process is repeated across three focal points — total listings, listings per category, and listings per search term — to build longitudinal records for comparison over time. For each, a timestamp and the relevant counts are appended to a CSV file on each manual run:

| Data | CSV | Chunk |
|------|-----|-------|
| Total listings | `data/job_counts.csv` | `snapshot` |
| Listings by category | `data/job_cats.csv` | `snapshot_cats` |
| Listings by search term | `data/job_phrases.csv` | `snapshot_phrase` |

Each CSV is then visualized as a line chart to track changes across runs.

> **Note:** All three snapshot chunks have `eval = FALSE` and must be run manually to prevent duplicate entries on each knit.

## Modifications

This scraper provides users with the opportunity to make a few modifications, including changing functions to scrape a larger number of pages over a larger time period for the Adzuna API listings. This can be altered by changing the parameters `n_pages`, `results_per_page`, or `max_days_old`. Additionally, for the creation of the "phrases" dataframe in our API data collection, the words included could be modified to include more vocabulary related to Machine Learning and AI, or could be changed entirely to examine the presence of different phrases for other research questions.

Our scraper is designed to limit the number of requests to a polite number for the server, which can also be adjusted. It's important to note the suggested scraping hours (between 1:00 and 6:00 AM), as well as the pause time between hits. Our scraper does not comply with the recommended downtime within this script, but it can be adjusted.

For the Selenium driver, users should select their browser of choice (Firefox or Chrome) by navigating to the "Launch Selenium Server and Browser" section in the script outline, and setting the incorrect browser to `eval=FALSE, include=FALSE`. The script currently runs under Firefox unless this is changed.

## Limitations

### Adzuna API

The free version of the Adzuna API is subject to limits of 25 requests per minute, 250 per day, 1,000 per week, and 2,500 per month. This significantly constrains the volume of data that can be collected in a single session (~5% of available listings for Spain). This is a fundamental limitation on the representativeness of the data and although proportional page allocations have been used to mitigate some degree of misrepresentation, the datasets should be interpreted with caution and not treated as complete or random samples of the Spanish job market.

Specific limitations:

- Adzuna returns a short description snippet, rather than the full job posting text, meaning that AI keyword counts represent a lower bound of actual mentions.
- A very small number of listings include salary data. Salary analysis should be interpreted with caution.
- Some salaries may be Adzuna predictions rather than employer-posted figures (flagged via `salary_predicted` field).
- Setting `max_days_old = 30` limits results to the most recent 30 days, so results will differ across runs.

### Web Scraper

- As the structure of webpages can change over time, our scraper may need to be adjusted.
- Salary data, as well as phrase data, should be interpreted with caution. Users should be mindful of the amount of missing observations in the salary variables.
- It's vital to read the limitations and code notes carefully to correctly interpret outputs. 
