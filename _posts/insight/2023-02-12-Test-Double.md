---
layout: single
title: "Test Double"
categories: [Insight]
tag: [Insight, Test, TestDobule]
author_profile: true
toc: true
toc_sticky: true
---

테스트 더블(Test Dobule)에 대해 설명합니다.

<br/>

# 🤔 테스트 더블이란?
- Test Dobule이라 한다.
- 테스트를 진행하기 어려운 경우 이를 **대신해 테스트를 진행할 수 있도록 만들어주는 객체를 말한다.**
- 제라드 메스자로스(Gerard Meszaros)가 만든 용어로,  스턴트 더블(영화 촬영에서 말하는 스턴트 대역 배우)에서 아이디어를 얻어서 만든 용어이다.  

{% capture notice %}
예를 들어 우리가 데이터베이스로부터 조회한 값을 연산하는 로직을 구현했다고 하자. 해당 로직을 테스트하기 위해선 항상 데이터베이스의 영향을 받을 것이고, 이는 데이터베이스의 상태에 따라 다른 결과를 유발할 수도 있다.

이때 **테스트하려는 객체와 연관된 객체를 사용하기가 어렵고 모호할 때 대신해 줄 수 있는 객체를 테스트 더블이라 한다.**
{% endcapture %}
<div class="notice--info">{{ notice | markdownify }}</div>
<br/>



# 📖 테스트 더블 종류
![TestDouble](/assets/images/posts/insight/20230212/e7604428-37a2-4e06-9dd4-35e3dc4e7d77.png)
테스트 더블은 크게 `Dummy`, `Fake`, `Stub`, `Spy`, `Mock`으로 나눈다.




## 🎯 Dummy
- 가장 기본적인 테스트 더블이다.
- 인스턴스화 된 객체가 필요하지만 기능은 필요하지 않은 경우에 사용한다.
- `Dummy` 객체의 메서드가 호출되었을 때 정상 동작은 보장하지 않는다.
- 객체는 전달되지만 사용되지 않는 객체이다.  
<br/>

**Dummy 예**
```java
public interface PringWarning {
    void print();
}
```
```java
public class PrintWarningDummy implements PrintWarning {
    @Override
    public void print() {
        // 아무런 동작을 하지 않는다.
    }
}
```
실제 객체는 PrintWarning 인터페이스의 구현체를 필요하지만, 특정 테스트에서는 해당 구현체의 동작이 전혀 필요하지 않을 수 있다. 실제 객체가 로그용 경고만 출력한다면 테스트 환경에서는 전혀 필요 없기 때문이다.

이처럼 동작하지 않아도 테스트에는 영향을 미치지 않는 객체를 `Dummy` 객체라고 한다.
<br/>
<br/>



## 🎯 Fake
- 복잡한 로직이나 객체 내부에서 필요로 하는 다른 외부 객체들의 동작을 단순화하여 구현한 객체이다.
- 동작의 구현을 가지고 있지만 실제 프로덕션에는 적합하지 않은 객체이다.
- 즉, **동작은 하지만 실제 사용되는 객체처럼 정교하게 동작하지는 않는 객체를 말한다.**  
<br/>

**Fake 예**
```java
public interface UserRepository {
    void save(User user);
    User findById(long id);
}
```
```java
public class FakeUserRepository implements UserRepository {
    private Collection<User> users = new ArrayList<>();
    
    @Override
    public void save(User user) {
        if (findById(user.getId()) == null) {
            user.add(user);
        }
    }
    
    @Override
    public User findById(long id) {
        for (User user : users) {
            if (user.getId() == id) {
                return user;
            }
        }
        return null;
    }
}
```
실제 데이터베이스를 연결해서 테스트해야 하지만, 실제 데이터베이스 대신 가짜 데이터베이스 역할을 하는 FakeUserRepository를 만들어 테스트 객체에 주입한다. 이렇게 하면 테스트 객체는 데이터베이스에 의존하지 않으면서도 동일하게 동작을 하는 가짜 데이터베이스를 가지게 된다.

