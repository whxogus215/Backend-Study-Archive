# Contents Of Http
- [서블릿](#서블릿의-역할)
  - [서블릿을 통해 처리하는 HTTP 메서드](#get-요청)
  - [서블릿을 이용한 HTTP 응답 처리](#http-응답처리)
- [PORT 그리고 DNS](#PORT)
- 통신

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





