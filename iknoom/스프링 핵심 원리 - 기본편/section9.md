# 빈 스코프

스코프란 빈이 존재할 수 있는 범위

- 싱글톤 : 스프링 컨테이너 시작 ~ 종료(가장 넓은 범위)
- 프로토타입
- 웹관련 스코프
  - request : 웹 요청이 들어오고 나갈때 까지
  - session : 웹 세션이 생성되고 종료될 때 까지
  - application : 웹 서블릿 컨텍스와 같은 범위



## 프로토타입 스코프

프로토타입 스코프는 스프링 컨테이너에서 조회할 때마다 새로운 인스턴스다.

스프링 컨테이너는 프로토타입 빈을 생성, 의존관계 주입, 초기화까지만 처리

클라이언트가 종료해야한다. (@PreDestroy 같은 메서드가 호출되지 않는다.)

```java
@Scope("prototype")
static class PrototypeBean {
    @PostConstruct
    public void init() {
        System.out.println("PrototypeBean.init");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("PrototypeBean.destroy");
    }

}
```

`ac.close`로 컨테이너를 종료해도 @PreDestroy가 호출되지 않는다.

`prototypeBean1.destroy();`로 직접 호출해야한다.



## 싱글톤에서 프로토타입 빈 사용

```java
@Scope("singleton")
static class ClientBean {
    private final PrototypeBean prototypeBean; // 생성시점에 주입해서 같은 것을 사용

    @Autowired
    public ClientBean(PrototypeBean prototypeBean) {
        this.prototypeBean = prototypeBean;
    }

    public int logic() {
        prototypeBean.addCount();
        int count = prototypeBean.getCount();
        return count;
    }
}
```

이렇게 설계하면 의도와 다르다. 사용할 때마다 새로 생성하지 않는다.

의존관계를 주입하는게 아니라 직접 필요한 의존관계를 찾는 것을 의존관계 탐색, Dependency Lookup(DL)이라고 한다.

applicationContext를 주입받는 것은 설계상 좋지 않다.(클래스가 컨테이너에 종속적, 테스트가 어렵다)

지금 필요한건 딱 `ac.getBean(PrototypeBean.class)`의 기능한 해주는 무언가이다.



### 해결방법1 : ObjectProvider(ObjectFactory)

```java
@Scope("singleton")
static class ClientBean {
    @Autowired
    private ObjectProvider<PrototypeBean> prototypeBeanProvider;

    public int logic() {
        PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
        prototypeBean.addCount();
        int count = prototypeBean.getCount();
        return count;
    }
}
```

ObjectFactory는 ObjectProvider의 부모클래스. ObjectFactory는 과거에 사용하던 것

ObjectProvider에 편의 기능 있다.

기능이 단순하여 단위테스트를 만들거나 mock코드를 만들기 훨씬 쉽다.

하지만 스프링에 의존적인 상황은 똑같다.



### 해결방법2 : JSR-330 Provider

java 표준이지만 gradle에 추가해야한다.

```java
@Scope("singleton")
static class ClientBean {
    @Autowired
    private Provider<PrototypeBean> prototypeBeanProvider;

    public int logic() {
        PrototypeBean prototypeBean = prototypeBeanProvider.get();
        prototypeBean.addCount();
        int count = prototypeBean.getCount();
        return count;
    }
}
```

`get()`메서드 하나만 제공하여 매우 단순하고 자바표준이므로 다른 컨테이너에서도 사용할 수 있다.

하지만 별도의 라이브러리가 필요하다.



## 웹 스코프

- request : HTTP 요청이 들어오고 나갈때 까지 (각각의 HTTP 요청마다 별도의 빈 인스턴스)
- session : HTTP Session과 같은 생명주기
- application : 서블릿 컨텍스(ServletContext)와 같은 생명주기
- websocket : 웹 소켓과 같은 생명주기

request만 이해하면 나머지도 동일하게 이해할 수 있다.

```java
@Component
@Scope(value = "request")
public class MyLogger {

    private String uuid;
    private String requestURL;

    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }

    public void log(String message) {
        System.out.println("[" + uuid + "]" + "[" + requestURL + "] " + message);
    }

    @PostConstruct
    public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this);
    }

    @PreDestroy
    public void close() {
        System.out.println("[" + uuid + "] request scope bean close:" + this);
    }

}
```

MyLogger에서 오류가 발생한다.

싱글톤 빈은 생성해서 주입 가능하지만 request 스코프 빈은 아직 생성되지 않고 고객의 요청이 와야 생성할 수 있다.



1. `ObjectProvider<MyLogger>`를 사용한다.

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final ObjectProvider<MyLogger> myLoggerProvider;

    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    }
}
```

이제 같은 HTTP 요청이면 같은 스프링 빈이 반환된다. `http://localhost:8080/log-demo`

```
[73ef8289-3a20-4759-b558-de1cbb769214] request scope bean create:hello.core.common.MyLogger@128a7bb5
[73ef8289-3a20-4759-b558-de1cbb769214][http://localhost:8080/log-demo] controller test
[73ef8289-3a20-4759-b558-de1cbb769214][http://localhost:8080/log-demo] service id = testId
[73ef8289-3a20-4759-b558-de1cbb769214] request scope bean close:hello.core.common.MyLogger@128a7bb5
```



2. 프록시 빈을 사용한다.

Provider가 아닌 싱글톤처럼 코드를 작성해두고 아래와 같이 수정한다.

```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
	...
}
```

```
myLogger = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$87136fe4
```

CGLIB라는 라이브러리로 가짜 프록시 객체를 만들어서 주입한다.

가짜 프록시 객체에 실제 요청이 오면 그때 실제 빈을 요청하는 위임 로직을 수행한다.

이러한 기능은 웹 스코프가 아니더라도 사용할 수 있다.





