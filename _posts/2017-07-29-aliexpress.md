---
layout: post
title: A beginner's guide to Aliexpress data scraping and mining
tags: [python, scraping, aliexpress]
img: /img/aliexpress.png
---

**Table Of Content** <!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Introduction](#introduction)
- [Scraping](#scraping)
  - [Login](#login)
  - [Hot Products](#hot-products)
  - [Detail page](#detail-page)
  - [Feedback](#feedback)
- [Feedback Mining](#feedback-mining)
<!-- /TOC -->

# Introduction
Aliexpress is a popular place to buy and source good and cheap products. One of the time-consumping problem for retailers is how to find hot products on Aliexpress and how to choose suppliers for the chosen products. In this post, I will introduce how to use ```python, selenium``` to scrape products and reviews from Aliexpress, and how to do a simple analytics on the data.

# Scraping
## Login
When scraping Aliexpress pages, you will get banned very soon if you are not logged in. So the first step is to login with selenium and save the cookies into local files for later use.

![login](/img/aliexpress-login.png)

```python
import pickle
from selenium import webdriver

browser = webdriver.Firefox()


def get_cookies():
    browser.get("https://login.aliexpress.com/buyer.htm?return=https%3A%2F%2Fwww.aliexpress.com%2F&random=CEA73DF4D81D4775227F78080B9B6126")
    print('input your username and password in Firefox and hit Submit')
    input('Hit Enter here if you have summited the form: <Enter>')
    cookies = browser.get_cookies()
    pickle.dump(cookies, open("cookies.ini", "wb"))


def set_cookies():
    browser.get("https://aliexpress.com")
    cookies = pickle.load(open("cookies.ini", "rb"))
    for cookie in cookies:
        browser.add_cookie(cookie)
    browser.get("https://bestselling.aliexpress.com/en")


if __name__ == '__main__':
    get_cookies()

```
