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
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.feature_extraction.text import TfidfTransformer
from sklearn.metrics import classification_report
from sklearn.metrics import precision_recall_fscore_support
from sklearn.naive_bayes import MultinomialNB
from sklearn.neighbors import KNeighborsClassifier
from nltk.corpus import stopwords


class TextCls():
    def __init__(self, label_names, X_train, y_train):
        self.k = 2
        self.label_names = label_names
        self.X_train, self.y_train = X_train, y_train

    def fit(self):
        self.vectorizer = CountVectorizer(ngram_range=(1, 1), stop_words=stopwords.words('english'))

        X_train_vect = self.vectorizer.fit_transform(self.X_train)
        self.tfidf_transformer = TfidfTransformer()
        X_train_trans = self.tfidf_transformer.fit_transform(X_train_vect)

        self.classifier = KNeighborsClassifier(n_neighbors=self.k)
        # self.classifier = MultinomialNB()

        self.classifier.fit(X_train_trans, self.y_train)

    def predict(self, X_test):
        X_test_vect = self.vectorizer.transform(X_test)
        X_test_trans = self.tfidf_transformer.transform(X_test_vect)
        y_pred = self.classifier.predict(X_test_trans)
        return y_pred

    def predict_single(self, doc):
        X_test_vect = self.vectorizer.transform([doc])
        X_test_trans = self.tfidf_transformer.transform(X_test_vect)
        y_pred = zip(self.classifier.classes_, self.classifier.predict_proba(X_test_trans)[0])
        y_pred = sorted([(self.label_names[ind], score) for ind, score in y_pred], key=lambda x: -x[1])
        return y_pred

    def report(self, X_test, y_test, y_pred):
        print(classification_report(y_test, y_pred, target_names=self.label_names, digits=4))

        total = 0
        same = 0
        for i in range(len(y_test)):
            if y_test[i] == y_pred[i]:
                same += 1
            total += 1
        print(total, same)

        print precision_recall_fscore_support(y_test, y_pred, average='micro')

    def predict_proba(self, X):
        pass


def main():
    train_samples = {
        'article': [
            'this is an amazing article',
            'this article is great read'
        ],
        'artwork': [
            'this is a great painting art work',
            'this is a random oil painting'
        ]
    }

    label_names = sorted(train_samples.keys())

    X_train, y_train = [], []
    for label_name, docs in train_samples.iteritems():
        label_index = label_names.index(label_name)
        for doc in docs:
            X_train.append(doc)
            y_train.append(label_index)

    model = TextCls(label_names, X_train, y_train)

    model.fit()

    X_test = [
        'this article is great',
        'cool painting'
    ]
    y_test = [
        label_names.index('article'),
        label_names.index('artwork')
    ]

    for doc in X_test:
        top_preds = model.predict_single(doc)[:2]
        print(doc)
        for label, score in top_preds:
            print('\t{}\t{}'.format(label, score))

    y_pred = model.predict(X_test)
    model.report(X_test, y_test, y_pred)


if __name__ == '__main__':
    main()
```

resunt:
```
this article is great
    article 1.0
    artwork 0.0
cool painting
    artwork 1.0
    article 0.0
             precision    recall  f1-score   support

    article     1.0000    1.0000    1.0000         1
    artwork     1.0000    1.0000    1.0000         1

avg / total     1.0000    1.0000    1.0000         2

(2, 2)
(1.0, 1.0, 1.0, None)
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
