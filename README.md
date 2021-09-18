# JokeyJokes
Using Machine Learning to generate joke concepts

1) Scrape Clickhole (or any website) for content to train Machine Learning algorithm on
2) Clean up text generated from scraping
3) Publish training data text on a publicly accessible website (such as Github...)
4) Use existing Google collab to train against the data, and generate new jokes
_______________________

## 1) Scrape Clickhole (or any website) for content to train Machine Learning algorith on.

I used a python tool called "Scrapy" https://scrapy.org/ 
Should be able to install with `pip install scrapy`, or `python3 -m pip install Scrapy` or similar.  See website or stack overflow for installation help if having issues.

### Long Install (not very long):

Navigate terminal to a new directory you want to work in, and run `scrapy startproject tutorial`

Navigate terminal to `tutorial` sub-directory, where a `scrapy.cfg` file should have been created.

Create a .py file in the `tutorial/spiders/` directory called `quotes_spider.py`.  Populate that py file with the content in the `tutorial/spiders/quotes_spider.py` file in this repo:
```
import scrapy

# to run, nav terminal to directory with scrapy.cfg file, and run `scrapy crawl clickhole -o FullArticles.json`
class ClickholeSpider(scrapy.Spider):
    name = "clickhole"
    start_urls = [
        'https://clickhole.com/',
    ]

    def parse(self, response):

        yield { 
            'headline': response.css("h2.post-title a::text").getall(),
            # 'article': response.css("div.post-content p span::text").getall(),
            }

        # for article in response.css("h2.post-title a::attr(href)"):
        #     yield scrapy.Request(article.get(), callback=self.parse)

        next_page = response.css("a.next::attr(href)").get()
        if next_page is not None:
            yield scrapy.Request(next_page, callback=self.parse)
```

### Short Install: just use the files provided in this repo.  Still need to run `pip install scrapy`, or `python3 -m pip install Scrapy` or similar though.

To run the scraper, navigate terminal to the directory with the `scrapy.cfg` file in it, and run `scrapy crawl clickhole -o Headlines.json`.  This should generate a huge list of every article headline in the entire Clickhole website, and save it to a file called `Headlines.json` in the same directory as the terminal.

To collect full text for every article as well, un-comment the commented out lines in the `quotes_spider.py` file:
```
import scrapy

# to run, nav terminal to directory with scrapy.cfg file, and run `scrapy crawl clickhole -o FullArticles.json`
class ClickholeSpider(scrapy.Spider):
    name = "clickhole"
    start_urls = [
        'https://clickhole.com/',
    ]

    def parse(self, response):

        yield { 
            'headline': response.css("h2.post-title a::text").getall(),
            'article': response.css("div.post-content p span::text").getall(),
            }

        for article in response.css("h2.post-title a::attr(href)"):
            yield scrapy.Request(article.get(), callback=self.parse)

        next_page = response.css("a.next::attr(href)").get()
        if next_page is not None:
            yield scrapy.Request(next_page, callback=self.parse)
```

To collect only text for a single article tag (e.g. "wow" or "so sad", or any of the tags used by Clickhole), change the `start_urls` to 

`https://clickhole.com/tag/wow/`
or 
`https://clickhole.com/tag/probably-illegal/`
or whatever tag you'd like.

## 2) Clean Up Generated Text

The text generated by scraping off the html will be a little messy, with artifacts that you don't want messing up a machine learning training data set.  Just copy / paste the text data into your favorite text editor (I'm a fan of Visual Studio Code), and find / replaceAll until the text data is looking good and ready to be trained against.

## 3) Use existing Google collab to train against the data, and generate new jokes

Here's a helpful google collab that takes care of all the hard stuff: 
https://colab.research.google.com/drive/1VLG8e7YSEwypxU-noRNhsv5dW4NfTGce#scrollTo=aeXshJM-Cuaf

It's a tutorial which by default trains off of a bunch of text from Shakespear plays, and uses it to auto-generate new "Shakespearean" content.

In order to modify it for our purposes, upload the text you want to train against onto the side-bar of the google colab screen, and edit the following line:
`file_name = "Shakespear.txt"`

to

file_name = "AmericanVoices.txt"

Or, whatever you named your training text file

That's it!  Then, just let the Google Collab tutorial do all the heavy lifting.  Walk through from top to bottom, executing every code block until it auto-generates content in the "Generate Text" stage.

There are lots of ways to customize and tinker with the algorithm, but this is enough to get you started.
