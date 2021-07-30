# +MySQL 스토리지 엔진

# MySQL 에서 사용 가능한 엔진

## *MyISAM

- MySQL 의 5.5버전 이전의 기본 스토리지 엔진

장점

- 엔진 자체가 기본적인 성능만 제공하기때문에 가볍다
- 검색(SELECT) 작업이 빠르다
- 복합 검색이 가능하다.

단점

- Transaction 이 세이프하지 않다. 그래서 트랙잭션을 처리하고자 한다면 프로그래머가 코드레벨에서 처리해 주어야 한다.
- 데이터 무결성이 보장되지 않는다 이 또한 프로그래머가 코드레벨에서 처리해 주어야 한다.
- 쓰기 작업 시 Table level locking 이 걸리기때문에 속도가 느리다.
- 외래키의 생성이 불가능하다.

## **InnoDB

- MySQL을 위한 데이터베이스엔진(5.5버전 이후 기본엔진)

장점

- 데이터 무결성이 보장된다.
- Row level locking 으로 MyISAM 보다 동시성에서 우위를 보이고, Insert, Update, Delete 작업에 유리하다.
- Transaction 을 지원하며, commit 과 Rollback 을 지원한다.
- 장애복구, 외래키 등 다양한 기능을 제공한다.

단점

- 엔진이 무거워져서 작업속도가 상대적으로 느리다.
- 시스템 자원을 많이 소모한다.

## Memory 엔진

메모리에 데이터를 저장하는 엔진이며 Transaction 을 지원하지 않고, Table-level-locking 을 사용한다.

- 메모리를 사용하기 때문에 기본적인 속도가 매우 빠르지만 데이터 손실의 위험이 있다.
- 중요하지 않지만 빠른 처리가 필요한 임시 테이블에 많이 사용된다.
- ex
    - 조회용 또는 매핑용 테이블
    - 주기적으로 집계되는 데이터의 결과 캐시용
    - 데이터 분석 시 중간결과 저장용

++ MySQL은 내부에서 메모리 엔진을 사용한다 중간 결과가 메모리 테이블에 저장하기에 너무 커지거나 TEXT 또는 BLOB 칼럼이 포함되어 있다면 MySQL은 디스크에 MyISAM 테이블을 만들어 처리한다고 한다

++ CREATE TEMPORARY TABLE 임시테이블과는 다르다. 이건 어떤 스토리지 엔진이든 사용 할 수 있다.

## 그 외 엔진들

- Archive 엔진
- CSV 엔진
- Federated 엔진
- Blackhole 엔진
- 등등

# MyISAM 와 InnoDB 의 비교

- 두 가지가 반대성향이라고 생각하면 편하다.
- MyISAM 은 기본적인기능을 제공하며 속도가 빠르고, Transaction  이 안된다.
- InnoDB 는 Transaction 을 제공하고 기능이 많지만 상대적으로 무겁다.
- 이런것을 보면 트랜잭션이 필요한 상황이면 InnoDB 를 사용해야 하고,
- 트랜잭션이 필요없고, select 가 메인인 업무에는 MyISAM 도 좋은 선택이 될 수 있다.
이 같은 경우 Transaction 이 없는 DB 가 말이되냐 할 수 있는데 logging 같은 환경에서는 유리하다고 볼 수도 있다.
- 또 여러 작업이 서로 인터럽트 없이 동시에 실행되려면 Row level lock 기능이 있는 InnoDB 엔진을 선택해야 한다.

## 마치며

- 이렇게 여러가지 조건들을 따지며 엔진을 선택 할 수 있는데 지금 내가 하려는 환경은 InnoDB 가 맞으니 그에 따른 공부를 더 해봐야겠다

참조

[https://nomadlee.com/mysql-스토리지-엔진-종류-및-특징/](https://nomadlee.com/mysql-%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80-%EC%97%94%EC%A7%84-%EC%A2%85%EB%A5%98-%EB%B0%8F-%ED%8A%B9%EC%A7%95/)