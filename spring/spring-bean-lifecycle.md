
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
- See : https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-java-bean-annotation

- [JSR-250](https://en.wikipedia.org/wiki/JSR_250)