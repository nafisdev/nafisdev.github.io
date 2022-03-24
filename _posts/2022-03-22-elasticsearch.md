---
layout: post
title: ElasticSearch - Now you Know
comments: true
image: elasticsearchheader.jpeg
excerpt: Lets Uncover it
keywords: ""
---

We have a playground for you [tgifplayground](http://20.106.20.226:9200/).

## Whats hidden

There are lots of tutorial for elasticsearch online, but here we'll proceed the engineer way. ElasticSearch is a documented oriented database similar to other mainstream no-sql dbs. The crux algorithm is [InvertedIndex](https://www.geeksforgeeks.org/inverted-index/) and the elasticsearch uses [ApacheLucene](https://lucene.apache.org/)
The following code gives a gist the undergoing logic.

```cs
#include <bits/stdc++.h>

using namespace std;



struct Doc
{
    int id;
    string data;
};



class ElasticSearch
{

    public:
    
    unordered_map<string, vector<Doc> > invertedIndex;
    ElasticSearch()
    {

    }

    void add(Doc *d)
    {
        vector<string> characters =  analyzeAndTokenize(d->data);
        for(auto a: characters)
        {
            // cout<<a;
            // cout<<endl<<d->data;
            invertedIndex[a].push_back(*d);
            // // docmap[d.id]=d.data;
        }
    }
    

    vector<string> analyzeAndTokenize(string word)
    {
        return {"Nafis" ,  "I" , "am"};
    }

    void Search(string word)
    {
        vector<Doc> docs;
        for(auto a: invertedIndex)
        {
            if(a.first==word)
            {
               docs = invertedIndex[word];
               break;
            }
        }
        for(auto a : docs)
        {
            cout<<a.data;
        }
    }
};



int main()
{
    ElasticSearch *e =  new ElasticSearch();
    Doc *d = new Doc();
    d->id=1;
    d->data="Hey I am Nafis, Find Me";
    e->add(d);
    e->Search("Nafis");
}
``` 

<figure>
  <img src="{{ '/images/elastic-lucene.png' | prepend: site.baseurl }}" alt=""> 
  <figcaption>Fig1. - Lucene Index</figcaption>
</figure>


## Lets Start

Once, the fundamental logic is standardized, it can be wrapped around. And, apache community has done a commendable job to expose this logic through api. 
Now, all we have to learn is the schema and standard operations to manipulate default behaviour.

## Operations: 


### Create index

    PUT /index

### Lets be typesafe [Mapping]

    PUT /my_index/_mapping?pretty
    {
    "properties": {
        "email": {
        "type": "keyword"
        }
    }
    }


### Get the analyzer

<figure>
  <img src="{{ '/images/analyzers.png' | prepend: site.baseurl }}" alt=""> 
  <figcaption>Fig1. - Analyzer Workflow</figcaption>
</figure>
### Request

    PUT my-index-000001
    {
    "settings": {
        "analysis": {
        "analyzer": {
            "my_custom_analyzer": {
            "type": "custom", 
            "tokenizer": "standard",
            "char_filter": [
                "html_strip"
            ],
            "filter": [
                "lowercase",
                "asciifolding"
            ]
            }
        }
        }
    }
    }

    POST my-index-000001/_analyze
    {
    "analyzer": "my_custom_analyzer",
    "text": "Is this <b>déjà vu</b>?"
    }


### How to boost and set Score

There is a mathematical formula for getting the 
[relevance score.](https://www.infoq.com/articles/similarity-scoring-elasticsearch/). Morever,  individual fields can be boosted automatically — count more towards the relevance score — at query time, with the boost parameter as follows:

    PUT my-index-000001
    {
    "mappings": {
        "properties": {
        "title": {
            "type": "text",
            "boost": 2 
        },
        "content": {
            "type": "text"
        }
        }
    }
    }

To understand the relevance score, there is a _explain api



    GET /my-index-000001/_explain/0
    {
    "query" : {
        "match" : { "message" : "elasticsearch" }
    }
    }



## Aha , we can scale
<figure>
  <img src="{{ '/images/clusterelasticsearch.png' | prepend: site.baseurl }}" alt=""> 
  <figcaption>Fig1. - Analyzer Workflow</figcaption>
</figure>

The ElasticSearch distributive architecture is configurable and can be orchestrated through config files
We can specify our own routing parameters and customise our indexing.
The formula is :
shard = hash(routing) % number_of_primary_shards

    PUT my-index-000001/_doc/1?routing=user1&refresh=true 
    {
    "title": "This is a document"
    }

    GET my-index-000001/_doc/1?routing=user1 

