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







# 단축키 정리
- 메서드 추출 : `컨트롤 + 알트 + M`
- 테스트 코드 자동 생성 : `컨트롤 + 알트(or 쉬프트) + T`
