# Install

The blog is built with [mkdocs](https://www.mkdocs.org/), which requires Python 3.6+.

```
python --version
```

Install the Python requirements.

```
pip install -r requirements.txt
```

Build the site in development mode.

```
mkdocs serve
```

# Deploying

Deploying is done via github pages: files under the `site/` directory are published to https://tjwaterman99.github.io/tjwaterman99/.

You can build the site and publish it with `mkdocs`.

```
mkdocs gh-deploy
```
