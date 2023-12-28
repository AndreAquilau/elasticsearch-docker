# ELK (Elasticsearch + Kibana) 7.6.2 with Docker


```
docker-compose up -d
```

* User `elastic` and password `123change...`
* Elasticsearch: http://localhost:9200
* Kibana: http://localhost:5601
* For elasticsearch configuration, see `elasticsearch.yml` in `elasticsearch/config` dir
* Search for test `http://localhost:9200/_search`


## Elasticsearch basics
* index documents

* size, from, sort
* query
  https://www.elastic.co/guide/en/elasticsearch/reference/current/term-level-queries.html
  * range
  * exists
  * term
  * terms   
    * boost
  https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html
  * bool
    * filter
    * must
    * must_not
    * should
    * minimum_should_match
  https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html
  * match (variations)
https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html
* aggs
  * metrics: avg, max, min, sum, cardinality, top hits
  * buckets: date histogram, date range, histogram, range, filter, terms 
* analyzers 
  * mappings https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html
  * brazilian https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html#brazilian-analyzer

```py
# GET /
GET /_cat/indices
GET /_cat/indices?v
GET /_cat/shards
GET /_cat/shards?v


GET /_cat/indices?v&pretty=true

DELETE hits-27.12.2023 

POST /_bulk

GET /hits-27.12.2023/_search
GET /hits-27.12.2023/_doc/2
GET /hits-27.12.2023/_mapping


# Use * para busca
GET /hits*/_search

# size - quantidade de registros
# from - inicio de busca
# sort -> asc e desc
POST /hits*/_search
{
  "size": 5, 
  "from": 0,
  "sort": [
    {
      "Year.keyword": "asc"
    },
    {
      "Genre.keyword": "asc"
    }
  ]
}

# Maior ou igual e Menor ou igual
POST /hits*/_search
{
  "size": 5, 
  "from": 0,
  "query": {
    "range": {
      "Number": {
        "gte": 10,
        "lte": 12
      }
    }
  }
}


# Maior e Menor
POST /hits*/_search
{
  "size": 5, 
  "from": 0,
  "query": {
    "range": {
      "Number": {
        "gt": 30,
        "lt": 32
      }
    }
  }
}

# tipo "term" só funciona para o campo keyword

# Várias queries
POST /hits-2023.12.28/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "Number": {
              "gte": 1,
              "lte": 500
            }
          }
        }
      ],
      "must": [
        {
          "match": {
            "Album": {
              "query": "Highway Revisite",
              "minimum_should_match": 2,
              "fuzziness": 1,
              "operator": "or"
            }
          }
        }
      ]
    }
  }
}

# Várias queries
POST /hits-2023.12.28/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "Number": {
              "gte": 1,
              "lte": 500
            }
          }
        }
      ],
      "must": [
        {
          "match": {
            "Artist": {
              "query": "beatles",
              "minimum_should_match": 2,
              "fuzziness": 1
            }
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "Artist": {
              "query": "Guns in Rose",
              "fuzziness": 1,
              "minimum_should_match": 2
            }
          }
        }
      ]
    }
  }
}


# Várias queries
POST /hits-2023.12.28/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "Number": {
              "gte": 1,
              "lte": 500
            }
          }
        }
      ],
      "must": [
        {
          "match": {
            "Artist": {
              "query": "beatles",
              "minimum_should_match": 2,
              "fuzziness": 1
            }
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "Artist": {
              "query": "Guns in Rose",
              "fuzziness": 1,
              "minimum_should_match": 2
            }
          }
        }
      ],
      "should": [
        {
          "match": {
            "Subgenre": "beat"
          }
        }
      ]
    }
  }
}

# Os 4 piláres de uma busca.
POST /hits-2023.12.28/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "Number": {
              "gte": 1,
              "lte": 500
            }
          }
        }
      ],
      "must": [
        {
          "match": {
            "Artist": {
              "query": "beatles",
              "minimum_should_match": 2,
              "fuzziness": 1
            }
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "Artist": {
              "query": "Guns in Rose",
              "fuzziness": 1,
              "minimum_should_match": 2
            }
          }
        }
      ],
      "should": [
        {
          "match": {
            "Subgenre": "beat"
          }
        },
        {
          "match": {
            "Subgenre": "Psychedelic"
          }
        }
      ],
      "minimum_should_match": 0
    }
  }
}


# Os 4 piláres de uma busca.
POST /hits-2023.12.28/_search
# Os 4 piláres de uma busca.
POST /hits-2023.12.28/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "Number": {
              "gte": 1,
              "lte": 500
            }
          }
        }
      ],
      "must": [
        {
          "match": {
            "Artist": {
              "query": "beatles",
              "minimum_should_match": 2,
              "fuzziness": 1
            }
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "Artist": {
              "query": "Guns in Rose",
              "fuzziness": 1,
              "minimum_should_match": 2
            }
          }
        }
      ],
      "should": [
        {
          "match": {
            "Subgenre": "beat"
          }
        },
        {
          "match": {
            "Subgenre": "Psychedelic"
          }
        }
      ],
      "minimum_should_match": 0
    }
  }
}       
```