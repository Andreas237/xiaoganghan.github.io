---
layout: post
title: Text classification and similarity search with Python and sklearn
tags:
  - classification
  - sklearn
  - python
  - nearest neighbour search
---

# Motivation

Nearest neighbour search and classifcation are two most common use cases. In this post, I will summarize how to setup basic flow for both cases.

# Text classification

## code
```python
print('to be added')
```


# Nearest neighbour search

## code
```python
import nltk
from sklearn.metrics.pairwise import cosine_similarity
from sklearn.feature_extraction.text import TfidfVectorizer


stemmer = nltk.stem.porter.PorterStemmer()


def stem_tokens(tokens):
    return [stemmer.stem(item) for item in tokens]


def tokenize(text):
    return stem_tokens(nltk.word_tokenize(text.lower()))


def find_similar_docs(docs, queries, top_n=3):
    vectorizer = TfidfVectorizer(tokenizer=tokenize, stop_words='english')
    tfidf = vectorizer.fit_transform(docs)

    cosine_similarities_list = cosine_similarity(vectorizer.transform(queries), tfidf)

    result_list = []
    for i in range(len(cosine_similarities_list)):
        cosine_similarities = cosine_similarities_list[i]
        sorted_sim_docs_indices = cosine_similarities.argsort()[::-1][:top_n]
        sorted_sim_docs_scores = cosine_similarities[sorted_sim_docs_indices]
        result_list.append(zip(sorted_sim_docs_indices, sorted_sim_docs_scores))
    return result_list


if __name__ == '__main__':
    docs_list = [
        'this is an article',
        'this is a photography work',
        'this is a video'
    ]
    queries = [
        "nice work.",
        "nice article."
    ]

    print(find_similar_docs(docs_list, queries))
```

result:
```
[
    [(1, 0.6524908845125339), (2, 0.0), (0, 0.0)], 
    [(0, 0.86103699594397642), (2, 0.0), (1, 0.0)]
]
```
