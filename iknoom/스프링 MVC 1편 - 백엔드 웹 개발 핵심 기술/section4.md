# 프론트 컨트롤러 패턴

![image](https://user-images.githubusercontent.com/52124204/131318539-234704bb-001d-4c7d-b228-9199873b31fc.png)

- 프론트 컨트롤러 서블릿 하나가 클라이언트의 요청을 받는다.
  - 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 된다.
  - 공통 로직 처리 가능

\* 스프링 웹 MVC의 `DispatcherServlet`이 프론트 컨트롤러 패턴으로 구현되어있다.



## 프론트 컨트롤러 도입(v1)

```java
@WebServlet(name = "frontControllerServletV1", urlPatterns = "/front-controller/v1/*")
public class FrontControllerServletV1 extends HttpServlet {

    private Map<String, ControllerV1> controllerV1Map = new HashMap<>();

    public FrontControllerServletV1() {
        controllerV1Map.put("/front-controller/v1/members/new-form", new MemberFormControllerV1());
        controllerV1Map.put("/front-controller/v1/members/save", new MemberSaveControllerV1());
        controllerV1Map.put("/front-controller/v1/members", new MemberListControllerV1());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String requestURI = request.getRequestURI();
        ControllerV1 controller = controllerV1Map.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        controller.process(request, response);
    }
}

```

- `urlPatterns = "/front-controller/v1/*"`으로 특정 url 패턴을 한꺼번에 받는다.
- 컨트롤러는 모두 `ControllerV1`이라는 컨트롤러 인터페이스로 구현한다.
  - 프론트 컨트롤러에서는 `controller.process()`를 공통적으로 호출하면 된다.

## View 분리(v2)

```java
String viewPath = "/WEB-INF/views/new-form.jsp";
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```

모든 컨트롤러에서 뷰로 이동하는 부분이 공통된다.

뷰를 처리하는 객체를 만든다.

```java
@WebServlet(name = "frontControllerServletV2", urlPatterns = "/front-controller/v2/*")
public class FrontControllerServletV2 extends HttpServlet {

    private Map<String, ControllerV2> controllerV1Map = new HashMap<>();

    public FrontControllerServletV2() {
        controllerV1Map.put("/front-controller/v2/members/new-form", new MemberFormControllerV2());
        controllerV1Map.put("/front-controller/v2/members/save", new MemberSaveControllerV2());
        controllerV1Map.put("/front-controller/v2/members", new MemberListControllerV2());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("FrontControllerServletV2.service");
        String requestURI = request.getRequestURI();
        ControllerV2 controller = controllerV1Map.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        MyView view = controller.process(request, response);
        view.render(request, response);
    }
}

```

- `controller.process()`에서 `MyView` 객체를 return하고 `MyView.render()`를 호출하여 공통 로직을 처리한다.



## Model 추가 (v3)

```java
@WebServlet(name = "frontControllerServletV3", urlPatterns = "/front-controller/v3/*")
public class FrontControllerServletV3 extends HttpServlet {

    private Map<String, ControllerV3> controllerV1Map = new HashMap<>();

    public FrontControllerServletV3() {
        controllerV1Map.put("/front-controller/v3/members/new-form", new MemberFormControllerV3());
        controllerV1Map.put("/front-controller/v3/members/save", new MemberSaveControllerV3());
        controllerV1Map.put("/front-controller/v3/members", new MemberListControllerV3());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("FrontControllerServletV2.service");
        String requestURI = request.getRequestURI();
        ControllerV3 controller = controllerV1Map.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        Map<String, String> paramMap = createParamMap(request);

        ModelView mv = controller.process(paramMap);

        String viewName = mv.getViewName();// 논리 이름
        MyView view = viewResolver(viewName);

        view.render(mv.getModel(), request, response);
    }

    private MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }

    private Map<String, String> createParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
}
```

- 컨트롤러에 **서블릿** 종속성을 없앤다.

  - request 객체 대신에 Model 객체를 만들어서 반환한다.
    - `ModelView` 객체를 만들어 view 이름 뿐만이 아니라 Model까지 받는다.
  - 그럼 컨트롤러는 `HttpServletRequest, HttpServletResponse`이 필요 없다.
  - 따라서 컨트롤러로는 파라미터 정보만 넘겨주면 된다. 

- `viewResolver`로 뷰 이름에 중복된 부분을 제거한다.

  