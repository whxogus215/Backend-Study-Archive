# Contents Of MVC2
- [도메인 패키지 구조](#도메인-패키지-구조)
- [테스트 데이터 추가하는 법](#테스트-데이터-추가하는-법)
- [로그인 구현에 필요한 기능](#로그인-구현에-필요한-기능)
- [로그인의 핵심 기능 : 쿠키와 세션](#로그인의-핵심-기능--쿠키와-세션)
  - [쿠키의 보안 문제점](#쿠키의-보안-문제점)
  - [쿠키의 보안 이슈 대안](#쿠키의-보안-이슈-대안)
  - [세션](#세션)
- [스프링에서 세션기능 사용하는 법](#스프링에서-세션기능-사용하는-법)
  - [첫 로그인 시 URL에 세션 ID가 포함되는 이유](#첫-로그인-시-url에-세션-id가-포함되는-이유)
  - [세션을 사용할 때 주의할 점(필독)](#세션을-사용할-때-주의할-점필독)
- [단축키](#단축키-정보)

###### Reference
- **(main)** 인프런 김영한 스프링 MVC 2편 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard


# 도메인 패키지 구조
웹 애플리케이션 설계에서 도메인을 설정하는 것은 매우 중요하다. 따라서 패키지도 도메인별로 나누어 관리하는 것이
바람직하다. **도메인이란** 화면, UI, 기술 인프라 등등의 영역은 제외한 **시스템이 구현해야 하는 핵심 비즈니스 업무 영역을
말한다.** 따라서 `domain`과 `web`으로 나누면, `domain`에는 해당 `domain`에 관련된 서비스나 리포지토리
계층의 코드들이 존재할 것이다. `web`에는 웹 기술에 관련된 컨트롤러 등이 존재한다.

**여기서 주의할 점은 `domain`은 `web`에 의존해서는 안된다는 것이다.** `web`은 언제든지 다른 기술로
바뀔 수 있다. 따라서 기술이 변경되어도 `domain`에 있는 코드들은 변경이 되어서는 안된다. **즉, `web`은
`domain`을 알지만 `domain`은 `web`을 몰라야 한다.** 즉, 양방향이 아닌 단방향으로 의존관계가 맺어져야
안전한 웹 애플리케이션 설계라고 할 수 있다.

# 테스트 데이터 추가하는 법
```java
@Component
@RequiredArgsConstructor
public class TestDataInit {

    private final ItemRepository itemRepository;
    private final MemberRepository memberRepository;

    /**
     * 테스트용 데이터 추가
     */
    @PostConstruct
    public void init() {
        itemRepository.save(new Item("itemA", 10000, 10));
        itemRepository.save(new Item("itemB", 20000, 20));

        Member member = new Member();
        member.setLoginId("test");
        member.setPassword("test!");
        member.setName("테스터");

        memberRepository.save(member);
    }

}
```
- 빈으로 등록한 뒤, 메서드에 `@PostConstruct` 추가

# 로그인 구현에 필요한 기능
1. `Validation` 기능을 활용하여, 아이디와 비밀번호 입력에 대한 검증이 필요
   - `BindingResult`를 통해 에러 정보 추출
2. 추후 추가

# 로그인의 핵심 기능 : 쿠키와 세션
로그인의 상태를 유지하기 위해서는 웹 브라우저에게 쿠키를 전달함으로써 가능해진다.
서버에서 한 번 쿠키를 저장해서 반환하면, 클라이언트는 해당 쿠키를 통해 인증을 할 수 있다.

![MVC1](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/bb753517-ad17-4d87-952f-3953b373a155)

![MVC2](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/5386f467-0a88-454f-a9d8-6dc118dd57b3)

![MVC3](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/d4cbe519-0325-4dd4-9165-6bc332d29978)

## 쿠키의 보안 문제점
쿠키는 웹 브라우저에서 얼마든지 조작이 가능하다. 따라서 보안상의 이슈가 존재할 수 있다.
외부에서 쿠키 값을 조정해서 사용자 인증 권한을 얻게 된다면 위험할 수 있다.
1. 쿠키 값은 임의로 변경할 수 있다.
2. 쿠키에 보관된 정보는 훔쳐갈 수 있다.
   - 쿠키는 클라이언트의 로컬 브라우저에 저장되기 때문에 얼마든지 도난당할 수 있다.
3. 해커가 쿠키를 훔쳐갈 경우, 이를 계속해서 악용할 수 있다.

## 쿠키의 보안 이슈 대안
1. 쿠키에는 중요한 값을 노출시켜선 안된다.(ex. 회원 ID 관련 값 등등) 따라서 예측 불가능한 랜덤 값(토큰)을 발생시켜야 하며,
이를 사용자 id와 매핑시키는 별도의 메모리를 **서버에서 관리한다.** (이를 세션이라고 한다.)
2. 쿠키는 해커가 얼마든지 임의의 값을 넣어 보낼 수 있다. 따라서 해커가 임의의 값을 설정하지 못하도록 예측 불가능한 랜덤 값이어야 한다.
3. 만약 해커가 토큰을 털었다고 하더라도 시간이 지나면 사용할 수 없도록 토큰에 대한 만료시간을 설정해야 한다.(평균적으로 30분으로 설정)
혹은 서버 측에서 강제로 토큰을 제거할 수도 있어야 한다.

## 세션
세션이란 **서버에서 중요한 정보(토큰 값과 사용자 id를 매핑)를 보관하고 클라이언트와의 연결을 유지하는
방법을 말한다.** 세션은 로그인 기능에서 핵심적인 기술 중 하나이기 때문에 스프링에서 이미 구현을 해놓았다.
이를 잘 사용하면 로그인 기능을 쉽게 구현할 수 있다.

![MVC4](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/16fdc6db-6a4c-4e94-bd29-da74e279782d)

![MVC5](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/8000ae9a-dc45-486c-b5d5-7f5cd70beb1e)

![MVC6](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/516a6741-e5ed-4b68-8385-1f03508c4686)

이처럼 서버에서 세션 ID를 생성하여 쿠키로 전달할 수 있다면 클라이언트는 연결 정보를 유지할 수 있다.
또한 쿠키에 중요한 정보를 저장하는 것이 아니기 때문에 보안상 이슈도 해결이 된다.

![MVC7](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/d79967db-365c-4e1d-b7c3-725bcce2cc11)

# 스프링에서 세션기능 사용하는 법

## 서블릿 세션
`http 서블릿`을 통해 세션을 얻을 수 있다. 이 `HttpSession`을 사용하면 `JSESSIONID`라는 이름의 쿠키를
생성할 수 있다. 이 때 Value는 **추정 불가능한 랜덤값이다.** 

```java
// 객체 생성 없이 상수 값만 담는 경우 : interface or abstract class
public interface SessionConst {
    String LOGIN_MEMBER = "loginMember";
}
```
이는 세션 이름을 조회할 때 사용할 상수 값으로 따로 값으로 관리하면 편하다. 이 때, 해당 상수 객체는
생성되어선 안되므로 **인터페이스 혹은 추상 클래스로 선언해야 한다.**

1. 로그인
```java
@PostMapping("/login")
public String loginV3(@Valid @ModelAttribute LoginForm form, BindingResult bindingResult, HttpServletRequest request) {
    if (bindingResult.hasErrors()) {
        return "login/loginForm";
    }

    Member loginMember = loginService.login(form.getLoginId(), form.getPassword());

    if (loginMember == null) {
        bindingResult.reject("loginFail", "아이디 또는 비밀번호가 맞지 않습니다.");
        return "login/loginForm";
    }

    // 로그인 성공 처리
    
    // 세션이 있으면 있는 세션 반환, 없으면 신규 세션을 생성
    HttpSession session = request.getSession();
    // 세션에 로그인 회원 정보 보관
    session.setAttribute(SessionConst.LOGIN_MEMBER, loginMember);

    return "redirect:/";
}
```
- 로그인 요청은 POST 요청이다. 이 때, 로그인은 검증절차가 끝나면 클라이언트에게 기존의 세션을 반환하거나 새로 만들어서 반환해야 한다.
- 세션을 만들기 위해 `HttpServletRequest`가 필요하며, 이를 통해 세션을 만든다.
  - `request.getSession()`의 기본 값은 `true`이다. 따라서 세션이 없으면 자동으로 생성한다.
  - `request.getSession(false)`로 하게 되면, 세션이 없을 때 자동으로 세션을 만들지 않는다.(이는 다른 컨트롤러에서 사용된다.) 조회된 세션이 없을 경우, `null`을 반환한다.
  - 어쨌든 여기서는 로그인 후 세션이 반드시 필요하기 때문에 `true` 옵션을 사용하였다.
- `session.setAttribute(세션 ID 이름, 세션에 들어갈 Value 설정)`을 통해 세션 정보를 설정하여 저장할 수 있다.
- **이렇게 세션을 생성하여 저장하면, 사용자에게 응답 헤더에 자동으로 세션 ID가 들어간 쿠키를 반환하게 된다.
그리고 이를 받은 사용자가 다른 API 요청을 할 경우, 자동으로 받은 쿠키의 세션 ID로 요청하게 된다.**

2. 로그아웃
```java
@PostMapping("/logout")
public String logoutV3(HttpServletRequest request) {
    HttpSession session = request.getSession(false);
    if (session != null) {
        session.invalidate(); // null이 아닌 세션이 존재할 경우, 비활성화
    }
    return "redirect:/";
}
```
- 로그아웃을 할 때는 `HttpServletRequest`에서 세션을 가져오되, `getSession(false)`로 가져와야 한다.
기존에 있는 세션을 가져오거나 없으면 null이 반환된다.
- 만약, null이 아니라면 기존에 있는 세션이 있다는 것이므로 이를 비활성화 해야 한다. 이 때는 `invalidate()`를 사용한다.

3. 로그인 후 세션 처리
```java
@GetMapping("/")
public String homeLoginV3(HttpServletRequest request, Model model) {
    
    // true로 할 경우, 처음 홈 화면에 들어오는 사람도 세션이 생겨버림
    HttpSession session = request.getSession(false);
    if (session == null) {
        return "home";
    }

    Member loginMember = (Member) session.getAttribute(SessionConst.LOGIN_MEMBER);
    
    // 세션에 회원 데이터가 없으면 home으로 이동
    if (loginMember == null) {
        return "home";
    }
    
    // 세션이 유지되면 로그인으로 이동
    model.addAttribute("member", loginMember);
    return "loginHome";
}
```
- 마찬가지로 기존의 세션을 조회하는 것이기 때문에 `false` 옵션으로 조회해야 한다. 여기서는 세션을 추가로 생성해서는 안되기 때문이다.
  - 조회된 세션이 없다면 이는 로그인한 것이 아니기 때문에 `home`으로 이동한다.

3-2. 스프링에서 제공하는 세션 조회 기능
```java
@GetMapping("/")
public String homeLoginV3Spring(
        @SessionAttribute(name = SessionConst.LOGIN_MEMBER, required = false) Member loginMember, Model model) {

    // 세션에 회원 데이터가 없으면 home으로 이동
    if (loginMember == null) {
        return "home";
    }

    // 세션이 유지되면 로그인으로 이동
    model.addAttribute("member", loginMember);
    return "loginHome";
}
```
- `@SessionAttribute`를 사용하면 `HttpServletRequest`를 사용해서 세션을 얻지 않아도 된다.
- 세션에 들어가는 Value 값을 파라미터에 지정하면 알아서 바인딩이 된다. 없으면 `null`로 바인딩된다.
- 이 기능은 세션을 생성하지 않는 단순 조회용 기능이다. 따라서 세션을 생성하는 로직에서는 사용하면 안된다.

## 첫 로그인 시 URL에 세션 ID가 포함되는 이유
서버 입장에서는 클라이언트의 브라우저가 쿠키를 지원하는지 안하는지 모른다. 따라서 처음 세션을 반환할 때,
URL에도 같이 정보를 담아서 반환한다. (맨 처음 이후로는 안함)

`http://localhost:8080/;jsessionid=F59911518B921DF62D09F0DF8F83F872`

따라서 항상 쿠키 방식으로만 세션을 유지하고 싶다면 `application.properties`에 속성 값을 줘야 한다.
```properties
server.servlet.session.tracking-modes=cookie
```
이렇게 하면 첫 세션 반환 시에도 URL에 전달하지 않고, **오로지 쿠키로만 전달한다.**

## 세션 정보와 타임아웃 설정
`HttpServletRequest`를 통해 얻은 세션에 대한 로그를 찍어보면 다양한 정보들을 출력해볼 수 있다.
- 세션 ID (`JSESSIONID` 값)
- 세션 유효 시간 (기준 값 : 초 ex. 1800(1800초 = 30분))
- 세션 생성일시
- 세션 갱신일시 (클라이언트가 세션으로 요청한 날짜 : 생성일시와 다름)
- 새로 생성된 세션인지 과거에 만들어져 조회된 세션인지 여부

```properties
session.setMaxInactiveInterval(1800); //1800초
```
- `application.properties`를 통해 세션의 타임아웃 시간을 설정할 수 있다.
- 세션을 통해 요청을 하면 타임아웃 시간이 다시 초기화된다. 설정한 시간 내에 세션을 통해 요청을 하지 않으면
WAS에서 내부적으로 해당 세션을 제거한다.

## 세션을 사용할 때 주의할 점(필독)
세션에는 최소한의 데이터만 보관되어야 한다. 왜냐하면 세션은 서버 내 메모리에서 관리하는 것이기 때문에
세션에서 저장되는 데이터 수가 많아질 수록 메모리 사용량은 늘어난다. 따라서 세션 내에 저장되는 값은
인증에 필요한 최소한의 값이어야 한다. 또한 세션의 시간을 너무 길게 가져가도 메모리 부족 문제가 발생할 수 있다.

실제 세션에 보관하는 데이터는 최소화해야 한다.(메모리 용량 관리 차원)

**따라서 로그인 관련 세션일 경우, 로그인에 필요한 1~3개 정도의 필드만 갖는 로그인용 객체를 따로 만든 뒤 그것을 세션에
저장해야 한다. (김영한님의 말씀)**

# 단축키 정보
- `컨트롤 + 알트 + P` : 메서드의 파라미터를 추가할 수 있다.
- `컨트롤 + 알트 + C(Constant)` : 특정 값을 final 상수 값으로 빼낼 수 있다.
