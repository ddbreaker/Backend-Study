# Ch5. HTTP 메서드 활용

## 클라이언트에서 서버로 데이터 전송

데이터를 전송하는 방법은 아래 두가지

- **쿼리 파라미터**
  - GET
  - 주로 정렬 필터(검색)
- **메시지 바디**
  - POST, PUT, PATCH
  - 회원 가입, 상품 주문, 리소스 등록, 변경 등등



### 예시 1. 정적 데이터 조회

- GET 사용, 리소스 경로로 간단하게 조회 가능
- 정적 데이터: 이미지, 정적 텍스트 문서
- 추가적인 데이터가 필요없다. (쿼리 파라미터 미사용)

### 예시2. 동적 데이터 조회

- GET 사용, 쿼리 파라미터 사용
- 주로 검색 + 정렬 필터(검색어)

### 예시3. HTML Form 데이터 전송

- GET 또는 POST 지원
- GET으로 하면 쿼리 파라미터, POST면 메시지 바디에 들어간다.
- `Content-Type: applicaiton:x-www-form-urlencoded`로 전송된다.
- 파일 업로드 같은 바이너리 데이터를 전송할 때는 `Content-Type: multipart/form-data`를 사용

### 예시4. HTTP API 데이터 전송

- 서버 to 서버, 웹/앱 클라이언트
- POST, PUT, PATCH: 메시지 바디
- GET: 쿼리 파라미터
- Content-Type: application/json을 주로 사용(거의 표준, 그 외에는 TEXT, XML)



# HTTP API 설계 예시

## HTTP API - 컬렉션 (대부분의 경우)

- 클라이언트는 등록될 리소스의 URI를 모른다. -> 서버가 생성해준다.
- 컬렉션: 서버가 관리하는 리소스 디렉토리
- ex) 회원 등록 POST /members


## HTTP API - 스토어

- 클라이언트가 리소스 URI를 알고 있어야 한다. -> 클라이언트가 직접 지정
- 스토어: 클라이언트가 관리하는 리소스 저장소
- ex) 파일 등록 PUT /files/star.jpg

## HTML FORM

- GET과 POST만 지원 -> 컨트롤 URI를 써야한다.
  - /new, /edit, /delete
  - 컨트롤 URI는 어쩔 수 없이 사용할 수도 있다. http 메서드로 모든 행동을 정의할 수 없기 때문



## *참고하면 좋은 URI 설계 개념

https://restfulapi.net/resource-naming/

- 문서(document)
  - 단일 개념(파일 하나, 객체 인스턴스, 데이터베이스 row)
- 컬렉션(collection)
  - 서버가 관리하는 리소스 디렉토리
- 스토어(store)
  - 클라이언트가 관리하는 자원 저장소
- 컨트롤러(controller), 컨트롤 URI
  - 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스
