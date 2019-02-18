![](https://raw.githubusercontent.com/byrage/TIL/master/img/spring-bean-lifecycle.jpg)
- [JSR-250](https://en.wikipedia.org/wiki/JSR_250)에 정의된 @PostConstruct, @PreDestroy가 먼저 실행되고, Spring InitializingBean.afterPropertiesSet(), init-method / DisposableBean.destroy(), destroy-method가 순차적으로 실행된다.

- init-method
~~~java
public class BeanOne {

    public void init() {
        // initialization logic
    }
}

public class BeanTwo {

    public void cleanup() {
        // destruction logic
    }
}

@Configuration
public class AppConfig {

    @Bean(initMethod = "init")
    public BeanOne beanOne() {
        return new BeanOne();
    }

    @Bean(destroyMethod = "cleanup")
    public BeanTwo beanTwo() {
        return new BeanTwo();
    }
}
~~~
- See : 
  - https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-java-bean-annotation
  - https://stackoverflow.com/questions/8519187/spring-postconstruct-vs-init-method-attribute