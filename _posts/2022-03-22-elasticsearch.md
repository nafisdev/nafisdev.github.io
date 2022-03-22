---
layout: post
title: ElasticSearch - Now you Know
comments: true
image: 2021.jpg
excerpt: Lets Uncover it
keywords: ""
---

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

Operations: 


### Create index

`GET /thing/`

    curl -i -H 'Accept: application/json' http://localhost:7000/thing/


### Lets be typesafe

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

To understand the relevance score, there is a explain api



    GET /my-index-000001/_explain/0
    {
    "query" : {
        "match" : { "message" : "elasticsearch" }
    }
    }

The API returns the following response:

```cs
{
"_index":"my-index-000001",
"_id":"0",
"matched":true,
"explanation":{
    "value":1.6943598,
    "description":"weight(message:elasticsearch in 0) [PerFieldSimilarity], result of:",
    "details":[
        {
            "value":1.6943598,
            "description":"score(freq=1.0), computed as boost * idf * tf from:",
            "details":[
            {
                "value":2.2,
                "description":"boost",
                "details":[]
            },
            {
                "value":1.3862944,
                "description":"idf, computed as log(1 + (N - n + 0.5) / (n + 0.5)) from:",
                "details":[
                    {
                        "value":1,
                        "description":"n, number of documents containing term",
                        "details":[]
                    },
                    {
                        "value":5,
                        "description":"N, total number of documents with field",
                        "details":[]
                    }
                ]
            },
            {
                "value":0.5555556,
                "description":"tf, computed as freq / (freq + k1 * (1 - b + b * dl / avgdl)) from:",
                "details":[
                    {
                        "value":1.0,
                        "description":"freq, occurrences of term within document",
                        "details":[]
                    },
                    {
                        "value":1.2,
                        "description":"k1, term saturation parameter",
                        "details":[]
                    },
                    {
                        "value":0.75,
                        "description":"b, length normalization parameter",
                        "details":[]
                    },
                    {
                        "value":3.0,
                        "description":"dl, length of field",
                        "details":[]
                    },
                    {
                        "value":5.4,
                        "description":"avgdl, average length of field",
                        "details":[]
                    }
                ]
            }
            ]
        }
    ]
}
}

```








## Aha , we can scale

shard = hash(routing) % number_of_primary_shards

distributive system logic