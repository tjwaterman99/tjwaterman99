# Web Scraping with Github Actions

The tools available for building web scrapers have improved significantly since [my very first webscraping project](https://github.com/tjwaterman99/IAAF-Stats) back in 2015.

Maybe the most interesting new tool for building web scrapers is [Github Actions](https://github.com/features/actions), a service for running arbitrary code directly on Github.

Using Github actions, you don't have to configure or manage a server yourself. Instead, you can configure Github Actions to run on a schedule, download the data you want to scrape, and save it to your repository. You can then publish the scraped data using [Github releases](https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/managing-releases-in-a-repository), making the scraped data easily available for others.

I wanted to try this strategy out myself, so I built a [US Box Office Revenues tracker](https://github.com/tjwaterman99/boxofficemojo-scraper) on Github Actions. 

Each day Github Actions will run a web scraper that downloads the latest revenue data from boxofficemojo.com and saves it to the repository. It then parses the scraped data and publishes a polished dataset that's easy to download from a single url on the project's [releases page](https://github.com/tjwaterman99/boxofficemojo-scraper/releases). 

You can try out the polished dataset yourself.

```python
import pandas as pd

url = 'https://github.com/tjwaterman99/boxofficemojo-scraper/releases/latest/download/revenues_per_day.csv.gz'
df = pd.read_csv(url, parse_dates=['date'], index_col='id')
df.head()
```

Since that project went so well, I wanted to share more details about how to build a Github Actions web scraper for others that are developing their own web scrapers. 

Github actions is also free to use for public repositories, so everything that I'm sharing below doesn't cost any money. That said, there are size limits on the repositories themselves: no individual file in your repo can be larger than 100MB, and the total repository can not be larger than 10GB. But that's actually _more_ than enough space for even large web scraping projects: with 10GB you can store millions of web pages, and billions of parsed web pages.

## 1. Scrape the data with a github action

- Write a script to download your data
- Schedule the script using Github Action's `scheduled` parameter.

## 2. Commit the scraped data to your repository

- Using git as your "database" works fine for many projects. My project has just ~100MB of data _uncompressed_

## 3. Transform your scraped data with dbt

- Try to keep as much of your business logic in here as possible. 
- If you don't like SQL, you can skip this step, but using DBT will make it easier to maintain your project

## 4. Publish your data with github releases

- 
