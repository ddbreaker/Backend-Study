# 빈 생명주기 콜백

스프링 빈의 이벤트 라이프사이클

```
스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸전 콜백 -> 스프링 종료
```

객체의 생성과 초기화를 분리하자. 생성자는 필수 정보를 받고 메모리에 할당, 초기화는 이렇게 생성된 값으로 외부 커넥션을 연결하는 등 무거운 동작 -> 역할을 확실히 나누자 (유지보수 관점에서 좋다)



콜백을 받는 방법 3가지

### 1. 인터페이스 InitializingBean, DisposableBean

```java
public class NetworkClient implements InitializingBean, DisposableBean {
    ...
}
```

외부 라이브러리에서 사용할 수 없는 단점.

스프링 초창기에 사용한 방법이며 이제 거의 사용하지 않는다.

### 2. 빈 등록 초기화, 소멸 메서드

```java
public void init() {
    System.out.println("NetworkClient.afterPropertiesSet");
    connect();
    call("초기화 연결 메세지");
}

public void close() {
    System.out.println("NetworkClient.destroy");
    disconnect();
}
```

```java
@Bean(initMethod = "init", destroyMethod = "close")
public NetworkClient networkClient() {
    NetworkClient networkClient = new NetworkClient();
    networkClient.setUrl("http://iknoom.com");
    return networkClient;
}
```

destroyMethod는 default 값이 '(inferred)'이며 `close`, `shutdown`라는 이름의 메서드를 자동으로 호출한다.

쓰기 싫으면 `destroyMethod=""`로 설정하면 된다.

### 3. 애노테이션 @PostConstruct, @PreDestory

```java
@PostConstruct
public void init() {
    System.out.println("NetworkClient.afterPropertiesSet");
    connect();
    call("초기화 연결 메세지");
}

@PreDestroy
public void close() {
    System.out.println("NetworkClient.destroy");
    disconnect();
}
```

가장 권장하는 방법

패키지를 보면 `javax.annotation.PostConstruct`인데 스프링이 아니라 자바 표준이라 다른 컨테이너에서도 잘 동작한다.

컴포넌트 스캔과 어울린다.

하지만 외부 라이브러리에 적용하지 못한다. -> 이때는 @Bean 기능을 사용한다.

