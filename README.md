# Power2TheWiki
Final project for the Mining Massive Datasets course at the Ukrainian Catholic University. The goal of this project is to build the graph of all Wikipedia pages and learn from it to fing pages that correspond to the non-existing pages referenced by red links in other languages. This would allow to create the so-called "red pages" by translating the existing ones.

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

## Minimum value product (MVP)

* Well-documented dataset
* Baseline - simple graph-based method of finding nearest neighbours. Proof of concept that our dataset can be used for such things.
* Something above the baseline.

## Research Questions
1. If almost all red pages have only one incoming link from page X, the idea of graph isomorphism will make little sense because we will be looking for similarity based on a single common neighbour. And this means that any other neighbour of X would be a potential candidate.
    1. What is the average number of incoming links of red pages?
    2. What percent of red pages have the number of incoming links greater than some threshold = 3, 4, 5, ...?
    
## 1. Collecting the data
We have downloaded the full dumps of [English](https://dumps.wikimedia.org/enwiki/20180620/) and [Ukrainian](https://dumps.wikimedia.org/ukwiki/20180620/) Wikipedia articles from 20/06/2018. Then we have parsed those articles to get outgoing red and blue links: the link article, text and position in the current article text. Also we have downloaded [Wiki interlanguage link records](https://dumps.wikimedia.org/ukwiki/20180620/ukwiki-20180620-langlinks.sql.gz) and parsed out all interlingual links between English and Ukrainian Wikipedia articles.

For every eng red link in the matrix we calculate similarity (using similarity metrics selected on the previous step) to those ukr articles that do not have eng version. The most similar ukr article is the one that correeponds to this red link. Possibly, we will find several ukr articles with the same similarities, so add them as candidates for further preprocessing.


## 2. Baseline

We got a list of non-translated uk articles that have at list 5 incoming links in uk_wiki. There are 82,122 such articles. We encoded all uk non-translated articles as 'bag of words' by its unique incoming uk links.

We got a list of red links in en wiki, for which there are a least 5 incoming links in en wiki. There are 3,593 such links. We encoded every red link as 'bag of words' by its unique incoming en link. Then we 'translated' such encoding to uk: every en article in the 'bag of words' we replaced with its uk corresponding article if exists, and just removed if not. As a result, both uk non-translated articles and en red links are encoded in the same feature space - incoming uk articles.

For every red link we found its Jaccard similarities with all non-translated uk articles, the we select top 5 most similar uk_articles.

We evaluated algorithm performance manually, as it is impossible to find threshold for Jaccard similarity, and correct corresponding uk article is not always the first one among to 5 most similar articles. For about a third among 3,593 red link algorithm didn't found any similar articles. However, for 165 en red links corresponding non-translated uk article was found.

## 3. Results

We provided a list of en red articles for which we found corresponding uk non-translated articles. Such articles are about people, films and places in Ukraine, mostly connected with sport, and about Roman Emperors (with no obvious reasons). We sorted these red links based on number of incoming links from en wiki. We assume that those red links, that have more incoming en links, are more valuable for wikipedia community, and their corresponding uk articles are best candidates for translation.

## 4. Creating the graphs

### Software libraries

* [networkx](https://networkx.github.io)

## 5. Graph embeddings

## Literature

1. P. Goyal, E. Ferrara, Graph Embedding Techniques, Applications, and Performance: A Survey. December 2017. Available at: https://arxiv.org/abs/1705.02801
