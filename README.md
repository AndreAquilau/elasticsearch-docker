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


```
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

DELETE /dio-experts

# Configuração de Idioma
# Configuração da tabela
PUT /dio-experts
{
    "aliases": {},
    "mappings": {
      "properties": {
        "content": {
          "type": "text",
          "analyzer": "rebuilt_brazilian"
        }
      }
    },
    "settings": {
      "analysis": {
        "filter": {
          "brazilian_stemmer": {
            "type": "stemmer",
            "language": "brazilian"
          },
          "brazilian_keywords": {
            "keywords": [
              "exemplo"
            ],
            "type": "keyword_marker"
          },
          "brazilian_stop": {
            "type": "stop",
            "stopwords": "_brazilian_"
          }
        },
        "analyzer": {
          "rebuilt_brazilian": {
            "filter": [
              "lowercase",
              "brazilian_stop",
              "brazilian_keywords",
              "brazilian_stemmer"
            ],
            "tokenizer": "standard"
          }
        }
      }
    }
}



POST /dio-experts/_doc/1
{
  "content" : "Vejo muito valor no aprendizado e no trabalho em equipe. Gosto de ser parte importante de um time e gosto de ter pessoas com quais eu possa adquirir mais conhecimento. Minha entrada na área de tecnologia pode ser relativamente tardia, mas acredito que eu possa agregar com a experiência que obtive durante minha atuação no mercado e em diferentes áreas."
}

GET /dio-experts/_search

GET /dio-experts


# ANALYZE


GET /dio-experts/_analyze
{
  "analyzer": "rebuilt_brazilian",
  "text": "Vejo muito valor no aprendizado e no trabalho em equipe. Gosto de ser parte importante de um time e gosto de ter pessoas com quais eu possa adquirir mais conhecimento. Minha entrada na área de tecnologia pode ser relativamente tardia, mas acredito que eu possa agregar com a experiência que obtive durante minha atuação no mercado e em diferentes áreas."
}


POST /dio-experts/_search
{
  "query": {
    "match": {
      "content": {
        "query": "vej"
      }
    }
  }
}


POST /teste-aggs/_doc
{
  "data" : "2020-04-04",
  "numero" : 15,
  "status": "active"
}


GET /teste-aggs/_mapping

POST /teste-aggs/_search
{
  "size": 20
}


# Agregação, bom para gráficos
POST /teste-aggs/_search
{
  "size": 2,
  "from": 0,
  "aggs": {
    "perStatus": {
      "terms": {
        "field": "status.keyword",
        "size": 10
      }
    },
    "perDay": {
      "date_histogram": {
        "field": "data",
        "calendar_interval": "day"
      }
    }
  }
}

POST /teste-aggs/_search
{
  "size": 2,
  "from": 0,
  "aggs": {
    "perStatus": {
      "terms": {
        "field": "status.keyword",
        "size": 10
      }
    },
    "perDay": {
      "date_histogram": {
        "field": "data",
        "calendar_interval": "1w"
      }
    }
  }
}


# Agregação sub-agregação
POST /teste-aggs/_search
{
  "size": 2,
  "from": 0,
  "aggs": {
    "perStatus": {
      "terms": {
        "field": "status.keyword",
        "size": 10
      }
    },
    "perDay": {
      "date_histogram": {
        "field": "data",
        "calendar_interval": "1d"
      },
      "aggs": {
        "perStatus": {
          "terms": {
            "field": "status.keyword",
            "size": 10
          }
        }
      }
    }
  }
}
```