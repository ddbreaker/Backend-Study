# 프록시와 연관관계 관리

## 프록시

가짜 Entity

Member member = em.getReference(...)를 하고나서

1. member.getName()
2. 프록시가 영속성 컨텍스트에 초기화를 요청
3. 영속성 컨텍스트에서 DB를 조회 -> 실제 Entity 생성
4. target.getName()을 호출하여 진짜 Entity를 호출

주의할 것들

1. 프록시가 실제 Entity로 바뀌는 게 아님
   1. 원본 Entity를 상속받는다. -> 타입 체크시에 유의해야한다.(==이 아니라 instance of 사용)
2. 영속성 컨텍스트에 찾으려는 Entity가 이미 있으면 프록시가 아니라 실제 Entity 반환
   1. 반대로 이미 프록시가 있을 때 실제 Entity를 조회하면 프록시를 반환한다.
   2. JPA는 같은 트랜젝션 내에서 ==을 보장하려 한다?
3. 준영속 상태일 때, 프록시를 초기화 하면 에러가 발생

## 지연로딩

```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "TEAM_ID")
private Team team;
```

이렇게 하면 Team이 지연로딩 된다.

실제로 Team이 쓰일 때 조회 쿼리를 실행한다.

### 즉시 로딩

```java
@ManyToOne(fetch = FetchType.EAGER)
...
```



**가급적 지연 로딩만 사용하자**

- 즉시 로딩을 하면 예상 못한 SQL이 발생한다.
- JPQL에서 심각하게 쿼리가 많이 발생할 수 있다.(N + 1 문제, 처음 selcet 뿐만이 아니라 N개의 select 추가)
- ManyToOne, OneToOne은 즉시 로딩이 기본값 -> 바꿔주기
- OneToMany, ManyToMany는 지연 로딩이 기본값



## 영속성 전이: cascade

```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.ALL)
private List<Child> childList = new ArrayList<>();
```

영속화를 할 때 연관된 Entity인 Child도 같이 영속화를 한다.

라이프 사이클이 비슷하고 child의 소유자가 유일할 때(참조하는 곳이 하나일 때) 쓰면 유용하다.



## 고아 객체: orphanRemoval

```java
@OneToMany(mappedBy = "parent", orphanRemoval = true)
private List<Child> childList = new ArrayList<>();
```

자동으로 삭제 쿼리가 발생한다.

참조하는 곳이 하나일 때 사용할 것

