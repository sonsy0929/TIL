# String
## String의 불변성 (Immutable)
- Immutable이란 객체를 생성한 후 **객체의 상태를 변경할 수 없는 것**을 의미한다.
- String 객체는 문자열의 상태 값을 변경할 수 없기 때문에 불변 객체라고 한다.
- String은 불변(immutable)하기 때문에 String + String = 새로운 String 객체를 생성한다. 이 때 String + String 하는 시점에 메모리 할당과 해제가 계속 발생할 수 있게 된다.

## String 객체가 불변 객체인 이유

### 1. String 객체의 캐싱 기능
    
String은 가장 많이 사용되는 데이터 타입 중 하나이며, String 타입의 객체들이 가장 많은 메모리를 차지한다. 문자열 객체는 재사용될 가능성이 높기에 같은 값일 경우 하나의 String 객체만을 String Pool에 생성해두어 JVM의 heap을 절약하고자 했다. 참조하려는 문자열이 String Pool에 존재하는 경우 새로 생성하지 않고 Pool에 있는 객체를 재사용한다. 따라서 객체를 새로 생성하는 오버헤드가 발생하지 않기 때문에 특정 문자열값을 재사용하는 빈도가 높을수록 성능 향상을 기대할 수 있다.

  - 문자열을 생성하는 방법
    1. String literal을 통한 생성

          `String name = "Heize"`
          
          String literal로 생성하면 문자열은 힙영역의 String Pool에 저장된다.

    2. new 연산자를 통해 생성 
          
          `String name = new String("Heize")`

          new 연산자로 생성하면 문자열은 heap 영역에 저장되어 문자열이 같더라도 새롭게 생성된다.

 - String Pool

    String 객체를 보관하고 있는 일종의 보관소로 내부적으로 생성되는 String 객체를 Instance로 보관하고 있다. 

    ```java
    @Test
    void createString() {
        final String first = new String("ABC");
        final String second = new String("ABC");
        assertThat(first).isSameAs(second); // fail
    }
    ```
    ```java
    @Test
    void createString() {
        final String first = "ABC"
        final String second = "ABC"
        assertThat(first).isSameAs(second); // pass
    }
    ```
- intern()
  
  String 객체에 `intern()`이 붙어 있으면 new 키워드로 생성하지 않고, String Pool에서 가져온다.

  ```java
    @Test
    void createStringIntern() {
        String first = new String("ABC");
        String second = "ABC"
        String third = first.intern();
        assertThat(first).isSameAs(second); // fail
        assertThat(second).isSameAs(third); // pass
    }
  ```

### 스레드 안정성 (Thread-Safe)
String 객체가 변경될 수 없다는 것은 여러 스레드에서 동시에 특정 String 객체를 참조한다고 하더라도 안전하다는 것을 뜻한다.


## String vs. StringBuffer, StringBuilder
- String은 immutable하고 StringBuffer, StringBuilder는 mutable하다. 따라서 문자열 연산이 자주 있을 때에는 StringBuffer나 StringBuilder를 사용하는 것이 효율적이다. 
- StringBuffer와 StringBuilder의 차이점으로는 StringBuffer는 멀티 쓰레드 환경에서 synchronized 키워드가 가능하므로 동기화가 가능하며 thread-safe하다. 반대로 StringBuilder는 동기화를 지원하지 않기 때문에 thread-safe하지 않으며 멀티 쓰레드 환경에서는 적합하지 않다.
