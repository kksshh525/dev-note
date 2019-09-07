
## Spring Security Example

> 인프런 강의 요약정리



**요구사항**

- Spring MVC를 이용해서 구현하라
- GET `/` 인증된 사용자, 인증되지 않은 사용자 모두 접근할 수 있다. 
- GET `/info` 누구나 접근할 수 있다. 
- GET `/dashboard` 인가된 사용자만(로그인한) 접근할 수 있다. 
- GET `/admin ` 인가된 사용자중에서 `ADMIN` 권한을 가진 사용자만 접근할 수 있다.



**구현**

요구사항에 맞는 Controller단 구현

```java
@GetMapping("/")
public String index(Model model, Principal principal){
  if(principal == null){
    model.addAttribute("message", "Hello Spring Security");
  }else {
    model.addAttribute("message", "Hello Spring Security" + principal.getName());
  }
  return "index";
}

@GetMapping("/info")
public String info(Model model){
  model.addAttribute("message", "Hello Info");
  return "info";
}

@GetMapping("/dashboard")
public String dashboard(Model model, Principal principal){
  model.addAttribute("message", "Hello Dashboard" + principal.getName());
  return "dashboard";
}

@GetMapping("/admin")
public String admin(Model model, Principal principal){
  model.addAttribute("message", "Hello Admin" + principal.getName());
  return "admin";
}
```

해당 url로 들어왔을때 return은 해당 페이지로 리턴하고, Principal 정보를 파라미터로 넘겨서 User의 정보를 확인할 수 있다. 실제 index, info, dashboard, admin은 모두다 html, thymleaf엔진을 사용해서 컨트롤러단에서 넘겨준 `message`만을 받는 단순한 구성으로 각 페이지에 대한 설명은 생략한다. 애플리케이션을 실행해 보면 `/` , `/info` 요청을 제외한 나머지 요청들은 Spring에서 제공하는 에러페이지가 나온다. 

왜 나올까? 생각해보면 Principal에 대한 정보가 설정된게 없기 때문에 `principal.getName()`를 호출할 때 NullPointerException이 발생한다. 



## **Spring Security 기본 적용** 

SpringBoot 프로젝트인 경우에 다음과 같은 starter-security 의존성을 추가 한다. 

```java
<dependency>   
	<groupId>org.springframework.boot</groupId>   
	<artifactId>spring-boot-starter-security</artifactId>   
</dependency>
```



```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .mvcMatchers("/", "/info").permitAll()
                .mvcMatchers("/admin").hasRole("ADMIN")
                .anyRequest().authenticated()
                .and()
                .formLogin()
                .and()
                .httpBasic();
    }

```

WebSecurityConfigurereAdapter를 상속받아 인자가 http인 configure 메서드를 구현합니다. 내용은 요구사항에 맞게 mvcMatchers에 ant 패턴 형태로 url패턴을 매칭시키고, Role과 권한을 부여한다. 애플리케이션을 실행하게 되면 콘솔창에 다음과 같은 password가 나온다. 

![](https://user-images.githubusercontent.com/28615416/64464783-30e7f500-d144-11e9-8e11-8a65c82711b6.png)

`localhost:8080/dashboard`  를 호출하게 되면 다음과 같이 Spring Security가 제공해 주는 기본 로그인폼이 나온다.

<img src="https://user-images.githubusercontent.com/28615416/64464842-7d333500-d144-11e9-8bd6-ffdfc3bf6a39.png" style="zoom:40%;" />

기본 default Username은 `user` 이고  패스워드는 위에 콘솔에 출력된 결과물이다. 

조금 더 자세하게 알고 싶다면 **<u>UserDetailSErviceAutoConfiguration</u>** 클래스에서 기본 설정들이 어떻게 만들어 지는 지 확인 하면 된다. 



**그렇다면 지금 상황에서 문제점은 무엇일까?** 

-   user가 한 명으로 고정되어 있다. 
-   비밀번호가 애플리케이션을 실행할 때마다 console창에 변경되어 나타난다.
-   권한에 대한 설정이 없다.(Admin)
  



## InMemory User관리

SecurityConfig에서 메서드 인자로 AuthenticationBuilder를 넘겨주는 configure메서드를 구현하자. 

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
  auth.inMemoryAuthentication().withUser("andrew").password("{noop}123").roles("USER")
    .and().withUser("admin").password("{noop}456").roles("ADMIN");
}
```

위와 같이 inMemoryAuthentication형태로 메서드 체이닝을 통해서 user, password, role들을 설정할 수 있다. 애플리케이션을 실행하고 `/dashboard`  andrew/123 로그인 성공  `/admin`  admin/456 로그인 성공

>  참고로 password를 PasswordEncoding 형식을 지정하지 않는 `{noop}`이라는 prefix를 붙여야 한다는 것. 그렇지 않으면 다음과 같은 메세지를 만나게 된다. PasswordEncoder와 일치하는 id값이 null이기 때문에 `IllegalArgumentException`  이 발생했다.
>
> ![](https://user-images.githubusercontent.com/28615416/64465202-3e05e380-d146-11e9-965f-17d33b960d61.png)



하지만 여전히 문제는 user에 대한 정보를 Source 코드 상에 하드코딩했다는 점과 모든 유저들을 저런식으로 등록해서 사용할 수 없다. 



## DB를 이용한 User관리

휘발성인 Inmemory가 아닌, DB를 이용한다. (여기서는 Spring Data JPA, H2를 이용한다)

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <scope>runtime</scope>
</dependency>
```

