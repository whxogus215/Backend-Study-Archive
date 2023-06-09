# Contents Of MVC1
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
  - [스프링 MVC 시작하기](#스프링-mvc-시작하기)
  - [스프링 MVC 컨트롤러 통합](#스프링-mvc-컨트롤러-통합)
  - [스프링 MVC - 실용적인 방식](#스프링-mvc---실용적인-방식)
- [스프링 MVC 기본](#스프링-mvc-기본-기능)
  - [프로젝트 설정](#프로젝트-설정)
  - [로깅](#로깅)
  - [요청 매핑](#요청-매핑)
  - [HTTP 요청](#http-요청)
  - [HTTP 응답](#http-응답)
  - [HTTP 메시지 컨버터](#http-메시지-컨버터)
  - [요청 매핑 핸들러 어댑터 구조](#요청-매핑-핸들러-어댑터-구조)

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

### 스프링 MVC 시작하기
```java
@Controller
public class SpringMemberFormControllerV1 {
 @RequestMapping("/springmvc/v1/members/new-form")
 public ModelAndView process() {
    return new ModelAndView("new-form");
 }
}
```
이제는 해당 컨트롤러에 애노테이션 기반으로 작성을 하면 스프링이 알아서 인식을 한다.  
`@Controller`의 경우, 스프링 빈이 자동으로 스프링 빈으로 등록하며, `RequestMappingHandelrMapping`의 **핸들러 대상이 된다.**  
`@RequestMapping`의 경우, 안에 작성된 URL이 호출되면 이 어노테이션을 사용한 메서드를 호출한다.

> 스프링 부트 3.0 이상부터는 스프링 컨트롤러로 인식되기 위해서는 반드시 `@Controller`만을 사용해야 한다고 한다.

다른 로직들도 마찬가지로 기존의 코드들을 그대로 가져오되, 반환 타입만 커스텀 객체인 `ModelView` 대신 스프링에서 구현한 `ModelAndView`를 사용한다.
핵심은 어노테이션을 통해 스프링이 자동으로 처리할 수 있는 객체들을 지정하는 것이다. 이미 MVC에 대한 구조는 스프링에서 다 짜여있으며, 개발자는 그저
그것의 전체적인 구조를 파악하고, 사양에 맞게 어노테이션을 붙여서 만들기만하면 되는 것이다.

### 스프링 MVC 컨트롤러 통합
지금까지 작성한 스프링 MVC 컨트롤러의 경우, 각 URL마다 메서드에 `@RequestMapping`이 붙어 있었다. (메서드 단위에 적용됨)
따라서 이들 메서드를 하나의 컨트롤러로 통합한다면 여러 컨트롤러를 사용하지 않아도 된다.

```java
@Controller
@RequestMapping("/springmvc/v2/members") // 각 메서드에 공통되는 URL을 뽑아냄
public class SpringMemberControllerV2 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/new-form")
    public ModelAndView newForm() {
        return new ModelAndView("new-form");
    }

    // @RequestMapping("/members/") -> /springmvc/v2/members/members로 매핑된다. 따라서 이 URL의 경우, 아무것도 붙이지 않는다. 그래야 /springmvc/v2/members로 매핑된다.
    @RequestMapping
    public ModelAndView members() {
        List<Member> members = memberRepository.findAll();
        ModelAndView mv = new ModelAndView("members");
        mv.addObject("members", members);

        return mv;
    }

    @RequestMapping("/save")
    public ModelAndView save(HttpServletRequest request, HttpServletResponse response) {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        ModelAndView mv = new ModelAndView("save-result");
        mv.addObject("member", member);
        return mv;
    }
}
```
또한 클래스 레벨에 작성한 `@RequestMapping`과 메서드 레벨에서 작성한 `@RequestMapping`을 조합하면 메서드의 `@RequestMapping`에 작성하는
URL의 중복을 줄일 수 있다.  
- 클래스 레벨 `@RequestMapping("/springmvc/v2/members")`
  - 메서드 레벨 `@RequestMapping("/new-form") /springmvc/v2/members/new-form`
  - 메서드 레벨 `@RequestMapping("/save") /springmvc/v2/members/save`
  - 메서드 레벨 `@RequestMapping(") /springmvc/v2/members`
    - 메서드 레벨에 `/members`를 작성할 경우, 클래스 레벨에 있는 members와 겹치기 때문에 메서드 레벨의 URL은 비워둔다.

### 스프링 MVC - 실용적인 방식
지금까지는 컨트롤러가 직접 `ModelAndView` 객체를 생성해서 반환해야 했기 때문에 컨트롤러가 하는 일이 많았다.
하지만 이제는 단순히 논리적인 이름만 반환하고, Model 또한 스프링에서 이미 구현한 Model을 그대로 사용하도록 한다.

```java
@Controller
@RequestMapping("/springmvc/v3/members")
public class SpringMemberControllerV3 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    // @RequestMapping을 통해 HTTP 메서드를 지정할 수 있다. "method = "
//    @RequestMapping(value = "/new-form", method = RequestMethod.GET)
    @GetMapping("/new-form")
    public String newForm() {
        return "new-form";
    }

//    @RequestMapping(method = RequestMethod.GET)
    @GetMapping()
    public String members(Model model) {
        List<Member> members = memberRepository.findAll();

        model.addAttribute("members", members);
        return "members";
    }

//    @RequestMapping(value = "/save", method = RequestMethod.POST)
    @PostMapping("/save")
    public String save(@RequestParam("username") String username, @RequestParam("age") int age, Model model) {
        
        Member member = new Member(username, age);
        memberRepository.save(member);
        
        // 스프링에서 제공하는 Model 사용
        model.addAttribute("member", member);
        return "save-result";
    }
}
```
이처럼 각 메서드들은 단순히 논리적 이름만 반환(String)하며, Model 또한 스프링에서 구현한 Model을 가져와서 사용한다.

이 때, 파라미터로 `@RequestParam`을 통해 스프링 측에서 HTTP 요청 파라미터를 자동으로 쉽고 간편하게 받을 수 있도록 한다.
이는 `request.getParameter()`와 유사한 코드이다. GET/POST 둘 다 지원한다.

`@RequestMapping`을 통해 HTTP 메서드를 지정할 수 있다. 따라서 지정된 메서드 이외의 메서드는 접근할 수 없다.
이 또한 스프링에서 관리한다. 이 때, GET 메서드로 지정하는 경우 `@GetMapping`으로 지정할 수 있고, POST 메서드로 지정하는 경우
`@PostMapping`으로 지정할 수 있다. `@GetMapping` 어노테이션을 들어가 보면 안에 `@RequestMapping`이 있음을 알 수 있다.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@RequestMapping(method = RequestMethod.GET)
public @interface GetMapping {
  ...
}
```
즉, 스프링이 어노테이션을 인식할 때, 안에 포함된 여러 어노테이션들도 확인한다. -> 스프링과 어노테이션 조합의 사기성
사용자가 직접 형식에 맞게 어노테이션을 만든다면 스프링에서도 동작하게끔 할 수 있다는 뜻이다.

## 스프링 MVC 기본 기능
### 프로젝트 설정
프로젝트 생성 시, Packaging을 `Jar`가 아닌 `War`로 한다면 이는 외부 서버에 배포하는 목적으로 사용하는 것이다.
`Jar`의 경우, 항상 내장 서버(톰캣)을 사용하고, `webapp` 경로도 사용하지 않기 때문에 내장 서버 사용에 최적화된 기능이다.

또한 스프링 부트에 `Jar`를 사용하면 `/resources/static/` 위치에 `index.html`을 넣으면 **Welcome Page**
로 사용할 수 있다. (정적 컨텐츠)

### 로깅
`System.out.println()`의 경우, 그저 단순한 값을 출력할 뿐이며 개발 과정에서 지저분한 로그를 남길 수 있기 때문에
실무에서는 절대 사용하지 않는다. 스프링 부트에서 자동으로 포함한 라이브러리인 `SLF4J`(일종의 인터페이스)를 사용하며,
그 중 구현체인 `Logback`을 사용한다. (Log4J 등등도 다 이 라이브러리에 포함)

![로깅 결과](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/5cd2bdd0-aed2-4317-a452-8b15b0e7d3e2)

```java
@RestController
public class LogTestController {
    private final Logger log = LoggerFactory.getLogger(getClass());

    @RequestMapping("/log-test")
    public String logTest() {
        String name = "Spring";

        System.out.println("name = " + name);

        log.trace("trace log = {}", name);
        log.debug("debug log = {}", name);
        log.info("info log = {}", name);
        log.warn("warn log = {}", name);
        log.error("error log = {}", name);

        return "ok"; // @RestController의 메서드이기 때문에 단순히 문자열이 그대로 반환된다. (View의 논리적 이름이 아님)
    }
}
```
- `@RequestController`를 사용할 경우, 반환 값이 바로 **HTTP 메시지 바디에 입력된다.**
- 출력되는 로그의 포맷은 `시간, 로그 레벨, 프로세스 ID, 쓰레드 명, 클래스 명, 로그 메시지`로, 일반적인 `System.out.println()`보다 구체적인 정보들을 제공함을 알 수 있다.
  - `{}` 안에 변수 명에 해당하는 값이 들어간다.
- 로그 레벨 : `TRACE > DEBUG > INFO > WARN > ERROR`
  - 개발 서버는 debug, 운영 서버는 info 레벨로 설정한다.
    - debug 레벨의 경우 굉장히 많은 양의 로그가 남게 된다. (레벨이 info 보다 높기 때문에 그만큼 많은 데이터를 알려줌)

```java
# 전체 로그 레벨 설정(기본 info)
logging.level.root=info

# hello.springmvc 패키지와 그 하위 로그 레벨 설정
logging.level.hello.springmvc=trace
```
- 로그 레벨을 설정하게 되면, 설정한 구간 마다 출력하는 로그의 범위를 지정할 수 있다. 위처럼 전체 레벨을 설정한 뒤,
  하위 레벨을 설정하게 되면, 하위 레벨에 대한 것이 우선적으로 적용된다.

#### 올바른 로그 사용법
`log.debug("data="+data)`
- 이런식으로 메서드 안에 문자를 더하는 연산을 해서는 안된다. 로그를 출력하기 전에 자바에서 위 연산을 먼저 하기 때문이다.
  이는 리소스 낭비에 해당하며, 로그레벨 설정으로 인해 해당 로그가 출력되지 않더라도 연산을 진행할 수 있다.(리소스 낭비)  

`log.debug("data={}", data)`
- 이처럼 더하기 연산을 사용하지 않는 형태를 사용해야 한다. 그러면 만약 로그레벨로 인해 해당 로그가 출력되지 않더라도 위와 같은 불필요한 연산은 하지 않는다.
- 실무에서는 반드시 이렇게 로그를 작성해야 한다.

로그는 단순히 콘솔 창에 입력하는 `System.out.println()`보다 성능이 훨씬 뛰어나며, 콘솔 창 이외에도 별도의 파일로 생성도 가능하다.
또한 로그를 통해 여러 부가정보들을 확인할 수 있기 때문에 실무에서 적극 권장하는 방식이다.

### 요청 매핑
```java
@RestController
public class MappingController {

    private Logger log = LoggerFactory.getLogger(getClass());

    @RequestMapping(value = "/hello-basic", method = RequestMethod.GET)
    public String helloBasic() {
        log.info("helloBasic");
        return "ok";
    }

    @GetMapping(value = "/mapping-get-v2")
    public String mappingGetV2() {
        log.info("mapping-get-v2");
        return "ok";
    }

    @GetMapping(value = "/mapping/{userId}")
    public String mappingPath(@PathVariable("userId") String data) {
        log.info("mappingPath userId={}", data);
        return "ok";
    }

    @GetMapping("/mapping/users/{userId}/orders/{orderId}")
    public String mappingPath(@PathVariable String userId, @PathVariable Long orderId) {
        // @PathVariable의 데이터 변수와 리소스 식별자 이름이 같으면 (" ") 생략 가능
        log.info("mappingPath userId = {}, orderId = {}", userId, orderId);
        return "ok";
    }
}
```
1. `@RestController`를 사용하면 문자열을 반환하여, 이를 HTTP 메시지 바디에 담게 된다.
2. `@RequestMapping`을 통해 특정 URI와 메서드를 연결시키는 작업(매핑)이 가능하다.
   3. URL은 배열을 통해 다중 설정이 가능하다.`@RequestMapping({"/hello-basic", "/hello-go"})`
4. `/hello-basic`과 `/hello-basic/`은 같은 URL이지만 스프링 부트 3.0부터는 이들을 서로 구분하도록 되어 있다. 따라서 매핑 과정에서 구분하도록 한다.
5. `@RequestMapping`에 메서드를 지정하거나 혹은 `GetMapping`, `PostMapping`등을 사용할 수 있다.

#### PathVariable(경로 변수) 사용
이는 URL에 특정한 값을 넣어 전달할 경우, 이들을 처리하는 방식이다. 쿼리 스트링인 ?로 시작하는 방식이 아니므로,
서로 혼동하지 않도록 한다.

```java
@GetMapping("/mapping/{userId}")
public String mappingPath(@PathVariable("userId") String data) {
 log.info("mappingPath userId={}", data);
 return "ok";
}
```
최근 여러 API는 리소스 경로에 값을 넣어 전달하는 경우가 많기 때문에 Pathvariable에 대한 사용법도 숙지하는 것이 필요하다.  
`@Pathvariable`에서의 이름과 파라미터 이름이 같으면 생략할 수 있다.
> @PathVariable("userId") String data -> @PathVariable String data  
> 만약 생략이 되어 있다면 이들의 이름이 같기 때문이라고 이해하면 된다.

PathVariable은 다중 사용도 가능하다. 여러 식별자들을 가져올 수 있다는 뜻이다.
```java
@GetMapping("/mapping/users/{userId}/orders/{orderId}")
public String mappingPath(@PathVariable String userId, @PathVariable Long orderId) {
 log.info("mappingPath userId={}, orderId={}", userId, orderId);
 return "ok";
}
```
만약, 입력받은 식별자 값의 타입과 파라미터의 타입이 일치하지 않을 경우 에러가 발생한다.
- `userId`에 숫자, `orderId`에 문자가 입력되었을 경우
- `2023-06-16 06:59:50.870  WARN 29892 --- [nio-8080-exec-1] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.web.method.annotation.MethodArgumentTypeMismatchException: Failed to convert value of type 'java.lang.String' to required type 'java.lang.Long'; nested exception is java.lang.NumberFormatException: For input string: "hello"]` 출력됨.

#### 특정 헤더 조건 매핑
```java
/**
 * Content-Type 헤더 기반 추가 매핑 Media Type
 * consumes="application/json"
 * consumes="!application/json"
 * consumes="application/*"
 * consumes="*\/*"
 * MediaType.APPLICATION_JSON_VALUE
 */
@PostMapping(value = "/mapping-consume", consumes = "application/json")
public String mappingConsumes() {
    log.info("mappingConsumes");
    return "ok";
}
```
HTTP 요청의 `Content-Type`의 타입을 지정하는 방식이다. 위에서 지정한 형식과 맞지 않는 요청에 대해서는 
415 (Unsupported Media Type)을 반환한다. (컨트롤러(서버) 입장에서 요청받은 데이터를 소비하는 그림이기에 `consumes` 표현을 사용)

```java
/**
 * Accept 헤더 기반 Media Type
 * produces = "text/html"
 * produces = "!text/html"
 * produces = "text/*"
 * produces = "*\/*"
 */
@PostMapping(value = "/mapping-produce", produces = "text/html")
public String mappingProduces() {
     log.info("mappingProduces");
     return "ok";
}
```
이는 컨트롤러가 직접 생성(produces)하는 타입을 지정하는 방식이다. 즉, 위의 경우라면 서버는 `test/html` 형식으로 응답하는 것이다.
따라서 HTTP 요청 시 `Accept 헤더`에 지정한 타입과 일치해야 한다. `Accept`는 클라이언트가 서버로부터 받고 싶은 데이터 형식을 지정하는 헤더이기 때문이다.
만약 헤더가 일치하지 않는다면, 406 (Not Acceptable)을 반환한다.

#### 요청 매핑 API 예시
```java
package hello.springmvc.basic.requestmapping;

import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/mapping/users")
public class MappingClassController {

    /**
     * 회원 목록 조회: GET /mapping/users
     * 회원 등록: POST /mapping/users
     * 회원 조회: GET /mapping/users/id1
     * 회원 수정: PATCH /mapping/users/id1
     * 회원 삭제: DELETE /mapping/users/id1
     */

    @GetMapping
    public String user() {
        return "get usrs";
    }

    @PostMapping
    public String addUser() {
        return "post user";
    }

    @GetMapping("/{userId}")
    public String findUser(@PathVariable String userId) {
        return "get userId=" + userId;
    }

    @PatchMapping("/{userId}")
    public String updateUser(@PathVariable String userId) {
        return "update userId=" + userId;
    }

    @DeleteMapping("/{userId}")
    public String deleteUser(@PathVariable String userId) {
        return "delete userId=" + userId;
    }
}
```
URL마다 겹치는 부분인 `/mapping/users`는 `@RequestMapping`으로 묶으면, 컨트롤러 설계가 훨씬 간편해진다.
또한 RestController이기 때문에 단순한 문자열 형태로 반환이 가능하다. 같은 URL이더라도 요청 메서드에 따라 다른 컨트롤러가
호출됨을 알 수 있으며, Path Variable을 통해 클라이언트로부터 데이터를 입력받아 처리하는 로직도 확인할 수 있다.

### HTTP 요청
애노테이션을 활용한 스프링 컨트롤러는 다양한 파라미터를 지원한다. 즉, HTTP 요청에서 헤더와 바디의 데이터들을
쉽게 가져올 수 있는 다양한 기능들을 지원한다.
```java
@Slf4j
@RestController
public class RequestHeaderController {

    @RequestMapping("/headers")
    public String headers(HttpServletRequest request,
                          HttpServletResponse response,
                          HttpMethod httpMethod,
                          Locale locale,
                          @RequestHeader MultiValueMap<String, String> headerMap,
                          @RequestHeader("host") String host,
                          @CookieValue(value = "myCookie", required = false) String cookie) {

        log.info("request={}", request);
        log.info("response={}", response);
        log.info("httpMethod={}", httpMethod);
        log.info("locale={}", locale);
        log.info("headerMap={}", headerMap);
        log.info("header host={}", host);
        log.info("myCookie={}", cookie);

        return "ok";

    }

}
```
HTTP 서블릿을 포함해, HTTP 요청 헤더에 있는 메서드, locale, 그리고 HTTP 헤더내용을 Key-Value 형태인 Map으로 가져올 수도 있다.
이 때, MultiValueMap은 하나의 Key에 여러 Value들이 있을 수 있다. 이외에 특정 HTTP 헤더를 조회하는 방식도 있다. (`@RequestHeader("")`)
> 스프링 컨트롤러가 사용 가능한 파라미터 목록 공식 매뉴얼 :  
  https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html

#### HTTP 요청 파라미터 - 쿼리 파라미터, HTML Form
클라이언트에서 서버에게 요청 데이터를 전달하는 방법은 크게 세 가지이다.
1. GET - 쿼리 파라미터
2. POST - HTML Form(Content-Type : application/x-www-form-urlencoded)
3. HTTP 메시지 바디에 데이터 담기(JSON)

이 때 1번과 2번은 데이터 형식이 쿼리 파라미터이다. 다만 1번은 URL에 직접 담기고, 2번은 메시지 바디에 담긴다.
하지만 데이터 형식은 동일하다. `PathVariable`은 URL에 입력된 식별자를 가져오는 것이며, 이는 쿼리 파라미터처럼 데이터를
요청하는 것과는 거리가 멀다. `PathVariable`은 그 자체가 요청 URL이 되지만, **쿼리 파라미터는 특정 URL에 별도로 데이터를 담는 방식으로 그 둘은 차이가 존재한다!**

```java
@Slf4j
@Controller
public class RequestParamController {

    @RequestMapping("/request-param-v1")
    public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        log.info("username={}, age={}", username, age);

        response.getWriter().write("ok");
    }
}
```
`HttpServletRequest` 객체를 사용하면 HTTP 요청의 파라미터를 쉽게 가져올 수 있다.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="/request-param-v1" method="post">
    username: <input type="text" name="username" />
    age: <input type="text" name="age" />
    <button type="submit">전송</button>
</form>
</body>
</html>
```
파일의 위치는 `/resources/static`으로 스프링 부트에서 자동으로 인식한다. 이 때 입력되는 `username`과 `age`도
동일하게 쿼리 파라미터 형식으로 넘어온다. 다만 **POST 요청이고, 쿼리 파라미터가 메시지 바디에 담긴다는 차이점만 존재한다.**
같은 컨트롤러를 통해 동일한 로그가 출력됨을 확인할 수 있다. - URL 쿼리 파라미터와 HTML Form 요청은 메서드만 다를뿐 근본은 같다.

#### HTTP 요청 파라미터 - @RequestParam
스프링이 제공하는 어노테이션 `@RequestParam`을 사용하면 Http 서블릿 객체를 사용하지 않아도 된다.
```java
@ResponseBody
    @RequestMapping("/request-param-v2")
    public String requestParamV2(
            @RequestParam("username") String memberName,
            @RequestParam("age") int memberAge) {
        log.info("username={}, age={}", memberName, memberAge);
        return "ok"; // 일반 @Controller에서 문자열을 반환할 경우, 해당 이름의 View를 찾게 된다. -> @ResponseBody를 사용해서 메시지 바디에 "ok"를 담도록 한다.
    }

    @ResponseBody
    @RequestMapping("/request-param-v3")
    public String requestParamV3(
            @RequestParam String username, // HTTP 요청 파라미터의 이름과 같게하면 코드를 간략화할 수 있다.
            @RequestParam int age) {
        log.info("username={}, age={}", username, age);
        return "ok";
    }

    @ResponseBody
    @RequestMapping("/request-param-v4")
    public String requestParamV4(String username, int age) { // HTTP 요청 파라미터의 이름과 동일하다면 @RequestParam도 생략 가능하다.
        log.info("username={}, age={}", username, age);
        return "ok";
    }

    @ResponseBody
    @RequestMapping("/request-param-required")
    public String requestParamRequired(
            @RequestParam(required = true) String username,
            @RequestParam(required = false) Integer age) {
        log.info("username={}, age={}", username, age);
        return "ok";
    }

    @ResponseBody
    @RequestMapping("/request-param-default")
    public String requestParamDefault(
            @RequestParam(required = true, defaultValue = "guest") String username,
            @RequestParam(required = false, defaultValue = "-1") int age) {
        log.info("username={}, age={}", username, age);
        return "ok";
    }

    @ResponseBody
    @RequestMapping("/request-param-map")
    public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
        log.info("username={}, age={}", paramMap.get("username"), paramMap.get("age"));
        return "ok";
    }
```
컨트롤러의 매개변수에 `@RequestParam`을 붙이면 HTTP 요청 파라미터를 바로 가져올 수 있다.
만약, 요청 파라미터 이름과 매개변수를 같게하면, `@RequestParam`의 속성 값을 생략할 수 있다. (`@RequestParam` 어노테이션 자체도 생략이 가능하지만 가급적 붙이는 걸 권장한다. (HTTP 요청 파라미터를 받는 부분임을 표시하기 위해서))
- `RequestParam`의 `required` 값의 디폴트는 true이다. `required`는 말 그대로, 파라미터 필수 여부를 설정하는 것이다.
만약, `required = true`인 파라미터를 요청하지 않게 되면, `Bad Request`가 발생한다.
- 값이 공백일 경우, 이는 `" "` 즉, 빈 문자열로 받아들인다. 따라서 `defaultValue`를 설정할 수 있다.
즉, 요청하지 않은 변수들에 대해 자동으로 설정하는 것이다.
- Map을 사용해서 요청 파라미터 key 값을 통해 값을 가져올 수도 있다.
> `@ResponseBody`를 사용하면 문자열을 반환하면 메시지 바디에 담겨서 전송된다. `@Controller`에서 문자열을 반환해버리면 해당 이름의 View를 찾게 된다. 따라서 `@RestController`를 붙여야 한다. 하지만 
> 이 방법 말고도 `@ResponseBody`를 사용할 수 있다.

#### HTTP 요청 파라미터 - @ModelAttribute
실제로 개발을 할 때는, 요청 파라미터를 받은 뒤 이를 통해 실제 필요한 객체를 생성해야 한다. 즉, Model을 사용해야 한다.
```java
@RequestParam String username;
@RequestParam int age;

HelloData data = new HelloData();
data.setUsername(username);
data.setAge(age);
```
이렇게 요청 파라미터를 받아서 객체를 생성하고 프로퍼티를 설정하는 과정을 하나의 어노테이션으로 간편화할 수 있다.
```java
@Data
public class HelloData {
    private String username;
    private int age;

} // 롬복 @Data - @Getter, @Setter, @ToString 등이 자동으로 적용된다. 
```
```java
@ResponseBody
@RequestMapping("/model-attribute-v1")
public String modelAttributeV1(@ModelAttribute HelloData helloData) {
 log.info("username={}, age={}", helloData.getUsername(),helloData.getAge());
 return "ok";
}
```
1. 먼저 HTTP 요청 파라미터를 바인딩(입력) 받을 객체를 생성한다.
2. 컨트롤러의 파라미터에 `@ModelAttribute` 붙여서 객체를 파라미터로 받도록 한다.

스프링 MVC는 `@ModelAttribute`를 통해 HelloData 객체를 찾은 뒤, 요청 파라미터의 이름으로 HelloData 객체의 프로퍼티를 찾는다.
그리고 해당 프로퍼티의 setter를 호출해서 파라미터 값을 바인딩(입력) 한다. 만약, 요청받은 값의 타입과 프로퍼티 타입이 일치하지 않으면 `BindException`이 발생한다. 이는 검증을 통해 해결한다.(MVC 2편에서 다룸)

객체에 `getUsername()`, `setUsername()` 이라는 메서드가 있다면, 그 객체는 `username`이라는 프로퍼티(멤버)를 갖고 있는 것이다.
> 프로퍼티 xxx -> getXxx() / setXxx()

`@ModelAttribute` 또한 생략이 가능하다. 하지만 앞서 `@RequestParam`도 생략이 가능하다고 했으니 혼동이 생길 수 있다.
- `String`,`int`,`Integer` 같은 단순 타입을 생략할 경우 `@RequestParam`으로 인식
- 그 이외에 사용자가 생성한 객체, argument resolver로 지정된 타입을 생략할 경우 `@ModelAttribute`로 인식
어쨌든 `@ModelAttribute`와 `@RequestParam`은 요청 파라미터를 전달받아 처리한다는 점은 동일하다. **다만 객체에 직접 바인딩을
하냐 안하냐의 차이일 뿐이다.**

#### HTTP 요청 메시지 - 단순 텍스트
요청 파라미터(GET 메서드의 쿼리 파라미터, POST 메서드의 HTML FORM 요청)가 아닌 메시지 바디에 데이터가 직접 담겨져 들어오는 경우
`@ResponseBody`를 사용해야 한다.
> HTML FORM의 경우, 메시지 바디에 데이터를 담아서 요청한다. 하지만 이는 결국 쿼리 스트링 형식으로 전달되기 때문에
쿼리 파라미터로 인정이 되는 것이다. 즉, 메시지 바디에 담기는 경우 Content-Type이 Form이어야만 가능하다. 그 이외에는 다 쿼리 파라미터로 인정되지 않는다.

```java
@Slf4j
@Controller
public class RequestBodyStringController {
    @PostMapping("/request-body-string-v1")
    public void requestBodyString(HttpServletRequest request, HttpServletResponse response) throws IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8); // 바이트 코드를 변환하는 코드

        log.info("messageBody={}", messageBody);

        response.getWriter().write("ok");
    }

    @PostMapping("/request-body-string-v2")
    public void requestBodyStringV2(InputStream inputStream, Writer responseWriter) throws IOException {
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8); // 바이트 코드를 변환하는 코드
        log.info("messageBody={}", messageBody);
        responseWriter.write("ok");
    }

    @PostMapping("/request-body-string-v3")
    public HttpEntity<String> requestBodyStringV3(HttpEntity<String> httpEntity) throws IOException {
        String messageBody = httpEntity.getBody();
        log.info("messageBody={}", messageBody);

        return new HttpEntity<>("ok");
    }
    
    // 실무에서는 이 방식을 사용
    @ResponseBody
    @PostMapping("/request-body-string-v4")
    public String requestBodyStringV4(@RequestBody String messageBody){
        log.info("messageBody={}", messageBody);
        return "ok";
    }
}
```
가장 원초적인 방법은 자바의 `Stream`을 사용하여 HTTP 요청 메시지 바디에 있는 내용을 직접 읽는 것이다.
그러나 스프링에서 지원하는 `HttpEntity`를 사용하면 HTTP 헤더 및 바디 정보를 쉽게 조회할 수 있다. 또한 응답 시에도 `HttpEntity`를 사용할 수 있다.

스프링은 **HTTP 메시지 컨버터**를 통해 HTTP 메시지 바디를 읽어서 알맞은 타입으로 변환하여 전달한다. 또한 반환할 때도
객체를 알맞은 타입(문자, JSON 등)으로 바꿔주는 역할도 한다. 이 덕분에 스프링을 통해 HTTP 메시지 바디를 쉽게 다룰 수 있는 것이다.

`@RequestBody`를 사용하면 HTTP 메시지 바디를 쉽게 조회할 수 있으며, 실무에서 가장 많이 사용하는 방식이다.
이는 앞선 코드들과 달리 여러 객체들을 사용할 필요 없이 어노테이션 하나로 정리가 가능하다.
`@RequestBody`는 메시지 바디를 조회하는 것이다. `@RequestParam`,`@ModelAttribute`처럼 요청 파라미터를 조회하는 것과는
전혀 관계가 없다.

#### HTTP 요청 메시지 - JSON
```java
@Slf4j
@Controller
public class RequestBodyJsonController {

    private ObjectMapper objectMapper = new ObjectMapper();

    @PostMapping("/request-body-json-v1")
    public void requestBodyJsonV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        log.info("messageBody={}", messageBody);
        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());

        response.getWriter().write("ok");
    }

    @ResponseBody
    @PostMapping("/request-body-json-v2")
    public String requestBodyJsonV2(@RequestBody String messageBody) throws IOException {

        log.info("messageBody={}", messageBody);
        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());

        return "ok";
    }

    @ResponseBody
    @PostMapping("/request-body-json-v3")
    public String requestBodyJsonV3(@RequestBody HelloData helloData){
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());

        return "ok";
    }

    @ResponseBody
    @PostMapping("/request-body-json-v4")
    public String requestBodyJsonV4(HttpEntity<HelloData> helloData){
        HelloData body = helloData.getBody();
        log.info("username={}, age={}", body.getUsername(), body.getAge());

        return "ok";
    }

    @ResponseBody
    @PostMapping("/request-body-json-v5")
    public HelloData requestBodyJsonV5(@RequestBody HelloData helloData){
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());

        return helloData;
    }
}
```
단순 문자열로 요청을 받는 것보다는 API를 통해 JSON 타입으로 데이터를 요청 받는 경우가 실제로는 더욱 많다.
하지만 전달받은 JSON을 그대로 사용할 수는 없다. 따라서 JSON을 적절한 문자열 및 기타 객체로 바꿔주는 기능이 필요하다.
이를 쉽게 해주는 것이 앞서 설명한 **HTTP 메시지 컨버터**이다.

`@RequestBody`를 사용하면 메시지 바디에 있는 JSON을 객체로 바꿔줄 수 있다. 그러면 이 객체에서 각종 프로퍼티를
쉽게 가져올 수 있는 것이다. **주의할 점은 `@RequestBody`는 생략이 불가하다.** 생략될 경우, `@ModelAttribute`가 자동으로
추가되기 때문이다.(요청 매개변수가 객체이므로 생략 시 `ModelAttribute`로 인식되는 것임)

HTTP 요청 헤더의 Content-Type이 application/json이어야 한다. 그래야 JSON을 처리할 수 있는 HTTP 메시지 컨버터가 실행된다.

응답의 경우에도, `@ResponseBody`를 통해 직접 응답 메시지 바디에 데이터를 넣을 수 있으며, `HttpEntity` 및 객체를 반환 가능하다.
- `@RequestBody` 사용
  - JSON 요청받음 -> HTTP 메시지 컨버터 실행 -> 객체
- `@ResponseBody` 사용
  - 객체 반환 -> HTTP 메시지 컨버터 실행 -> JSON으로 응답

### HTTP 응답
HTTP 응답에는 크게 세 가지가 있다.
1. 정적 리소스 : 변동되지 않는 정적인 파일 제공 (HTML, CSS, JS가 변하지 않음)
2. 뷰 템플릿 사용 : 웹 브라우저에 동적인 HTML을 제공해야 할 경우 사용
3. HTTP 메시지 사용(HTTP API) : HTML 파일이 아닌 JSON 같은 형식의 데이터를 전달하는 경우 사용

#### 정적 리소스
스프링 부트에서 지원하는 디렉토리는 `/static`,`/public`,`/resources`,`/META-INF/resources`가 있다.
따라서 정적인 파일을 해당 디렉토리에 넣으면 스프링 부트가 서비스를 제공한다.
즉, `src/main/resources/static`이라는 스프링 부트가 지원하는 디렉토리에 `/basic/hello.html`을 추가하면
웹 브라우저에서 `xxx/basic/hello.html`을 통해 접근이 가능하다.

#### 뷰 템플릿
뷰 템플릿을 거치면 자동으로 HTML이 생성되어 View에서 응답을 한다. 주로 HTML을 동적으로 생성하는 용도로 사용한다.
그 외에 뷰 템플릿이 지원하는 어떤 형태라도 지원이 가능하다. 스프링 부트의 경유 기본 뷰 템플릿 경로를 제공한다.  
`src/main/resources/templates` : 뷰 템플릿이므로 templates 폴더로 지정되어 있다. 정적 리소스의 경우 `/static`
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<p th:text="${data}">empty</p>
</body>
</html>
```
이는 Thymeleaf 뷰 템플릿에서 지원하는 형태로 컨트롤러에서 넘겨준 Model Attribute 값을 자동으로 바인딩한다.
```java
@Controller
public class ResponseViewController {

    @RequestMapping("/response-view-v1")
    public ModelAndView responseViewV1() {
        ModelAndView mav = new ModelAndView("response/hello")
                .addObject("data", "hello!");

        return mav;
    }

    @RequestMapping("/response-view-v2")
    public String responseViewV2(Model model) {
        model.addAttribute("data", "hello!");
        return "response/hello"; // @Controller일 때 : view의 논리적 이름 반환
        // response/hello는 스프링에서 지원하는 경로인 resources/templates의 하위 경로이다.
    }
}
```
`@Controller`에서 View 객체를 반환하거나 String을 반환하여 뷰의 논리적 이름을 반환할 수도 있다. 만약 `@ResponseBody`가 있으면
뷰의 논리적 이름이 아닌 메시지 바디에 들어가는 문자열을 반환하게 되는 것이다. 따라서 이 때는 뷰 리졸버가 아닌 HTTP 메시지 컨버터가 실행되어
HTTP 메시지 바디에 직접 문자열이 입력된다.( + HttpEntity 객체도 동일하다.)

Thymeleaf에는 뷰 리졸버 동작에 필요한 설정들이 자동으로 등록되어 있다.
```java
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```
> 뷰의 논리적 이름을 반환했을 때, 뷰 리졸버가 위와 같은 설정 값을 통해 실제 리소스 값을 찾아서 반환한다.

#### HTTP API, 메시지 바디에 직접 입력
HTTP API의 경우, 정적 리소스나 뷰 템플릿을 거치지 않고 직접 HTTP 응답 메시지를 전달하는 경우이다.
```java
@Slf4j
@Controller
@ResponseBody // 클래스 레벨에 어노테이션을 붙이면 메서드 레벨에 전부 적용이 된다.
@RestController // @Controller + @ResponseBody = @RestController
public class ResponseBodyController {

    @GetMapping("/response-body-string-v1")
    public void responseBodyV1(HttpServletResponse response) throws IOException {
        response.getWriter().write("ok");
    }

    @GetMapping("/response-body-string-v2")
    public ResponseEntity<String> responseBodyV2() {
        return new ResponseEntity<>("ok", HttpStatus.OK);
    }

    @ResponseBody
    @GetMapping("/response-body-string-v3")
    public String responseBodyV3() {
        return "ok";
    }

    @GetMapping("/response-body-json-v1")
    public ResponseEntity<HelloData> responseBodyJsonV1() {
        HelloData helloData = new HelloData();
        helloData.setUsername("userA");
        helloData.setAge(20);

        return new ResponseEntity<>(helloData, HttpStatus.OK);
    }

    @ResponseStatus(HttpStatus.OK) // ResponseEntity를 반환하지 않기 때문에 HTTP 상태코드를 어노테이션으로 설정해줘야 한다.
    @ResponseBody
    @GetMapping("/response-body-json-v2")
    public HelloData responseBodyJsonV2() {
        HelloData helloData = new HelloData();
        helloData.setUsername("userA");
        helloData.setAge(20);

        return helloData;
    }


}
```
- `ResponseEntity(HttpEntity를 상속받음)` 객체를 사용하면 메시지 바디와 함께 응답 코드도 설정이 가능하다.
- `@ResponseBody`를 사용하면 HTTP 메시지 컨버터를 통해 HTTP 메시지를 직접 입력할 수 있다. ( + ResponseEntity도 동일하게 동작한다.)
- `ResponseEntity`도 JSON 타입의 데이터로 반환하는 데 사용할 수 있다.
  - 만약, `@ResponseBody`를 사용하는 경우라면, 응답 코드를 설정하기 까다롭다. 따라서 추가로 `@ResponseStatus`라는 어노테이션을 붙여준다.
    - 프로그램 조건에 맞게 동적으로 응답코드를 변경해야 한다면 `ResponseEntity`를 사용해야 한다. (어노테이션은 고정되어 있기 때문)
- `@RestController`는 `@Controller`와 `@ResponseBody`가 합쳐진 것이다. 실제로 `@RestController` 안에 `@Controller`와 `@ResponseBody`가 있다.
컨트롤러 전체에 `@ResponseBody`를 적용해야 할 경우, 클래스 레벨에 작성하면 된다. 이렇게 설정하게 되면 이는 뷰 템플릿을 사용하지 않고
직접 데이터를 전달하는 형태로, Rest API(HTTP API)를 만들 때 사용하는 컨트롤러이다. 따라서 이름이 `@RestController`인 것이다.

### HTTP 메시지 컨버터
![메시지 컨버터](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/64627cff-4d82-4869-bee4-5da5f588850c)

HTTP 메시지 컨버터는 기본적으로 **인터페이스**이다. 따라서 특정 조건에 맞는 여러 형태의 컨버터를 지원하는 것이다.
- HTTP 요청에서 사용되는 메시지 컨버터 : `@RequestBody`,`HttpEntity(RequestEntity)` 사용했을 경우
- HTTP 응답에서 사용되는 메시지 컨버터 : `@ResponseBody`,`HttpEntity(ResponseEntity)` 사용했을 경우

```java
public interface HttpMessageConverter<T> {
    
  boolean canRead(Class<?> clazz, @Nullable MediaType mediaType);
  boolean canWrite(Class<?> clazz, @Nullable MediaType mediaType);
  
  List<MediaType> getSupportedMediaTypes();
  
  T read(Class<? extends T> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException;
    void write(T t, @Nullable MediaType contentType, HttpOutputMessage outputMessage)
    throws IOException, HttpMessageNotWritableException;
    
}
```
HTTP 메시지 컨버터는 HTTP 요청 및 응답 둘 다 사용된다. 메시지 컨버터는 다음과 같은 메서드 순서로 동작한다.(선체크 후동작)
- `canRead(), canWrite()` : 메시지 컨버터가 해당 클래스와 미디어 타입을 지원하는지 체크하는 메서드
- `read(), write()` : 메시지 컨버터를 통해서 실제 메시지를 읽고 쓰는 메서드

![스프링 부트 메시지 컨버터](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/a7218850-d7e4-4c19-a5d2-dad867212bc2)

HTTP 요청의 경우, 요청 대상의 클래스 타입을 확인하며, HTTP 요청 헤더의 `Content-Type`을 확인한 후, 알맞은 컨버터를 사용한다.  
HTTP 응답의 경우, 응답 대상의 클래스 타입을 확인하며, HTTP 요청 헤더의 `Accept` 미디어 타입을 확인한다.  
(클라이언트가 받을 수 있는 미디어 타입인지 확인)

![컨버터 동작 순서](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/fd8469a1-edb5-44c9-baab-d8f7cd57734e)

> `*/*`은 아무 타입이나 상관없다는 뜻이다.

### 요청 매핑 핸들러 어댑터 구조
![mvc 구조](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/4ddfb7d0-d1e1-4484-801d-1d981d04540d)

![핸들러 어댑터 동작방식](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/15a35fc6-43f8-430c-8ab7-c7c32986758e)


MVC 구조에서 개발자가 설계한 컨트롤러(핸들러)가 실행되기 위해서는 핸들러 어댑터가 먼저 실행되어야 한다. 즉, 핸들러가
필요로 하는 파라미터들을 찾은 뒤, 컨트롤러 메서드에 집어넣어 호출하는 것이다. 따라서 핸들러 어댑터 입장에서 컨트롤러를
호출하기 위해서는 파라미터를 어디서 가져오거나 직접 생성해야만 한다. 이 **핸들러 어댑터에게 컨트롤러를 호출하기 위해 필요한
파라미터(Argument)를 직접 생성하여 제공하는 것이 Argument Resolver이다.** 그리고 이 Argument가 HTTP 메시지 컨버터를 사용한다.

#### Argument Resolver
애노테이션 기반 컨트롤러의 장점은 여러 종류의 파라미터를 지원한다는 것이다. (`@RequestBody`,`HttpEntity`,`@ModelAttribute`,`@RequestParam` 등등)
핸들러 어댑터는 Argument Resolver를 호출하여 컨트롤러가 필요로 하는 다양한 파라미터의 값(객체)을 생성한다.
이를 통해 핸들러 어댑터는 컨트롤러를 호출할 수 있게 되는 것이다.

스프링은 30개가 넘는 ArgumentResolver를 갖고 있다.  
(참고 : https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html)

```java
public interface HandlerMethodArgumentResolver {
    boolean supportsParameter(MethodParameter parameter);
    
    @Nullable
    Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
        NativeWebRequest webRequest, @Nullable WebDataBinderFactory 
            binderFactory) throws Exception;
}
```

ArgumentResolver는 인터페이스로 구현되어 있기 때문에 다양한 종류의 리졸버로 확장이 가능하다. 따라서 ArgumentResolver의
정식명칭은 `HandlerMethodArgumentResolver`이다. 첫 번째로 `supportsParameter()`를 호출하여 컨트롤러가 필요로 하는 파라미터를
생성해낼 수 있는지를 판단한다. 만약 True라면 `resolveArgument()`를 호출하여 실제 객체를 생성한다.(반환타입 Object) 그리고 이렇게
생성된 객체가 컨트롤러 호출시 넘어가게 되는 것이다.

#### ReturnValueHandler
`HandlerMethodReturnValueHandler`를 줄여서 ReturnValueHandler라고 부른다. 이 또한 ArgumentResolver와 마찬가지로
인터페이스로 구현되어 있다. 스프링은 10여개가 넘는 ReturnValueHandler를 갖고 있다.  
(참고 : https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/return-types.html)

이 ReturnValueHandler는 반환 타입으로 오는 `String`,`HttpEntity`,`Model`등을 처리하는 것으로 ArgumentResolver와 비슷한 역할을 한다.
```java
public interface HandlerMethodReturnValueHandler {

	boolean supportsReturnType(MethodParameter returnType);
	void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType,
			ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws Exception;

}
```

#### HTTP 메시지 컨버터가 동작하는 곳
![HTTP 메시지 컨버터 위치](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/17d8b2de-45f8-41f8-9e1e-cf41f6033c0a)

HTTP 메시지 컨버터가 사용되는 조건은 컨트롤러 파라미터에 `@RequestBody` 혹은 클래스(or 메서드) 레벨에 `@ResponseBody`가 있는 경우에
동작한다. 이는 즉, ArgumentResolver 및 ReturnValueHandler에서 처리하는 요청 파라미터 객체 / 반환 타입 처리와 관련이 있는 것이다.
따라서 HTTP 메시지 컨버터는 ArgumentResolver 및 ReturnValueHandler에서 호출된다.

- 요청의 경우 : `@RequestBody`를 처리하는 ArgumentResolver가 있고, `HttpEntity`를 처리하는 ArgumentResolver가 있을 것이다.
이 ArgumentResolver가 HTTP 메시지 컨버터를 사용해서 필요한 객체를 생성하는 것이다. (HTTP 메시지 바디에 있는 데이터를 쉽게 조회할 수 있는 이유)
- 응답의 경우 : `@ResponseBody`와 `HttpEntity`를 처리하는 ReturnValueHandler가 있다. 그리고 여기에서
HTTP 메시지 컨버터를 호출해서 응답 결과를 만든다. (HTTP 응답 메시지 바디에 데이터를 쉽게 넣을 수 있는 이유)

스프링 MVC는 `@RequestBody``@ResponseBody`가 있으면 `RequestResponseBodyMethodProcessor`(ArgumentResolver)를 사용하며,
`HttpEntity`가 있으면 `HttpEntityMethodProcessor`(ArgumentResolver)를 사용한다.

```java
 @ResponseBody
    @PostMapping("/request-body-string-v4")
    public String requestBodyStringV4(@RequestBody String messageBody){
        log.info("messageBody={}", messageBody);
        return "ok";
    }
```
**컨트롤러는 그저 하나의 메서드에 불과하다.** 따라서 요청 파라미터에 어노테이션과 함께 붙어있지만 결국에는 평범한 문자열인 것이다.
컨트롤러는 자바에서 지원하는 여러 타입의 매개변수를 통해 값을 처리하는 메서드이고, 실제 HTTP 요청에서 들어오는 메시지를 적절하게 변환시키는
존재가 필요한 것이다. **컨트롤러에 여러 어노테이션을 붙임으로써 다양한 요청 파라미터를 처리할 수 있다고 해서 컨트롤러가 자동으로 타입을 바꾸고
인식하는 것이 아님을 주의하자! 그 뒤에는 숨은 조력자들이 늘 도와주고 있으며, 컨트롤러는 정해진 업무를 수행하기 위한 메서드에 불과하다.**



