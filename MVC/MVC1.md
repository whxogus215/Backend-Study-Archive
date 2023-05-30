# Contents Of Http
- [서블릿](#서블릿의-역할)
  - [서블릿을 통해 처리하는 HTTP 메서드](#get-요청)
  - [서블릿을 이용한 HTTP 응답 처리](#http-응답처리)
  - [서블릿으로 회원 관리 웹 애플리케이션 만들기](#서블릿으로-회원-관리-웹-애플리케이션-만들기)
  - [JSP로 회원 관리 웹 애플리케이션 만들기](#JSP로-회원-관리-웹-애플리케이션-만들기)
- [MVC 패턴](#MVC-패턴)
  - [MVC 패턴 개요](#MVC-패턴-개요)
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