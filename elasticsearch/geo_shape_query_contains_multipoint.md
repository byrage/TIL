- 멀티포인트 필드에 거리 비교 쿼리
~~~json
"locations": [
  {
    "lat": 37.509113,
    "lon": 127.029247
  },
  {
    "lat": 37.503743,
    "lon": 127.0517026
  },
  {
    "lat": 37.505215,
    "lon": 127.039976
  },
  {
    "lat": 37.482589,
    "lon": 127.060575
  }
]
~~~

~~~java
boolQueryBuilder.filter(QueryBuilders.geoDistanceQuery("locations")
                                      .point(location)
                                      .distance(3000, DistanceUnit.METERS));
~~~

- distance가 ES 필드내에 색인 되어 있을 경우
- script query로 `doc['locations'].values`의 for문으로 arcDistance 코드는 동작하지 않음. 확인필요
~~~java
@Language("groovy")
String code = "def campaignFields = params['campaignFields'];\n"
        + "for (location in doc['locations'].values) {\n"
        + "    if (location.arcDistance(params['lat'], params['lon']) <= doc['displayRadius'].value) {\n"
        + "        return true;\n"
        + "    }\n"
        + "}\n"
        + "return false;";

Script script = new Script(ScriptType.INLINE, SCRIPT_LANGUAGE, code,
                            ImmutableMap.of());

boolQueryBuilder.must(QueryBuilders.scriptQuery(script));
~~~