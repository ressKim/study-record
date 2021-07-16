# OOME 에 관하여

# OOME 란? 왜 발생하는가?

## OOME 란?

- Out Of Memory Error 의 약자로 oome라고 부른다.
- 글자 그대로 메모리가 초과하는 에러이다.
- 프로그램 실행중에 에러가 뜬다.

### OOME 종류

- java heap space - 가장 많이 볼 수 있는 에러로써 heap 영역이 부족할 때 일어나고 해결 방법으로는 단순하게 heap 영역을 늘리는 방법이있지만 이것보다는 보통 java 객체 설계를 잘못해서 나는 경우가 많으므로 늘리는것보다 코드 최적화를 먼저 하는게 좋을 것 같다.
- GC Overhead limit exceeded - GC 가 이루어졌지만 새로 확보된 메모리가 2% 미만일때 발생하는 것으로 heap 영역 size 를 키우는것으로 해결할 수 있다.
- Metaspace - metaspace 영역이 부족할 때 나타나는 에러이다 - 여기 또한 단순 공간부족 인 경우 metaspace 크기를 늘려서 해결 할 수 있지만 쓸데없는 class 들이 많이 생성되서 일어날 수도 있으니 Eclipse 의 mat 등 분석을 한 뒤 이유를 찾아 해결하는게 바람직하겠다.
- 이 외에도 Requested array size exceed VM limit , comparessed class space 등 여러가지 있으니 에러 상황에 따라 해결방법을 찾아가야 할 것이다.

## 마치며

- out of memory error 를 찾아보면서 이 에러같은 경우 보통 사용자들이 사용하거나 할 때 거의 알아차릴 수 있는 에러들이므로 미리 어떤 에러들이 어떤 이유로 발생하는지 대략적으로 안 다음 빨리 대응해야겠다.
- 물론, heap space 같은 경우 보통 코드 최적화 문제가 많기 때문에 쓸데없는 객체가 낭비되고 있진 않은지 항상 생각하면서 코드를 짜는 버릇을 해야겠다.