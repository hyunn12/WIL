# Spring 정리



## Spring MVC 구조

![image](https://user-images.githubusercontent.com/81224398/131423794-00ed8e48-1ff8-4020-8a7d-561bbc9d7539.png)



### Dispatcher Servlet

FrontController방식

일종의 분배자 역할

Client에서 들어온 요청을 Handler Mapping으로 전달

후에 들어온 응답에 맞게 각각의 구조에 전달 및 요청함



### Handler Mapping

Dispatcher Servlet에서 받은 요청에 따라 적절한 Controller를 찾아서 정보를 다시 Dispatcher Servlet 으로 보냄 

보통 UrlHandlerMapping



### Controller

Dispatcher Servlet 에서 받은 요청에 따라 적절한 controller로 가게됨

***결과 data (Model)  + 뷰*** 형식으로 Dispatcher Servlet 에 전달 ( *ModelAndView* )



### View Resolver

Dispatcher Servlet에서 controller가 반환한 결과를 받아 어떤 view를 사용해야하는지 해석

해석한 결과 (어떤 view를 써야하는지) 를 다시 Dispatcher Servlet 에 전달



확장 & 커스터마이징도 가능함!



#### ex. ServletConfig - configure

```java
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        // View Resolver controller가 반환한 결과를 어떤 view를 사용해야하는지 해석
        InternalResourceViewResolver bean = new InternalResourceViewResolver();
        bean.setViewClass(JstlView.class);
        bean.setPrefix("/WEB-INF/views/");
        //여기서 경로를 미리 fix 해뒀기때문에 자동으로 뒤에 경로와 .jsp가 붙어서 기본경로에서 주소에 해당하는 jsp파일을 찾는 것임
        bean.setSuffix(".jsp");
        registry.viewResolver(bean);
    }
```





### View

Dispatcher Servlet 에서 받은 결과에 따라 맞는 JSP파일 선택 - 실행

실행 결과를 다시 Dispatcher Servlet 에 전달



최종적으로 Dispatcher Servlet 는 결과를 다시 Client에 전달함







----



## AOP 관점 지향 프로그래밍

Aspect Oriented Progrmming 



### 횡단관심사

대부분의 시스템이 공통으로 가지는 비즈니스 로직이 아니지만 반드시 처리가 필요한 부분 = **횡단관심사, 흩어진 관심사 (cross-concern)**

이러한 횡단관심사를 모듈로 분리하는 프로그래밍의 패러다임 = ***AOP***



어떤 로직을 기준으로 핵심, 부가적 관점으로 나누어보고 각각 모듈화하는 것

**모듈화** : 공통되는 로직이나 기능을 하나의 단위로 묶는 것



Spring 에서도 bean에만 AOP 적용 가능



### 기본 취지

소스코드 상에서 다른 부분에 반복해서 쓰는 코드들 = 흩어진 관심사

이 흩어진 관심사를 Aspect로 모듈화하고 핵심 비즈니스 로직에서 분리, 모듈화해서 재사용하겠다는 것



###  Aspect

흩어진 관심사를 모듈화 한 것, 주로 부가기능들 (핵심로직XXX)





----

## Bean



이전 ***servlet***을 이용한 model2 방식에서는 *enum* 클래스파일에 `INSTANCE`를 설정함으로서 ***singleton*** 구현함

***Spring*** 에서는 *Bean을* 이용해 singleton 구현 가능

기본적으로 *singleton* 방식으로 *bean*이 생성됨



### Bean 등록 방법

**@Bean, @Configuration, @Component**



#### @Bean

해당 클래스나 메소드가 bean 객체임을 명시적으로 표현

보통 초기설정이나 라이브러리에서 제공하는 메소드의 경우 사용



#### @Configuration

해당 클래스에서 1개 이상의 Bean을 생성하고 있음을 명시

*@Bean* 어노테이션을 사용하는 클래스의 경우 반드시 *@Configuration*과 함께 사용



#### @Component

직접 만든 클래스를 bean 객체로 만들고 싶을 때 사용

*@Component* 를 걸어준 클래스를 사용하려면 *@ComponentScan*으로 스캔할 패키지나 클래스를 걸어줘야함

##### 종류

- *@Controller*
- *@Repository*
- *@Service*
- *@Configuration*









## Bean Lifecycle



### @Bean(initMethod)

bean을 생성한 후 초기화하는 방법 중 하나

초기화메소드

```java
@SpringBootApplication
public class TaengPlication {

    @Bean(initMethod = "initTaengPonent")
    public TaengPonent callInitMethod() {
        return new TaengPonent();
    }
}

class TaengPonent {
    public void initTaengPonent() {
        // ...
    }
}
```

메소드의 리턴타입을 객체로 만들어줌

initMethod = "초기화에 사용할 메소드"



----



## @Autowired



### 종류

![image](https://user-images.githubusercontent.com/81224398/131271800-f3981c38-4ac7-4b09-a786-abe471106efb.png)



```
No
스프링 프레임워크의 기본값
autowiring이 꺼져 있음
빈 정의에서 <property> 태그를 사용하여 종속성을 명시적으로 설정해야함

byName
Bean 이름을 기반으로 하는 종속성 주입을 활성화
빈에서 속성을 자동 연결하는 경우 속성 이름은 구성 파일에서 일치하는 빈 정의를 검색하는 데 사용됩니다. 그러한 빈이 발견되면 속성에 주입됩니다. 그러한 bean이 발견되지 않으면 오류가 발생합니다.


byType
이 옵션은 Bean 유형을 기반으로 하는 종속성 주입을 활성화합니다. Bean에서 속성을 autowiring할 때 속성의 클래스 유형은 구성 파일에서 일치하는 Bean 정의를 검색하는 데 사용됩니다. 그러한 빈이 발견되면 속성에 주입됩니다. 그러한 bean이 발견되지 않으면 오류가 발생합니다.


constructor
생성자에 의한 자동 연결은 byType과 유사하지만 생성자 인수에 적용됩니다. autowire 사용 bean에서 생성자 인수의 클래스 유형을 찾은 다음 모든 생성자 인수에 대해 autowire bytype을 수행합니다. 컨테이너에 생성자 인수 유형의 빈이 정확히 하나만 없으면 치명적인 오류가 발생한다는 점에 유의하십시오.
```





----



## 날짜 data 보내기

보통 날짜를 파라미터로 보내면 날짜로 보내지는 것이 아니라 **문자열 형식**으로 보내짐

=>***typeMismatch!*** 매칭불가



=> how???

- 137p. ***@InitBinder***
- 138p. ***@DataTimeFormat*** >>> 편함



- *LocalDate* = 날짜만

- *LocalDataTime* = 날짜+시간

format지정!

`@DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")`

```java
    //형식 주의 특히 월 MM - 분 mm
//    @DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    //근데 이거 줘도 typeMismatch.regDate,typeMismatch.java.time.LocalDateTime 예외 발생함 왜????
    private LocalDateTime regDate;
    private LocalDateTime modDate;
```

근데 이거 해도 실행 시 예외발생,,,,

*typeMismatch.regDate,typeMismatch.java.time.LocalDateTime*



왜???

*DateTime인데 날짜만 줘서*



매번 같은 형식으로 줘야할까?

그렇게 안들어오면 어떡해?

=> 아예 다른 방식으로 접근해야함!!



**converter 이용!**



### Converter

날짜형식 다 다름 - if else 일일히 처리해야할까?

**=> 정규표현식** 이용!

or 시 분 초 필요하면 문자열 길이 길어짐 - **문자열 길이로 체크** (길면 pass 짧으면 시간 추가)

```java
@Log4j2
@Component
public class StringToLocalDateTimeConverter implements Converter<String, LocalDateTime> {
	//날짜의 형식을 변경해주는 converter
    @Override
    public LocalDateTime convert(String source) {
        log.info("---------------");
        log.info("converter: " + source);

        //pattern으로 패턴 지정
        Pattern pattern =  Pattern.compile("^((19|20)\\d\\d)?([- /.])?(0[1-9]|1[012])([- /.])?(0[1-9]|[12][0-9]|3[01])$");
        //matcher로 pattern과 source의 형식이 일치하는지 체크
        Matcher matcher = pattern.matcher(source);

        //형식이 일치하지 않는다면?
        if(matcher.find()) {
            return LocalDateTime.parse(
                	//뒤에 시간 추가
                    source + "T00:00:00", DateTimeFormatter.ISO_LOCAL_DATE_TIME);
        }
        //아니면 그냥 현재시간 출력
        return LocalDateTime.now();
    }
}
```



Customizing 하려면 ***HandlerMethodArgumentResolver*** 이용

\- 예전에는 abstract 했었음





----



## DTO

### DTO - validation







### DTO vs VO







----



## Cross-Site Scripting

### XSS ( Cross-site scripting )

${dto.bno} JSP에서 EL 사용 시

그냥 안쓰고 c:out을 사용하는 이유



내용이 스크립트를 실행하는 내용일 수도 있음

다른 사이트에 정보를 보내는 스크립팅 코드가 들어갈 수도 있기때문에!

= cross site scripting (XSS)

이걸 막으려고 c:out tag를 사용해 막아주는 것임!





----



## 링크 거는 방법 - \<a href>, JavaScript

| a tag                        | JS                                  |
| ---------------------------- | ----------------------------------- |
| 앵커태그를 이용해 직접 걸기  | JavaScript를 사용해 간접적으로 걸기 |
| JS 관계없이 실행 가능        | url 간결, 재사용가능, 처리용이      |
| 링크 url이 길어져 지저분해짐 | JS 실행안되면 처리안됨              |



### **링크를 JS처리시**

*form tag*에 *input type = hidden* 주어 파라미터 전달

페이지 이동 시 input tag 내의 값만 바뀌어 전송

목록버튼을 눌렀을 때 보던 페이지와 사이즈로 가야하기때문에



----



## SQL 문 *AND* / *OR* 우선순위

```sql
-- 방법1. A or B and C
select * from tbl_board
where title like concat('%', '9', '%') or content like concat('%', '9', '%') and bno > 0
order by bno desc
;

-- 방법2. (A or B) and C
select * from tbl_board
where (title like concat('%', '9', '%') or content like concat('%', '9', '%')) and bno > 0
order by bno desc
;
```

기본적으로 *AND*가 *OR*보다 우선순위 높음

방법 1과 방법 2에서 괄호의 유무에 따라서 연산 진행 순서가 달라짐



방법 1에서는 B and C 가 먼저 연산된 후 A와의 연산이 일어남

방법 2에서는 A or B 가 먼저 연산된 후 and C 가 연산됨

따라서 괄호 유무가 매우 중요함!



= [MyBatis의 동적쿼리](https://mybatis.org/mybatis-3/ko/dynamic-sql.html)



### MyBatis 동적쿼리

- if
- choose (when, otherwise)
- trim (where, set)
- foreach



참고 : https://developyo.tistory.com/242, https://mybatis.org/mybatis-3/ko/dynamic-sql.html



----



## Spring Framework



### 스프링 특징

- 경량 프레임워크
- **제어의 역행** Inversion of Control **(IoC)**
- **의존성주입** Dependency Injection **(DI)**
- **관점지향 프로그래밍** Aspect-Oriented programming **(AOP)**





객체지향적구조



이전에 singleton 구현

enum -> INSTANCE; new ~~ 

Spring의 Singleton 구현

그냥 만들고 annotation으로 singleton만들으라고 명령

객체지향구조 - wiring 와이어링

연관관계를 통해 문제를 해결

객체지향 패러다임 - 현실세계모델





### 의존성주입

**Dependency Injection**

코드의 내부에서 객체간 연결을 이루지않고 외부에서 설정통해 객체 연결



- #### 생성자 주입

  *@RequiredArgsConstructor* + *final* 고정 

  

- #### Setter 통한 주입

  *@Autowired*

  

- #### Interface 통한 주입







### Application Context

필요한 객체를 내가 일일이 찾는게 아니라 context가 찾아서 주입해줌

**의존성**이 필요한 객체에 필요한 객체를 찾아서 **주입**



따라서 스프링을 쓰게되면 하나의 객체에 다 밀어넣는게 아닌 객체를 분리해서 생성하고 wiring 해주는 것



스프링에선 주로 생성자와 setter를 이용한 주입 사용

설정은 xml과 annotation 이용









----



## REST API



### @RestController









----



## Exception 에러처리



### @ExceptionHandler

예외상황을 걸어줘서 해당 예외 발생 시 원하는 페이지로 리다이렉트 될 수 있도록 설정할 수 있음

`@Controller`, `@RestController`  가 걸린 Bean 내에서 발생하는 예외를 잡아 메서드에서 처리할 수 있도록 설정 가능

각 예외마다 다르게 작동하도록 설정도 가능함

***@ExceptionHandler(예외클래스.class)***

메소드의 리턴타입은 원하는대로 설정가능

아래의 예제에서는 원하는 에러 페이지로 리다이렉트를 시켜주기 위해서 String 으로 리턴타입 설정함



### @ControllerAdvice

`@ExceptionHandler` 는 각각 클래스에 따로 적용할 수 있는 예외처리

@ControllerAdvice는 모든 @Controller = 전역에서 발생할 예외를 잡아 처리해주는 annotation

ViewResolver를 통해 예외처리 페이지로 리다이렉트 시킬 수 있음



```java
package org.zerock.jex01.common.exception;

@Log4j2
@ControllerAdvice
public class CommonExceptionAdvice {

    //model - controller
    //spring bean 처리 - servletcontext.xml
    @ExceptionHandler(Exception.class)
    //exception은 상속가능 사용자 정의가능
    //어떤 페이지에서 어떤 에러가 났는지에따라 어떤 페이지로 보내줘야하는지가 달라짐 - 설계주의
    //error전용 페이지 만들어서 처리 ex - 404error
    //ex. 파라미터 덜 넣었을때 등
    public String exceptAll(Exception ex, Model model){
        log.error(ex.getClass().getName());
        log.error(ex.getMessage());

        model.addAttribute("exception", ex);

        return "error_page";
    }


    //error 상황에 맞게 해당 예외에 맞게 코드를 잡아서 예외처리페이지로 리다이렉트 시킬 수 있음
    //AOP - 비지니스 로직이 아니면 다 분리, 주요로직만 신경쓰기!

    //ArithmeticException
    @ExceptionHandler(ArithmeticException.class)
    public String exceptArithmetic(ArithmeticException ex, Model model){

        log.info("exceptArithmetic");
        log.error(ex.getClass().getName());
        log.error(ex.getMessage());

        model.addAttribute("exception", ex);

        return "error_arithmetic_page";
    }

    @ExceptionHandler(NoHandlerFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public String handle404(NoHandlerFoundException ex){
        return "custom404";
    }

    //설정해주면 파일 [/WEB-INF/views/custom404.jsp]을(를) 찾을 수 없습니다. 라는 메세지 뜸!
    //이제 그냥 404페이지가 아닌 내가 설정한 custom404 page 가 뜨게됨!

}
```







----



## Service interface와 ServiceImpl 클래스로 나눠준 이유



### Loose Coupling

느슨한 결합을 통해 의존도를 최소화하기위해서



만약 A가 B가 필요해서 의존성 주입을 하게되면 어쨌든 A는 B가 없으면 안되기때문에 B가 뭔지 어떤 타입인지에 영향을 많이 받게됨

그런데 인터페이스란 주입받고 바라보는 것이 정확히 어떤건지 불명확하게 하려고 사용하는 것

interface를 중간에 끼움으로서 A는 B가 뭔지 몰라도 인터페이스만 바라보면 B가 뭐든 받을 수 있는것이기때문에

의존성주입을 원활하게 사용하려면 인터페이스를 따로 분리하는 것이 유리하다는 것!



즉, 서로 간의 의존도를 낮춰 개별 코드들로 캡슐화하고 유지보수성을 높일 수 있음





여떻게 보면 IoC 관리



----







## **Ioc(Inversion of control)란?**

 

Ioc(제어의 역전)이란 **프로그램의 제어 흐름 구조가 뒤바뀌는 것**이다. 

 

일반적인 프로그램의 흐름은 main() 메소드와 같이 프로그램이 시작되는 지점에서 사용할 오브젝트를 결정하고, 결정한 오브젝트를 생성하고, 만들어진 오브젝트에 있는 메소드를 호출하고, 그 오브젝트 메소드 안에서 다음에 사용할 것을 결정하고 호출하는 식의 작업이 반복된다. 

 

**제어의 역전**이란 이런 제어 흐름의 개념을 거꾸로 뒤집는 것이다. 오브젝트가 자신이 사용할 오브젝트를 스스로 선택하지 않는다. 당연히 생성하지도 않는다. 또 자신도 어떻게 만들어지고 어디서 사용되는지를 알 수 없다. 모든 제어 권한을 자신이 아닌 다른 대상에게 위임하기 때문이다. 스프링에서는 application context라고하는 **IoC 컨테이너**를 통해 IoC를 관리한다. 

(**컨테이너**는 특정 객체의 생성과 관리를 담당하고, 객체 운용에 필요한 다양한 기능을 제공한다.)

 

스프링은 IoC를 모든 기능의 기초가 되는 기반기술로 삼고 있으며, IoC를 극한까지 적용하고 있는 프레임워크다. 스프링의 대표적인 IoC 동작원리로 **DI(Dependency Injection)**이 있다. DI가 무엇인지는 뒤에 설명한다.

 



![img](https://blog.kakaocdn.net/dn/bpPJ8T/btqBkM6phCe/UGhA0gUlBgxrNFjf6VqU40/img.gif)

출처:http://www.ytechie.com/2008/06/i-finally-get-the-point-of-inversion-of-control/



 

 

**프레임워크는 제어의 역전 개념이 적용된 대표적인 기술**이다. 프레임워크는 단지 미리 만들어둔 반제품이나 확장해서 사용할 수있도록 준비된 추상 라이브러리의 집합이 아니다. 라이브러리를 사용하는 애플리케이션 코드는 애플리케이션 흐름을 직접 제어한다.

 

반면에 **프레임워크는 거꾸로 애플리케이션 코드가 프레임워크에 의해 사용된다**. 보통 프레임워크 위에 개발된 클래스를 등록해두고, 프레임워크가 흐름을 주도하는 중에 개발자가 만들 애플리케이션 코드를 사용하도록 만드는 방식이다. 

 



![img](https://blog.kakaocdn.net/dn/b4Yc0W/btqBkOiRncP/iYG0G26DL9hHrQmnSy6eYK/img.png)출처:https://www.programcreek.com/2011/09/what-is-the-difference-between-a-java-library-and-a-framework/



 

 

 

## **DI(Dependency Injection) 란?**

 

DI는 **의존관계 주입**, **의존관계 설정**이라는 의미이다. 스프링 IoC 기능의 대표적인 동작원리다. DI는 오브젝트 레퍼런스를 외부로부터 제공(주입)받고 이를 통해 여타 오브젝트와 다이나믹하게 의존관계가 만들어지는 것이다.

 

 



![img](https://blog.kakaocdn.net/dn/b4AdYE/btqBlh6bI8o/kCYTKcR2n13fz56YPHyXp0/img.jpg)출처:https://www.codeproject.com/Articles/592372/Dependency-Injection-DI-vs-Inversion-of-Control-IO



####  

## ***의존관계란?***

 

의존 관계란 한쪽의 변화가 다른 쪽에 영향을 주는 것이다. 자바에서는 인터페이스를 통해 의존관계를 제한하여 변경에서 자유로워질 수 있다.

 

 

## ***ApplicationContext* 란?**

 

스프링 애플리케이션 전반에 걸쳐 모든 구성요소의 제어 작업을 담당하는 **IoC 엔진**이다.

 

IoC 방식을 따라 만들어진 일종의 빈 팩토리이다. 빈 팩토리라고 말할 때는 빈을 생성하고 관계를 설정하는 IoC의 기본 기능에 초점을 맞춘 것이고, 애플리케이션 컨텍스트는 별도의 정보를 참고해**서 빈의 생성, 관계설정 등의 제어를 총괄**한다. 

 

또한 싱글톤을 저장하고 관리하는 **싱글톤 레지스트리**이다. **스프링은 기본적으로 별다른 설정을 하지 않으면 내부에서 생성하는 빈 오브젝트를 모두 싱글톤으로 만든다**.

 

매번 클라이언트에서 요청이 올 때마다 각 로직을 담당하는 오브젝트를 새로 만들어서 사용한다고 생각해보자 요청 한 번에 5개의 오브젝트가 새로 만들어지고 초당 500개의 요청이 들어오면, 초당 2500개의 새로운 오브젝트가 생성된다. 서버가 감당하기 힘들다. 그래서 필요한 것이 싱글톤이다.

 

## **Bean Factory란?**

 

빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트이다.

 

### **Bean이란?**

 

스프링에서는 스프링이 제어권을 가지고 직접 생성하고, 의존관계를 부여하는 오브젝트를 Bean이라고 부른다. 스프링에서 빈 등록은 XML, 자바 코드(애너테이션)를 통해 할 수 있다.



