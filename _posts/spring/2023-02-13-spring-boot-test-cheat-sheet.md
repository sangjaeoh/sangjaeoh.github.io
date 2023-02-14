---
layout: single
title: "Spring Boot Test Cheat Sheet"
categories: [Spring]
tag: [Spring, Java, Test, CheatSheet]
author_profile: true
toc: true
toc_sticky: true
---

📝 Spring Boot Test Cheat Sheet 입니다. 설명은 없고 자주 사용하는 테스트 코드 복붙용 입니다.
- [JUnit 5, AssertJ Cheat Sheet](/java/TDD(Test-Driven-Development)-테스트-주도-개발){:target="_blank"}

<br/>



## build.gradle
```groovy
dependencies {
    runtimeOnly 'com.h2database:h2'
    implementation 'com.google.guava:guava:31.1-jre'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'io.rest-assured:rest-assured'
}
```
<br/>
<br/>



## application-test.yml
```yaml
spring:
  datasource:
    url: jdbc:h2:mem:test
    username: sa
    password:
    driver-class-name: org.h2.Driver
  jpa:
    hibernate:
      ddl-auto: 'create-drop'
```
<br/>
<br/>




## import
```java
import static org.hamcrest.Matchers;
import static org.junit.jupiter.api.Assertions.*;
import static org.assertj.core.api.Assertions.*;
```
<br/>
<br/>




## DatabaseCleanup
JPA용, 애플리케이션 내 모든 엔티티 테이블 초기화 클래스
```java
@Service
@ActiveProfiles("test")
public class DatabaseCleanup implements InitializingBean {
    @PersistenceContext
    private EntityManager entityManager;

    private List<String> tableNames;

    @Override
    public void afterPropertiesSet() {
        tableNames = entityManager.getMetamodel().getEntities().stream()
                .filter(e -> e.getJavaType().getAnnotation(Entity.class) != null)
                .map(e -> CaseFormat.UPPER_CAMEL.to(CaseFormat.LOWER_UNDERSCORE, e.getName()))
                .collect(Collectors.toList());
    }

    @Transactional
    public void execute() {
        entityManager.flush();
        entityManager.createNativeQuery("SET REFERENTIAL_INTEGRITY FALSE").executeUpdate();

        for (String tableName : tableNames) {
            entityManager.createNativeQuery("TRUNCATE TABLE " + tableName).executeUpdate();
            entityManager.createNativeQuery("ALTER TABLE " + tableName + " ALTER COLUMN ID RESTART WITH 1").executeUpdate();
        }

        entityManager.createNativeQuery("SET REFERENTIAL_INTEGRITY TRUE").executeUpdate();
    }
}
```
<br/>
<br/>



## Acceptance Abstract
RestAssured 사용시 공통 부모 클래스. 랜덤포트와 테이블 초기화
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public abstract class AcceptanceTest {

    @LocalServerPort
    int port;

    @Autowired
    private DatabaseCleanup databaseCleanup;

    @BeforeEach
    public void setUp() {
        RestAssured.port = port;
        databaseCleanup.execute();
    }
}
```
<br/>
<br/>




## RestAssured
- [RestAssured 가이드](https://github.com/rest-assured/rest-assured/wiki/Usage){:target="_blank"}

```java
public static ExtractableResponse<Response> 자동차_등록_요청(String name) {
    Map<String, Object> params = new HashMap<>();
    params.put("name", name);

    return RestAssured
            .given().log().all()
            .body(params)
            .contentType(MediaType.APPLICATION_JSON_VALUE)
            .when().post("/api/cars")
            .then().log().all()
            .extract();
}
```
```java
public static void 자동차_등록됨(ExtractableResponse<Response> response) {
    assertThat(response.statusCode()).isEqualTo(HttpStatus.CREATED.value());
    assertThat(response.header("Location")).isNotBlank();
}
```
```java
public static ExtractableResponse<Response> 정보_조회_요청(String accessToken) {
    return RestAssured
            .given().log().all()
            .auth().oauth2(accessToken)
            .when().get("/members/me")
            .then().log().all()
            .extract();
}
```
<br/>
<br/>



## @ExtendWith(SpringExtension.class), @MockBean
```java
@ExtendWith(SpringExtension.class)
public class SpringExtensionTest {

