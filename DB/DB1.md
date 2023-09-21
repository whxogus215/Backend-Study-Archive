# Contents Of DB1
- [JDBC 이해](#jdbc-이해)
    - [JDBC의 등장 이유](#jdbc의-등장-이유)
    - [JDBC와 최신 데이터 접근 기술](#jdbc와-최신-데이터-접근-기술)
    - [JDBC를 활용한 CRUD](#jdbc를-활용한-crud)
- [커넥션 풀과 데이터 소스 이해](#커넥션-풀과-데이터-소스-이해)
  - [커넥션 풀 이해](#커넥션-풀-이해)
  - [데이터 소스 이해](#데이터-소스-이해)
    - [DriverManager](#drivermanager)
      - [기존 DriverManager와 DriverManagerDataSource의 차이점](#기존-drivermanager와-drivermanagerdatasource의-차이점)
      - [설정과 사용의 분리](#설정과-사용의-분리)
    - [커넥션 풀](#커넥션-풀)
  - [DataSource 활용](#datasource-활용)
- [트랜잭션 이해](#트랜잭션-이해)
  - [트랜잭션 개념](#트랜잭션-개념)
  - [트랜잭션 사용법](#트랜잭션-사용법)
  - [트랜잭션 DB 적용](#트랜잭션-db-적용)
- [DB 락](#db-락)
  - [DB 락 - 조회](#db-락-조회)
- [애플리케이션의 구조와 추상화의 필요성](#애플리케이션의-구조와-추상화의-필요성)
  - [애플리케이션의 전반적인 구조](#애플리케이션의-전반적인-구조)
  - [서비스 계층은 애플리케이션에서 가장 중요하며 변화에 민감하다](#서비스-계층은-애플리케이션에서-가장-중요하며-변화에-민감하다)
  - [기존 JDBC를 사용했을 때의 문제점](#기존-jdbc를-사용했을-때의-문제점)
  - [트랜잭션 추상화](#트랜잭션-추상화)
    - [스프링의 트랜잭션 추상화](#스프링의-트랜잭션-추상화)
    - [스프링 트랜잭션 동작원리 정리](#스프링-트랜잭션-동작-원리-정리)
  - [트랜잭션 탬플릿](#트랜잭션-템플릿)
    - [트랜잭션 템플릿 동작원리](#트랜잭션-템플릿-동작-원리) 
- [트랜잭션 AOP](#트랜잭션-aop)
  - [트랜잭션 어노테이션 테스트](#트랜잭션-어노테이션-테스트) 
- [테스트 코드 작성 Tip](#테스트-코드-작성-tip)
- [단축키 정리](#단축키-정리)


###### Reference
- **(main)** 인프런 김영한 스프링 DB 1편 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-1/dashboard

# JDBC 이해

## JDBC의 등장 이유
애플리케이션을 개발할 때 **중요한 데이터는 대부분 데이터베이스에 보관한다.** 웹 서비스이든 앱이든 서비스가 안정적으로
돌아가기 위해서는 클라이언트, 애플리케이션 서버 그리고 DB가 잘 갖추어져 있어야 한다. 따라서 애플리케이션 서버와 DB의
연결은 매우 중요하다.

![DB1](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/51210003-3961-4e5f-89fc-9471f13765bb)
![DB2](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/24afcbdd-821f-4b21-8015-888b62ddadf4)

이와 같이 서버와 DB는 다양한 절차를 거치며 데이터를 주고받게 된다. 이 때, DB가 바뀌게 되면 서버에 작성된 DB 접근 관련 코드를
바뀐 DB의 스펙에 맞게 다시 작성해야 한다. 또한 DB마다 커넥션 연결, SQL 전달, 결과를 받는 방법을 새로 학습해야 한다.

**이러한 문제를 해결하기 위해 표준화된 것이 JDBC이다.** JDBC 표준 인터페이스는 자바에서 DB에 접근할 수 있는
방법을 표준화한 자바 API이다. 즉, DB에 CRUD 하는 방법을 제공하는 것이다.

![DB3](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/1b4621d1-d529-4d1a-bf81-516b995ea5ba)

이처럼 애플리케이션 서버는 구체화된 DB와 연결되지 않고, 추상화된 인터페이스와 연결되어 있다. 이는 SOLID 원칙 중 [DIP](https://github.com/whxogus215/JavaBookArchive/blob/main/OOP-for-Spring/JavaAndOOP.md#ISP-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-%EB%B6%84%EB%A6%AC-%EC%9B%90%EC%B9%99)를 준수한다.
JDBC는 인터페이스이기 때문에 이 자체로는 동작하지 않는다. 따라서 이 JDBC에서 정의한 기능들을 직접 구현하는 것은 DB 벤더(회사)의 몫이다.
따라서 각각의 DB 회사들은 자신들의 DB에 맞게 구현한 라이브러리를 제공한다. 이를 **JDBC 드라이버**라고 한다. MySQL JDBC 드라이버라면
MySQL DB에 접근할 수 있는 라이브러리이다. **드라이버는 DB가 아니다! 드라이버는 DB와 연결될 수 있도록 돕는 라이브러리에 불과하다.
(JDBC의 구현체)** JDBC 드라이버 뿐만 아니라 실제 DB에 대한 설계가 있어야 한다.

![DB4](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/e31c361e-f132-4c71-ac98-73fc6b225c1a)

### 핵심 정리
JDBC로 인해 다음과 같은 문제가 해결되었다.
1. DB가 변경될 때마다 서버에서 **DB 관련 코드를 매번 변경해야 하는 문제**
   - **서버의 애플리케이션 로직은 이제 추상화된 JDBC라는 표준 인터페이스에 의존**하기 때문에 DB가 변경되면 구현체만 바꾸면 된다.
   왜냐하면 **서버의 로직은 JDBC에서 제공하는 코드들로 작성되었기 때문에 DB 구현체의 변동에 영향을 받지 않기 때문이다.**
2. 개발자가 각각의 DB마다 커넥션, SQL 응답 및 결과에 관한 **방법을 새로 학습해야 하는 문제**
   - **개발자는 JDBC 표준 인터페이스의 사용법만 학습하면 된다. 이것만 알면 여러 DB와 연결하는 것에 동일하게 적용할 수 있기 때문이다.**

> 물론 DB마다 사용법이 다른 부분이 분명 존재한다. 즉, JDBC가 표준화를 통해 많은 편리함을 제공하지만 표준화하지 못한 부분도 존재한다.
> 따라서 JDBC 코드는 변경하지 않아도 되지만, SQL 자체를 다르게 써야할 수도 있다. 이를 해결하는 방법이 JPA이며, 매번 SQL을 달리 정의하지 않아도 된다.

## JDBC와 최신 데이터 접근 기술
앞서 JDBC를 통해 많은 것이 표준화됨으로써 개발자의 수고를 덜 수 있다는 장점에 대해 알아보았다. 하지만 **JDBC는
1997년에 출시된 만큼 매우 오래된 기술이고, 사용방법도 복잡하다.** 따라서 최근에는 JDBC를 직접 사용하기 보다는
JDBC를 편리하게 사용할 수 있는 기술들을 사용하는 편이다. 대표적으로 **SQL Mapper와 ORM 기술**이 있다.

![DB5](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/36c8fa9e-15b5-4633-a2d7-c98a22f9e802)

**JPA는 자바 진영의 인터페이스이며, 이를 구현한 것이 존재해야 한다. 대표적인 기술로 하이버네이트(제일 많이 사용), 이클립스 링크 등이 있다.**

### SQL Mapper VS ORM 기술
- SQL Mapper : SQL만 작성할 줄 알면 나머지는 SQL Mapper가 알아서 해준다. **(SQL 알아야 함)**
- ORM : SQL 자체를 작성하지 않아도 클래스를 작성하면 알아서 DB의 테이블과 매핑되기 때문에 기존에 컬렉션(배열, 맵 등)에
데이터를 저장하는 방식으로 메서드를 사용하기만 해도 DB 접근이 가능하다. **(SQL 몰라도 됨 - 다만 기술이 복잡해서 학습 난이도 높음)**

### 핵심 정리
SQL Mapper와 ORM 둘 다 JDBC를 사용하는데 있어서 편리함을 제공해주는 기술들이다. **즉, 어떤 기술을 사용하더라도
JDBC에 대한 이해가 반드시 있어야 하는 것이다.** 위 그림에서 알 수 있듯이 결국 DB와 접근하기 위해서는 JDBC를 거치기 때문이다.
SQL Mapper와 ORM은 그저 서버 어플리케이션과 가까이 있고, 개발자의 편리함에 초점을 맞춘 고수준 기술이기 때문이다.
**즉, DB에 접근하기 위한 다양한 기술들이 존재하지만 핵심은 JDBC라는 것이다.**

## JDBC를 활용한 CRUD
애플리케이션과 데이터베이스를 연결하는 과정이 필요하다.
```java
// DB 연결에 대한 정보들을 담고 있기 때문에 객체로 생성되면 안된다. 따라서 추상 클래스로 생성한다.
public abstract class ConnectionConst {
    public static final String URL = "jdbc:h2:tcp://localhost/~/test";
    public static final String USERNAME = "sa";
    public static final String PASSWORD = "";
}
```
- DB 연결과 관련된 정보들이 들어있으며, 추상 클래스 및 final 필드로 구성되어 있다. (수정 및 객체 생성 방지)
- 필드들이 static이므로 DB 연결 시 자유롭게 상수를 사용할 수 있다.

```java
@Slf4j
public class DBConnectionUtil {

    public static Connection getConnection() {
        try {
            Connection connection = DriverManager.getConnection(URL, USERNAME, PASSWORD);
            log.info("get connection={}, class={}", connection, connection.getClass());
            return connection;
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```
JDBC가 제공하는 `DriveManager`를 사용하면 라이브러리에 존재하는 DB의 드라이버를 찾아서 해당 드라이버에 있는 커넥션(구현체)을
반환한다. 앞서 드라이버는 JDBC 인터페이스를 구현한 라이브러리라고 했다. 따라서 해당 코드는 JDBC가 제공하는 Connection 타입이지만
**실제로 반환되는 것은 H2 DB의 커넥션을 반환한다.**

```java
url=jdbc:h2:tcp://localhost/~/test user=SA, class=class org.h2.jdbc.JdbcConnection
```
실제 출력문을 보면 알 수 있듯이 반환데는 커넥션의 실제 타입은 h2에서 제공하는 JDBC 커넥션이다.
> 실제 반환한 JdbcConnection은 구현 클래스이며, JDBC의 Connectionin 인터페이스를 구현하였다.

![DB1](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/4a035630-35c4-4162-a31e-713b3c21f513)

![DB2](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/ca0bcc49-52cc-4730-8ece-b95368d176f9)

![DB3](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/a8099ea2-8192-460b-aaf7-4591ffa84b62)

1. 애플리케이션 로직은 JDBC가 제공하는 `DriverManager.getConnection(URL, USERNAME, PASSWORD);`을 통해 커넥션을 요청한다.
2. `DriverManager`는 라이브러리에 등록된 드라이버 목록을 자동으로 인식한다. 즉, 드라이버들에게 URL, USERNAME, PASSWORD를 전달하여
알맞은 드라이버를 찾는다. 우리의 URL은 `jdbc:h2`로 시작하기 때문에 h2 데이터베이스에 접근하고자 하는 것임을 알 수 있다.
3. 이로 인해 찾게된 커넥션 구현체(JdbcConnection)를 어플리케이션 서버(클라이언트)에 반환한다.
4. **당연하게도 특정 DB를 추가로 사용하기 위해서는 build.gradle에 해당 DB의 드라이버를 등록해야 한다.**

### 데이터 저장하기 (Create)
```java
@Data
public class Member {

    private String memberId;
    private int money;

    public Member() {
    }

    public Member(String memberId, int money) {
        this.memberId = memberId;
        this.money = money;
    }
}
```
H2 DB에 있는 Member 테이블에 들어가는 데이터를 정의한 것이다. **JDBC를 통해 해당 객체를 데이터베이스에 저장할 수 있다.**

```java
@Slf4j
public class MemberRepositoryV0 {

    public Member save(Member member) throws SQLException {
        String sql = "insert into member(member_id, money) values (?,?)";

        Connection con = null;
        PreparedStatement pstmt = null;

        try {
            con = getConnection();
            pstmt = con.prepareStatement(sql);
            // sql 쿼리문의 ? 부분을 바인딩하는 코드
            pstmt.setString(1, member.getMemberId());
            pstmt.setInt(2, member.getMoney());
            pstmt.executeUpdate();
            return member;
        } catch (SQLException e) {
            log.error("db error", e);
            throw e;
        } finally { // TCP/IP를 통해 외부 리소스와 연결되었기 때문에 사용이 종료되면 사용한 역순으로 닫아줘야 된다.
            // finally 블록을 사용해야 예외에 상관없이 진행될 수 있다.
            close(con, pstmt, null);
        }

    }

    private void close(Connection con, Statement stmt, ResultSet rs) {

        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                log.info("error", e);
            }
        }

        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException e) {
                log.info("error", e);
            }
        }

        if (con != null) {
            try {
                con.close();
            } catch (SQLException e) {
                log.info("error", e);
            }
        }
    }

    private static Connection getConnection() {
        return DBConnectionUtil.getConnection();
    }
}
```
DB와 연결되기 위해서는 `커넥션, SQL 전달(Statement), 결과 반환(Result Set)`의 과정을 거친다. 위 코드에서도
이 세 가지에 대한 코드가 작성되어 있다. SQL을 전달하기 위해 문자열로 쿼리문을 작성해야 한다. JDBC의 Statement 인터페이스의
자식인 `PreparedStatement` 인터페이스를 사용하면 쿼리문의 ? 부분을 바인딩할 수 있다.
> Statement가 인터페이스인 이유도 Connection과 마찬가지로 여러 DB 드라이버 구현체를 받을 수 있기 때문이다.

커넥션, Statement를 처음 null로 하고, try-catch 안에 인스턴스를 생성하는 이유는 이 과정에서 예외가 발생할 수 있기 때문이다.
즉, **커넥션 연결 혹은 SQL 전달 과정에서 예외가 발생할 수 있고, 이를 처리하기 위한 과정이 필요하다.** 예외가 발생할 경우,
try 블럭은 아예 실행이 되지 않기 때문이다. 그리고 커넥션과 Statement 그리고 ResultSet은 외부 리소스를 사용한 것이기 때문에
사용을 마치면 사용한 순서의 역순으로 리소스를 닫아야 한다.(close) 따라서 예외와 상관없이 finally 블럭을 통해 리소스를 닫아준다.
**커넥션이 종료되지 않고 계속 유지되는 문제가 발생하면 후에 커넥션 부족으로 장애가 발생할 수 있다.**

> 커넥션을 획득한 다음, 이를 통해 Statement가 생성되는 것이기 때문에 닫을 때는 이의 역순으로 해야하는 것이 맞다. 

`pstmt.executeUpdate()` : Statement 를 통해 준비된 SQL을 커넥션을 통해 실제 데이터베이스에 전달
한다. 참고로 `executeUpdate()` 은 int 를 반환하는데 영향받은 DB row 수를 반환한다. 여기서는 하나
의 row를 등록했으므로 1을 반환한다.

### 데이터 조회하기 (Read)
```java
public Member findById(String memberId) throws SQLException {
        String sql = "select * from member where member_id = ?";

        Connection con = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            con = getConnection();
            pstmt = con.prepareStatement(sql);
            pstmt.setString(1, memberId);

            rs = pstmt.executeQuery();
            if (rs.next()) {
                Member member = new Member();
                member.setMemberId(rs.getString("member_id"));
                member.setMoney(rs.getInt("money"));
                return member;
            } else {
                throw new NoSuchElementException("member not found memberId=" + memberId);
            }

        } catch (SQLException e) {
            log.error("db error", e);
            throw e;
        } finally {
            close(con, pstmt, rs);
        }

    }
```
데이터를 가져올 때는 ResultSet이 사용된다. 데이터를 변경할 때는 `executeUpdate()`를 사용하지만, 단순히 조회할 때는
`executeQuery()`를 사용한다. 이는 ResultSet을 반환한다. 가져온 데이터를 객체로 변환하기 위해 새로 Member를 만든 뒤,
ResultSet의 컬럼의 값을 가져와 설정한다. (DB의 member_id와 객체 Member의 memberId는 서로 호환되지 않음)

![DB4](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/ad5be060-2f2a-48e4-bc9e-b13480cb3ec2)

이처럼 초기의 cursor는 데이터를 가리키고 있지 않기 때문에 최소 1번은 커서를 이동시켜야 한다.(컬렉션의 이터레이터와 비슷)

### 데이터 수정 및 삭제하기 (Update & Delete)
```java
public void update(String memberId, int money) throws SQLException {
        String sql = "update member set money = ? where member_id=?";

        Connection con = null;
        PreparedStatement pstmt = null;

        try {
            con = getConnection();
            pstmt = con.prepareStatement(sql);
            pstmt.setInt(1, money);
            pstmt.setString(2, memberId);
            int resultSize = pstmt.executeUpdate();
            log.info("resultSize={}", resultSize);
        } catch (SQLException e) {
            log.error("db error", e);
            throw e;
        } finally {
            close(con, pstmt, null);
        }
    }

    public void delete(String memberId) throws SQLException {
        String sql = "delete from member where member_id=?";

        Connection con = null;
        PreparedStatement pstmt = null;

        try {
            con = getConnection();
            pstmt = con.prepareStatement(sql);
            pstmt.setString(1, memberId);
            pstmt.executeUpdate();
        } catch (SQLException e) {
            log.error("db error", e);
            throw e;
        } finally {
            close(con, pstmt, null);
        }
    }
```
`executeUpdate()`는 쿼리를 실행하고 Update된 row(행)의 수를 반환한다.

```java
MemberRepositoryV0 repository = new MemberRepositoryV0();

    @Test
    void crud() throws SQLException {
        // save
        Member member = new Member("memberV5", 10000);
        repository.save(member);

        // findById
        Member findMember = repository.findById(member.getMemberId());
        log.info("findMember={}", findMember);
        assertThat(findMember).isEqualTo(member);

        // update money : 10000 -> 20000
        repository.update(member.getMemberId(), 20000);
        Member updatedMember = repository.findById(member.getMemberId());
        assertThat(updatedMember.getMoney()).isEqualTo(20000);

        // delete : 람다식 사용
//        repository.delete(member.getMemberId());
//        assertThatThrownBy(() -> repository.findById(member.getMemberId()))
//                .isInstanceOf(NoSuchElementException.class);
            
        // delete : 람다식 사용 X, 삭제를 구현함으로써 반복적인 테스트 실행이 가능해졌다.
        ThrowableAssert.ThrowingCallable throwingCallable = new ThrowableAssert.ThrowingCallable() {
            @Override
            public void call() throws Throwable {
                repository.findById(member.getMemberId());
            }
        };

        repository.delete(member.getMemberId());
        assertThatThrownBy(throwingCallable)
                .isInstanceOf(NoSuchElementException.class);
    }
```
이처럼 삭제까지 구현을 완료함으로써 테스트를 진행할 때, CRUD라는 싸이클을 돌게 된다. 즉 데이터가 추가되었다가 삭제되기 때문에
**반복적으로 테스트가 가능하다.(테스트를 반복적으로 하는 것은 매우 중요!)** 이는 추후 트랜잭션 기능을 통해 해결이 가능하다.
> 삭제에 대한 검증은 삭제를 한 뒤에 다시 조회를 했을 때 발생하는 예외가 실제로 발생하는지(터지는지) 확인하면 된다.
> : `assertThatThrownBy()`


### 핵심 정리
**JDBC를 통해 진행되는 CRUD의 공통점은 **커넥션, SQL 전달(Statement), ResultSet**이다. 또한 직접 SQL문을 작성해서
넘겨주는 것이다.** 여기서 알 수 있는 단점은 CRUD에 대한 메서드들이 공통적으로 갖는 코드가 있다. 즉 코드가 반복되는 부분이 있다는 것이다.
또한 커넥션, Statement, ResultSet에 대한 예외처리가 반드시 있어야 한다는 것이다. 이는 개발자에게 있어서
번거로운 작업 중 하나이다. 이것이 바로 초기 JDBC가 갖는 문제점이다. **안심할만한 점은 현 개발시장에서 이렇게 코드를
작성하지는 않는다.**

# 커넥션 풀과 데이터 소스 이해

## 커넥션 풀 이해
![DB1](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/d6735a00-9046-4037-a674-835d5054aefb)

애플리케이션은 사용자가 많아질수록 그만큼 요청할 SQL문도 많아진다. 따라서 매번 커넥션을 획득해서 실행하는 것은 굉장히 시간이
많이 소요될 것이다. 기본적으로 커넥션을 확보하는 시간은 ms 정도로 매우 빠르다. 하지만 그럼에도 커넥션을 매번 획득하는 것은
비효율적이다. **이를 해결하는 방법이 커넥션을 미리 생성해서 보관하는 커넥션 풀을 이용하는 것이다.** 애플리케이션 서버가 실행되었을 때
바로 DB와 연결하여 커넥션을 가져온다. 그리고 이를 서버 내 커넥션 풀에 보관한다.

![DB2](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/16ffc7c7-df80-475e-bc08-2ab71a375b19)

**이렇게 확보된 커넥션들은 이미 DB와 TCP/IP를 통해 연결하고 인증이 완료된 것들이기에 언제든지 바로바로 SQL문을 전달할 수 있다.**

![DB3](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/82c1044c-83ee-42fd-ab88-80416e12944d)

### 정리
커넥션 풀의 개수는 애플리케이션 서버 스펙, DB 서버 스펙에 맞게 성능 테스트를 진행하여 결정해야 한다. 또한 **커넥션의 개수의 상한선을 지정함으로써
DB에 무한정 연결되는 것을 막는 효과도 있다.** 이처럼 성능 향상 및 보호의 효과가 있기에 커넥션 풀은 **실무에서 기본으로 사용된다.** 오픈소스인
커넥션 풀을 주로 사용하며, 대표적으로 `HikariCP`가 있다. 스프링 부트 2.0 부터는 기본 커넥션 풀로 `HikariCP`를 제공한다. 따라서 어떠한 DB를 사용하더라도
터미널에서 HikariCP가 뜨는 이유가 바로 이것이다. **스프링부트 서버가 실행됨에 따라 DB와 연결을 통해 커넥션을 커넥션 풀에 저장하는 과정에서 HikariCP가
실행되기 때문이다.**

## 데이터 소스 이해
이제 커넥션을 사용하는 방법은 크게 2가지로 나뉠 수 있다. 앞서 설명한 JDBC의 `DriverManager`를 사용하여 매번 커넥션을 얻을 수 있고,
커넥션 풀을 사용하여 미리 생성된 커넥션을 사용할 수도 있다. 

![DB4](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/f7ae3a71-5c05-4ac8-9c72-0611e9a2a1e4)

**이처럼 애플리케이션 로직이 DriverManager와 커넥션 풀을 추상화한 것에 의존하지 않기 때문에 방법을 변경할 경우 로직 전체에 코드 수정이 필요하다.**

![DB5](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/86d62823-9f12-413d-abfe-d95121af1d72)

자바에서는 이처럼 커넥션을 획득하는 방법들을 **추상화한 인터페이스 DataSource를 제공한다.**

```java
public interface DataSource {
 Connection getConnection() throws SQLException;
}
```
- DataSource는 이처럼 **커넥션을 얻는 방법에 대한 기능을 정의하고 있다.**

### 정리
`DataSource`는 **커넥션을 얻기 위한 방법을 추상화한 인터페이스이다.** 대부분의 커넥션 풀은 이 `DataSource`를 구현해두었다. 따라서
애플리케이션 로직은 `DataSource` 인터페이스만 의존하는 코드로 작성된다. 또한 `DriverManger`도 스프링에서 제공하는 `DriverManagerDataSource`라는 클래스를
사용하면 `DataSource` 인터페이스에 의존하도록 코드를 작성할 수 있다. (`DriverManagerDataSource`는 `DataSource` 인터페이스를 구현한 클래스이다.)

### DriverManager
```java
    @Test
     void dataSourceDriverManager() throws SQLException {
         //DriverManagerDataSource - 항상 새로운 커넥션 획득
         DriverManagerDataSource dataSource = new DriverManagerDataSource(URL, USERNAME, PASSWORD);
         useDataSource(dataSource);
     }

    private void useDataSource(DataSource dataSource) throws SQLException {
        Connection con1 = dataSource.getConnection();
        Connection con2 = dataSource.getConnection();
        log.info("connection={}, class={}", con1, con1.getClass());
        log.info("connection={}, class={}", con2, con2.getClass());
    }
```
이처럼 `DriverManagerDataSource`를 사용하면 `DataSource` 인터페이스 형태로 주입받아 사용할 수 있다.

### 기존 DriverManager와 DriverManagerDataSource의 차이점
```java
/// JDBC의 DriverManager 사용
DriverManager.getConnection(URL, USERNAME, PASSWORD)
DriverManager.getConnection(URL, USERNAME, PASSWORD)

/// 스프링 부트의 DriverManagerDataSource 사용
@Test
void dataSourceDriverManager() throws SQLException {
     DriverManagerDataSource dataSource = new DriverManagerDataSource(URL, USERNAME, PASSWORD);
     useDataSource(dataSource);
}

private void useDataSource(DataSource dataSource) throws SQLException {
    Connection con1 = dataSource.getConnection();
    Connection con2 = dataSource.getConnection();
    log.info("connection={}, class={}", con1, con1.getClass());
    log.info("connection={}, class={}", con2, con2.getClass());
}
```
JDBC의 `DriverManager`는 커넥션을 획득할 때마다 파라미터인 `USER, USERNAME, PASSWORD` 를 계속 넘겨야 된다.
하지만 스프링 부트의 `DriverManagerDataSource`는 초기에 한 번만 파라미터(설정 값)를 넘기고, 이후 커넥션을 획득할 때는
`getConnection()`만 호출하면 된다.

### 설정과 사용의 분리
- 설정 : `DataSource`를 만들고 필요한 속성들을 사용해서 `URL , USERNAME , PASSWORD` 같은 부분을 입력하
  는 것을 말한다. 이렇게 설정과 관련된 속성들은 한 곳에 있는 것이 향후 변경에 더 유연하게 대처할 수 있다.
- 사용 : 설정은 신경쓰지 않고, `DataSource` 의 `getConnection()` 만 호출해서 사용하면 된다.

- DataSource가 만들어지는 시점에서 최초로 한 번만 설정값을 넘긴 후, 이후 커넥션을 획득할 때는 `getConnection()`만 호출하면 된다.
- **즉, Repository는 `DataSource`만 의존하고, 설정 값에 대한 정보를 몰라도 된다.**
- 이것이 중요한 이유는 `DataSource`를 사용하는 곳이 여러 곳일 수 있기 때문에 설정과 사용을 분리하는 것이 필요하다. 


### 커넥션 풀
```java
@Test
void dataSourceConnectionPool() throws SQLException, InterruptedException {
     //커넥션 풀링: HikariProxyConnection(Proxy) -> JdbcConnection(Target)
     HikariDataSource dataSource = new HikariDataSource();
     
     dataSource.setJdbcUrl(URL);
     dataSource.setUsername(USERNAME);
     dataSource.setPassword(PASSWORD);
     dataSource.setMaximumPoolSize(10);
     dataSource.setPoolName("MyPool");
     
     useDataSource(dataSource);
     Thread.sleep(1000); //커넥션 풀에서 커넥션 생성 시간 대기
}
```
- HikariCP 커넥션 풀을 사용한다. 이는 `DataSource` 인터페이스를 구현하고 있다. 커넥션 풀에서 커넥션을 생성하는
작업은 별도의 쓰레드에서 진행된다. 따라서 어플리케이션 실행 속도에 영향을 주지 않는다. 이처럼 `Thread.sleep()`을 하지 않으면
커넥션이 다 생성되기 전에 테스트가 종료되어 버린다.

## DataSource 활용
```java
@Slf4j
public class MemberRepositoryV1 {
     private final DataSource dataSource;
     
     public MemberRepositoryV1(DataSource dataSource) {
        this.dataSource = dataSource;
     }
     
     //save()...
     //findById()...
     //update()....
     //delete()....
        
     private void close(Connection con, Statement stmt, ResultSet rs) {
         JdbcUtils.closeResultSet(rs);
         JdbcUtils.closeStatement(stmt);
         JdbcUtils.closeConnection(con);
     }
     
     private Connection getConnection() throws SQLException {
         Connection con = dataSource.getConnection();
         log.info("get connection={}, class={}", con, con.getClass());
         return con;
     }
}
```
- `DataSource` 의존관계 주입
  - 외부에서 `DataSource`를 주입 받아서 사용한다.
  - `DataSource`는 인터페이스이므로, 구현체가 `DriverManagerDataSource`에서 `HikariDataSource`로 변경되어도
  전체 로직을 변경할 필요가 없다. (**DI + OCP**)
- `JdbcUtils` 편의 메서드
  - 스프링은 JDBC를 편리하게 다룰 수 있는 `JdbcUtils` 라는 편의 메서드를 제공한다.
  - `JdbcUtils`를 사용하면 커넥션 및 기타 리소스를 닫는 메서드를 제공한다.

# 트랜잭션 이해

## 트랜잭션 개념
데이터를 단순 파일이 아닌 데이터베이스라는 공간에 저장하는 이유 중 하나가 바로 **트랜잭션**이다. 트랜잭션이란 말그대로 거래이다.
**즉, 하나의 거래가 안전하게 처리하도록 보장해주는 것을 뜻한다.** 트랜잭션은 커밋 혹은 롤백으로 구성되어 있다.

만약, 계좌이체를 해야 하는 상황에서 A라는 곳에서 2000원을 빼고, B라는 곳에 2000원을 넣어야 한다면 둘 중 하나만 성공해서는
안된다. **둘 다 성공하거나, 둘 다 실패해야 한다.** 트랜잭션 기능을 사용하면 이것이 가능하다. 모든 작업이 성공적으로 동작하여
최종적으로 데이터베이스에 반영하는 것을 **커밋(commit)**, 하나라도 실패해서 아예 처음으로 돌아가는 것을 **롤백(rollback)** 이라고 한다.

### 트랜잭션 ACID
트랜잭션은 **원자성(Atomicity),일관성(Consistency),격리성(Isolation),지속성(Durability)**을 보장해야 한다.
- **원자성** : 하나의 트랜잭션 내에서 실행된 작업들은 모두 성공하거나 모두 실패해야 하는 하나의 작업 단위로 이루어져야 한다.
- **일관성** : 모든 트랜잭션은 일관된 DB 상태를 유지해야 한다. DB의 무결성 조건이 하나의 예시이다.
- **격리성** : 동시에 같은 데이터를 수정하지 못하도록, 서로가 영향을 주지 않아야 한다. 이는 성능에 영향을 주는 성질이다.
- **지속성** : 트랜잭션이 성공적으로 끝나면, 그 결과가 항상 기록으로 남아야 한다. 그래야 트랜잭션 내용을 로그를 통해 복구할 수 있다.

#### 트랜잭션의 격리 수준 - Isolation Level
1. READ UNCOMMITED(커밋되지 않은 읽기)
2. **READ COMMITTED(커밋된 읽기)**
3. REPEATABLE READ(반복 가능한 읽기)
4. SERIALIZABLE(직렬화 가능)

1번에서 4번으로 갈수록 격리성이 엄격해지며, 이에 따라 동시 처리 성능은 나빠진다. 

![DB1](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/41d6a378-db68-4f8c-93ec-914a81dbb834)

![DB2](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/32c6571d-28b0-4ad7-a3e4-37bb9a9e623f)

**하나의 커넥션은 하나의 세션과 연결되며, DB에 연결될 때 세션 ID가 다를 경우, 서로 다른 클라이언트로 분류된다.**

## 트랜잭션 사용법
1. 데이터 변경 쿼리를 실행한 뒤, 이를 반영하려면 `commit`을 호출한다. 만약, 이를 반영하고 싶지 않으면 `rollback`을 수행한다.
2. `commit`을 하기 전까지는 데이터를 **임시로 저장하는 것이다.** 즉, `commit`을 하기 전까지는 해당 트랜잭션을 시작한
세션(사용자)에게만 변경 데이터가 보이고, 다른 세션(사용자)에게는 보이지 않는다.
3. 트랜잭션을 사용하기 위해서는 **자동 커밋**이 아닌 **수동 커밋**을 사용해야 한다.

- 자동 커밋 : 쿼리를 하나하나 실행할 때 마다 자동으로 커밋이 된다. 따라서 트랜잭션 기능(커밋, 롤백)을 사용할 수 없다.
- 수동 커밋 : 수동 커밋을 설정한다는 것은 **트랜잭션을 시작하는 것과 같다.** 수동 커밋, 즉 트랜잭션을 사용할 경우
데이터 변경 시 `commit` 혹은 `rollback`을 반드시 호출해야 한다.

### Rollback을 사용하는 이유
쿼리문을 작성하다 보면 잘못된 쿼리를 실행하거나, 혹은 특정 쿼리에 오타로 인해 특정 쿼리만 실행이 안될 수가 있다.
그러면 앞서 말한 것처럼 트랜잭션의 한 단위 만큼의 수행에 원자성이 어긋나게 된다. **즉, 트랜잭션을 시작하기 전 상태로
돌아가야만 한다.** 따라서 이러한 경우에 **Rollback**을 사용해야 한다.

## DB 락
동기화 문제에서 **Critical Section**에 대한 동시 접근을 막기 위해 해당 자원에 대한 Lock을 얻는 방법을 사용한다.
DB에서도 동시에 같은 Row(테이블의 행)에 접근하게 되면, **서로 다른 값을 수정하게 됨으로써 데이터가 서로 일치하지 않는 문제가 발생한다.**
 따라서 트랜잭션을 시작하고 마칠 때까지는 다른 세션에서 접근할 수 없도록 **Lock(락)** 을 얻어야 한다.
### DB 락 그림 예시
![DB1](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/adf9dea0-246b-4c69-9276-ac3b3a93af3d)

![DB2](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/44143843-5f64-4943-8a70-f400fe7108d7)

![DB3](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/111c6727-4090-458d-801d-14038259d79c)

이처럼 트랜잭션을 시작하여 쿼리문을 실행하기 위해서는 **락을 먼저 얻어야 한다.** 만약 락이 없는 상태에서 쿼리문을
수행하게 되면 **락을 얻을 때까지 대기해야 한다.** 만약 락을 얻은 세션이 커밋을 함으로써 트랜잭션을 종료하면 락을 반납하게 된다.
그리고 그 다음으로 락을 얻은 세션은 방금 실행했던 쿼리문이 정상 동작하게 된다.

### 정리
특정 Row에 접근하기 위해서는 Lock을 얻어야 하며, Lock이 없다면 대기해야 한다. 또한 `Commit` 혹은 `Rollback`을 통해
트랜잭션이 종료되면 Lock을 반납하게 된다.

### DB 락 조회
보통 데이터를 조회하는 SELECT문을 사용할 때는 락이 없어도 가능하다. 데이터를 변경하는 것이 아니기 때문에 큰 문제가 없다.
하지만 데이터를 조회하여 특정 계산을 해야할 경우, 이 과정에서는 데이터가 변경되어서는 안된다. **즉, 조회하는 과정에서
Lock을 얻는다면 특정 값에 대한 조회가 끝날 때까지 누구도 해당 값을 변경할 수 없다.**
> 편의점 정산으로 예를 들면 이해하기 쉬울 것이다. 마감을 하고 최종적으로 포스기 정산을 하는데 이 때 조회하는 값들은
> 변경되어서는 안되는 값들이다.(금일 판매 총액, 재고 등) **이처럼 조회한 값으로 무언가를 해야할 때 값이 변경되는 것을 막기 위해
> 조회하는 과정에서 Lock을 얻는 것이다.**

![DB4](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/695bbd11-697b-4deb-b69e-659586cd4242)

```sql
set autocommit false;
select * from member where member_id='memberA' for update;
```
조회하는 과정에서 Lock을 얻으려면 다음과 같이 `for update`를 붙여야 한다. 그러면 트랜잭션이 종료될 때(`commit`, `rollback`) Lock을 반납하게 된다.

## 트랜잭션 DB 적용
테스트 코드를 작성하여 실행하는 경우, 보통 트랜잭션을 활용하면 유용하다. 테스트에 사용되는 데이터가 DB에 들어간 상태로
커밋이 되면, 실제 DB에 영향을 주기 때문이다. 따라서 테스트가 다 끝나면 Rollback을 하여 트랜잭션 시작 전 상태로 돌아가면
원래 DB에 영향을 주지 않고 테스트를 안전하게 얼마든지 진행할 수 있다.

![DB5](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/95ae2561-7d74-4d0e-b604-cd403e006cfb)

물론 이처럼 커넥션을 파라미터로 전달하는 방식은 번거롭다. 실제로는 이러한 방식을 사용하지 않는다.

```java
/*
* 트랜잭션 - 파라미터 연동, 풀을 고려한 종료
*/
@Slf4j
@RequiredArgsConstructor
public class MemberServiceV2 {

    private final DataSource dataSource;
    private final MemberRepositoryV2 memberRepository;

    public void accountTransfer(String fromId, String toId, int money) throws SQLException {
        Connection con = dataSource.getConnection(); // 서비스 계층에서 Connection을 얻어 Repository 계층에 전달한다.
        try {
            con.setAutoCommit(false); // 트랜잭션 시작
            // 비즈니스 로직 수행
            bizLogic(con, fromId, toId, money);
            con.commit(); // 성공 시 커밋
        } catch (Exception e) {
            con.rollback(); // 실패 시 롤백
            throw new IllegalStateException(e);
        } finally {
            release(con);
        }
    }

    private void bizLogic(Connection con, String fromId, String toId, int money) throws SQLException {
        Member fromMember = memberRepository.findById(con, fromId);
        Member toMember = memberRepository.findById(con, toId);

        memberRepository.update(con, fromId, fromMember.getMoney() - money);
        validation(toMember);
        memberRepository.update(con, toId, toMember.getMoney() + money); // validation 검증에 실패하면 해당 로직이 수행되지 않음
    }

    private static void release(Connection con) {
        if (con != null) {
            try {
                con.setAutoCommit(true); // 풀에 반납할 때는 오토커밋으로 변경하고 반납해야 한다. - 커넥션 풀을 안 쓰면 상관X
                con.close();
            } catch (Exception e) {
                log.info("error", e);
            }
        }
    }

    private static void validation(Member toMember) {
        if (toMember.getMemberId().equals("ex")) {
            throw new IllegalStateException("이체 중 예외 발생");
        }
    }
}
```
**이처럼 트랜잭션의 시작과 끝은 서비스 계층에서 담당하게 된다.** 따라서 커넥션을 얻는 것도 서비스 로직에서부터 시작된다.
만약 중간에 예외가 발생한다면 로직이 중간에 끊기는 것이므로 **Rollback을 해줘야 한다.** 그게 아니라면 **Commit을 해야하고,**
Rollback 과 Commit 둘 다 트랜잭션이 종료되면 리소스를 종료해야 한다. **따라서 try-catch-finally문이 사용된 것이다.**
이 때 주의해야할 점은 커넥션을 닫을 때, **AutoCommit**을 True로 해야 한다. 이 커넥션은 커넥션 풀로 반환되는 것이기 때문에
트랜잭션 시작할 때 설정한 AutoCommit을 다시 활성화 시켜야 한다. 모든 DB의 기능들은 AutoCommit을 기반으로 진행된다.
**따라서 트랜잭션을 다 사용하였다면 AutoCommit을 활성화 시켜야 하는 것이다.**

### 정리
이처럼 try-catch-finally 문을 사용하면 로직 성공 시 Commit, 로직 실패 시 Rollback, 트랜잭션 종료 시 커넥션 닫기를 수행할 수 있다.
다만 이는 매우 번거롭고 실제 서비스 로직에서 많은 부분을 차지한다. 위 코드를 살펴보면 실제 비즈니스 로직보다 트랜잭션 관련 코드가
더 많다. **따라서 스프링을 활용하여 이러한 문제들을 해결해보도록 하자. (프레임 워크가 존재해야만 하는 이유이다.)**


# 애플리케이션의 구조와 추상화의 필요성

## 애플리케이션의 전반적인 구조

![DB1](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/42b97805-97e1-42a4-b782-d5956d5a85be)

애플리케이션은 크게 컨트롤러(프레젠테이션) - 서비스(서비스) - 저장소(데이터 접근) - DB로 나뉜다.
- 프레젠테이션 계층
  - **UI**와 관련된 처리 담당
  - **웹 요청과 응답**
  - 사용자 **요청을 검증**
  - HTTP 서블릿 같은 웹 기술, 스프링 MVC
- 서비스 계층
  - **비즈니스 로직**을 담당
  - 주로 **순수 자바 코드**로 작성
- 데이터 접근 계층
  - 실제 DB에 접근하는 코드
  - **JDBC, JPA, Redis, Mongo 등** 여러 기술들이 사용되는 계층이다.

## 서비스 계층은 애플리케이션에서 가장 중요하며 변화에 민감하다
애플리케이션이 발전함에 따라 UI 및 데이터 저장 기술이 변화하더라도 서비스 계층에 있는 **비즈니스 로직**은
변경되어서는 안된다. 이들에게 종속될 경우, 유지보수에 많은 비용이 들기 때문이다. 따라서 클라이언트가 접근하는
UI 기술 같은 것이 변경된다면 **프레젠테이션 계층의 코드만 변경되어야 하며**, JDBC에서 JPA로 기술을 변경하더라도
**데이터 접근 계층 코드만 변경되어야 한다.** 따라서 서비스 계층은 데이터 접근 계층에 의존할 때, **추상화 된 인터페이스
에 의존해야 한다.** 스프링은 이러한 추상화를 지원한다.

## 기존 JDBC를 사용했을 때의 문제점
```java
@RequiredArgsConstructor
public class MemberServiceV1 {
 private final MemberRepositoryV1 memberRepository;
 
 public void accountTransfer(String fromId, String toId, int money) throws SQLException {
   Member fromMember = memberRepository.findById(fromId);
   Member toMember = memberRepository.findById(toId);
   
   memberRepository.update(fromId, fromMember.getMoney() - money);
   memberRepository.update(toId, toMember.getMoney() + money);
 }
}
```
이처럼 서비스 계층은 특별한 기술(JDBC, 서블릿 등)을 사용하지 않고 순수 자바의 코드만으로 구현되어야 한다.
여기서 순수한 자바코드란 단순 구현되어 있는 메서드를 그대로 사용하는 것을 말한다. 특정 기술을 사용함으로써
연관관계를 맺는다면 이후 기술이 변경되었을 때 서비스 계층의 많은 부분을 수정해야 할 것이다.

물론 위에 `SQLException`이라는 JDBC 기술에 의존하고 있다. `memberRepository`에서 던진 예외이기에 이는 저장소에서
처리해야 하는 것이다. 이 또한 예외를 다룰 때 알아보도록 한다. 그리고 `MemberRepositoryV1`라는 구체 클래스에 의존하고 있기에
인터페이스에 의존하도록 변경할 필요도 있다.

```java
@Slf4j
@RequiredArgsConstructor
public class MemberServiceV2 {
 private final DataSource dataSource;
 private final MemberRepositoryV2 memberRepository;
 public void accountTransfer(String fromId, String toId, int money) throws SQLException {
   Connection con = dataSource.getConnection();
   try {
     con.setAutoCommit(false); //트랜잭션 시작
     //비즈니스 로직
     bizLogic(con, fromId, toId, money);
     con.commit(); //성공시 커밋
   } catch (Exception e) {
       con.rollback(); //실패시 롤백
       throw new IllegalStateException(e);
   } finally {
       release(con);
   }
 }
 private void bizLogic(Connection con, String fromId, String toId, int money) throws SQLException {
   Member fromMember = memberRepository.findById(con, fromId);
   Member toMember = memberRepository.findById(con, toId);
   
   memberRepository.update(con, fromId, fromMember.getMoney() - money);
   memberRepository.update(con, toId, toMember.getMoney() + money);
 }
}
```
**이는 서비스 계층에서 트랜잭션을 사용하는 경우이다.** 여기서 알 수 있는 문제점은 서비스 계층에서 트랜잭션을
사용하기 위해 `Connection`,`DataSource`,`SQLException` 같은 **JDBC 기술에 의존하고 있다는 것이다.**
이처럼 특정 기술에 의존함으로써 순수 비즈니스 로직보다 더 많은 양을 차지하고 있다. 이 때 JDBC를 JPA로 바꿀 경우,
해당 트랜잭션 관련 코드들을 싹 다 고쳐야 한다는 문제가 생긴다.

### 정리
1. 트랜잭션 문제
  - 트랜잭션을 사용하기 위해 서비스 계층에서 JDBC 구현 기술을 사용하고 있다.
  - 데이터 접근에 관련된 코드들은 데이터 접근 계층에 몰아야 한다.
  - 이와 같이 특정 기술에 의존하면 **JPA 같은 다른 데이터 접근 기술로 변경하였을 때, 코드를 싹 바꿔야 한다.**
  - 같은 트랜잭션을 유지하기 위해 커넥션을 파라미터로 넘겨야 하는 문제가 있다. **(커넥션을 넘길 필요가 없는 경우와 구분해야 하는 번거로움이 발생하기 때문)**
  - `try-catch-finally`와 같은 **반복되는 부분이 많다.**
2. 예외 누수
  - `SQLException` 같은 예외가 서비스 계층으로 전파된다는 문제가 발생한다. 이는 곧 서비스 계층에서 해당 기술에 의존하게 된다는 것이다.
3. JDBC 반복 문제
  - `try-catch-finally`와 같이 반복되는 코드가 많다.
  - 커넥션을 열고, `PreparedStatement`를 사용하고, 결과를 매핑하고 실행한 다음 커넥션과 리소스를 정리하는 일련의 과정이
모든 데이터 접근 메서드마다 동일하게 반복된다.

## 트랜잭션 추상화
앞서 서비스 계층의 코드가 **트랜잭션을 사용하기 위해** JDBC 기술에 의존하고 있다는 문제점을 지적하였다.
JDBC와 JPA의 트랜잭션 관련 코드는 완전히 다르기 때문에 향후 기술이 변경되었을 때, 코드를 완전히 바꿔야 한다.
- JDBC : `con.setAutoCommit(false)` - 트랜잭션 시작
- JPA : `transaction.begin()` - 트랜잭션 시작

![DB1](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/5a6ebf45-24c5-4769-ad4b-ef28cd776596)

**데이터 접근 계층뿐만 아니라 서비스 계층까지 데이터 접근 기술에 의존하고 있기 때문에 발생하는 문제이다.**

트랜잭션은 **오토 커밋을 끄고, 로직을 수행 후, 커밋 혹은 롤백을 하는 단순한 절차로 이루어진다.**
따라서 인터페이스에 위와 관련된 메서드만 정의하고 구현체가 이를 구현만 한다면 트랜잭션을 얼마든지 추상화 할 수 있을 것이다.

![DB2](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/6d17c21a-dd6d-4bb9-a252-b92fa45e7054)

이처럼 서비스 계층은 특정 트랜잭션 기술에 더이상 의존하지 않는다. 사용하는 쪽에서 생성자 주입을 통해 원하는 구현체를 DI를 통해
주입하기만 하면 된다. 이로 인해 **OCP 원칙을 지키게 되었고, 서비스 계층은 코드를 변경하지 않은 채 다양한 트랜잭션
기술을 사용할 수 있다.** 즉, 서비스 계층 입장에서는 자신의 확장**(얼마든지 다른 구현체를 주입 받을 수 있음)**에는 열려 있으며, 주변의 변화에 대해서는 닫혀 있게 되는 것이므로, OCP
를 지키게 된 것이다.**(구현체가 바뀌더라도 서비스 계층의 코드는 변경되지 않아도 됨)**

## 스프링의 트랜잭션 추상화
![DB3](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/4e0646bd-178b-4ff8-a191-518684364a86)

이처럼 스프링 트랜잭션에서는 `PlatformTransactionManager` 인터페이스가 핵심이다.

```java
package org.springframework.transaction;

public interface PlatformTransactionManager extends TransactionManager {
    TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;
    void commit(TransactionStatus status) throws TransactionException;
    void rollback(TransactionStatus status) throws TransactionException;
}
```
`PlatformTransactionManager`는 **트랜잭션을 시작하고, 커밋 혹은 롤백하는 메서드만 정의하고 있다.**
이것이 트랜잭션의 전부이기 때문이다. 

## 스프링의 트랜잭션 동기화
트랜잭션을 유지하기 위해서는 서비스 계층에서 **커넥션을 생성한 뒤, 트랜잭션이 종료될 때까지 같은 커넥션을
유지해야 한다.** 즉, DB에 접근할 때 같은 커넥션을 동기화(맞추어 사용)하기 위해서 파라미터로 커넥션을 전달하였다.
하지만 이는 두 가지 문제점을 야기한다.
1. 코드가 지저분해진다.
2. 파라미터로 사용하지 않는 경우, 즉 트랜잭션을 사용하지 않을 경우에는 파라미터가 없는 메서드를 따로 만들어줘야 한다.

![DB4](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/4af4c9a3-779d-4971-814a-e3c33b12ff6b)

![DB5](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/5c583564-a057-4766-9353-457867c9720d)

스프링 트랜잭션의 동작 원리는 다음과 같다.
1. 서비스 계층에서 트랜잭션을 시작할 때, **DataSource를 통해 커넥션을 만든 다음 트랜잭션을 시작한다.**
2. 트랜잭션 매니저(스프링에서 지원)는 트랜잭션이 시작된 커넥션을 **트랜잭션 동기화 매니저에 보관한다.**
3. 리포지토리는 DB에 접근하기 위한 메서드를 수행할 때 커넥션이 필요하다. 따라서 이 때, **트랜잭션 동기화 매니저에
보관된 커넥션을 꺼내 사용한다.** 보관하는 공간이 있기 때문에 따로 파라미터로 넘겨주지 않아도 된다.
4. 트랜잭션이 종료되면 트랜잭션 매니저는 동기화 매니저에 보관된 커넥션을 통해 트랜잭션을 종료하고, 커넥션도 알아서 닫는다.

> 쓰레드 로컬을 사용하면 각 쓰레드마다 별도의 저장소가 부여된다. 따라서 해당 쓰레드만 해당 데이터에 접근할 수 있으며
멀티 쓰레드 환경에서 여러 문제들을 방지할 수 있다.

**이처럼 스프링이 제공하는 트랜잭션 (+동기화) 매니저를 사용하면 트랜잭션을 위한 커넥션을 관리하기 쉬워지며, 코드도 간편해진다.**

### 커넥션을 관리하는 방법 : `DataSourceUtils`
1. 커넥션을 얻기 : `DataSourceUtils.getConnection()`
  - 트랜잭션 동기화 매니저가 관리하는 커넥션이 있으면 **해당 커넥션을 반환한다.** : 트랜잭션이 시작하면 트랜잭션 매니저는 동기화 매니저에게 해당 커넥션을 보관하도록 한다.
  - 트랜잭션 동기화 매니저가 관리하는 커넥션이 없으면, **새로운 커넥션을 생성해서 반환한다.**
2. 커넥션 닫기 : `DataSourceUtils.releaseConnection()`
  - 기존의 `con.close()`를 사용하면 커넥션을 직접 닫아버리게 된다. 트랜잭션을 종료하고 커넥션을 종료해야하기 때문에
**리포지토리 계층에서는 커넥션을 닫아서는 안된다.**
  - **트랜잭션을 사용하기 위해 동기화된 커넥션일 경우,** 닫지 않고 그대로 유지해준다.
  - **트랜잭션 동기화 매니저가 관리하는 커넥션이 없을 경우,** 해당 커넥션을 닫는다.

### 스프링 트랜잭션 동작 원리 정리

![DB6](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/0a8ea3c9-5bc4-4e28-a78b-2893cba70e59)

![DB7](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/8b39ef9b-9a4d-4efb-a830-69c012e9663d)

![DB8](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/99cd2488-d524-47ae-94ae-e6e638e89752)

## 트랜잭션 템플릿
```java
public void accountTransfer(String fromId, String toId, int money) throws SQLException {
        // 트랜잭션 시작
        TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());

        try {
            // 비즈니스 로직 수행
            bizLogic(fromId, toId, money);
            transactionManager.commit(status); // 성공 시 커밋
        } catch (Exception e) {
            transactionManager.rollback(status); // 실패 시 롤백
            throw new IllegalStateException(e);
        }
    }
```
해당 코드의 문제점은 서비스 계층의 메서드에서 **비즈니스 로직보다 트랜잭션 관련 로직이 더 많다는 것이다.**
따라서 트랜잭션과 관련된 부분을 간소화 시키는 **트랜잭션 템플릿을 사용한다.**

```java
@Slf4j
public class MemberServiceV3_2 {

  private final TransactionTemplate txTemplate;
  private final MemberRepositoryV3 memberRepository;

  public MemberServiceV3_2(PlatformTransactionManager transactionManager, MemberRepositoryV3 memberRepository) {
    this.txTemplate = new TransactionTemplate(transactionManager); // 트랜잭션 템플릿을 만들기 위해서는 트랜잭션 매니저가 필요
    this.memberRepository = memberRepository;
  }
  ...
}
```
이전과 달리 트랜잭션 매니저가 아니라 **트랜잭션 템플릿을 주입받는다.** 이 때 외부 주입과정에서 생성자에 트랜잭션 매니저를 주입한다.
따라서 이 경우는 `@RequiredArgsConstructor`를 사용하지 않았다. (외부 빈 설정을 통해 주입받아도 된다.)

```java
public void accountTransfer(String fromId, String toId, int money) throws SQLException {

        txTemplate.executeWithoutResult((status) -> {
            // 비즈니스 로직 수행
            try {
                bizLogic(fromId, toId, money);
            } catch (SQLException e) {
                throw new IllegalStateException(e);
            }
        });
}
```
트랜잭션 템플릿을 사용한 결과 비즈니스 로직과 트랜잭션 로직을 하나의 메서드로 간소화 할 수 있었다. **트랜잭션의 응답 값이
있을 경우, `execute()`를, 없을 경우, `executeWithoutResult()`를 사용한다.**

### 트랜잭션 템플릿 동작 원리
1. 비즈니스 로직이 정상 수행되면 커밋한다.
2. **언체크 예외가 발생하면 롤백한다. 그외의 경우는 커밋한다.**
3. `bizLogic()` 호출했을 때 체크 예외(`SQLException`)가 발생한다. 람다식에서는 이를 던질 수 없기 때문에 이를
언체크 예외로 바꾸어서 던지도록 작성하였다.(`SQLException -> IllegalStateException`)

이처럼 트랜잭션 템플릿을 통해 반복되는 트랜잭션 코드를 제거할 수 있었다. 하지만 여전히 서비스 계층에서 비즈니스 로직과
트랜잭션을 처리하는 기술이 함께 사용되고 있다. **이처럼 비즈니스 로직과 트랜잭션 로직이라는 두 가지 관심사를 
하나의 클래스에서 처리할 경우, 유지보수가 어려워 진다. 또한 트랜잭션을 사용하지 않는 메서드에 대한 처리도 번거로워 진다.**
서비스 로직에는 가급적 **핵심 비즈니스 로직만 있도록 해야 한다.** 트랜잭션 기술을 사용하되 이 코드를 분리시켜야 한다.

## 트랜잭션 AOP
지금까지 트랜잭션을 편리하게 사용하기 위해 **트랜잭션 추상화 및 템플릿**을 적용하였다.
- 트랜잭션 추상화를 통해 **JDBC, JPA 등 여러 데이터 접근 기술에 의존하지 않는 코드를 작성할 수 있었다.**
- 트랜잭션 템플릿을 통해 **트랜잭션을 위해 수행되는 반복 코드들을 줄임으로써 서비스 계층에서 트랜잭션 코드를
최소한으로 줄일 수 있었다.**

**하지만 여전히 서비스 계층에 순수한 비즈니스 로직만 남지 않는다는 문제가 존재한다.** 이는 스프링 AOP를 통해
프록시를 도입하면 문제를 해결할 수 있다. **프록시 즉, 중개인을 통해 트랜잭션 코드를 수행한다고 보면 된다.**

![DB1](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/10188164-5f15-483e-a0b4-664f7fb1316e)

![DB2](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/4446dc72-e0b0-4e60-aa47-eeabf4d070e0)

이처럼 프록시를 도입함으로써 트랜잭션을 처리하는 객체와 서비스 객체를 **분리할 수 있다.** 트랜잭션과 관련된 부분은
트랜잭션 프록시가 처리하기 때문에 서비스 객체는 비즈니스 로직만 갖고 있어도 된다.

![DB3](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/f4d0bb9e-59f9-4d3a-9a77-82d422b8a409)

이처럼 트랜잭션 프록시 객체는 서비스 계층의 로직을 갖고오며, 이를 트랜잭션 관련 코드들로 감싸고 있다. 따라서 트랜잭션 프록시도
기존에 있는 스프링 트랜잭션 추상화 기술을 사용하는 것이다. 뒤에서 얘기하겠지만 결국, **테스트 환경에서 트랜잭션 프록시를 사용하려면
트랜잭션 추상화인 PlatformTransactionManager 빈이 필요하다.**

### 스프링이 트랜잭션 AOP를 제공하는 이유
트랜잭션은 데이터를 처리하는 데 있어서 매우 중요한 기술 중 하나이다. 따라서 스프링은 이를 처리하기 위한
다양한 기능들을 제공한다. 이러한 기능들을 사용하면 트랜잭션 처리에 필요한 스프링 빈도 자동으로 등록해준다. (다만 테스트
코드에서는 수동으로 빈 등록 필요)

```java
/*
* 트랜잭션 - @Transactional AOP
*/
@Slf4j
public class MemberServiceV3_3 {

  private final MemberRepositoryV3 memberRepository;

  public MemberServiceV3_3(MemberRepositoryV3 memberRepository) {
    this.memberRepository = memberRepository;
  }

  @Transactional // 이 비즈니스 로직에 대해 Transaction을 수행함(성공 시 커밋, 실패 시 롤백)
  public void accountTransfer(String fromId, String toId, int money) throws SQLException {
    // 비즈니스 로직만 남겨둠
    bizLogic(fromId, toId, money);
  }

  private void bizLogic(String fromId, String toId, int money) throws SQLException {
    Member fromMember = memberRepository.findById(fromId);
    Member toMember = memberRepository.findById(toId);

    memberRepository.update(fromId, fromMember.getMoney() - money);
    validation(toMember);
    memberRepository.update(toId, toMember.getMoney() + money);
  }
}
```
이처럼 트랜잭션이 필요한 메서드 혹은 클래스 자체에 `@Transactional` 어노테이션을 붙여주기만 하면 알아서
스프링에서 **트랜잭션 프록시를 적용해준다.**

## 트랜잭션 어노테이션 테스트
`@Transcation`은 기본적으로 스프링 AOP를 사용하기 때문에 **스프링 컨테이너가 필요하다.**
따라서 테스트 환경에서 스프링 컨테이너를 사용하기 위해서는 `@SpringBootTest` 어노테이션을 사용해야 한다.
이렇게하면 테스트 시 스프링 부트를 통해 스프링 컨테이너를 생성한다. 그리고 `@Autowired`를 통해 빈을 사용할 수 있다.

스프링 부트가 자동으로 관리하는 빈 외에 추가로 설정해야 할 빈이 있을 경우, Config 메서드를 만든 다음 `@TestConfig`를 활용한다.

### TestConfig
- `DataSource`, `DataSourceTransactionManager`를 테스트 환경에서 사용하기 위해 빈으로 등록해야 한다.
- `@Transaction`을 사용하는 스프링 AOP는 스프링 빈에 등록된 트랜잭션 매니저를 사용한다. 따라서 트랜잭션 매니저(그리고 이를 사용하기 위해서는
DataSource도 같이)를 빈으로 등록해야 한다.
> 테스트 환경이 아닌 실제 서비스 코드에서는 스프링 부트에서 자동으로 등록시킨다. 따라서 `@Transaction`을 사용하는
서비스 객체만 빈으로 등록하면 된다.

```java
/*
 * 트랜잭션 - @Transactional AOP
 * */
@Slf4j
@SpringBootTest
class MemberServiceV3_3Test {

  public static final String MEMBER_A = "memberA";
  public static final String MEMBER_B = "memberB";
  public static final String MEMBER_EX = "ex";

  @Autowired
  private MemberRepositoryV3 memberRepository;
  @Autowired
  private MemberServiceV3_3 memberService;
}
```
```java
@TestConfiguration
    static class TestConfig {
        @Bean
        DataSource dataSource() {
            return new DriverManagerDataSource(URL, USERNAME, PASSWORD);
        }

        @Bean // 트랜잭션 프록시에서 사용하기 때문에 빈 등록 필요!
        PlatformTransactionManager transactionManager() {
            return new DataSourceTransactionManager(dataSource());
        }

        @Bean
        MemberRepositoryV3 memberRepositoryV3() {
            return new MemberRepositoryV3(dataSource());
        }

        @Bean
        MemberServiceV3_3 memberServiceV3_3() {
            return new MemberServiceV3_3(memberRepositoryV3());
        }
    }
```

### 트랜잭션 프록시 확인

```java
@Test
void AopCheck() {
    log.info("memberService class={}", memberService.getClass()); // 실제 서비스 객체가 아닌 트랜잭션 프록시 객체가 출력됨
    log.info("memberRepository class={}", memberRepository.getClass());

    Assertions.assertThat(AopUtils.isAopProxy(memberService)).isTrue();
    Assertions.assertThat(AopUtils.isAopProxy(memberRepository)).isFalse();
}
```

![DB4](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/1c56fade-b5bc-4c0e-97db-e06f371982c1)

그림처럼 CGLIB 부분이 붙어있다. **이는 해당 객체에 프록시가 적용됐다는 뜻이다.** `@Transaction`이 붙은 객체는
MemberService이기 때문에 이부분만 프록시가 적용되었다. 이처럼 트랜잭션 AOP를 사용할 경우, 프록시 객체가 트랜잭션을 수행하며,
**실제 비즈니스 로직이 호출될 때 서비스 객체가 사용됨을 알 수 있다.**

![DB5](https://github.com/whxogus215/Backend-Study-Archive/assets/70999462/049f16a8-f460-4470-96ab-41278ea6bf84)

### 선언적 트랜잭션과 프로그래밍 트랜잭션
- 선언적 트랜잭션 : `@Transaction`처럼 단순 선언을 통해 트랜잭션 기능을 활용할 수 있다.
  - **실무에서는 대부분 이 방식을 사용한다.** 
- 프로그래밍 트랜잭션 : 트랜잭션 매니저 혹은 템플릿을 사용하여 트랜잭션 관련 코드를 직접 작성한다.
  - 스프링 AOP를 사용하지 않기 때문에 테스트 시에 가끔 사용될 수도 있다.
- **트랜잭션을 사용할 경우, 웬만하면 스프링 AOP를 사용한 `@Transaction`을 사용하는 걸로 하자.**


### 정리
사용자는 트랜잭션을 사용해야 할 경우, 해당 비즈니스 로직에 `@Transaction`만 붙여주면 된다.(딸깍)
그러면 해당 비즈니스 로직에 대한 트랜잭션(오토커밋 False, 커밋/롤백) 기능을 스프링 트랜잭션 AOP에서 자동으로
처리해준다. 즉, `@Transaction`은 서비스 계층에서 주로 사용됨을 알 수 있다. 또한 서비스 로직만을 순수하게 남겨놓을 수 있다.






















## 테스트 코드 작성 Tip
### JUnit5 어노테이션 정리
1. `@BeforeEach` : 테스트 코드 실행하기 전에 실행되는 메서드를 정의할 수 있다. `void before()`
2. `@AfterEach` : 테스트 코드가 다 끝난 후에 실행되는 메서드를 정의할 수 있다. `void after()`
```java
    @BeforeEach
    void before() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource(URL, USERNAME, PASSWORD);
        memberRepository = new MemberRepositoryV2(dataSource);
        memberService = new MemberServiceV2(dataSource, memberRepository);
    }

    @AfterEach
    void after() throws SQLException {
        memberRepository.delete(MEMBER_A);
        memberRepository.delete(MEMBER_B);
        memberRepository.delete(MEMBER_EX);
    }
```
3. `@Test` : 테스트 메서드에 붙이는 어노테이션이다.
4. `@DisplayName("출력 이름")` : 테스트 메서드에 대한 이름을 지정할 수 있다. 실제 테스트 출력 창에 해당 이름이 표시된다.
```java
    @Test
    @DisplayName("정상 이체")
    void accountTransfer() throws SQLException {
        //given
        Member memberA = new Member(MEMBER_A, 10000);
        Member memberB = new Member(MEMBER_B, 10000);
        memberRepository.save(memberA);
        memberRepository.save(memberB);

        //when
        log.info("START TX");
        memberService.accountTransfer(memberA.getMemberId(), memberB.getMemberId(), 2000);
        log.info("END TX");

        //then
        Member findMemberA = memberRepository.findById(memberA.getMemberId());
        Member findMemberB = memberRepository.findById(memberB.getMemberId());

        assertThat(findMemberA.getMoney()).isEqualTo(8000);
        assertThat(findMemberB.getMoney()).isEqualTo(12000);
    }
```
5. `@Slf4j` : 로그를 찍을 때 사용하는 애노테이션이다. `log.info()`를 통해 특정 값에 대한 로그를 남길 수 있다. (`System.out.println()` 지양)

### 테스트 코드 작성법
- given : 테스트하기 위해 주어지는 값들에 대한 것
- when : 실제 테스트 로직이 실행되는 부분
- then : 테스트 실행 후 결과 값에 대한 판독이 일어나는 부분(테스트 값과 예상 값이 일치하는지 등등 판별)
```java
    @Test
    @DisplayName("이체중 예외 발생")
    void accountTransferEx() throws SQLException {
        //given
        Member memberA = new Member(MEMBER_A, 10000);
        Member memberEX = new Member(MEMBER_EX, 10000);
        memberRepository.save(memberA);
        memberRepository.save(memberEX);

        //when - 예외가 터지는지 확인
        assertThatThrownBy(() -> memberService.accountTransfer(memberA.getMemberId(), memberEX.getMemberId(), 2000))
                .isInstanceOf(IllegalStateException.class);

        //then
        Member findMemberA = memberRepository.findById(memberA.getMemberId());
        Member findMemberEX = memberRepository.findById(memberEX.getMemberId());

        assertThat(findMemberA.getMoney()).isEqualTo(8000);
        assertThat(findMemberEX.getMoney()).isEqualTo(10000);
    }
```

### assertj - Assertions 메서드 정리
1. `assertThat` : 테스트 코드에서 가장 많이 쓰이는 메서드로 보통 `isEqualTo`와 같이 쓰임으로써
**테스트 결과 값과 예상하는 값이 일치하는 지를 판단하는데 많이 쓰인다.**
```java
    @Test
    @DisplayName("정상 이체")
    void accountTransfer() throws SQLException {
        //given
        Member memberA = new Member(MEMBER_A, 10000);
        Member memberB = new Member(MEMBER_B, 10000);
        memberRepository.save(memberA);
        memberRepository.save(memberB);

        //when
        log.info("START TX");
        memberService.accountTransfer(memberA.getMemberId(), memberB.getMemberId(), 2000);
        log.info("END TX");

        //then
        Member findMemberA = memberRepository.findById(memberA.getMemberId());
        Member findMemberB = memberRepository.findById(memberB.getMemberId());

        assertThat(findMemberA.getMoney()).isEqualTo(8000);
        assertThat(findMemberB.getMoney()).isEqualTo(12000);
    }
```
2. `assertThatThrownBy` : 예외가 잘 발생하는 지를 확인하는 메서드이다. 특정 메서드에서 예외를 Throw하는지를
확인하는 것이다. 보통 람다식을 통해 확인하고자 하는 메서드를 작성한다. 또한 `isInstanceOf`를 통해 확인하고자 하는 예외 클래스를
지정할 수 있다.
```java
    @Test
    @DisplayName("이체중 예외 발생")
    void accountTransferEx() throws SQLException {
        //given
        Member memberA = new Member(MEMBER_A, 10000);
        Member memberEX = new Member(MEMBER_EX, 10000);
        memberRepository.save(memberA);
        memberRepository.save(memberEX);

        //when - 예외가 터지는지 확인
        assertThatThrownBy(() -> memberService.accountTransfer(memberA.getMemberId(), memberEX.getMemberId(), 2000))
                .isInstanceOf(IllegalStateException.class);

        //then
        Member findMemberA = memberRepository.findById(memberA.getMemberId());
        Member findMemberEX = memberRepository.findById(memberEX.getMemberId());

        assertThat(findMemberA.getMoney()).isEqualTo(8000);
        assertThat(findMemberEX.getMoney()).isEqualTo(10000);
    }
```
- `memberService.accountTransfer()`가 예외를 확인하고자 하는 메서드이다. 또한 `isInstanceOf`의 파라미터인
  `IllegalStateException.class`는 확인하고자 하는 예외 클래스이다.

# 단축키 정리
- 메서드 추출 : `컨트롤 + 알트 + M`
- 테스트 코드 자동 생성 : `컨트롤 + 알트 + T`
- 메서드 파라미터 순서 변경 : `컨트롤 + F6`

