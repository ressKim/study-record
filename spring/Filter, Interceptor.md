# Filter, Interceptor

개발을 하다보면 여러 군데에서 많이 써야되는 로직들이 있다.(예를들어 로그인 여부 확인 등(여기선 security적용 방법을 제외한 것을 생각해보자))

```java
public class XXController{

	@GetMapping(...)
	public String itemPage(){
	-- 로그인 여부 체크 로직 --
	...
	}

	@GetMapping(...)
	public String teamPage(){
	-- 로그인 여부 체크 로직 --
	...
	}

	@GetMapping(...)
	public String nothingPage(){
	-- 로그인 여부 체크 로직 --
	...
	}

}
```

위와 같은 상황이 나오기 마련이다. 이런 공통 로직을 처리하기 위한 방법은 어떤게 있을까??

Filter , Interceptor , AOP 등이 있다. 이번 장에서는 Filter 와 spring Interceptor 에 대해 간단하게 알아보자.

# Filter 란?

- servlet 에서 제공하는 것
- filters는 특정 URL 패턴에 적용도 가능
- filter 흐름

    HTTP요청 → WAS → Filter → Servlet(spring 일 경우 DispatcherServlet) → Controller

    - 여기서 Filter 에서 걸린다면
    - HTTP 요청 → WAS → Filter(걸림, 다음 Servlet 을 호출 안함)
    - 이렇게 다음 Servlet 을 그냥 요청을 안하고 바로 끝을 낼 수 있다.
- filter 는 chain 형식으로도 추가할 수 있다.
- WAS → Filter1 → Filter2 ... 이런식으로 여러개 순서를 정해서 하는것도 가능하다.

# Interceptor 란?

- spring 에서 제공하는 것
- Interceptor 흐름

    HTTP 요청 → WAS → Filter → Servlet → 스프링 인터셉터 → controller

    - 마찬가지로 Interceptor 에서 걸린다면
    - HTTP 요청 → WAS → Filter → Servlet → Spring Interceptor(걸림, 다음 controller 를 요청안함)
- Controller 에 접근하기 전, 후, 그리고 작업이 다 끝난 후 에 대해 각각 설정할 수 있다.
    - preHandler() - Controller 호출 전에 작업하는 것으로 여기서 return 값을 true/false 로 설정하여 controller 를 실행 할 지 안할 지 설정할 수 있다.
    - postHandler()- controller 호출 후에 작업하는 것으로, 만약 controller 에서 에러가 뜬다면 실행이 안된다.
    - afterCompletion()- 에러가 뜨더라도 호출되며, 내부에서 ex 를 받아서 로그로 출력 등을 처리할 수 있다.
- Interceptor 또한 chain 형식으로 여러개 할 수 있다.
- + Interceptor 는 MVC 구조에 좀 더 특화된 필터 기능을 제공한다고 이해해도 된다. 스프링 MVC 를 사용하면 특별히 filter 를 사용해야 하는 상황이 아니라면 Interceptor 가 더 편리하다고 한다.

---

참조

[https://bk-investing.tistory.com/61](https://bk-investing.tistory.com/61)

[https://heodolf.tistory.com/40](https://heodolf.tistory.com/40)