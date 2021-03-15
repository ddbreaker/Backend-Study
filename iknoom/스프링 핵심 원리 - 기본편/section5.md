# 싱글톤 컨테이너

## 싱글톤 패턴

클래스의 인스턴스가 1개만 생성되는 것을 보장하는 디자인 패턴

```java
public class SingletonService {

    private static final SingletonService instrance = new SingletonService();

    public static SingletonService getInstance() {
        return instrance;
    }

    private SingletonService() {
    }

    public void logic() {
        System.out.println("싱글톤 로직 호출");
    }
}
```

스프링 컨테이너로 만들면 모두 싱글톤 패턴으로 만든다.

문제점

- 위 코드에서 보다싶이 코드가 길어진다.
- 클라이언트가 구체 클래스에 의존한다. -> DIP를 위반하며, OCP도 위반할 가능성이 높다
- 테스트하기 어렵다.
- 자식클래스 만들기 어렵다.



## 싱글톤 컨테이너

스프링 컨테이너는 모든 객체 인스턴스를 싱글톤으로 관리한다.(스프링 빈)

싱글톤 패턴의 문제점을 모두 해결한다.



### 주의점

무상태(stateless)로 설계해야한다.

- 클라이언트에 의존적인 필드 있으면 안된다. 가급적 읽기만 가능해야한다.
- 필드 대신에 자바에서 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야한다.



## @Configuration은 @Bean의 싱글톤을 보장해준다.

스프링 컨테이너는 싱글톤 레지스트리다. 따라서 스프링 빈이 싱글톤이 되도록 보장해주어야 한다.

- @Configuration이 붙으면 CGLIB라는 바이트코드 조작 라이브러리를 사용해서 클래스(AppConfig)를 상속받은 임의의 다른 클래스(AppConfig@CGLIB)를 만들고, 그 다른 클래스를 스프링 빈으로 등록한다.(확인해보면 클래스 명에 xxxCGLIB가 붙는다.)
- 그 다른 클래스(AppConfig@CGLIB) @Bean이 붙은 메서드마다 싱글톤을 보장하는 로직이 있다.