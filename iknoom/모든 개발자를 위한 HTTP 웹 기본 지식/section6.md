# Ch6. HTTP 상태코드
## 상태코드
클라이언트가 보낸 요청의 처리 상태를 응답에서 알려주는 기능
## 2XX 성공
- 200 OK
	- 요청 성공
- 201 Created
	- 새로운 리소스가 생성됨
	- Location 헤더 필드로 생성된 리소스를 식별
- 202 Accepted
	- 요청이 접수되었으나 처리가 완료되지 않았음
- 204 No Content
	- 응답 페이로드 본문에 보낼 데이터가 없음
## 3XX 리다이렉션
리다이렉션 : 웹 브라우저는 3XX 응답 결과에 Location 헤더가 있으면 그 위치로 자동으로 이동한다.
영구 리다이렉션
- 301 Moved Permanently
	- 리다이렉트 시 GET으로 바뀌고 message body가 제거될 수 있다(브라우저 구현마다 다르지만 거의 다 그렇다.)
- 308 Permanent Redirect
	- 리다이렉트 시 메서드와 message body를 유지
일시적인 리다이렉션
- 302 Found
	- 리다이렉트 시 GET으로 바뀌고 message body가 제거될 수 있다(브라우저 구현마다 다르지만 거의 다 그렇다.)
- 307 Temporary Redirect
	- 리다이렉트 시 메서드와 message body를 유지
- 303 See Other
	- 리다이렉트 시 메서드를 GET으로 변경
- 304 Not Modified
	- 캐시를 목적으로 사용

## 4XX 클라이언트 오류
- 400 Bad Request
	- 잘못된 요청
- 401 Unauthorized
	- 인증(Authentication)되지 않음
- 403 Forbidden
	- 승인을 거부함
	- 접근 권한이 불충분한 경우
- 404 Not Found
	- 요청한 리소스가 없음
## 5XX 서버 오류
- 500 Internal Server Error
	- 서버 내부 문제
- 503 Service Unavailable
	- 서비스 이용 불가
