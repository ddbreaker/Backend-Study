# HTTP 헤더2 - 캐시와 조건부 요청
## 캐시 기본 동작
- 응답에서 `cachecontrol: max-age=60` 헤더를 설정
	- 헤더에서 설정한 시간만큼 캐시에 데이터를 저장
	- 헤더에서 설정한 시간이 초과하면 그 때 서버에서 데이터를 받음

## 검증 헤더, 조건부 요청
- 캐시에 있는 데이터를 갱신할지 결정
- 첫번째 요청
	- 응답에 다음 헤더를 넣음
		- `Last-Modified: 2020년 11월 10일 10:00:00`
		- 이를 검증 헤더라고 한다.
- 두번째 요청
	- 요청에 다음 헤더를 넣음
		- `if-modified-since: 2020년 11월 10일 10:00:00`
		- 이를 조건부 요청이라 한다.
- 이 때, 서버에서 요청을 확인 후 최종 수정일이 같은 경우
	- 데이터가 수정되지 않았다.
	- 서버의 응답으로 `304 Not Modified` status을 보내고 HTTP Body를 생략한다.
	- 클라이언트는 응답을 받으면 캐시의 메타 정보를 갱신한다.

Last-Modified, If-Modified-Since 단점
- 1초 미만 단위로 캐시 조정이 불가능
- 날짜 기반의 로직 사용
- 데이터를 수정해서 날짜가 다르지만, 같은 데이터를 수정해서 데이터 결과가 똑같은 경우
- 서버에서 별도의 캐시 로직을 관리하고 싶은 경우
	- 예를 들어 스페이스나 주석처럼 크게 영향이 없는 변경에서 캐시 유지
### ETag, If-None-Match
- ETag(Entity Tag)
	- 캐시용 데이터에 임의의 고유한 버전 이름을 달 수 있다.
	- 데이터가 변경되면 이 이름을 바꾼다.
- 캐시 제어 로직을 서버에서 완전히 관리할 수 있다.

## 캐시 관련 헤더
- `Cache-Control: max-age`: 캐시 유효 시간, 초단위
- `Cache-Control: no-cache`: 데이터는 캐시해도 되지만, 항상 origin 서버에 검증
- `Cache-Control: no-store`: 저장하면 안됨(최대한 빨리 삭제)
- `Pragma`: 하위 버전에서 사용
- `Expires`: 하위 버전에서 사용

## 프록시 캐시
- 클라이언트와 Origin 서버 사이에 프록시 캐시 서버를 둔다.
	- 프록시 캐시 서버에서의 캐시를 public 캐시
	- 클라이언트에서의 캐시를 private 캐시라고 한다.
- `Cache-Control: public`
	- 응답이 public 캐시에 저장되어도 된다.
- `Cache-Control: private`
	- 응답이 해당 사용자만을 위한 것임, private 캐시에 저장해야 함(기본값)
- `Cache-Control: s-maxage`
	- 프록시 캐시에만 적용되는 max-age
- `Age: 60`
	- 오리진 서버에서 응답 후 프록시 캐시 내에 머문 시간(초)

## 캐시 무효화
확실하게 캐시 무효화 하고 싶으면 아래 헤더를 다 넣는다.
```
Cache-Control: no-cache, no-store, must-revalidate
Pragma: no-cache
```
- `Cache-Control: must-revalidate`
	- 캐시 만료후 최초 조회시 원 서버에 검증해야함
	- 원 서버 접근 실패시 반드시 오류가 발생해야함 - 504(Gateway Timeout)
	- must-revalidate는 캐시 유효 시간이라면 캐시를 사용함

예를 들어 프록시 캐시 서버와 origin 서버가 단절 되었을 때
- `Cache-Control: no-cache`는 경우에 따라 프록시 캐시 서버가 OK 200을 보내줄 수 있고
- `Cache-Control: must-revalidate`는 오류가 발생한다.

