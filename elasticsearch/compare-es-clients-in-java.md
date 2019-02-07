## Java Low Level Client, Java High Level Client
### Java Low Level REST Client (Since v5.0)
#### Overview
- 엘라스틱서치의 공식 low-level 클라이언트
- http를 통해서 클러스터와 통신 
- 마샬링 및 언마샬링을 사용자가 직접 구현해야 함
- 모든 ElasticSearch 버전과 호환됨
#### features
- 적은 ES 버전 의존성 (TransportClient에 비해)
- 사용가능한 모든 노드의 로드밸런싱
- 노드 장애 또는 특정 응답코드 반환 시 failover 
- 실패한 연결 페널티부과 (실패한 노드가 재 시도되었는지 여부는 연속 실패 횟수에 따라 다르며 시도 횟수가 많을수록 클라이언트는 동일한 노드를 다시 시도하기 전에 기다리는 시간이 길어집니다)
- 지속적인 커넥션
- 응답, 요청 로깅
- 클러스터 노드 자동 디스커버리 (옵셔널)

### Java High Level REST Client (Since v5.6)
#### Overview
- 엘라스틱서치의 공식 high-level 클라이언트
- low-level 클라이언트를 기반으로 함
- ES API(기능) 관련 메소드 제공
- 마샬링 및 언마샬링 지원
#### Additional Explanation
- API를 sync, async 메소드로 호출할 수 있음
  - sync : response 객체 리턴
  - async : listner(low level client에서 관리하는 쓰레드풀에서 처리)를 argument로 넘겨서 응답 성공 (onResponse) 또는 에러 (onFailure)시 콜백을 처리할 수 있음.  
- Elasticsearch core 프로젝트에 의존하고 있음. 즉, TransportClient와 동일한 argument를 받고 동일한 응답 객체를 반환

## TransportClient
- will deprecating in v7.0, will removing in v8.0 
- ES 초기에 사용하던 클라이언트
- Transport(deault 9300)를 이용해서 단일 노드와 통신하는 방식
- 요청을 java에서 직렬화므로 http 요청보다 속도가 빠름
- 클라이언트와 서버의 ES, JVM 버전이 다르면 이슈가 발생할 수도 있음

## Jest
- See : https://github.com/searchbox-io/Jest/blob/master/jest/README.md#comparison-to-native-api

## Transport vs ES Java Client
### [Motivations around a new Java client](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/_motivations_around_a_new_java_client.html#_motivations_around_a_new_java_client)
- TransportClient란
  - ES 초창기부터 존재하고 있었음
  - transport 프로토콜 (default 9300)를 사용해서 ES와 커뮤니케이션 하는 클라이언트
  - 클라이언트와 ES 버전이 다를 경우 호환성 문제를 일으킬 수 있다.
- 그래서 2016년에 low level REST client를 릴리즈
- Apache HTTP client를 기반으로 버전에 상관없이 ES 클러스터와 통신 가능
- low level REST Client를 기반으로 요청 마샬링과 응답 언마샬링을 처리하는 High Level REST Client를 릴리즈

## 크게 중요하진 않은 문서들
### Migration Transport to ES Java Client
#### [의존성 업데이트](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/_updating_the_dependencies.html)
- org.elasticsearch.client:transport -> org.elasticsearch.client:elasticsearch-rest-client
#### [client 생성 코드 변경](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/_changing_the_client_8217_s_initialization_code.html)
- Transport
```java
Settings settings = Settings.builder()
        .put("cluster.name", "prod").build();

TransportClient transportClient = new PreBuiltTransportClient(settings)
        .addTransportAddress(new TransportAddress(InetAddress.getByName("localhost"), 9300))
        .addTransportAddress(new TransportAddress(InetAddress.getByName("localhost"), 9301));
```
- RestHighLevelClient
```java
RestHighLevelClient client = new RestHighLevelClient(
        RestClient.builder(
                new HttpHost("localhost", 9200, "http"),
                new HttpHost("localhost", 9201, "http")));
```
- `RestClient`(Low level Client)는 Thread-safe 함
- application 시작시간 또는 처음 요청이 발생되었을때 인스턴스 생성된다.
              
#### [application 코드 변경](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/_changing_the_application_8217_s_code.html)
- `RestHighLevelClient`와 `Transport`는 같은 request, response 객체를 지원하지만 지원 메소드가 약간 다름
- 기존의 `client.prepareIndex()`와 같은 레거시 메소드의 request builder 지원하지 않음. 대신 `new IndexRequest()`와 같은 request 생성자 방식으로 변경하고, sync/async를 선택할 수 있다.

### [State of the official Elasticsearch Java clients](https://www.elastic.co/blog/state-of-the-official-elasticsearch-java-clients)
- REST Client 개발시점에 작성한 문서
- TransportClient의 문제점과 개발 로드맵을 설명함

### [Benchmarking REST Client vs TransportClient](https://www.elastic.co/blog/benchmarking-rest-client-transport-client)
- REST Client 초기 버전 (v5.0.0-alpha-version)에 테스트한 결과로 큰 의미는 없음
- 성능테스트 결과는 TransportClient가 살짝 좋거나 거의 비슷함
- 자세한 결과는 링크 참고
#### 결론
- REST Client를 사용함으로 application과 ES간의 커플링을 끊을 수 있음
- 지금은 성능이 안나오지만 계속 개선해 나가겠다고 함...