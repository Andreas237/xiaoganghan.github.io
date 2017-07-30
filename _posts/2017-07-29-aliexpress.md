---
layout: post
title: A beginner's guide to Aliexpress data scraping and mining
---

**Table Of Content** <!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Introduction](#introduction)
- [Scraping](#scraping)
  - [Login](#login)
  - [Scrape hot products with Selenium](#scrape-hot-products-with-selenium)
  - [Extract product data from detail page](#extract-product-data-from-detail-page)
  - [Extract Feedback using the hidden API](#extract-feedback-using-the-hidden-api)
- [Feedback Mining](#feedback-mining)
- [Conclusion](#conclusion)
<!-- /TOC -->

# Introduction
Aliexpress is a popular place to buy and source good and cheap products. In this post, I will introduce how to use ```python and selenium``` to scrape products and reviews from Aliexpress, and how to do a simple analytics on the data.

# Scraping
## Login
When scraping Aliexpress pages, you will get banned very soon if you are not logged in. So the first step is to login with selenium and save the cookies into local files for later use.

Run the code below, you will get Aliexpress login page opened in Firefox, type in your Aliexpress username/password and hit Submit, the code will save the cookies to ```cookies.ini```

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
    pickle.dump(cookies, open("cookies.pickle", "wb"))


def set_cookies():
    browser.get("https://aliexpress.com")
    cookies = pickle.load(open("cookies.pickle", "rb"))
    for cookie in cookies:
        browser.add_cookie(cookie)
    browser.get("https://bestselling.aliexpress.com/en")


if __name__ == '__main__':
    get_cookies()

```

## Scrape hot products with Selenium
With the credential cookies we've just saved in the previous step, we can now use Selenium to scrape hot products. Aliexpress has a [Weekly Bestselling](https://sale.aliexpress.com/__pc/bestselling.htm) page. We will scrape all the product urls listed on the page.

![hot](/img/aliexpress-hot.png)

```python
from selenium import webdriver
import pickle
import time


driver = webdriver.Firefox()
driver.get("https://aliexpress.com")
cookies = pickle.load(open("cookies.ini", "rb"))
for cookie in cookies:
    driver.add_cookie(cookie)


def extract_product_urls_from_list_page(list_page_url):
    driver.get(list_page_url)
    time.sleep(5)
    cats = driver.find_elements_by_css_selector('span.title')

    all_links = set()
    for ind, cat in enumerate(cats):
        print(cat.text)
        try:
            cat.click()
        except Exception:
            continue
        if ind == 0:
            items = driver.find_elements_by_class_name('item-desc')
            links = [item.get_attribute('href') for item in items]
        else:
            items = driver.find_elements_by_css_selector('div.title > a')
            links = [item.get_attribute('href') for item in items]
        for link in links:
            all_links.add(link)
        time.sleep(2)
    return all_links


if __name__ == '__main__':
    extract_product_urls_from_list_page('https://sale.aliexpress.com/__pc/bestselling.htm')
```

## Extract product data from detail page
With the product detail page url, we can now request the detail page and extract all the metadata for a single product. 

![detail](/img/aliexpress-detail.png)

```python
from selenium import webdriver
import json
import pickle
from datetime import datetime
from bs4 import BeautifulSoup


driver = webdriver.Firefox()
driver.get("https://aliexpress.com")
cookies = pickle.load(open("cookies.ini", "rb"))
for cookie in cookies:
    driver.add_cookie(cookie)


def extract_product_info(product_url):
    driver.get(product_url)
    content = driver.page_source

    soup = BeautifulSoup(content, "html.parser")

    product_id = soup.find('input', {'id': 'hid-product-id'})['value']
    title = soup.find('h1', {'class': 'product-name'}).text
    price = float(soup.find('span', {'id': 'j-sku-price'}).text.split('-')[0])

    if soup.find('span', {'id': 'j-sku-discount-price'}):
        discount_price = float(soup.find('span', {'id': 'j-sku-discount-price'}).text.split('-')[0])
    else:
        discount_price = None

    properties = soup.findAll('li', {'class': 'property-item'})
    attrs_dict = {}
    for item in properties:
        name = item.find('span', {'class': 'propery-title'}).text[:-1]
        val = item.find('span', {'class': 'propery-des'}).text
        attrs_dict[name] = val
    description = json.dumps(attrs_dict)

    stars = float(soup.find('span', {'class': 'percent-num'}).text)
    votes = int(soup.find('span', {'itemprop': 'reviewCount'}).text)
    orders = int(soup.find('span', {'id': 'j-order-num'}).text.split()[0].replace(',', ''))
    wishlists = 0  # int(soup.find('span', {'id': 'j-wishlist-num'}).text.strip()[1:-1].split()[0])

    try:
        shipping_cost = soup.find('span', {'class': 'logistics-cost'}).text
        shipping_company = soup.find('span', {'id': 'j-shipping-company'}).text
    except Exception:
        shipping_cost = ''
        shipping_company = ''
    is_free_shipping = shipping_cost == 'Free Shipping'
    is_epacket = shipping_company == 'ePacket'

    primary_image_url = soup.find('div', {'id': 'magnifier'}).find('img')['src']

    store_id = soup.find('span', {'class': 'store-number'}).text.split('.')[-1]
    store_name = soup.find('span', {'class': 'shop-name'}).find('a').text
    store_start_date = soup.find('span', {'class': 'store-time'}).find('em').text
    store_start_date = datetime.strptime(store_start_date, '%b %d, %Y')

    if soup.find('span', {'class': 'rank-num'}):
        store_feedback_score = int(soup.find('span', {'class': 'rank-num'}).text)
        store_positive_feedback_rate = float(soup.find('span', {'class': 'positive-percent'}).text[:-1]) * 0.01
    else:
        driver.refresh()
        try:
            store_feedback_score = int(soup.find('span', {'class': 'rank-num'}).text)
            store_positive_feedback_rate = float(soup.find('span', {'class': 'positive-percent'}).text[:-1]) * 0.01
        except Exception:
            store_feedback_score = -1
            store_positive_feedback_rate = -1

    try:
        cats = [item.text for item in soup.find('div', {'class': 'ui-breadcrumb'}).findAll('a')]
        category = '||'.join(cats)
    except Exception:
        category = ''

    row = {
        'product_id': product_id,
        'title': title,
        'description': description,
        'price': price,
        'discount_price': discount_price,
        'stars': stars,
        'votes': votes,
        'orders': orders,
        'wishlists': wishlists,
        'is_free_shipping': is_free_shipping,
        'is_epacket': is_epacket,
        'primary_image_url': primary_image_url,
        'store_id': store_id,
        'store_name': store_name,
        'store_start_date': store_start_date,
        'store_feedback_score': store_feedback_score,
        'store_positive_feedback_rate': store_positive_feedback_rate,
        'category': category,
        'product_url': product_url
    }
    return row


if __name__ == '__main__':
    extract_product_info('https://www.aliexpress.com/item/Hair-Accessories-Synthetic-Wig-Donuts-Bud-Head-Band-Ball-French-Twist-Magic-DIY-Tool-Bun-Maker/32457370321.html?scm=1007.13442.37932.0&pvid=f8b9f498-65d4-400f-a14f-38b4bba77546&tpp=1')

```

## Extract Feedback using the hidden API
One more interesting thing we can extract from the detail page is the list of reviews from buyers. Aliexpress has a hidden rest API to load the reviews. We will use Python ```requests``` library to extract them.

![review](/img/aliexpress-review.png)

```python
import csv
import requests


def extract_product_reviews(product_id, max_page=100):
    url_template = 'https://m.aliexpress.com/ajaxapi/EvaluationSearchAjax.do?type=all&index={}&pageSize=20&productId={}&country=US'
    initial_url = url_template.format(1, product_id)
    reviews = []

    s = requests.Session()

    resp = s.get(initial_url)
    if resp.status_code == 200:
        data = resp.json()
        total_page = data['totalPage']
        total_page = min([total_page, max_page])
        reviews += data['evaViewList']

        if total_page > 1:
            next_page = 2
            while next_page <= total_page:
                print('{}\t{}/{}'.format(product_id, next_page, total_page))
                next_url = url_template.format(next_page, product_id)
                resp = s.get(next_url)

                next_page += 1

                try:
                    data = resp.json()
                except Exception:
                    continue

                reviews += data['evaViewList']

    filtered_reviews = []
    for review in reviews:
        data = {
            'anonymous': review['anonymous'],
            'buyerCountry': review['buyerCountry'],
            'buyerEval': review['buyerEval'],
            'buyerFeedback': review['buyerFeedback'],
            'buyerGender': review['buyerGender'] if 'buyerGender' in review else '',
            'buyerHeadPortrait': review['buyerHeadPortrait'] if 'buyerHeadPortrait' in review else '',
            'buyerId': review['buyerId'] if 'buyerId' in review else '',
            'buyerName': review['buyerName'],
            'evalDate': review['evalDate'],
            'image': review['images'][0] if 'images' in review and len(review['images']) > 0 else '',
            'logistics': review['logistics'] if 'logistics' in review else '',
            'skuInfo': review['skuInfo'] if 'skuInfo' in review else '',
            'thumbnail': review['thumbnails'][0] if 'thumbnails' in review and len(review['thumbnails']) > 0 else '',
        }
        filtered_reviews.append(data)

    keys = filtered_reviews[0].keys()
    with open('reviews.csv', 'w') as output_file:
        dict_writer = csv.DictWriter(output_file, keys)
        dict_writer.writeheader()
        dict_writer.writerows(filtered_reviews)
    return filtered_reviews


if __name__ == '__main__':
    extract_product_reviews('32457370321')

```

# Feedback Mining
The feedback from the buyers contains rich information about the buyers' preferences and choices when buying the products.

1. The number of the reviews by date
![detail](/img/aliexpress-plot-review.png)

2. Top countries of the buyers
![detail](/img/aliexpress-plot-countries.png)

3. Top logistics used by the buyers
![detail](/img/aliexpress-plot-logistics.png)

4. The product variants
![detail](/img/aliexpress-plot-skuinfo.png)

The code for generating the visualization:
```python
import matplotlib.pyplot as plt
import pandas as pd
import matplotlib
matplotlib.style.use('ggplot')

df = pd.read_csv('reviews.csv')
df['evalDate'] = pd.to_datetime(df['evalDate'])

vc = df['evalDate'].value_counts()
ax = vc.plot()
ax.set_xlabel("date")
ax.set_ylabel("review count")
plt.savefig('aliexpress-plot-review.png')

vc = df['buyerCountry'].value_counts()[:10]
ax = vc.plot(kind='pie')
ax.set_ylabel("top 10 buyer countries")
plt.savefig('aliexpress-plot-countries.png')


vc = df['logistics'].value_counts()[:5]
ax = vc.plot(kind='pie')
ax.set_ylabel("top 5 logistics")
plt.savefig('aliexpress-plot-logistics.png')


vc = df['skuInfo'].value_counts()
ax = vc.plot(kind='pie')
ax.set_ylabel("skuInfo")
plt.savefig('aliexpress-plot-skuinfo.png')
```
# Conclusion
In this post, we went through the process to extract product information from Aliexpress, and did some simple data mining with the buyer feedback data. One thing to mention is that the buyer feedback can tell more information about the product. For example, you can do some text mining to find usually how long it takes for the buyers to get the product by location.
