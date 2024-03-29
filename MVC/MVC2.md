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
- [타임리프 - 스프링 통합과 폼](#타임리프---스프링-통합과-폼)
  - [스프링 통합으로 추가되는 기능들](#스프링-통합으로-추가되는-기능들)
  - [입력 폼 처리](#입력-폼-처리)
  - [체크박스 - 단일1](#체크박스---단일-1)
  - [체크박스 - 단일2](#체크박스---단일-2)
  - [체크박스 - 멀티](#체크박스---멀티)
  - [라디오 버튼](#라디오-버튼)
  - [셀렉트 박스](#셀렉트-박스)
- [메시지, 국제화](#메시지-국제화)
  - [메시지](#메시지)
  - [국제화](#국제화)
  - [스프링 메시지 소스 설정](#스프링-메시지-소스-설정)
  - [스프링 메시지 소스 사용](#스프링-메시지-소스-사용)
  - [실제 웹 어플리케이션에 메시지 적용](#실제-웹-어플리케이션에-메시지-적용)
  - [실제 웹 어플리케이션에 국제화 적용](#웹-애플리케이션에-국제화-적용하기)
- [검증1 - Validation](#검증-1---validation)
  - [검증이 필요한 이유](#검증이-필요한-이유)
  - [검증 처리 로직 V1](#검증-처리-로직-v1)
  - [검증 처리 로직 V2](#검증-처리-로직-v2)
  - [BindingResult](#bindingresult)
  - [사용자 입력 오류 메세지를 화면에 남기는 법](#사용자-입력-오류-메세지를-화면에-남기는-법---fielderror-objecterror)
  - [오류 코드와 메시지 처리1](#오류-코드와-메시지-처리1)
  - [오류 코드와 메시지 처리2](#오류-코드와-메시지-처리2)
  - [오류 코드와 메시지 처리3](#오류-코드와-메시지-처리3)
  - [오류 코드와 메시지 처리4](#오류-코드와-메시지-처리4)
  - [오류 코드와 메시지 처리5](#오류-코드와-메시지-처리5)
  - [오류 코드와 메시지 처리6](#오류-코드와-메시지-처리6)
  - [오류 코드와 메시지 처리 정리](#오류-코드와-메시지-처리-정리)
  - [Validator 분리1](#validator-분리1)
  - [Validator 분리2](#validator-분리2)
- [검증2 - Bean Validation](#검증-2---bean-validation)
  - [Bean Validation이란?](#bean-validation이란)
  - [Bean Validation - 스프링 적용](#bean-validation---스프링-적용)
  - [Bean Validation - 에러 코드](#bean-validation---에러-코드)
  - [Bean Validation - 오브젝트 오류](#bean-validation---오브젝트-오류)
  - [Bean Validation - 여러 검증사항 적용](#bean-validation-여러-검증사항-적용)
  - [Bean Validation - 메시지 바디에 적용](#bean-validation---메시지-바디에-적용)
- [단축키](#단축키-정보)

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

# 타임리프 - 스프링 통합과 폼
타임리프는 크게 두 가지 매뉴얼을 제공한다.
- 기본 메뉴얼 : https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html
- 스프링 통합 메뉴얼 : https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html  

타임리프는 스프링 없이도 동작하지만, 스프링과 통합을 위한 다양한 기능을 편리하게 제공한다. 그리고 이런 부분은
스프링 백엔드 개발자 입장에서 타임리프를 선택해야 하는 이유이기도 하다.

### 스프링 통합으로 추가되는 기능들
- Form 파일을 편리하게 관리할 수 있는 추가 속성을 지원한다.
  - `th:object`, `th:field` 등
- Form 컴포넌트 기능
  - checkbox, radio, buttons, List 등을 편리하게 사용할 수 있는 기능을 지원한다.
- 스프링의 메시지, 국제화 기능의 편리한 통합
- 스프링의 검증, 오류 처리 통합
- 스프링의 변환 서비스 통합(ConversionService)

이처럼 스프링과 통합된 타임리프를 사용하기 위해서는 타임리프 템플릿 엔진을 스프링 빈에 등록하고, 타임리프용 뷰 리졸버도
스프링 빈으로 등록해야 한다. 타임리프 자체는 스프링 없이도 동작할 수 있기 때문에 스프링과 통합된 다양한 기능을 활용하기 위해서는
당연히 템플릿 엔진과 뷰 리졸버 객체를 스프링 빈에 등록해야하는 절차가 필요하다.  
- https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html#the-springstandard-dialect
- https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html#views-and-view-resolvers

링크에서 보다시피 절차가 복잡하고 방대하다. 하지만 스프링 부트를 사용하면 이러한 것들을 다 자동으로 설정해준다.
(스프링 부트를 사용하는 이유 중 하나 : 편리함) `build.gradle`에 `implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'`
이 있으면 Gradle에서 타임리프와 관련된 라이브러리를 다운로드 받으며, 스프링 부트는 앞서 설명한 타임리프와 관련된
설정용 스프링 빈을 자동으로 등록해준다.

### 입력 폼 처리
`th:object`를 사용하면 모델로 넘어온 객체에 접근할 수 있다.(커맨드 객체) 그리고 여기에 있는 객체 변수를
접근하기 위해서는 `${객체.변수}` 혹은 `*{변수}(선택변수)`를 사용한다. 이는 보통 `th:field`와 함께 사용되며,
이렇게 했을 때, html 태그의 속성인 `id`,`name`,`value`를 자동으로 생성해준다. 이는 단순히 편리한 기능이기만 한 것 같지만
이후 검증, Validation에서 유용하게 사용된다.
```java
@GetMapping("/add")
public String addForm(Model model) {
     model.addAttribute("item", new Item());
     return "form/addForm";
}
```
```html
<form action="item.html" th:action th:object="${item}" method="post">
 <div>
   <label for="itemName">상품명</label>
   <input type="text" id="itemName" th:field="*{itemName}" class="formcontrol" placeholder="이름을 입력하세요">
 </div>
 <div>
   <label for="price">가격</label>
   <input type="text" id="price" th:field="*{price}" class="form-control" placeholder="가격을 입력하세요">
 </div>
 <div>
   <label for="quantity">수량</label>
   <input type="text" id="quantity" th:field="*{quantity}" class="formcontrol" placeholder="수량을 입력하세요">
 </div>
```
먼저 컨트롤러에서 Model을 넘겨준다. 그리고 이를 `Form` 태그에서 활용할 때는 `th:object` 속성을 통해 객체를 가져온다.
그 다음 `id`와 `name`을 지정해야 하는 태그에 `th:field="*{변수}`를 사용한다. 이 때 컨트롤러에서 단순히 빈 객체를
넘겨줘야 하지만 이는 큰 비용이 들지 않는다. `th:object`와 `th:field`를 사용했을 때의 장점 중 하나는 `name` 이나 `id` 속성을
잘못 입력했을 때 오류를 방지할 수 있다는 것이다.

```html
렌더링 전
<input type="text" id="itemName" th:field="*{itemName}" class="form-control" placeholder="이름을 입력하세요">

렌더링 후
<input type="text" id="itemName" class="form-control" placeholder="이름을 입력하세요" name="itemName" value="">
```
렌더링 결과에서 알 수 있듯이 `name` 및 `value` 속성이 추가되었다. 물론 `id`도 생략이 가능하다

```html
수정 HTML 파일
<form action="item.html" th:action th:object="${item}" method="post">
 <div>
   <label for="id">상품 ID</label>
   <input type="text" id="id" th:field="*{id}" class="form-control" readonly>
 </div>
 <div>
   <label for="itemName">상품명</label>
   <input type="text" id="itemName" th:field="*{itemName}" class="formcontrol">
 </div>
 <div>
   <label for="price">가격</label>
   <input type="text" id="price" th:field="*{price}" class="form-control">
 </div>
 <div>
   <label for="quantity">수량</label>
   <input type="text" id="quantity" th:field="*{quantity}" class="formcontrol">
 </div>
```
수정 HTML 파일의 경우, `id`, `name`, `value` 등 신경써야 하는 속성들이 많은데 이를 하나의 속성 값으로 대체할 수 있다.

### 체크박스 - 단일 1
```html
<!-- single checkbox -->
<div>판매 여부</div>
<div>
 <div class="form-check">
   <input type="checkbox" id="open" name="open" class="form-check-input">
   <label for="open" class="form-check-label">판매 오픈</label>
 </div>
</div>
```
```java
@PostMapping("/add")
public String addItem(Item item, RedirectAttributes redirectAttributes) {
   log.info("item.open={}", item.getOpen());
   ...
}
```
-> `@Slf4j` 어노테이션을 통해 로그 찍는 방법  
HTML Form에서 체크 박스를 체크하면 `open=on`이라는 값이 넘어간다. 그리고 스프링은 이를 `true` 타입으로 변환한다.
(스프링 타입 컨버터 기능) 따라서 로그를 출력했을 때, True or Null이 찍힌다.

**만약 체크 박스를 체크하지 않는다면** HTTP 메시지 바디에 `open` 필드가 포함되지 않는다. 즉, 서버 측으로 전송자체가
안된다. 따라서 서버에서는 값이 없다고 판단하기 때문에 `null`을 출력하는 것이다. 여기서 발생하는 문제점은 클라이언트에서
서버 측으로 값 자체를 보내지 않는다는 것이다. 체크가 안되어 있다면 체크가 안되었다는 정보라도 보내줘야 나중에 수정할 일이 생겼을 때
핸들링이 가능하기 때문이다. 따라서 스프링 MVC는 이 문제를 해결하기 위해 히든 필드인 `_open`을 사용한다. 이는
체크 여부와 관계없이 항상 전송된다.

- HTML Form 체크표시 : 클라이언트는 `open`과 `_open` 필드를 전송 - 서버에서 체크 판단
- HTML Form 체크표시 X : 클라이언트는 `_open` 필드만 전송 - 서버에서 체크 안함을 판단

#### 체크 해제를 인식하기 위한 히든 필드
`<input type="hidden" name="_open" value="on"/>`
```html
<!-- single checkbox -->
<div>판매 여부</div>
    <div>
        <div class="form-check">
         <input type="checkbox" id="open" name="open" class="form-check-input">
         <input type="hidden" name="_open" value="on"/> <!-- 히든 필드 추가 -->
         <label for="open" class="form-check-label">판매 오픈</label>
    </div>
</div>
```
1. 체크박스 체크 시
  - `open=on & _open = on`이 넘어오고, 스프링 MVC가 `open`에 값이 있다고 판단하며, `_open`은 무시한다.
  따라서 로그를 찍었을 때 true가 나온다.
2. 체크박스 미체크 시
  - `_open = on`이 넘어고, 스프링 MVC는 `_open`만 있는 것을 확인하고 `open` 값이 없다고 판단한다.
따라서 이 때는 null이 아닌 false를 출력한다.

** `_open`은 히든 필드로써 HTML 파일에 영향을 미치지 않는다. 단지 스프링 MVC가 체크 박스를 판단할 수 있도록
만든 일종의 장치에 불과하다.**

### 체크박스 - 단일 2
타임리프를 사용하면 이렇게 히든필드를 일일이 지정하는 번거로움을 피할 수 있다. 타임리프가 제공하는 폼 기능을
사용하면 이러한 부분을 자동으로 처리할 수 있다. 즉, 렌더링 시 히든필드를 자동으로 생성해준다는 뜻이다.
```html
<!-- single checkbox -->
<div>판매 여부</div>
<div>
   <div class="form-check">
     <input type="checkbox" id="open" th:field="*{open}" class="form-check-input">
     <label for="open" class="form-check-label">판매 오픈</label>
   </div>
</div>
```
- `th:field`를 통해 `open` 필드를 자동으로 생성할 수 있다. 이 때 식별자 `*{...}`를 쓸 수 있는건
form 태그에 `th:object`가 지정되어 있기 때문에 가능한 것이다. 만약 없다면 `${item.open}`을 사용해야 한다.
```html
<!-- single checkbox -->
<div>판매 여부</div>
<div>
  <div class="form-check">
    <input type="checkbox" id="open" class="form-check-input" name="open" value="true">
    <input type="hidden" name="_open" value="on"/>
    <label for="open" class="form-check-label">판매 오픈</label>
  </div>
</div>
```
렌더링 후 HTML 결과 파일이다. 보다시피 히든 타입의 `input` 태그가 생성되었으며 `_open` 필드가 자동으로 생성되어 있다.
```html
<!-- single checkbox -->
<div>판매 여부</div>
<div>
 <div class="form-check">
   <input type="checkbox" id="open" th:field="${item.open}" class="form-check-input" disabled>
   <label for="open" class="form-check-label">판매 오픈</label>
 </div>
</div>
```
- 만약, HTML 상세 페이지에서 체크 박스가 수정되지 않도록 하려면 체크 박스에 `disabled` 속성을 추가하면 된다.
```html
<!-- single checkbox -->
<div class="form-check">
 <input type="checkbox" id="open" class="form-check-input" disabled name="open" value="true" checked="checked">
 <label for="open" class="form-check-label">판매 오픈</label>
</div>
```
렌더링 후 HTML 결과 파일이다.(페이지 상세 화면) 체크가 되어 있으면 `checked` 속성이 추가되어 있다.
이 또한 `th:field`를 사용했을 때, `open` 값을 판단하여 true인 경우, 자동으로 체크 처리가 된다.

### 체크박스 - 멀티
체크 박스가 여러 개인 상태에서, 하나 이상을 체크할 수 있도록 해보자.

```java
@ModelAttribute("regions")
public Map<String, String> regions() {
    Map<String, String> regions = new LinkedHashMap<>();
    
    regions.put("SEOUL", "서울");
    regions.put("BUSAN", "부산");
    regions.put("JEJU", "제주");
    
    return regions;
}
```
먼저 컨트롤러에 `@ModelAttribute`를 사용하여 모델을 추가하면, 컨트롤러가 호출될 때마다 자동으로 모델에 담기게 할 수 있다.
```html
<!-- multi checkbox -->
<div>
 <div>등록 지역</div>
  
 <div th:each="region : ${regions}" class="form-check form-check-inline">
    <input type="checkbox" th:field="*{regions}" th:value="${region.key}" class="form-check-input">
    <label th:for="${#ids.prev('regions')}" th:text="${region.value}" class="form-check-label">서울</label>
 </div>
</div>
```
체크박스가 여러개이기 때문에 `th:each` 반복문을 사용하였다. 이 때 `${regions}`는 컨트롤러에서 추가한 Map 형태의
모델이다. 마찬가지로 체크박스이기 떄문에 `th:field`를 통해 자동으로 `open` 필드가 생성되도록 하였다.
그리고 **각각의 체크박스는 name이 같을 수는 있어도, id는 서로 같으면 안된다.** 따라서 반복문을 사용했을 때,
자동으로 id 값이 구별되도록 하기 위해선 `th:for="${#ids.prev('regions')}"`을 사용한다. 이는 id에 regions를 붙이고 뒤에
1,2,3 이런식으로 숫자를 부여한다는 뜻이다.
```html
 <!-- multi checkbox -->
        <div>
            <div>등록 지역</div>
          
            <div class="form-check form-check-inline">
                <input type="checkbox" value="SEOUL"
                       class="form-check-input" id="regions1" name="regions"><input type="hidden" name="_regions" value="on"/>
                <label for="regions1"
                       class="form-check-label">서울</label>
            </div>
            <div class="form-check form-check-inline">
                <input type="checkbox" value="BUSAN"
                       class="form-check-input" id="regions2" name="regions"><input type="hidden" name="_regions" value="on"/>
                <label for="regions2"
                       class="form-check-label">부산</label>
            </div>
            <div class="form-check form-check-inline">
                <input type="checkbox" value="JEJU"
                       class="form-check-input" id="regions3" name="regions"><input type="hidden" name="_regions" value="on"/>
                <label for="regions3"
                       class="form-check-label">제주</label>
            </div>
        </div>
```
렌더링 후 HTML 결과 화면이다. `th:field`를 반복문에 활용하면 id 값을 자동으로 겹치지 않도록 생성한다. 따라서 `label` 태그에서
`for` 속성 값으로 `input` 태그에 맞는 `id` 값을 지정해줘야 한다. (그래야 이름을 눌렀을 때도 체크가 된다.) 따라서
자동으로 생성된 `region + 숫자`와 매칭시키기 위해서 `label` 태그의 `for` 속성을 타임리프로 지정한 것이다.
따라서 `th:for`를 사용하였고, id를 동적으로 바인딩 시키는 `${#ids.prev('regions')}`를 사용하였다.
> `${#ids.next('regions')}`를 사용하게 되면 해당 라벨을 눌렀을 때 오른쪽에 있는 체크박스를 체크하게 된다. 즉, prev와 next의 구분은
라벨과 체크박스의 위치에 따라 결정된다.
> prev(라벨의 왼쪽 가리킴) : 체크박스 라벨, next(라벨의 오른쪽 가리킴) : 라벨 체크박스
> prev나 next나 동적으로 생성되는 id 값은 동일하다 `region + 숫자`

### 라디오 버튼
라디오 버튼은 여러 선택지 중에서 하나를 선택할 수 있는 버튼 기능이다. 이 경우의 자바의 ENUM 클래스를 활용한다.
```java
public enum ItemType {

  BOOK("도서"), FOOD("음식"), ETC("기타");

  private final String description;

  ItemType(String description) {
    this.description = description;
  }

  // Getter가 필요한 이유 : 타임리프에서 해당 description 변수에 접근하는 것은 프로퍼티 접근법이기
  // 때문에 실제로 get 함수를 호출하기 때문이다. 따라서 getter가 정의되어 있지 않으면 오류 발생
  public String getDescription() {
    return description;
  }
}

@ModelAttribute("itemTypes")
public ItemType[] itemTypes() {
 return ItemType.values();
}
```
모델에 ENUM의 값을 리스트로 반환하면, 타임리프를 통해 가져다 쓸 수 있다.

```
item.itemType=FOOD: 값이 있을 때
item.itemType=null: 값이 없을 때
```
라디오 버튼은 체크박스와 달리 이미 선택된 것이 있다면 수정할 때도 하나가 선택되어야 한다. 따라서
별도의 히든 필드가 필요하지 않는다.
```html
<!-- radio button -->
<div>
 <div>상품 종류</div>
   <div th:each="type : ${itemTypes}" class="form-check form-check-inline">
     <input type="radio" th:field="${item.itemType}" th:value="${type.name()}" class="form-check-input" disabled>
     <label th:for="${#ids.prev('itemType')}" th:text="${type.description}" class="form-check-label"> BOOK </label>
   </div>
</div>
```
- `th:field`를 사용하면 id와 name을 자동으로 생성할 뿐만 아니라, 모델의 값과 HTTP 요청 메시지 값을 비교하여 실제로 있는 값에
자동으로 `checked` 혹은 `selected`를 추가해준다.

### 셀렉트 박스
```java
@ModelAttribute("deliveryCodes")
public List<DeliveryCode> deliveryCodes() {
   List<DeliveryCode> deliveryCodes = new ArrayList<>();
   deliveryCodes.add(new DeliveryCode("FAST", "빠른 배송"));
   deliveryCodes.add(new DeliveryCode("NORMAL", "일반 배송"));
   deliveryCodes.add(new DeliveryCode("SLOW", "느린 배송"));
   
   return deliveryCodes;
}
```
이 메서드는 컨트롤러가 호출될 때마다 항상 호출이된다. 따라서 이 값들을 따로 static으로 생성한 뒤 재사용하는 것도 하나의 방법이다.
```html
<!-- SELECT -->
<div>
 <div>배송 방식</div>
 <select th:field="*{deliveryCode}" class="form-select">
   <option value="">==배송 방식 선택==</option>
   <option th:each="deliveryCode : ${deliveryCodes}" th:value="${deliveryCode.code}" th:text="${deliveryCode.displayName}">FAST</option>  
 </select>
</div>
```
- `th:field`를 사용하면 id와 name을 자동으로 생성할 뿐만 아니라, 모델의 값과 HTTP 요청 메시지 값을 비교하여 실제로 있는 값에
  자동으로 `checked` 혹은 `selected`를 추가해준다.

# 메시지, 국제화
### 메시지
메시지란 메시지를 보여주는 것은 아니고, html에서 하드코딩된 값들을 하나의 변수로 관리하는 것이다.
그러면 나중에 값을 수정해야 할 경우, 일일이 수정하지 않아도 된다. `messages.properties`라는 파일을 사용한다.

`<label for="itemName" th:text="#{item.itemName}"></label>` 이런 식으로 데이터의 key 값을 사용한다.
### 국제화
국제화는 메시지에서 더 나아간 개념이다. 나라마다 언어가 다르게 세팅되어야 할 경우 이 기능을 사용한다. 즉,
`messages.properties`을 나라별로 관리하는 것이다. ex) `messages_en.properties`, `messages_ko.properties`
### 스프링 메시지 소스 설정
메시지 관리 기능을 사용하려면 스프링이 제공하는 `MessageSource`를 스프링 빈으로 등록해야 한다.
```java
@Bean
public MessageSource messageSource() {
 ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
 messageSource.setBasenames("messages", "errors");
 messageSource.setDefaultEncoding("utf-8");
 return messageSource;
}
```
- `basenames` : 설정파일의 이름을 지정한다.
  - basename이 messages라면 설정파일은 `messages.properties`이다.
  - 만약, 국제화 기능을 추가한다면 basename 옆에 언어 정보를 추가한다.
    - `messages_en.properties`,`messages_ko.properties` 등
  - 파일의 위치는 `/resources/basename.properties`이다.

스프링 부트를 사용한다면 메시지 기능이 자동으로 설정되어 있다. 기본 설정 값은
`spring.messages.basename=messages`이기 때문에 `messages.properties` 파일을 만들기만 하면 된다.
스프링 사이트에서 `properties` 문서를 살펴보면 다양한 세팅이 가능하다.
```
/resources/messages.properties
hello=안녕
hello.name=안녕 {0}

/resources/messages_en.properties
hello=hello
hello.name=hello {0}
```
이처럼 Key-Value 형태로 값을 저장하게 된다.

### 스프링 메시지 소스 사용
```java
public interface MessageSource {
    String getMessage(String code, @Nullable Object[] args, @Nullable String defaultMessage, Locale locale);
    String getMessage(String code, @Nullable Object[] args, Locale locale) throws NoSuchMessageException;
```
실제 MessageSource 인터페이스의 메서드를 살펴보면 일부 파라미터를 통해 메시지를 가져오는 것을 알 수 있다.

```java
@SpringBootTest
public class MessageSourceTest {
   @Autowired
   MessageSource ms;
   
   @Test
   void helloMessage() {
     String result = ms.getMessage("hello", null, null);
     assertThat(result).isEqualTo("안녕");
   }
}
```
Locale 정보가 없을 경우, basename에서 설정한 기본 이름 메시지 파일을 조회한다.

```java
@Test
void notFoundMessageCode() {
 assertThatThrownBy(() -> ms.getMessage("no_code", null, null))
 .isInstanceOf(NoSuchMessageException.class);
}
@Test
void notFoundMessageCodeDefaultMessage() {
 String result = ms.getMessage("no_code", null, "기본 메시지", null);
 assertThat(result).isEqualTo("기본 메시지");
}
```
- 메시지가 없는 경우에는 `NoSuchMessageException`이 발생한다.
- 메시지가 없더라도, 기본 메시지(`defaultMessage`)를 사용하면 기본 메시지가 반환된다.

### 실제 웹 어플리케이션에 메시지 적용
`properties` 파일에 메시지를 만든 다음 타임리프를 통해 적용하려면 `#{...}`을 사용하면 된다.
```messages.properties
label.item=상품
label.item.id=상품 ID
label.item.itemName=상품명
label.item.price=가격
label.item.quantity=수량

page.items=상품 목록
page.item=상품 상세
page.addItem=상품 등록
page.updateItem=상품 수정

button.save=저장
button.cancel=취소
```
파일은 Key-Value 형식으로 작성한다. Properties 파일은 자바에서 환경변수를 읽어올 수 있도록 하는 파일 확장자이다.
```
<div th:text="#{label.item}"></h2> -> <div>상품</h2>
```
이처럼 `th:text`를 통해 타임리프 메시지를 적용할 수 있다. 파일에서 정의한 변수명(Key)을 그대로 가져와서 사용하며
실제로 출력되는 값은 Value 이다.

만약, 파라미터가 있는 경우에는 다음과 같이 사용할 수 있다.
```
hello.name = 안녕 {0} -> <p th:text="#{hello.name(${item.itemName})}"></p>
```
{0} 안에 입력한 파라미터 값이 들어가게 된다.

### 웹 애플리케이션에 국제화 적용하기
만약 웹 브라우저에서 HTTP 요청 헤더의 `Accept-Language` 필드에서 우선순위가 `en`이 높다면
메시지 파일이 `basename_en.properties`인 것을 사용한다.

# 검증 1 - Validation

### 검증이 필요한 이유
지금까지 만든 웹 애플리케이션은 오류가 발생했을 때 바로 `WhilteLabel Error` 페이지를 보여주면서 끝난다.
만약 사용자가 여러 값들을 입력한 뒤, 전송을 눌렀을 때 어떠한 것이 오류인지 보여주지 않고 단순히 에러 페이지를
보여주고 새로고침했을 때 값들이 다 초기화가 된다면 사용자는 싫증이 나서 해당 웹 서비스를 이용하지 않을 것이다.
즉, 어떠한 부분이 문제가 있는지 확인하는 절차가 필요하며 이를 클라이언트에게 전달하는 것 또한 중요하다.

**컨트롤러의 중요한 역할 중 하나는 HTTP 요청이 정상인지 검증하는 것이다.** 정상 로직을 개발하는 것보다 이러한
검증 로직을 개발하는 것이 더욱 어렵고 중요한 작업일 수 있다.

### 검증 처리 로직 V1
![검증 성공](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/82c0c371-0be2-4de9-8659-30f97d990310)

![검증 실패](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/0ad2236e-7390-461d-be69-b9687728bc3c)

검증이 실패했을 경우, 실패한 값을 그대로 모델에 담아 다시 View에 전달해야 한다. 따라서 이때는 PRG 패턴이 사용되지 않는다.

```java
@PostMapping("/add")
public String addItem(@ModelAttribute Item item, RedirectAttributes redirectAttributes, Model model) {
 
 //검증 오류 결과를 보관
 Map<String, String> errors = new HashMap<>();
 //검증 로직
 if (!StringUtils.hasText(item.getItemName())) {
 errors.put("itemName", "상품 이름은 필수입니다.");
 }
 if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() >
1000000) {
 errors.put("price", "가격은 1,000 ~ 1,000,000 까지 허용합니다.");
 }
 if (item.getQuantity() == null || item.getQuantity() >= 9999) {
 errors.put("quantity", "수량은 최대 9,999 까지 허용합니다.");
 }
 //특정 필드가 아닌 복합 룰 검증
 if (item.getPrice() != null && item.getQuantity() != null) {
 int resultPrice = item.getPrice() * item.getQuantity();
 if (resultPrice < 10000) {
 errors.put("globalError", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 
현재 값 = " + resultPrice);
 }
 }
 //검증에 실패하면 다시 입력 폼으로
 if (!errors.isEmpty()) {
 model.addAttribute("errors", errors);
 return "validation/v1/addForm";
 }
 //성공 로직
 Item savedItem = itemRepository.save(item);
 redirectAttributes.addAttribute("itemId", savedItem.getId());
 redirectAttributes.addAttribute("status", true);
 return "redirect:/validation/v1/items/{itemId}";
}
```
클라이언트의 요청 값을 컨트롤러에서 직접 검증하고 성공과 실패시 각각 다른 로직을 수행하는 것을 알 수 있다.
어떠한 에러가 났는지를 확인할 수 있도록 필드를 설정하였고 이를 Map 타입으로 관리하여 Model에 넘겨준다. 그러면
이를 View에서 꺼내 오류관련 메시지를 출력하면 된다.

만약 특정 필드의 오류가 아닌 경우엔 `globalError`라는 Key를 사용한다.

검증이 실패하게 되면 다시 입력 폼을 보여주게 된다. 따라서 사용자는 검증 실패 시 계속 입력 폼을 응답으로 받게 된다.
기존의 PRG 패턴의 경우 에러 페이지를 보여주게 되면서 새로고침시 입력했던 값이 다 사라진다는 단점이 있었다.
이렇게 검증을 사용하면 입력한 값을 그대로 유지한 채 에러 내용을 보여줄 수 있다.

```html
<form action="item.html" th:action th:object="${item}" method="post">
 <div th:if="${errors?.containsKey('globalError')}">
    <p class="field-error" th:text="${errors['globalError']}">전체 오류 메시지</p>
 </div>
    ...
</form>
```
if문을 사용하여, errors라는 이름의 모델 값이 있을 경우, 오류 페이지를 추가하는 방법이다.
이 때, `?`를 사용한 이유는 errors가 아예 존재하지 않는 null일 수도 있기 때문이다. (GET으로 등록 폼을
조회했을 때는 errors라는 hashMap이 생성조차 되지 않았기 때문에 null이다.) 따라서 그냥 null에 `.containsKey()`를
접근하게 되면 `NullPointerException`이 발생하게 된다. 즉. `?.`을 사용하면 해당 객체가 null일 경우 전체를 null로
반환하게 된다. 그러면 타임리프 문법인 `th:if` 자체가 동작하지 않게 된다.
> Safe Navigation Operator : https://docs.spring.io/spring-framework/reference/core/expressions/language-ref/operator-safe-navigation.html

### 필드 오류 처리
```html
<input type="text" id="itemName" th:field="*{itemName}"
       th:class="${errors?.containsKey('itemName')} ? 'form-control field-error' : 'form-control'"
       class="form-control" placeholder="이름을 입력하세요">

<input type="text" th:classappend="${errors?.containsKey('itemName')} ? 'field-error' : _" class="form-control">
```
errors에 필드가 존재한다는 것은 에러가 발생했다는 뜻이다. 따라서 key를 조회하는 `containsKey()`를 사용하였고,
만약 해당 Key가 있을 때 클래스를 추가하는 방법이다. 이를 사용하면 if문 보다 간결하게 사용할 수 있다. `_ (No-Operation)`은
아무것도 하지 않는다는 뜻이다.

#### 장점
- 검증 오류가 발생하면 입력 폼을 다시 보여준다.
- 검증 오류들을 고객에게 친절하게 안내해서 다시 입력할 수 있게 한다.
- 검증 오류가 발생해도 고객이 입력한 데이터가 유지된다.

#### 단점
- 뷰 템플릿에서 중복되는 코드가 많다.
- 잘못된 타입이 들어왔을 경우, 검증을 하지 못한다.
- 숫자 입력란에 문자가 들어왔을 경우, 이를 그대로 저장한 상태로 고객에게 보여주지 못한다.

### 검증 처리 로직 V2
스프링이 제공하는 검증  오류 처리 방법을 사용하면 보다 간편하게 작성할 수 있다. 여기서 핵심은 **BindingResult**이다.
```java
@PostMapping("/add")
public String addItemV1(@ModelAttribute Item item, BindingResult bindingResult,RedirectAttributes redirectAttributes) {
 
    if (!StringUtils.hasText(item.getItemName())) {
 bindingResult.addError(new FieldError("item", "itemName", "상품 이름은
필수입니다."));
 }
 if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() >
1000000) {
 bindingResult.addError(new FieldError("item", "price", "가격은 1,000 ~ 
1,000,000 까지 허용합니다."));
 }
 if (item.getQuantity() == null || item.getQuantity() > 10000) {
 bindingResult.addError(new FieldError("item", "quantity", "수량은 최대
9,999 까지 허용합니다."));
 }
 //특정 필드 예외가 아닌 전체 예외
 if (item.getPrice() != null && item.getQuantity() != null) {
 int resultPrice = item.getPrice() * item.getQuantity();
 if (resultPrice < 10000) {
 bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은
10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
 }
 }
 if (bindingResult.hasErrors()) {
 log.info("errors={}", bindingResult);
 return "validation/v2/addForm";
 }
 //성공 로직
 Item savedItem = itemRepository.save(item);
 redirectAttributes.addAttribute("itemId", savedItem.getId());
 redirectAttributes.addAttribute("status", true);
 return "redirect:/validation/v2/items/{itemId}";
}
```
마찬가지로 컨트롤러에서 검증 처리를 진행한다. 이 때, 매개변수로 `BindingResult`가 추가된 것을 알 수 있다.
이는 기존의 errors를 담는 Map 대신에 사용되는 것이다. 모델에 담겨지지 않는 것들을 담는 객체라고 이해하면 된다.
따라서 **해당 매개변수의 위치가 중요한데, 반드시 `@ModelAttribute` 다음에 와야 한다.**

```java
if (!StringUtils.hasText(item.getItemName())) {
 bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수입니다."));
 }
 
if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() >1000000) {
bindingResult.addError(new FieldError("item", "price", "가격은 1,000 ~ 1,000,000 까지 허용합니다."));
}

if (item.getQuantity() == null || item.getQuantity() > 10000) {
bindingResult.addError(new FieldError("item", "quantity", "수량은 최대 9,999 까지 허용합니다."));
}
```
기존에 Model에 추가하는 방법이 아닌 `bindingResult` 객체의 `addError` 메서드를 통해 `FieldError`라는 객체를 생성하여
담아준다. 이 객체에는 `@ModelAttribute`의 이름, 오류가 발생한 필드 이름, 메시지를 담을 수 있다.

```java
bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
```
글로벌 오류의 경우에는 `FieldError`가 아닌 `ObjectError`를 생성해서 담는다. 이 때는 필드가 존재하지 않기 때문에
필드 이름을 넣지 않는다.

```html
<form action="item.html" th:action th:object="${item}" method="post">
 <div th:if="${#fields.hasGlobalErrors()}">
    <p class="field-error" th:each="err : ${#fields.globalErrors()}" th:text="${err}">글로벌 오류 메시지</p>
 </div>
 <div>
   <label for="itemName" th:text="#{label.item.itemName}">상품명</label>
   <input type="text" id="itemName" th:field="*{itemName}" th:errorclass="field-error" class="form-control" placeholder="이름을 입력하세요">
     <div class="field-error" th:errors="*{itemName}">
     상품명 오류
     </div>
 </div>
 <div>
   <label for="price" th:text="#{label.item.price}">가격</label>
   <input type="text" id="price" th:field="*{price}" th:errorclass="field-error" class="form-control" placeholder="가격을 입력하세요">
     <div class="field-error" th:errors="*{price}">
     가격 오류
     </div>
 </div>
 <div>
   <label for="quantity" th:text="#{label.item.quantity}">수량</label>
   <input type="text" id="quantity" th:field="*{quantity}" th:errorclass="field-error" class="form-control" placeholder="수량을 입력하세요">
     <div class="field-error" th:errors="*{quantity}">
     수량 오류
     </div>  
 </div>
```
- `#fields`를 사용하면 스프링의 `BindingResult`를 활용하여 검증 오류를 표현할 수 있다. (타임리프와 스프링의 통합 기능)
- `th:errors="*{필드 이름}"`을 사용하면 해당 필드에 오류가 있을 경우, 태그를 출력하게 된다. 이는 `th:if`문을 사용하는 것보다
훨씬 편리하다. 물론 `#fields`를 사용해도 동일하다.
- `th:errorclass`를 사용하면 `th:field`에서 지정한 필드에 오류가 발생하게 되면 지정한 `class`를 추가한다.
- 스프링 통합 문법인 field를 사용하면 이처럼 간편하게 사용할 수 있고, 개발자가 일일이 지정하지 않아도 알아서 field
이름을 조회해서 동작하게 된다.

> https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html#validation-and-error-messages  
> 해당 문서를 확인하면 보다 쉽게 이해할 수 있다.

### BindingResult
BindingResult는 스프링이 제공하는 **검증오류를 보관하는 객체이다.** 즉, 검증오류가 발생한 내용들은
여기에 보관되는 것이다. Model에 입력받은 내용들 중에서 타입이 맞지 않거나 검증로직을 통과하지 못한 값들이
들어간다고 이해하면 된다. 따라서 `BindingResult`가 있으면 `@ModelAttribute`에 데이터 바인딩 시
오류가 발생해도 컨트롤러가 호출된다. 기존에는 에러 페이지를 보내면서 다운됐었다.

#### `@ModelAttribute`에 바인딩 시 타입 오류가 발생할 경우
- `BindingResult`가 있을 때 : `FieldError`를 `BindingResult`에 담아서 컨트롤러를 호출한다.
- `BindingResult`가 없을 때 : 400 오류가 발생하면서 컨트롤러가 호출되지 않은 채 에러 페이지로 이동한다.

즉, `@ModelAttribute`의 객체에 타입 오류 등으로 인해 바인딩이 실패하는 경우, 스프링이 `FieldError`를 생성해
`BindingResult` 객체에 넣어준다.

#### BindingReulst 출력 결과  
`Field error in object 'item' on field 'price': rejected value [qqqqq]; codes [typeMismatch.item.price,typeMismatch.price,typeMismatch.java.lang.Integer,typeMismatch]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [item.price,price]; arguments []; default message [price]]; default message [Failed to convert property value of type 'java.lang.String' to required type 'java.lang.Integer' for property 'price'; nested exception is java.lang.NumberFormatException: For input string: "qqqqq"]`
- Field Error를 생성하여 해당 객체에 담겨있음을 알 수 있다.

`BindingResult`는 검증할 대상 바로 다음에 와야 한다. 따라서 순서가 중요하다. 즉, `@ModelAttribute`
다음에 `BindingResult`가 와야 한다.

### 사용자 입력 오류 메세지를 화면에 남기는 법 - FieldError, ObjectError
사용자가 입력한 데이터를 그대로 가지고 있어야 한다. `FieldError`와 `ObjectError`를 사용하면
검증에 실패한 데이터를 저장한 채로 컨트롤러를 호출할 수 있다.

```java
// 검증 로직
        if (!StringUtils.hasText(item.getItemName())) {
//            bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수입니다."));
            bindingResult.addError(new FieldError("item", "itemName", item.getItemName(),false,null,null,"상품 이름은 필수입니다."));
        }
        if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
//            bindingResult.addError(new FieldError("item", "price", "가격은 1,000 ~ 1,000,000 까지 허용합니다."));
            bindingResult.addError(new FieldError("item", "price", item.getPrice(), false,null, null,"가격은 1,000 ~ 1,000,000 까지 허용합니다."));

        }
        if (item.getQuantity() == null || item.getQuantity() >= 9999) {
//            bindingResult.addError(new FieldError("item", "quantity", "수량은 최대 9,999 까지 허용합니다."));
            bindingResult.addError(new FieldError("item", "quantity", item.getQuantity(), false, null, null,"수량은 최대 9,999 까지 허용합니다."));
        }

        // 특정 필드가 아닌 복합 룰 검증
        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.addError(new ObjectError("item",null,null, "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
            }
        }
```
FieldError는 두 가지 생성자를 제공한다.
```java
public FieldError(String objectName, String field, String defaultMessage);
public FieldError(String objectName, String field, @Nullable Object rejectedValue, boolean bindingFailure, 
@Nullable String[] codes, @NullableObject[] arguments, @Nullable String defaultMessage)
```
- `rejectedValue`에 사용자가 입력한 거절된 값이 들어간다. 이는 `Object` 타입이기에 어떠한 데이터든 다 들어갈 수 있다.
- `bindingFailure`가 True면 타입 오류에 의한 것으로 바인딩 실패를 의미한다.

#### 타임리프의 사용자 입력 값 유지
`th:field="*{price}"` : 타임리프의 `th:field`는 매우 유용하고 똑똑한 기능이다. 만약 정상적으로
검증이 통과하는 경우, Model(위 코드에서는 item)의 값을 사용한다. 하지만 검증 실패로 인한 오류가 발생할 경우,
`FieldError`에서 보관한 값을 사용해서 출력한다.
```html
 <div>
    <label for="price" th:text="#{label.item.price}">가격</label>
    <input type="text" id="price" th:field="*{price}"
           th:errorclass="field-error" class="form-control" placeholder="가격을 입력하세요">
    <div class="field-error" th:errors="*{price}">
        가격 오류
    </div>
 </div>
```
이 예시의 경우, 검증이 성공적으로 되었을 경우, value가 Model의 값으로 바인딩된다. 하지만 검증이 실패했을 경우,
FieldError의 값을 가져와서 value로 설정한다. 만약 `th:field`를 사용하지 않았다면 일일이 if문을 사용해서 코드를 작성해야했을 것이다.

#### 스프링의 바인딩 오류 처리
만약, 타입 오류로 인해 실패했을 경우, 스프링이 `FieldError`를 생성하여 사용자가 입력한 값을 넣어준다.
Integer가 들어와야 하는 곳에 String 데이터가 들어왔다면 이를 FieldError의 `rejectedValue`에 담는다.
그리고 이를 `BindingResult`에 담아서 컨트롤러에 호출한다. 따라서 BindingResult의 위치가 `@ModelAttribute` 다음에
와야 하는 것이다. (Model에 담기지 않은 값들이 BindingResult에 들어가기 때문)

### 오류 코드와 메시지 처리1
```java
public FieldError(String objectName, String field, String defaultMessage);
public FieldError(String objectName, String field, @Nullable Object 
rejectedValue, boolean bindingFailure, @Nullable String[] codes, @Nullable
Object[] arguments, @Nullable String defaultMessage)
```
`FieldError`가 제공하는 두 번째 생성자를 사용하면 외부에서 지정한 메시지 파일을 이용해서 오류 메시지를
간편하게 설정할 수 있다.  
`codes`는 외부 `properties` 파일에서 지정한 key 값이다. 이는 String 타입의 배열로 입력되며,
여러 값을 입력할 수 있다.(처음 오는 값이 우선순위가 높다. `ex) new String[]{"aaa","bbb})`  
`argument`는 `codes`에 해당하는 값이 파라미터를 필요로 할 경우, 넘겨주는 인자이다. 만약, `codes`가 `range.item.price=가격은 {0} ~ {1} 까지 허용합니다.`
라면 `argument`를 `new Object[]{1000,10000}` 이런식으로 넘길 수 있다. 그러면 이 값들이 각각 {0} 과 {1}에 바인딩된다.

이러한 외부 메시지 파일을 관리하도록 별도의 `errors.properties` 파일을 만든다.
그리고 이것을 스프링 부트가 메시지 파일로 읽을 수 있도록 별도로 세팅을 해야 한다.
```
// application.properties
// 공백일 경우 basename = messages
spring.messages.basename=messages,errors

// errors.properties
required.item.itemName=상품 이름은 필수입니다.
range.item.price=가격은 {0} ~ {1} 까지 허용합니다.
max.item.quantity=수량은 최대 {0} 까지 허용합니다.
totalPriceMin=가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}
```
- `errors_en.properties` 파일을 만든다면 오류 메시지도 국제화가 가능하다.

```java
@PostMapping("/add")
public String addItemV3(@ModelAttribute Item item, BindingResult bindingResult,
RedirectAttributes redirectAttributes) {
 if (!StringUtils.hasText(item.getItemName())) {
    bindingResult.addError(new FieldError("item", "itemName",item.getItemName(), false, new String[]{"required.item.itemName"}, null,null));
 }
 if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() >1000000) {
    bindingResult.addError(new FieldError("item", "price", item.getPrice(),false, new String[]{"range.item.price"}, new Object[]{1000, 1000000}, null));}
 if (item.getQuantity() == null || item.getQuantity() > 10000) {
     bindingResult.addError(new FieldError("item", "quantity",item.getQuantity(), false, new String[]{"max.item.quantity"}, new Object[]{9999}, null));
 }
 //특정 필드 예외가 아닌 전체 예외
 if (item.getPrice() != null && item.getQuantity() != null) {
   int resultPrice = item.getPrice() * item.getQuantity();
   if (resultPrice < 10000) {
   bindingResult.addError(new ObjectError("item", new String[]{"totalPriceMin"}, new Object[]{10000, resultPrice}, null));}
   }
   if (bindingResult.hasErrors()) {
   log.info("errors={}", bindingResult);
   return "validation/v2/addForm";
 }
 //성공 로직
 Item savedItem = itemRepository.save(item);
 redirectAttributes.addAttribute("itemId", savedItem.getId());
 redirectAttributes.addAttribute("status", true);
 return "redirect:/validation/v2/items/{itemId}";
}
```

### 오류 코드와 메시지 처리2
매번 `FieldError`와 `ObjectError`를 생성하는 것은 귀찮고 번거롭다. 이전 코드는
`bindingResult`에 `FieldError` 또는 `ObjectError`를 생성하여 넣어주었다.
하지만 BindingResult는 검증 객체인 `item` 다음으로 오기 때문에 이미 검증 타겟이 무엇인지 아는 상태이다.
실제로 `bindingResult`가 갖고 있는 객체 명과 타겟 객체를 출력해서 확인할 수 있듯이 이미 객체 이름을 알고 있다.

```java
log.info("objectName={}", bindingResult.getObjectName());
log.info("target={}", bindingResult.getTarget());

/// 결과
objectName=item //@ModelAttribute name
target=Item(id=null, itemName=상품, price=100, quantity=1234)
```
따라서 굳이 Error 객체를 생성하지 않고 특정 값만 넘기면 알아서 Error 객체를 생성하게 할 수 있다.
```java
if (item.getQuantity() == null || item.getQuantity() > 10000) {
 bindingResult.rejectValue("quantity", "max", new Object[]{9999}, null);
}

// 글로벌 에러
if (resultPrice < 10000) {
  bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
}
```
- `bindingReulst`가 객체 이름만 알기 때문에 특정 필드이름은 넘겨주어야 한다. 이외의 `codes`와 `args`는 동일한 방법으로 넘겨준다. 글로벌 에러의 경우에는 필드 이름을 넘기지 않는다.
  - 필드 에러일 경우 : `rejectValue()`
  - 글로벌 에러일 경우 : `reject()`
- 해당 메서드를 사용하면 넘겨받은 인자를 통해 알아서 `FieldError` 또는 `ObjectError` 객체를 생성한다.

### 오류 코드와 메시지 처리3
오류 코드를 만들 때 간단하게 만들면 좀 더 범용성이 높아진다. 즉, 여러 곳에서 사용할 수 있는 것이다.
하지만 자세하게 만들 경우, 특정 범위 내에서만 사용된다. 이 둘을 적절히 활용하는 방법은
일단 범용성 높은 코드를 사용하다가. 세밀하게 설정해야 하는 경우는 세밀하게 작성된 코드를 사용하는 것이다.

예를들어, `required`라는 코드명과 `required.item.itemName`이라는 코드명이 있을 경우, 전자는 좀 더 일반적이고 범용성이 높은 코드이고
후자는 자세하게 작성된 코드이다. 이 때 우선순위는 후자가 더 높다.

```
#Level1
required.item.itemName: 상품 이름은 필수 입니다.

#Level2
required: 필수 값 입니다.
```
이렇게 하면 구현 코드를 바꾸지 않고, 메시지 파일만 수정하면 된다는 장점이 있다. 이것이 가능한 이유는
스프링의 `MessageCodesResolver`가 존재하기 때문이다.

### 오류 코드와 메시지 처리4
MessageCodeResolver는 입력받은 객체명, 필드 이름, 에러 코드 이름을 통해 에러코드를 문자열 배열로
출력해준다. 즉, 이를 가지고 `FieldError` 혹은 `ObjectError`를 만들어서 `BindingResult`에게 넘기는 것이다.
그리고 이를 타임리프 `th:errors`를 통해 확인할 수 있다.

```java
public class MessageCodesResolverTest {

    MessageCodesResolver codesResolver = new DefaultMessageCodesResolver();

    @Test
    void messageCodesResolverObject() {
        String[] messageCodes = codesResolver.resolveMessageCodes("required", "item");
        for (String messageCode : messageCodes){
            System.out.println("messageCodes = " + messageCode);
        }
        /*
        messageCodes = required.item
        messageCodes = required
        */
        // new ObjectError("item", new Sring[]{"required.item", "required"});
        Assertions.assertThat(messageCodes).containsExactly("required.item", "required");
    }

    @Test
    void messageCodesResolverField() {
        String[] messageCodes = codesResolver.resolveMessageCodes("required", "item", "itemName", String.class);
        /*for (String messageCode : messageCodes) {
            System.out.println("messageCode = " + messageCode);
        }*/
        Assertions.assertThat(messageCodes).containsExactly(
                "required.item.itemName",
                "required.itemName",
                "required.java.lang.String",
                "required"
        );
    }
}
```
#### MessageCodesResolver
- 검증 오류 코드로 메시지 코드들을 생성한다.
  - `messageCodes = required.item,
    messageCodes = required`
  - `MessageCodesResolver`는 인터페이스이고, `DefaultMessageCodesResolver`는 기본 구현체이다.
#### MessageCodesResolver의 기본 메시지 생성 규칙
1. Object 오류
```
객체 오류의 경우 다음 순서로 2가지 생성
1.: code + "." + object name
2.: code

예) 오류 코드: required, object name: item
1.: required.item
2.: required
```
2. Field 오류
```
필드 오류의 경우 다음 순서로 4가지 메시지 코드 생성
1.: code + "." + object name + "." + field
2.: code + "." + field
3.: code + "." + field type
4.: code

예) 오류 코드: typeMismatch, object name "user", field "age", field type: int
1. "typeMismatch.user.age"
2. "typeMismatch.age"
3. "typeMismatch.int"
4. "typeMismatch"
```
BindingResult가 `rejectValue(), reject()`를 사용하면 별도로 `FieldError`나 `ObjectError`를
생성하지 않아도 되었다. 그 이유는 해당 메서드 내부에서 `MessageCodesResolver`를 사용하기 때문이다.
`FieldError`,`ObjectError`의 경우, 오류 코드를 문자열을 통해 여러개 받을 수 있는데, `MessageCodesResolver`를 통해
생성된 것들이다.

즉, 에러 코드들을 규악에 맞게 자동으로 생성한다. 그리고 이를 타임리프 렌더링 과정에서 `th:errors`가 실행된다.
만약 오류가 있다면 생성된 오류 메시지 코드를 순서대로 돌아가면서 메시지를 찾는다. 그리고 없으면 디폴트 메시지를 출력한다.

### 오류 코드와 메시지 처리5
`MessageCodesResolver`는 `required.item.itemName`처럼 구체적인 코드를 먼저 만들고, `required`처럼
덜 구체적인 코드를 낮은 우선순위로 둔다. 이렇게 하는 이유는 구체적인 것과 범용적인 것을 함께 사용할 수 있기 때문이다.
개발자는 로직 코드를 변경하지 않고, 단순히 설정 파일만 조절해서 오류 메시지를 상황에 맞게 처리할 수 있다.

```
errors.properties

#required.item.itemName=상품 이름은 필수입니다.
#range.item.price=가격은 {0} ~ {1} 까지 허용합니다.
#max.item.quantity=수량은 최대 {0} 까지 허용합니다.
#totalPriceMin=가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}

#==ObjectError==
#Level1
totalPriceMin.item=상품의 가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}

#Level2 - 생략
totalPriceMin=전체 가격은 {0}원 이상이어야 합니다. 현재 값 = {1}

#==FieldError==
#Level1
required.item.itemName=상품 이름은 필수입니다.
range.item.price=가격은 {0} ~ {1} 까지 허용합니다.
max.item.quantity=수량은 최대 {0} 까지 허용합니다.

#Level2 - 생략

#Level3
required.java.lang.String = 필수 문자입니다.
required.java.lang.Integer = 필수 숫자입니다.
min.java.lang.String = {0} 이상의 문자를 입력해주세요.
min.java.lang.Integer = {0} 이상의 숫자를 입력해주세요.
range.java.lang.String = {0} ~ {1} 까지의 문자를 입력해주세요.
range.java.lang.Integer = {0} ~ {1} 까지의 숫자를 입력해주세요.
max.java.lang.String = {0} 까지의 문자를 허용합니다.
max.java.lang.Integer = {0} 까지의 숫자를 허용합니다.

#Level4
required = 필수 값 입니다.
min= {0} 이상이어야 합니다.
range= {0} ~ {1} 범위를 허용합니다.
max= {0} 까지 허용합니다.
```
필드명이 `itemName`일 경우, `required` 검증 오류 메시지가 발생하면 다음 코드가 순서대로 생성된다.
1. `required.item.itemName`
2. `required.itemName`
3. `required.java.lang.String`
4. `required`

그리고 이 메시지 코드들을 기반으로 `MessageSource`에서 메시지를 찾는다. (`MessageSource`는 스프링 메시지 파일을 찾는 객체)
우선순위가 높은(구체적인 것) 것에서 우선순위가 낮은(덜 구체적인 것) 순으로 찾는다.

#### ValidationUtils
- ValidationUtils 사용 전
```java
if (!StringUtils.hasText(item.getItemName())) {
 bindingResult.rejectValue("itemName", "required", "기본: 상품 이름은 필수입니다.");
}
```
- ValidationUtils 사용 후
```java
ValidationUtils.rejectIfEmptyOrWhitespace(bindingResult, "itemName","required");
```
-> 공백이나 빈 값을 넣는 경우에는 이와 같은 유틸리티 코드를 활용할 수 있다.

### 오류 코드와 메시지 처리6
만약 타입이 맞지 않을 경우, 스프링이 자동으로 `typeMismatch`라는 오류 코드를 사용한다. 따라서 `bindingResult`의 로그를 확인해보면
`FieldError`가 담겨 있으며, 다음과 같은 메시지 코드들이 생성됨을 확인할 수 있다.  
`codes[typeMismatch.item.price,typeMismatch.price,typeMismatch.java.lang.Integer,typ
eMismatch]`  
- `typeMismatch.item.price`
- `typeMismatch.price`
- `typeMismatch.java.lang.Integer`
- `typeMismatch`
따라서 `error.properties`에 위 코드들에 대한 메시지 내용을 추가하면 된다.
```
#추가
typeMismatch.java.lang.Integer=숫자를 입력해주세요.
typeMismatch=타입 오류입니다.
```
**여기서 핵심은 소스코드를 하나도 건들지 않고, 원하는 메시지를 단계별로 설정할 수 있다는 점이다.**

### 오류 코드와 메시지 처리 정리
1. `rejectValue()` 호출
2. `MessageCodesResolver`를 사용해서 검증 오류 코드로 메시지 코드들을 생성
3. `new Field()`를 생성하면서 메시지 코드들을 보관
4. `th:errors`에서 메시지 코드들로 메시지를 순서대로 메시지에서 찾고, 노출(출력)

### Validator 분리1
기존의 컨트롤러는 검증로직과 실행로직이 존재하기 때문에, 컨틀로러가 처리하는 업무가 상당히 많았다.
따라서 검증에 해당하는 부분을 따로 Validator라는 객체로 분리시키도록 한다.
```java
@Component
public class ItemValidator implements Validator {
  @Override
  public boolean supports(Class<?> clazz) {
    return Item.class.isAssignableFrom(clazz); // 들어온 클래스가 Item 클래스와 같거나 하위 클래스인지 판단
  }

  // 검증 로직
  @Override
  public void validate(Object target, Errors errors) { // Errors는 BindingResult의 부모 클래스
    Item item = (Item) target;

    if (!StringUtils.hasText(item.getItemName())) {
      errors.rejectValue("itemName","required");
    }
    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
      errors.rejectValue("price", "range", new Object[]{1000, 1000000}, null);

    }
    if (item.getQuantity() == null || item.getQuantity() >= 9999) {
      errors.rejectValue("quantity", "max", new Object[]{9999}, null);
    }

    if (item.getPrice() != null && item.getQuantity() != null) {
      int resultPrice = item.getPrice() * item.getQuantity();
      if (resultPrice < 10000) {
        errors.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
      }
    }
  }
}
```
```java
@PostMapping("/add")
public String addItemV5(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

    itemValidator.validate(item, bindingResult);

    if (bindingResult.hasErrors()) {
        log.info("errors = {} ", bindingResult);
        return "validation/v2/addForm";
    }

    // 성공 로직 - 검증 로직이 너무 길다는 문제가 존재(컨트롤러의 역할 많음)
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    return "redirect:/validation/v2/items/{itemId}";
}
```
컨트롤러에서 Validator를 주입하였으며, 이를 활용하여 검증 로직을 수행하였다.

### Validator 분리2
스프링이 제공하는 `Validator` 인터페이스를 사용하는 이유는 스프링의 추가적인 도움을 받기 위해서이다.
하지만 위처럼 직접 Validator를 호출하는 방법은 굳이 스프링의 인터페이스를 사용할 필요가 없음을 보여준다.
```java
@InitBinder
public void init(WebDataBinder dataBinder) {
   log.info("init binder {}", dataBinder);
   dataBinder.addValidators(itemValidator);
}
```
컨트롤러에 다음과 같은 코드를 추가하면, 컨트롤러에 있는 메서드가 실행될 때마다 해당 메서드가 동작한다.
`WebDataBinder`라는 객체에 사용자가 생성한 Validator를 추가하여, 검증을 자동으로 적용시킬 수 있는 것이다.

```java
@PostMapping("/add")
public String addItemV6(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
 if (bindingResult.hasErrors()) {
   log.info("errors={}", bindingResult);
   return "validation/v2/addForm";
 }
 //성공 로직
 Item savedItem = itemRepository.save(item);
 redirectAttributes.addAttribute("itemId", savedItem.getId());
 redirectAttributes.addAttribute("status", true);
 return "redirect:/validation/v2/items/{itemId}";
}
```
매개변수에 추가된 `@Validated`는 검증기를 실행하라는 애노테이션이다. 즉, 이 애노테이션을 통해 `WebDataBinder`에 등록한
검증기를 찾아서 실행한다. 검증 대상은 `@ModelAttribute`의 대상인 Model, Item이다. 이 때, 검증기가 여러 개라면 그 중 어떤 검증기를 실행해야 하는지 구분이 필요하다.
이 때, 사용하는 것이 Validator 인터페이스에서 지원하는 `supports()`이다.
```java
@Component
public class ItemValidator implements Validator {
 @Override
   public boolean supports(Class<?> clazz) {
   return Item.class.isAssignableFrom(clazz);
 }
 @Override
 public void validate(Object target, Errors errors) {...}
}
```
`supports()`의 매개변수로 Item 타입을 집어넣으면, ItemValidator가 동작하게 되는 것이다.

# 검증 2 - Bean Validation

### Bean Validation이란?
```java
@Data
public class Item {

    private Long id;

    @NotBlank(message = "공백X")
    private String itemName;

    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;

    @NotNull
    @Max(9999)
    private Integer quantity;

    public Item() {
    }

    public Item(String itemName, Integer price, Integer quantity) {
        this.itemName = itemName;
        this.price = price;
        this.quantity = quantity;
    }
}
```
기존의 검증은 여러 if 문을 통해 복잡하게 코드를 작성하였다. 하지만 스프링 통합 Bean Validation 기술을 사용하면
애노테이션을 통해 값을 검증할 수 있다. 이런 **검증 로직을 모든 프로젝트에 적용할 수 있게 공통화하고, 표준화 한 것이
바로 Bean Validation이다.**

Bean Validation은 특정한 구현체가 아니라 Bean Validation 2.0이라는 기술 표준이다. 즉, 검증 애노테이션과
인터페이스의 모음이다. JPA와 하이버네이트의 관계처럼 말이다. Bean Validation을 구현한 기술 중에 일반적으로 사용하는
구현체는 하이버네이트 Validator이다.

- 공식 사이트 : http://hibernate.org/validator
- 공식 메뉴얼 : https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single/

`javax.validation`의 경우, 구현체에 상관없이 제공되는 표준 인터페이스이다. `org.hibernate.validator`의 경우
하이버네이트 validator 구현체를 사용할 때만 제공되는 검증 기능이다. `build.gradle`을 통해 라이브러리를 설치하게 될 경우,
포함된 클래스 및 애노테이션을 통해 특정 기능을 구현할 수 있는 것이다. 따라서 라이브러리의 패키지명을 통해 기능들을 잘 파악하는 것이
중요하다.

### Bean Validation - 스프링 적용
스프링 부트가 `spring-boot-starter-validation` 라이브러리를 추가할 경우 자동으로 Bean Validator를
인지하여 스프링에 통합한다. 스프링부트는 `LocalValidatorFactoryBean`을 글로벌 Validator로 등록한다. 이 Validator는
`@NotNull` 같은 **애노테이션을 보고 검증을 수행한다.** 글로벌 Validator가 프로젝트에 적용되어 있기 때문에 `@Valid`,`@Validated` 같은
애노테이션만 적용하면 된다. 만약, 오류가 발생할 경우 `FiledError`,`ObjectError`를 생성해서 `BindingResult`에 담아준다.

#### 검증 순서
1. `@ModelAttribute` 각각의 필드에 타입 변환 시도
  - `Model`인 객체에 입력받은 파라미터를 바인딩한다. 이 때, 정의한 자바 필드 타입에 맞게 변환이 이루어진다.
  - 성공하면 2번으로 넘어가고, 바인딩에 실패할 경우 `typeMismatch`로 `FieldError`가 추가 된다.
2. Validator가 적용된다.

여기서 중요한건 **바인딩에 성공한 필드만 Bean Validation이 적용된다는 것이다.** 타입 변환에 성공해서
실제 모델에 들어있는 값이어야 검증 적용이 의미가 있기 때문이다. 문자가 들어와야 하는 곳에 숫자가 들어온 이상
검증하는 것은 아무런 의미가 없기 때문이다. 이미 그 자체로 타입 오류이기 때문에 검증을 통과하지 못한 케이스인 것이다.

### Bean Validation - 에러 코드
Bean Validation이 제공하는 에러코드는 기본적으로 애노테이션의 이름을 기반으로 생성된다.
예를들어, `@NotBlank`의 경우, `NotBlank.item.itemName`, `NotBlank.itemName` ... `NotBlank` 식으로 에러코드가 생성된다.
여기에 있는 코드들에 대해 메시지를 적용하기 위해서는 기존에 작성했던 `errors.properties` 파일을 수정하기만 하면 된다.

#### Bean Validation 메시지 찾는 순서
1. 생성된 메시지 코드 순서대로 `messageSource`에서 메시지 찾기 (없으면 2번으로)
2. 애노테이션의 `message` 속성 사용 (ex. `NotBlank(message = "공백! {0}")) (없으면 3번으로)
3. 라이브러리가 제공하는 기본 값 사용 -> (ex. 공백일 수 없습니다.)

### Bean Validation - 오브젝트 오류
특정 필드(`FieldError`)가 아닌 해당 오브젝트 관련 오류(`ObjectError`, 두 가지 이상의 필드에 대한
오류, 글로벌 오류)일 경우, 하이버네이트에서 제공하는 `@ScriptAssert()`를 사용할 수 있다.
```java
@Data
@ScriptAssert(lang = "javascript", script = "_this.price * _this.quantity >= 
10000")
public class Item {
 //...
}
```
메시지 코드 또한 `ScriptAssert`를 기반으로 생성된다. (`ScriptAssert.item`, `ScriptAssert`)

하지만 실무에서 사용하기에는 제약이 많고 복잡하다. 따라서 **검증 코드를 자바 코드로 구현해서 컨트롤러에 추가하는 것을 권장한다.**
```java
@PostMapping("/add")
    public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }

        if (bindingResult.hasErrors()) {
            log.info("errors = {} ", bindingResult);
            return "validation/v3/addForm";
        }

        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v3/items/{itemId}";
    }
```
이처럼 오브젝트 오류가 발생했을 경우, 컨트롤러에 따로 검증기능을 추가한다. 

### Bean Validation 여러 검증사항 적용
아이템을 등록할 때 검증할 항목과 수정할 때 검증할 항목이 다를 수 있다. 분명 서비스를 개발하다보면 각 상황에 따른 검증을 수행해야 할 경우가
반드시 존재할 것이다. 따라서 하나의 도메인 객체에 대해 두 가지 다른 검증항목을 적용하기 위해서는 두 가지 방법을 적용할 수 있다.
1. groups 기능 적용
   - `@Validated`의 value 속성을 통해 검증이 적용되는 항목을 구분할 수 있다. (이는 인터페이스를 사용한다.)
   - 실무에서는 잘 사용하지 않는 방법이다.
2. 도메인 객체 분리
   - 데이터를 등록할 때 전달되는 객체와 실제 저장소에 저장되는 객체가 일치하지 않을 수 있다. 데이터를 등록할 때는 Id 값이 필요하지 않지만 실제 값을 저장할 때는 Id가 필요할 수 있다.
   - 따라서 하나의 객체로 모든 것을 활용하지 않고 HTTP Form 입력하는 곳에서 사용할 수 있는 Form 객체를 생성한다.
   - `HTML Form -> ItemSaveForm -> Controller -> Item 생성 -> Repository` 이 순서대로 진행된다.

```java
@Data
public class ItemSaveForm {
 @NotBlank
 private String itemName;
 @NotNull
 @Range(min = 1000, max = 1000000)
 private Integer price;
 @NotNull
 @Max(value = 9999)
 private Integer quantity;
}

@Data
public class ItemUpdateForm {
  @NotNull
  private Long id;
  @NotBlank
  private String itemName;
  @NotNull
  @Range(min = 1000, max = 1000000)
  private Integer price;
  //수정에서는 수량은 자유롭게 변경할 수 있다.
  private Integer quantity;
}
```
```java
@PostMapping("/add")
    public String addItem(@Validated @ModelAttribute("item") ItemSaveForm form, BindingResult bindingResult, RedirectAttributes redirectAttributes) {

        if (form.getPrice() != null && form.getQuantity() != null) {
            int resultPrice = form.getPrice() * form.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }

        if (bindingResult.hasErrors()) {
            log.info("errors = {} ", bindingResult);
            return "validation/v4/addForm";
        }

        // 데이터를 저장할 때는 Item 타입으로 생성해서 전달한다.
        Item item = new Item();
        item.setItemName(form.getItemName());
        item.setPrice(form.getPrice());
        item.setQuantity(form.getQuantity());

        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v4/items/{itemId}";
    }
```
- `@ModelAttribute`에서 View에 전달되는 모델이름은 자동으로 객체 변수 이름으로 되기 때문에 특별한 값을 지정할 수 있다.
- 실제 저장소에 저장하는 로직에서는 `ItemSaveForm`이 아닌 `Item` 객체를 생성해서 저장한다.

### Bean Validation - 메시지 바디에 적용
API 요청 값에 대해서도 검증을 적용할 수 있다. `@ModelAttribute`는 HTTP 요청 파라미터(URL 쿼리 스트링, POST Form)에 사용되며,
`@RequestBody`는 HTTP Body의 데이터를 객체로 변환할 때 사용한다. (주로 API 요청에서 사용)

```java
@Slf4j
@RestController
@RequestMapping("/validation/api/items")
public class ValidationItemApiController {

  @PostMapping("/add")
  public Object addItem(@RequestBody @Validated ItemSaveForm form, BindingResult bindingResult) {
    log.info("API 컨트롤러 호출");

    if (bindingResult.hasErrors()) {
      log.info("검증 오류 발생 errors={}", bindingResult);
      return bindingResult.getAllErrors();
    }

    log.info("성공 로직 실행");
    return form;
  }
}
```
만약, 입력한 타입이 다를 경우 `HttpMessageConverter`에서 요청받은 JSON을 객체로 변환하는 것에 실패한다. 따라서 컨트롤러의 매개변수인 `ItemSaveForm` 객체가 생성되지 않으므로
**컨트롤러 자체가 호출되지 않는다.** 그 전에 예외가 발생해버린다.

`@ModelAttribute`의 경우, 각각의 필드 단위로 객체에 바인딩되기 때문에, 특정 필드에 타입이 맞지 않더라도 나머지는 정상적으로 처리한다. 하지만
`@RequestBody`의 경우, 하나의 필드가 타입이 맞지 않으면 객체 자체가 생성이 되지 않는다. `HttpMessageConverter`가 객체 단위로 적용되기 때문이다.
따라서 컨버터가 정상적으로 작동해서 컨트롤러의 매개변수 객체가 생성되어야 `@Valid`,`@Validated` 같은 검증 기능이 적용된다.

### 단축키 정보
- `컨트롤+쉬프트+R` : Replace - 특정 단어를 한번에 변경할 수 있다.
- `컨트롤+E` : 가장 최근에 봤던 파일로 이동할 수 있다. 파일을 왔다갔다 봐야할 경우 유용한 단축키
