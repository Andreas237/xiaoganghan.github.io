---
layout: post
title: Wikipeida title translations
subtitle: find the translation of all the titles one language in another language on wikipedia
category: data
tags:
  - data, python, wikipedia
---

# Problem

Wikipedia is great knowledge base for interlanguage interpretation. In this post I will show how to use wikipedia to build a dictionary for any given pair of languages. For example, find the English translation of all Chinese titles.

# Solution

## the data
We will need to use two different data dumps to make the translation: 

1. Wiki interlanguage link records sql dump data
[The schema](https://www.mediawiki.org/wiki/Manual:Langlinks_table) is as follows:

```
mysql> describe langlinks;
+----------+------------------+------+-----+---------+-------+
| Field    | Type             | Null | Key | Default | Extra |
+----------+------------------+------+-----+---------+-------+
| ll_from  | int(10) unsigned | NO   | PRI | 0       |       |
| ll_lang  | varbinary(20)    | NO   | PRI |         |       |
| ll_title | varbinary(255)   | NO   |     |         |       |
+----------+------------------+------+-----+---------+-------+
```

2. Base per-page sql dump data (id, title, old restrictions, etc)
[The schema](https://www.mediawiki.org/wiki/Manual:Page_table) is follows:

```
mysql> describe page;
+--------------------+---------------------+------+-----+----------------+----------------+
| Field              | Type                | Null | Key | Default        | Extra          |
+--------------------+---------------------+------+-----+----------------+----------------+
| page_id            | int(10) unsigned    | NO   | PRI | NULL           | auto_increment |
| page_namespace     | int(11)             | NO   | MUL | NULL           |                |
| page_title         | varbinary(255)      | NO   |     | NULL           |                |
| page_restrictions  | tinyblob            | NO   |     | NULL           |                |
| page_counter       | bigint(20) unsigned | NO   |     | 0              |                |
| page_is_redirect   | tinyint(3) unsigned | NO   | MUL | 0              |                |
| page_is_new        | tinyint(3) unsigned | NO   |     | 0              |                |
| page_random        | double unsigned     | NO   | MUL | NULL           |                |
| page_touched       | binary(14)          | NO   |     |                |                |
| page_latest        | int(10) unsigned    | NO   |     | NULL           |                |
| page_len           | int(10) unsigned    | NO   | MUL | NULL           |                |
| page_content_model | varbinary(32)       | YES  |     | NULL           |                |
+--------------------+---------------------+------+-----+----------------+----------------+
```


## method
Given a source language and target language, the mapping process can be described as follows
- read the ```page_id``` and ```page_title``` from ```page``` table of the source language;
- for each ```page_id``` in ```page``` table, look up ```ll_from``` field in ```langlinks``` table, and filter the results the language code of the target language

## steps
I've created to python script to do the translation (https://github.com/xiaoganghan/wikipedia-interlanguage-titles). Suppose we would like to find the translations for all Chinese page titles to English. The steps are:

1. download and unzip the data dumps 
The data is dumped daily. Both the data for a specific language in each month can be found on https://dumps.wikimedia.org. For example, the Chinese version on 5th March 2016 can be found at https://dumps.wikimedia.org/zhwiki/20160305/

(1) page https://dumps.wikimedia.org/zhwiki/20160305/zhwiki-20160305-langlinks.sql.gz
(2) langlinks https://dumps.wikimedia.org/zhwiki/20160305/zhwiki-20160305-page.sql.gz


2. run the tool
```
python wikititles.py -p /path/to/zhwiki-20160305-page.sql -l /path/to/zhwiki-20160305-langlinks.sql -c en -o trans.txt
```

Output sample
```
```
