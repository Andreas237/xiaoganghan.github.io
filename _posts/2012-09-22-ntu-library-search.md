title: Using Google Books API to Improve NTU Library Book Search
description: ""
category:
tags: []


### Introduction

NTU library search
([http://ntulibrarysearch.appspot.com/](http://ntulibrarysearch.appspot.com/))
is an app to facilitate searching books in [NTU
library](http://opac.ntu.edu.sg/) with improved accuracy and relevance.
It is powered by [Google Books search
api](http://code.google.com/apis/books/). The application is deployed on
Google Appengine and the [source
code](https://github.com/chrishan/ntulibrarysearch) is available on
GitHub.

### Why?

The book search provided by NTU is pretty poor. I can seldom find the
most relevant books. Below are some search examples for comparison.

#### "game engine"

Books returned from NTU library for "game engine"

![image](http://hanxiaogang.com/site_media/static/images/posts/gameenginentu.jpg)

Books returned from the app for "game engine"

![image](http://hanxiaogang.com/site_media/static/images/posts/gameenginemine.jpg)

### "ruby"

Books returned from NTU library for "ruby"

![image](http://hanxiaogang.com/site_media/static/images/posts/rubyntu.jpg)

Books returned from the app for "ruby"

![image](http://hanxiaogang.com/site_media/static/images/posts/rubymine.jpg)

It is very obvious that the results returned from NTU library search are
of low relevance and even confusing. On the other hand, the results from
the app are very accurate.

### How it works

1.  submit query keywords to the server;
2.  Retrieve top matched books from Google Books API;
3.  Using ISBN of retrieved books to query NTU library

### Known Limitations

-   Only the first page (8 books) of books was retrieved from Google
    Books API. Therefore the search result shown is always less than 8
    books

### Final Words

Putting Google Books search api on top of the library search interface
is very simple and the resulting improvement is very obvious for me.
Hopefully the NTU library will adapt such advanced search api to their
database to make their search function usable.

Dear all NTU library users, please try the app
([http://ntulibrarysearch.appspot.com/](http://ntulibrarysearch.appspot.com/))
and save your time find the books you want.