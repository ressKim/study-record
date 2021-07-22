# DB Lock,  Isolation level

# InnoDB Lock

- InnoDB 엔진은 다양한 Lock 을 이용하여 ACID 및 동시성을 보장합니다.
- Lock 을 사용자가 각각 명시적으로 걸어 줄 수도 있지만, 일반적으로 Transaction isolation level 에 따라 Lock 을 걸어준다.
- 보통 많이 쓰이는 것들 위주로 설명 해 보려고 한다.
- Lock의 적용 요소에 따른 분류, 적용되는 상황에 따른 분류 로 나누어 보겠다.

## Lock 적용 요소에 따른 분류

### Shared lock(S-lock)

- row-level lock
- SELECT 를 위한 read lock
- shared lock이 걸려있는 동안 다른 트랜잭션이 해당 row 에 대해 X lock획득(exclusive write)는 불가능하지만 S lock 획득(shared read)는 가능하다.
- 한 row 에 대해 여러 트랜잭션이 동시에 S lock을 획득 가능

### Exclusive lock(X-lock)

- row-level lock
- UPDTE, DELETE 를 위한 write lock
- exclusive lock 이 걸려있으면 다른 트랜잭션이 해당 row에 대해 X, S lock 을 모두 획득하지 못하고 대기해야 한다.

## Lock이 적용되는 상황에 따른 분류

### Record Lock

- A record lock is a lock on an index record.
- DB의 index record 에 S-lock 또는 X-lock 을 거는 것이다.
- Table 에 index 가 생성되있지 않더라도 테이블 생성시에 함께 생성되는 default Clustered index의 record 에 lock 을 걸어서 항상 index record 에 lock 을 설정한다.
- ex

    tb Table 이 있다고 가정해 보자

    ```sql
    (Transaction A)
    SELECT clu1 FROM tb where clu = 99;

    (Transaction B)
    DELETE FROM tb WHERE clu1 = 99; 
    ```

    이렇게 실행하면 A 는 s-lock,  B는 x-lock 이 걸리게 된다.

### Gap Lock( = range lock)

- A gap lock is a lock on a gap between index records, or a lock on the gap before the first or after the last index record
- DB index record 의 gap 에 걸리는 lock 이다. (gap 이란 index 중 DB에 실제 record 가 없는 부분 을 칭함)
- negative infinity(최초 record 이전), positive infinity(마지막 record 이후) 를 가상의 index record 로 생각해서 lock 을 거는게 가능하다.
- Gap Lock 을 통해서 같은 SELECT 쿼리를 두 번 실행했을 떄 다른 트랜잭션에서 데이터가 수정되었더라도 같은 결과가 리턴되는 것을 보장할 수 있다.(Phantom read 방지)
    - ex

        ```sql
        SELECT id FROM tb WHERE id BETWEEN 10 and 20 FOR UPDATE
        ```

    - 위 쿼리는 10~20 사이에 x-lock 이 걸리기 때문에 다른 트랜잭션에서  15를 INSERT 하려면 대기상태로 빠진다.
- READ COMMITED isolation level 을 사용하면 트랜잭션 내부에서 "동일 select 실행, 동일 결과" 보장을 하지 않아도 되기 때문에 gap lock 을 비활성화 해도 된다.
    - 이 경우 DB 쿼리 처리량은 늘어나지만 대기시간이 줄고 동시write 가 증가하기 때문에 Dead Lock 에 걸릴 위험히 높아진다
- 컬럼에 unique index 가 걸려있는 경우 gap lock 이 필요가 없다(record lock 이 사용됨)
- 컬럼에 index 가 걸려있지 않거나, 걸려있어도 unique 하지 않다면 gap lock 이 필요하다.

### Next-Key Lock

- A next-key lock is a combination of a record lock on the index record and a gap lock on the gap before the index record.
- record lock 와 gap lock 이 복합적으로 사용되는 것

### 그 외

- Insert Intention Lock, Auto-INC Lock 등이 있다.

# Transaction Isolation level?

- ACID 의 원칙을 너무 타이트하게 지키면 동시성(Cuncurrency) 에 대한 성능이 너무 떨어지기 때문에 Isolation Level 별로 차등을 두어서 동이성에 대한 이점을 가질 수 있게 한다.
- 간단하게 ACID 원칙을 희생하면서 동시성을 얻게 하는 방법이다 - isolation level 을 덜 지킨다.

## Isolation Level

ANSI/ISO SQL Standard 에서 정의한 Isolation Level 은 이렇다.

ANSI(American National Standards Institute) : 미국 국립 표준 협회

ISO(International Organization for Standartdization) : 국제 표준화 기구

### READ COMMITTED

- commit 된 데이터만 보이는 수준의 isolation을 보장하는 level 이다.

### READ UNCOMMITTE

- transaction 은 READ COMMITTED 와 동일하지만, SELECT 쿼리를 실행할 때 commit 되지 않은 데이터를 읽어올 수 있다.

### REPEATABLE READ

- 반복해서 read operation 을 수행하더라도, 읽어 들이는 값이 변화하지 않는 정도의 isolation 을 보장하는 level 이다.

### SERIALIZABLE

- transaction 은 REPEATABLE READ 와 동일하지만, SELECT 쿼리가 전부 SELECT ... FOR SHARE 로 자동으로 변경된다.

참조

[https://taes-k.github.io/2020/05/17/mysql-transaction-lock/](https://taes-k.github.io/2020/05/17/mysql-transaction-lock/)

[https://velog.io/@lsb156/Transaction-Isolation-Level](https://velog.io/@lsb156/Transaction-Isolation-Level)

[https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/](https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/)

[https://blog.sapzil.org/2017/04/01/do-not-trust-sql-transaction/](https://blog.sapzil.org/2017/04/01/do-not-trust-sql-transaction/)

[https://www.letmecompile.com/mysql-innodb-lock-deadlock/](https://www.letmecompile.com/mysql-innodb-lock-deadlock/)

[https://dev.mysql.com/doc/refman/5.7/en/innodb-locks-set.html](https://dev.mysql.com/doc/refman/5.7/en/innodb-locks-set.html)