# 영속성 관리 - 내부 동작 방식

## 엔티티의 생명주기

![image](https://user-images.githubusercontent.com/52124204/113610834-9d143d00-9688-11eb-9a5b-c5c895e1fa62.png)

- 비영속(new/transient) : 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
- 영속(managed) : 영속성 컨텍스트에 관리되는 상태
- 준영속(detached) : 영속성 컨텍스트에 저장되었다가 분리된 상태
- 삭제(removed) : 삭제된 상태
- 플러시(em.flush) : 영속성 컨텍스트의 변경내용 반영(쓰기 지연 SQL을 반영)
  - 트랜잭션 커밋, JPQL 쿼리 시 자동 호출
  - 영속성 컨텍스트를 비우지 않음 - 영속성 컨텍스트의 변경내용을 DB에 동기화 하는 것
- 준영속 상태
  - 영속 상태의 엔티티가 영속성 컨텍스트에서 분리
  - em.detach(entity), em.clear(), em.close()



## 영속성 컨텍스트의 이점

- 엔티티 조회시 1차 캐시 : 캐시메모리에서 PK로 먼저 조회
- 영속 엔티티의 동일성(identity) 보장 : 1차 캐시로 반복 가능한 읽기면 애플리케이션 차원에서 트랜잭션을 격리
- 엔티티 등록시 트랜잭션을 지원하는 쓰기 지연(transactional write-behind) : 커밋에서 한번에 INSERT
- 변경 감지(Dirty Checking)
- 지연 로딩(Lazy Loading)



### 궁금한 것

DB의 트랜잭션과 커밋







