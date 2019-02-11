## store
* https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-store.html
* 디폴트 설정 => `"store" : "false"`
* false로 설정하더라도 _source를 통해서 색인시 사용한 json 데이터를 조회할 수 있음
* 검색 시 stored_fields 옵션을 줄때는 `"store" : "true"` 필드만 확인할 수 있음

```json
PUT /shop
{
  "mappings": {
    "_doc": {
      "properties": {
        "id":{
          "type": "integer"
        }
        "store": {
          "type": "keyword",
          "store": "true"
        },
        "no_store": {
          "type": "keyword",
          "store": "false"
        }
      }
    }
  }
}

POST /shop/_doc
{
  "id" : 1,
  "store" : "test",
  "no_store" : "test"
}

POST /shop/_doc/_search
{
    "_source" : [],
    "stored_fields": [ "store", "no_store" ]
}

result
{
  "_index": "shop",
  "_type": "_doc",
  "_id": "SfdAMWgB339bf_DSuAyv",
  "_score": 1,
  "_source": {
    "id": 2,
    "store": "test",
    "no_store": "test"
  },
  "fields": {
    "store": [
      "test"
    ]
  }
}
```


## index
* https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-index.html
* https://www.elastic.co/guide/en/elasticsearch/reference/current/index-options.html
* 디폴트 설정 => `"index" : "true"` 
* index 된 필드만 query 가능
* index 하위 옵션으로 docs, freqs, positions, offsets가 있음. numeric 필드에서는 옵션설정 불가능

```json
PUT shop2
{
  "mappings": {
    "_doc": {
      "properties": {
        "index_id":{
          "type":"integer",
          "index" : "true"
        },"noindex_id":{
          "type":"integer",
          "index" : "false"
        }        
      }
    }
  }
}

POST shop2/_doc
{
  "index_id" : 1,
  "noindex_id" : 1
}

POST shop2/_doc/_search
{
  "query" : {
    "term" : {
      "index_id" : 1
    }
  }
}

result
{
    "_index": "shop2",
    "_type": "_doc",
    "_id": "TPdJMWgB339bf_DSaQwD",
    "_score": 1,
    "_source": {
      "index_id": 1,
      "noindex_id": 1
    }
}

POST shop2/_doc/_search
{
  "query" : {
    "term" : {
      "noindex_id" : 1
    }
  }
}

result
...
"caused_by": {
  "type": "illegal_argument_exception",
  "reason": "Cannot search on field [noindex_id] since it is not indexed."
}
```