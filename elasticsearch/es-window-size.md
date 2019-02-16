## Elasticsearch Window Size

- from + size가 10000이 넘을 경우 에러가 발생함

~~~json
{
  "from": 10000,
  "size": 5000
}

"reason": {
          "type": "query_phase_execution_exception",
          "reason": "Result window is too large, from + size must be less than or equal to: [10000] but was [15000]. See the scroll api for a more efficient way to request large data sets. This limit can be set by changing the [index.max_result_window] index level parameter."
}
~~~

- 해결방법 
  1. Scroll API 사용 : 효율적인 방법
  2. max_result_windows 세팅
```json
curl -XPUT "http://localhost:9200/my_index/_settings" -d '{ "index" : { "max_result_window" : 500000 } }'
```
