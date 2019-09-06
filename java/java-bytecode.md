

## 다이나믹 프록시 

다이나믹 프록시를 알아보기 전에 다음과 같은 상황을 살펴보자. 

1. Spring Data Jpa에서 다음과 같은 코드를 보면 어노테이션을 선언하지 않아도, 빈으로 등록이 되고, JpaRepository가 제공하는 메서드들을 사용할 수 있다. 왜? 어떻게 사용할 수 있을까? 

```java
public interface BookRepository implements JpaRepository<Book, Long>{
  
}
```



2. 프록시 패턴에 대해서 알아보자

![](https://upload.wikimedia.org/wikipedia/commons/6/6e/W3sDesign_Proxy_Design_Pattern_UML.jpg)

프록시 패턴은 클라이언트는 Subjec(Proxy)를 바라보고, Proxy는 실제 Real Subject를 참조한다. 그래서 실제 어떤 작업 전, 후에 Logging을 추가한다고 했을 때, 프록시 패턴을 통해서 구현이 가능하다. Subject를 인터페이스화 시키고, Proxy 클래스에서는 RealSubject의 해당 메서드를 위임 받아서, 하고 싶은 작업들 (Logging)를 추가할 수 있다. 

하지만, 새로운 기능이 추가되고, 여기에 또 앞/뒤로 로깅을 추가해야 하는 작업이 생겨나면 어떻게 될까? Proxy클래스에서 또다시 위임을 받고, 앞뒤에 로깅을 찍는 작업을 해야 한다. 결국 변화에 따른 작업해야 하는 양이 늘어난다. 이쯤에서 다이나믹(동적) 프록시를 통해서 이런 문제들을 어떻게 해결하는지 살펴보자. 



### 다이나믹 프록시란? 

런타임에 인터페이스, 클래스를 프록시 인스턴스로 만들어서 활용하는 프로그래밍 기법을 뜻한다. 



### Java에서 제공하는 Reflection 을 이용한 다이나믹 프록시는?

Proxy.newInstance()라는 메서드를 통해서 Proxy객체를 만들 수 있다. 

```java
// To create a proxy for some interface Foo:
     InvocationHandler handler = new MyInvocationHandler(...);
     Class proxyClass = Proxy.getProxyClass(
         Foo.class.getClassLoader(), new Class[] { Foo.class });
     Foo f = (Foo) proxyClass.
         getConstructor(new Class[] { InvocationHandler.class }).
         newInstance(new Object[] { handler });
 
//or more simply:
     Foo f = (Foo) Proxy.newProxyInstance(Foo.class.getClassLoader(),
                                          new Class[] { Foo.class },
                                          handler);
```



```java
public static Object newProxyInstance(ClassLoader loader,
                      Class<?>[] interfaces,
                      InvocationHandler h)
                               throws IllegalArgumentException
```

API문서를 살펴보면, 첫번째 인자로는 ClassLoader가 들어가고, 두번째 인자로는 Subject인 인터페이스인 클래스 타입을 받는다. 반드시 인터페이스일때만 다이나믹 프록시를 만들 수 있다. 세번째 인자는 InvocationHandler인자를 받고, 메서드 invoke를 통해서 원하는 작업을 할 수 있다. 하지만 역시나 newProxyInstance를 통해서 구현을 하게 되면 메서드 자체가 커질 수 있고, 변경에 유연하지 못하다. **<u>추가적인 학습 토비의 스프링 AOP</u>**

그렇다면 클래스를 통해서 Proxy를 적용할 수는 없나? 

- CGLib
- ByteBuddy

를 통해서 인터페이스가 아닌, 클래스의 Proxy를 적용할 수 있다. 





### 다이나믹 프록시 실제 사용처

- Spring AOP
- Spring Data JPA
- Mockito
- Hibernate Lazy Initialize

