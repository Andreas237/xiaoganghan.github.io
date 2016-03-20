Title: How to use the APIs to collect data from Sina Weibo
Date: 2014-07-01 15:40
Category: Coding
Tags: sns, weibo, data
Slug: weibo-data

Suppose you want to extract tweets posted by a selected group of Weibo users. Here are the steps:

## Step 1: Create a new Weibo account

After the account is created, manually add the target users (whose tweets you want to collect) as your followees.

## Step 2: Create a new app on Weibo Open Platform

1. create a [new app (网页应用)](http://open.weibo.com/apps/new?sort=web) (应用地址：http://127.0.0.1)
2. configure callback addresses on advanced setting page (授权回调页：http://127.0.0.1:8000/weibo, 取消授权回调页：http://127.0.0.1:8000/weibo/cancel)
3. App Key and App Secret will be issued on the basic setting page

## Step 3: install python SDK

```
pip install sinaweibopy
```

## Step 4: obtain tweets data

```python
# -*- coding: utf-8 -*-

import sys
import weibo
import webbrowser
import pymongo

APP_KEY = YOUR_API_KEY
APP_SECRET = YOUR_API_SECRET
REDIRECT_URL = 'http://127.0.0.1:8000/weibo'


def get_access_token():
    api = weibo.APIClient(APP_KEY, APP_SECRET)

    authorize_url = api.get_authorize_url(REDIRECT_URL)
    print(authorize_url)
    webbrowser.open_new(authorize_url)
    code = raw_input('authencation code: ')

    request = api.request_access_token(code, REDIRECT_URL)
    access_token = request.access_token
    expires_in = request.expires_in
    print 'access token: ', access_token
    print 'expire: ', expires_in


def get_data():
    access_token = ''
    expires_in = ''
    api = weibo.APIClient(APP_KEY, APP_SECRET, redirect_uri=REDIRECT_URL)
    api.set_access_token(access_token, expires_in)
    r = api.statuses.home_timeline.get(uid=YOUR_USERID, count=100)
    return r.statuses


def save_data():
    conn = Connection()
    db = conn.tweets_db
    tweets_table = db.tweets_table
    tweets_table.ensure_index('id', unique=True)

    tweets = get_data()
    orignal_count = tweets_table.count()
    for tweet in tweets:
        tweets_table.update({'id': tweet['id']}, tweet, True)
    print 'added: ', tweets_table.count() - orignal_count


if __name__ == '__main__':
    pass

```

1. run a local web server to  `python -m SimpleHTTPServer`

2. copy the code below into a text editor

3. run `get_access_token()`. During code execution, your browser will open with address like: *http://127.0.0.1:8000/weibo?code=bc3de7eee81bb9a03b6d608a*

4. copy the *code* back into the console which is waiting for your input, and hit *Enter*. The *access token* and its *expire time* will be printed in the console.

5. copy and assign *access token* and *expire time* to the corresponding variables in `get_data()` function, obtain the userid of your account and assign it to *uid* in `api.statuses.home_timeline.get(uid=YOUR_USERID, count=100)`

6. run `save_data()`, and the data will be saved to MongoDB

__notes__ : you may need to run `get_access_token()` per few days to refresh *access token* in case it expires.

## Useful links

* http://github.liaoxuefeng.com/sinaweibopy/
* http://blog.csdn.net/changemyself/article/details/9172807