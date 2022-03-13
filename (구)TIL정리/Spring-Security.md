

# Spring Security





접근 이력 등을 확인할 때 기존의 model1, 2 방식에서는 쿠키만을 이용해 로그인 여부만 체크가 가능했음

\- 세션쿠키

\- 사용자 정의 쿠키



스프링 시큐리티에서는 인증과 인가 두가지 방식을 사용해 구별함

인증 - 사용자가 로그인을 함으로서 name(id)와 password를 제출

인가 - 해당 로그인의 처리 결과로 허가를 얻음



이전엔 DB 검색 구분을 줄 때 where 절로 조건을 줬다면 이제는 인증과 인가 방식으로 구별을 해줌

즉, 사용자의 정보를 가져와 권한체크에 이용하고 해당 권한에 따라 다르게 설정 가능







----







## Spring Security Setting



### Spring Security Library 추가



```
//Spring Security   (다른 Spring과 버전 차이남)
implementation group: 'org.springframework.security', name: 'spring-security-core', version: '5.5.2'
implementation group: 'org.springframework.security', name: 'spring-security-web', version: '5.5.2'
implementation group: 'org.springframework.security', name: 'spring-security-config', version: '5.5.2'
implementation group: 'org.springframework.security', name: 'spring-security-taglibs', version: '5.5.2'
```

build.gradle 에 추가

원래 같은 group 끼리는 version을 맞춰줘야하지만 spring과 spring security 는 나중에 합쳐져서 서로 버전이 다르니 주의







### Servlet 4 API Library 추가

```
// https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api
implementation group: 'javax.servlet', name: 'javax.servlet-api', version: '4.0.1'
```









### SecurityConfig 생성

Spring Security를 설정해줄 클래스 생성



해당 파일이 설정파일임을 알려주기 위해서 `@Configuration` 걸어주고

Security를 위한 설정파일임을 알려주기 위해서 `@EnableWebSecurity` 걸어준다

실행이 제대로 되는지 확인위해 `@Log4j2`도 설정해줌

```java
@Configuration  //설정파일
@EnableWebSecurity  //security용
@Log4j2
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/sample/doAll").permitAll()
                .antMatchers("/sample/doMember").access("hasRole('ROLE_MEMBER')")
                .antMatchers("/sample/doAdmin").access("hasRole('ROLE_ADMIN')");
        //access 대신 hasRole 줘도됨, 책이랑 동일하게 하기 위해 access 로 처리함

        http.formLogin();  //<form>로그인</form> 이용하겠다는 것
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().withUser("member1").password("$2a$10$cNyhfGUHvrus7Not2GgfZes4vjs55h6v5f6.v9OXcl9G477Aaelpm")
                .roles("MEMBER");//"hasRole('ROLE_MEMBER')" 여기에 있는 멤버 ROLE_(접두사)없이 사용
        //noop (option XX)
        //noop 대신 bcrypt로 암호화를 거친 암호를 넣어주면
        //백퍼 안전하지는않음!
        auth.inMemoryAuthentication().withUser("admin1").password("$2a$10$cNyhfGUHvrus7Not2GgfZes4vjs55h6v5f6.v9OXcl9G477Aaelpm")
                .roles("MEMBER","ADMIN");

    }

}
```



입력받은 password를 암호화하기 위해서 `PasswordEncoder` 메소드를 만들어 *@Bean*으로 걸어줌

***BCryptPasswordEncoder*** 를 이용해 bcrypt 방식으로 암호화



어떤 페이지가 어떤 권한과 연결되는지 설정해주기 위해서 `configure(HttpSecurity http)` 메소드 override

***authorizeRequests()*** - security 처리에 *HttpServletRequest* 이용한다고 알려주기

***antMatchers()*** - 특정 경로 지정

***access()*** - access 허용 어디에?

​	***permitAll()*** - 모든 사용자가 접근할 수 있다는 것

​	***hasRole()*** - *permitAll()* 대신 특정 권한 사용자만 접근 가능하다고 지정

