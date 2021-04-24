# 값 타입

JPA의 데이터 타입 분류

- 엔티티 타입
  - @Entity로 정의하는 객체
  - 데이터가 변해도 식별자로 추적 가능(회원의 키나 나이를 변경해도)
- 값 타입
  - int, String, Long처럼 단순한 값
  - 기본값 타입: 자바에서 제공해주는 타입(자바 기본타입-int/double, 래퍼 클래스-Integer/Long, String)
  - **임베디드 타입**
  - **값 타입 컬렉션**



## 임베디드 타입

새로운 값 타입을 직접 정의

주로 기본 값 타입을 모아서 만듬

```java
// Period.java
@Embeddable
public class Period {
    private LocalDateTime startDate;
    private LocalDateTime endDate;
}
```

```java
public class Member {
    @Embedded
	private Period workPeriod;	
    @Embedded
	private Address homeAddress;
}
```

DB의 매핑하는 테이블은 그대로

객체와 테이블을 세밀하게 매핑이 가능하다(테이블 수 < 객체 수)

**임베디드 타입 같은 값 타입을 여러 엔티티에서 공유하면 굉장히 위험함**

- 임베디드 타입은 자바의 기본 타입이 아니라 **객체 타입**이다.
- 객체 타입은 참조 값을 직접 대입하는 것을 막을 수 없다. = 객체의 공유 참조는 피할 수 없다.
- -> 값을 복사해서 사용해야하는데 실수하면 에러를 찾기 힘들다

### 불변 객체

수정할 수 없는 객체

값 타입은 불변 객체(immutable object)로 설계해야하고 따라서 임베디드 타입도 불변 객체로 설계해야함

불변 객체를 만드는 방법 중 하나는 생성자로만 값을 설정하고 수정자를 만들지 않으면 된다.



## 값 타입 비교

동일성(identity) 비교: 인스턴스의 참조 값을 비교, ==을 사용

동등성(equivalence) 비교: 인스턴스의 값을 비교, equals()를 사용

값타입은 동일성이 아니라 동등성을 비교해야함

- equals()를 재정의 해야한다.
- 프록시를 고려해서 Use getter during generation에 체크할 것



## 값 타입 컬렉션

Set\<String>, List\<Address>...처럼 값 타입을 하나 이상 저장

별도의 테이블이 필요하다.

```java
@ElementCollection
@CollectionTable(name = "", joinColumns = 
                @JoinColumn(name = "")
)
```

생명주기가 Entity에 의존 - Entity가 수정되면 자동으로 수정됨

지연 로딩을 사용함

### 값 타입 컬렉션의 제약사항

값 타입은 식별자가 없다 + 값은 변경하면 추적이 어렵다

```
값 타입 컬렉션에 변경 사항이 발생하면, 주인 엔티티와 연관된 모든 데이터를 삭제하고, 값 타입 컬렉션에 있는 현재 값을 모두 다시 저장한다.
```

=> 쓰지 않는게 좋다. 차라리 일대다 관계를 고려한다.

=> 아님 업데이트 할 필요 없고 갯수가 적을 때만 쓴다.

![image](https://user-images.githubusercontent.com/52124204/115969521-452a7100-a578-11eb-8730-0810e96b6aa2.png)

![image](https://user-images.githubusercontent.com/52124204/115969529-4eb3d900-a578-11eb-8c12-d46d714e030a.png)