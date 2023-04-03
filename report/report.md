---
# ppandoc report/report.md -o report/pdf/report.pdf --from markdown --template report/eisvogel.tex --listings --pdf-engine=xelatex --toc --number-sections -H report/disable_float.tex

papersize: a4
lang: en-US
documentclass: article
title: UE10 Extraction et programmation statistique de l'information 

author: \textbf{LAI Khang Duy} \newline
        \textbf{Mazen TEBIB} \newline
        \newline
        \newline
        \textit{Université Paris-Saclay} \newline 
        \textit{Faculté des sciences d'Orsay}
footer-left: Université Paris-Saclay
date: 23-03-2023
titlepage: true
toc-own-page: true
# lof: true
# lof-own-page: true
titlepage-logo: report/images/ups.png
header-includes: 
      - |
        ``` {=latex}
        \let\originAlParaGraph\paragraph
        \renewcommand{\paragraph}[1]{\originAlParaGraph{#1} \hfill}
        ```
# output:
#   rmarkdown::pdf_document:
#     fig_caption: yes        
#     includes:  
#       in_header: figure_placement.tex
...

# Data exploration

First, we want to see which exactly in the corpus in order to further do the data preprocessing. As you can see in the image of Raw data set, we have 2 columns in the corpus, ```country``` and the ```description```. ```country``` is functioned as the label ```y```, whereas ```description``` is the data itself. 

![Raw data set](report/images/data-explore.png){ width=65% }

We have 10 labels stand for 10 different native languages. The number of data points in each label is equal.

![Number of data point in each class](report/images/explore-number.png){ width=120px }

# Data preprocessing 

In this step, we apply a few data preprocessing techniques before feed the processed data to the model. These steps include cleaning the texts, using IF-IDF to vertorized it, encode the labels in the column ```country``` and Extract important features.

## Clean raw texts

Normally, this process includes also removing stop words, lemmatization and stemming. However, due to the fact that removing such thing might remove very important features when the people who use English as second language could do such mistake too, so in this case we are not going to include it it. We only include:

- Lowering all texts
- Remove numbers
- Remove punctuations
- Remove special characters

![After cleaning corpus](report/images/after-clean.png){ width=450px }

## TF-IDF Vectorizer

Split the data into test set and train set before use tf-idf to vectorize it. If you learn tf-idf also on the test set you will have data leakage. In example, you won't have out-of vocabulary words in inference on the test set, what might happen on the real world.

```python
#would be better if u split data before then fit then transform  
tf = TfidfVectorizer(stop_words='english', ngram_range=(1,2))
tf_Xtrain = tf.fit_transform(X_train)
tf_Xtest = tf.transform(X_test)
```

We have tried to adjust the ```ngram_range``` between ```ngram_range=(2,3)``` and ```ngram_range=(1,2)```. The best one is ```(1,2)```.

## Encoding labels

We cannot use the direct label to feed the model. Before feeding the model, it is necessary to encode it into the form of numbers. We added a new column in the table. ```country``` is the label and ```y_country``` is the encoded one, from 0 to 9 according to 10 labels.

| country | y_country |
|--------------|---------|

## Extracting features

### Lexical features

### Syntatic features


sthg

# Models

We have tried several method for the model of this subject. First, we tried to determine which is the bes classification model. Second, we apply the hierachy method (cascade method), in order to see if there are any improvements.

## Model selection

In order to find the best model that work with the corpus, we must run several models and then select the model with the best score overall. 

The list of models that we used including:

- Nearest Neighbors
- Linear SVC
- Random Forest
- Kmeans
- GaussianNB

![Model selection results](report/images/model-select-result.png){ width=65% }

With the LinearSVC, we have the best score overall so in the next cascade method I will use LinearSVC as our model. 

Here is the confusion matrix of LinearSVC with the average accuracy of 70% if we classify language directly.

![LinearSVC confusion matrix](report/images/linearsvc-confuse.png){ width=65% }

I re run the test with randomly train test split 4 times to take the average heat map confusion matrix. 

## Cascade method

After we find the best model, we implement cascade method on the project. The idea is simple. We have 2 layers of models. First, we train the model so that it could well separate the language groups. We devided 10 languages into 4 language groups as below:

- Asian language group
- Roman language group
- Indian language group
- Other languages

![Language group confusion matrix](report/images/language-group-acc.png){ width=30% }

We can achieve the confident of 93% with group separation. Below is the confusion matrix bettween language groups.

![Language group confusion matrix](report/images/language-group.png){ width=65% }

After that, which data point have the highest confident we will pass it in to the language specific layer. You can see the visualization of it as the diagram below.

![Cascade diagram](report/images/cascade.png)

# Hyperparameter tuning

We can change the threshold of the language group layer and language specific layer until we have the best score.

```
hierarchical_predict(X, group_clf, classifiers, threshold_group=0.8, threshold_specific=0.5):
```
the ```threshold_group``` and the ```threshold_specific``` varies from 0.5 to 0.8, and the impact on the last score is minor. So we keep the score as 0.5.