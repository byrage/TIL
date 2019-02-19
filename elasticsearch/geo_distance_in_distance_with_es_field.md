geo_distance 쿼리를 실행시킬떄 반경 거리값도 ES에 저장되어 있는 경우
-> query phrase에서 script를 이용해서 비교 

https://www.elastic.co/guide/en/elasticsearch/reference/6.4/query-dsl-script-query.html

~~~json
{
  "script": {
    "script": "doc['location'].arcDistanceInMiles(39.74000930786133, -104.99230194091797) < doc['searchRadius'].value"
  }
}
~~~
