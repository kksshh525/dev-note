

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



![](http://image.yes24.com/Goods/78226025/800x0)

-   **<u>라인은 문서화</u>**가 굉장히 잘 되어있고, 실수를 했을 때 누군가를 탓하기 보다는 그 문제에 대한 원인과 현상 대책을 뚜렷하게 마련함으로써 계속해서 문서화를 만들어 간다. 이는 곧 회사의 자산이다. 

-   다양한 경험들을 거친 사람들이 있다. 모두가 그런것은 아닐테지만, 정말 찐 개발자부터 오픈소스매니저, 테크 에반젤리스트 등 다양한 경험을 한 사람들을 다양한 방향으로 뻗어나갈 수 있게 서포트를 한다는 것을 느꼈다. **<u>하고 싶은거 다 해봐!</u>**(자율성)

-   개발자는 개발에만 더 집중할 수 있도록, 인프라 적인 요소를 최대한으로 지원한다. 

-   글로벌을 대상으로 하고 있고, 본사, 지사 다양한 국적의 사람들과 교류하기 때문에 협업/커뮤니케이션에 대한 배려를 해준다. 예를 들어 간단한 채팅도 파파고 수준의 실시간 번역을 통해서 메세지가 주고 받어 지고, 중요한 wiki문서도 영문/한국어/일본어 등으로 번역되어서 다양한 국적의 사람들에게 동일한 내용의 리소스를 전달한다. 

    









## More study about...

-   http schema
-   MD5 hash
-   WebRequest vs HttpServletRequest랑뭐가 다르나?
-   RED Attatck
-   XSS Attack
-   http Content-Disposition Header