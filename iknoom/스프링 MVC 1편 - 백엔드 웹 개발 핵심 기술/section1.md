# 웹 어플리케이션 이해

## 웹 시스템 구성

### 웹 서버(Web Server)

- HTTP 기반으로 동작
- 정적 리소스 제공
- ex) NGINX, APACHE

### 웹 애플리케이션 서버(WAS)

- 웹 서버 기능 + 프로그램 코드 실행
  - 애플리케이션 로직 수행
  - 동적 HTML, HTTP API(JSON)
  - 서블릿, JSP, 스프링 MVC
- ex) 톰캣

### WAS + DB으로 구성했을 때 문제점

- WAS가 너무 많은 역할을 담당
- 애플리케이션 로직이 정적 리소스 때문에 수행이 어려울 수 있다.
- WAS 장애시 오류 화면 노출 불가능

### WEB + WAS + DB

- 정적 리소스는 WEB, 애플리케이션 로직은 WAS
- 정적 리소스가 많이 사용되면 WEB 증설
- 애플리케이션 로직이 많이 사용되면 WAS 증설
- WEB에서는 거의 장애가 일어나지 않는다.



## 서블릿

![image](https://user-images.githubusercontent.com/52124204/126437013-839f4984-f0e5-4e79-8252-a309892a426d.png)

![image](https://user-images.githubusercontent.com/52124204/126437041-768d2c55-6f40-4123-b543-f2aa36e69dab.png)

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) {
        // 애플리케이션 로직
    }
}
```

- urlPatterns("/hello")의 URL이 호출되면 서블릿 코드 실행
- HTTP 요청 정보, 응답 정보(HttpServletRequest, HttpServletResponse)를 편리하게 사용 -> HTTP 스펙을 편리하게 사용

### 서블릿 컨테이너

- 톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 함
- 서블릿 객체(HelloServlet)를 생성, 호출, 관리
- 서블릿 객체는 싱글톤으로 관리
  - 고객 요청마다 객체를 생성하는건 비효율 -> 최초 로딩 시점에 서블릿 객체 생성
  - 공유 변수 사용 주의
- JSP도 서블릿으로 변환 되어서 사용

### 동시 요청을 위한 멀티 쓰레드 처리 지원

- 요청마다 쓰레드 생성
  - 단점
    - 쓰레드 생성 비용은 매우 비싸다.(생성하는 시간이 걸린다)
      - 응답 속도가 늦어진다.
    - 컨텍스트 스위칭 비용이 발생
    - 스레드 생성에 제한이 없다.
      - 너무 많은 요청이 오면 메모리 임계점을 넘어서 서버가 죽을 수 있다.
- 쓰레드 풀
  - 쓰레드를 미리 생성해놓는다.
    - 쓰레드 풀에 생성 가능한 쓰레드의 최대치를 관리한다. (톰캣은 최대 200개, 변경 가능)
  - 쓰레드가 부족하면 대기 또는 거절
  - 쓰레드를 생성하고 종료하는 비용이 절약(CPU)되서 응답시간이 빠르다.
  - 최대치가 있어서 안전하다.
- 개발자는 멀티 쓰레드 관련 코드를 신경쓰지 않아도 됨

## SSR, CSR

### SSR - 서버 사이드 렌더링

- 서버에서 최종 HTML을 생성해서 클라이언트에 전달
- 주로 정적인 화면
- JSP, 타임리프

### CSR - 클라이언트 사이드 렌더링

- HTML 결과를 자바스크립트로 동적으로 생성
- (웹 환경을 마치 앱 처럼) 필요한 부분 변경하여 동적인 화면
- React, Vue.js





















