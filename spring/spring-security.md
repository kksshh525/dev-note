
# Spring Security Example

> 인프런 강의 요약정리



## 1. Spring Security 폼인증



**요구사항**

- Spring MVC를 이용해서 구현하라
- GET `/` 인증된 사용자, 인증되지 않은 사용자 모두 접근할 수 있다. 
- GET `/info` 누구나 접근할 수 있다. 
- GET `/dashboard` 인가된 사용자만(로그인한) 접근할 수 있다. 
- GET `/admin ` 인가된 사용자중에서 `ADMIN` 권한을 가진 사용자만 접근할 수 있다.



**구현**

요구사항에 맞는 Controller단 구현

```java
@Controller
public class SampleController {

    @GetMapping("/")
    public String index(Model model, Principal principal){
        if(principal == null){
            model.addAttribute("message", "Hello Spring Security");
        }else {
            model.addAttribute("message", "Hello Spring Security: " + principal.getName());
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
        model.addAttribute("message", "Hello Dashboard: " + principal.getName());
        return "dashboard";
    }

    @GetMapping("/admin")
    public String admin(Model model, Principal principal){
        model.addAttribute("message", "Hello Admin: " + principal.getName());
        return "admin";
    }

}
```

해당 url로 들어왔을때 return은 해당 페이지로 리턴하고, Principal 정보를 파라미터로 넘겨서 User의 정보를 확인할 수 있다. 실제 index, info, dashboard, admin은 모두다 html, thymleaf엔진을 사용해서 컨트롤러단에서 넘겨준 `message`만을 받는 단순한 구성으로 각 페이지에 대한 설명은 생략한다. 애플리케이션을 실행해 보면 `/` , `/info` 요청을 제외한 나머지 요청들은 Spring에서 제공하는 에러페이지가 나온다. 

왜 나올까? 생각해보면 Principal에 대한 정보가 설정된게 없기 때문에 `principal.getName()`를 호출할 때 NullPointerException이 발생한다. 



### **Spring Security 기본 적용** 

SpringBoot 프로젝트인 경우에 `spring-boot-starter-security` 의존성을 추가 한다. WebSecurityConfigurereAdapter를 상속받아 인자가 http인 configure 메서드를 구현합니다. 내용은 요구사항에 맞게 mvcMatchers에 ant 패턴 형태로 url패턴을 매칭시키고, Role과 권한을 부여한다.

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

 애플리케이션을 실행하게 되면 콘솔창에 다음과 같은 password가 나온다. 

![](https://user-images.githubusercontent.com/28615416/64464783-30e7f500-d144-11e9-8e11-8a65c82711b6.png)

`localhost:8080/dashboard`  를 호출하게 되면 다음과 같이 Spring Security가 제공해 주는 기본 로그인폼이 나온다.

<img src="https://user-images.githubusercontent.com/28615416/64464842-7d333500-d144-11e9-8bd6-ffdfc3bf6a39.png" style="zoom:40%;" />

기본 default Username은 `user` 이고  패스워드는 위에 콘솔에 출력된 결과물이다. 조금 더 자세하게 알고 싶다면 **<u>UserDetailSErviceAutoConfiguration</u>** 클래스에서 기본 설정들이 어떻게 만들어 지는 지 확인 하면 된다. 



**그렇다면 지금 상황에서 문제점은 무엇일까?** 

-   user가 한 명으로 고정되어 있다. 
-   비밀번호가 애플리케이션을 실행할 때마다 console창에 변경되어 나타난다.
-   권한에 대한 설정이 없다.(Admin)
  



### InMemory User관리

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



하지만 여전히 문제는 user에 대한 정보를 Source 코드 상에 하드코딩 했다는 점과 모든 유저들을 저런식으로 등록해서 사용할 수 없다. 



### DB를 이용한 User관리

휘발성인 Inmemory가 아닌, DB를 이용한다. (여기서는 `spring-boot-starter-data-jpa`, `H2`를 이용한다)

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



### PasswordEncoder 사용하기

현재 까지 문제는 `{noop}`의 하드코딩, 적절한 PasswordEncorder가 없다. 

```java
@Bean
PasswordEncoder passwordEncoder(){
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



### MockUser 테스트 케이스 

`spring-security-test` 의존성 추가

```java
@SpringBootTest
@RunWith(SpringRunner.class)
@AutoConfigureMockMvc
public class AccountControllerTest {

    @Autowired
    MockMvc mockMvc;
```

`@AutoConfigureMockMvc` 를 선언하면, `MockMvc`를 주입받아서 사용할 수 있다. 

```java
	@WithMockUser(username = "andrewMock", password="123", roles = "USER")
    public void index_mockUser() throws Exception {
        mockMvc.perform(get("/"))
                .andDo(print())
                .andExpect(status().isOk());
    }
```

`@WithMockUser`를 통해서 가짜 객체를 만들어서, 가짜 User가 있는 상태라면(실제 DB에 있는 데이터가 아니다) 어떻게 응답을 보여주는지 확인할 수 있다. 



### FormLogin 테스트

```java
	@Test
    @Transactional
    public void form_login_success_test() throws Exception {
        Account account = createAccount();
        mockMvc.perform(formLogin().user(account.getUsername()).password("123"))
                .andExpect(authenticated());
    }

	private Account createAccount() {
        Account account = new Account();
        account.setUsername("andrew");
        account.setPassword("123");
        account.setRole("USER");

        return accountService.createAccount(account);
    }
```

`formLogin()`를 통해서 실제 user, password 정보를 넣는다. 중요한 것은 기존의 accountService의 createAccount()메서드를 통해서 Account객체를 저장할때 Password를 Bcrypt Encorder를 통해서 인코딩 했기 때문에 결과값을 비교할 때, 저렇게 하드코딩으로 "123" 값을 넣어서 비교했다.











## 2. Spring Security 아키텍쳐



![](https://user-images.githubusercontent.com/28615416/64468077-fb9ad180-d15a-11e9-85b3-549cc37a6e65.png)

빨간색 부분만 살펴보면SecurityContextHolder안에 SecurityContext가 있고, 그 안에 Authentication 객체가 있다. Authentication 객체는 Principal과 GrantAuthority정보가 있다. SecurityContextHolder는 SecurityContext를 제공해주는 놈이고, ThreadLocal이다. 이 말인 즉슨, 인증이된 사용자는 같은 쓰레드내에서 죽기 전까지는 인증됨을 유지한다. 



Authentication의 Principal이라는 객체는 이전 코드에서 UserDetailsService 인터페이스를 구현했을 당시에, 리턴문에서 Spring Security가 제공하는 User클래스를 만들어서 리턴했던 정보가 바로 Principal (신원정보) 이다. 

```java
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

