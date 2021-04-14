# 엔티티 매핑

jpa에서 중요한 두가지

- jpa의 내부 동작을 이해
- 실제 설계 - 객체와 관계형 데이터를 어떻게 매핑하는지

매핑해야하는 것

- 객체와 테이블
- 필드와 칼럼
- 기본 키
- 연관관계



## 객체와 테이블 매핑

- 기본 생성자 필수(Spec 상으로 그렇다.)

### 데이터베이스 스키마(DDL) 자동 생성(개발 시만 사용)

- 데이터베이스 방언에 맞게 생성(ex: H2Dialect)
- create: 삭제후 생성
- create-drop: create + 종료 시점에 drop
- update: 변경분만 반영
- validate: 엔티티-테이블이 정상 매핑이 되었는지만 확인 해줌
- 제약조건: 제약조건 DDL도 생성 설정이 가능 -> 하지만 jpa의 실행 로직에 영향을 주지 않음



## 필드와 칼럼 매핑

@Column

- name
- insertable, updatable: 등록, 변경 가능 여부
- nullable(DDL): not null 제약조건 생성
- unique(DDL): unique 제약조건(잘 사용하지 않는다 - 이름이 알아보기 힘듬, @Table에서 직접 설정 가능)
- columnDifinition(DDL): 칼럼 정보를 string으로 직접 준다.
- length(DDL)

@Enumerated

- enum
- Enumerated.ORDINAL 사용하지 않는다. 인덱스(순서)가 저장되기 때문에 중간에 enum 코드가 수정됐을 때 반영이 어려움
- Enumerated.STRING을 사용할 것

@Temporal

- 날짜
- 최근 하이버네이트는 LocalDate, LocalDateTime를 쓰면 생략 가능하다.

@Lob: BLOB(바이트), CLOB(문자)



## 기본 키 매핑

@GeneratedValue를 사용하지 않으면 직접 할당

@GeneratedValue를 사용하면 자동 할당

- AUTO 전략: DB 방언에 맞게 
- IDENTITY 전략: (MySQL 등에서 지원) 기본 키 생성은 DB에 위임
  - PK를 insert를 해야 알 수 있다. 그래서 commit 시점에 insert하는게 아니라 persist 시점에 바로 insert 한다.
- SEQUENCE 전략: (오라클) 시퀀스 오브젝트를 통해서 할당
  - persist 시점에 insert를 하는게 아니라 시퀸스로 PK 값만 받아옴(call next value)
  - allocationSize를 통해서 최적화 가능(시퀸스 한 번 호출에 미리 받아옴)
- TABLE 전략: 이 전략은 모든 DB에 적용 가능, 키 전용 테이블을 만들어서 키를 생성(DB의 시퀸스를 흉내냄), 느림
  - 시퀸스 전략과 같은 최적화 가능



## 실습

```
Hibernate: 
    
    drop table Item if exists
Hibernate: 
    
    drop table Member if exists
Hibernate: 
    
    drop table OrderItem if exists
Hibernate: 
    
    drop table ORDERS if exists
Hibernate: 
    
    drop sequence if exists hibernate_sequence
Hibernate: create sequence hibernate_sequence start with 1 increment by 1
Hibernate: 
    
    create table Item (
       ITEM_ID bigint not null,
        name varchar(255),
        price integer,
        stockQuantity integer,
        primary key (ITEM_ID)
    )
Hibernate: 
    
    create table Member (
       MEMBER_ID bigint not null,
        city varchar(255),
        name varchar(255),
        street varchar(255),
        zipcode varchar(255),
        primary key (MEMBER_ID)
    )
Hibernate: 
    
    create table OrderItem (
       ORDER_ITEM_ID bigint not null,
        count integer,
        ITEM_ID bigint,
        ORDER_ID bigint,
        orderPrice integer,
        primary key (ORDER_ITEM_ID)
    )
Hibernate: 
    
    create table ORDERS (
       ORDER_ID bigint not null,
        MEMBER_ID bigint,
        orderDate timestamp,
        status varchar(255),
        primary key (ORDER_ID)
    )
```