​		hasRole에 부여할 권한의 이름 작성 - 보통 대문자로 *hasRole('**ROLE_####**')* 형식으로 작성

​		실질적으로 뒤의 ***####*** 가 권한명



어떤 사용자에게 어떤 권한을 부여할지 설정해주기 위해서 `configure(AuthenticationManagerBuilder auth)` 메소드 override

***withUser()*** - ID 설정, spring security에서는 id대신 name이라 표현

***password()*** - 앞의 name과 매칭되는 password 입력

***roles()*** - 앞에서 지정한 name, password를 가진 사용자에게 어떤 권한을 부여해줄지 

​	앞서 위에서 만든 권한의 이름에서 앞의 ROLE_을 제외한 나머지가 권한의 이름

​	*role("**####**")* 이런 식으로 해당 사용자에게 어떤 권한을 부여할지 작성

​	쉼표로 구분해 여러개의 권한을 나열해서 부여해줄 수도 있음







### 설정파일 로딩

만들어준 설정파일을 톰캣이 시작될 때 다른 설정파일과 함께 로딩될 수 있도록 설정



#### 1. RootConfig에 선언

```java
@Configuration
@Import({BoardRootConfig.class, SecurityConfig.class})
@EnableTransactionManagement
public class RootConfig {
    /*...*/
```



#### 2. WebConfig에 설정

```java
@Log4j2
public class WebConfig extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        //정상작동 하려면 설정파일이 로딩되어야함 = 로딩여부를 로그로 확인
        //web.xml 대신 설정이 로딩된것 확인 가능
        log.info("1-------------------------------");
        log.info("1-------------------------------");

        return new Class[]{RootConfig.class, ServletConfig.class};
    }
        /*...*/
```







### SecurityInitializer 생성

```java
public class SecurityInitializer extends AbstractSecurityWebApplicationInitializer {

}
```

`AbstractSecurityWebApplicationInitializer` 를 상속받아 웹 애플리케이션으로 들어오는 요청을 가로채 ID가 springSecurityFilterChain인 Bean에 위임시킴

스프링 시큐리티의 필터체인에 걸리게 된다는 것



즉, 얘를 만듦으로서 이제 스프링 시큐리티에서 기본제공하는 로그인폼화면으로 자동으로 연결되게 되는 것





#### 로그인 화면 확인

생성 후 톰캣 서버를 실행하면 이런 로그인 화면이 뜸

