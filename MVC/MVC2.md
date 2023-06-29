# Contents Of MVC2
- [타임 리프](#타임-리프)
  - [텍스트 - text,utext](#텍스트---text-utext)
  - [변수 - SpringEL](#변수---springel)
  - [유틸리티 객체와 날짜](#유틸리티-객체와-날짜)
  - [URL 링크](#url-링크)
  - [리터럴](#리터럴)
  - [연산](#연산)
  - [속성 값 설정](#속성-값-설정)
  - [반복](#반복)
  - [조건식](#조건부-평가)
  - [주석](#주석)
  - [블록](#블록)
  - [자바스크립트 인라인](#자바스크립트-인라인)

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

### 속성 값 설정
타임리프는 HTML 태그에 `th:*` 속성을 지정하는 방식으로 동작한다. 즉, `*`인 기존 속성을 `th:*` 타임리프 속성으로
대체하는 것이다.(서버에서 렌더링되었을 때) 기존에 있던 속성을 대체하는 것이 일반적이며, 만약 기본 속성이 없는 경우라면 새로 만든다.  
ex) `<input type="text" name="mock" th:name="userA" />` ->(렌더링 후) `<input type="text" name="userA" />`

#### 속성 추가
1. `th:attrappend` : 속성 값의 뒤에 추가한다.
  - `<input type="text" class="text" th:attrappend="class='large'" /><br/>` -> `<input type="text" class="textlarge" /><br/>`
2. `th:attrprepend` : 속성 값의 앞에 추가한다.
  - `<input type="text" class="text" th:attrprepend="class='large'" /><br/>` -> `<input type="text" class="largetext" /><br/>`
3. `th:classappend` : class 속성 값의 뒤에 추가한다. 자동으로 공백이 추가된다.
  - `<input type="text" class="text" th:classappend="large" /><br/>` -> `<input type="text" class="text large" /><br/>`

#### checked 처리
HTML에서는 ` <input type="checkbox" name="active" checked="false" />` 처럼 태그에 `checked` 속성이 있기만 해도
체크 처리가 되어있다. 따라서 `th:checked` 속성을 사용할 경우, `false`라면 `checked` 속성 자체를 제거한다.  
ex) `<input type="checkbox" name="active" th:checked="false" />` ->(렌더링 후) `<input type="checkbox" name="active" />`

### 반복
타임리프에서 태그의 반복 출력은 `th:each`를 사용한다. Model을 통해 받은 여러 값들을 `th:each = 변수 : ${data}` 형식으로
받으며, 자바의 Enhanced For 문과 유사하다.
```html
<table border="1">
 <tr>
   <th>username</th>
   <th>age</th>
 </tr>
 <tr th:each="user : ${users}">
   <td th:text="${user.username}">username</td>
   <td th:text="${user.age}">0</td>
 </tr>
</table>
```
`th:each`는 `List` 뿐만 아니라 배열, `Iterable`, `Enumeration`을 구현한 모든 객체를 반복에 사용할 수 있다.
`Map`도 가능한데, 이 때 변수에 담기는 값은 `Map.Entry`이다.

#### 반복 상태 유지
반복의 두 번째 파라미터를 설정하여 반복되는 변수의 상태를 출력할 수 있다. 두 번째 파라미터는 생략이 가능한데,
`지정한 변수명 + Stat`일 경우, 생략이 가능하다. 즉, `<tr th:each = "user : ${users}">`라면 두 번째 파라미터는
`userStat`으로 생략이 가능하다.(다른 변수로 지정 가능) 이 때, Stat으로 출력할 수 있는 값의 종류는 다음과 같다.
- index : 0부터 시작하는 값
- count : 1부터 시작하는 값
- size : 전체 사이즈
- even , odd : 홀수, 짝수 여부( boolean )
- first , last :처음, 마지막 여부( boolean )
- current : 현재 객체  

`index`나 `count`를 통해 순번을 함께 출력할 수도 있다. 혹은, 데이터의 순서에 따라 로직을 수행할 수 있도록
`even, odd`를 활용할 수도 있다.

### 조건부 평가
타임리프의 조건식 : `if`,`unless`
```html
<tr th:each="user, userStat : ${users}">
   <td th:text="${userStat.count}">1</td>
   <td th:text="${user.username}">username</td>
   <td>
     <span th:text="${user.age}">0</span>
     <span th:text="'미성년자'" th:if="${user.age lt 20}"></span>
     <span th:text="'미성년자'" th:unless="${user.age ge 20}"></span>
   </td>
 </tr>
```
- `if`는 조건이 True일 때, 렌더링하고, `unless`는 조건이 False일 때, 렌더링한다.
- 조건에 맞지 않는 태그는 아예 렌더링이 되지 않고 사라진다.
```html
<tr th:each="user, userStat : ${users}">
   <td th:text="${userStat.count}">1</td>
   <td th:text="${user.username}">username</td>
   <td th:switch="${user.age}">
     <span th:case="10">10살</span>
     <span th:case="20">20살</span>
     <span th:case="*">기타</span>
   </td>
 </tr>
```
조건문에는 switch문 또한 사용이 가능하다. `*`는 만족하는 조건이 없을 때 사용하는 디폴트이다.

### 주석
```html
<h1>1. 표준 HTML 주석</h1>
<!--
<span th:text="${data}">html data</span>
-->

<h1>2. 타임리프 파서 주석</h1>
<!--/* [[${data}]] */-->
<!--/*-->
<span th:text="${data}">html data</span>
<!--*/-->

<h1>3. 타임리프 프로토타입 주석</h1>
<!--/*/
<span th:text="${data}">html data</span>
/*/-->
```
1. 표준 HTML 주석 : 렌더링 시 주석이 소스에 그대로 남아있다.
2. 타임리프 파서 주석 : 기존 HTML 주석에 `/*`,`*/`이 추가된 형태로, 렌더링 후에 소스에도 완전히 사라진다.
즉, 이것이 타임리프의 주석인 것이다.
3. 타임리프 프로토타입 주석 : 렌더링이 되지 않은 채, 웹 브라우저에서 열면 이는 HTML 주석처리가 되어 보이지 않는다.
하지만 렌더링하게 되면 주석처리가 되지 않고 정상 렌더링 된다. 즉, **렌더링 한 경우에만 보이도록 하는 기능이다.**

### 블록
`th:block`은 타임리프의 유일한 자체 태그이다.
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
 <meta charset="UTF-8">
 <title>Title</title>
</head>
<body>
<th:block th:each="user : ${users}">
    <div>
        사용자 이름1 <span th:text="${user.username}"></span>
        사용자 나이1 <span th:text="${user.age}"></span>
    </div>
    <div>
        요약 <span th:text="${user.username} + ' / ' + ${user.age}"></span>
    </div>
</th:block>
</body>
</html>
```
타임리프는 HTML 태그의 속성 값으로 설정하기 때문에 위 처럼 적용해야 하는 태그가 많을 경우, 일일이 속성을 적용하기에
번거롭거나 애매한 경우가 있다. 즉, block 단위에 타임리프 속성을 적용해야 하는 경우에 사용되며, 렌더링 후에 `<block>` 태그는 제거된다.
쉽게 말해, block 단위로 타임리프 값을 적용해야 하는 경우 사용한다.

### 자바스크립트 인라인
`<script>`에서 타임리프를 편리하게 사용할 수 있도록 제공하는 기능이며, `<script th:inline="javascript">`로 사용한다.
```html
<!-- 자바스크립트 인라인 사용 전 -->
<script>
   var username = [[${user.username}]];
   var age = [[${user.age}]];
   //자바스크립트 내추럴 템플릿
   var username2 = /*[[${user.username}]]*/ "test username";
   //객체
   var user = [[${user}]];
</script>
<!-- 자바스크립트 인라인 사용 후 -->
<script th:inline="javascript">
   var username = [[${user.username}]];
   var age = [[${user.age}]];
   //자바스크립트 내추럴 템플릿
   var username2 = /*[[${user.username}]]*/ "test username";
   //객체
   var user = [[${user}]];
</script>
```
1. 인라인이 적용되면 문자에 자동으로 `" "`가 붙는다.
2. 내추럴 템플릿 형태를 유지할 수 있도록 지원한다. 즉, 값을 할당하는 부분을 주석처리하면 렌더링 시 그 부분이 데이터로 바인딩된다.
  - `"test username"` -> `${user.username}`
3. 객체를 할당하게 되면 자동으로 객체를 JSON 타입으로 변환해준다.  
(인라인 사용 전 : 객체의 toString()이 호출된 값, 인라인 사용 후 : JSON 타입의 객체)

#### 자바스크립트 인라인 each
```html
<!-- 자바스크립트 인라인 each -->
<script th:inline="javascript">
   [# th:each="user, stat : ${users}"]
   var user[[${stat.count}]] = [[${user}]];
   [/]
</script>
```
자바스크립트 변수에 반복문을 사용하여 할당도 가능하다. (`[#] ~ [/]`)

### 템플릿 조각
웹 페이지를 개발할 때는 공통 영역이 많이 있다. 홈페이지의 상단부, 하단부, 사이드 영역 등 여러 페이지에서 공동으로
사용하는 경우가 있다. 홈페이지의 구성 요소가 카테고리마다 제각각이면 홈페이지의 통일성이 깨지며, 번잡스러워 보일 수
있기 때문이다. 따라서 타임리프는 공통되는 부분을 하나의 조각으로 관리하여 필요한 부분에 가져다가 쓰는 방식을 채택하였다.
만약 여러 사이트의 홈페이지 하단부(Footer)를 수정해야 한다면 일일이 코드를 수정하지 않고, 사용된 템플릿 조각만 변경시키면
모든 사이트의 하단부가 변경되므로 훨씬 간편하고 쉽다.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<footer th:fragment="copy">
  푸터 자리 입니다.
</footer>
<footer th:fragment="copyParam (param1, param2)">
  <p>파라미터 자리 입니다.</p>
  <p th:text="${param1}"></p>
  <p th:text="${param2}"></p>
</footer>
</body>
</html>
```
이는 템플릿 조각들을 모아놓은 하나의 html 파일이다. 해당 태그에 `th:fragment`가 붙은 경우, 다른 곳에 쓰일 수 있는
템플릿 조각으로 만든다는 뜻이다. 조각에 이름을 정의하면 나중에 사용하는 곳에서 템플릿 조각의 이름을 통해 호출할 수 있다.
만약 파라미터가 필요한 경우에는 이름과 함께 `(변수이름1, 변수이름2)`을 추가한다. (함수 형태)
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <head>
   <meta charset="UTF-8">
   <title>Title</title>
  </head>
  <body>
    <h1>부분 포함</h1>
       <h2>부분 포함 insert</h2>
          <div th:insert="~{template/fragment/footer :: copy}"></div>
       <h2>부분 포함 replace</h2>
          <div th:replace="~{template/fragment/footer :: copy}"></div>
       <h2>부분 포함 단순 표현식</h2>
          <div th:replace="template/fragment/footer :: copy"></div>
       <h1>파라미터 사용</h1>
          <div th:replace="~{template/fragment/footer :: copyParam ('데이터1', '데이터2')}"></div>
  </body>
</html>
```
이는 실제 화면으로 쓰이는 html 파일이다. 여기서 템플릿 조각을 사용하는 부분을 잘 보도록 하자. 템플릿 조각을
불러오기 위해서 `~{경로 :: 템플릿 조각 이름}` 형식으로 사용하고 있다. 즉, `"~{template/fragment/footer :: copy}"`은
`template/fragment/footer` 경로에 있는 `th:fragment="copy"`라는 템플릿 조각을 사용한다는 뜻이다.
통상적으로 `~{경로}`를 사용하지만 템플릿 조각의 코드가 단순하다면 `~{}` 없이 경로를 설정할 수 있다.

- `th:insert` : 이는 해당 태그 내부에 템플릿 조각을 추가한다는 뜻이다.
  - `<div th:insert="~{template/fragment/footer :: copy}">` : `div` 태그 밑에 템플릿 조각인 `footer` 태그가 추가된다.
- `th:replace` : 이는 해당 태그자체를 템플릿 조각으로 바꿔버린다.
  - `<div th:replace="~{template/fragment/footer :: copy}">` : 기존의 `div` 태그가 템플리 조각인 `footer` 태그로 변경된다.
  `th:insert`와 달리 `div` 태그 밑에 `footer`가 존재하지 않는다.
- 파라미터를 사용하면 전달한 값을 통해 동적으로 바인딩이 가능하다.

### 템플릿 레이아웃 1
템플릿 레이아웃은 여러 페이지가 공통으로 사용하는 태그를 만들어 하나의 레이아웃으로 관리하는 것이다. 대신에
템플릿 레이아웃을 사용하는 html 파일에서 특정 값들을 전달함으로써 공통적으로 갖는 부분과 사이트마다 동적으로
정보를 갖게 할 수 있다. **즉, 하나의 레이아웃을 공통으로 사용하되 사이트에 추가될 필요가 있는 부분만 추가하는 것이다.**
이렇게 하면 하나의 레이아웃을 통해 사이트들이 공통적으로 갖는 부분을 하나의 템플릿 레이아웃으로 관리할 수 있다.
```html
<html xmlns:th="http://www.thymeleaf.org">
<head th:fragment="common_header(title,links)">
   <title th:replace="${title}">레이아웃 타이틀</title>
    
   <!-- 공통 -->
   <link rel="stylesheet" type="text/css" media="all" th:href="@{/css/
  awesomeapp.css}">
   <link rel="shortcut icon" th:href="@{/images/favicon.ico}">
   <script type="text/javascript" th:src="@{/sh/scripts/codebase.js}"></script>
   
   <!-- 추가 -->
   <th:block th:replace="${links}" />
</head>
```
이는 템플릿 레이아웃으로 `head` 태그가 하나의 조각으로 있는 상태이다. 파라미터를 받는 조각으로 사용하게 되면
동적으로 내용을 변경할 수 있다. 내용이 추가되는 부분은 `th:replace`가 사용되었으며, 각각엔 받은 파라미터를 바인딩한다.
(바인딩 문법 : `${파라미터}`)
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <head th:replace="template/layout/base :: common_header(~{::title},~{::link})">
     <title>메인 타이틀</title>
     <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}">
     <link rel="stylesheet" th:href="@{/themes/smoothness/jquery-ui.css}">
  </head>
  <body>
  메인 컨텐츠
  </body>
</html>
```
이는 템플릿 레이아웃을 사용하는 쪽이며, `head` 태그를 `th:replace`를 통해 템플릿 레이아웃으로 대체하였다.
이 때 파라미터를 넘겨주는 부분을 잘 살펴보자. `::title`은 현재 html 파일에 있는 `title` 태그를 의미한다.
`<title>메인 타이틀</title>`을 템플릿 레이아웃에 넘겨준 것이다. 그러면 템플릿 레이아웃에서 입력받은 파라미터로
바인딩된다. 링크 또한 마찬가지이다. 템플릿 조각을 사용하는 문법은 기본적으로 `~{...}`을 사용한다. 따라서 파라미터를 넘겨주는 경우에도
`~{::title}`이라고 사용한다. 파라미터 입력받는 조각에서의 사용법과는 살짝 다르다는 것을 주의하자.
```html
<!DOCTYPE html>
<html>
  <head>
  <title>메인 타이틀</title>
    
  <!-- 공통 -->
  <link rel="stylesheet" type="text/css" media="all" href="/css/awesomeapp.css">
  <link rel="shortcut icon" href="/images/favicon.ico">
  <script type="text/javascript" src="/sh/scripts/codebase.js"></script>
    
  <!-- 추가 -->
  <link rel="stylesheet" href="/css/bootstrap.min.css">
  <link rel="stylesheet" href="/themes/smoothness/jquery-ui.css">
    
  </head>
  <body>
  메인 컨텐츠
  </body>
</html>
```
실제 렌더링된 코드이며, 템플릿 레이아웃에서 지정한 공통부분 이외에 `th:replace`로 설정했던 부분들이 입력받은 파라미터인
`title` 태그와 `link` 태그로 대체된 것을 확인할 수 있다. 템플릿 레이아웃은 템플릿 조각을 보다 적극적으로 사용하는
방법이다. 축구 웹 사이트에서 전체적인 틀을 동일하되 보여주는 결과표만 다르게 해야할 때 이러한 내용을 사용할 수 있을 것 같다.

### 템플릿 레이아웃 2
템플릿 레이아웃을 단순히 특정 태그에만 적용하는 것이 아니라 html 파일 전체에 적용할 수도 있다.
```html
<!DOCTYPE html>
<html th:fragment="layout (title, content)" xmlns:th="http://www.thymeleaf.org">
  <head>
   <title th:replace="${title}">레이아웃 타이틀</title>
  </head>
  <body>
    <h1>레이아웃 H1</h1>
    <div th:replace="${content}">
     <p>레이아웃 컨텐츠</p>
    </div>
    
    <footer>
     레이아웃 푸터
    </footer>
  </body>
</html>
```
이처럼 `html` 태그에 `th:fragment`로 조각을 설정할 수 있다. 이러면 사용하는 쪽에서 하나의 html 파일 전체를
템플릿 레이아웃으로 대체하게 되는 것이다. 여기서도 마찬가지로 파라미터를 입력받는데, 입력받은 파라미터가 `title`과
`div` 즉, 제목과 본문 내용을 동적으로 바인딩함을 알 수 있다. 그 이외에 푸터는 동일하게 가져간다.
```html
<!DOCTYPE html>
<html th:replace="~{template/layoutExtend/layoutFile :: layout(~{::title}, ~{::section})}" xmlns:th="http://www.thymeleaf.org">
  <head>
   <title>메인 페이지 타이틀</title>
  </head>
  <body>
    <section>
     <p>메인 페이지 컨텐츠</p>
     <div>메인 페이지 포함 내용</div>
    </section>
  </body>
</html>
```
실제 사용하는 곳에서 `html` 태그 자체를 템플릿 레이아웃으로 대체하였다. 여기서도 마찬가지로 파라미터를 넘겨주게 되는데
현재 html 파일에 있는 `title` 태그와 `section` 태그를 넘겨주었다. 그러면 이는 각각 템플릿 레이아웃의 `title`과
`content`로 매핑된다.
```html
<!DOCTYPE html>
<html>
  <head>
    <!-- 추가된 부분 : 템플릿 레이아웃의 title 태그가 대체됨 -->
    <title>메인 페이지 타이틀</title>
  </head>
  <body>
    <h1>레이아웃 H1</h1>
    
    <!-- 추가된 부분 : 템플릿 레이아웃의 div 태그가 대체됨-->
    <section>
      <p>메인 페이지 컨텐츠</p>
      <div>메인 페이지 포함 내용</div>
    </section>
    
    <footer>
    레이아웃 푸터
    </footer>
  </body>
</html>
```
주의해야 할 점은 템플릿 레이아웃을 사용했다면 일단 전체적인 틀은 템플릿 레이아웃과 동일하다. 즉, 사용하는 쪽에서
하는 것은 바인딩에 필요한 태그만 생성해 놓기만 하면 되는 것이다. 위의 경우도 `title` 태그와 `section` 태그를
생성해놓았고 이것이 템플릿 레이아웃의 형태에서 바인딩된 것이다. **전체적인 틀은 템플릿 레이아웃과 동일함을 주의하자!**







