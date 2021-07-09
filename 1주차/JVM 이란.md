## JVM 이란?

- Java Virtual Machine 의 약자이며 자바 가상머신 , 자바 바이트 코드를 실행할 수 있는 주체라고 보면 된다.
- 자바 코드를 컴파일해서 얻은 '바이트 코드' 를 해당 '운영체제가 이해할 수 있는 기계어' 로 바꿔 실행시켜주는 역할을 하기 때문에 OS 의 종류에 상관 없이 실행이 가능하다

## JVM 구조

### Runtime 시의 JVM 구조

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b308cb21-9d66-41ab-9455-8e182bec831a/jvm-runtime.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b308cb21-9d66-41ab-9455-8e182bec831a/jvm-runtime.png)

위쪽부터 실행 순서대로 보면

1. 프로그램 실행 시 JVM은 OS로부터 메모리를 할당
2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 자바 바이트코드(.class)로 컴파일
3. Class Loader를 통해 JVM Runtime Data Area로 적재하는 역할 수행
4. Class Loader에 의하여 로딩 된 .class들은 Execution Engine을 통해 해석되어 각 역할에 맞추어 수행된다.
    - Interpreter - ByteCode 를 기계가 이해할 수 있도록 Native Code로 바꾸는 작업을 함
    - JIT(Just In Time) Compiler - Interpreter 의 효율을 높이기 위해 JIT 컴파일러로 반복되는 코드를 모두 Native Code 로 바꾼다
    - Garbage Collector(GC) - Runtime Data Area 의 Heap 영역에있는 참조되지 않는 객체를 정리한다

### JVM Memory 구조

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1b896b20-512d-4d7e-803c-61b603d6c878/Runtime_Data_Area.svg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1b896b20-512d-4d7e-803c-61b603d6c878/Runtime_Data_Area.svg)

Runtime Data Area

- JVM 의 메모리 영역으로 자바 어플리케이션을 실행할 때 사용되는 데이터들이 적재되는 영역이다.
1. Method area(메소드 영역)
    - 모든 Thread 에 공유 됨
    - Class Loader 가 적재한  클래스(또는 인터페이스) 에 대한 메타데이터 정보가 저장된다.
    - **전역변수 와 정적 멤버변수(static 변수) 는 이 영역에 저장되므로 프로그램시작부터 종료될 때 까지 메모리에 남아있게 된다. 그래서 전역변수는 프로그램이 종료될 때까지 어디서든 사용 가능하게 된다.
    - 자세히 보면 Type Information(타입정보), Runtime Consatant Pool(런타임 상수 풀), Field Information(필드 정보), Method Information(메소드 정보), Class Variable(클래스 변수) 가 있다.
    - 그 중 Runtime Constant Pool 은 Type, Field, Method로의 모든 레퍼런스를 저장하는데, JVM 은 이 영역을 통해 실제 메모리 상의 주소를 찾아 참조한다.

2. Heap area(힙 영역)
    - 모든 Thread 에 공유 됨
    - 동적 데이터가 할당되어 저장된다.
    - GC의 동작 대상이며 레퍼런스(참조)가 없는 객체들은 GC동작 과정에 따라 메모리에서 제거된다.
    - Heap 영역은 크게 Eden(Young Generation), Survivor 1 & 2(Young Generation), Old(tenured generation), Permanent(JDK 8부터 삭제되고, 이 기능을 Metaspace 가 함)

3. Stack Area(스택 영역)
    - Thread 별로 1개씩 존재
    - 자바에서 메소드를 호출할 때 메소드의 Stack Frame 이 저장되는 영역
    - Stack Area 에 저장되는 변수는 기본자료형(int, double, long, float), 참조자료형(주소값)
    - ** 메소드 호출이 끝나면 Stack 내 메소드 관련 지역변수도 지워지기 때문에  메소드 영역 밖에서는 사용할수 없는 이유가 된다.
    - stack area 의 동작과정
    1. 자바는 스레드를 생성하지 않았다면 main 스레드만 존재하고 그에 따라 stack frame도 한개만 있다.
    2. 그 과정에서 메서드를 호출하면 stack프레임을 하나 새로 생성(push)된다
    3. 그리고 그 안에 메서드와 관계된 지역변수, 매개변수 등을 스택 영역에 저장한다.
    4. 이렇게 스택 영역은 메소드의 호출과 함께 할당되고 메소드의 호출이 완료되면 소멸(pop)한다.

4. PC Resisters(PC 레지스터)
    - Thread 별로 1개씩 존재
    - 현재 실행중인 JVM 의 명령주소(Program Counter) 를 갖고 있는 영역이다.
    - Java 의 PC Resister 는 Cpu 내의 기억장치인 레지스터와는 다르게 동작한다.

5. Native Stack(네이티브 스택)
    - Java 이외의 언어로 만들어진 코드를 실행하기위한 stack
    - JNI(Java Native Interface) 를 통해 호출되는 C/C++ 등의 코드를 실행가기 위함
    - JVM 내부에 영향을 주지 않기 위해 따로 메모리 공간을 활용함

   참조 :

   [https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html)

   [https://sgcomputer.tistory.com/64](https://sgcomputer.tistory.com/64)

   [https://yaboong.github.io/java/2018/05/26/java-memory-management/](https://yaboong.github.io/java/2018/05/26/java-memory-management/)

   [https://velog.io/@jsj3282/JVM과-자바-메모리-구조Runtime-Data-Area](https://velog.io/@jsj3282/JVM%EA%B3%BC-%EC%9E%90%EB%B0%94-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B5%AC%EC%A1%B0Runtime-Data-Area)