![image](https://user-images.githubusercontent.com/81224398/132787487-475e48b0-dfca-40b9-947f-16bef2c64000.png)





#### 로그 확인

톰캣 실행 중 로그를 확인하면 아래같은 로그들을 확인할 수 있음 

![image](https://user-images.githubusercontent.com/81224398/132787834-c7093ee7-0222-4ae7-b3c1-4a9fa7b5b9f9.png)

괄호 내의 숫자 15는 현재 적용되고 있는 **필터의 개수**

나중에 저 필터를 커스터마이징 할 수도 있음







### SampleController 생성

```java
@Log4j2
@Controller
@RequestMapping("/sample")
public class SampleController {

    @GetMapping("/doAll")
    public void doAll() {
        log.warn("doAll....................");
    }

    @GetMapping("/doMember")
    public void doMember() {
        log.warn("doMember....................");
    }

    @GetMapping("/doAdmin")
    public void doAdmin() {
        log.warn("doAdmin....................");
    }

}
```



스프링 시큐리티 작동 확인 위해 *GetMapping으로만* 이뤄진 sample controller 클래스를 생성해줌







### Controller 연결



#### SpringSecurityConfig 생성

```java
@Configuration
@ComponentScan(basePackages = "org.zerock.jex01.security.controller")
public class SecurityServletConfig {

}
```

컨트롤러와 servlet 연결할 클래스 생성





#### WebConfig 수정

WebConfig에서 직접 Classes에 어떤 파일과 연결되는지 지정

```java
@Log4j2
public class WebConfig extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getServletConfigClasses() {

        log.info("2-------------------------------");
        log.info("2-------------------------------");

        return new Class[]{ServletConfig.class, SecurityServletConfig.class};
    }
    /*...*/
```





이제 controller 연결되어서 앞서 SecurityConfig에서 설정해준 권한을 가진 회원으로 로그인하면 해당 권한 에 연결된 jsp 페이지로 자동 리다이렉션되는 것을 확인할 수 있음











----









## Bcrypt 암호화



메세지의 내용을 직접적으로 확인하는 것이 아니라 bcrypt 해시함수를 이용한 알고리즘을 통해 복호화할 수 없도록 하는 암호화방식

단방향으로만 작용해 복호화를 할 수 없어서 상대적으로 안전함

물론 완벽하게 안전하다고 볼 순 없음









## Encode - Bcrypt 암호화 적용





#### SecurityConfig 의 PasswordEncoder 사용할 것

```java
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
    /*...*/
```







### PasswordTests 생성

### `testEncode()` 메소드 추가

```java
@Log4j2
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = {SecurityConfig.class, RootConfig.class})
public class PasswordTests {

    @Autowired
    PasswordEncoder passwordEncoder;

    @Test
    public void testEncode() {
        //인코딩 먼저
        String str = "member1";
        String enStr = passwordEncoder.encode(str);

        log.info(enStr);
    }

}
```

암호화를 진행할 testEncode 메소드 생성

*@Autowired* 를 이용해 **SecurityConfig**에 걸어준 암호화 메소드를 불러올 것이라고 지칭



*passwordEncoder.encode* 메소드를 이용해 *passwordEncoder()* 에 걸어준 Bcrypt 방식의 암호화를 이용해 입력한 단어를 암호화해줌





#### 결과 로그 확인

해시함수를 이용해 암호화 되어 실행할 때마다 코드가 바뀜

![image](https://user-images.githubusercontent.com/81224398/132797034-fcc80520-aa44-4c14-8562-680ff5b6b25f.png)









### `testDecode()` 추가

```java
@Test
public void testDecode() {
    String str = "$2a$10$cNyhfGUHvrus7Not2GgfZes4vjs55h6v5f6.v9OXcl9G477Aaelpm";
    boolean match = passwordEncoder.matches("member1", str);
    //입력한 암호가 일치하는지
    log.warn(match);
}
```

암호를 복호화하는 것이 아닌 인코딩된 암호화 입력받은 암호가 일치하는지 확인

여기서는 암호를 실제로 입력받는 것까지 연결이 안되서 그냥 직접 입력해줌





#### 결과 로그 확인

![image](https://user-images.githubusercontent.com/81224398/132797351-0a02df78-a006-424e-b6d8-e4462a4b3802.png)

실제 입력받은 암호와 인코딩 전의 암호가 일치하면 true 가 뜸







### SecurityConfig 수정

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.inMemoryAuthentication().withUser("member1").password("$2a$10$cNyhfGUHvrus7Not2GgfZes4vjs55h6v5f6.v9OXcl9G477Aaelpm")
            .roles("MEMBER");//"hasRole('ROLE_MEMBER')" 여기에 있는 멤버 ROLE_(접두사)없이 사용
    //noop (option XX)
    //noop 대신 bcrypt로 암호화를 거친 암호를 넣어주면
    //백퍼 안전하지는않음!
    auth.inMemoryAuthentication().withUser("admin1").password("$2a$10$cNyhfGUHvrus7Not2GgfZes4vjs55h6v5f6.v9OXcl9G477Aaelpm")
            .roles("MEMBER","ADMIN");

}
```

이제 코드 상에서는 사용자의 실제 암호를 알 수 없고 암호화를 거친 암호만 알 수 있음

해당 암호는 해시함수를 거쳐 복호화 할 수 없어서 암호를 해석하는 것도 불가능함

(물론 백퍼 안전하지는 않음)

똑같이 로그인을 거쳤을 때 인코딩 전 암호를 입력하면 문제없이 로그인 되는 것 확인 가능



























