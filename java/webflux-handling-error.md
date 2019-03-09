#### Method
- `Publisher.onErrorReturn(final T fallback)` : throwable과 상관없는 고정적인 fallback을 리턴할때 사용
- `Publisher.onErrorResume(Function<? super Throwable, ? extends Mono<? extends
			T>> fallback)` : throwable을 받아서 fallback을 처리. 다이나믹 fallback을 리턴할 수 있음
- `Publisher.switchIfEmpty(Mono<? extends T> alternate)` : `Publisher.empty()` 체크에 사용. `flatMap()` 메소드에서 null 체크로는 `empty()` 체크가 불가능함

#### Link
- https://www.baeldung.com/spring-webflux-errors
- https://projectreactor.io/docs/core/release/reference/#which.errors
- https://stackoverflow.com/questions/47242335/how-to-check-if-mono-is-empty

#### Example
~~~java
return someMono.flatMap(dto ->
                someService.doSomething(...)
                          .onErrorResume(throwable -> Mono.empty())
                          .flatMap(result -> Mono.just(...))
                          .switchIfEmpty(Mono.just(...)
                    .doOnSuccessOrError(handleResult(...))
                    .onErrorReturn(Lists.newArrayList())
                    .subscribe();
~~~~
