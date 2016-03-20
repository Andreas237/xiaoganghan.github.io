title: shouldinvent
description: ""
category:
tags: []


My new side project -
[ShouldInvent](http://shouldinvent.hanxiaogang.com/) is online, which is
basically an automatic idea aggregation web application based on Twitter
Search. The primary motivation is to provide inspirations for engineers
in various domains.

The project is initially inspired by
[one](http://twitter.com/#!/sivers/status/77683713793736704) of
[sivers](http://twitter.com/#!/sivers)'s tweets:

[@sivers](http://twitter.com/#!/sivers) Derek Sivers - Entrepreneurs,
need inspiration for what to invent Try this:
[http://search.twitter.com/q=%22should+invent%22](http://search.twitter.com/q=%22should+invent%22)

After thought about it for a minutes, I asked myself - how about create
an app that search Twitter periodically for "should invented" things?
Can great ideas be mined from such informally mentioned things?

The algorithm behind the application is illustrated in the following
figure.

![image](http://hanxiaogang.com/site_media/static/images/posts/how.png)
The application works like this:

1.  periodically, a bot is called to search Twitter using keywords
    "should invent" (also "should develop");
2.  naive natural language processing techniques like tokenizing and
    chunking is then performed on the content of tweets to identify the
    target objects;
3.  finally, each of the target objects are automatic classified into
    hierarchical categories.

Based on tweets collected in the last month, the ["most desired
inventions"](http://shouldinvent.hanxiaogang.com/%20sort=tweet_count&dir=asc)
on Twitter are:

-   [mirror](http://shouldinvent.hanxiaogang.com/invent/1) [4899 tweets]
    a mirror that takes pictures
-   [waterproof](http://shouldinvent.hanxiaogang.com/invent/1664) ipod
    [414 tweets] a waterproof ipod
-   [snooze button](http://shouldinvent.hanxiaogang.com/invent/234) [366
    tweets] a snooze button that hits back
-   [food machine](http://shouldinvent.hanxiaogang.com/invent/172) [223
    tweets] a food machine where when you ask for something
-   [version of twitter](http://shouldinvent.hanxiaogang.com/invent/138)
    [89 tweets] a version of twitter for people who spell things like
    rappers

The current named entry recognition and classification algorithm are by
no means perfect and still need to be improved over time. On the other
hand, the UI need improvement as I am not a competent designer. But at
least it appears to work.

Go to the [website](http://shouldinvent.hanxiaogang.com/) and happy
"steal" ideas from Twitter!

Any feedback are appreciated and welcomed.