이처럼 실제 객체와 동일한 역할을 하도록 만들어 사용하는 객체가 `Fake`이다.  
<br/>
<br/>



## 🎯 Stub
- `Dummy` 객체가 실제로 동작하는 것 처럼 보이게 만들어 놓은 객체이다.
- 테스트에서 호출된 요청에 대해 미리 준비해둔 결과를 제공한다.
- **상태를 검증한다.**
- 즉, **테스트를 위해 프로그래밍된 내용에 대해서만 준비된 결과를 제공하는 객체이다.**

**Stub 예**
```java
public class GradeServiceTest {

    private Student student;
    private Gradebook gradebook;

    @Before
    public void setUp() throws Exception {
        gradebook = mock(Gradebook.class);
        student = new Student();
    }

    @Test
    public void calculates_grades_average_for_student() {
        when(gradebook.gradesFor(student)).thenReturn(grades(8, 6, 10)); //stubbing gradebook

        double averageGrades = new GradesService(gradebook).averageGrades(student);

        assertThat(averageGrades).isEqualTo(8.0);
    }
}
```
우리가 테스트에서 자주 사용하는 `Mockito` 프레임워크도 `Stub`와 같은 역할을 해준다.

이처럼 테스트를 위해 의도한 결과만 반환되도록 하기 위한 객체가 `Stub`이다.  
<br/>
<br/>



## 🎯 Spy
- Stub의 역할을 가지면서 호출된 내용에 대해 약간의 정보를 기록한다.
- 실제 객체처럼 동작시킬 수도 있고, 필요한 부분에 대해서는 Stub로 만들어서 동작을 지정할 수도 있다.
- 즉, **실제 기능을 사용하면서 선택적으로 Stub하는 객체이다.**

**Spy 예**
```java
@ExtendWith(MockitoExtension.class)
public class SpyTests {

    @Spy
    private OrderRepository orderRepository;
    @Spy
    private NotificationClient notificationClient;
    @InjectMocks
    private OrderService orderService;

    @Test
    public void createOrderTest_basic() {
        // given
        // Spy 객체의 orderRepository의 createOrder()만 Stub하고 나머지 기능은 그대로 사용
        Mockito.doAnswer(invocation -> {
            System.out.println("I'm spy orderRepository createOrder");
            return null;
        }).when(orderRepository).createOrder();
        Mockito.doAnswer(invocation -> {
            System.out.println("I'm spy notificationclient");
            return null;
        }).when(notificationClient).notifyToMobile();


        // when
        orderService.createOrder(true);

        // then
        Mockito.verify(orderRepository, Mockito.times(1)).createOrder();
        Mockito.verify(notificationClient, Mockito.times(1)).notifyToMobile();
    }
}
```
<br/>
<br/>




## 🎯 Mock
- Mock은 행위를 검증하기 위해 가짜 객체를 만들고 테스트하는 방법이다.
- 테스트가 정상적으로 호출되었는지, **행위를 검증한다.**  
<br/>


**Mock 예**
```java
public class SecurityCentral {
    private final Window window;
    private final Door door;

    public SecurityCentral(Window window, Door door) {
        this.window = window;
        this.door = door;
    }

    void securityOn() {
        window.close();
        door.close();
    }
}
```
```java
public class SecurityCentralTest {
    Window windowMock = mock(Window.class);
    Door doorMock = mock(Door.class);

    @Test
    public void enabling_security_locks_windows_ans_doors() {
        SecurityCentral securityCentral = new SecurityCentral(windowMock, doorMock);

        securityCentral.securityOn();

        // 행위 검증
        verify(windowMock).close();
        verify(doorMock).close();
    }
}
```
<br/>
<br/>
<br/>





# 🙇🏻‍♂️ 참고사이트
- [https://tecoble.techcourse.co.kr](https://tecoble.techcourse.co.kr/post/2020-09-19-what-is-test-double/){:target="_blank"}
- [https://cobbybb.tistory.com/16](https://cobbybb.tistory.com/16){:target="_blank"}

