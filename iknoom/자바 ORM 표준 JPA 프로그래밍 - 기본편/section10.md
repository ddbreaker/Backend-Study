# 객체지향 쿼리 언어 JPQL - 기본 문법

## JPQL

JPA는 SQL을 추상화한 JPQL(객체 지향 쿼리 언어)를 제공

SQL과 거의 유사하다

- JPQL은 대상이 엔티티 객체
- SQL은 대상이 테이블

단점: 동적 쿼리가 어렵다.

-> QueryDSL 사용

### QueryDSL

- 문자가 아닌 자바 코드로 JPQL을 작성
- JPQL 빌더 역할
- 컴파일 시점에 오류를 찾을 수 있다.
- 동적쿼리 작성이 쉽다.
- JPQL만 잘 공부하면 문서를 보고 쉽게 쓸 수 있다

### Native SQL

네이티브 SQL 쿼리도 지원한다.

### \* JDBC 직접 사용

JDBC 커넥션을 직접 사용할 수 있다.

단, 영속성 컨텍스트를 강제로 플러시 해야한다.(em.flush())



## 기본 문법

```
select_문 :: =
    select_절
    from_절
    [where_절]
    [groupby_절]
    [having_절]
    [orderby_절]
update_문 :: = update_절 [where_절]
delete_문 :: = delete_절 [where_절]
```

- 엔티티, 속성 : 대소문자 구분O (Member, age)
- JPQL 키워드 : 대소문자 구분X (SELECT, FROM, where)
- 별칭은 필수(m) (as는 생략가능)
- TypeQuery : 반환 타입이 명확할 때
- Query : 반환 타입이 명확하지 않을 때
- query.getResultList()
  - 결과가 하나 이상일 때 리스트 반환
  - 결과가 없으면 빈 리스트
- query.getSingleResult()
  - 결과가 없을 때 Exception
  - 결과가 2개 이상일 때 Exception

## 프로젝션

SELECT 절에 조회할 대상을 지정

- SELECT m FROM Member m -> 엔티티 프로젝션
- SELECT m.team FROM Member m -> 엔티티 프로젝션
  - 이 경우 join으로 명시적으로 적는 편이 파악하기 좋다.
- SELECT m.address FROM Member m -> 임베디드 타입 프로젝션
  - Address만으로는 조회하지 못한다.
- SELECT m.username, m.age FROM Member m -> 스칼라 타입 프로젝션
  - 여러값을 조회하는 방법
    - Query 타입으로 조회
    - Object[] 타입으로 조회
    - new 명령어로 조회 -> DTO 생성

## 페이징

- setFirstResult(int startPosition) : 조회 시작 위치 (0부터 시작)
- setMaxResults(int maxResult) : 조회할 데이터 수

DB 방언에 맞게 쿼리를 아주 잘 생성해준다.

## 조인

- 내부 조인: SELECT m FROM Member m [INNER] JOIN m.team t
- 외부 조인: SELECT m FROM Member m LEFT [OUTER] JOIN m.team t
- ON
  - 조인 대상 필터링
  - 연관관계 없는 엔티티 외부 조인

## 서브쿼리

SQL과 비슷하게 지원해준다.

JPA 서브쿼리의 한계

- FROM 절의 서브 쿼리는 현재 JPQL에서 불가능
  - 조인으로 풀 수 있으면 풀어서 해결한다.
  - 정 안되면 Native SQL

## 조건식

- CASE 식
- COALESCE: 하나씩 조회해서 null이 아니면 반환
- NULLIF: 두 값이 같으면 null 반환, 다르면 첫번째 값 반환