Account, AccountRepository, AccountService를 구현한다. 

```java
@Entity
public class Account {

    @Id @GeneratedValue
    private Long id;

    @Column(unique = true)
    private String username;
    private String password;
    private String role;
		
  //... getter setter
}
```

```java
@Service
public class AccountService implements UserDetailsService {

    @Autowired
    private AccountRepository accountRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Account account = accountRepository.findByUsername(username);
        if(account == null){
            throw new UsernameNotFoundException(username);
        }
        return User.builder()
                .username(account.getUsername())
                .password(account.getPassword())
                .roles(account.getRole())
                .build();
    }
```

AccountService는 SrpingSecurity에 있는 `UserDetailsService` 인터페이스를 구현한다. 다음과 같이 loadUserByUserName()이라는 메서드를 구현해준다. 여기서는 spring-data-jpa를 사용했지만(`accountRepository`) 다른 DAO 형태로 구현도 가능하다. 문제는 우리가 만든 Account객체를 —> UserDetails 라는 객체로 Casting해서 리턴해야 한다. 이를 편하게 하기 위해서 Spring Security에서 User클래스의 빌더를 제공해준다. 만약에 이런 클래스가 없다면? Adapter패턴을 사용해서 구현한다. 



User를 저장하는 API를 만든다. 

```java
@RestController
public class AccountController {

    @Autowired
    private AccountRepository accountRepository;

    @GetMapping("/account/{role}/{username}/{password}")
    public Account createAccount(@ModelAttribute Account account){
        account.setPassword("{noop}"+ account.getPassword());
        return accountRepository.save(account);
    }
}
```

편의상 Get메서드로 URL정보에서 받은 정보를 바탕으로 account만들지만, 원래는 Post메서드와 @RequestBody 어노테이션을 통해서 해야한다. 또한, Security Config 정보에서 `/account/**`  다음과 같은 url패턴을 허용해 주도록 추가해주고 기존에 AuthenticationBuilder를 통해서 만든 InMemoryAuthentication 정보는 삭제한다. 



## PasswordEncoder 사용하기

현재 까지 문제는 `{noop}`의 하드코딩, 적절한 PasswordEncorder가 없다. 

```java
		@Bean
    public PasswordEncoder passwordEncoder(){
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }
```

PasswordEncoder를 빈으로 등록하고, passwordEncoder의 encode()메서드를 통해서 password를 인코딩한다. `createDelegatingPasswordEncoder()`메서드 기본은 Bcrypt 인코딩을 사용한다. 다른 알고리즘을 바꿔서 적용가능하다.

```java
    @Autowired
    private PasswordEncoder passwordEncoder;

    @GetMapping("/account/{role}/{username}/{password}")
    public Account createAccount(@ModelAttribute Account account){
        account.setPassword(passwordEncoder.encode(account.getPassword()));
        return accountRepository.save(account);
    }
```

해당 결과를 보면, {bcrypt} Prefix가 붙어있고, 123이라는 비밀번호는 encoding된 문자열로 바뀌어 있다. 

![](https://user-images.githubusercontent.com/28615416/64466816-e61faa80-d14e-11e9-9a12-f9d6df683e8f.png)



