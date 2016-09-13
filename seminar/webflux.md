목적 : 효율적인 인프라스터럭쳐의 사용



microservice로 전환되며 많은 network I/O가 발생.. -> CPU 사용률은 적지만 하나의 쓰레드당 메모리를 차지함.

기존의 쓰레드풀 방식은 쓰레드 개수가 '유한적' 일 수 밖에 없다.



비동기 + blocking(restTemplate) : 메인쓰레드는 종료시키지만 새로운 쓰레드가 생성되므로 효율적인 리소스 사용에 이점이 없다.



NIO

- java.NIO package 공부
- 100개의 IO 작업을 1개의 쓰레드에서 한번에 묶어서 처리 



CompletableFuture nested callback : thenApplyAsync()



AsyncAwait 라이브러리

- Async.await(Callback c) : 컴파일러가 코드를 동적으로 변경. 콜백의 로직을 밖으로 빼서 처리할 수 있음.. syntactic sugar



IntStream.boxed()



여러개의 Item을 묶어서 처리하는 방법

- Mono.when().blocked()
- Flux().blockLast()



subscribe() -> default subscriber가 동작. request(unbounded)

subscriber를 직접 구현해서 request 숫자를 제어. -> backpressure



Mono<List> : Flux = List : Iterable



#### 개선

1. service

flatmap ->

​	flatmap ->

​         	flatmap->

로 return 하는부분을 **Compose** 로 변경



2. Controller

exception or complete 타입을 가지고 Response를 결정해서 response



onErrorXXX 동작방식 

- 해당 publisher는 onError 발생시에 끝냄, 대신 이후에 새로운 스트림을 열어서 해당 값을 반환함



reactor 디버그 모드??

테스트케이스에서 Mock 사용시 체인이 끊기지 않도록 주의. 코드가 동작하지 않을 수 있다.



Reactor 3 부록 참고



#### 주의사항

코드에서 subscribe() 사용 금지, 스프링 Webflux Controller에 위임해라

ReactiveSpringDataElasticsearch가 있나??

직접 비동기 체인 시작하지마라. Mono.just()

subscribeOn(), publishOn() 모르면 쓰지마라.



풀 논블로킹 스트림 : ??

업스트림, 다운스트림 : ??



함수형 Router, Handler 방식은 아직 인큐베이터 수준.. 어노테이션 방식 컨트롤러를 작성하자



Content-Type 별 타입 매핑

- application/json -> mono, flux
- application/stream+json -> flux only
- text/event+json -> flux only



Order order -> 바디 파싱이 다 끝난 후에 메소드 실행

Mono<Order> -> 리액티브 스택에서만 동작하며 바디를 받기 전 부터 메소드를 실행시킬 수 있음. **



#### Use case

-> API 파라미터를 Flux<Order>를 받아서 ReactiveRepository.insert()를 호출하는데 timeWindow, bufferWindow를 걸어서 모아서 여러개의 요청을 한개의 쓰레드로 한번에 insert 할 수 있을 듯



응답 content type이 event stream 방식이면 spring 기본설정에서 backpressure를 3~40개로 설정함



reactive mongo db에서 @Tailable 어노테이션을 사용할 경우 order by를 스트리밍 방식으로 지원함