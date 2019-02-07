# Painless 스크립트 작성 팁
- ES v5.0부터 디폴트 스크립트가 painless로 변경됨

#### Date 타입 필드 현재시간 비교
- ES 내부적으로 Date 타입 필드 저장 시 JodaCompatibleZonedDateTime 타입을 사용
- painless 스크립트 내부에서 날짜인스턴스 생성시에는 Date(java.util.Date) 밖에 사용이 불가능함
- JodaTime, LocalDateTime 등의 클래스는 패키지 인식이 불가능하다.
> ```def date = new org.joda.time.DateTime() 또는 import org.joda.time 추가 시 스크립트 에러 발생```
- 스크립트 파라미터에 LocalDateTime 날짜객체 또는 timestamp를 넘기고, 스크립트 내부에서 ES Date 타입필드를 timestamp로 변환해서 비교하는 방식으로 사용중
- v6.5.1 에서 JodaDateTime.getMillis()는 deprecated 되었으므로 toInstant().toEpochMilli()를 사용하라는 Warning 에러가 발생하므로 주의
> ```
> def now = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss").parse(params['now']).getTime();
> if(doc['closeDate.openTime'].value.toInstant().toEpochMilli() <= now &&
>        now <= doc['closeDate.closeTime'].value.toInstant().toEpochMilli()) {
> ...
> }
> ```

#### doc['variable']과 params['_source']의 차이
- doc['variable']을 사용할 경우 ES 저장 시 doc_value로 저장되어 있는 필드를 타입 그대로 가져온다. 단, mapping 시 "doc_values": false로 설정했다면 사용 불가능
- params['_source']을 사용할 경우 ES에서 색인할때 저장했던 json 필드를 가져오므로 모두 가져올수 있지만 전부 다 String 타입이다.

#### [value, values](https://www.elastic.co/guide/en/elasticsearch/reference/master/modules-scripting-fields.html)
- 단일 필드의 경우 `doc['modificationDate'].value`로 사용해야 함
- value를 붙이지 않을 경우 String으로 취급된다.
- 리스트 필드의 경우 `doc['themes'].values`로 받아서 for, for-each 구문으로  사용해야 함
>~~~groovy
> // for
> for (int i = 0; i < doc['themes'].values.length; i++) {
>    if (doc['themes'].values.get(i) == 1) {
>      ...
>    }
>}
>~~~

>~~~groovy
> // for-each
> for (def theme : doc['themes'].values) {
>    if (theme == 1) {
>      ...
>    }
>}
>~~~

#### [Painless에서 사용할 수 있는 메소드 whitelist](https://www.elastic.co/guide/en/elasticsearch/painless/6.1/painless-api-reference.html)

#### Deprecated arcDistanceInKm
- 미터단위인 arcDistance, arcDistanceWithDefault(기본값 지정 가능)를 사용하고 * 0.001해서 사용하라고함
- See : https://www.elastic.co/guide/en/elasticsearch/reference/5.2/breaking_50_scripting.html#_geopoint_scripts

#### Writing Script in Java String
- String 변수에 커서 위치한 후 Alt+Enter -> Inject language or reference -> Groovy 선택 or String 변수위에 `//language=Groovy` 주석 추가
- 문자열을 Groovy 코드처럼 작성할 수 있고 IntelliJ에서 문법 체크 및 포맷팅 지원
- 진정한 꿀팁!
