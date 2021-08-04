# Ch3. HTTP 기본

## HTTP

HyperText Transfer Protocol

원래는 html만 담아서 전송하는 프로토콜이었으나 이제는 모든걸 HTTP로 전송한다.

거의 모든 형태의 데이터를 전송 가능

- HTML, TEXT
- 이미지, 음성, 영상, 파일
- JSON, XML (API)
- 서버간에 데이터를 주고 받을 때도 사용

HTTP/1.1 (1997년): 가장 많이 사용하고 현재 가장 중요한 버전

HTTP/2, HTTP/3도 점점 사용량 증가하는 중

### 기반 프로토콜

**TCP**: HTTP/1.1, HTTP/2

**UDP**: HTTP/3

### HTTP 특징

- 클라이언트 서버 구조
- 무상태(stateless) 프로토콜
- 비연결성
- 단순함, 확장 가능



## 1. 클라이언트 서버 구조

Request Response 구조

클라이언트는 서버에 요청하고 서버의 응답을 대기

서버는 비지니스 로직에 집중하고 클라이언트는 UI, 사용성에 집중

양쪽이 독립적으로 진화 가능

## 2. 무상태 프로토콜(stateless)

서버가 클라이언트의 상태를 보존하지 않는다.

무상태는 응답 서버를 쉽게 바꿀 수 있다. -> 서버 증설이 쉽다.

스케일 아웃 - 수평 확장 유리

### 한계

- 모든 것을 무상태로 설계할 수는 없다. ex) 로그인
- 주고받는 데이터가 많다.

## 3. 비 연결성

연결을 유지하지 않는 모델

### 장점 

- 서버 자원을 효율적으로 사용
- 1시간 동안 수천명이 서비스를 사용해도 실제로 동시에 처리하는 요청은 작을 것

### 한계

- TCP/IP 연결을 계속 새로 맺어야함(3 way handshake 반복)
- HTML 뿐만 아니라 js, css, 이미지 등의 많은 자원이 함께 다운로드
- HTTP 지속 연결(Persistent Connections)로 해결
- HTTP/2, HTTP/3에서 더 최적화



## HTTP 메시지

공식 스펙(https://datatracker.ietf.org/doc/html/rfc7230#section-3)

```
HTTP-message
= start-line
  *( header-field CRLF )
  CRLF
  [ message-body ]
```

## start-line

start-line: request-line / status-line

### HTTP 요청 메시지

```
request-line = method SP request-target SP HTTP-version CRLF
// HTTP 메서드 + 요청 대상 + HTTP 버전
```

ex) GET /search?q=hello&hl=ko HTTP/1.1

### HTTP 응답 메시지

```
status-line = HTTP-version SP status-code SP reason-phrase CRLF
// HTTP 버전 + HTTP 상태 코드 + 이유 문구(짧은 상태 코드 설명)
```

ex) HTTP/1.1 200 OK

## HTTP 헤더

```
header-field = field-name ":" OWS field-value OWS
// ":"와 field-name 사이에 띄어쓰기 허용X
// OWS는 띄어쓰기 하거나 하지 않아도 
// field-name은 대소문자 구분 X
// value는 대소문자 구분 O
```

ex) Host: www.google.com

HTTP 전송에 필요한 모든 부가정보

