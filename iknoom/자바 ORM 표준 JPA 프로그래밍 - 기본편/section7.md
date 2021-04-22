# 고급 매핑



## 상속관계 매핑

3가지 방법

@Inheritance(stategy=InheritanceType.XXX)

- JOINED: 각각을 모두 테이블로 만든다. select마다 조인한다.
- SINGLE_TABLE: 하나의 테이블에 만든다. 없는 속성은 null로 채운다.
- TABLE_PER_CLASS: 구현 클래스마다 테이블 만든다. -> 쓰지 말자

## @MappedSuperclass

공통 매핑 정보가 필요할 때 -> @MappedSuperclass + BaseEntity를 만들고 상속받는다.

엔티티가 아니고 그러므로 상속관계 매핑을 한 것도 아니다.

어차피 안쓰니까 추상클래스로 만들자