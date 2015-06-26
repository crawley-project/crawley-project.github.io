---
layout: post
title: "Crawley 0.2.4 released"
quote: "New release of crawley cloud."
image:
      url: /media/2011-11-14-new-crawley-release/cover.jpg
video: false
comments: true
theme_color: 302F2D
---

#Crawley 0.2.4"

### Features

* High Speed WebCrawler built on Eventlet.
* Supports relational databases engines like Postgre, Mysql, Oracle, Sqlite.
* Supports NoSQL databased like Mongodb and Couchdb. **New!**
* Export your data into Json, XML or CSV formats. **New!**
* Command line tools.
* Extract data using your favourite tool. XPath or Pyquery (A Jquery-like library for python).
* Cookie Handlers.
* Very easy to use (see the example).

------------------------------------------------------------------

### To install crawley run

{% highlight bash %}

~$ python setup.py install

{% endhighlight %}


### or from pip

{% highlight bash %}

~$ pip install crawley

{% endhighlight %}


------------------------------------------------------------------

### To start a new project run

{% highlight bash %}

~$ crawley startproject [project_name]
~$ cd [project_name]

{% endhighlight %}


------------------------------------------------------------------

### Write your Models

{% highlight python %}

    """models.py"""

    from crawley.persistance import Entity, UrlEntity, Field, Unicode

    class Package(Entity):

    #add your table fields here
    updated = Field(Unicode(255))
    package = Field(Unicode(255))
    description = Field(Unicode(255))

{% endhighlight %}

------------------------------------------------------------------

### Write your Scrapers

{% highlight python %}

""" crawlers.py """

from crawley.crawlers import BaseCrawler
from crawley.scrapers import BaseScraper
from crawley.extractors import XPathExtractor
from models import *

class pypiScraper(BaseScraper):

    #specify the urls that can be scraped by this class
    matching_urls = ["%"]

    def scrape(self, response):

        #getting the current document's url.
        current_url = response.url
        #getting the html table.
        table = response.html.xpath("/html/body/div[5]/div/div/div[3]/table")[0]

        #for rows 1 to n-1
        for tr in table[1:-1]:

            #obtaining the searched html inside the rows
            td_updated = tr[0]
            td_package = tr[1]
            package_link = td_package[0]
            td_description = tr[2]

            #storing data in Packages table
            Package(updated=td_updated.text, package=package_link.text, description=td_description.text)


class pypiCrawler(BaseCrawler):

    #add your starting urls here
    start_urls = ["http://pypi.python.org/pypi"]

    #add your scraper classes here
    scrapers = [pypiScraper]

    #specify you maximum crawling depth level
    max_depth = 0

    #select your favourite HTML parsing tool
    extractor = XPathExtractor

{% endhighlight %}

### Configure your settings

{% highlight python %}

""" settings.py """

import os
PATH = os.path.dirname(os.path.abspath(__file__))

#Don't change this if you don't have renamed the project
PROJECT_NAME = "pypi"
PROJECT_ROOT = os.path.join(PATH, PROJECT_NAME)

DATABASE_ENGINE = 'sqlite'
DATABASE_NAME = 'pypi'
DATABASE_USER = ''
DATABASE_PASSWORD = ''
DATABASE_HOST = ''
DATABASE_PORT = ''

SHOW_DEBUG_INFO = True

{% endhighlight %}

------------------------------------------------------------------

### Finally, just run the crawler

{% highlight bash %}

~$ crawley run

{% endhighlight %}

## Versions

Here is a table with all Thinny's versions:

|----
| Version | Platform | Release date
|:-:|:-:|:-:|:-:
| [0.1.0](https://github.com/crawley-project/crawley/releases/tag/v0.1.0)| Python | October 2011
| [0.2.0](https://github.com/crawley-project/crawley/releases/tag/0.2.0) | Python | October 2011
| [0.2.1](https://github.com/crawley-project/crawley/releases/tag/0.2.1) | Python | October 2011
| [0.2.4](https://github.com/crawley-project/crawley/releases/tag/0.2.4) | Python | December 2011
| [0.3.0](https://github.com/crawley-project/crawley/releases/tag/0.3.0)| Python | _Soon..._[^2]
|----

## Download

> Crawley 0.2.4 is already [available for download on GitHub](https://github.com/crawley-project/crawley/releases).

-----
Want to see something else added or report a bug? [Open an issue](https://github.com/jmg/crawley/issues/new).

[^1]: This file is placed in your root directory. It's the main file of configuration. For more information, read [the docs](http://jekyllrb.com/docs/configuration/).
[^2]: See the [issues list](https://github.com/jmg/crawley/issues).
