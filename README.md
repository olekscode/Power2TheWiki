# Power2TheWiki
Final project for the Mining Massive Datasets course at UCU.

## Terminology
* **Page** - either an acual page in Wikipedia or a non-existing page having red links pointing to it (see Red Page).
* **Blue Page** - existing page of Wikipedia that has blue links pointing to it.
* **Red Page** - non-existing page of Wikipedia that has red links pointing to it.

## Notation
* **G-L** - graph of all pages of the Wikipedia in language L.
* **G-Lb** - graph of blue pages of the Wikipedia in language L.
* **V-L** - nodes of graph G-L. All pages of the Wikipedia in language L.
* **V-Ls** - pages of the Wikipedia in language L with status s є {b, r} (blue or red pages). V-Lb represents the nodes of graph G-Lb. V-Lb is a set of red pages. Graph G-Lr does not exist, because red pages don't have any outgoing links.
* **E-L** - edges of graph G-L. Links between all pages of the Wikipedia in language L.
* **E-Lb** - edges of graph G-Lb. Links between blue pages of the Wikipedia in language L.

## Problem Statement
Among all red pages in the Wikipedia in language A find those that can be linked to some blue page in the Wikipedia in language B.

## Plan
1. Collect the data
2. Create the following graphs. In all graphs E = {links between pages}
    1. **G-EN**: V = {all pages of English Wikipedia}
    2. **G-UK**: V = {all pages of Ukrainian Wikipedia}
    3. **G-ENb**: V = {blue pages of English Wikipedia} (subset of EN)
    4. **G-UKb**: V = {blue pages of Ukrainian Wikipedia} (subset of UK)
    5. **G-Q**: V = {pages from ENb and UKb that are the translations of each-other}
3. Create the following subsets of nodes:
    1. **V-ENr** = {red pages from G-EN}
    2. **V-UKr** = {red pages from G-UK}
4. Baseline. Find the nearest neighbours for all nodes from V-ENr in V-UKb and for all nodes from V-UKr in V-ENb using the following similarity measures from graph theory
    1. Number of neighbours in common (a.k.a. mutual friends).
5. Create graph embeddings
6. Find the nearest neighbours for all nodes from V-ENr in V-UKb and for all nodes from V-UKr in V-ENb using graph embeddings
7. Compare the results to the baseline

## Research Questions
1. If almost all red pages have only one incoming link from page X, the idea of graph isomorphism will make little sense because we will be looking for similarity based on a single common neighbour. And this means that any other neighbour of X would be a potential candidate.
    1. What is the average number of incoming links of red pages?
    2. What percent of red pages have the number of incoming links greater than some threshold = 3, 4, 5, ...?

## 1. Collecting the data
We have downloaded the full dumps of [English](https://dumps.wikimedia.org/enwiki/20180620/) and [Ukrainian](https://dumps.wikimedia.org/ukwiki/20180620/) Wikipedia articles from 20/06/2018. Then we have parsed those articles to get outgoing red and blue links: the link article, text and position in the current article text. Also we have downloaded [Wiki interlanguage link records](https://dumps.wikimedia.org/ukwiki/20180620/ukwiki-20180620-langlinks.sql.gz) and parsed out all interlingual links between English and Ukrainian Wikipedia articles.

## 2.Basic Algorithms
Every article (both eng and ukr) will be represented by incoming links - other articles (imagine bag of words representation). Why incoming? - features for conctructing embeddings for blue articles has to be the same as for red articles, but red links have only incoming links, so any other features we cannot use. 

We need to make embedings for ukr and eng articles comparable; eng article has to be represented the same way as its translated eng version, so distance between eng and appropriate ukr article (vectors) is minimal. For ukr article it wil be represented by it incoming articles in ukr wiki. But eng article will be represented by it incoming articles in ukr wiki as well. This can be done the following way: 1) for every article in eng wiki find its eng incoming links; 2) for every eng incoming link find its ukr translated acticle if exists; 2) create bag of words representation containing ukr links for every eng article. Red eng links will be represented the same way using ukr articles.

We end up with big sparse matrix where columns are ukr articles (our bag of words) and rows are ukr and eng article represented by 0/1 if article in column has link to article in row. Red eng links will be in this matrix as well.

Next step is to learn this embeddings. Start with some simple idea: we made tf-idf on this matrix and want to select similarity metric - euclidian or cosine distance? For each eng wiki article we calculate similarity with all the ukr articles in the matrix (yeah, it very inefficient, so this approach is used only to explain idea) using cosine distance. We calculate top1 error for cosine similarity. Then we do the same for euclidian distance. Similarity metric that has the lower error is the one we select.

For every eng red link in the matrix we calculate similarity (using similarity metrics selected on the previous step) to those ukr articles that do not have eng version. The most similar ukr article is the one that correeponds to this red link. Possibly, we will find several ukr articles with the same similarities, so add them as candidates for further preprocessing.

## 2. Creating the graphs

### Software libraries

* [networkx](https://networkx.github.io)

## 5. Graph embeddings

## Literature

1. P. Goyal, E. Ferrara, Graph Embedding Techniques, Applications, and Performance: A Survey. December 2017. Available at: https://arxiv.org/abs/1705.02801
