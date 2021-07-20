# Collection에서의 Synchronization

collection 에서의 synchronization 방법을 알아보기 전에 앞서

synchronize 가 왜 필요한지 먼저 알아보자

# Race Conditions and Critical Sections

(출처 : [https://velog.io/@huttels/Race-Condition-Thread-Safety-Immutability](https://velog.io/@huttels/Race-Condition-Thread-Safety-Immutability))

- 경쟁 조건이란 임계 구역 안에서 일어날 수 있는 특별한 상태를 말한다.
- 임계 구역이란 여러 쓰레드에 의해 실행되는 코드 지역으로 쓰레드들의 실행 순서가 동시 실행 결과를 다르게 하는 지역이라는 뜻이다.
- 임계 구역의 다중 쓰레드 실행 결과가 어떤 쓰레드부터 실행했는지 순서에 따라 달라질 때, 임계 구역은 경쟁 조건을 가진다고 한다.

## Critical Sections

- 하나 이상의 쓰레드를 응용에서 실행하는 것만이 문제를 발생시키지 않는다.
- 문제는 다중 쓰레드가 같은 자원에 접근할경우 발생한다.
    - 예를들어 같은 메모리(변수, 배열, 객체), 시스템(데이터 베이스, 웹 서비드 등), 파일
- 사실 문제는 이러한 자원에 하나 이상의 쓰레드가 write할 경우 발생한다.
- 자원이 변하지 않는다면 여러 쓰레드가 같은 자원을 읽는 것은 안전하다.

## Preventing Race Conditions

- 경쟁 조건을 방지하기 위해서 임계 구역이 원자적 instruction으로 실행됨을 보장해야한다.
- 하나의 쓰레드가 실행할 때 다른 쓰레드는 실행중인 쓰레드가 임계 구역을 나오기 전까지 실행할 수 없게 만드는 것을 말한다.
- 경쟁 조건은 임계구역의 적절한 쓰레드 동기화를 통해 피할 수 있다.
- 자바 코드의 동기화 블록을 통해 쓰레드 동기화를 얻을 수 있다.
- 쓰레드 동기화는 lock 또는 원자 변수를 사용해서도 얻을 수 있다.

** 이러한 현상은 java 에 국한된 것이 아니라 운영체제 까지 전부에 해당하는 영역이지만 이번 장에서는 

Java Collection 에서는 어떻게 해결하는지 알아보자.

# Java Collection 에서의 Synchronized

### Vector, HashTable

- vector 과 hashTable 은 내부적으로 동기화(Synchronized) 된 환경에서 실행되므로 멀티 스레드 환경에서 thread safe 한 상태이다.

### List, HashMap, Set

- 이 세가지는 내부적으로 각각 synchronized**() 메소드를 가지고 있고 이것을 사용하면 동기화가 된다.
- 그러나 이 synchronized**() 는 동시에 접근할때 락을 걸어버리기때문에 속도가 효율적이지는 못하다.

### ConcurrentHashMap, ConcurrentLinkedQueue

- 이 concurrent** 메소드들은 동기화가 되지만 전체에서 락을 다 거는게 아니라 쓰기 작업에만 락을 거는 방식이기때문에 synchronized**() 메소드를 쓰는거보다 빠르다.
- 읽기 작업이 주인 경우보다 쓰기작업에서 중요한 상황에 잘 쓰는 것이 중요하다.
- + 읽기작업(get()) 에는 동기화가 안되기때문에 가장 최근에 완료된 결과를 보여준다.

참조 사이트

[https://velog.io/@huttels/Race-Condition-Thread-Safety-Immutability](https://velog.io/@huttels/Race-Condition-Thread-Safety-Immutability)

[https://stackoverflow.com/questions/34510/what-is-a-race-condition](https://stackoverflow.com/questions/34510/what-is-a-race-condition)

[https://devlog-wjdrbs96.tistory.com/269](https://devlog-wjdrbs96.tistory.com/269)

[https://pplenty.tistory.com/17](https://pplenty.tistory.com/17)