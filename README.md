# [스프링 DB1편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-1/dashboard)
# 커넥션풀과 데이터소스 이해
## 커넥션 풀 이해

- 커넥션을 얻는 방법
    - 드라이버 매니저를 통해
    - 커넥션 풀을 통해
- 데이터베이스 커넥션을 매번 획득
    - TCP/IP 연결 수립
    - 드라이버가 아이디 패스워드 전달
    - 디비 내부적으로 세션 생성
    - 디비 드라이버가 커넥션 반환
- 과정 + 시간이 많이 소모
- 커넥션풀은 미리 커넥션을 생성해놓고 있는 개념
- 어플리케이션이 실행할 때 커넥션 풀을 다 생성하고 디비와 TCP/IP 연결을 전부 수립
    - 서비스하는 상황마다 다르겠지만 기본은 10개의 커넥션풀 생성
- 커넥션을 생성하는 시간이 사라지고 SQL 실행시간만 존재 → 사용자경험 상승

## DataSource의 이해

- 만약 기존 드라이버매니저를 이용해서 커넥션을 얻어오는 방법에서 커넥션 풀을 이용하는 방법으로 변환하려하면 코드의 수정이 필요하고 둘의 사용법도 약간을 다를 것
- 커넥션을 획득하는 방법 추상화 → getConnection()
- DataSource는 커넥션을 얻는 방법을 추상화하는 인터페이스
- DriverManager는 DataSouce인터페이스를 구현 안해놓음 → 스프링에서는 DriverManagerDataSource를 사용해서 내부적으로  DataSource를 사용하도록 함

## DataSource 예제1 - DriverManager

- DriverManager를 사용해서 커넥션 얻어오는 테스트코드 작성
- DriverManagerDataSource를 사용해서 커넥션 얻어오는 테스트코드 작성
- DriverManagerDataSource → 설정과 사용의 분리
    - DriverManagerDataSource에서는 getConnection()만 호출 하면됨
    - DriverManager를 사용해서 커넥션을 얻어올 땐 URL, USERNAME, PASSWORD를 매번 넘겨야함

## DataSource 예제2 - 커넥션 풀

- hikaricp 사용해서 커넥션풀 생성 테스트 코드 작성
    - 커넥션을 생성해서 커넥션풀에 채우는 로그를 보고 싶으면 Thread.sleep(1000)해서 잠시 멈추고 실행
    - XXX connection adder 라는 별도의 스레드에서 실행되기 때문
    - 테스트 코드가 순식간에 끝나니깐 별도의 스레드에서 실행되고있는 로그가 나오기 전에 테스트가 끝남
- 풀이 다 차버렸을 경우 얼마나 더 대기하는지, 예외를 발생한다든지 하는 설정을 할 수 있음

## DataSource 적용

- DataSource 의존관계 주입
    - 외부에서 dataSource 주입받아서 사용
    - DriverManagerDataSource 사용하다가 HikariDataSource를 사용해도 상관 없음
- JdbcUtils
    - JdbcUtils을 사용하면 커넥션을 보다 편하게 닫을 수 있다
- `MemberRepositoryV1Test` 테스트코드 작성
    - DriverManagerDataSource 사용하는 방법 → 매번 커넥션을 얻어오는 로그 확인
    - HikariDataSource → 풀에 커넥션을 채워놓고 풀에서 커넥션을 얻어오는 로그, 커넥션하나 사용하고 반환하고 하기때문에 하나의 커넥션이 계속 사용되는것을 볼 수 있다.
