- webflux로 mvc test를 수행할때 WebTestClient를 사용하는 예제
  
#### example
~~~java
@RunWith(SpringRunner.class)
@WebFluxTest(SampleController.class)
public class SampleControllerTest {

    @Autowired
    private WebTestClient webTestClient;

    @MockBean
    private SomeService someService;

    @Test
    public void getBaeminCampaign() {

        webTestClient.get().uri(UriBuilder.from("/foo/bar")
                                          .queryParam("name", "hi")
                                          .build())
                     .accept(MediaType.APPLICATION_JSON).exchange()
                     .expectStatus().isOk()
                     .expectBody()
                     .jsonPath("$.name").isEqualTo("hi")
                     .returnResult();
    }
~~~