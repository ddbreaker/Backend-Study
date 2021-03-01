# 스프링 핵심 원리 이해1 - 예제 만들기

- 프로젝트 생성(start.spring.io에서 아무런 dependency 없이)
- 요구사항 설명



## 회원 도메인

회원 도메인 설계

- 도메인 협력 관계 - 클래스 다이어그램(정적인 것) - 객체 다이어그램(동적인 것)
- 클래스 다이어그램에는 여러 구현체 후보들도 다이어그램에 그리지만 객체 다이어그램은 그 중에서 결정된 것을 그린다.

---

*코드 생략*

**문제점**

```java
private final MemberRepository memberRepository = new MemoryMemberRepository();
```

인터페이스(MemberRepository) 뿐만 아니라 구현(MemoryMemberRepository)에도 의존한다.

OCP, DIP를 위반하는 것



## 주문과 할인 도메인

주문과 할인 도메인 설계

![image](https://user-images.githubusercontent.com/52124204/109557911-11087600-7b1c-11eb-8d0c-4da88cc620ea.png)

*코드 생략*

