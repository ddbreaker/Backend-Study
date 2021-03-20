# 의존관계 자동 주입

## 다양한 의존관계 주입 방법

- 생성자 주입
  - 여태까지 한 것
  - 생성자 호출 시점 1번 호출되는 것이 보장 -> 불변, 필수 의존관계
  - 스프링 빈은 생성자가 딱 하나만 있으면 `@Autowired`를 알아서 해준다.
- setter(수정자)주입
  - setter에 `@Autowired`를 하면 알아서 의존관계를 설정해준다. 주입할 대상이 없으면 오류가 발생하는데 `@Autowired(required = false)`로 하면 된다.
  - 선택, 변경 의존관계
- 필드 주입
  - `@Autowired private MemberService memberService`
  - 외부에서 변경이 불가능해서 테스트가 어렵다. 예를 들어서 순수 자바코드로 객체를 생성해서 테스트하려고 하면 불가능하다.
- 일반 메서드 주입
  - setter랑 별 차이 없다. 거의 사용하지 않는다.

\* 대부분 생성자 주입이 좋은 설계다. (불변, 테스트 용이, final 키워드 등)



## 옵션 처리

```java
@Test
void AutowiredOption() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(TestBean.class);
    
}

static class TestBean {

    @Autowired(required = false)
    public void setNoBean1(Member noBean1) {
        System.out.println("noBean1 = " + noBean1);
    }

    @Autowired
    public void setNoBean2(@Nullable Member noBean2) {
        System.out.println("noBean1 = " + noBean2);
    }

    @Autowired
    public void setNoBean3(Optional<Member> noBean3) {
        System.out.println("noBean1 = " + noBean3);
    }
}
```

1. `@Autowired(required = false)` : 수정자 메서드 자체가 호출 자체가 안됨
2. `@Nullable Member noBean2` : null이 입력됨
3. `Optional<Member> noBean3` : Optional.empty가 입력됨



## 조회 빈이 2개 이상인 경우

@Autowired 필드 명 : 아무것도 없으면 필드, 파라미터 명으로 보고 매칭한다.

```java
@Autowired
private DiscountPolicy rateDiscountPolicy
```

@Qualifier : 구분자를 붙인다. 가장 상세하며 실행시 우선순위가 제일 높다.

```java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}
```

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, @Qualifier("mainDiscountPolicy") DiscountPolicydiscountPolicy) {
	this.memberRepository = memberRepository;
	this.discountPolicy = discountPolicy;
}
```

@Primary : 우선순위를 정한다. (간단)

\* 주로 쓰는 것을 기본으로 `@Primary`를 설정하고 필요할 때 명시적으로 `@Qualifier`를 쓰는 것이 깔끔하다.



## 롬복

생성자가 하나면 `@Autowired`를 생략 가능 + 롬복 `@RequiredArgsConstructor`(final을 인자로 생성자)

코드가 깔끔해진다.



## 애노테이션 만들기

@Qualifier로 만들면 문자열이라 체크가 어렵다.

```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@Qualifier("mainDiscountPolicy")
public @interface MainDiscountPolicy {
}
```

이렇게 만들고 Qualifier 대신에 직접 만든 애노테이션을 사용한다.

Qualifier을 쓰는 것보다 에러를 체크하는 면에서 이 방법이 더 나은 것 같다.

위와 같이 애노테이션을 조합해서 사용하는 기능은 스프링이 지원해주는 기능이다.



## 조회한 빈이 모두 필요할 때

```java
static class DiscountService {
    private final Map<String, DiscountPolicy> policyMap;
    private final List<DiscountPolicy> policies;

    @Autowired
    public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
        this.policyMap = policyMap;
        this.policies = policies;
        System.out.println("policyMap = " + policyMap);
        System.out.println("policies = " + policies);
    }

    public int discount(Member member, int price, String discountCode) {
        DiscountPolicy discountPolicy = policyMap.get(discountCode);
        return discountPolicy.discount(member, price);
    }
}
```

Map, List로 필드를 생성하고 컴포넌트 스캔을 하면 여러 빈을 다 넣어준다.



