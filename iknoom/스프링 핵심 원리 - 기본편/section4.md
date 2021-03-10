# 스프링 컨테이너와 스프링 빈



## 스프링 컨테이너 생성

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

- `ApplicationContext`는 스프링 컨테이너의 인터페이스이다.
- XML 기반으로 만들어가 애노테이션 기반으로 만들 수 있다.
- 스프링 컨테이너에는 `빈 이름 : 빈 객체`가 등록된다.
  - 빈 이름은 메서드 이름이다. (`@Bean(name='')`으로 등록 가능)
- 등록 후 스프링 빈 사이의 의존관계를 주입(DI)한다. (자세한 단계는 추후에)



## 빈 조회

```java
// 컨테이너에 등록된 전체 빈 조회
AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

@Test
void findApplicationBean() {
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();
    for (String beanDefinitionName : beanDefinitionNames) {
        BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
        if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
            Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name = " + beanDefinitionName + " object = " + bean);
        }
    }
}
```

```
name = appConfig object = hello.core.AppConfig$$EnhancerBySpringCGLIB$$232078bf@71cf1b07
name = memberService object = hello.core.member.MemberServiceImpl@615091b8
name = MemberRepository object = hello.core.member.MemoryMemberRepository@4fce136b
name = orderService object = hello.core.order.OrderServiceImpl@4aa83f4f
name = discountPolicy object = hello.core.discount.RateDiscountPolicy@43b6123e
```

`ROLE_APPLICATION` : 사용자가 정의한 빈

`ROLE_INFRASTRUCTURE` : 스프링이 내부에서 사용하는 빈

```java
// 이름으로 조회
MemberService memberService = ac.getBean("memberService", MemberService.class);
// 타입으로 조회(인스턴스)
MemberService memberService = ac.getBean(MemberService.class);
// 특정 타입을 모두 조회
Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
// 구체 타입으로 조회(구현 클래스)
MemberService memberService = ac.getBean(MemberServiceImpl.class);
```

- 부모 타입으로 조회하면 자식타입도 함께 조회된다. Object로 조회하면 모두 조회된다.
- 타입으로 조회시 자식이 둘 이상이거나 같은 타입이 둘 이상이면 에러가 난다. 이 경우 빈 이름을 지정해야한다.



## BeanFactory

```
// 상속관계
BeanFactory<<interface>> <- ApplicationContext<<interface>> <- AnnotationConfigApplicationContext
```

- `BeanFactory`

  - 스프링 컨테이너의 최상위 인터페이스
  - 스프링 빈을 관리/조회

- `ApplicationContext`

  - `BeanFactory` 뿐만이 아니라 애플리게이션을 개발할 때 필요한 부가기능을 함께 상속한다.
  - 메시지소스를 활용한 국제화 기능 / 환경변수 / 애플리케이션 이벤트 / 편리한 리소스 조회

  

## 다양한 설정 형식 방법

### 애노테이션 기반 자바 코드

여태까지 한 것

### XML

```xml
<bean id="memberService" class="hello.core.member.MemberServiceImpl" >
    <constructor-arg name="memberRepository" ref="memberRepository" />
</bean>

<bean id="memberRepository" class="hello.core.member.MemoryMemberRepository" />

<bean id="orderService" class="hello.core.order.OrderServiceImpl">
    <constructor-arg name="memberRepository" ref="memberRepository"/>
    <constructor-arg name="discountPolicy" ref="discountPolicy"/>
</bean>

<bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy" />
```

```java
ApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");
```



## BeanDefinition

빈 설정 메타 정보를 `BeanDefinition`으로 추상화를 했기 때문에 다양한 설정 형식을 지원해주는 것

스프링 컨테이너는 정확히는 `BeanDefinition`을 기반으로 스프링 빈을 생성하는 것

자세한 방법은 생략

