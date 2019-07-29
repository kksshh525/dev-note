# Java8

## 1.Lambda

#### 함수형 프로그래밍

1. 순수 함수(pure function)

   [부작용(side-effect)](https://ko.wikipedia.org/wiki/%EB%B6%80%EC%9E%91%EC%9A%A9_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99))이 없는 함수, 즉, 함수의 실행이 외부에 영향을 끼치지 않는 함수를 뜻한다. 따라서 순수한 함수는 [스레드 안전](https://ko.wikipedia.org/wiki/%EC%8A%A4%EB%A0%88%EB%93%9C_%EC%95%88%EC%A0%84)하고, 병렬적인 계산이 가능하다.

2. 익명 함수(anonymous function)

   이름이 없는 함수를 뜻한다. 이러한 익명 함수는 대부분 프로그래밍 언어에서 '람다식'으로 대치된다. 

3. 고계 함수(high-order function)

   함수를 인자로 넘기거나, 값으로 반환이 가능한 함수



기존의 자바(**since Java7**)에서 함수라는 개념이 없다. 함수형 인터페이스(**단, 하나의 메소드만이 선언된 인터페이스**)를 도입. Java8에서 함수형 인터페이스라는 개념과 람다식 표현을 통해 입력에 의해서만 출력이 결정되도록 '순수한 함수'를 표현할 수 있게 되었고, 람다식으로 표현함 —> ''익명함수'를 정의할 수 있게 되었고, 함수형 인터페이스의 메소드에서 또다른 함수형 인터페이스를 인자로 받을 수 있도록 하여 --> '고계 함수'를 정의 할 수 있음,, 결론적으로, 함수형 프로그래밍 언어의 조건을 만족시킴

> **메서드 vs 함수**
>
> 함수는 수학에서 넘어온 개념, method는 자바에서 객체의 행위나 동작을 의미. method는 반드시 class 안에 반드시 포함되어 있어야 한다는 제약이 따름. 이제 자바 1.8부터 람다식을 통해서 method가 하나의 독립적인 기능을 하기 때문에 '함수'라는 용어를 사용



- [함수형 언어의 특징?](https://engineering.linecorp.com/ko/blog/detail/244) 
  - **immutability**
  - First class citizen, first class function , high order function
  - lazy evalauation <— Stream



#### 1.1 람다식

람다식? 메서드를 하나의 **식(expression)** 으로 표현 한 것

```java
//return 값이 있는 경우
int max(int a, int b) {
    return a > b ? a :b;
}

//step 1: 메소드 이름이 지워진 경우
(int a, int b) -> { return a >b ? a:b; }

//step 2: 단일 식이면, return문 생략 가능 
//(statement가 아니라 식으로 끝이나므로 ; 를 붙이지 않는다.) 
(int a, int b) -> a> b ? a:b

//step 3: 파라미터 타입도 추론가능 하기 때문에 생략
(a , b) -> a > b ? a :b
```



#### 1.2 함수형 인터페이스

- 언제 사용? 함수형 인터페이스의 추상메서드가 하나만 정의 되어 있는 경우

- 사실, 람다식 == 익명 클래스의 객체 

  > [익명클래스란?](http://yookeun.github.io/java/2017/01/24/java-anonymousclass/) 이름이 없는 클래스

  ```java
  Car car = new Car();
  
  ==============================
  
  Thread thread = new Thread(new Runnable() {
        @Override
        public void run() {
          System.out.println("hello world");
        }
      });
  ```


- 함수형 인터페이스의 타입의 <u>매개변수</u>와 <u>반환타입</u>
- 람다식의 <u>타입과 형변환</u>
- <u>외부변수를 참조</u>



#### 1.4 java.util.function 기본 패키지

보통 메서드는 매개변수 한 개, 두개, return type이 비슷하다. generic 타입으로 정의하면 매개변수, 리턴타입이 달라져도 지장이 되지 않는다. java에서 지정한 기본 **함수형 인터페이스** 를 사용하면, 메서드 이름도 통일, 재사용성도 높아지니까  사용하는게 권장 --> <u>내 입맛에 맞지 않을땐, 직접 함수형 인터페이스를 만들어서 사용하면 된다!</u>  

**기존에 새롭게 정의해서 썼는데, java.util.function 패키지에 자주쓰는 함수형 인터페이스를 정의함**

| 함수형 인터페이스  | 메서드              | 설명                                     |
| :----------------- | :------------------ | :--------------------------------------- |
| java.lang.Runnable | `void run()`        | 매개변수x, 반환값x                       |
| Supplier<T>        | `T get()`           | 매개변수x, 반환값 O                      |
| Consumer<T>        | `void accept(T t)`  | 매개변수O, 반환값X                       |
| Function<T,R>      | `R apply(T t)`      | 일반적인 함수, T를 받아서 R을 반환       |
| Predicate<T>       | `boolean test(T t)` | 조식을 표현하는데, T를 받아 boolean 반환 |

**매개변수가 2개**

| 함수형 인터페이스 | 메서드                  | 설명                                       |
| :---------------- | :---------------------- | :----------------------------------------- |
| BiConsumer<T,U>   | `void accept(T t, U u)` | 매개변수O, 반환값X                         |
| BiFunction<T,U,R> | `R apply(T t, U u)`     | 일반적인 함수, T,U를 받아서 R을 반환       |
| BiPredicate<T,U>  | `boolean test(T t,U u)` | 조식을 표현하는데, T,U를 받아 boolean 반환 |

**Function의 또다른 변형 Unary Operator**

| 함수형 인터페이스  | 메서드              | 설명                                        |
| :----------------- | :------------------ | :------------------------------------------ |
| UnaryOperator<T>   | `T apply(T t)`      | Function의 자손, 매개변수와 반환타입이 같다 |
| BiUnaryOperator<T> | `T apply(T t, T t)` | BiFunction의 자손, 매개변수와 반환타입 같다 |

언제쓰일까? 

: 스트림의 최종연산에서 reduce() `스트림요소를 줄여나가면서 연산을 수행하고, 최종결과를 반환할때`



**기본형을 사용하는 함수형 인터페이스**

그동안, 함수형 인터페이스의 매개변수와 반환값이 지네릭 타입이었다. 기본형 타입(int..,) 을 처리할때 래퍼클래스를 활용(Integer) 그러나, <u>기본형을 래퍼클래스로 처리하는건 비효율적!(why?)</u> 그래서 보다 효율적으로 처리할 수 있도록 기본형을 사용하는 함수형 인터페이스를 제공한다.  

| 함수형 인터페이스   | 메서드                     | 설명                                             |
| :------------------ | :------------------------- | :----------------------------------------------- |
| DoubleToIntFunction | `int applyAsInt(double d)` | **A**To**B**Function은 입력이 A타입 출력이 B타입 |
| ToIntFunction<T>    | `int applyAsInt(T value)`  | To**B**Function은 출력이 B타입. 입력은 제네릭    |
| IntFunction<R>      | `R apply(T t, U u)`        | AFunction은 입력이 A타입이고, 출력은 제네릭      |
| ObjIntConsumer<T>   | `void accept(T t, U u)`    | ObjAFunction은 입력이 T, A타입이고, 출력 X       |

**컬렉션프레임웍과 함수형 인터페이스**

기존의 컬렉션프레임웍에 새롭게 추가된 함수형 인터페이스

 

#### 1.5 Function의 합성과 Predicate의 결합

디폴트 메서드와 static 메서드가 정의되어 있음

- [interface기능에 대해](http://blog.eomdev.com/java/2016/03/30/default-method.html) (static final, default method도 가능)





#### 1.6 Method reference(메서드 참조)

람다식을 더욱 간결하게 쓸 수 있는 방법. **람다식이 하나의 메서드만 호출하는 경우**에는 '메서드 참조' 라는 방법으로 람다식을 간략히 할 수 있다. 

| 종류                         | 람다                     | 메서드 참조       |
| :--------------------------- | :----------------------- | :---------------- |
| static메서드 (클래스 메서드) | `x->ClassName.method(x)` | ClassName::method |
| 인스턴스 메서드              | `(obj,x)->obj.method(x)` | ClassName::method |
| 특정 객체 인스턴스 메서드    | `x->obj.method(x)`       | obj::method       |

###### 생성자 참조

```java
BiFunction<String, Integer, Person> bifuc1 = (name, age) -> new Person(name, age); 
BiFunction<String, Integer, Person> bifuc2 = Person::new;
```





## 2. Stream

#### 2.1 Stream 이란?

문제1) 코드의 방대함: 우리는 수 많은 데이터를 다룰때,i 컬렉션이나 배열 for문 + 조건문
문제2) 데이터 소스(컬렉션, 배열, 임의의 수)마다 다른 방식으로 다뤄야 함. 
	 ex)List 정렬 - Collections.sort()  , 배열 정렬- Arrays.sort()

해결: 스트림은 **데이터 소스를 추상화**(소스가 무엇이든 간에, 같은 방식으로 다룰 수 있다) 
데이터를 다루는데 자주 사용되는 메서드들을 정의해 놓음([java stream api](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#method.summary))



###### 스트림 특징

1. **스트림은 데이터 소스를 변경하지 않는다.**

2. 스트림은 일회용이다. 

   ```java
   Stream<String> strStream = strList.stream();
   strStream.forEach(System.out::println); //이미 stream 소비했다. 
   long count = strStream.count(); //이미 스트림이 닫혀 있다. 
   ```

   

3. 스트림 작업을 내부 반복으로 처리한다.

  - 외부 반복자는 일반적으로 사용하는 루프처럼 요소를 클라이언트가 직접 컬렉션 1)요소를 하나씩 꺼내와서 2)반복 처리
  - 내부 반복자는 처리할 행동(보통 콜백 함수)을 컬렉션 요소에 넘겨주어 1)반복처리 
    - 장점: 컬렉션 내부에서 어떻게 요소를 반복시킬것인지 컬렉션에 맞기고, <u>개발자는 요소 처리 코드에만 집중</u>

  

  ![그림](http://cfile7.uf.tistory.com/image/2467073655EEA2D1216B8B)

  ![img](http://cfile25.uf.tistory.com/image/2749744558DC59482EDF30)

4. 스트림 연산 

  ```java
  중간연산과 최종연산
  stream.distinct().limit(5).sorted().forEach(System.out::println)
  ```

5. 지연된 연산: 최종 연산이 일어나기 전까지, 중간 연산은 일어나지 않는다. ex) [kevin youtube](https://www.youtube.com/watch?v=7e7FCMFrwcg&list=PLRIMoAKN8c6O8_VHOyBOhzBCeN7ShyJ27&index=7)
   : 어떤 코드 조각을 실행할때, 그 때 그때 값을 평가하지 않고, 결과값이 필요한 시점까지 평가를 늦춘다. 연산을 미룸으로써 불필요한 연산을 줄 일 수 있는 연산 전략의 일종
   하지만, 무한히 큰 자료구조 List를 출력한다고 생각해보면, 계산을 끝내지 못하거나, 메모리를 다 쓰게 될 수 있다. 

6. Stream<Integer> , IntStream : 기본 자료형에 접근할 때는 오토박싱,unboxing 비용이 줄어드는 

7. 병렬 스트림 
   병렬 처리: 하나의 작업을 서브 작업으로 분할하고, 서브 작업들을 분리된 스레드에서 병렬적으로 처리하는 것
   그래서 병렬 처리 스트림을 이용하면, 런타임시에 하나의 작업을 자동으로 서브 작업으로 나누고, **서브 작업의 결과를 자동으로 결합해서 최종 결과물을 생성함**





#### 2.2 스트림 만들기

컬렉션의 최고 조상인 Collection에 stream() 정의 되어 있음, 그렇기 때문에 Collection의 자손인 List, Set를 구현한 컬렉션 클래스들은 모두 스트림을 생성할 수 있다. 

![Image result for collection java](https://qph.fs.quoracdn.net/main-qimg-49a226eed52120114179cee381c1958f)



##### 컬렉션

##### 배열

##### 특정 범위의 정수

##### 임의의 수

##### 람다식-iterate(), generat()

##### 파일

##### 빈 스트림

##### 두 스트림의 연결



#### 2.3 스트림의 중간연산

##### 스트림 자르기 - skip(), limit()

##### 스트림 요소 걸러내기 - filter(), distinct()

##### 정렬 - sorted()

##### 변환 - map()

##### 조회 - peek()

##### mapToInt(), mapToLong(), mapToDouble()

#####flatMap() - Stream<T[]>을 Stream<T>로 변환



#### 2.4 Optional<T>와 OptionalInt

optional<T>는 T타입의 객체를 감싸는 래퍼 클래스이다. 그래서 Optional타입의 객체에는 모든 타입의 참조변수를 담을 수 있다.  `nullpointerException 막아줌` **optional 객체**로 반환하는 메소드들은 **최종연산**과 매우 잘 어울린다. 

**효과적인 사용 방법: 값이 없을때는 대체 값을 생산, 값이 있을 때는 해당 값을 소비하는 메서드를 사용해야 한다.** 

1. Optional 객체 생성
2. Optional 객체 가지고 오기
3. OptionalInt, OptionalLong, OptionalDouble

#### 2.5 최종연산

1. foreach()

2. 조건검사-allMatch(), anyMatch(), noneMatch(), findFirst(), findAny()

3. 통계 - count(), sum(), average(), max(), min()

4. 리듀싱- reduce()

   

#### 2.6 Collection

collect() 스트림의 요소를 수집하는 최종연산. 어떻게 수집할 것인가에 대한 방법 정의

```
1. Collectors 클래스에서 제공하는 static method 
2. Collector 인터페이스를 구현해서 custom !
```



### Summary

- lazy evaluation 다분히 큰 자료구조 이면
- 