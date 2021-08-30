# MySQL 에서의 Table 생성시 추가 고려할점

MySQL 에서 단순 Table 생성 쿼리 외에 같이 생각해야 될 것들을 알아보자.

# Engine

사실 엔진은 MySQL 5.5 이상 쓴다면 Default 로 InnoDB 엔진기 설정이 되어 있지만

어떠한 상황에서도 InnoDB 엔진으로 생성된다는보장이 없을 수 있어서 같이 써주자

```sql
CREATE TABLE student
(
	...
) ENGINE = InnoDB
```

이렇게 뒤에 ENGINE = InnoDB 를 붙여주자

++ 다른 글에서 설명했지만 InnoDB 에 관해 간단하게 설명해보자

## **InnoDB

- MySQL을 위한 데이터베이스엔진(5.5버전 이후 기본엔진)

장점

1. 다수의 사용자가 동시 접속을 할 수 있고, 대용량의 데이터를 처리할 수 있다.(성능이 우수하다)
2. 장애 복구 기능 - 단순하게 장애를 복구하는것이 아니라, 논리적으로 장애 복구를 수행한다.
3. 세이프 스토리지 엔진이다.

단점

1. Deadlock 고려 - 노드간의 데이터 체크 등의 이유로 Deadlock 이 발생 할 수 있다.
2. 많은 자원 소모 - 대용량 처리를 하게 된다면 순간적으로 많은 자원을 소모 할 수도 있다.
3. 데이터 복구의 어려움 - 단순하게 파일 백업으로 파일 복구를 하는게 아니고 특정한 방법을 사용하여 복구를 수행한다.

---

# Character Set

문자를 출력하는 인코딩 형태인데 보통은 다들 UTF-8 을 쓰는 것으로 알고있다.

그런데 요즘 이모지(이모티콘) 을 쓰면서 4바이트 코드인 utf8mb4 를 쓰는 경우도 많이 생긴다.

그럼 이것을 설정하려면 어떻게 해야 할까??

MySQL 에서  제공하는것이 utf8mb4 이다 (mysql 5.6 이상부터 지원)

(Max Byte 가 4 까지 된다는 것을 의미한다.)

## 적용해 보자

- 이제 DB 전체 단위로 바꾸어 보자.

my.cnf 또는 / my.inf 로 들어가서 mysql 설정을 바꾸어 주자.

혹시 경로를 모른다면 

> mysqld --verbose --help | grep -A 1 'Default options'

를 치면 경로가 뜨니 확인하고 들어가서

아래 내용들을 추가 해 주자

```sql
[client]
default-character-set = utf8mb4

[mysql]
default-character-set = utf8mb4

[mysqldump]
default-character-set = utf8mb4

[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
skip-character-set-client-handshake
```

를 추가해 주고 mysql 을 재시동 해주자.

잘 적용됬는지 확인을 해보자

mysql 에서

```sql
show variables like 'char%';
```

을 실행해서 보면 된다.

```sql

+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8mb4                    |
| character_set_connection | utf8mb4                    |
| character_set_database   | utf8mb4                    |
| character_set_filesystem | binary                     |
| character_set_results    | utf8mb4                    |
| character_set_server     | utf8mb4                    |
...
```

대략 이런 결과가 나온다

그리고 JDBC 를 연결하는 URL 에서도 

```sql
jdbc:mysql://localhost:3306/your_database?useUnicode=true
```

이런 식으로 useUnicode=true 를 설정해 주자.

- JDBC url 에서 만약 utf-8 설정을 같이 적용했으면 에러가 뜨니 빼 주자

Database 마다 적용 된것이 다를 수 있으니 보면서 사용하고,

필요한 테이블마다 적용을 해 주면 될 것 같다.

---

참조

[https://velog.io/@devmin/MySQL-engine-character-set](https://velog.io/@devmin/MySQL-engine-character-set)

[https://cirius.tistory.com/1769](https://cirius.tistory.com/1769)

[https://www.lesstif.com/java/java-+-mysql-+-utf8mb4-emoji-51283094.html](https://www.lesstif.com/java/java-+-mysql-+-utf8mb4-emoji-51283094.html)

[https://medium.com/oldbeedev/mysql-utf8mb4-character-set-설정하기-da7624958624](https://medium.com/oldbeedev/mysql-utf8mb4-character-set-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-da7624958624)