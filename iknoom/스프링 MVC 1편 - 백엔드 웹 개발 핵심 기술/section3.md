# 서블릿, JSP, MVC 패턴

## 

## 서블릿의 한계

뷰(View)화면을 위한 HTML을 만드는 작업이 복잡하다.

```java
	...
	response.setContentType("text/html");
    response.setCharacterEncoding("utf-8");
    PrintWriter w = response.getWriter();
    w.write("<html>");
    w.write("<head>");
    w.write(" <meta charset=\"UTF-8\">");
    w.write(" <title>Title</title>");
    w.write("</head>");
    w.write("<body>");
    w.write("<a href=\"/index.html\">메인</a>");
    w.write("<table>");
    w.write(" <thead>");
    w.write(" <th>id</th>");
    w.write(" <th>username</th>");
    w.write(" <th>age</th>");
    w.write(" </thead>");
    w.write(" <tbody>");
    for (Member member : members) {
        w.write(" <tr>");
        w.write(" <td>" + member.getId() + "</td>");
        w.write(" <td>" + member.getUsername() + "</td>");
        w.write(" <td>" + member.getAge() + "</td>");
        w.write(" </tr>");
    }
    w.write(" </tbody>");
    w.write("</table>");
    w.write("</body>");
    w.write("</html>");
	...
```



## JSP의 등장, JSP의 한계

JSP를 사용하여 뷰를 생성하는 HTML 작업을 더 간단하게 수행한다.

동적으로 변경이 필요한 부분은 자바 코드를 쓴다.

```html
<ul>
    <li>id=<%=member.getId()%></li>
    <li>username=<%=member.getUsername()%></li>
    <li>age=<%=member.getAge()%></li>
</ul>
```

```html
<table>
    <thead>
    <th>id</th>
    <th>username</th>
    <th>age</th>
    </thead>
    <tbody>
    <%
        for (Member member : members) {
            out.write(" <tr>");
            out.write(" <td>" + member.getId() + "</td>");
            out.write(" <td>" + member.getUsername() + "</td>");
            out.write(" <td>" + member.getAge() + "</td>");
            out.write(" </tr>");
        }
    %>
    </tbody>
</table>
```



### 한계

```html
<%@ page import="hello.servlet.domain.member.MemberRepository" %>
<%@ page import="hello.servlet.domain.member.Member" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
    // request, response는 사용 가능
    MemberRepository memberRepository = MemberRepository.getInstance();
    System.out.println("MemberSaveServlet.service");
    String username = request.getParameter("username");
    int age = Integer.parseInt(request.getParameter("age"));

    Member member = new Member(username, age);
    memberRepository.save(member);
%>

<!DOCTYPE html>
<html>
	...
```

비즈니스 로직이 JSP에 다 노출되어있다. JSP가 너무 많은 역할을 한다.



## MVC 패턴의 등장
### 너무 많은 역할

- JSP에서 비즈니스 로직과 뷰 렌더링까지 모두 처리하면 너무 많은 역할을 한다.
- 따라서 유지보수가 어려워진다. (두 역할이 합쳐진 JSP 코드는 복잡할 것이다.)
  - 비즈니스 로직에 변경이 일어나는 경우
  - UI를 변경하는 경우

### 변경의 라이프 사이클

- UI 변경과 비지니스 로직 변경의 라이프 사이클이 다르다.
  - 두 변경은 각각 다르게 발생할 가능성이 매우 높고 대부분 서로에게 영향을 주지 않는다.

### 기능 특화
- JSP 같은 뷰 템플릿은 화면을 렌더링 하는데 최적화 되어 있다.
- 이 역할만 담당하는 것이 가장 효과적이다.



### MVC(Model View Controller)

- **컨트롤러**(Controller)
  - HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행한다.
  - 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.
- **모델**(Model)
  - 뷰에 출력할 데이터를 담아둔다.
  - 뷰가 필요한 데이터를 모두 모델에 담아서 전달해주는 덕분에 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 되고, 화면을 렌더링 하는 일에 집중할 수 있다.
- **뷰**(View)
  - 모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중한다.

> 참고
>
> 컨트롤러에 비즈니스 로직을 둘 수도 있지만, 이렇게 되면 컨트롤러가 너무 많은 역할을 담당한다.
>
> 그래서 일반적으로 비즈니스 로직은 서비스(Service)라는 계층을 별도로 만들어서 처리한다.

![image](https://user-images.githubusercontent.com/52124204/129238865-b700b471-8887-4e2b-a79e-6531384f527e.png)