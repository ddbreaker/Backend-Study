# 스프링 핵심 원리 이해2 - 객체 지향 원리 적용

## 관심사의 분리

```java
    private final MemberRepository memberRepository = new MemoryMemberRepository();
//    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
```

DIP를 위반하는 부분(인터페이스 뿐만 아니라 구현에도 의존)

클라이언트 코드도 변경했기 때문에 OCP도 위반

해결 방안 - `OrderServiceImpl`에 누가 구현 객체를 대신 생성해서 주입해줘야한다.

## 생성자 주입

```java
private final MemberRepository memberRepository;

public MemberServiceImpl(MemberRepository memberRepository) {
    this.memberRepository = memberRepository;
}
```

```java
private final MemberRepository memberRepository;
private final DiscountPolicy discountPolicy;

public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}
```

그리고 `AppConfig.java`를 만든다.

`AppConfig.java`는 객체를 생성하고 연결하는 역할만 한다.

```java
public class AppConfig {

    public MemberService memberService () {
        return new MemberServiceImpl(new MemoryMemberRepository());
    }

    public OrderService orderService() {
        return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
    }
}
```

이제 실제 사용할 때에는 아래와 같이 AppConfig를 통해서 객체를 생성한다.

```java
AppConfig appConfig = new AppConfig();
MemberService memberService = appConfig.memberService();
OrderService orderService = appConfig.orderService();
```

## AppConfig 리팩터링

- 역할이 드러나야한다.
- 중복된 부분을 제거하자.(`new MemoryMemberRepository()`)

```java
public class AppConfig {

    public MemberService memberService () {
        return new MemberServiceImpl(MemberRepository());
    }

    private MemberRepository MemberRepository() {
        return new MemoryMemberRepository();
    }

    public OrderService orderService() {
        return new OrderServiceImpl(MemberRepository(), discountPolicy());
    }

    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
    }
}
```

이제 역할과 구현 클래스가 한 눈에 보인다. 깔끔

---

### 제어의 역전(IoC, Inversion of Control)

프로그램을 작성하는 개발자는 어떤 구현 객체가 실행될지 예상할 수 없다. 제어 흐름에 대한 권한이 없다.

프레임워크 : 프레임워크가 작성한 코드를 제어하고 대신 실행

라이브러리 : 개발자가 작성한 코드가 직접 제어의 흐름을 담당

### 의존관계 주입 (DI, Dependency Injection)

의존관계는 다음 두가지로 분리해서 생각해야한다. (클래스 다이어그램과 객체 다이어그램)

- 정적인 클래스 의존 관계(실행하지 않아도 import만 보고 판단할 수 있는 것)
- 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계

의존관계 주입을 사용하면 `클래스 의존관계`를 변경하지 않고 `객체 의존관계`를 쉽게 변경할 수 있다.

### DI 컨테이너(또는 IoC 컨테이너)

AppConfig같이 객체를 생성하고 의존관계를 연결하는 컨테이너

(IoC는 더 범용적인 의미, JUnit도 IoC다)

---

## 스프링으로 전환

```java
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService () {
        return new MemberServiceImpl(MemberRepository());
    }

    @Bean
    public MemberRepository MemberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(MemberRepository(), discountPolicy());
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        return new RateDiscountPolicy();
    }
}
```

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
OrderService orderService = applicationContext.getBean("orderService", OrderService.class);
```

- `ApplicationContext`를 스프링 컨테이너라 한다.
- 이제는 객체를 생성하고 DI를 할 때 `AppConfig`를 사용하는게 아니라 스프링 컨테이너를 사용한다.

스프링 컨테이너는

- `@Configuration`이 붙은 `AppConfig`를 설정 정보로 사용한다.
- `@Bean`이 붙은 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다.
- 스프링 빈은 `@Bean`이 붙은 메서드의 이름을 스프링 빈의 이름으로 사용한다.
- 이제 getBean()으로 스프링 빈(객체)를 찾는다.

이제 어떤 장점이 있는지는 앞으로 볼 것이다.

---

기타

\* 단축키 Ctrl + Shift + T : 테스트 생성