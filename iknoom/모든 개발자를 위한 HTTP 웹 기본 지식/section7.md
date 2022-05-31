# HTTP 헤더1 - 일반 헤더
## 헤더
- 작성법
```
header-field = field-name ":" OWS field-value OWS
```
- HTTP 전송에 필요한 모든 부가정보
- 표준 헤더가 많다
- 임의의 헤더 추가 가능

## 헤더 분류
![image](https://user-images.githubusercontent.com/52124204/170034260-58ca318d-466a-45d9-b34d-e34b8e48d438.png)
출처: https://developer.mozilla.org/ko/docs/Web/HTTP/Messages
- General 헤더: 메시지 전체에 적용되는 정보
- Request 헤더: 요청 정보
- Response 헤더: 응답 정보
- representation 헤더(구 Entity 헤더): 표현 데이터(구 엔티티 바디) 정보
	- 표현 데이터: 요청이나 응답에서 전달할 데이터
	- 표현 = 표현 메타데이터 + 표현 데이터, 메시지 바디에 있다.
	- 메시지 바디 = 페이로드(payload)
## 표현 헤더
- `Content-Type`
	- 표현 데이터 형식
- `Content-Encoding`
	- 표현 데이터의 압축 방식
	- 데이터를 읽는 쪽에서 압축 해제
- `Content-Language`
	- 표현 데이터의 자연 언어
- `Content-Length`
	- 표현 데이터의 길이, 바이트 단위
	- Transfer-Encoding(전송 코딩)을 사용하면 이 헤더를 넣으면 안됨
## 협상 헤더
클라이언트가 선호하는 표현 요청에 대한 정보, 요청 시에만 사용한다

- `Accept`
	- 클라이언트가 선호하는 미디어 타입
- `Accept-Charset`
	- 클라이언트가 선호하는 문자 인코딩
- `Accept-Encoding`
	- 클라이언트가 선호하는 압축 인코딩
- `Accept-Language`
	- 클라이언트가 선호하는 자연 언어

협상과 우선순위
- Quality Values(q) 사용
- 0~1 사이의 값, 클수록 높다
- 생략하면 1
- `Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7`
- 더 구체적인걸 기준으로 우선순위를 맞춘다.
- `text/*;q=0.3, text/html;q=0.7, text/html;level=1,text/html;level=2;q=0.4, */*;q=0.5`
	- text/html의 우선순위는 0.7
	- text/plain의 우선순위는 0.3

## 전송 방식에 따른 헤더
- 단순 전송
	- `Content-Length`만 넣어서 한번에 전송
- 압축 전송
	- `Content-Encoding`을 추가해서 전송
- 분할 전송
	- `Transfer-Encoding: chuncked`
```
HTTP/1.1 200 OK
Content-Type: text/plain
Transfer-Encoding: chuncked

5
Hello
5
World
0
\r\n
```
- 범위 전송
	- 데이터의 필요한 범위만 요청 및 응답
	- 요청: `Range: bytes=1001-2000`
	- 응답: `Content-Range: bytes 1001-2000 / 2000`

## 일반 헤더
- `From`: 유저 에이전트의 이메일 정보
- `Referer`: 이전 웹 페이지 주소
- `User-Agent`: 유저 에이전트 애플리케이션 정보
- `Server`: 요청을 처리하는 ORIGIN 서버의 소프트웨어 정보
- `Date`: 메시지가 발생한 날짜와 시간, 응답에서 사용

## 특별한 헤더들
- `Host`
	- 요청 시 필수로 넣어줘야한다.
- `Location`
	- 3xx 또는 201(Created)에서 사용
- `Allow`
	- 허용 가능한 HTTP 메서드
	- 405(Method Not Allowed)를 응답할 시 같이 사용
- `Retry-After`
	- 유저 에이전트가 다음 요청을 하기까지 기다려야하는 시간
	- 503(Service Unavailable)을 응답할 시 같이 사용
- 인증
	- `Authorization`
		- 클라이언트의 인증 정보를 서버에 전달
		- 어떤 인증을 하는지에 따라 넣어줘야하는 값이 다르다
	- `WWW-Authenticate`
		- 리소스 접근에 필요한 인증 방법 정의
		- 401 Unauthorized 응답과 같이 사용
## 쿠키
- 쿠키를 설정하면 모든 요청에 쿠키 정보가 자동으로 포함된다.
- `Set-Cookie`
	- 서버에서 클라이언트로 쿠키 전달(응답)
	- 클라이언트는 서버에서 받은 쿠키를 저장한다.
- `Cookie`
	- 저장된 쿠키를 HTTP 요청 시 같이 전달
- 저장된 쿠키를 모든 요청에 항상 전달하기 때문에 최소한의 정보만 사용하는게 좋음
- 서버에 전송하지 않지만 브라우저에 데이터를 저장하고 싶으면 웹 스토리지 사용
- 주로 사용자 로그인 세션 관리, 광고 정보 트래킹 등에 사용한다.
- 보안에 민감한 데이터는 저장하면 안된다.
- 생명주기
	- 세션 쿠키
		- 만료 날짜를 생략하면 브라우저 종료시까지만 유지
	- 영속 쿠키
		- 만료 날짜를 입력하면 해당 날짜까지 유지
		- `Set-Cookie: expires=Sat, 26-Dec-2020 04:39:21 GMT`
			- 만료일이 되면 쿠키 삭제
		- `Set-Cookie: max-age=3600 (3600초)`
			- 0이나 음수를 지정하면 쿠키 삭제
- 도메인
	- 도메인을 명시하면 명시한 문서 기준 도메인 + 서브 도메인까지 포함
		- domain=example.org를 지정하면 dev.example.org도 쿠키 접근
	- 도메인을 명시하지 않으면 현재 문서 기준 도메인만 적용
		- example.org에서 쿠키를 생성하면 dev.example.org는 쿠키 접근 불가능
- 경로
	- 지정한 경로의 하위 경로 페이지만 쿠키 접근
	- 일반적으로 `path=/` (루트)로 지정
- 보안
	- Secure
		- 이를 적용하면 https인 경우에만 전송할 수 있다
	- HttpOnly
		- XSS 공격을 방지하기 위해서 자바스크립트 접근(document.cookie)이 불가능
	- SameSite
		- XSRF 공격 방지
		- 요청 도메인과 쿠키에 설정된 도메인이 같은 경우에만 쿠키 전송