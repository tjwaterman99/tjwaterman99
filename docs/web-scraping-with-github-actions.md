# Web Scraping with Github Actions

The tools available for building web scrapers have improved significantly since my very first project back in 2015.

For me, the most exciting new tool for building web scrapers is [Github Actions](https://github.com/features/actions), a service for running arbitrary code directly on Github. Using Github actions, you can download the data you want to scrape on a schedule, save it to your repository, and then publish the scraped data using [Github releases](https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/managing-releases-in-a-repository), making the scraped data easily available for others.

That was the strategy I used for my [US Box Office Revenues tracker](https://github.com/tjwaterman99/boxofficemojo-scraper) - each day Github Actions downloads the latest revenue data from boxofficemojo.com, and then publishes a polished dataset that's easy to download from a single url on the project's [releases page](https://github.com/tjwaterman99/boxofficemojo-scraper/releases). You can try it out yourself below.

```python
import pandas as pd

url = 'https://github.com/tjwaterman99/boxofficemojo-scraper/releases/latest/download/revenues_per_day.csv.gz'
df = pd.read_csv(url, parse_dates=['date'], index_col='id')
df.head()
```

Since that project went so well, I wanted to share the "architecture" for others that are developing their own web scrapers. Github actions is free to use for public repositories too, so everything that I'm sharing below is cost-free.

## Scrape the data with a github action

- Write a script to download your data
- Schedule the script using Github Action's `scheduled` parameter.

## Commit the scraped data to your repository

- Using git as your "database" works fine for many projects. My project has just ~100MB of data _uncompressed_

## Transform your scraped data with dbt

- Try to keep as much of your business logic in here as possible. 
- If you don't like SQL, you can skip this step, but using DBT will make it easier to maintain your project

## Publish your data with github releases

- 

- Did my first web scraping project 5 years ago, and decided to try another scraping project mostly to understand how difficult it might be to maintain a curated, publicly available dataset.
- https://www.armchairanalysis.com/
- the world has lots of new useful tools
- 
- Using dbt for business logic transformations
- Using [Github releases](https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/about-releases) for publishing your datasets

