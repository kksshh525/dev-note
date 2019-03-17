

# 2장 객체의 생성과 삭제
## 규칙1 - 생성자 대신 정적 팩터리 메서드를 사용할 수 없나

일반적으로 클래스의 생성자를 살펴보자 

```java
public class Account {
    private Long id;
    private String name;
    private int age;
    
    // 기본 생성자
    public Account(){
    }
    
    // 인자가 있는 생성자 
    public Account(String name, int age){
        this.name = name;
        this.age = age;
    }
    
    // 정적 팩터리 메서드
    public Account of(String name, int age){
        this.name = name;
        this.age = age;
    }
}
```

정적 팩터리 메서드가 갖는 장점

1. 생성자와 달리, 정적 팩터리 메서드는 메서드 명을 갖는다

2. 반환 타입을 지정할 수 있다. 여기에서는 Account

3. Account 클래스가 만들어 질때 `new Account()` 매 번 호출될 때 마다 새로운 객체를 생성하는 것과 달리, 만약에 변경 불가능한 객체(immutable)라면 이미 만들어둔 객체를 활용할 수 있다. 

4. 형인자 자료형 객체를 만들 때 편하다





## 규칙2 - 생성자 인자가 많을 때는 Builder패턴 적용을 고려하라. 

1. 점층적인 생성자 패턴
2. 자바빈 패턴(setter 위주의) 
3. Builder패턴 

1번의 경우에 더 많은 인자 개수에 잘 적응 하지 못한다. 코드가 너무 방대해 지는 단점이 있다. 

2번의 경우에는 일관성이 훼손(setter로 변경) 되고, 항상 변경 가능하다. 

1,2번의 문제를 빌더 패턴으로 해결!  😃



실제 실무에서는 `Lombok` 를 사용해서 클래스 위가 X 해당 생성자 위에(O) `@Builder` 를 달아준다. 

```java
public class Member {

    private String name;
    private int age;
    private String address;
    
    @Builder
    public Member(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

```



실제 사용하는 코드는 다음과 같다.

```java
public static void main(String[] args) {
        Member member = Member.builder()
                .name("andrew")
                .age(32)
                .build();
    }
```





## 규칙3 - private 생성자나 enum 자료형은 싱글턴 패턴을 따르도록 설계하라

싱글턴 패턴을 만들 때는 private 생성자를 통해서 해당 클래스 내에서 만들고(유일하게 만듬) 외부에서 접근할 때는 getInstance()를 통해서 만들어진 객체의 참조값으로 접근하고 사용한다. 싱글턴 패턴을 사용하는 케이스는 파일 시스템, 단 하나만을 요구하는 그런 상황에서 쓰인다. 

enum 자료형을 이용해서 싱글턴 패턴을 만들 수 있는 것 자체를 몰랐다. (기능적으로 public 필드를 사용해서 접근하는 방법과 동일) 하지만 다음과 같은 장점이 존재한다.  

- 좀더 간결하다는 것

  ```jav
  public enum Elvis {
      INSTANCE;
  }
  ```

  

- 직렬화가 자동으로 처리 된다는 것 

- 리플렉션을 통한 공격에도 안전

  ```java
  public class PrivateInvoker {
  
      public static void main(String[] args) throws Exception {
          Constructor<?> constructor = Private.class.getDeclaredConstructors()[0];
          constructor.setAccessible(true); //접근할 수 있는 권한을 획득 함
          Private p = (Private) constructor.newInstance();
  
      }
  
      class Private {
         	private Private() {
              System.out.println("hello world");
          }
      }
  }
  
  ```

그래서 원소가 하나 뿐인 enum 자료형이야 말로 싱글턴을 구현하는 가장 좋은 방법이다.
