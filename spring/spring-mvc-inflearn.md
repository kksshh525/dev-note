>  인프런 백기선님의 Spring MVC 강의를 보고 정리한 내용입니다.

<!-- TOC -->

- [들어가며](#%EB%93%A4%EC%96%B4%EA%B0%80%EB%A9%B0)
	- [서블릿](#%EC%84%9C%EB%B8%94%EB%A6%BF)
	- [서블릿 리스너와 필터?](#%EC%84%9C%EB%B8%94%EB%A6%BF-%EB%A6%AC%EC%8A%A4%EB%84%88%EC%99%80-%ED%95%84%ED%84%B0)
	- [Dispatcher Servlet 동작 원리](#dispatcher-servlet-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC)
		- [DispatcherServlet 디버깅](#dispatcherservlet-%EB%94%94%EB%B2%84%EA%B9%85)
- [Spring MVC 구성요소](#spring-mvc-%EA%B5%AC%EC%84%B1%EC%9A%94%EC%86%8C)
		- [DispatcherServlet 기본전략](#dispatcherservlet-%EA%B8%B0%EB%B3%B8%EC%A0%84%EB%9E%B5)
		- [다양한 DispatcherServlet 전략들](#%EB%8B%A4%EC%96%91%ED%95%9C-dispatcherservlet-%EC%A0%84%EB%9E%B5%EB%93%A4)
			- [MultipartResolver](#multipartresolver)
			- [LocaleResolver](#localeresolver)
			- [ThemeResolver](#themeresolver)
			- [HandlerMapping](#handlermapping)
			- [HandlerAdapter](#handleradapter)
			- [HandlerExceptionResolver](#handlerexceptionresolver)
			- [RequestToViewNameTranslator](#requesttoviewnametranslator)
			- [ViewResolver](#viewresolver)
			- [FlashMapManager](#flashmapmanager)
	- [핸들러 인터셉터](#%ED%95%B8%EB%93%A4%EB%9F%AC-%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0)
	- [리소스 핸들러](#%EB%A6%AC%EC%86%8C%EC%8A%A4-%ED%95%B8%EB%93%A4%EB%9F%AC)
	- [HTTP 메세지 컨버터](#http-%EB%A9%94%EC%84%B8%EC%A7%80-%EC%BB%A8%EB%B2%84%ED%84%B0)

<!-- /TOC -->


# 들어가며

자바 EE는 웹애플리케이션을 개발 할 수 있는 HTTP Servlet 스펙과 API를 제공한다. 스프링 MVC는 서블릿 기반의 애플리케이션 개발할 때 보다 쉽고 빠르게 개발 할 수 있는 프레임워크를 제공한다.





## 서블릿

서블릿 

- 자바 EE는 웹 애플리케이션 개발용 스펙과 API 제공
- 요청 당 스레드(만들거나, 풀에서 가져다가 사용)
- 중요한 클래스 중 하나 HttpServlet



서블릿 이전에 사용하던 기술 CGI 

- 요청 당 프로세스 만들어서 사용



서블릿 생명주기

- 서블릿 컨테이너가 서블릿 인스턴스의 init() 메소드를 호출하여 초기화 한다.

- - 최초 요청을 받았을 때 한번 초기화 하고 나면 그 다음 요청부터는 이 과정을 생략한다.

- 서블릿이 초기화 된 다음부터 클라이언트의 요청을 처리할 수 있다. 각 요청은 별도의 쓰레드로 처리하고 이때 서블릿 인스턴스의 service() 메소드를 호출한다.

- - 이 안에서 HTTP 요청을 받고 클라이언트로 보낼 HTTP 응답을 만든다.
  - service()는 보통 HTTP Method에 따라 doGet(), doPost() 등으로 처리를 위임한다.
  - 따라서 보통 doGet() 또는 doPost()를 구현한다.

- 서블릿 컨테이너 판단에 따라 해당 서블릿을 메모리에서 내려야 할 시점에 destroy()를 호출한다.





## 서블릿 리스너와 필터?

서블릿 리스너

- 웹 애플리케이션에서 발생하는 주요 이벤트를 감지하고 각 이벤트에 특별한 작업이 필요한 경우에 사용할 수 있다.

- - 서블릿 컨텍스트 수준의 이벤트

  - - 컨텍스트 라이프사이클 이벤트
    - 컨텍스트 애트리뷰트 변경 이벤트

  - 세션 수준의 이벤트

  - - 세션 라이프사이클 이벤트
    - 세션 애트리뷰트 변경 이벤트

서블릿 필터

- 들어온 요청을 서블릿으로 보내고, 또 서블릿이 작성한 응답을 클라이언트로 보내기 전에 특별한 처리가 필요한 경우에 사용할 수 있다.
- 체인 형태의 구조

![img](https://lh6.googleusercontent.com/7laA8AB0wDUrOkRV9LA2E9saOuip6pE-HX9X6Fr05oQJjw45tf206Hzz93ibYbIx814PL4xC5RDeCsUr0dYCbO-FFA149TTXly3Z8w-9hPmMyEI0g83tZiiAS6h92sKTHVwad4yQ)





## Dispatcher Servlet 동작 원리

-  spring MVC 가 servelt container가 먼저 뜨고, Application ContextLoad, Dispatcher Servlet을 통해서 연결한다면
- Srping Boot는 스프링부트가 먼저 뜨고, 그 안에 내장된 톰캣에 코드로 servlet을 띄운다.  





### DispatcherServlet 디버깅 

- 기본 dispatcherServlet 핸들러는 2개 

  - BeanNameHanlderMapping <— Controller를 빈으로 등록한 경우 
  - RequestMappingHandlerMapping <— 어노테이션 기반 Controller로 코드 짜는 경우 

- InitStratiges

  ```java
  //DispatcherServlet.java
  
  protected void initStrategies(ApplicationContext context) {
  		initMultipartResolver(context);
  		initLocaleResolver(context);
  		initThemeResolver(context);
  		initHandlerMappings(context);
  		initHandlerAdapters(context);
  		initHandlerExceptionResolvers(context);
  		initRequestToViewNameTranslator(context);
  		initViewResolvers(context);
  		initFlashMapManager(context);
  	}
  
  
  //DispatcherServlet.properties
  
  ```

  

# Spring MVC 구성요소



> DispatcherSevlet의 기본전략과 여러가지 유형에 대해서 알아보자.

### DispatcherServlet 기본전략 

```java
protected void initStrategies(ApplicationContext context) {
		initMultipartResolver(context);
		initLocaleResolver(context);
		initThemeResolver(context);
		initHandlerMappings(context);
		initHandlerAdapters(context);
		initHandlerExceptionResolvers(context);
		initRequestToViewNameTranslator(context);
		initViewResolvers(context);
		initFlashMapManager(context);
	}
```

DispatcherServlet 의 초기화 전략은 다음과 같다. 초기화 구조는 거의다 비슷하기 때문에 여기에서는 `initViewResolvers(context)` 를 살펴보자. 

```java

	/**
	 * Initialize the ViewResolvers used by this class.
	 * <p>If no ViewResolver beans are defined in the BeanFactory for this
	 * namespace, we default to InternalResourceViewResolver.
	 */
	private void initViewResolvers(ApplicationContext context) {
		this.viewResolvers = null;

		if (this.detectAllViewResolvers) {
			// Find all ViewResolvers in the ApplicationContext, including ancestor contexts.
			Map<String, ViewResolver> matchingBeans =
					BeanFactoryUtils.beansOfTypeIncludingAncestors(context, ViewResolver.class, true, false);
			if (!matchingBeans.isEmpty()) {
				this.viewResolvers = new ArrayList<>(matchingBeans.values());
				// We keep ViewResolvers in sorted order.
				AnnotationAwareOrderComparator.sort(this.viewResolvers);
			}
		}
		else {
			try {
				ViewResolver vr = context.getBean(VIEW_RESOLVER_BEAN_NAME, ViewResolver.class);
				this.viewResolvers = Collections.singletonList(vr);
			}
			catch (NoSuchBeanDefinitionException ex) {
				// Ignore, we'll add a default ViewResolver later.
			}
		}

		// Ensure we have at least one ViewResolver, by registering
		// a default ViewResolver if no other resolvers are found.
		if (this.viewResolvers == null) {
			this.viewResolvers = getDefaultStrategies(context, ViewResolver.class);
			if (logger.isTraceEnabled()) {
				logger.trace("No ViewResolvers declared for servlet '" + getServletName() +
						"': using default strategies from DispatcherServlet.properties");
			}
		}
	}
```

크게 `this.detectAllViewResolvers` 의 boolean 값(default = true) 에 의해서 viewReoslvers 들을 찾는다. 그리고 만약에 `this.viewResolvers` 가 null 이면 `getDefaultStrageies()` default 전략으로 viewResolver를 찾는다. 



아래는 `DispacherServlet.properties` 에 정의되어 있는 기본전략이다.

```properties
# Default implementation classes for DispatcherServlet's strategy interfaces.
# Used as fallback when no matching beans are found in the DispatcherServlet context.
# Not meant to be customized by application developers.

org.springframework.web.servlet.LocaleResolver=org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver

org.springframework.web.servlet.ThemeResolver=org.springframework.web.servlet.theme.FixedThemeResolver

org.springframework.web.servlet.HandlerMapping=org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,\
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping

org.springframework.web.servlet.HandlerAdapter=org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter,\
	org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter,\
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter

org.springframework.web.servlet.HandlerExceptionResolver=org.springframework.web.servlet.mvc.method.annotation.ExceptionHandlerExceptionResolver,\
	org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver,\
	org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver

org.springframework.web.servlet.RequestToViewNameTranslator=org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator

org.springframework.web.servlet.ViewResolver=org.springframework.web.servlet.view.InternalResourceViewResolver

org.springframework.web.servlet.FlashMapManager=org.springframework.web.servlet.support.SessionFlashMapManager
```

여기에서 `ViewSolver` 의 기본 전략은 `InternalResourceViewResovler` 이다





### 다양한 DispatcherServlet 전략들



#### MultipartResolver

- 파일 업로드 요청 처리에 필요한 인터페이스
- HttpServletRequest를 MultipartHttpServletRequest로 변환해주어 요청이 담고 있는 File을 꺼낼 수 있는 API 제공.



#### LocaleResolver

- 클라이언트의 위치(Locale) 정보를 파악하는 인터페이스
- 기본 전략은 요청의 accept-language를 보고 판단.



#### ThemeResolver

- 애플리케이션에 설정된 테마를 파악하고  변경할 수 있는 인터페이스
- 참고: <https://memorynotfound.com/spring-mvc-theme-switcher-example/>



#### HandlerMapping

- 요청을 처리할 핸들러를 찾는 인터페이스



#### HandlerAdapter

- HandlerMapping이 찾아낸 “핸들러”를 처리하는 인터페이스



#### HandlerExceptionResolver

- 요청 처리 중에 발생한 에러 처리하는 인터페이스



#### RequestToViewNameTranslator

- 핸들러에서 뷰 이름을 명시적으로 리턴하지 않은 경우, 요청을 기반으로 뷰 이름을 판단하는 인터페이스

```java
		@GetMapping("/sample")
    public String sample() {
        return "sample";
    }
		
		//아무것도 리턴을 void로 해도, /sample 요청에 의해서 viewName으로 바꿔준다.
		@GetMapping("/sample")
    public void sample() {}

```





#### ViewResolver

- 뷰 이름(string)에 해당하는 뷰를 찾아내는 인터페이스



#### FlashMapManager

- 폼정보를 submit하고 POST 요청을 했을 때 -> GET 요청으로 화면을 리다이렉트 시키는 목적
  여기에서 브라우저 화면을 refresh 했을때, 다시 form 정보로 보여지지 않게 하기 위함 !!









@EnableMVC는 DelegatingWebMvcConfiguration 클래스이고, delegation 패턴을 활용하기 때문에 아주 손쉽게 조금만 사용해서 변경가능하다. 

/app/userName=andrew&age=32

```java
@GetMapping("/app/")
public String get(@ModelAttribute modelattribe){
  
}

@GetMapping("/app/{id}")
public String getId(@Pathvariable Long id){
  
}

//  /app/userName=andrew
@GetMapping("/app/")
public String getUserName(@RequestParam String userName){
  
}
```



messageConverter는 json 요청 본문을 파싱한다. @RequestBody 



스프링 부트를 사용하면 Foratter를 등록하지 않아도, 빈으로만 설정하면, 알아서 넣어준다 (WebMvcAutoConfigure)

```java
@Override
		public void addFormatters(FormatterRegistry registry) {
			for (Converter<?, ?> converter : getBeansOfType(Converter.class)) {
				registry.addConverter(converter);
			}
			for (GenericConverter converter : getBeansOfType(GenericConverter.class)) {
				registry.addConverter(converter);
			}
			for (Formatter<?> formatter : getBeansOfType(Formatter.class)) {
				registry.addFormatter(formatter);
			}
		}
```





- 스프링 부트에서 확장: @Configuration + implements WebMvcConfigurer 
- 스프링 부트에서 제공하는 설정을 사용하고 싶지 않으면 : @Configuration + @EnableWebMvc +implements WebMvcConfigurer 
  - 가능한 이유는 DelegatingWebMvcConfiguration를 Import를 하고, 부트에서 WebMvcAutoConfigurer에서 빈 설정 조건에서 DelegatingWebMvcConfiguration이 빠져있으면 -> WebMvcAutoConfigurer에서 설정한 값들을 사용하겠다고 명시되어 있음. 







war로 패키징하게되면, SpringBootServletInitializer 라는 클래스를 상속받게되는데, 독립적으로 실행할 때는 DemoApplication의 main()메서드를 실행하고, war로 패키징된 결과물을 톰캣에 서블릿엔진에 배포하게 되면, WebApplicationInitializer를 상속받아 최적화로 구성되어 있는 SpringBootServeltInitializer를 사용하게 된다. 





Formatter보다 Converter가 더General하다. 
Formatter는 객체 - 문자열 / Converter는 객체-객체 로 변환까지 가능



spring data jpa에서 제공하는 도메인 클래스 컨버터를 사용한다. 

- spring-data-jpa
- 사용한 h2 db 추가 

pathvariable로 id를 받으면 -> 메서드의 파라미터를 Person (객체)로 받는 변환해 주는 작업 (도메인 클래스 컨버터)를 제공한다. 



## 핸들러 인터셉터

HandlerInterceptor

- 핸들러 맵핑에 설정할 수 있는 인터셉터
- 핸들러 전, 후, 완료(렌더링 후) 시점에 작업 적용가능
- 여러 핸들러에서 반복적으로 사용하는 코드를 줄이고 싶을 때 사용
  - 로깅, 인증 체크, Locale 변경 등등

>  핸들러 인터셉터는 핸들러를 파라미터 정보로 넘어가서 해당 핸들러에대해서 세밀하게 조정가능(Filter 보다)



1. preHandle1
2. preHandle2
3. 요청처리
4. postHandle2
5. postHandle1
6. 뷰렌더링
7. afterCompletion2
8. afterCompletion1



핸들러 인터셉터 vs 필터 

- 핸들러 인터셉터 (조금더 spring mvc에 특화되어 있는)
- 필터 - 일반적인 구현인 경우



인터셉터는 특정 패턴에만 적용가능, /hello/* 인경우에만, order를 통해서 인터셉터의 순서도 지정가능



## 리소스 핸들러 

애플리케이션의 리소스(이미지, 자바스크립트, CSS, HTML 파일과 같은 정적 리소스)를 처리하는 핸들러



Default 서블릿 

- 서블릿 컨테이너가 기본으로 제공하는 서블릿으로 정적인 리소스를 처리할 때 사용



스프링 MVC 리소스 핸들러 맵핑 등록 

- …...





## HTTP 메세지 컨버터



HTTP 메세지 컨버터는 @RequestBody, @ResponseBody인 경우에 본문을 읽어 들이는 용도로 사용한다.  



- contentType = 요청 본문에  이 컨텐츠가 어떤 타입인지 서버에 알려준다. 
- accept =  이 요청에 대한 응답으로, 어떤 데이터가 담길지를 원한다. 



## 31. HTTP 요청 매핑하기 - 요청 메서드

Http Method

- GET, POST, PUT, PATCH, DELETE 등등

GET 요청

- 클라이언트가 서버의 리소스를 요청할 때 사용한다. 
- 캐싱할 수 있다. 
- 브라우저 기록에 남는다.
- 북마크 할 수 있다.
- 민감한 데이터를 보낼 때 사용하지 말것
- Idemponent



Post 요청

- 클라이언트가 서버의 리소스를 수정하거나 새로 만들때 사용한다.
- 서버에 보내는 데이터를 POST요청 본문에 담는다.
- 캐시 할 수 없다. 
- 브라우저 기록에 남지 않는다.
- 북마크 할 수 없다.
- 데이터 길이 제한이 없다. 



PUT 요청

- URI에 해당하는 데이터를 새로 만들거나 수정할 때 사용한다.
- POST와 다른점은 URI에 대한 의미가 다르다. 
  - POST의 URI는 보내는 데이터를 처리할 리소스를 지칭하며
  - PUT의 URI는 보내는 데이터에 해당하는 리소스를 지칭한다. 
- Idempotent



PATCH 요청

- PUT과 비슷하지만, 기존 엔티티와 새 데이터의 차이점만 보낸다는 차이가 있다. 
- Idempotent



DELETE 요청

- URI에 해당하는 리소스를 삭제할 때 사용한다.
- Idempotent 



스프링 웹 MVC에서 HTTP method 맵핑하기

- @RequestMapping(method= RequestMethod.GET)
- @RequestMapping(method= {RequestMethod.GET, RequestMethod.POST})
- @GetMapping, @PostMapping, 
- 

