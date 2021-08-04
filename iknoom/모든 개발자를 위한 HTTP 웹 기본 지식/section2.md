# Ch2. URI와 웹 브라우저 요청 흐름

## URI

- Uniform: 통일된 방식
- Resource: 자원
- Identifier: 식별하기 위한 정보

**URL**: 위치(Locator)를 지정

**URN**: 이름(Name)을 지정

URN 이름만으로 자원을 찾는 방법이 보편화 되지 않음

```
scheme://[userinfo@]host[:port][/path][?query][#fragment]
```

### scheme

- 주로 프로토콜을 사용함
- http는 80포트, https는 443포트를 주로 사용한다.
- https는 http에 보안을 추가한 것

### userinfo

- 사용자 정보를 포함해서 인증
- 거의 사용하지 않는다.

### host

- 호스트명
- 도메인명 또는 IP주소

### port

-  접속 포트
- 일반적으로 생략(http는 80포트, https는 443포트)

### path

- 리소스 경로
- 계층적 구조

### query

- key=value형태, &로 추가 가능
- query parameter, query string 등으로 불린다.
- 전부 문자 형태로 넘어간다.

### fragment

- html 내부 북마크 등에 사용(서버에 전송되지 않음)



## 웹 브라우저 요청 흐름

### 1. URL 주소 입력

```
https://www.google.com:443/search?q=hello&hl=ko
```

### 2. 웹 브라우저가 HTTP 요청 메시지 생성

```
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com
```

### 3. SOCKET 라이브러리를 통해 TCP/IP 계층에 전달

1. IP, PORT로 먼저 연결(3way handshake)
2. 데이터 전달

### 4. TCP/IP 패킷 생성

- 출발지 IP, PORT
- 목적지 IP, PORT
- 전송 데이터: HTTP 요청 메시지

### 5. LAN 장비(네트워크 인터페이스)를 통해서 인터넷으로 전송

### 6. HTTP 응답 메시지를 포함한 응답 패킷을 받음

```
HTTP/1.1 200 OKContent-Type: text/html;charset=UTF-8Content-Length: 3423
<html> <body>...</body></html>
```

### 7. 웹 브라우저가 HTML을 렌더링
