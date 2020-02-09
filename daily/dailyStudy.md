

## 1/26

https://docs.spring.io/spring/docs/4.2.x/spring-framework-reference/html/transaction.html

-   스프링에서 transaction management는 2가지 방법이 있다.
    -   declarative transaction mangagement(선언적)
    -   programmatic transaction management(프로그래밍적)
        -   transactionTemplate
        -   PlatformTranscationManager 구현하기



https://en.wikipedia.org/wiki/Service_provider_interface

-   SPI란? 
    -   service는 interface나 추상클래스의 집합을 의미하고, service provider는 위의 service를 구현한 것을 의미한다. 
        -   기본정의는 Service Provider Interface는 thrid party에 의해서 구현된, 확장된 API 이다. 

https://en.wikipedia.org/wiki/Java_Naming_and_Directory_Interface

-   JNDI? java naming and directory interface
    -   Java API로 directory service 를 위함
        -   name으로 접근해서 data에 접근하는 방법



https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#filters

-   Filter
    -   Form Data 
        -   requet의 body의 form데이터를 ServletRequest로 감싸서 ServletRequest.getParameter*()로 읽어서 사용한다. 이렇게 만들어 주는 놈이 `FormContentFilter`라는 놈이다.
-   Forwarded Headers
    -   proxy(ex. 로드밸런서)를 통해서 host,port, shcema가 달라지니까, 기존의 origin의 host, port, shcema 정보를 알 필요가 있다. 
-   Shallow ETag
    -   `ShallowEtagHeaderFilter` ETag를 만든다. 하는 일은 header정보에 MD5해쉬로 계산된 결과값이 동등하면(변하지 않으면) return 304(NOT_MODIFIED) 
        -   Controller 레벨에서 HTTP CACHE를 이용한다. 

-   CORS 
    -   Controller 레벨에서 쉽게 사용가능하다.
        -   근데 spring security를 사용한다면, built-in 된 `CorsFilter` 에 의존해서 Spring Security chain의 order를 명시해야 한다. 



https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-controller



-   1.3 Annotated Controllers
    -   URI variables는 자동으로 type conversion 된다. (int, Long, Date)  (DataBinder, Type Conversion)
    -   conent-type이란게 request, response 도있다.
        -   요청에 대해서 좁힘 Content-Type - consumes
        -   요청에 대해서 좁힘 Accept - produces



-   나는 라인 개발자 입니다- 책리뷰
    -   **<u>라인은 문서화</u>**가 굉장히 잘 되어있고, 실수를 했을 때 누군가를 탓하기 보다는 그 문제에 대한 원인과 현상 대책을 뚜렷하게 마련함으로써 계속해서 문서화를 만들어 간다. 이는 곧 회사의 자산이다. 

    -   다양한 경험들을 거친 사람들이 있다. 모두가 그런것은 아닐테지만, 정말 찐 개발자부터 오픈소스매니저, 테크 에반젤리스트 등 다양한 경험을 한 사람들을 다양한 방향으로 뻗어나갈 수 있게 서포트를 한다는 것을 느꼈다. **<u>하고 싶은거 다 해봐!</u>**(자율성)

    -   개발자는 개발에만 더 집중할 수 있도록, 인프라 적인 요소를 최대한으로 지원한다. 

    -   글로벌을 대상으로 하고 있고, 본사, 지사 다양한 국적의 사람들과 교류하기 때문에 협업/커뮤니케이션에 대한 배려를 해준다. 예를 들어 간단한 채팅도 파파고 수준의 실시간 번역을 통해서 메세지가 주고 받어 지고, 중요한 wiki문서도 영문/한국어/일본어 등으로 번역되어서 다양한 국적의 사람들에게 동일한 내용의 리소스를 전달한다. 

      



## 1/27 

-   DataBinder 
    -   @Controller @ControllerAdvice는 @InitBinder 메서드를 가지고 있고, 얘는 WebDataBinder 인스턴스를 Initializing 한다.



