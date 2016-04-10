---
layout: post
title: Wikipeida title translations
subtitle: find the translation of all the titles one language in another language on wikipedia
category: data
tags:
  - data, python, wikipedia
---

# Problem

Wikipedia is great knowledge base for interlanguage interpretation. In this post I will show how to use wikipedia to build a dictionary for any given pair of language.

find the translation of all the titles one language in another language on wikipedia

# Solution

http://w3stack.org/question/easy-way-to-export-wikipedias-translated-titles/

https://dumps.wikimedia.org/zhwiki/20160305/
https://dumps.wikimedia.org/thwiki/20160305/

wikidata dump in sql format

wikipedia page sql data - format and example
https://www.mediawiki.org/wiki/Manual:Page_table
format: Base per-page data (id, title, old restrictions, etc).

wikipedia langlink sql data - format and example
https://www.mediawiki.org/wiki/Manual:Langlinks_table

parse the files directly, rather than dump to MySQL


The table contains mappings from page id of your wiki (in your case the Russian Wikipedia) to page titles in other wikis. This means you will need the page ids of the pages you’re interested in. Download the dump of the page table, which contains this mapping.

# Code

The code is available at https://github.com/xiaoganghan/wikipedia-interlanguage-titles
