# Contents Of MVC2
- [타임 리프](#타임-리프)
  - [텍스트 - text,utext](#텍스트---text-utext)
  - [변수 - SpringEL](#변수---springel)
  - [유틸리티 객체와 날짜](#유틸리티-객체와-날짜)
  - [URL 링크](#url-링크)
  - [리터럴](#리터럴)
  - [연산](#연산)


###### Reference
- **(main)** 인프런 김영한 스프링 MVC 2편 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard


# 타임 리프
타임리프는 백엔드 서버에서 HTML을 동적으로 렌더링하는 용도로 사용된다.**(SSR : 서버 사이드 렌더링)**
타임리프는 순수 HTML의 특징을 유지하는 특성이 있다. 즉, 타임리프로 작성한 HTML은 서버를 가동하지 않아도
본래의 HTML 파일을 열어볼 수 있다. JSP처럼 서버를 통해서만 렌더링이 되는 경우엔 그냥 HTML을 열 경우, 깨지게 된다.
하지만 타임리프는 서버를 통해 동적으로 렌더링 되지만, HTML의 구조를 그대로 유지하는 장점이 있다.

이처럼 HTML의 특징을 그대로 유지하면서 뷰 템플릿을 사용하는 것을 **네츄럴 템플릿**이라고 한다.

- 타임 리프 기본 선언 : `<html xmlns:th="http://www.thymeleaf.org">`
- 기본 표현식
  - 간단한 표현:
    - 변수 표현식: ${...}
    - 선택 변수 표현식: *{...}
    - 메시지 표현식: #{...}
    - 링크 URL 표현식: @{...}
    - 조각 표현식: ~{...}
  - 리터럴
    - 텍스트: 'one text', 'Another one!',…
    - 숫자: 0, 34, 3.0, 12.3,…
    - 불린: true, false
    - 널: null
    - 리터럴 토큰: one, sometext, main,…
  - 문자 연산:
    - 문자 합치기: +
    - 리터럴 대체: |The name is ${name}|
  - 산술 연산:
    - Binary operators: +, -, *, /, %
    - Minus sign (unary operator): -
  - 불린 연산:
    - Binary operators: and, or
    - Boolean negation (unary operator): !, not
  - 비교와 동등:
    - 비교: >, <, >=, <= (gt, lt, ge, le)
    - 동등 연산: ==, != (eq, ne)
  - 조건 연산:
    - If-then: (if) ? (then)
    - If-then-else: (if) ? (then) : (else)
    - Default: (value) ?: (defaultvalue)
  - 특별한 토큰:
    - No-Operation: _
- 참고 : https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#standard-expression-syntax

### 텍스트 - text, utext
**타임리프는 기본적으로 HTML 태그의 속성에 기능을 정의해서 동작한다.** HTML의 컨텐츠에 데이터를 출력할 경우, `th:text`를 사용한다.
ex) `<span th:text="${data}">`  
HTML 태그를 사용하지 않을 경우, `[[ 내용 ]]`을 사용한다.
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
 <meta charset="UTF-8">
 <title>Title</title>
</head>
<body>
<h1>컨텐츠에 데이터 출력하기</h1>
<ul>
 <li>th:text 사용 <span th:text="${data}"></span></li>
 <li>컨텐츠 안에서 직접 출력하기 = [[${data}]]</li>
</ul>
</body>
</html>
```
#### Escape
HTML 문서는 `<``>` 같은 특수 문자를 기반으로 정의된다. 따라서 뷰 템플릿으로 HTML을 생성할 경우, 이와 같은
특수 문자들을 주의해서 사용해야 한다.

- HTML 엔티티 : 웹 브라우저는 `<`를 태그의 시작으로 인식한다. 즉, `<` 같은 특수문자를 태그의 시작이 아닌 단순한
문자로 표현하는 것이 필요한데 이를 HTML 엔티티라고 한다. ex) `<` -> `&``lt`;(HTML 엔티티)
- `<`가 `&``lt`;라는 HTML 엔티티로 변경하는 것을 **이스케이프(Escape)**라고 한다.
- 따라서 특수문자를 HTML 파일이 아닌 다른 곳에서 문자열로 담아서 출력할 경우, 이처럼 이스케이프가 적용된다.

#### Unescape
타임리프에서 이스케이프가 적용되지 않도록 하는 방법은 크게 두 가지가 있다.
1. `th:text` -> `th:utext`
2. `[[ 내용 ]]` -> `[( 내용 )]` (대괄호에서 소괄호로 변경됨)

하지만 실제 서비스 개발에서 Escape를 사용하지 않으면 HTML이 정상적으로 렌더링되지 않을 수 있다. 따라서 escape 적용을
기본으로 하되, 꼭 필요한 경우에만 Unescape를 사용하는 것을 권장한다.

### 변수 - SpringEL
변수는 `${...}` 형식으로 사용할 수 있다. 보통 Model을 통해 넘어온 값들을 바인딩하기 위해 많이 사용된다.
```java
@GetMapping("/variable")
public String variable(Model model) {
   User userA = new User("userA", 10);
   User userB = new User("userB", 20);
   
   List<User> list = new ArrayList<>();
   list.add(userA);
   list.add(userB);
   
   Map<String, User> map = new HashMap<>();
   map.put("userA", userA);
   map.put("userB", userB);
   
   model.addAttribute("user", userA);
   model.addAttribute("users", list);
   model.addAttribute("userMap", map);
   return "basic/variable";
}
```
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
 <meta charset="UTF-8">
 <title>Title</title>
</head>
<body>
<h1>SpringEL 표현식</h1>
  <ul>Object
   <li>${user.username} = <span th:text="${user.username}"></span></li>
   <li>${user['username']} = <span th:text="${user['username']}"></span></li>
   <li>${user.getUsername()} = <span th:text="${user.getUsername()}"></span></
  li>
  </ul>
  <ul>List
   <li>${users[0].username} = <span th:text="${users[0].username}"></
  span></li>
   <li>${users[0]['username']} = <span th:text="${users[0]['username']}"></
  span></li>
   <li>${users[0].getUsername()} = <span th:text="$
  {users[0].getUsername()}"></span></li>
  </ul>
  <ul>Map
   <li>${userMap['userA'].username} = <span th:text="$
  {userMap['userA'].username}"></span></li>
   <li>${userMap['userA']['username']} = <span th:text="${userMap['userA']
  ['username']}"></span></li>
   <li>${userMap['userA'].getUsername()} = <span th:text="$
  {userMap['userA'].getUsername()}"></span></li>
  </ul>
</body>
</html>
```
- `user.username` : user 객체의 username이라는 변수를 프로퍼티 접근( = `user.getUsername()`)
- `user['username']` : 위와 동일
- `user.getUsername()` : user 객체의 `getUsername()` 메서드를 직접 호출

- `users[0].username` : List 인덱스의 접근 방법으로 그 뒤로 변수 접근은 위와 동일하다.
- `userMap['userA'].username` : Map에서 Key 값으로 userA를 찾은 뒤, 변수에 접근한다.

#### 지역변수 선언
`th:with`를 사용하면 지역 변수를 선언해서 사용할 수 있다. 다만 해당 문법이 사용된 태그 안에서만 사용이 가능하다.
```html
<h1>지역 변수 - (th:with)</h1>
<div th:with="first=${users[0]}">
 <p>처음 사람의 이름은 <span th:text="${first.username}"></span></p>
</div>
```
- 이 경우에는 `div` 태그 안에서만 사용이 가능하다.

### 유틸리티 객체와 날짜
타임리프는 문자, 숫자, 날짜, URI 등을 편리하게 다루는 다양한 유틸리티 객체들을 제공한다.

#### 타임리프 유틸리티 객체들
- `#message` : 메시지, 국제화 처리
- `#uris` : URI 이스케이프 지원
- `#dates` : java.util.Date 서식 지원
- `#calendars` : java.util.Calendar 서식 지원
- `#temporals` : 자바8 날짜 서식 지원
- `#numbers` : 숫자 서식 지원
- `#strings` : 문자 관련 편의 기능
- `#objects` : 객체 관련 기능 제공
- `#bools` : boolean 관련 기능 제공
- `arrays` : 배열 관련 기능 제공
- `#lists , #sets , #maps` : 컬렉션 관련 기능 제공
- `#ids` : 아이디 처리 관련 기능 제공, 뒤에서 설명

> 타임리프 유틸리티 객체 : https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#expression-utility-objects  
> 유틸리티 객체 예시 : https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#appendix-b-expression-utility-objects

이런 유틸리티의 경우, 필요에 따라 매뉴얼에서 찾아 사용하는게 더욱 효과적이다.

### URL 링크
타임리프에서 URL을 생성할 때는 `@{...}` 문법을 사용하면 된다. 타임리프 속성은 `th:href`이다.
```java
@GetMapping("/link")
public String link(Model model) {
    model.addAttribute("param1", "data1");
    model.addAttribute("param2", "data2");
    
    return "basic/link";
}
```
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>URL 링크</h1>
<ul>
    <li><a th:href="@{/hello}">basic url</a></li>
    <li><a th:href="@{/hello(param1=${param1}, param2=${param2})}">hello query param</a></li>
    <li><a th:href="@{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}">path variable</a></li>
    <li><a th:href="@{/hello/{param1}(param1=${param1}, param2=${param2})}">path variable + query parameter</a></li>
</ul>
</body>
</html>
```
1. `@{/hello}` : `localhost:8080/hello`로 연결된다.
2. `@{/hello(param1=${param1}, param2=${param2})}` : `localhost:8080/hello?param1=data1&param2=data2`로 연결된다.
  - 쿼리 파라미터를 사용해야 할 경우, 소괄호와 함께 `${...}` 문법을 사용한다.
3. `@{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}` : `localhost:8080/hello/data1/data2`로 연결된다.
  - Path Variable의 경우, 기존의 컨트롤러에서 사용했던 것처럼 { }를 활용하며 마지막에 소괄호와 `@{...}` 문법을 통해 바인딩을 한다.
4. `@{/hello/{param1}(param1=${param1}, param2=${param2})}` : `localhost:8080/data1?param2=data`로 연결된다.
  - {...}로 덮인 param1은 Path Variable로 동작하며, param2는 소괄호에 있기 때문에 쿼리 파라미터로 동작한다.

### 리터럴
리터럴은 소스 코드 상에 고정된 값을 말하는 용어이다. `"Hello", 10, 20` 등이 문자 혹은 숫자 리터럴이다.
- 문자 : `hello`
- 숫자 : `10`
- 불린 : `true, false`
- null : `null`

**타임 리프에서 문자 리터럴은 항상 `'(작은 따옴표)`로 감싸야 한다.** ex) `<span th:text="'hello'">`  
만약 문자가 공백없이 쭉 이어진다면 하나의 토큰으로 인지하기 때문에 작은 따옴표를 생략할 수 있다.  
ex) `A-Z , a-z , 0-9 , [] , . , - , _`  
하지만 문자열에 공백이 있을 경우, 반드시 작은 따옴표로 감싸야 한다.  
ex) `<span th:text="hello world!"></span>` -> `<span th:text="'hello world!'"></span>`

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
 <meta charset="UTF-8">
 <title>Title</title>
</head>
<body>
<h1>리터럴</h1>
<ul>
 <!--주의! 다음 주석을 풀면 예외가 발생함-->
<!-- <li>"hello world!" = <span th:text="hello world!"></span></li>-->
 <li>'hello' + ' world!' = <span th:text="'hello' + ' world!'"></span></li>
 <li>'hello world!' = <span th:text="'hello world!'"></span></li>
 <li>'hello ' + ${data} = <span th:text="'hello ' + ${data}"></span></li>
 <li>리터럴 대체 |hello ${data}| = <span th:text="|hello ${data}|"></span></li>
</ul>
</body>
</html>
```
문자끼리 덧셈도 가능하며, 이러한 코드 작성이 번거로울 경우를 대비해 리터럴 대체 문법을 사용할 수 있다.  
`<span th:text="|hello ${data}|">` : 여기서 data는 컨트롤러에서 넘겨준 Model의 속성 이름이다.

### 연산
타임리프 연산은 자바와 크게 다르지 않다. HTML 안에서 사용하기 때문에 HTML 엔티티 사용만 주의하면 된다.
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
 <meta charset="UTF-8">
 <title>Title</title>
</head>
<body>
<ul>
 <li>산술 연산
   <ul>
     <li>10 + 2 = <span th:text="10 + 2"></span></li>
     <li>10 % 2 == 0 = <span th:text="10 % 2 == 0"></span></li>
   </ul>
 </li>
 <li>비교 연산
   <ul>
     <li>1 > 10 = <span th:text="1 &gt; 10"></span></li> <!-- >가 아니라 `&gt`;이다. 렌더링과정에서 >로 보인 것임.  -->
     <li>1 gt 10 = <span th:text="1 gt 10"></span></li>
     <li>1 >= 10 = <span th:text="1 >= 10"></span></li>
     <li>1 ge 10 = <span th:text="1 ge 10"></span></li>
     <li>1 == 10 = <span th:text="1 == 10"></span></li>
     <li>1 != 10 = <span th:text="1 != 10"></span></li>
   </ul>
 </li>
 <li>조건식
   <ul>
      <li>(10 % 2 == 0)? '짝수':'홀수' = <span th:text="(10 % 2 == 0)? '짝수':'홀수'"></span></li>
   </ul>
 </li>
 <li>Elvis 연산자
   <ul>
     <li>${data}?: '데이터가 없습니다.' = <span th:text="${data}?: '데이터가없습니다.'"></span></li>
     <li>${nullData}?: '데이터가 없습니다.' = <span th:text="${nullData}?: '데이터가 없습니다.'"></span></li>
   </ul>
 </li>
 <li>No-Operation
   <ul>
     <li>${data}?: _ = <span th:text="${data}?: _">데이터가 없습니다.</span></li>
     <li>${nullData}?: _ = <span th:text="${nullData}?: _">데이터가 없습니다.</span></li>
   </ul>
 </li>
</ul>
</body>
</html>
```
연산 결과 값을 HTML에 출력하는 것이기 때문에 `th:text` 속성을 사용한다. 단순 연산의 경우, 자바와 동일하나
비교 연산(`<`,`>`)의 경우에는 HTML 엔티티를 사용해야 한다. ex) >가 아니라 `&``gt`;를 사용해야 한다. (> 을 써도 잘 동작하기는 한다.) 조건식도 자바 문법과 동일하다. (? True : False)

Elvis 연산자 : 조건식을 간단화한 버전으로, ? 다음에 오는 값은 조건문이 False일 경우(값이 없을 경우)에 실행되는 문구이다.
위 예시에서 ${data} 값이 있기 때문에 `데이터가 없습니다.`가 출력되지 않고 있는 값이 출력된다. 하지만 ${nullData}의 경우,
값이 없기 때문에 `데이터가 없습니다.`가 출력된다.

No Operation : `_`를 사용하며, 이것이 동작됐을 때는 No Operation, 타임리프가 실행되지 않는 것처럼 동작한다. 즉,
HTML의 내용을 그대로 출력한다. 위 예시에서 ${data}의 경우, 값이 있기 때문에 No Operation이 실행되지 않지만, ${nullData}의 경우,
값이 없기 때문에 No Operation이 실행되어 HTML 태그에 있는 <span> 내용이 그대로 출력된다.
