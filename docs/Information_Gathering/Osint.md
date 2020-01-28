# OSINT

## Tracking findings

* Dradis
* Faraday
* Magitree

## Search Engines

* Google (Dorks)
* Bing
* AOL
* Ask
* dogpile.com
* Pandastats.com

### Google Dorks

```
# to see the cached content of the website.
cache: www.website.com
``` 

```
# to see sites that link to www.website.com
link: www.website.com
```

```
# looks for `keyword` only on www.website.com
keyword site: www.website.com
```

```
# looks for files of type `filetype` connected to `keyword` - can be combined with `site:`.
keyword filetype:pdf
```

*Useful Link* : [Google Hacking Database](https://www.exploit-db.com/google-hacking-database/).

### Financial Info

For company working in US or with US government, there are codes `DUNS` or `CAGE`.
These codes can be retrieved from [SAM](https://www.sam.gov/).

Once the code is known, it is possible to use [EDGAR](http://www.sec.gov/edgar.shtml) to search
financial information about the company.

Other useful places to look for are:

* http://www.crunchbase.com/ (company, people, investors, mostly US based)
* http://www.inc.com/ 

### Job postings

Platform for Job Advertising are very useful to understand company operations and technological
stack. 

* LinkedIn
* Indeed
* Monster
* Careerbuilder
* Glassdoor
* Simplyhired
* Dice

### Harvesting Documents

In addition to the already mentioned Google Dorks (site+filetype) there are tools such as

* FOCA (Windows only)
* [The Harvester](https://github.com/laramies/theHarvester)

```bash
theharvester -d domain.com -l 100 -b google[linkedin|..]
```

Limits the research to domain `domain.com`, limited to 100 results using Google as data source.


### Cached and archived sites

In addition to Google Dork (cache), it is possible to use some service such as
http://www.archive.org/ to retrieve the historical content of a page.


## Social Media

In addition to classic social media (Facebook, Twitter, Linkedin etc.):

* http://www.pipl.com/
* http://www.spokeo.com/
* http://www.peoplefinders.com/
* http://www.crunchbase.com/
