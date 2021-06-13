---
layout: post
title:      "My first project! "
date:       2021-06-13 23:45:49 +0000
permalink:  my_first_project
---

## A CLI App for Recommending Stocks

Since this is my first blog post, I'm going to start with the "who" and the "why" before I get to the "what."

My name is Roshanak and I did not recently start this coding bootcamp. Four years ago I left my career in the craft brewing industry and got my first job as a data analyst at an almond processing co-op. I would need more than two hands to count the number of times people have asked me why I would actively choose to leave the "cool kids table" of industries for a desk job working at a nut factory. My answer is simple: I wanted to love my job the way brewers love the smell of sweet wort when they're mashing in at 7am, the way they can talk endlessly about yeast byproducts and the merits of an extended lagering process. My brewing professor has written at least a dozen books on the topic and yet still approaches every lecture with a reverence that aptly earned him the title of the Pope of Foam. 

At the end of those shorter winter days when my coworkers were delighted to go home and crack open a stout, I reveled in the hours I could spend tucked warmly in my spreadsheets, figuring out how to optimize my meal preps for higher nutrition at lower costs, or organizing my budget sheet so that I could more efficiently review my expenses against my limited income. The brewers taught me it was possible to love my work, I just had to realize I didn't love the work I was getting paid to do. 

18 months into working as an analyst, I had built a fearsome reputation as a numbers ninja with unrivaled speed and agility in the Excel arena. Having automated all of my routine reports using customized templates, reference formulas and VBA macros, I had ample time to lend my expertise to other departments' projects where I learned that Excel's capabilities were not limitless. Not everyone is an analyst or an Excel power user, so when working on cross-departmental projects, like product launches, we needed custom tools with simpler user interfaces that could organize our intricate processes, protect our trade secrets, and successfully guide our operations without losing vital inputs in the rushing currents of overflowing Outlook inboxes. I set out to find a better way and in the summer of 2019 my coding bootcamp journey began. 

Two years, a 400-mile move, one pandemic, a couple job changes and a masters degree in business analytics later, I'm finally ready to submit my first project! To be fair, I had finished this project once by Dec 2019 but web scraping can be a fickle fiend. The website I scraped for my first go (CNN Business) completely overhauled their site before I had a chance to record my project walkthrough, none of my scraper methods worked, and in the throes of 2020 my motivation fell to the wayside. 

But like a good ale yeast, championing its fermentation, I rose. I finally tried again. I was too stuborn to come up with a new concept because I liked the first one I had and I wanted a tool that was capable of showing me information this way. I am proud to present my first project, a CLI app that scrapes stock market information by sector and shows overall percent change from the previous day. The user can then go one level deeper to review the list of individual stocks within a sector that either have a recommendation of "strong buy" or "strong sell". The data that was scraped for this project comes from https://www.tradingview.com/markets/stocks-usa/sectorandindustry-sector/ where the information is displayed in a similar fashion. 

### Project Setup

Github Link to my project

This project employs Object Oriented Ruby. What this means is that the code is organized around the objects - *or classes* - that it represents. These classes contain methods, blocks of code representative of a procedure that a class instance or the class itself are responsible for performing. I used 5 classes to create this app, each with their own unique class- and instance-methods:

**CLI:** Whenever a user opens the cli app in the terminal, each user session is an instance of the CLI class. This class has a "call" method that guides the user through their experience. It welcomes the user, shows a disclaimer about using the app, calls the sector scraper and then begins a loop which takes the user's inputs to instruct the flow of information.   

**SectorScraper:** When instantiated, this class is responsible for going to the Trading View website and using Nokogiri to scrape the data for the Sectors. Each sector is housed in a table row. Using an "each" method to iterate over all of the table rows, the SectorScrape creates new instances of the Sector class for each market sector.

**Sector:** A sector, or category of companies, has a name, a percentage change from the previous day, and a URL which is used to send the StockScraper to the sector's website to scrape for its stocks. 

**StockScraper:** When instantiated, this class is responsible for going to the Trading View website for a particular sector and using Nokogiri to scrape the data for the sector's stocks. Each stock is housed in a table row. Using an "each" method to iterate over all of the table rows, the StockScraper creates new instances of the Stock class for each company. One of the attributes for Stocks is sec_name which is the sector that the stock belongs to. Instead of scraping for the sector name, the user's input is used to set the sector name. 

**Stock:** Each company has a ticker symbol, a company name, a stock price, a percentage change in price from the previous day's close, and a recommendation ("Strong Buy", "Buy", "Neutral", "Sell", and "Strong Sell"). The stock also belongs to a particular sector.

### Getting funky with formatting: Color in a CLI App

As an analyst, the greatest lesson I learned was that you can know all the complicated math and statistical methods you want, but if you can't show your audience the data in a way that is easy to understand and engaging, you've lost any buy-in for your results. 

The same can be said for coding. You can build a super functional app, but if the user can't understand how to work with it and lacks the visual cues to guide their experience, it'll likely be a bust.

Below I will discuss two gems, Colorize and Lolcat, that I had fun incorporating into my project to bring color into the otherwise lackluster interface of the terminal.

I added both of these gems to my project's Gemfile and then required them in my environment file (/lib/stock_recommender.rb)

##### Colorize

Colorize is a gem that provides the developer with methods to add color or emphasis (bold, italics, etc) to their text. 

You can add these formatting methods by simplying chaining them to the end of a puts statement like this: 

> puts "Hello World".bold.green

and it will display in the terminal as bold and green.

I used this formatting tool to give visual distinction in the following ways:
* yellow for the welcome statement
* yellow for the table headings
* green for user input options
* red for errors
* pink for goodbye

I found [this page](http://www.devdungeon.com/content/colorize-ruby-terminal-output) a little more helpful on how to use the gem as opposed to it's README

##### Lolcat

This was a fun gem that allows for various rainbow color formattings. Because my app is about stocks and provides recommendations for buying or selling, I thought it would be appropriate to include a disclaimer at the beginning of my app (anyone listen "Snacks Daily"?) especially since these were not my recommendations, but were coming from the website being scraped. 

Originally I thought to use the colorize gem to make the disclaimer text red. But I stumbled upon lolcat and decided to make the disclaimer rainbow color to draw extra attention to it. 

Implementing lolcat is different from colorize. Instead of chaining, it needs to be written in a way that commands the terminal to print a block of text:

> system('cat ./lib/stock_recommender/disclaimer.txt | lolcat)
 
Instead of typing my disclaimer directly into the method like I did with the other puts statements, I created a text file containing the disclaimer. Then using the system command, I directed it to read from that text file with the lolcat formatting applied to it. 

Lolcat also has additional options like shimmering the rainbow. For more info on its additional features, here's a [link to it's github README](http://github.com/busyloop/lolcat/blob/master/README.md)


