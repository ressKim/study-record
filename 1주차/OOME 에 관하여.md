# OOME 에 관하여

# OOME 란? 왜 발생하는가?

## OOME 란?

- Out Of Memory Error 의 약자로 oome라고 부른다.
- 글자 그대로 메모리가 초과하는 에러이다.
- 프로그램 실행중에 에러가 뜬다.

### OOME 종류

- java heap space - 가장 많이 볼 수 있는 에러로써 heap 영역이 부족할 때 일어나고 해결 방법으로는 단순하게 heap 영역을 늘리는 방법이있지만 이것보다는 보통 java 객체 설계를 잘못해서 나는 경우가 많으므로 늘리는것보다 코드 최적화를 먼저 하는게 좋을 것 같다.
    - 간단하게 Heapspace 가 부족한 상황에 터뜨리는 에러이다.
    - ex)

        ```java
        int[] oomTest = new int[999999 * 99999];
        ```

        - 메모리를 여유롭게 잡고 실행시키는 것이 아니면 여기서 'java.lang.OutOfMemoryError: Java heap space' 를 만날 수 있을 것이다  - 배열의 크기도 중요하단것을 알 수 있다.

- GC Overhead limit exceeded - cpu 사용량 중 98%이상이 GC 가 이루어졌지만 새로 확보된 메모리가 2% 미만일때 발생하는 것이다
    - 간단하게 GC가 cpu 를 엄청나게 사용하면서 정리하려는데 정리가 2%도 안되는 상황에서 터뜨리는 에러라 생각하면 된다.
    - ex)

        ```java
          Map map = System.getProperties();
          Random ran = new Random();
          while (true) {
            map.put(ran.nextInt(), "value");
          }
        ```

    - –Xmx100m -XX:+UseParallelGC 를 옵션으로 주면 에러가 뜨게 된다.
    - reference 가 존재하기 때문에 메모리가 부족하면 대부분의 cpu를 사용하고 그로인해 에러가 발생하게 된다.
    - 보통 이건 단순 Heapspace 부족, 큰 메모리를 사용하는 코드, 메모리 누수 같은 이유에 의해 발생한다.

- Metaspace - metaspace 영역이 부족할 때 나타나는 에러이다 - 여기 또한 단순 공간부족 인 경우 metaspace 크기를 늘려서 해결 할 수 있지만 쓸데없는 class 들이 많이 생성되서 일어날 수도 있으니 Eclipse 의 mat 등 분석을 한 뒤 이유를 찾아 해결하는게 바람직하겠다.
    - 간단하게 Class 가 많이 생겨서 metaspace 공간이 부족해 질 때 나는 에러이다.
    - ex)
        - 기본적으로 class 에 비해 memory 가 작은 경우 발생할 수 있다.
        - 외부 라이브러리 등에서 class 가 대량으로 생산되는 경우 발생할 수 있다.

- 이 외에도 Requested array size exceed VM limit , comparessed class space 등 여러가지 있으니 에러 상황에 따라 해결방법을 찾아가야 할 것이다.

# ++ 메모리 분석 도구

- VisualVM ([https://visualvm.github.io/](https://visualvm.github.io/))
- mat ([https://www.eclipse.org/mat/](https://www.eclipse.org/mat/))

## 마치며

- out of memory error 를 찾아보면서 이 에러같은 경우 보통 실행단계, 사용자들이 사용할 때 거의 알아차릴 수 있는 에러들이므로 미리 어떤 에러들이 어떤 이유로 발생하는지 대략적으로 안 다음 빨리 대응해야겠다.
- 물론, heap space 같은 경우 보통 코드 최적화 문제가 많기 때문에 쓸데없는 객체가 낭비되고 있진 않은지 항상 생각하면서 코드를 짜는 버릇을 해야겠다.

참고

[https://www.whatap.io/ko/blog/57/](https://www.whatap.io/ko/blog/57/)

[https://changrea.io/java/oom-issue/](https://changrea.io/java/oom-issue/)같은 경우 보통 코드 최적화 문제가 많기 때문에 쓸데없는 객체가 낭비되고 있진 않은지 항상 생각하면서 코드를 짜는 버릇을 해야겠다.