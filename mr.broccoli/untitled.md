# enum

1. Enum

* 열거형\(상수 집합\) 데이터 타입



2. 출현

* java 1.5 부터 출현
* C/C++ 에는 이미 있었고 java에서는 상수 클래스에 스태틱 변수로 비슷하게 사용하고 있었음

```java
// C/C++
enum REWARD_TYPE
{
    REWARD_TYPE_ITEM_1,     >> REWARD_TYPE::REWARD_TYPE_ITEM_1
    REWARD_TYPE_ITEM_2,     >> REWARD_TYPE::REWARD_TYPE_ITEM_2
    REWARD_TYPE_ITEM_3,     >> REWARD_TYPE::REWARD_TYPE_ITEM_3
}

// java
public class RewardType {
    public static final int REWARD_TYPE_ITEM_1 = 1;
    public static final int REWARD_TYPE_ITEM_2 = 2;
    public static final int REWARD_TYPE_ITEM_3 = 3;
}
```

* 하지만 C/C++ 의 enum 보다 많은것을 할 수 있는 java의 enum이 출현



3. 주요 특징

* 상속을 받거나 할수 없음 \(확장 불가\)
* 기본 생성자가 private \(인스턴스를 만들 수 없음, 사용자 정의 생성자는 추가 가능\)

```java
TestEnum e = new TestEnum(); 
// 에러 : Cannot instantiate the type TestEnum
```

* 각 원소가 public static final \(런타임 시 추가 타입 생성 불가\)

```java
public enum TestEnum {A,B};
TestEnum.A //로 사용가능
```

* switch 문에 사용 할 수 있음

```java
TestEnum e = TestEnum.A;   
switch (e) {
case A:
    break;
case B:
    break;
default:
    break;
}
```



4. 메소드

* static TestEnum\[\] values\(\)

  모든 원소를 배열로 리턴

```java
TestEnum[] enumArray = TestEnum.values();
```

* static Class&lt;T&gt; valueOf\(String arg\) / valueOf\(Class&lt;T&gt; class, String arg\) 파라미터와 일치하는 이름의 원소를 찾아 리턴

```java
TestEnum e;
e = TestEnum.valueOf("A");
e = TestEnum.valueOf(TestEnum.class, "A");
```

* int ordinal\(\) 선언된 순서를 리턴함. 하지만 재정의될 수 있기때문에 ordinal\(\)을 사용하여 map&lt;int, TestEnum&gt;의 인덱스로 쓰는 등 다른곳에 enum을 구분하는 index로 사용하면 위험

```java
int i = 0;
i = TestEnum.A.ordinal();
i = e.ordinal();
  
이럼 안됨 ↓
Map<Integer, String> testMap = new HashMap<Integer, String>();
testMap.put(TestEnum.ONE.ordinal(), TestEnum.ONE.name());
testMap.put(TestEnum.TWO.ordinal(), TestEnum.TWO.name());
```



5. 사용

* 선언

```java
public enum TestEnum {A,B};     //클래스 밖 or 안에 선언-
```

* 확장 \(원소들이 하위의 멤버를 가짐\)

```java
public enum TestEnum {
     
    A(0, "a"),
    B(1, "b");
     
    private int order;
    private String str;
     
    TestEnum(int order, String str) {
        this.order = order;
        this.str = str;
    }
}
```

* EnumSet \(특정 타입 원소들의 Set\)

```java
EnumSet<TestEnum> enumSet = EnumSet.of(TestEnum.A, TestEnum.B);
  
if(testSet.contains(TestEnum.A)) {  }           
// 매소드 내부에서 ordinal 사용으로 add, remove, contains 시 hashMap보다 속도가 빠름
testSet.add(TestEnum.B);
testSet.remove(TestEnum.A);
```

* EnumMap \(Enum 타입을 키로 사용하는 맵\)

```java
EnumMap<TestEnum, String> testMap = new EnumMap<TestEnum, String>(TestEnum.class);
testMap.put(TestEnum.B, "b");  
testMap.put(TestEnum.A, "a");               
// B, A 순으로 put 해도 출력이나 반복시 순서는 TestEnum에 선언된 순서인 A, B 순이다.
  
System.err.println(testMap);                    

// 출력 : {A=a, B=b}
```



6. 활용

* 추상함수 활용 원소에 추상 함수를 정의하여 필요한 추상함수만 활용하는 EnumSet 생성

```java
// TestEnum
// 선언
public enum TestEnum {
     
    MORE_THAN_TEN{
        @Override
        public boolean validate(TestObject object) {
            // 맴버에 추상함수 정의
            return object.getTestInt() > 10;
        }
    },
    IS_EMPTY{
        public boolean validate(TestObject object) {
            return object.getTestString().isEmpty();
        }
    };
    public abstract boolean validate(TestObject object);
}
 

  
// main
public static void main(String[] args) {       
    TestObject object = new TestObject();
    object.setTestInt(11);
    object.setTestString("a");
     
    EnumSet<TestEnum> testSet1
         = EnumSet.of(TestEnum.MORE_THAN_TEN);
    EnumSet<TestEnum> testSet2
         = EnumSet.of(TestEnum.MORE_THAN_TEN, TestEnum.IS_EMPTY);
     
    System.err.println(validate(testSet1, object));
    // 출력 : true
    System.err.println(validate(testSet2, object));  
    // 출력 : false
}
  
public static boolean validate(EnumSet<TestEnum> testSet, TestObject object) {
    for(TestEnum e : testSet) {
        if(!e.validate(object)) {
            return false;
        }
    }
    return true;
}
```

* 인터페이스 활용 2개 이상의 정의된 Enum에 공통 인터페이스를 만들어 ID로 원소를 찾음 &gt;&gt; 같은 식으로 2개 이상의 Enum을 extends 한 것처럼 사용할 수 있음

```java
// interface
public interface Identifiable<K> {
    K getId();
}
 
 

  
// util
public class IdUtil {
    public static <T extends Enum<T> & Identifiable<S>, S> T get(Class<T> type, S id) {
        for (T t : type.getEnumConstants()) {
            if (t.getId() == id) {
                return t;
            }
        }
        return null;
    }
}
 
 

  
// enum
public enum TestEnum implements Identifiable<Integer> {
    ONE(1), TOW(2), THREE(3);
    private int id;
    TestEnum(int id) {
        this.id = id;
    }
    public Integer getId() {
        return id;
    }
}
  

 
// main
TestEnum e = IdUtil.get(TestEnum.class, 2);
System.err.println(e); 
// 출력 : TOW
```

