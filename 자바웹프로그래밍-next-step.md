

## 11장 

### 1. 왜 DI가 필요한가? 



- DI는 객체 간의 의존관계를 어떻게 해결하느냐에 따른 새로운 접근 방식이다. 



**<u>즉, Calendar 인스턴스에 대한 생성을 getDateMessag()가 결정하는 것이 아니라, DateMessageProvider 외부에서 Calendear인스턴스를 생성한 후 전달하는 구조로 바꿔야 한다.</u>** 외부에서 Calendar 인스턴스를 전달하는 방법은 생성자를 통해 전달하거나 getDateMessage() 메소드 인자로 전달하는 두 가지 방법이 있다. 

### 2. DI를 적용하면서 쌓이는 불편함

