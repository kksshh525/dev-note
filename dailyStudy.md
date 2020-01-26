1/26

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







More study about

-   http schema
-   MD5 hash
-   WebRequest vs HttpServletRequest랑뭐가 다르나?