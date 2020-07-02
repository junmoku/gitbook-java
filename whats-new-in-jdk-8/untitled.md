# Lambda Expressions



**1. 람다 대수**

계산 가능한 문제를 표현하는 수학적 모델

* _**F**_**\(x\) = y** 를 예를 들면, x라는 변수를 받아 y라는 결과를 출력해야 하는 문제를 풀 수 있는 _**F**_ 라는 함수가 있음
* _**F**_ ****를 λx.y 로 추상화 하여 표기  
* _**F**_ ****를 \(x\) -&gt; y 표현

\*\*\*\*

**2. Java 8 에서의 람다**

* C/C++은 함수 포인터, C\# 은 인디케이터 등으로 람다개념을 제공
* Java8 에서 인터페이스의 형태로 람다 등장
* 함수를 변수처럼 파라미터에 넣거나 리턴값으로 사용 할 수 있는 개념
*   | `post("/test", (request, response)->{ }, templateEngine)` |
  | :--- |

* 인터페이스와 같은 개념이지만 람다는 함수명 없이 사용할 수 있음 **인터페이스로 써도 같은 로직이지만 필요없는 것들이 많이 생긴다**

```java
get("/test", new TemplateViewRoute() {
    @Override
    public ModelAndView handle(Request paramRequest, Response paramResponse) {
        // TODO Auto-generated method stub 
        return null;    
    }
}, templateEngine);
```

| `get("/test", new` `TemplateViewRoute() {`                 `@Override    public` `ModelAndView handle(Request paramRequest, Response paramResponse) {        // TODO Auto-generated method stub        return` `null;    }}, templateEngine);` |
| :--- |


**4. Java 람다식의 장단점**

**장점**

* 코드의 라인 감소
* 람다로 사용 할 수 있는 Collection, Stream 의 메소드
* 함수 레벨의 추상화 \(인터페이스의 기능과 동일\)

**단점**

* 자바의 한계로 완벽한 람다 개념을 제공하지 못해 생기는 한계 점들이 있음
* 단점은.... 다음에

**5. 람다의 사용법**

* 인터페이스 작성**인터페이스**

  | `@FunctionalInterfaceinterface` `Func {    public` `int` `calc(int` `a, int` `b);}` |
  | :--- |


  @FunctionalInterface : 람다를 사용하기 위해서는 인터페이스의 메소드가 하나만 존재해야 한다. 그걸 컴파일단계에서 걸러주는 어노테이션  
  

* 사용**사용**

  | `Func sub = (int` `a, int` `b) -> a - b;Func add = (int` `a, int` `b) -> { return` `a + b; };Func ext = Test::extTest;`  `System.err.println(sub.calc(1,2));System.err.println(add.calc(1,2));`  `결과 : -1, 3` |
  | :--- |


  Func sub = \(int a, int b\) -&gt; a - b : 리턴을 그대로 써줄 수 있음  
  Func add = \(int a, int b\) -&gt; { return a + b; } : 함수 바디와 같은 형태  
  Func ext = Test::extTest : 스태틱으로 선언되어 있고, 파라미터와 리턴타입이 동일하면 확장\(?\) 만으로도 정의 할 수 있음

**6. 람다의 활용**

* 콜랙션에서 확용**콜랙션에서 확용**

  | `// 람다 활용List<Integer> testList = new` `ArrayList<Integer>();`         `for(int` `i=0; i<100; i++) {    testList.add(i);}`         `testList.forEach((val) -> {System.out.println(val);});testList.forEach(val -> System.out.println(val));testList.forEach(System.out::println);`  `// 기존 코드for(Entry<Integer, Integer> iter : testMap.entrySet()) {    System.err.println(iter.getValue());}` |
  | :--- |


  testList.forEach\(\(val\) -&gt; {System.out.println\(val\);}\) : 풀 문법  
  testList.forEach\(val -&gt; System.out.println\(val\)\) : 단축형  
  testList.forEach\(System.out::println\) : "::"문법으로 스태틱 메소드를 확장하면 에타변환\(컴파일러가 추론 할 수 있을 때 축약\) 됨

* Stream API  
  콜렉션의 반복, 복잡한 질의 처리를 위한 API **Stream**

  | `Map<Integer,Integer> testMap = new` `HashMap<Integer,Integer>();`         `for(int` `i=0; i<100; i++) {    testMap.put(i,i);}`         `// Stream API 를 사용한 코드long` `startMs = System.currentTimeMillis();OptionalDouble d = testMap.entrySet().stream().filter(item -> item.getValue()%2` `== 1).mapToDouble(item -> item.getValue()).average();System.err.println(d.getAsDouble());`  `// 기존 코드System.err.println(System.currentTimeMillis() - startMs);long` `startMs2 = System.currentTimeMillis();Double dd = 0.0;for(Entry<Integer, Integer> iter : testMap.entrySet()) {    if(iter.getValue()%2` `== 1) {        dd = dd+iter.getValue();    }}dd = dd/(testMap.size()/2);System.err.println(dd);System.err.println(System.currentTimeMillis() - startMs2);` |
  | :--- |


  **\[스트림의 세단계 구성\]**  


  testMap.entrySet\(\).**stream\(\)** : 스트림 생성  
  **filter\(item -&gt; item.getValue\(\)%2 == 1\)** : 중개 연산  
  mapToDouble\(item -&gt; item.getValue\(\)\).**average\(\)** : 종단 연산 \(이것 말고 쏠쏠한 stream 메소드가 많음\)  
  
  **\[시간 측정 결과\]                                    \(ms\)**  


  | 아이템 수 | 100 | 100000 | 10000000 |
  | :--- | :--- | :--- | :--- |
  | 아이템 수 | 100 | 100000 | 10000000 |
  | stream | 45 | 51 | 138 |
  | for | 0 | 6 | 660 |

  stream을 사용하면 건수가 많을수록 유리함 \(공식적이고 정확한 이유는 못찾음\)  
  
  **\[병렬 처리\]**   
  매우 간단하게 스트림을 생성할 때  **parallelStream\(\)** 이라고 하면 됨

* 함수 추상화  
  **Stream**

  | `@FunctionalInterfacepublic` `interface` `Func<T> {    public` `T doSomething(T t1, T t2);}Func<String> stringAdder = (String s1, String s2) -> s1 + s2;Func<Something> somethingAdder = (Something s1, Something s2) -> { return` `new` `Something(s1.get() + s2.get()); };` |
  | :--- |

**7. JAVA에서 제공하는 람다 인터페이스**

* **java.util.function.Predicate&lt;T&gt;** 임의의 타입 &lt;T&gt; 형태의 객체입력을 받아 그값을 처리한 후 결과가 true인지 false 인지를 리턴한다.
* **java.util.function.Function&lt;T,R&gt;** 첫번째 임의의 형태&lt;T&gt;의 입력값을 받아 처리한후 두번째 임의의 형태&lt;R&gt;의 값으로 출력한다. 
* **java.util.function.Consumer&lt;T&gt;** 임의형태의 입력값&lt;T&gt;을 받아 처리하고 출력은 하지 않는 형태의 인터페이스이다.
* **java.util.function.Supplier&lt;R&gt;**  
  입력값은 따로 없지만, 출력값&lt;R&gt;의 형태를 지정할수 있다.

   \(이외에도 제공하는 인터페이스들이 있음\)

