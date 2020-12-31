---
date: 2020-12-30
---

# Web Scraping with Github Actions

The tools available for building web scrapers have improved significantly since [my very first webscraping project](https://github.com/tjwaterman99/IAAF-Stats) back in 2015.

Maybe the most interesting new tool for building web scrapers is [Github Actions](https://github.com/features/actions), a service for scheduling any code you want to run directly on Github.

Using Github actions, you don't have to set up a server to run your scraper every night. Instead, you can configure Github Actions to run your scripts on a schedule, and it automatically starts and stops a server for you. 

For a web scraping project, you might have Github actions run a script to scrape the data from a website, and then save save the scraped data to your repository.

Github actions is also free to use for public repositories, so building a web scraper won't cost any money. That said, there are certain limits: you can run your scripts for 3,000 minutes per month, no individual file in your repo can be larger than 100MB, and the total repository can not be larger than 10GB. 

But that's actually more than enough time and space for even large web scraping projects: with 10GB you can store millions of whole web pages, and billions of parsed web pages.

Github actions is also quite easy to set up, since it uses a yaml file for configuring how to run your script. Here's a simple example:

```yml
name: Scrape  # The name of your Workflow

on:
  schedule:  # Tells Github to run this Workflow every night
    - cron: "23 8 * * *"

jobs:
  scrape-latest:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repo
        uses: actions/checkout@v2
      - name: Set up Python
        uses: actions/setup-python@v2.0.0
        with:
          python-version: '3.7'
      # Tells Github to run the file `scraper.py` from your repository
      - name: Run Scraper
        run: python scraper.py scrape
```

The script that actually downloads the data is just the python file `scraper.py` - you can see that the script is being ran in the last step of the workflow above.

Once you've scraped the data you want, you can then publish that data using [Github releases](https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/managing-releases-in-a-repository), making its data easily available for others.

I wanted to try out this strategy myself, so I built a [US Box Office Revenues tracker](https://github.com/tjwaterman99/boxofficemojo-scraper) based on Github actions.

You can download the latest polished dataset yourself.

```python
import pandas as pd

url = 'https://github.com/tjwaterman99/boxofficemojo-scraper/releases/latest/download/revenues_per_day.csv.gz'
df = pd.read_csv(url, parse_dates=['date'], index_col='id')
df.head()
```

The scraper that produces that dataset follows the strategy pretty much exactly: each day Github Actions runs a script that downloads the latest revenue data from boxofficemojo.com and saves it to the repository. Another script then parses the scraped data and publishes a polished dataset that's easy to download from a single url on the project's [releases page](https://github.com/tjwaterman99/boxofficemojo-scraper/releases).

Overall I've been very impressed with Github's features - Github actions for running a CI/CD service, managed releases, and now even [Codespaces](https://www.tjwaterman.com/github-codespaces), an on-demand development environment. With other companies like Heroku, Netlify, and Snowflake, it's never been faster or easier to build software.

It's genuinely amazing how far developer tools have come since 2015.
