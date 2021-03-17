# 컴포넌트 스캔

## 컴포넌트 스캔

등록해야할 `@Bean`이 너무 많으면 설정 정보가 너무 커진다.

컴포넌트 스캔 : 자동으로 스프링 빈을 등록

```java
@Configuration
@ComponentScan(
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class) // 원래는 이렇게 안하지만 예제 코드를 유지하기 위함임
)
public class AutoAppConfig {
    
}
```

이제 `@Component`를 붙이면 자동으로 스프링 빈으로 등록된다.

그리고 `@Autowired`를 붙이면 자동으로 의존관계 주입이 된다.

```java
@Component
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
    ...
```

`@ComponentScan`이 `@Component`를 스프링 빈으로 등록할 때

빈 이름은 클래스명을 사용하되 맨 앞글자를 소문자를 사용한다. (`@Component("")`로 직접 지정할 수 있다.)

```
20:05:41.876 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'autoAppConfig'
20:05:41.880 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'rateDiscountPolicy'
20:05:41.880 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memberServiceImpl'
20:05:41.893 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memoryMemberRepository'
```



## 탐색 위치와 기본 스캔 대상

탐색 시작 위치를 직접 지정할 수 있다.

```java
@ComponentScan(
	basePackages = "hello.core.member"
    // basePackages = {"hello.core.member", ...}
    // basePackageClasses = AutoAppConfig.class (그 클래스가 있는 패키지부터)
)
```

default는 `@ComponentScan`을 붙인 패키지부터 스캔한다.

기본으로 AppConfig를 최상단에 두고 basePackages를 쓰지 않는 것을 권장한다. (프로젝트의 대표 정보이기 때문에)

참고 : 스프링 부트를 사용하면 스프링 부트의 대표 시작 정보인 `@SpringBootApplication`을 프로젝트 시작 위치에 두는 것이 관례(이 설정 안에 `@ComponentScan`이 들어있다.)



### 필터

```java
@ComponentScan(
    // excludeFilters = ...
    // includeFilters = ...
)
```

```java
@Configuration
@ComponentScan(
    includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
    excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
)
static class ComponentFilterAppConfig {
}
```

생략해도 된다.

```java
@Configuration
@ComponentScan(
    includeFilters = @Filter(classes = MyIncludeComponent.class),
    excludeFilters = @Filter(classes = MyExcludeComponent.class)
)
static class ComponentFilterAppConfig {
}
```

FilterType은 여러가지 옵션이 있다.(잘 사용하지 않음, 애초에 includeFilters, excludeFilters를 잘 사용하지 않는다.)



## 중복 등록과 충돌

**case1 : 자동 빈 등록 vs 자동 빈 등록**

예외가 발생한다. (`ConflictingBeanDefinitionException`)



**case2: 수동 빈 등록 vs 자동 빈 등록**

수동 빈 등록이 우선권을 가진다. (Overriding)

```
Overriding bean definition for bean 'memoryMemberRepository' with a different definition: replacing ...
```



하지만 case2도 현업에서 문제가 발생할 가능성이 너무 높기 때문에 최근의 스프링 부트는 에러가 발생한다.(`@SpringBootApplication`)

```
Action:
Consider renaming one of the beans or enabling overriding by setting spring.main.allow-bean-definition-overriding=true
```

application.properties를 통해서 수정 가능하다.