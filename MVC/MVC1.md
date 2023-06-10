# Contents Of Http
- [서블릿](#서블릿의-역할)
  - [서블릿을 통해 처리하는 HTTP 메서드](#get-요청)
  - [서블릿을 이용한 HTTP 응답 처리](#http-응답처리)
  - [서블릿으로 회원 관리 웹 애플리케이션 만들기](#서블릿으로-회원-관리-웹-애플리케이션-만들기)
  - [JSP로 회원 관리 웹 애플리케이션 만들기](#JSP로-회원-관리-웹-애플리케이션-만들기)
- [MVC 패턴](#MVC-패턴)
  - [MVC 패턴 개요](#MVC-패턴-개요)
  - [MVC 패턴 적용](#mvc-패턴-적용)
  - [MVC 패턴 한계](#mvc-패턴-한계)
- [프론트 컨트롤러 패턴](#프론트-컨트롤러-패턴)
  - [프론트 컨트롤러 도입 - v1](#프론트-컨트롤러-도입---v1)
  - [View 분리 - v2](#view-분리---v2)
  - [Model 추가 - v3](#model-추가---v3)
  - [단순하고 실용적인 컨트롤러 - v4](#단순하고-실용적인-컨트롤러---v4)
  - [유연한 컨트롤러1 - v5](#유연한-컨트롤러1---v5)
  - [유연한 컨트롤러2 - v5](#유연한-컨트롤러2---v5)
- [스프링 MVC 구조](#스프링-mvc---구조)
  - [스프링 MVC 전체 구조](#스프링-mvc-전체-구조)
  - [핸들러 매핑과 핸들러 어댑터](#핸들러-매핑과-핸들러-어댑터)
  - [뷰 리졸버](#뷰-리졸버)
###### Reference
- **(main)** 인프런 김영한 스프링 MVC 1편 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard


# 서블릿의 역할
네트워크 통신 계층에서 제일 끝 단에 있는 어플리케이션 계층은 여러 프로토콜 방식을 사용하여 호스트 간 통신을 한다. 이 때 클라이언트에서 요청한 여러 정보들을 개발자가 일일이 꺼내보는 것은 매우 복잡할 것이다. 따라서 개발자는 요청으로부터 온 데이터를 처리하기 위한 로직만을 집중적으로 개발할 수 있도록 **서블릿**이라는 기능이 생겼다.

### 서블릿이 제공하는 여러 메서드들
```java
    System.out.println("--- REQUEST-LINE - start ---");
    System.out.println("request.getMethod() = " + request.getMethod()); //GET
    System.out.println("request.getProtocol() = " + request.getProtocol()); //HTTP/1.1
    System.out.println("request.getScheme() = " + request.getScheme()); //http
    // http://localhost:8080/request-header
    System.out.println("request.getRequestURL() = " + request.getRequestURL());
    // /request-header
    System.out.println("request.getRequestURI() = " + request.getRequestURI());
    //username=hi
    System.out.println("request.getQueryString() = " +
            request.getQueryString());
    System.out.println("request.isSecure() = " + request.isSecure()); //https 사용 유무
    System.out.println("--- REQUEST-LINE - end ---");
    System.out.println();
```
![image](https://user-images.githubusercontent.com/70999462/227401705-fbdb4ab3-75a0-4d08-b0dd-02a238b5320e.png)
- 이처럼 서블릿은 HTTP 요청으로부터 온 Header 정보를 조회할 수 있는 다양한 메서드를 제공한다.

### GET 요청
- **GET 요청의 경우 주로 Query Parameter( ? + "key" + "=" + "value" )를 사용한다.** 브라우저에서 검색을 하거나 필터링, 혹은 페이지를 넘어갈 때 메세지 바디에 내용을 담지 않고 URL에 함께 데이터를 담아서 전달하는 방식을 많이 사용한다.
```java
System.out.println("[전체 파라미터 조회] - start");
// paramName : Key, request.getParameter(Key) : Value
request.getParameterNames().asIterator()
        .forEachRemaining(paramName -> System.out.println(paramName + "=" + request.getParameter(paramName)));
System.out.println("[전체 파라미터 조회] - end");
System.out.println();

System.out.println("[단일 파라미터 조회]");
String username = request.getParameter("username");
String age = request.getParameter("age");

System.out.println("username = " + username);
System.out.println("age = " + age);
System.out.println();

System.out.println("[이름이 같은 복수 파라미터 조회]");
String[] usernames = request.getParameterValues("username");
for (String name : usernames) {
    System.out.println("username = " + name);
}
```
- 파라미터의 Key 값이 같은 경우는 실무에서는 잘 쓰이지 않는다고 한다. 또한 전체 파라미터를 조회하기 보다는 특정한 파라미터 값을 조회하는 방식을 주로 사용한다고 한다.

### POST 요청(HTML form : 웹 브라우저)
- **POST 요청의 경우 메세지의 바디에 내용을 담아 전달한다.** POST 요청에는 두 가지 방법이 있는데, 그 중 HTML form을 통해 데이터를 전달할 경우 GET의 Query parameter와 동일한 형식으로 데이터를 전달한다. 따라서 GET 요청의 Query Parameter를 처리하는 로직에서 그대로 POST의 HTML form 요청 데이터도 같이 받을 수 있다. (두 HTTP 메서드에 대한 요청을 하나의 로직에서 처리할 수 있다는 편리함이 있음)
  - 그 이유는 둘 다 서버로부터 받는 데이터의 형식이 Query Parametr로 동일하기 때문이다.
  - 다만 GET 과 다른 점은 메세지 바디가 있기 때문에 HTTP 헤더의 Content-Type이 Null이 아니다. `application/x-www-form-urlencoded `로 이는 웹 브라우저에서 자동으로 생성한다.
![image](https://user-images.githubusercontent.com/70999462/227410958-0e02b353-6a64-4f9e-a737-e489f6fd9dc9.png)
> [김영한 스프링 MVC 1편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)

### POST(+ PUT, PATCH) 요청2(API 요청 방식)
- 메세지 바디에 직접 데이터를 담아서 요청할 수 있다. (웹 브라우저에서 하는 것 아님!) 주로 JSON 타입을 많이 사용하며, 서버와 서버 통신 or 안드로이드 앱과 서버 통신 or React/Vue 웹 클라이언트에서 JavaScript 방식으로 데이터를 요청할 때 등등 사용된다.
- 서블릿으로 JSON 데이터를 처리하는 경우, 보통 JSON 타입을 받아서 이를 객체로 파싱하는 작업을 한다. (JavaScript 타입의 데이터를 Java 타입으로 변환해서 처리)
  - JSON도 어쨌든 하나의 문자에 불과하다. 그저 통신에 있어서 데이터의 표준 포맷이기 때문에 이것을 백엔드에서 처리하기 위해선 자바 타입으로 파싱할 수 있어야 한다. 이를 돕는 JSON 변환 라이브러리로는 Jackson, Gson 등이 있다.

```java
@Getter @Setter
public class HelloData {

    private String username;
    private int age;

}
```
> JSON이 파싱될 객체 생성(자바 프로퍼티 접근법 Getter & Setter 자동생성 - 롬복 라이브러리)

```java
private ObjectMapper objectMapper = new ObjectMapper();

@Override
protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    ServletInputStream inputStream = request.getInputStream();
    String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

    System.out.println("messageBody = " + messageBody);

    HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
    System.out.println("helloData.username = " + helloData.getUsername());
    System.out.println("helloData.age = " + helloData.getAge());

    response.getWriter().write("ok");
}
```
> 메세지 바디에 내용을 가져왔을 때는 요청한 JSON 타입이 그대로 전달된다. 따라서 이를 자바 객체로 변환하기 위해 Jackson 라이브러리의 ObjectMapper 객체를 선언해서 사용해야 한다.

![image](https://user-images.githubusercontent.com/70999462/227415765-ecf44fe6-c717-4653-b2c4-223d529603cf.png)
- 이처럼 JSON 타입의 요청 데이터를 자바의 겍체로 접근하여 가져올 수 있다.

### HTTP 응답처리
HttpServlet이 다루는 요청 변수(HttpServletRequest request)와 응답 변수(HttpServletResponse response) 중 응답 변수를 통해 응답에 관한 메서드들을 사용할 수 있다. HTTP 응답의 종류는 크게 텍스트 출력, HTML 출력 그리고 API 응답이다. **주의할 점은 각각의 방식마다 Content-Type이 다르기 때문에 타입별로 지정해주어야 한다.**
```java
//[status-line]
  response.setStatus(HttpServletResponse.SC_OK); // 단순히 숫자 200을 넘기는 것보다는 더 의미있는 값으로 설정할 수 있음

  //[response-headers]
//        response.setHeader("Content-Type", "text/plain;charset=utf-8");
  response.setContentType("text/plain");
  response.setCharacterEncoding("utf-8");
  response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
  response.setHeader("Pragma", "no-cache");
  response.setHeader("my-header", "hello"); // 사용자가 직접 추가한 헤더 항목

  // 메세지 body 추가
  PrintWriter writer = response.getWriter();
  writer.print("ok");

//        response.setStatus(HttpServletResponse.SC_FOUND);
//        response.setHeader("Location", "/basic/hello-form.html");
  response.sendRedirect("/basic/hello-form.html");
```
> 일반적인 메세지 바디에 텍스트를 담는 방법, Content-Type은 "text/plain"이며, utf-8 인코딩 방식을 사용해야 한글 출력이 깨지지 않는다. 또한 redirect 기능을 지원하여 다른 url로 연결이 가능하다. (이 때 HTTP 응답 코드는 302로 자동 설정)

```java
// Content-Type : text/html; charset=utf-8
response.setContentType("text/html");
response.setCharacterEncoding("utf-8"); // 안하면 한글 깨짐

PrintWriter writer = response.getWriter();
writer.println("<html>");
writer.println("<body>");
writer.println("    <div>안녕?</div>");
writer.println("</body>");
writer.println("</html>");
```
> html을 출력하는 방법으로, 화면에는 안녕이 출력된다. 이때, 안녕은 문자가 아니라 개발자가 작성한 HTML 소스로 이루어져 있다. Content-Type은 "text/html"로 지정해야 한다.

```java
private ObjectMapper objectMapper = new ObjectMapper();
@Override
protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    // Content-Type : application/json
    response.setContentType("application/json");
    response.setCharacterEncoding("utf-8");

    HelloData helloData = new HelloData();
    helloData.setUsername("Kim");
    helloData.setAge(20);

    //{"username" : "Kim", "age" : 20}
    String result = objectMapper.writeValueAsString(helloData);
    response.getWriter().write(result);
}
```
> 주로 사용하는 API 응답 방법이다. JSON도 문자열에 불과하기 때문에 자바 객체를 사용하여 변환할 수 있다.(Jackson 라이브러리) Content-Type은 "application/json"으로 지정해야 한다.

### 서블릿으로 회원 관리 웹 애플리케이션 만들기

```java
  @WebServlet(name = "memberListServlet", urlPatterns = "/servlet/members")
  public class MemberListServlet extends HttpServlet {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse
            response)
            throws ServletException, IOException {
      response.setContentType("text/html");
      response.setCharacterEncoding("utf-8");
      List<Member> members = memberRepository.findAll();
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
  /*
   w.write(" <tr>");
   w.write(" <td>1</td>");
   w.write(" <td>userA</td>");
   w.write(" <td>10</td>");
   w.write(" </tr>");
  */
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
    }
  }
```
자바의 HTTP 서블릿을 사용하면, HTTP 요청의 값들을 가져와 동적인 응답결과를 생성할 수 있다. 하지만 위 코드에서 보이듯이
**HTML 문서를 작성하는 것이 매우 번거롭고 복잡하다.** 이는 자바 코드로 HTML 문서를 만들었기 때문이다. 반대로 HTML 문서에 동적으로 변경되어야
하는 부분만 자바 코드로 구현한다면 훨씬 편리할 것이다. 이를 가능하게 해주는 것이 바로 **템플릿 엔진**이다.  

- 기존의 서블릿 : 자바 코드 안에서 HTML 작성
- 템플릿 엔진 : HTML 안에서 자바(기타 코드 포함) 코드 작성

템플릿 엔진을 사용하면 HTML 문서에서 필요한 부분만 코드를 통해 동적으로 적용할 수 있다. 템플릿 엔진에는 `JSP, Tymeleaf 등`이 있으며,
JSP는 이제 거의 사용하지 않는다. 성능 적인 면에서 더 좋고 스프링과 잘 통합되는 Thymeleaf가 있기 때문이다.

### JSP로 회원 관리 웹 애플리케이션 만들기
앞서 서블릿으로 작성한 코드는 비즈니스 로직을 처리하기엔 문제가 없지만 HTML 코드(View)를 작성하기엔
번거롭고 불편했다. 이는 **자바 코드로 HTML 코드를 작성하기 때문이다.** JSP를 사용하면 HTML 코드를 작성하는
부분을 서블릿보다 간편하게 사용할 수 있다. JSP는 서블릿과 다르게 **HTML 코드를 작성하고 그 안에 자바 코드를 부분부분 작성하게 된다.**

```java
  <%@ page import="hello.servlet.domain.member.MemberRepository" %>
  <%@ page import="hello.servlet.domain.member.Member" %>
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <%
  // request, response 사용 가능
          MemberRepository memberRepository = MemberRepository.getInstance();
          System.out.println("save.jsp");
          String username = request.getParameter("username");
          int age = Integer.parseInt(request.getParameter("age"));
          Member member = new Member(username, age);
          System.out.println("member = " + member);
          memberRepository.save(member);
          %>
  <html>
  <head>
    <meta charset="UTF-8">
  </head>
  <body>
  성공
  <ul>
    <li>id=<%=member.getId()%></li>
    <li>username=<%=member.getUsername()%></li>
    <li>age=<%=member.getAge()%></li>
  </ul>
  <a href="/index.html">메인</a>
  </body>
  </html>
```
- 실행 시에는 확장자인 `.jsp`까지 적어줘야 한다.
- JSP 안에 비즈니스 로직을 처리해야 하는 경우, 자바와 마찬가지로 import가 필요하다.
- `<% ~~ %>` 안에 자바 코드를 입력할 수 있다.
- `<%= ~~ %>` 안에 자바 코드를 입력할 수 있으며, **이는 HTML에 출력된다.**

이처럼 **JSP는 서블릿과 다르게 HTML을 중심으로 하며, 자바 코드를 중간중간 입력하는 방식이다.**

### 서블릿과 JSP의 한계 그리고 이를 해결할 MVC 패턴의 등장
서블릿을 사용할 때는 View 영역 개발이 어려웠고, JSP를 통해 이를 극복할 수 있었다.
하지만 JSP의 경우 화면을 뿌려주는 HTML(View 영역)과 비즈니스 로직을 처리하는 자바 코드가 같이 섞여있다.
즉, JSP에 중요한 비즈니스 로직 코드가 많이 노출되어 있다는 뜻이며 JSP가 너무 많은 역할을 담당하고 있다. (하나의 코드에서 처리하는 것이 매우 많음)

만약 실제 서비스에서 JSP를 사용한다면 이보다 훨씬 많은 양의 코드가 있을 것이다. 여기서 View와 비즈니스 로직을 분리하고
다른 코드를 건드리지 않고 유지보수를 하기엔 매우 어렵다. 따라서 **서블릿 등을 통해 비즈니스 로직을 처리하고, 
JSP는 View 영역에만 집중하도록 서로를 분리시키는 방법이 등장했다. 이것이 바로 MVC 패턴이다.**

## MVC 패턴
### MVC 패턴 개요
MVC의 등장은 JSP의 역할 과부하를 배경으로 한다. 하지만 이것보다 중요한 것은 View 영역과 비즈니스 로직 영역이
**서로 변경되는 주기가 다르기 때문이다.** 서로 코드가 변경되는 주기가 다르다보니 이들을 하나의 코드로 작성하는 것은
바람직하지 않으며, 위험할 수 있다.

또한 JSP는 화면을 렌더링하는, 화면을 뿌려주는 역할에 최적화 되어 있기 때문에 이 부분의 업무에만 집중할 수 있도록
하는 것이 훨씬 효과적이다.

- 컨트롤러 : HTTP 요청을 최초로 받으며, 해당 요청이 올바른지, 파라미터를 검증하며 서비스 계층의 로직을 호출한다. 서비스 계층으로부터 받은 결과 값을 모델에 저장한다.
  - 서비스 : 컨트롤러의 요청을 받은 서비스 계층은 실제 로직을 수행하여 결과 값을 다시 컨트롤러에게 넘긴다.
- 모델 : 뷰 로직에서 출력할 데이터를 담고 있는 곳이다. 모델 덕분에 뷰와 비즈니스 로직은 직접적으로 연결되어 소통하지 않는다.
- 뷰 : 모델에 담겨있는 데이터를 사용해여 화면을 그리는 역할을 한다. (ex. HTML 생성)

![MVC 패턴 이전](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/760c6d1b-a4cd-48c1-b5f8-4244372b89e8)
![MVC 패턴](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/64f5f106-f97a-4a19-b089-c9eb9b623785)

### MVC 패턴 적용
```java
@WebServlet(name = "mvcMemberFormServlet", urlPatterns = "/servlet-mvc/members/new-form")
public class MvcMemberFormServlet extends HttpServlet {
      @Override
      protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
         String viewPath = "/WEB-INF/views/new-form.jsp";
         RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
         dispatcher.forward(request, response);
     }
}
```
컨트롤러를 통해 view 영역에 해당하는 JSP 파일을 불러온다. `dispatcher.forward()`는 서버 내부에서 JSP를 호출하는 것이다.
즉, 클라이언트는 요청을 한 번만 하지만 서버 내부에서는 여러 번 호출이 일어나면서 JSP 결과를 응답한다. **redirect와 다른 점은
redirect는 클라이언트에게 응답이 나갔다가 클라이언트가 경로를 바꾸고 다시 요청한다. 따라서 URL 경로 또한 변경된다.
하지만 forward는 내부에서 호출이 여러번 일어나기 때문에 클라이언트 측에서는 하나의 경로에 대해서 응답이 내려진 것으로 인식한다.**

viewPath가 `/WEB-INF`를 루트 경로로 하고 있는데, 이 경로는 외부에서 직접 JSP를 호출할 수 없는 경로이다. 즉, 클라이언트는
**컨트롤러를 통해서 JSP를 얻을 수 있는 것이다. (컨트롤러가 필요한 이유)** 

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <meta charset="UTF-8">
   <title>Title</title>
</head>
<body>
<!-- 상대경로 사용, [현재 URL이 속한 계층 경로 + /save] -->
<form action="save" method="post">
   username: <input type="text" name="username" />
   age: <input type="text" name="age" />
   <button type="submit">전송</button>
</form>

</body>
</html>
```
form 태그의 action 속성을 보면 상대경로로 지정되어 있다. 따라서 `/servlet-mvc/members/new-form` Path로 요청한 것에 대해서
상위 계층인 `servlet-mvc/members`에서 save 경로로 이동하게 된다.
> /servlet-mvc/members/new-form에서 저장버튼을 누르면 /servlet-mvc/members/save 로 이동한다. (상대경로를 사용)

```java
@WebServlet(name = "mvcMemberSaveServlet", urlPatterns = "/servlet-mvc/members/save")
public class MvcMemberSaveServlet extends HttpServlet {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
       String username = request.getParameter("username");
       int age = Integer.parseInt(request.getParameter("age"));
       Member member = new Member(username, age);
       System.out.println("member = " + member);
       memberRepository.save(member);
       
       //Model에 데이터를 보관한다.
       request.setAttribute("member", member);
       String viewPath = "/WEB-INF/views/save-result.jsp";
       RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
       dispatcher.forward(request, response);
    }
}
```
```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
 <meta charset="UTF-8">
</head>
<body>
성공
<ul>
 <li>id=${member.id}</li>
 <li>username=${member.username}</li>
 <li>age=${member.age}</li>
</ul>
<a href="/index.html">메인</a>
</body>
</html>

```
HttpServlet의 Request 객체에는 내부 저장소가 존재한다. 따라서 이를 **Model로 사용한다.** `setAttribute()`를 사용하면
Model에 데이터를 담을 수 있다. 이 때 담기는 데이터는 저장소인 `memberRepository`로 부터 가져온 결과 값이다.
반대로 View 영역에서는 Model의 데이터를 꺼낼 때 `getAttribute()`를 사용한다. 하지만 JSP 문법에서 `${}`를 제공함으로써 Property 접근법으로 쉽게 값을 가져올 수 있다.

요즘엔 JSP를 거의 쓰지 않는 분위기며, 타임리프를 더 많이 쓰기 때문에 JSP 문법에 대해 굳이 숙지할 필요는 없다.
다만 JSP를 써야하는 상황이 올 경우, 검색을 통해 배우면 된다.(난이도도 많이 어렵지 않음)

### MVC 패턴 한계
MVC 컨트롤러를 통해 여러 계층을 분리할 수 있다. 하지만 컨트롤러 내에서 중복으로 작성되는 부분을 좀 더 효율적으로
관리할 수 있는 방법이 있다. 즉, 컨트롤러가 실행되기에 앞서 **수문장 역할**을 하는 **프론트 컨트롤러를 도입하는 것이다.**

## 프론트 컨트롤러 패턴
![도입 전](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/97a18d79-f8d8-4290-abb2-8fa71a8fab8c)
![도입 후](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/8b7f401a-cdd7-4ee3-9bcf-1d83b1f88a5f)
이처럼 각각의 컨트롤러가 공통적으로 처리하는 부분을 하나의 프론트 컨트롤러로 관리할 수 있다. 그러면 공통 처리가 가능하며,
여러 컨트롤러의 입구가 하나이기 때문에 관리하기가 편하다. 또한 서블릿으로 관리하는 것은 **프론트 컨트롤러 단 하나이다.**
서블릿은 WAS에서 HTTP 요청을 객체로 매핑시키는 것인데, 이를 프론트 컨트롤러에서 수행한다. 그 다음에 **프론트 컨트롤러는 각각의 요청에 맞게
특정 컨트롤러를 호출하기만 하면 되는 것이다.**

스프링 웹 MVC의 핵심인 `DispatcherServlet`도 이 프론트 컨트롤러 패턴으로 구현되어 있다. 따라서 프론트 컨트롤러가 무엇인지
이해한다면 스프링 MVC의 여러 기능들을 보다 쉽게 이해할 수 있을 것이다.

### 프론트 컨트롤러 도입 - v1
![v1 구조](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/6b36f90d-e5fc-4686-9475-d28bdb4d9221)
이처럼 프론트 컨트롤러를 통해 먼저 요청을 받은 후, 각 URI의 매핑정보가 담겨있는 Map을 통해 컨트롤러 객체를 가져온다.
그 다음 해당 컨트롤러의 메서드를 호출하여 기존에 있는 JSP를 활용하여 응답한다.

이 때, 프론트 컨트롤러는 **HttpServlet**이어야 하며, URI별 컨트롤러는 하나의 **인터페이스**를 통해 구현된다.
즉, 인터페이스의 다형성을 활용하면 하나의 인터페이스 타입으로 여러 컨트롤러를 호출할 수 있기 때문이다.

```java
public interface ControllerV1 {
 void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
}
```
```java
public class MemberFormControllerV1 implements ControllerV1 {
 @Override
 public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   String viewPath = "/WEB-INF/views/new-form.jsp";
   RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
   dispatcher.forward(request, response);
 }
}
```
각각의 컨트롤러는 이 인터페이스를 구현하면 된다. 즉, 컨트롤러의 핵심 로직은 인터페이스의 메서드인 `process()`에 들어간다.
이 안에는 기존에 구현했던 로직이 그대로 들어간다. (변경 없음)

```java
@WebServlet(name = "frontControllerServletV1", urlPatterns = "/frontcontroller/v1/*")
public class FrontControllerServletV1 extends HttpServlet {
 private Map<String, ControllerV1> controllerMap = new HashMap<>();
 public FrontControllerServletV1() {
    controllerMap.put("/front-controller/v1/members/new-form", new MemberFormControllerV1());
    controllerMap.put("/front-controller/v1/members/save", new MemberSaveControllerV1());
    controllerMap.put("/front-controller/v1/members", new MemberListControllerV1());
 }
 
 @Override
 protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   System.out.println("FrontControllerServletV1.service");
   String requestURI = request.getRequestURI();
   ControllerV1 controller = controllerMap.get(requestURI);
    if (controller == null) {
        response.setStatus(HttpServletResponse.SC_NOT_FOUND);
        return;
    }
    controller.process(request, response);
    }
}
```
- `urlPatterns = "/front-controller/v1/*`라고 하게 되면, /front-controller/v1/를 포함한 모든 URI는 다 이 서블릿으로 받겠다는 뜻이다.
- 이 프론트 컨트롤러에는 URI 별로 컨트롤러를 매핑하는 HashMap이 존재하며, HttpServlet이기 때문에 Http의 요청과 응답을 처리할 수 있다.
  - 따라서 요청 URI를 가져와 매핑하고 일치하는 컨트롤러 객체를 반환하여 컨트롤러의 메서드를 호출한다. (`service()`)
- JSP의 경우 기존에 사용했던 것을 변경하지 않고 그대로 사용한다. (JSP의 action 부분에서 상대경로를 유지해야 한다.)
  - `action = "save"`로 하게 되면, **현재 URI가 속한 계층 경로** + save로 넘어가게 된다. (상대경로 필요성)

### View 분리 - v2
![V2 구조](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/03b53073-fe7b-459a-a70c-b074dae826b5)
v1에서는 각각의 컨트롤러 마다 View 영역을 처리하는 로직이 중복된다. 따라서 View에서 처리하는 렌더링 부분을 따로 `MyView`라는 객체에 담는다.
그러면 컨트롤러의 중복되는 코드가 줄어들어 훨씬 간편해진다.
```java
public interface ControllerV2 {
 MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
}
```
```java
public class MemberFormControllerV2 implements ControllerV2 {
    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        return new MyView("/WEB-INF/views/new-form.jsp");
    }
    }
```
새로운 컨트롤러 인터페이스는 MyView 객체를 반환한다. 그리고 구현된 컨트롤러는 마찬가지로 MyView 객체를 생성해서 반환한다.
즉, 렌더링에 관련된 부분을 MyView가 처리하도록 하는 것이다. `getRequestDispatcher()`,`forward()` 메서드 부분은 MyView 객체에게 있기 때문에
컨트롤러가 처리하는 부분이 훨씬 간단해진다.
> Dispatcher 객체와 forward 메서드의 경우, 다른 서블릿 혹은 JSP로 이동할 수 있는 기능이다. 서버 내부에서 호출되는 것. 즉, 해당 JSP가 있는 경로로 이동하여 response에 JSP를 담아 응답하도록 하는 것이다.  
> forward() : Includes the content of a resource (servlet, JSP page, HTML file) in the response. -> Response 객체에 JSP 파일을 담는 것임을 알 수 있다.(공식 API 설명)

### Model 추가 - v3
컨트롤러 v2 버전에는 두 가지 문제점이 있었다. **첫 번째**는 구현된 컨트롤러(프론트 컨트롤러 X)가 Http 서블릿을 몰라도 되는데 요청 파라미터로 받고 있다.
**두 번째**는 구현된 컨트롤러에 JSP 즉, View 이름(리소스 위치)이 중복돼서 써지고 있다는 것이다. 이렇게 하면, View의 폴더 위치가 변경되어도
구현 컨트롤러에 들어가서 하나하나 수정하는게 아니라 **프론트 컨트롤러의 코드만 바꿔주면 된다.**

따라서 Model을 추가하여, 컨트롤러가 Model의 파라미터 정보를 사용하게끔 하고, Model에게 논리적 이름을 전달하여
프론트 컨트롤러 측에서 물리적 이름으로 변환하도록 한다. 이렇게 하면 코드도 훨씬 간단해지며, 중복되는 부분도 많이 줄기 때문에
테스트 코드 작성이 쉬워진다.

논리적 이름 : `new-form` -> 물리적 이름 : `/WEB-INF/views/new-form.jsp`

![v3 구조](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/99061c8b-e12d-45da-bfcf-3dd237edaa25)
교안에서는 ModelView가 Model 역할을 하며, 추가로 View 이름까지 전달하기 때문에 ModelView라고 설정하였다.
ModelView의 멤버로는 논리적 이름을 저장하는 `viewName`과 Request 요청 정보를 담는 `HashMap`을 갖고 있다.
즉, 컨트롤러가 실제 접근하는 데이터는 여기에 있다.(Request 객체 아님)
```java
public class ModelView {
   private String viewName;
   private Map<String, Object> model = new HashMap<>();
   
   public ModelView(String viewName) {
    this.viewName = viewName;
   }
   public String getViewName() {
       return viewName;
   }
   public void setViewName(String viewName) {
    this.viewName = viewName;
   }
   public Map<String, Object> getModel() {
    return model;
   }
   public void setModel(Map<String, Object> model) {
    this.model = model;
 }
}
```
```java
public interface ControllerV3 {
    ModelView process(Map<String, String> paramMap);
}
```
컨트롤러 v3는 서블릿 기술을 전혀 사용하지 않는다. 따라서 컨트롤러가 구현하는 메서드도 전혀 서블릿을 몰라도 된다.
기존에 사용하던 Model인 Request 객체 대신 프론트 컨트롤러에게 요청 파라미터를 Map 형태로 전달받는다. 그러면 컨트롤러는
이 값들을 활용하여 ModelView 객체를 생성하여 다시 반환하는 것이다.

```java
public class MemberSaveControllerV3 implements ControllerV3 {
 private MemberRepository memberRepository = MemberRepository.getInstance();
 @Override
 public ModelView process(Map<String, String> paramMap) {
   // 전달받은 매개변수인 paraMap에는 Http 요청 정보가 들어있다.
    String username = paramMap.get("username");
    int age = Integer.parseInt(paramMap.get("age"));
    
   // 클라이언트가 입력한 값을 실제 Member로 생성하여 저장소에 저장
    Member member = new Member(username, age);
    memberRepository.save(member);

   // 서비스 로직이 끝났고, 이제 View 영역으로 넘길 차례 -> JSP 뿌려줘야할 때
    ModelView mv = new ModelView("save-result");
   // ModelView에 논리적 이름을 저장하고, 추가로 뷰에서 필요한 데이터를 담는다.(저장소에 새로 저장한 member에 대한 데이터)
    mv.getModel().put("member", member);
    
    return mv;
 }
}
```
- 컨트롤러가 하는 일
  - 프론트 컨트롤러에게 받은 요청 정보 Map에서 값을 가져와 Member를 생성하여 저장소에 저장하는 로직을 수행한다.(Save 컨트롤러의 예시)
  - 데이터를 전달하는 ModelView를 생성한다. 이 때, ModelView에 논리적 이름을 부여한다.
  - View 영역에서 렌더링 작업에 필요한 정보들을 ModelView(Model)에 담아서 반환한다.
    - 즉, 계층끼리 서로 주고 받는 것이 Request 객체가 아닌 Model이다!

```java
public class FrontControllerServletV3 extends HttpServlet {
 private Map<String, ControllerV3> controllerMap = new HashMap<>();
 
 @Override
 protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   // 구현 컨트롤러에게 전달할 요청 정보(Map) -> Request 객체에 있는 내용을 꺼내서 Map 형태로 생성하는 메서드로 분리
     Map<String, String> paramMap = createParamMap(request);
     // 구현된 컨트롤러에게 받은 Model
     ModelView mv = controller.process(paramMap);
     
     // 논리적 이름을 가져와 물리적 이름으로 변환
     String viewName = mv.getViewName();
     MyView view = viewResolver(viewName);
     
     // 실제 View 영역인 MyView에게 필요한 렌더링 정보(ModelView 객체에 있는 Map)를 담아서 요청 
     view.render(mv.getModel(), request, response);
   }
   
 private Map<String, String> createParamMap(HttpServletRequest request) {
    Map<String, String> paramMap = new HashMap<>();
    // Request 객체에 있는 내용을 꺼내서 Map 형태로 생성하는 메서드로 분리
    // Request 객체에 있는 Key 값을 꺼내서 이에 해당하는 Value와 함께 Map에 저장
    request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> paramMap.put(paramName,
    request.getParameter(paramName)));
    
    return paramMap;
 }
 // 논리적 이름을 전달받아 물리적 이름으로 변환하는 메서드 - View Resolver
 private MyView viewResolver(String viewName) {
    return new MyView("/WEB-INF/views/" + viewName + ".jsp");
 }
}
```
구현 로직이 복잡하고 코드가 많은 경우에 하나의 메서드로 추출하도록 한다. `메서드 추출 단축키 : Ctrl + Alt + M`

```java
public class MyView {

  public void render(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    
    // 전달받은 Map은 ModelView의 Map 저장소이다. 따라서 여기에 있는 값을 가져와서 Request 객체에 저장해야 한다.  
    modelToRequestAttribute(model, request);
    // 이전에 View 영역에서 하던 것과 동일하다. (수정 X)
    RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
    dispatcher.forward(request, response);
  }

  private void modelToRequestAttribute(Map<String, Object> model, HttpServletRequest request) {
     // ModelView의 Map 저장소의 Key, Value를 Request 객체에 저장한다. (람다식 사용)   
     model.forEach((key, value) -> request.setAttribute(key, value));
  }
}
```
이전에는 Request 객체에 값을 저장해서 전달했지만 이제는 Model 객체를 통해 데이터를 주고 받기 때문에 별도로 Model 객체의 데이터 저장소인 Map을 추가로 전달한다.
따라서 메서드를 오버로딩하여 내용을 추가해야 한다.

오버로딩된 메서드에서 추가된 부분은 **ModelView의 Map 저장소에 담긴 내용을 그대로 Request 객체에 저장하는 것이다.**
실제로 Request 객체에 값을 저장하여 데이터를 주고받지는 않았지만 최종적으로는 Request 객체에 값을 저장해야 한다.
**JSP의 경우, request.getAttribute()로 데이터를 조회하기 때문이다.**
> Request에 저장한 Key 값을 그대로 JSP에서 사용하여 값을 조회한다. 또한 그 객체에 있는 멤버에 접근하는 것도 실제 객체에 저장된 멤버 이름과 동일하게 사용한다. - members.jsp 참고

내용이 많이 복잡해졌지만, 핵심은 Model을 사용하여 컨트롤러의 종속성을 낮추고, 중복되는 부분을 줄이게 된 것이다.
또한 프론트 컨트롤러가 구현 컨트롤러에게 전달하는 파라미터가 Request 객체에서 Map으로 변경되었다.**(구현 컨트롤러는 Request 객체 즉, 서블릿을 몰라도 된다는 뜻)**

### 단순하고 실용적인 컨트롤러 - v4
v3는 서블릿의 종속성을 제거하고, 뷰 경로의 중복을 제거하는 등의 장점이 있었으나, ModelView를 항상 생성해서
반환하는 작업이 개발자에게 있어서는 번거로울 수 있다. **좋은 프레임워크는 무엇보다 개발자가 단순하고 편리하게
사용할 수 있어야 한다.(실용성이 좋다는 뜻)**
![v4 구조](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/0acc4dec-fcf9-4887-a872-019ef768546a)
```java
public interface ControllerV4 {
 /**
 * @param paramMap
 * @param model
 * @return viewName
 */
 String process(Map<String, String> paramMap, Map<String, Object> model);
}
```
컨트롤러 V4는 ModelView를 반환하지 않고 논리적 이름만 반환한다.
```java
public class MemberSaveControllerV4 implements ControllerV4 {
 private MemberRepository memberRepository = MemberRepository.getInstance();
 @Override
 public String process(Map<String, String> paramMap, Map<String, Object> model) {
   String username = paramMap.get("username");
   int age = Integer.parseInt(paramMap.get("age"));
   Member member = new Member(username, age);
   
   memberRepository.save(member);
   model.put("member", member);
   
   return "save-result";
 }
}
```
V4 버전의 구현 컨트롤러가 하는 것은 전달받은 model에 값을 넣고, 논리적 이름만 반환한다. **즉, 모델이 파라미터로
전달되기 때문에 직접 모델을 생성하지 않아도 된다.**

V1에서 V4까지의 과정을 보면 점진적으로 기능들이 추가되면서 발전한 형태임을 알 수 있다. 이처럼 모든 소프트웨어 개발 및 프레임워크는
한번에 업그레이드 되지 않고, 조금씩 단점들을 보완하는 방향으로 발전한다. **프레임워크나 공통 기능이 수고로워야 사용하는 개발자가 편리해진다.**

### 유연한 컨트롤러1 - v5
우리는 일상생활 속에서 **어댑터**를 유용하게 사용하고 있다. 즉, 110V 콘센트에 220V 충전기를 사용하기 위해서는 이들을 연결시키고 변환하는
어댑터를 사용한다. 소프트웨어 개발에서도 마찬가지로 어댑터 개념이 도입된 `어댑터 패턴`이 존재한다.

먄약 누군가는 ControllerV3 방식을 사용해야 하고, 누군가는 ControllerV4 방식을 사용해야 한다면 컨트롤러를 어떻게 설계해야 할까?
버전별 컨트롤러 인터페이스의 메서드 및 프론트 컨트롤러를 살펴보면, 특정 컨트롤러 버전에 종속되어 있음을 알 수 있다.(핸들러 매핑 등)

각각의 버전에 맞는 어댑터를 사용함으로써 프론터 컨트롤러가 여러 버전의 컨트롤러(핸들러)를 처리할 수 있도록 작성하는 것이 V5 방식이다.
![v5 구조](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/9f71f12d-6b78-435b-a512-c50aca319a80)
컨트롤러라는 용어를 핸들러라는 더 큰 범위의 이름으로 확장하여 표현하였다. 어댑터를 사용하면 컨트롤러 뿐만 아니라 어댑터의 사양에 맞는
모든 것들을 연결시킬 수 있기 때문이다.
```java
public interface MyHandlerAdapter {
    boolean supports(Object handler);
    ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException;
}
```
어댑터를 통해 들어오는 컨트롤러는 Object 타입이기 때문에 이들이 무엇을 구현하고 있는지(인터페이스 확인) 알아야 한다.
또한 Object 타입을 형변환 시켜야 해당 컨트롤러(핸들러)를 사용할 수 있다. 이 때 사용하는 메서드가 `supports()와 handle()`이다.
```java
public class ControllerV3HandlerAdapter implements MyHandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof ControllerV3); // handler가 ControllerV3 타입인 경우에만 True가 반환
    }

    @Override
    public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
        ControllerV3 controller = (ControllerV3) handler;
        
        // 컨트롤러 V3의 경우, ModelView를 반환하기 때문에 ModelView를 생성해서 반환한다.
        // 이처럼 컨트롤러의 버전에 맞게 구현해주면 된다.
        Map<String, String> paramMap = createParamMap(request);
        ModelView mv = controller.process(paramMap);

        return mv;
    }

    // Http의 요청 정보를 하나의 Map에 저장하여 반환하는 메서드
    private static Map<String, String> createParamMap(HttpServletRequest request) {
        Map<String, String> paraMap = new HashMap<>();
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> paraMap.put(paramName, request.getParameter(paramName)));
        return paraMap;
    }
}
```
이미 구현된 컨트롤러를 그대로 사용하되, 이들은 단지 어댑터에서 호출되는 용도이다. 즉, 실제 로직이 수행되는 곳은
핸들러 어댑터임을 알 수 있다.
```java
@WebServlet(name = "frontControllerServletV5", urlPatterns = "/front-controller/v5/*")
public class FrontControllerServletV5 extends HttpServlet {

  // 기존의 컨트롤러와 비교했을 때, 하나의 컨트롤러 타입이 들어가지 않고 Object 타입이 들어간다. -> 모든 컨트롤러 타입이 들어갈 수 있다는 뜻
  private final Map<String, Object> handlerMappingMap = new HashMap<>();
  private final List<MyHandlerAdapter> handlerAdapters = new ArrayList<>();

  public FrontControllerServletV5() {
    initHandlerMappingMap();

    initHandlerAdapters();
  }

  private void initHandlerMappingMap() {
    handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());
  }

  private void initHandlerAdapters() {
    handlerAdapters.add(new ControllerV3HandlerAdapter());
  }

  @Override
  protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    // 핸들러 찾기
    Object handler = getHandler(request);
    if (handler == null) {
      response.setStatus(HttpServletResponse.SC_NOT_FOUND);
      return;
    }
    // 핸들러에 맞는 어댑터를 찾기
    MyHandlerAdapter adapter = getHandlerAdapter(handler);

    // 어댑터가 핸들러를 호출하여 ModelView 획득
    ModelView mv = adapter.handle(request, response, handler);

    // 이하 이전 버전 컨트롤러와 같은 로직
    String viewName = mv.getViewName();
    MyView view = viewResolver(viewName);

    view.render(mv.getModel(), request, response);
  }

  private MyHandlerAdapter getHandlerAdapter(Object handler) {
    for (MyHandlerAdapter adapter : handlerAdapters) {
      if (adapter.supports(handler)) {
        return adapter;
      }
    }
    // 해당 핸들러를 지원하는 어댑터가 없을 경우 발생하는 예외
    throw new IllegalArgumentException("handler adapter를 찾을 수 없습니다. handler = " + handler);
  }

  private Object getHandler(HttpServletRequest request) {
    String requestURI = request.getRequestURI();
    return handlerMappingMap.get(requestURI);
  }

  private MyView viewResolver(String viewName) {
    MyView view = new MyView("/WEB-INF/views/" + viewName + ".jsp");
    return view;
  }
}
```
V5의 컨트롤러의 경우, 핸들러 매핑에 특정 컨트롤러 타입이 아닌 `Object` 타입이 들어간다. `Object`는 최상위 클래스이기 때문에 이를 사용하면
보다 유연하게 넓은 범위의 타입이 수용 가능하다. **(아무 값이나 입력받을 수 있기 때문)**

또한 각 핸들러에 맞는 어댑터를 찾을 수 있도록, 어댑터를 List에 담았다. 이렇게 하면 추후에 어댑터를 찾을 때, for문을 통해
알맞는 어댑터를 찾게 된다. (앞서 작성한 `supports()`를 통해 검사)

프론트 컨트롤러에서 수행하는 로직이 많기 때문에 이들을 하나의 메서드로 만들어서 관리하는 것이 더욱 편하고, 코드를 직관적으로 이해할 수 있다.

### 유연한 컨트롤러2 - v5
프론트 컨트롤러에 v4 버전을 추가함으로써, **프론트 컨트롤러를 거의 손대지 않고, 기능을 확장함을 알 수 있다.**
이것이 인터페이스가 필요한 이유이며, 실제 스프링 MVC도 다양한 인터페이스로 구성되어 있다.
```java
private void initHandlerMappingMap() {
        handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());

        // v4 추가
        handlerMappingMap.put("/front-controller/v5/v4/members/new-form", new MemberFormControllerV4());
        handlerMappingMap.put("/front-controller/v5/v4/members/save", new MemberSaveControllerV4());
        handlerMappingMap.put("/front-controller/v5/v4/members", new MemberListControllerV4());
    }

private void initHandlerAdapters() {
        handlerAdapters.add(new ControllerV3HandlerAdapter());
        handlerAdapters.add(new ControllerV4HandlerAdapter());
    }
```
- 프론트 컨트롤러에서 추가된 부분이다. 어댑터 항목 및 URI에 맞는 컨트롤러 매핑 부분
```java
public class ControllerV4HandlerAdapter implements MyHandlerAdapter {
    @Override
    public boolean supports(Object handler) {
        return (handler instanceof ControllerV4);
    }

    @Override
    public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
        ControllerV4 controller = (ControllerV4) handler;

        // 기존에 프론트 컨트롤러가 하던 파라미터 생성을 어댑터가 대신한다. -> 어댑터는 해당 컨트롤러가 필요한 매개변수에 밪춰서 메서드를 호출한다.
        Map<String, String> paramMap = createParamMap(request);
        HashMap<String, Object> model = new HashMap<>();

        // V4 컨트롤러에 의해 model에 필요한 데이터가 담긴다.
        String viewName = controller.process(paramMap, model);

        // 반환타입이 ModelView이기 때문에 ModelView를 생성해서 반환한다. (어댑터의 역할! - 110V를 220V로 바꾸는 어댑터 기능)
        ModelView mv = new ModelView(viewName);
        mv.setModel(model);

        return mv;
    }

    private static Map<String, String> createParamMap(HttpServletRequest request) {
        Map<String, String> paraMap = new HashMap<>();
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> paraMap.put(paramName, request.getParameter(paramName)));
        return paraMap;
    }
}
```
- 단순히 프론트 컨트롤러는 어댑터를 호출하여 원하는 반환타입인 ModelView를 얻는다. 즉, 구체적인 로직은 어댑터의 몫이다. 왜냐하면
프론트 컨트롤러와 컨트롤러의 타입이 안 맞을 수 있고, 그 둘을 연결하는 객체가 어댑터이기 때문이다. 따라서 그 둘을 호환하는 작업(110V를 220V로 바꾸는 작업)을 하는 것이 어댑터이다.
- 위 예시에도 어댑터를 도입하기 전 버전인 컨트롤러에서 직접 하던 ModelView 객체 생성을 어댑터가 대신하고 있다.
  - ControllerV4는 `String`을 반환하고, 프론트 컨트롤러는 `ModelViewe`를 처리해야 한다. 즉, 둘의 반환타입이 다르며,
  **이 둘을 호환해주는 것이 바로 어댑터의 역할이다. (110V -> 220V)**

지금까지 MVC 구조를 직접 만들어보면서 **다형성과 어댑터**를 통해 **기존의 구조를 유지하면서, 기능을 확장시키는 방법을 배웠다.**

## 스프링 MVC - 구조
### 스프링 MVC 전체 구조
![직접 만든 MVC](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/9d2f0dfa-ac45-4b11-8408-2ec93266c7ac)
![스프링 MVC](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/a8670dfa-530c-44bb-b618-eea01efc98c5)

직접 만들었던 MVC 구조와 실제 스프링에서 구현된 MVC 구조와 거의 일치한다.

스프링 MVC에서 제일 중요한 `DispatcherServlet`은 스프링의 **프론트 컨트롤러**이다. DispatcherServlet도 상위에
HttpServlet을 상속받고 있다. 스프링 부트는 이를 자동으로 서블릿으로 등록하며 모든 경로에 대해 매핑한다.
> 더 자세한 경로가 있을 경우, 그것이 더 우선순위가 높다. 따라서 이외의 서블릿도 동작할 수 있다.

서블릿에 호출됨으로써 스프링에서 구현한 핸들러 어댑터 및 핸들러, 뷰 리졸버 등등이 실행된다. 실행의 흐름은 이전에 구현한
컨트롤러 v3 ~ v5 동작 방식과 동일하다.

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
  HttpServletRequest processedRequest = request;
  HandlerExecutionChain mappedHandler = null;
  ModelAndView mv = null;
  // 1. 핸들러 조회
  mappedHandler = getHandler(processedRequest);
  if (mappedHandler == null) {
  noHandlerFound(processedRequest, response);
  return;
  }
  // 2. 핸들러 어댑터 조회 - 핸들러를 처리할 수 있는 어댑터
  HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
  // 3. 핸들러 어댑터 실행 -> 4. 핸들러 어댑터를 통해 핸들러 실행 -> 5. ModelAndView 반환
  mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
  processDispatchResult(processedRequest, response, mappedHandler, mv,
  dispatchException);
  }
private void processDispatchResult(HttpServletRequest request,HttpServletResponse response, HandlerExecutionChain mappedHandler, ModelAndView mv, Exception exception) throws Exception {
  // 뷰 렌더링 호출
  render(mv, request, response);
  }
protected void render(ModelAndView mv, HttpServletRequest request,HttpServletResponse response) throws Exception {
  View view;
  String viewName = mv.getViewName();
  // 6. 뷰 리졸버를 통해서 뷰 찾기, 7. View 반환
  view = resolveViewName(viewName, mv.getModelInternal(), locale, request);
  // 8. 뷰 렌더링
  view.render(mv.getModelInternal(), request, response);
}
```
이는 `DispatcherServlet`에서 핵심 로직인 `doDispatch()` 코드의 일부이다. 기존에 구현했던 프론트 컨트롤러의 `service()`와 거의 비슷하게 동작하고 있음을
알 수 있다.

### 핸들러 매핑과 핸들러 어댑터
과거에는 컨트롤러를 구현할 때 어노테이션 방식이 아닌 인터페이스를 사용하였다.
```java
@Component("/springmvc/old-controller")
public class OldController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        System.out.println("OldController.handleRequest");
        return null;
    }
}
```
- `@Component`를 통해 해당 스프링 빈의 이름을 등록하였으며, 이 URL에 접속했을 때 이 컨트롤러와 매핑된다.
- 이 컨트롤러가 호출되기 위해선 스프링 빈의 이름과 맞는 핸들러를 찾는 **핸들러 매핑**이 필요하다.
- 또한 찾은 핸들러 매핑을 통해서 핸들러를 찾으면 그것을 실행할 **핸들러 어댑터**가 필요하다.

실제 개발자가 핸들러 매핑과 핸들러 어댑터를 구현할 일은 없다. 이미 스프링에서 잘 구현되어 있기 때문이다.
![스프링 핸들러 매핑](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/3be523df-b199-43e3-9d94-34dfc67d17a0)

위의 경우, 사용자가 지정한 스프링 빈 이름을 갖는 핸들러가 있기 때문에 핸들러 매핑으로 `BeanNameUrlHandlerMapping`이 실행되어, `OldController`를 반환한다.

어댑터의 경우, `SimpleControllerHandlerAdapter`가 실행되어, Controller 인터페이스를 지원한다. 그러면 `DispatcherServlet`이 이 핸들러 어댑터를 실행하게 되고,
핸들러 어댑터는 전달받은 정보를 가지고 핸들러인 `OldController`를 내부에서 실행하여 결과만 반환한다.

실무에서는 거의 어노테이션 기반인 `@RequestMapping`을 사용한다. 여기에 사용되는 핸들러 매핑은 `RequestMappingHandlerMapping`, 핸들러 어댑터는
`RequestMappingHandlerAdapter`이다. 이들은 어노테이션 기반으로 작성한 컨트롤러(핸들러)를 지원하는 핸들러 매핑 및 어댑터이다.

### 뷰 리졸버
```java
@Component("/springmvc/old-controller")
public class OldController implements Controller {
 @Override
 public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
   System.out.println("OldController.handleRequest");
   return new ModelAndView("new-form");
 }
}
```
ModelAndView라는 객체를 반환하는데 이 때 논리적 이름을 부여한다. 그러면 스프링에서 이 이름을 갖는 파일이 어디에 있는 것인지를
지정해줘야 한다.

```java
        // application.properties

        spring.mvc.view.prefix=/WEB-INF/views/
        spring.mvc.view.suffix=.jsp
```
즉, 논리적 이름 앞에 `/WEB-INF/views` 뒤에 `.jsp` 가 붙는 것이다. 뷰 리졸버 객체인 `InternalResourceViewResolver`가 스프링 부트를 통해 자동으로 등록된다.
이 때 앞서 properties에서 설정한 정보들을 가져와서 객체를 생성하게 된다.(application.properties를 작성하는 이유)

![뷰 리졸버](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/b31dad89-d463-42f5-b205-238ccb9529cc)
![스프링 부트 뷰 리졸버](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/6bdd07e3-cd4f-4bf7-9d3f-435204137d21)

이처럼 뷰 리졸버는 전달받은 데이터를 통해 적절한 뷰 리졸버 객체를 반환한다. 그러면 프론트 컨트롤러인 `DispatcherServlet`은 전달받은 View 객체의 메서드인 `render()`를 실행하여
View를 처리한다. (JSP의 경우, `forward()`를 통해 해당 JSP로 이동한 뒤 렌더링이 된다. 이를 제외한 나머지 뷰 템플릿들은
바로 자바 코드를 통해 렌더링이 가능하다.)

Thymeleaf를 사용할 경우, 뷰 리졸버 객체로 `TymeleafViewResolver`가 등록된다. 이 또한 스프링 부트가 자동으로 해준다.
사용하는 뷰 템플릿에 따라 쓰이는 뷰 리졸버 객체도 달라짐을 알 수 있다.
