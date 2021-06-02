# 변경 감지와 병합(merge)

임의로 만들어낸 엔티티도 기존 식별자를 가지고 있으면 준영속 엔티티로 볼 수 있다.

```java
Book book = new Book();
book.setId(form.getId());
book.setName(form.getName());
book.setPrice(form.getPrice());
book.setStockQuantity(form.getStockQuantity());
book.setAuthor(form.getAuthor());
book.setIsbn(form.getIsbn());
```

### 준영속 엔티티를 수정하는 2가지 방법

- 변경 감지 기능 사용: 준영속 상태의 엔티티를 em.find로 찾는다.
- **병합(merge) 사용**
  - em.merge()를 호출하면 위와 같이 em.find로 실행해준다.
  - 차이점: 기존의 merge의 파라미터는 영속성 컨텍스트에서 관리되지 않고 새로운 영속성 컨텍스트에서 관리되는 엔티티를 return한다.
  - 주의: 병합을 사용하면 모든 속성이 변경된다. -> 병합 값이 없으면 null로 업데이트할 수도 있다.
  - -> 병합할 객체에 null이 없도록 잘 채워야하지만 위험하다.
  - -> _merge를 쓰지 말자._



