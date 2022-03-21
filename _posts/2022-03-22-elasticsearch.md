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
public class ElasticSearch
{

    public ElasticSeaResilient strategy flowrch()
    {}

    public void add()
    {

    }

    public string Search()
    {

    }
    .
    .
    .
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
Create index
Lets be typesafe
Get the analyzer
How to boost
Sort and Aggregate

### Request

`GET /thing/`

    curl -i -H 'Accept: application/json' http://localhost:7000/thing/

### Response

    HTTP/1.1 200 OK
    Date: Thu, 24 Feb 2011 12:36:30 GMT
    Status: 200 OK
    Connection: close
    Content-Type: application/json
    Content-Length: 2

    []





## Aha , we can scale

shard = hash(routing) % number_of_primary_shards

distributive system logic