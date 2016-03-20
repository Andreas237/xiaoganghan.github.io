title: Contextual Subtitle for Language Learning
description: ""
category:
tags: []


As a Chinese, I occasionally learn oral English via watching American TV
Series. One problem is about the subtitle. If I use the Chinese
subtitle, then I rely on the subtitle for understanding, rather than
"hearing"; otherwise, if I use the English subtitle, then there are
regular words that are out of my vocabulary, which makes the experience
not enjoyable. Then I come up with the idea - a mixed subtitle.

I wrote a python script to perform the creation of the mixed subtitle,
which go through the English subtitle file (.srt format) of the movie to
check for unacquainted word and add the Chinese translation of the word
immediately after the word. Here are two screenshots from the subtitle
generated for *The Big Bang theory season 2*.

![image](http://hanxiaogang.com/site_media/static/images/posts/movie1.jpg)
![image](http://hanxiaogang.com/site_media/static/images/posts/movie2.jpg)

To perform the generation, first I need an English-Chinese dict. The
[LDC](http://projects.ldc.upenn.edu/Chinese/) provides an such
[wordlist](http://www.ldc.upenn.edu/Projects/Chinese/docs/ldc_ec_dict.2.0.txt).
Second, an common English word list is needed. This is very easy to
find. I use
[resouce1](http://blog.hanxiaogang.com/blog/2012/02/29/media/aghjb2RpbmdhaXINCxIFTWVkaWEYwdECDA/known.txt),
[resource2](http://blog.hanxiaogang.com/blog/2012/02/29/media/aghjb2RpbmdhaXINCxIFTWVkaWEYorICDA/known3000.txt),
and
[resouce3](http://blog.hanxiaogang.com/blog/2012/02/29/media/aghjb2RpbmdhaXINCxIFTWVkaWEYqdkCDA/knownVOA.txt)
as the known wordlist. [One more
file](http://blog.hanxiaogang.com/blog/2012/02/29/media/aghjb2RpbmdhaXINCxIFTWVkaWEYkeECDA/local.txt)
is created to store the words frequently appears in the movie I am
watching (such as names).

The source code file can be download from the github
[gist](http://gist.github.com/647077)

### Problems

-   There are still many words not included in this corpus, which are
    therefore not translated if any;
-   The known wordlist is fixed, which is not a good model of each
    individual audience. There might be some computational model to
    simulate the vocabulary of a user, which I will explore in the
    future.

### Todos

-   introduce larger English-Chinese dict;
-   more contextual translation for word sense disambiguation;
-   dynamic personal vocabulary modeling;
-   contextual subtitle as a service: build a website to provide
    personalized vocabulary modeling and mixed subtitle generation.

Anyway, the current version is already in use:). I have enjoyed many
episodes of The Big Bang Theory with the mixed subtitle.

