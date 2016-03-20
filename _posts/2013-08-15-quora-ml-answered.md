Title: My Solution to the Quora ML Problem (Predicting whether a question gets an upvoted answer)
Date: 2013-08-15 22:16
Category: Python
Tags: machine-learning, python, scikit-learn
Slug: quora-ml-problem

The [Quora ML CodeSprint 2013](https://www.hackerrank.com/contests/quora/challenges) just finished on hackerrank.com. My entry for the first task - [Quora ML Problem: Answered](https://www.hackerrank.com/contests/quora/challenges/quora-ml-answered/leaderboard), was ranked 7th/71 on the dashboard.

The objective of the task is to predict whether a question gets an upvoted answer within 1 day, given Quora question text and topic data.

I built two classfiers - one based on text features and one based on categorical features, and then built a linear combinator based on the prob output from these two classifiers. The code:

    :::python
    import json
    import sys
    import math

    from nltk.corpus import stopwords

    from sklearn.feature_extraction.text import CountVectorizer
    from sklearn.feature_extraction.text import TfidfTransformer
    from sklearn.naive_bayes import MultinomialNB
    from sklearn.linear_model import LogisticRegression


    english_stopwords = stopwords.words('english')
    english_punctuations = [',', '.', ':', ';', '?', '(', ')', '[', ']', '&', '!', '*', '@', '#', '$', '%']

    IS_LOCAL = False


    def load():
        TRAIN_N = 0
        train_docs = []

        TEST_N = 0
        test_docs = []

        if IS_LOCAL:
            source = open('sample/answered_data_10k.in').readlines()
        else:
            source = sys.stdin
        for ind, line in enumerate(source):
            if ind == 0:
                TRAIN_N = int(line.strip())
            elif 1 <= ind <= TRAIN_N:
                train_docs.append(json.loads(line.strip()))
            elif ind == TRAIN_N + 1:
                TEST_N = int(line.strip())
            else:
                test_docs.append(json.loads(line.strip()))

        return train_docs, test_docs


    def verify(actual_dict, pred_dict):
        correct_count = 0
        for k, v in pred_dict.iteritems():
            true_v = actual_dict[k]
            if true_v == v:
                correct_count += 1
        return float(correct_count) / len(pred_dict)


    def get_text(doc):
        text = doc['question_text']
        context_topic = doc['context_topic']
        topics = doc['topics']

        context_topic_text = ''
        if context_topic:
            context_topic_text = context_topic['name']
        topic_text = ''
        if topics:
            for topic in topics:
                topic_text += topic['name']
        text = ' '.join([text, context_topic_text, topic_text])
        return text


    def text_clf(train_docs, test_docs):
        vectorizer = CountVectorizer(stop_words=english_stopwords)
        corpus = [get_text(doc) for doc in train_docs]
        X = vectorizer.fit_transform(corpus)
        tfidf_transformer = TfidfTransformer()
        X = tfidf_transformer.fit_transform(X)

        Y = []
        for doc in train_docs:
            if doc['__ans__'] :
                Y.append(1)
            else:
                Y.append(0)

        classifier = MultinomialNB()
        classifier.fit(X, Y)

        corpus = [get_text(doc) for doc in test_docs]
        X = vectorizer.transform(corpus)
        X = tfidf_transformer.transform(X)
        predictions = classifier.predict(X)
        pred_prob = classifier.predict_proba(X)
        return predictions, pred_prob


    def cat_clf(train_docs, test_docs):
        def extract(doc):
            is_anonymous = 1
            if doc['anonymous']:
                is_anonymous = 0
            cat_count = math.log(0.85 + len(doc['topics']))
            cat_following_count = math.log(0.85 + sum([t['followers'] for t in doc['topics']]))
            v = [is_anonymous, cat_count, cat_following_count]
            return v

        X = []
        for doc in train_docs:
            v = extract(doc)
            X.append(v)

        Y = []
        for doc in train_docs:
            if doc['__ans__'] :
                Y.append(1)
            else:
                Y.append(0)

        clf = LogisticRegression(tol=1e-8, penalty='l2', C=4)
        clf.fit(X, Y)

        X = []
        for doc in test_docs:
            v = extract(doc)
            X.append(v)

        pred = clf.predict(X)
        pred_prob = clf.predict_proba(X)
        return pred, pred_prob


    def build_model(train_docs, test_docs):
        text_pred1, text_prob1 = text_clf(train_docs, test_docs)
        text_pred2, text_prob2 = cat_clf(train_docs, test_docs)
        prob = (text_prob1 + text_prob2) / 2.0

        pred_dict = {}
        for ind, p in enumerate(prob):
            if p[1] > 0.5:
                mark = True
            else:
                mark = False
            d = {
                '__ans__': mark,
                'question_key': test_docs[ind]['question_key'],

            }
            d = json.dumps(d)
            print d
            pred_dict[test_docs[ind]['question_key']] = mark

        if IS_LOCAL:
            actual_dict = {}
            for line in open('sample/answered_data_10k.out').readlines():
                data = json.loads(line)
                actual_dict[data['question_key']] = data['__ans__']

            print verify(actual_dict, pred_dict)


    if __name__ == '__main__':
        train_docs, test_docs = load()
        build_model(train_docs, test_docs)
