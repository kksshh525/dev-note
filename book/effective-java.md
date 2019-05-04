> Effective Java 3판을 읽고 정리한 내용입니다.

![](http://image.yes24.com/Goods/65551284/800x0)

<!-- TOC -->

- [장 객체의 생성과 삭제](#장-객체의-생성과-삭제)
    - [아이템 1 - 생성자 대신 정적 팩터리 메서드를 사용할 수 없나](#아이템-1---생성자-대신-정적-팩터리-메서드를-사용할-수-없나)
    - [아이템2 - 생성자 인자가 많을 때는 Builder패턴 적용을 고려하라.](#아이템2---생성자-인자가-많을-때는-builder패턴-적용을-고려하라)
    - [아이템 3 - private 생성자나 enum 자료형은 싱글턴 패턴을 따르도록 설계하라](#아이템-3---private-생성자나-enum-자료형은-싱글턴-패턴을-따르도록-설계하라)
    - [아이템 16 public 클래스 안에는 public 필드를 두지 말고 접근자 메서드를 사용하라](#아이템-16-public-클래스-안에는-public-필드를-두지-말고-접근자-메서드를-사용하라)
    - [정리](#정리)
    - [아이템 17 변경가능성을 최소화 하라](#아이템-17-변경가능성을-최소화-하라)
        - [변경 불가능 클래스](#변경-불가능-클래스)
        - [변경 불가능 클래스 만드는 규칙 5가지](#변경-불가능-클래스-만드는-규칙-5가지)
        - [변경 불가능 클래스를 구현 해보자](#변경-불가능-클래스를-구현-해보자)
    - [정리](#정리-1)
    - [아이템 18 상속하는 대신 구성하라](#아이템-18-상속하는-대신-구성하라)
        - [해결책](#해결책)
    - [아이템 19 상속을 위한 설계와 문서를 갖추거나, 그럴 수 없다면](#아이템-19-상속을-위한-설계와-문서를-갖추거나-그럴-수-없다면)
        - [재정의 가능 메서드를 내부적으로 어떻게 사용하는지 반드시 문서로 남겨라](#재정의-가능-메서드를-내부적으로-어떻게-사용하는지-반드시-문서로-남겨라)
        - [상속을 고려하여 클래스를 설계할 때 protected로 선언할 멤버는 어떻게 정하나?](#상속을-고려하여-클래스를-설계할-때-protected로-선언할-멤버는-어떻게-정하나)
        - [상속을 허용하려면 반드시 따라야할 제약사항](#상속을-허용하려면-반드시-따라야할-제약사항)

<!-- /TOC -->
# 2장 객체의 생성과 파괴

## 아이템 1 - 생성자 대신 정적 팩터리 메서드를 사용할 수 없나

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





## 아이템2 - 생성자 인자가 많을 때는 Builder패턴 적용을 고려하라. 

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





## 아이템 3 - private 생성자나 enum 자료형은 싱글턴 패턴을 따르도록 설계하라

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





# 3장 모든 객체의 공통 메서드



# 4장 클래스와 인터페이스

## 아이템 16 - public 클래스 안에는 public 필드를 두지 말고 접근자 메서드를 사용하라

```java
class Point {
    public double x;
    public double y;
}
```

데이터의 필드를 직접 조작 할 수 있다. (캡슐화 X )

- 캡상추다!



```java
class Point {
    private double x;
    private double y;
    
    public Point(double x, double y){
        this.x = x;
        this.y = y;
    }
    
    public double getX(){
        return x;
    }
    public double getY(){
        return y;
    }
    public void setX(double x){
        this.x = x;
    }
    public void setY(double y){
        this.y = y;
    }
}
```



- public 클래스는 필드를 외부에 직접 공개 하지 말아야 한다.
  - 1 )따르지 않은 경우가 있어 엄청 심각함. java.awk 패키지 Point, Dimension 
  - 2) 그나마 덜 한건, 변경 불가능 필드



1번 케이스
```java
public class Point extends Point2D implements java.io.Serializable {
    /**
     * The X coordinate of this {@code Point}.
     * If no X coordinate is set it will default to 0.
     *
     * @serial
     * @see #getLocation()
     * @see #move(int, int)
     * @since 1.0
     */
    public int x;

    /**
     * The Y coordinate of this {@code Point}.
     * If no Y coordinate is set it will default to 0.
     *
     * @serial
     * @see #getLocation()
     * @see #move(int, int)
     * @since 1.0
     */
    public int y;
```



2번 케이스
```java
public final class Time {
    private static final int HOURS_PER_DAY = 24;
    private static final int MINUTE_PER_HOUR = 60;
    
    // public 으로 공개했으나, 변경 불가능 필드임
    public final int hour;
    public final int minute;
    
    // 생략
}
```



## 아이템 17 - 변경가능성을 최소화 하라

변경 불가능 클래스

- 정의: 그 객체를 수정할 수 없는 클래스이다. 객체가 생성될때 주어진 것. 
- 장점: 변경 가능 클래스보다, 변경 불가능클래스가  훨씬~~~~ 쉽다. (설계, 구현, 사용, 오류도 적고, 더 안전)



변경 불가능 클래스 만드는 규칙 5가지 

1. setter 제공 X
2. 상속 X : 클래스를 final로 선언한다.
3. 모든 필드를 final
4. 모든 필드를 private
5. 변경 가능 컴포넌트에 대해서는 독점적 접근권을 보장한다.  
   : *생성자나 접근자 readObject  메서드 안에서는 방어적 복사본을 만들어야 한다. ????* 



```java
public final class Complex {
    private final double re; //실수부 
    private final double im; //허수부
    
    public Complex(double re, double im){
        this.re = re;
        this.im = im;
    }
    
    // setter 수정자가 없음  //getter 메서드
    public double realPart(){return re;}
    public double imaginaryPart(){return im;}
    
    public Complex add(Complex c){
        return new Complex(re + c.re, im + c.im );
    }
    
	// 생략
}
```

위의 add() 메서드는 **함수형접근법**으로 알려져 있는데, 피 연산자를 변경하는 대신, 연산을 적용한 결과를 새롭게 만들어 반환함 

**절차적 또는 명령형 접근법**은 피연산자에 일정한 절차를 적용하여 그 상태를 바꾼다.  (새로운 객체를 매번 생성하지 않고, 기존의 필드에 값을 변경해 나가도록 프로그래밍하는 방식)

**그래서 함수형 접근법은 변경 불가능성을 보장하므로 장점이 많다.**



- 변경 불가능 객체는 스레드에 당연히 안전하다. 어떤 동기화도 필요 없고, 자유롭게 공유할 수 있다.
- 한단계 더 나악가서 -> 변경 불가능 클래스는 자주 사용하는 객체를 캐시하여 이미 있는 객체가 거듭 생성되지 않도록 팩터리를 제공할 수 있다. ex ) BigInteger클래스  
  -> 메모리 요구량과 가비지 콜렉터 비용이 줄어듬



변경 불가능 클래스를 구현 해보자

1. 간단한 방법 class 를 final로 선언
2. 대안적 설계 방법 
   1. 모든 생성자를 private하고 public 생성자 대신, **public 정적 팩터리를 제공한다.** 

```java
public class Complex {
   
    private final double re;
    private final double im;
    
    private Complex(double re, double im){
        this.re = re;
        this.im = im;
    }
    
    // public 정적 팩터리 메서드를 제공(규칙1)
    public static Complex valuOf(double re, double im){
        return new Complex(re, im);
    }
    
    // 나머지는 동일.
}
```







## 아이템 18 - 상속하는 대신 구성하라

상속은 코드의 재사용을 돕는 강력한 도구다. 

<u>조건: 상위 클래스와 하위 클래스를 같은 프로그래머가 통제 하는 단일 패키지 안에서 사용하면 안전</u>



메서드 호출과 달리, 상속은 캡슐화 원칙을 위반함. 하위 클래스가 정상 동작하기 위해서는 상위 클래스의 구현에 의존할 수 밖에 없다. 

```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;
    
    public InstrumentedHashSet(){}
    
    public InstrumentedHashSet(int intitCap, float loadFactor){
        super(initCap, loadFactor);
    }
    
    @Override public boolean add(E e){
        addCount++;
        return super.add(e);
    }
    
    @Override public boolean addAll(Collection<? extends E> c){
        addCount += c.size();
        return super.addAll(c);
    }
    
    public int getAddCount(){
        return addCount;
    }
}
```





```java
InstrumentedHashSet<String> s = new InstrumentedHashSet<String>();
s.addAll(Arrays.asList("snap", "crackle", "pop"));
```

이렇게 3개의 아이템을 넣었을 때, count를 1씩 증가시키는 프로그램이다. hashSet를 상속해서 구현했다. 문제는 addAll()이라는 메서드가, add()를 기반으로 만들어 졌기때문에 원하는 결과인 3이 나오지 않고, 6이 나온다.



이런 문제의 원인?: 메서드 재정의!!!!!!!! 이다.



해결책

기존의 클래스를 상속하는 대신, 새로운 클래스에 기존 클래스 객체를 참조하는 private 필드 하나를 두는 것이다. 이런 설계 기법을 구성(Composition)이라고 부름. 기존의 클래스가 새 클래스의 일부(component)가 되기 때문에 그렇게 부름 

새로운 클래스에 포함된 각각의 메서드는 기존 클래스에 있는 메서드 가운데 필요한 것을 호출해서 그 결과를 반환하면 된다. 이런 구현 기법을 전달(forwarding)이라고 하고, 전달 기법을 사용해 구현된 메서드를 전달메서드(forwarding method)라고 부른다. 





## 아이템 19 - 상속을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 

재정의 가능 메서드를 내부적으로 어떻게 사용하는지 반드시 문서로 남겨라 

- AbstractiCollection의 remove()

```java
/**
     * {@inheritDoc}
     *
     * <p>This implementation iterates over the collection looking for the
     * specified element.  If it finds the element, it removes the element
     * from the collection using the iterator's remove method.
     *
     * <p>Note that this implementation throws an
     * <tt>UnsupportedOperationException</tt> if the iterator returned by this
     * collection's iterator method does not implement the <tt>remove</tt>
     * method and this collection contains the specified object.
     
   		콜렉션의 반복자 메소드에 의해 리턴 된 반복자가 remove 메소드를 구현하지 않고,이 콜렉션에 지정된 오브젝트가 포함   
   		되어있는 경우에 UnsupportedOperationException이 발생

     
     * @throws UnsupportedOperationException {@inheritDoc}
     * @throws ClassCastException            {@inheritDoc}
     * @throws NullPointerException          {@inheritDoc}
     */
    public boolean remove(Object o) {
        Iterator<E> it = iterator();
        if (o==null) {
            while (it.hasNext()) {
                if (it.next()==null) {
                    it.remove();
                    return true;
                }
            }
        } else {
            while (it.hasNext()) {
                if (o.equals(it.next())) {
                    it.remove();
                    return true;
                }
            }
        }
        return false;
    }
```

Iterator메서드를 재정의하면 remove가 영향을 받는 다는 사실을 알 수 있다. 게다가 iterator 메서드가 반환하는 iterator 객체가 remove 메서드에 어떤 영향을 주는지 정확하게 명시되어 있다. 



- AbstractList의 removeRange()



상속을 고려하여 클래스를 설계할 때 protected로 선언할 멤버는 어떻게 정하나? 

- 실제로 하위클래스를 만들어 보면서 테스트 하는 것 
  - 중요한 멤버를 protected로 선언하는 것을 잊었다면, 하위 클래스를 만들때 영향을 받음
  - 반대로, 하위 클래스를 몇 개 만들어 봐도 사용할 일이 없었던 protected 멤버는 private으로 선언 해야 함



상속을 허용하려면 반드시 따라야할 제약사항 

1. 생성자는 직/간접적으로 재정의 가능 메서드를 호출해서는 안된다. 



```java
public class Super {
  // 생성자가 재정의가능 메서드를 호출하는 잘못된 사례
  public Super(){
    overrideMe();
  }
  public void overrideMe();
}
```

```java
public final class Sub extends Super {
  private final Date date; 
  
  Sub(){
    // 상위클래스 생성자 먼저 호출
    date = new Date();
  }
  
  // 상위 클래스 생성자가 호출하게 되는 재정의 메서드
  @Override
  public void overrideMe(){
    System.out.println(date);
  }
  
  public static void main(String[] args){
    Sub sub = new Sub();
    sub.overrideMe();
  }
}
```

두번 date가 찍힐것을 예상했지만, 첫번째는 null, 두번째만 date값이 찍힌다. 

Main메서드의 `new Sub()` 를 하게되면 하위클래스의 생성자가 호출 -> 상위 클래스의 생성자 호출 -> overrideMe()를 호출 (하지만 date값이 초기화만 되었고 아무 값이 없다.) 그리고 -> sub생성자의 `new Date()`가 실행되고 `date` 필드에 날짜 값이 할당된다. -> `sub.overrideMe()`를 호출하면 -> date값이 찍힌다. 



> NullPointerException이 발생하지 않은 이유? 
>
> System.out.println() 메서드가 null인자에 대해서 잘 대처하도록 구현되어 있기 때문



- Cloneable과 Serializable 인터페이스를 사용할 경우 상속용 클래스를 설계하는데 더 까다롭다.
  - 상속을 위한 클래스를 설계하면 클래스에 상당한 제약이 가해진다. 



# 7장 람다와 스트림

## 아이템 42 - 익명 클래스 보다는 람다를 사용하라

```java
Collections.sort(words, new Comparator<String>(){
  public int compare(String s1, String s2){
    return Integer.compare(s1.length(), s2.length())
  }
})
```

이 코드에서 Comprator 인터페이스가 정렬을 담당하는 추상 전략을 뜻하며, 문자열을 정렬하는 구체적인 전략을 익명 클래스로 구현했다. 하지만 익명 클래스 방식은 코드가 너무 길기 때문에 자바는 함수형 프로그래밍에 적합하지 않았다. 자바 8에 와서 추상 메서드 하나짜리 인터페이스는 특별한 의미를 인정받아 특별한 대우를 받게 되었다. 지금은 함수형 인터페이스라 부르는 이 인터페이스들의 인스턴스를 람다식을 사용해 만들 수 있게 된 것이다. 



```java
Collections.sort(words, (s1,s2)-> Integer.compare(s1.length(), s2.length()))
```



람다는 이름이 없고 문서화도 못한다. 따라서 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 말아야한다. 람다는 한 줄일때 가장 좋고 길어야 세줄 안에는 끝내는게 좋다. 



## 아이템 43 - 람다보다는 메서드 참조를 사용하라

 

```java
map.merge(key, 1, (count, incr)-> count  + incr)
```



```java
map.merge(key, 1, Integer::sum)
```



람다로 작성할 코드를 새로운 메서드에 담은 다음, 람다 대신 그 메서드 참조를 사용하는 식이다. 

때론 람다가 메서드 참조 보다 간결할 때가 있다. 주로 메서드와 람다가 같은 클래스에 있을 때 그렇다.



| 메서드 참조 유형   | 예                     | 같은 기능을 하는 람다                                   |
| ------------------ | ---------------------- | ------------------------------------------------------- |
| 정적               | Integer::parseInt      | str -> Integer.parseInt(str)                            |
| 한정적(인스턴스)   | Instant.now()::isAfter | Instant then  = Instant.now();<br />t-> then.isAfter(t) |
| 비한정적(인스턴스) | String::toLowerCase    | str->str.toLowerCase();                                 |
| 클래스 생성자      | TreeMap<K,V>::new      | ()-> new TreeMap<K,V>();                                |
| 배열 생성자        | int[]::new             | len-> new int[len]                                      |



> 핵심정리 
>
> 메서드 참조는 람다의 간단 명료한 대안이 될 수 있다. 메서드 참조 쪽이 짧고 명확하다면 메서드 참조를 쓰고, 그렇지 않을때만 람다를 사용하라. 





# 8장 메서드

메서드를 설계할 때 주의할 점들을 살펴본다. 

## 아이템 49 - 매개변수가 유효한지 검사하라

메서드와 생성자 대부분은 입력 매개변수의 값이 특정 조건을 만족하기를 바란다. 메서드 몸체가 실행되기 전에 매개변수를 확인한다면 잘못된 값이 넘어왔을때 즉각적이고 깔끔한 방식으로 예외를 던질 수 있다. 