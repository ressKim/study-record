# DB 인덱스

# DB 의 인덱스란??

- 테이블의 검색 속도를 향상시키기 위한 것이라고 보면 된다.
- 책의 마지막에 있는 '색인' 페이지와 비슷하다고 생각하면 이해하기 편하다.
- 테이블 전체를 조회하기에는 시간이 걸리니 성능 향상을 위해 칼럼의 값과 해당 레코드가 저장된 주소를 키와 값의 형태로 인덱스를 만들어 둔다.
- 보통 WHERE 절에 사용할 때 성능을 향상시킨다.

### 장점

- 읽기 에는 전체 값을 읽는 것보다 빠르다.
- 큰 테이블을 전체를 읽는게 아니기때문에 시스템 부하도 적게 소모한다.

### 단점

- 인덱스를 관리하기 위해 추가적인 저장공간이 필요하다.
- 읽기는 빠르지만 추가, 삭제 , 수정 같은 작업에서 상대적으로 느려진다.
- 잘 사용하지 않는 다면 오히려 속도가 느려지는 현상이 있을 수 있다.

# Index 사용 예시

### 인덱스 생성 예시

```sql
-- ex)
CREATE INDEX EX_INDEX_USER ON USERS(NAME,ADDRESS);
```

### 인덱스 조회 예시

```sql
-- ex)
SELECT * FROM EX_INDEX_USER WHERE TABLE_NAME = 'USERS';
```

### 인덱스 삭제 예시

```sql
-- ex)
DROP INDEX EX_INDEX_USER;
```

- 인덱스가 너무 많이 들어가면 INSERT, UPDATE, DELETE 에 성능이 저하되므로 안쓰는 것은 바로바로 삭제시키자.

### DB 인덱스 결론

- 읽기가 주로 이루어 지는 큰 테이블일 경우 사용하는 것이 좋으나, 잘 사용해야 될 것 같다.
- CREATE, UPDATE, DELETE 가 주로 이루어지는 Table 에는 피하는 것이 좋겠다.

참고

[https://coding-factory.tistory.com/746](https://coding-factory.tistory.com/746)