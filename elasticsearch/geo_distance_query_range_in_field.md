- 거리 비교 시 제한반경 거리값도 ES에 저장되어 있는 경우 geo_distance 쿼리 사용이 불가능함
- query phrase에서 script를 이용해서 거리비교 
- https://www.elastic.co/guide/en/elasticsearch/reference/6.4/query-dsl-script-query.html

~~~json
{
  "query": {
    "script": {
      "script": "doc['location'].arcDistance(39.74000930786133, -104.99230194091797) < doc['searchRadius'].value"
    }
  }
}
~~~
- arcDistanceInKm, arcDistainceInMiles가 deprecated 되었음 (참고 : [painless writing tips](painless-writing-tips.md) - Deprecated arcDistanceInKm)