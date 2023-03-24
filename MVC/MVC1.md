# Contents Of Http
- [서블릿](#서블릿의-역할)
  - [서블릿을 통해 처리하는 HTTP 메서드](#get-요청) 
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

### POST 요청(HTML form)
- **POST 요청의 경우 메세지의 바디에 내용을 담아 전달한다. ** POST 요청에는 두 가지 방법이 있는데, 그 중 HTML form을 통해 데이터를 전달할 경우 GET의 Query parameter와 동일한 형식으로 데이터를 전달한다. 따라서 GET 요청의 Query Parameter를 처리하는 로직에서 그대로 POST의 HTML form 요청 데이터도 같이 받을 수 있다. (두 HTTP 메서드에 대한 요청을 하나의 로직에서 처리할 수 있다는 편리함이 있음)
  - 그 이유는 둘 다 서버로부터 받는 데이터의 형식이 Query Parametr로 동일하기 때문이다.
  - 다만 GET 과 다른 점은 메세지 바디가 있기 때문에 HTTP 헤더의 Content-Type이 Null이 아니다. `application/x-www-form-urlencoded `로 이는 웹 브라우저에서 자동으로 생성한다.
![image](https://user-images.githubusercontent.com/70999462/227410958-0e02b353-6a64-4f9e-a737-e489f6fd9dc9.png)
> [김영한 스프링 MVC 1편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)




