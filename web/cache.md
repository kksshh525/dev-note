> 오늘은 Ehcache에 대해서 알아보도록 하겠습니다. 



캐쉬란 성능 개선을 위해서 사용합니다. 먼저 캐쉬는 브라우저 캐쉬와 서버 캐쉬가 존재합니다. 

### 브라우저 캐쉬

브라우저에서 필요한 자원에 대해서 서버로 요청을 하는데 매번 요청하는데 비용이 들기 때문에, 일정 부분을 캐슁해서 브라우저에서 기억하고 있다가 필요할 때마다 캐슁되어있는 데이터에서 즉시 바로 돌려줄 수 있습니다. 



### 서버 캐쉬

사실은 이번 포스팅에서 핵심은 이 부분입니다. 특히나 쇼핑몰에서 특정 상품군을 조회할 때, 1억개의 이상의 상품군을 조회(극단적으로)하는데 매번 DB에서 Select쿼리를 날린다면 속도면에서 굉장히 큰 낭비가 발생합니다. 







## 참고

https://hyeooona825.tistory.com/86
