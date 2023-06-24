# Contents Of MVC2
- [타임 리프](#타임-리프)
  - [텍스트 - text,utext](#텍스트---text-utext)


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
문자로 표현하는 것이 필요한데 이를 HTML 엔티티라고 한다. ex) `<` -> `&lt;`(HTML 엔티티)
- `<`가 `&lt;`라는 HTML 엔티티로 변경하는 것을 **이스케이프(Escape)**라고 한다.
- 따라서 특수문자를 HTML 파일이 아닌 다른 곳에서 문자열로 담아서 출력할 경우, 이처럼 이스케이프가 적용된다.

#### Unescape
타임리프에서 이스케이프가 적용되지 않도록 하는 방법은 크게 두 가지가 있다.
1. `th:text` -> `th:utext`
2. `[[ 내용 ]]` -> `[( 내용 )]` (대괄호에서 소괄호로 변경됨)

하지만 실제 서비스 개발에서 Escape를 사용하지 않으면 HTML이 정상적으로 렌더링되지 않을 수 있다. 따라서 escape 적용을
기본으로 하되, 꼭 필요한 경우에만 Unescape를 사용하는 것을 권장한다.