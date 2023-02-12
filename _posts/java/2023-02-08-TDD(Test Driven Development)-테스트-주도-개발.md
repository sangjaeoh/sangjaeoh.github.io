---
layout: single
title: "JUnit 5, AssertJ Cheat Sheet"
categories: [Java]
tag: [Java, JUnit 5, AssertJ, Test, CheatSheet]
author_profile: true
toc: true
toc_sticky: true
image_directory: "junit5"
---

JUnit 5, AssertJ의 사용법을 간단하게 정리하였습니다. 😁  
- [jUnit 5 가이드 링크](https://junit.org/junit5/docs/current/user-guide/#overview-what-is-junit-5){:target="_blank"}
- [AssertJ 가이드 링크](https://assertj.github.io/doc/){:target="_blank"}
<br/>
<br/>
<br/>


# 🤔 JUnit 이란?
- Java 진영의 대표적인 Test Framework
- 단위 테스트(Unit Test)를 위한 도구를 제공
    - 단위 테스트란?
        - 코드의 특정 모듈이 의도된 대로 동작하는지 테스트하는 절차를 의미
        - 모든 함수와 메소드에 대해 각각의 테스트 케이스를 작성하는 것
- 어노테이션(Annotation)을 기반으로 테스트를 지원
- 단정문(Assert)으로 테스트 케이스의 기대값에 대해 수행 결과를 확인 할 수 있다
- Spring Boot 2.2 버전 부터 JUnit 5 버전을 사용
- JUnit은 크게 Jupiter, Platform, Vintage 모듈로 구성  
<br/>
<br/>
<br/>
<br/>


# 📖 JUnit 5 모듈 설명
**JUnit Jupiter**  
- TestEngine API 구현체로 JUnit 5를 구현하고 있다.
- 테스트의 실제 구현체는 별도 모듈 역할을 수행하는데, 그 모듈 중 하나가 Jupiter-Engine이다.
- 이 모듈은 Jupiter-API를 사용하여 작성한 테스트 코드를 발견하고 실행하는 역할을 수행한다.
- 개발자가 테스트 코드를 작성할 때 사용된다.

**JUnit Platform**  
- Test를 실행하기 위한 뼈대
- Test를 발견하고 테스트 계획을 생성하는 TestEngine 인터페이스를 가지고 있다.
- TestEngine을 통해 Test를 발견하고, 수행 및 결과를 보고한다. 그리고 각종 IDE 연동을 보조하는 역할을 수행한다.(콘솔 출력 등)
- Platform = TestEngine API + Console Launcher + JUnit 4 Based Runner 등

**JUnit Vintage**  
- TestEngine API 구현체로 JUnit 3, 4를 구현하고 있다.
- 기존 JUnit 3, 4버전으로 작성된 테스트 코드를 실행할 때 사용된다.
- Vintage-Engine 모듈을 포함하고 있다.

![jUnit Module](/assets/images/posts/java/junit5/3b581de9-e2a5-4f19-afe8-098b61a9e370.png)
<br/>
<br/>
<br/>
<br/>



# 📖 JUnit 5 어노테이션
[어노테이션 가이드 링크](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations){:target="_blank"}  

## 라이프사이클 어노테이션
JUnit 5는 아래와 같은 테스트 라이프 사이클을 가지고 있습니다.

| Annotation | Description |
| --- | --- |
| @Test | 테스트용 메소드를 표현하는 어노테이션 |
| @BeforeEach | 각 테스트 메소드가 시작되기 전에 실행되어야 하는 메소드를 표현 |
| @AfterEach | 각 테스트 메소드가 시작된 후 실행되어야 하는 메소드를 표현 |
| @BeforeAll | 테스트 시적 전에 실행되어야 하는 메소드를 표현 (static 처리 필요) |
| @AfterAll | 테스트 종료 후에 실행되어야 하는 메소드를 표현 (static 처리 필요) |

<br/>


## @ParameterizedTest
파라미터 테스트를 할 수 있게 도와주는 어노테이션으로, `@ValueSource`, `@CsvSource`,`@MethodSource` 등을 통해 매개변수를 지정할 수 있다.
```java
@ParameterizedTest
@ValueSource(ints = { 1, 5 })
void isPositive(int number) {
  assertTrue(number > 0);
}
```
```java
@ParameterizedTest
@CsvSource({
    "apple,         1",
    "banana,        2",
    "'lemon, lime', 0xF1",
    "strawberry,    700_000"
})
void testWithCsvSource(String fruit, int rank) {
    assertNotNull(fruit);
    assertNotEquals(0, rank);
}
```
```java
@ParameterizedTest
@MethodSource("stringProvider")
void testWithExplicitLocalMethodSource(String argument) {
    assertNotNull(argument);
}

static Stream<String> stringProvider() {
    return Stream.of("apple", "banana");
}
```
<br/>


## @RepeatedTest
반복 테스트를 위해 사용되는 어노테이션
```java
@RepeatedTest(5)
void repeatedTest() {
  System.out.println("테스트 반복!");
}
```
<br/>


## @TestMethodOrder
우선 순위에 따라 테스트를 실행할 수 있도록 도와주는 어노테이션. `@Order` 를 통해 우선순위를 지정할 수 있다.
```java
@TestMethodOrder(OrderAnnotation.class)
class OrderedTestsDemo {
  @Test
  @Order(2)
  void secondTest() {
    System.out.println("2");
  }

  @Test
  @Order(1)
  void firstTest() {
    System.out.println("1");
  }
}
```
<br/>


## @TestInstance
생명주기를 지정할 수 있는 어노테이션
```java
@TestInstance(Lifecycle.PER_CLASS)
class OrderedTestsDemo {...}
```
<br/>


## @DisplayName
테스트 이름을 지정할 수 있는 어노테이션
```java
@DisplayName("기본 산수를 검증하는 테스트")
@Test
void basic() {
  assertEquals(2, 1 + 1);
}
```
<br/>


## @Nested
중첩 클래스 테스트라는 것을 명시하는 어노테이션 `@BeforeAll` 또는 `@AfterAll` 를 사용하려면 생명 주기를 명시해야한다.
```java
public class NestedTests {

  @TestInstance(Lifecycle.PER_CLASS)
  @Nested
  class Positive {

    @BeforeAll
    void startTest() {
      System.out.println("테스트 시작");
    }

    @Test
    void isPositive() {
      assertTrue(1 > 0);
    }
  }

  @Nested
  class Negative {
    @Test
    void isNegative() {
      assertTrue(-1 < 0);
    }
  }
}
```
<br/>


## @Tag
태그를 선언하는 어노테이션. 추후에 구성 설정 파일을 통해 테스트에 포함 시킬 태그를 선택하거나, 제외시킬 태그를 선택할 수 있다. JUnit4의 `@Category`가 이 어노테이션으로 대체됨
```java
@Tag("number")
@Test
void basic() {
  assertEquals(2, 1 + 1);
}
```
```groovy
junitPlatform {
  filters {
      tags {
          include 'number', 'korean'
          exclude 'english'
      }
  }
}
```
<br/>


## @Disabled
테스트에서 제외시키는 어노테이션. JUnit4의 `@Ingore`와 같은 기능이다.
```java
@Disabled
@Test
void ignoredTest() {
  System.out.println("테스트 하지 않습니다!");
}
```
<br/>


## @Timeout
실행 시간 제한을 걸어두는 어노테이션. 시간이 초과되면 `TimeoutException`이 발생하며 테스트가 실패
```java
@Timeout(1)
@Test
void mustRunIn1SecondTest() throws InterruptedException {
	Thread.sleep(1500); // 실패
}

@Test
@Timeout(value = 500, unit = TimeUnit.MILLISECONDS)
void failsIfExecutionTimeExceeds500Milliseconds() {
    // fails if execution time exceeds 500 milliseconds
}
```
<br/>
<br/>
<br/>
<br/>






# 📖 JUnit Assertions
[Assertions 가이드 링크](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations){:target="_blank"}

## assertTrue && assertFalse
참 거짓 여부 판단. 2번째 인자가 존재하면 실패 시 2번째 인자를 메시지로 출력해준다.
```java
assertTrue('a' < 'b');
assertTrue('a' < 'b', () -> "message");

assertFalse('a' < 'b');
assertFalse('a' < 'b', () -> "message");
```
<br/>


## assertNull && assertNotNull
객체의 null 여부 판단
```java
assertNull(System.getProperty("my.prop"));
assertNotNull(System.getProperty("my.prop"));
```
<br/>


## assertEquals && assertNotEquals
두 값을 비교하여 일치 여부 판단. 3번째 인자가 존재하면 실패 시 3번째 인자를 메시지로 출력해준다.
```java
assertEquals(2, calculator.add(1, 1));
assertEquals(4, calculator.multiply(2, 2), "message");
```
<br/>


## assertArrayEquals
두 배열을 비교하여 일치 여부 판단. 두 배열이 모두 null이어도 동일한 것으로 간주한다.
```java
char[] expected = {'J','u','n','i','t'};
char[] actual = "Junit".toCharArray();
assertArrayEquals(expected, actual);
```
<br/>


## assertAll
여러 개의 `assertions`가 만족할 경우에만 테스를 통과하였다고 판단하고 싶을 경우에는 `assertAll()`을 사용한다.
```java
@Test
public void test() {
    assertAll(
      "heading",
      () -> assertEquals(4, 2 * 2, "4 is 2 times 2"),
      () -> assertEquals("java", "JAVA".toLowerCase()),
      () -> assertEquals(null, null, "null is equal to null")
    );
}

@Test
void dependentAssertions() {
    assertAll("properties",
        () -> {
            String firstName = person.getFirstName();
            assertNotNull(firstName);
            assertAll("first name",
                () -> assertTrue(firstName.startsWith("J")),
                () -> assertTrue(firstName.endsWith("e"))
            );
        },
        () -> {
            String lastName = person.getLastName();
            assertNotNull(lastName);
            assertAll("last name",
                () -> assertTrue(lastName.startsWith("D")),
                () -> assertTrue(lastName.endsWith("e"))
            );
        }
    );
}
```
<br/>


## assertSame && assertNotSame
예상되는 값과 실제 값이 동일한 객체를 참조하는지 확인
```java
String language = "Java";
Optional<String> optional = Optional.of(language);

assertSame(language, optional.get());
assertNotSame(language, optional.get());
```
<br/>


## assertThrows
특정 예외가 발생하였는지 확인. 첫 번째 인자는 확인할 예외 클래스, 두 번째 인자는 테스트하려는 코드
```java
@Test
void exceptionTesting() {
    Exception exception = assertThrows(ArithmeticException.class, () ->
        calculator.divide(1, 0));
    assertEquals("/ by zero", exception.getMessage());
}
```
<br/>


## assertTimeout & assertTimeoutPreemptively
특정 시간 안에 실행이 끝나는지 확인  
- `assertTimeout`: 시간 내 실행이 끝나는지 여부 확인 시
- `assertTimeoutPreemptively`: 지정한 시간 내 끝나지 않으면 바로 종료

```java
@Test
void timeoutNotExceeded() {
    // 시간안에 성공
    assertTimeout(ofMinutes(2), () -> {
        // 2분 안에 끝나는 테스트 작성...
    });
}

@Test
void timeoutNotExceededWithResult() {
    // 시간 안에 성공, 그리고 supplied object 리턴
    String actualResult = assertTimeout(ofMinutes(2), () -> {
        return "a result";
    });
    assertEquals("a result", actualResult);
}

@Test
void timeoutExceeded() {
    // 시간안에 테스트가 성공하지 못한다면 다음과 비슷한 오류 메시지 출력
    // execution exceeded timeout of 10 ms by 91 ms
    assertTimeout(ofMillis(10), () -> {
        Thread.sleep(100);
    });
}

@Test
void timeoutExceededWithPreemptiveTermination() {
    // 시간안에 테스트가 성공하지 못한다면 다음과 비슷한 오류 메시지 출력
    // execution timed out after 10 ms
    assertTimeoutPreemptively(ofMillis(10), () -> {
        // 10ms 이상 소요되는 작업...
        new CountDownLatch(1).await();
    });
}
```
<br/>
<br/>
<br/>
<br/>




# 📖 AssertJ Assertions
- [AssertJ 가이드 링크](https://assertj.github.io/doc/){:target="_blank"}
- [Spring AssertJ 가이드 링크](https://www.baeldung.com/introduction-to-assertj){:target="_blank"}

{% capture notice %}
**참고**  

`AssertJ`에서 모든 테스트 코드는 `assertThat()`으로 시작한다.
```java
assertThat(타겟).메소드1().메소드2();
```
{% endcapture %}
<div class="notice--info">{{ notice | markdownify }}</div>
<br/>



## Object Assertions
Objects는 두 객체의 동등성이나 객체의 필드를 검사하기 위해 다양한 방법으로 비교할 수 있습니다.
```java
public class Dog { 
    private String name; 
    private Float weight;
    
    // Getters, Setters...
}

Dog fido = new Dog("Fido", 5.25);
Dog fidosClone = new Dog("Fido", 5.25)
```

### isEqualTo()
참조 비교
```java
assertThat(fido).isEqualTo(fidosClone);
```

### isEqualToComparingFieldByFieldRecursively()
내용 비교
```java
assertThat(fido).isEqualToComparingFieldByFieldRecursively(fidosClone);
```
<br/>



## Boolean Assertions
참, 거짓 확인

### isTrue() && isFalse()
```java
assertThat("".isEmpty()).isTrue();
```
<br/>



## Iterable/Array Assertions
[Iterable/Array 가이드 링크](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractIterableAssert.html){:target="_blank"}  
`Iterable/Array`에 특정 요소가 존재하는지 다양한 방법으로 알 수 있다.


### contains()
요소 포함 확인
```java
List<String> list = Arrays.asList("1", "2", "3");
assertThat(list).contains("1");
```

### doesNotContain()
요소가 포함되지 않았는지 확인
```java
assertThat(list).doesNotContain("4");
```

### containsExactly()
실제 그룹에 지정된 값이 순서대로 정확히 포함되어 있는지 확인한다. 일관된 반복 순서가 있는 그룹에만 사용해야 한다.
```java
 Iterable<Ring> elvesRings = newArrayList(vilya, nenya, narya);

 // 성공
 assertThat(elvesRings).containsExactly(vilya, nenya, narya);

 // 실패
 assertThat(elvesRings).containsExactly(nenya, vilya, narya);
```

### containsAll()
실제 그룹에 지정된 `Iterable`의 모든 요소가 임의의 순서로 포함되어 있는지 확인한다.
```java
Iterable<String> abc = Arrays.asList("a", "b", "c");

 // 성공
 assertThat(abc).containsAll(Arrays.asList("b", "c"))
                .containsAll(Arrays.asList("a", "b", "c"));

 // 실패
 assertThat(abc).containsAll(Arrays.asList("d"));
 assertThat(abc).containsAll(Arrays.asList("a", "b", "c", "d"));
```

### isNotEmpty()
비어있지 않았는지 확인
```java
assertThat(list).isNotEmpty();
```

### startsWith()
주어진 값으로 시작하는지 확인
```java
assertThat(list).startsWith("1");
```
<br/>


## Character Assertions
[Character 가이드 링크](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractCharacterAssert.html){:target="_blank"}  
Character에 대한 비교
```java
assertThat(someCharacter)
  .isNotEqualTo('a')             // a 가 아니고
  .inUnicode()                   // 유니코드 테이블에 있고
  .isGreaterThanOrEqualTo('b')   // b보다 크고
  .isLowerCase();                // 소문자 인지 확인
```
<br/>



## Class Assertions
[Class 가이드 링크](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractClassAssert.html){:target="_blank"}  
클래스에 관한 비교

### isInterface()
인터페이스인지 확인
```java
assertThat(Runnable.class).isInterface();
```

### isAssignableFrom()
할당 가능한지 확인
```java
assertThat(Exception.class).isAssignableFrom(NoSuchElementException.class);
```
<br/>



## File Assertions
[File 가이드 링크](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractFileAssert.html){:target="_blank"}  
파일에 관련된 비교

### exists()
존재 여부
```java
assertThat(someFile).exists();
```

### isFile()
파일유형인지 확인
```java
assertThat(someFile).isFile();
```

### canRead()
읽을 수 있는지 확인
```java
assertThat(someFile).canRead();
```

### canWrite()
쓸 수 있는지 확인
```java
assertThat(someFile).canWrite();
```
<br/>



## Double/Float/Integer Assertions
[Double/Float/Integer 가이드 링크](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractDoubleAssert.html){:target="_blank"}  
숫자에 대한 비교

### isBetween()
값이 두 값 사이에 존재하는지 확인 (start, end 포함)
```java
assertThat(3).isBetween(1,4);
```

### isStrictlyBetween()
값이 두 값 사이에 존재하는지 확인 (start, end 미포함)
```java
assertThat(3).isStrictlyBetween(1,4);
```

### isPositive()
양수인지 확인
```java
assertThat(3).isPositive();
```

### isNegative()
음수인지 확인
```java
assertThat(-1).isNegative();
```

### isZero()
값이 0인지 확인
```java
assertThat(0).isZero();
```
<br/>



## Map Assertions
[Map 가이드 링크](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractMapAssert.html){:target="_blank"}  
맵에 특정 항목, entry, key/value 값이 포함되어 있는지 확인한다.

### containsKey()
키가 존재하지는 확인
```java
assertThat(map).containsKey(2);
```

### doesNotContainKeys()
키가 존재하지 않는지 확인
```java
assertThat(map).doesNotContainKeys(10);
```

### entry()
map의 키, 값을 확인
```java
assertThat(map).contains(entry(2, "a"));
```
<br/>



## Throwable Assertions
[Throwable 가이드 링크](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractThrowableAssert.html){:target="_blank"}  
예외 메시지, 스택 추적 검사, 예외 발생했는지 확인
```java
Throwable thrown = catchThrowable(() -> {
    throw new Exception("boom!");
});

assertThat(thrown).isInstanceOf(Exception.class).hasMessageContaining("boom");
```

### assertThatThrownBy()
```java
assertThatThrownBy(() -> {throw new Exception("boom!");})
.isInstanceOf(Exception.class)
.hasMessageContaining("boom");
```

### assertThatExceptionOfType()
특정 타입으로 검사
```java
assertThatExceptionOfType(IOException.class)
.isThrownBy(() -> { throw new IOException("boom!"); })
.withMessage("%s!", "boom")
.withMessageContaining("boom")
.withNoCause();
```
그 밖에 특정 타입을 검사하는 `exceptions`를 이용할 수 있다.
- assertThatNullPointerException
- assertThatIllegalArgumentException
- assertThatIllegalStateException
- assertThatIOException  
<br/>



## Describing Assertions

### as()
테스트의 설명 작성
```java
assertThat(person.getAge())
    .as("%s's age should be equal to 100", person.getName())
    .isEqualTo(100);

// 출력
// [Alex's age should be equal to 100] expected:<100> but was:<34>
```
<br/>


## JUnit Assumptions
특정 환경일 때만 테스트를 진행하도록 하고 싶을 때 사용

### assumeTrue() && assumeFalse()
테스트가 성공, 실패일 때 계속 진행
```java
@Test
void trueAssumption() {
    assumeTrue("PRD".equals(System.getenv("ENV")));
    // 계속 진행...
}
```
```java
@Test
void falseAssumption() {
    assumeFalse(5 < 1);
    // 계속 진행...
}
```

### assumingThat()
첫번째 인자 성공시, 두번째 인자 실행
```java
@Test
void assumptionThat() {
    assumingThat("CI".equals(System.getenv("ENV")),
        () -> {
            // CI서버에서만 이 테스트를 실행한다.
            assertEquals(2, calculator.divide(4, 2));
        });
    assertEquals(5 + 2, 7);
}
```
<br/>
<br/>
<br/>
<br/>




# 📖 JUnit import static
기본 import static 해놓으면 좋은 것들을 정리
```java
import static org.hamcrest.Matchers;
import static org.junit.jupiter.api.Assertions.*;
import static org.assertj.core.api.Assertions.*;
```
<br/>
<br/>
