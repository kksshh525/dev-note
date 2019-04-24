> Mozlia MDN HTTP Tutorial을 읽고 정리한 내용입니다.

# HTTP 개요



- http는 상태는 없다. http 쿠키는 상태가 있는 세션을 만들도록 해준다



origin 정책브라우저는 웹사이트간의 엄격한 분리를 강제한다



http 흐름 

1. TCP 연결을 연다
2. HTTP 메세지를 전송한다
3. 서버에 의해 전송된 응답을 읽어 들인다. 
4. 연결을 닫거나 다른 요청들을 위해 재사용

HTTP 파이프라이닝이 활성화 되면, 첫번째 응답을 완전히 수신할 때 까지 기다리지 않고, 여러 요청을 보낼 수 있다. 





http 기반 API 

http 기반으로 가장 일반적으로 사용된 API는 user agent와 서버간의 데이터를 교환하는데 사용 될 수 있는 XMLHttpRequest API 이다. 







# HTTP 캐시