    @MockBean
    private LineRepository lineRepository;

    @MockBean
    private StationService stationService;

    @Test
    void findAllLines() {
        // given
        when(lineRepository.findAll()).thenReturn(Lists.newArrayList(new Line()));
        LineService lineService = new LineService(lineRepository, stationService);

        // when
        List<LineResponse> responses = lineService.findLineResponses();

        // then
        assertThat(responses).hasSize(1);
    }
}
```
<br/>
<br/>



## @ExtendWith(MockitoExtension.class), @Mock, @InjectMocks
```java
@ExtendWith(MockitoExtension.class)
public class MockitoExtensionTest {

    @Mock
    private CarRepository carRepository;

    @InjectMocks
    private CarService carService;

    @DisplayName("이름을 받아 자동차를 저장한다.")
    @Test
    public void save(){
        // given
        final String NAME = "Boong-Boong";
        when(carRepository.save(any())).thenReturn(Optional.of(new Car(NAME)));
        
        // when
        CarResponse carResponse = carService.save(NAME);

        // then
        assertThat(carResponse.getName()).isEqualTo(NAME);
    }
}
```
<br/>
<br/>



## @WebMvcTest, @MockBean, MockMvc
```java
@WebMvcTest(CarController.class)
public class CarControllerTest {

    private static final String API_BASE_PATH = "/api/cars";

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private CarService carService;

    @Autowired
    private ObjectMapper objectMapper;

    @DisplayName("자동차를 생성한다.")
    @Test
    public void createCar() throws Exception {
        //given
        CarRequest carRequest = new carRequest("Boong-Boong");
        CarResponse carResponse = new CarResponse(1L, carRequest.getName());
        given(carService.save(any())).willReturn(carResponse);

        //when, then
        mockMvc.perform(post(API_BASE_PATH)
                .content(objectMapper.writeValueAsString(carRequest))
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.id").value(carResponse.getId()));
    }
}
```
<br/>
<br/>



## @SpringBootTest, @AutoConfigureMockMvc, MockMvc
```java
@SpringBootTest
@AutoConfigureMockMvc
public class CarControllerMockTest {

    private static final String API_BASE_PATH = "/api/cars";

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @DisplayName("자동차를 생성한다.")
    @Test
    public void createCar() throws Exception {
        //given
        CarRequest carRequest = new carRequest("Boong-Boong");
        CarResponse carResponse = new CarResponse(1L, carRequest.getName());

        //when, then
        mockMvc.perform(post(API_BASE_PATH)
                .content(objectMapper.writeValueAsString(carRequest))
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.id").value(carResponse.getId()));
    }
}
```
<br/>
<br/>



## @DataJpaTest, @PersistenceContext
- JPA 관련 설정만 로드
- 데이터 소스, 엔티티 매니저 등 생성
- @Entity 어노테이션이 붙은 클래스 및 Spring Data JPA 에 대한 설정들 적용
- 기본으로 in-memory database 사용
- @AutoConfigureTestDatabase를 사용하여 어떤 database를 연결할지 선택할 수 있다.

```java
@DataJpaTest
@ActiveProfiles("test")
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class CarRepositoryTest {

    @Autowired
    private CarRepository carRepository;

    @PersistenceContext
    private EntityManager entityManager;

    @Test
    @DisplayName("자동차를 저장한다.")
    public void save() {
        
        ...

        Car savedCar = carRepository.save(new Car("Boong-Boong"));

        entityManager.flush();
        entityManager.clear();
        
        ...
    }
}
```
<br/>
<br/>



## @MybatisTest
- [Mybatis 테스트 가이드](http://mybatis.org/spring-boot-starter/mybatis-spring-boot-test-autoconfigure/index.html){:target="_blank"}
- 기본으로 in-memory database 사용
- @AutoConfigureTestDatabase를 사용하여 어떤 database를 연결할지 선택할 수 있다.

```java
@ExtendWith(SpringExtension.class)
@MybatisTest
@ActiveProfiles("test")
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
public class OrderTest {

    @Autowired
    private OrderMapper orderMapper;

    @Test
    public void mybatis_test() throws Exception {

        // given
        String seq = "1";

        // when
        OrderVO vo = orderMapper.getOrder(seq);

        // then
        assertThat(vo.getSeq).isEqualTo("1");

    }
}
```






