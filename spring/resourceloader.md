- resource 파일을 로딩에 ClassPathResource를 사용했더니, jar를 통해 java를 실행시키는 시점에 리소스 로딩에 실패함

- https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ResourceLoader.html
- https://blog.outsider.ne.kr/794
> 5.3.2 ClassPathResource
> 이 클래스는 클래스패스에서 얻어와야하는 리소스를 나타낸다. 이 클래스는 쓰레드 컨텍스트 클래스 로더, 주어진 클래스로더, 클래스를 로딩하려주 주어진 클래스를 모두 사용한다.
> 
> Resource 구현체는 클래스패스 리소스가 파일시스템에 존재하면 java.io.File같은 해결책을 지원하지만 jar에 존재하는 클래스패스 리소스는 지원하지 않고 파일시스템으로 확장하지도 않는다. 이를 다양한 Resource 구현체에 배치시키려면 java.net.URL같은 해결책을 항상 지원해야 한다.

> ClassPathResource는 ClassPathResource 생성자를 사용해서 자바코드로 명시적으로 생성하지만 때로는 경로를 나타내는 String 아규먼트를 받아들이는 API를 호출할 때 암묵적으로 생성될 것이다. 후자의 경우 JavaBeans PropertyEditor이 문자열 경로에서 특수한 classpath: 접두사를 인식할 것이고 이 경우에 ClassPathResource를 생성할 것이다.

~~~java
@Autowired
private final ResourceLoader resourceLoader;

private String getScheme(String simpleClassName) throws IOException {

    StringBuilder scheme = new StringBuilder();
    InputStream inputStream = resourceLoader.getResource(String.format("%s/scheme/%s.json", CLASSPATH_URL_PREFIX , simpleClassName)).getInputStream();
    try (BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream))) {
        reader.lines().forEach(scheme::append);
    }

    return scheme.toString();
}
~~~