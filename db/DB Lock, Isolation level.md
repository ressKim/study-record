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

---

# Transaction Isolation level?

- ACID 의 원칙을 너무 타이트하게 지키면 동시성(Cuncurrency) 에 대한 성능이 너무 떨어지기 때문에 Isolation Level 별로 차등을 두어서 동이성에 대한 이점을 가질 수 있게 한다.
- 간단하게 ACID 원칙을 희생하면서 동시성을 얻게 하는 방법이다 - isolation level 을 덜 지킨다.

## Isolation Level

ANSI/ISO SQL Standard 에서 정의한 Isolation Level 은 이렇다.

ANSI(American National Standards Institute) : 미국 국립 표준 협회

ISO(International Organization for Standartdization) : 국제 표준화 기구

++Consistent Read - read(Select) operation 을 수행할 때 현재 DB 의 값이 아닌 특정 시점의 DB snapshot 을 읽어오는 것이다. 물론 이 snapshot은 commit 된 변화만이 적용된 상태를 의미한다.

### READ COMMITTED

- commit 된 데이터만 보이는 수준의 isolation을 보장하는 level 이다.
- 자세하게
  - READ COMMITTED transaction 은 read operation 마다 DB snapshot을 다시 뜬다.
  - 그래서 ''다른 transaction이  commit '' 한 다음 read operation 을 수행하면 변경된것을 볼 수 있다.
  - 이게 실제 DB 에는 아직 commit 안된 쿼리도 적용되는 상태라 commit 된 데이터만을 복구하는 과정이 필요한데 이 때 consistent read 를 수행해야 한다.
  - *READ COMMITTED 는 select, update, delete 쿼리를 수행할때 record lock 만 사용하기 때문에 phantom read 가 일어날 수 있다

### READ UNCOMMITTED

- transaction 은 READ COMMITTED 와 동일하지만, SELECT 쿼리를 실행할 때 commit 되지 않은 데이터를 읽어올 수 있다.
- 자세하게
  1. Transaction A 에서 row 삽입
  2. READ UNCOMMITTED transaction B 가 해당 row 를 읽음
  3. Transaction A 가 rollback 된다.
  - DB 에 commit 되지 않은 값을, 존재하지 않아야 하는 데이터를 읽었다. 이러한 현상을 dirty read 라고 한다
  - +참고 MySQL reference 내용
    - InnoDB uses an optimistic mechanism for commits, so that changes can be written to the data files before the commit actually occurs. This technique makes the commit itself faster, with the tradeoff that more work is required in case of a rollback.

### REPEATABLE READ

- 반복해서 read operation 을 수행하더라도, 읽어 들이는 값이 변화하지 않는 정도의 isolation 을 보장하는 level 이다.
- 자세하게
  - REPEATABLE READ Transaction 은 처음 read(Select) operation 을 수행한 시간을 기록한다.
  - 그 이후 모든 read operation 마다 해당 시점을 기준으로 consistent read를 수행한다.
  - non-locking select 를 제외한 lock 을 사용하는 select, update, delete 쿼리를 실행할때 REPEATABLE READ transaction 은 보통 gap lock 을 사용한다.
    - gap lock 뿐만 아니라 next-key lock 도 활용한다.
    - WHERE 조건대로 index 를 탔을 때 반드시 row가 하나 이하만 걸릴 수 있는 쿼리에 대해는 gap lock 을 걸지 않는다.(어차피 하나 이하이기 때문에)

### SERIALIZABLE

- transaction 은 REPEATABLE READ 와 동일하지만, SELECT 쿼리가 전부 SELECT ... FOR SHARE 로 자동으로 변경된다.
- 이게 가장 높은 격리 수준 이지만 빡빡하게 잡는 터라 성능이 좋지는 않다.
- 자세하게

  ([https://blog.sapzil.org/2017/04/01/do-not-trust-sql-transaction/](https://blog.sapzil.org/2017/04/01/do-not-trust-sql-transaction/))참조

  ([https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/](https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/))

  다음 상황을 모두 SERIALIZABLE transaction 으로 실행한다고 가정해 보자.

    ```sql
    (A-1) SELECT state FROM account WHERE id = 1;
    (B-1) SELECT state FROM account WHERE id = 1;
    (B-2) UPDATE account SET state = ‘rich’, money = money * 1000 WHERE id = 1;
    (B-3) COMMIT;
    (A-2) UPDATE account SET state = ‘rich’, money = money * 1000 WHERE id = 1;
    (A-3) COMMIT;
    ```

  - (A-1) 에서 select 를 하면서 id=1 row에 s-lock 이 걸리게 된다, (B-1) 역시 s-lock 을 같은곳에 걸게 된다.
  - 이 때 A, B 둘 다 update 를 시도하면서 x-lock 을 걸려고 하는데 이미 s-lock 이 걸려있는 상황이라 Deadlock 에 빠지게 된다.
  - A, B 둘다 잘못된 업데이트는 일어나지 않았지만 Deadlock 에 걸리는 상황이 발생할 수 있다.

---

# +DeadLock

- 교착상태 라고도 부르며 한정된 자원을 여러 곳에서 사용하려고 할 때 발생한다.
- 발생하는 상황
  - 멀티 프로그래밍 환경에서 한정된 자원을 사용하려고 서로 경쟁하는 상태일 때.
  - 어떤 프로세스가 자원을 요청했는데 자원을 사용하고 있어서 대기상태로 들어간다. 그런데 대기상태로 들어간 프로세스가 실행상태로 변경될 수 없을 때.

참조

[https://taes-k.github.io/2020/05/17/mysql-transaction-lock/](https://taes-k.github.io/2020/05/17/mysql-transaction-lock/)

[https://velog.io/@lsb156/Transaction-Isolation-Level](https://velog.io/@lsb156/Transaction-Isolation-Level)

[https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/](https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/)

[https://blog.sapzil.org/2017/04/01/do-not-trust-sql-transaction/](https://blog.sapzil.org/2017/04/01/do-not-trust-sql-transaction/)

[https://www.letmecompile.com/mysql-innodb-lock-deadlock/](https://www.letmecompile.com/mysql-innodb-lock-deadlock/)

[https://dev.mysql.com/doc/refman/5.7/en/innodb-locks-set.html](https://dev.mysql.com/doc/refman/5.7/en/innodb-locks-set.html)