CORS

-   SpringMVC의 HandlerMapping 구현체가 CORS 를 위한 built-in support를 제공한다. 
-   Configuration을 통해서 설정하고, URL Pattern에 맞춰서 CORS 요청 헤더에 정보를 넣고, response 헤더에 정보를 담아서 반환한다. 
-   preflight 느낌으로 사전에 URL 패턴 매칭을 통해서 판단한다. 

-   Spring Security에서 CORSFilter를 사용할 수도 있고, SpringMVC에서 제공하는 CORS Support 를 그대로 사용할 수 있다. Security에서 제공하는 CorsFilter를 사용하면, Security를 타기전에 먼저 CORS 프로세싱이 진행된다. 왜? pre-flight request 에는 쿠키들을 포함하지 않기 때문에 해당 유저가 승인된 유저인지 아닌지를 먼저 판단해서 거절할지를 결정한다. 
    https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#cors



RestTemplate vs WebClient

-   두개다 client-side REST 엔드포인트에 접근하는 방법이다.
-   RestTemplate 동기, blocking
-   WebClient  비동기, non-blocking
-   더이상 RestTemplate 개발안한다. 나중에 WebClient로 대체될 것이다. 그렇다고 모든 소스코드를 코칠 필요는 없다. compatiable하기 때문에

>   https://www.youtube.com/watch?time_continue=13&v=zXBTTOQ_iSQ&feature=emb_logo





## 2/3

https://github.com/umanking/dev-note/blob/master/spring/spring-security.md

-   Spring security 복습 

    -   인증과 인가의 차이: `인증`은 id/password로 일치하는지를 판별, `인가`는 인증된 사용자에 한해서 ROLE를 부여함 

    -   SecurityContextHolder > SecurityContext  > Authentication객체 (Principal, GrantAuthority) 가 들어 있다. 

    -   ```java
        Authentication auth= SecurityContextHolder.getcontext().getAuthentication();
        
        User principal = (User) auth.getPrincipal()
            
        Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
        ```





https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2

-   oauth role 4가지

    -   Resource Owner
    -   Client
    -   Resource Server
    -   Authorization Server 

    

## 2/4

https://www.youtube.com/watch?v=mPB2CZiAkKM

우아한 레디스 강대명님 

-   redis는 collection을 제공함 memcached 는 제공안함
    -   이미 제공하는 라이브러리를 쓰면 좋은점? 
        -   개발의 편리성
        -   개발의 난이도



-   http schema
-   MD5 hash
-   WebRequest vs HttpServletRequest랑뭐가 다르나?
-   RED Attatck
-   XSS Attack
-   http Content-Disposition Header
-   netty
-   java NIO package? 
-   메모리 파편화

-   jemelloc 

-   메모리 paging 구조? 

-   skiptable ? 자료구조

-   ziplist 구조

-   spring security oauth RedisToken Store





2/7

https://www.baeldung.com/parameterized-tests-junit-5

- Junit5에서 나온 parameterized test 
- 하나의 메서드를 다양한 파라미터를 통해서 여러번 테스트 가능함
- @ParameterziedTest , @ValueSource (배열 형태로 넣어줌)
  - CSV files 실 파일 테스트
  - @EnumSource를 통한 테스트 



2020-02-09 

-   https://jojoldu.tistory.com/412 
    -   일급 컬렉션 사용해야 한다. 
        -   map, set, 컬렉션은 final로 막을 수 없다. 
        -   서비스 비즈니스에 의존적이다. —> 클래스를 명시해서 분리한다. 
            -   constructor 랑 getter 를 통해서만 접근하게 만든다. immutalbe collection wrapper가 된다.

http://www.differencebetween.net/technology/difference-between-git-fetch-and-git-pull/

-   git fetch vs git pull 의 차이 
-   ![](https://images.osteele.com/2008/git-transport.png)



