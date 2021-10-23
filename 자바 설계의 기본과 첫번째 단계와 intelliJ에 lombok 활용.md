# 자바 설계의 기본과 첫번째 단계와 intelliJ에 lombok 활용
오늘 배운 내용: 자바로 프로그램을 코딩함에 있어서 해야하는 초반부분 설계에 대해서 배웠다. 또한 설계에 있어서 lombok의 어노테이션을 활용했다. 하지만 맛만 본 수준이기 때문에 조금더 많은 공부가 필요하다.





## 데이터 위주 클래스와 로직(기능) 위주의 클래스
- 자바를 설계함에 있어서 먼저 내가 무엇을 하고자 하는 것인지를 명확하게 알아야 한다. 즉, 뚜렷한 목표 설정이 필수이다.
- 하고자 하는 목표가 뚜렷해졌으면 그 목표에서 명사를 먼저 찾아 정의해야한다. 
    - 예를들어 먹고싶은 요리를 선택하면 그 요리와 관련된 식당 이름들의 리스트를 출력하는 프로그램을 만든다고 가정했을때 명사는 '음식메뉴','수량' 이 될 수 있다.
- 그 다음 '식당'이라는 데이터 위주의 클래스를 만든다
    - 그리고 저 명사들이 식당이라는 클래스의 멤버 변수가 되는 것이다.
- 그 다음 로직 위주의 클래스를 만든다.
    - 로직 위주의 클래스는 기능들을 정의해놓은 클래스이기때문에 변수가 아닌 메서드를 먼저 선언해준다.
    - 그 다음 특정 정보를 저장할 수 있게 ArrayList와 생성자를 사용해 식당 정보 식당 클래스로부터 받아와 저정한다.
    - 그다음 정의헤놓은 메서드를 활용하여 원하는 작업을 수행한다.

## lombok - @Data
- lombok에 @Data는 자주 사용하는 어노테이션이다.
- @getter @setter @tostring @RequireArgsConstructor @EqualsAndHashCode들을 한꺼번에 설정해주는 매우 유용한 어노테이션이라고 한다.
- 즉 모든 필드를 대상으로 접근자와 설정자가 자동으로 생성되고 final 그리고 tostring, equals, hashCode 메소드가 자동으로 만들어 진다.

## lombok - @NoArgsConstructor
- 이 어노테이션은 파라미터가 없는 기본 생성자를 생성해주는 어노테이션이다.

## lombok - @AllArgsConstructor
- 이 어노테이션은 모든 필드값을 파라미터로 받는 생성자를 만들어준다.

## lombok - @Builder
- 객체 생성 방법과 표현 방법을 분리하는 것을 의미한다.
- @Builder를 사용하니 많은 양의 코딩이 줄어들었고 가독성이 올라가고 유지보수가 편리해진다.

```java
StoreDTO storeDTO = StoreDTO.builder()
                .name("식당")
                .lat(37.12334)
                .lng(127.23332)
                .menu("비빔밥")
                .build();
```







----

----

# 자바 프로그램 코딩 초반 설계 기초2

- 생성자가 너무 많다 builder 사용

- 객체지향 패러다임 => 시작은 객체 즉 문제를 먼저 해결하는게 아니라 객체를 먼저 바라본다.
  ex 증권가의 분석가가 기업을 분석할때 다른 문제를 보는게 아니라 재무재표라는 객체를 보는것처럼

- 멤버변수는 다른 객체마다 다 다른값을 갖게금하려고 하는 것이다. 그래서 초기화를 하지 않고 선언만 하는 것이다.

```java
// 상황에 맞춰서 Scanner 를 전역변수로 선언하거나 지역변수로 선언할 수 있다.
// 예를들어 딱 이 메서드에서만 사용한다고 하면 메서드 생성과 죽음을 같이 하게 메서드 안에다가 Scanner를 선언하면 된다.

// 하지만 그것이 아니라 다른 곳에서도 광범위 하게 사용된다면 여러개의 값을 받아야하기 때문에 멤버 변수를 초기화 해주는
// 것보다 선언만 해주고 각각 사용되는 곳에서 따로 따로 초기화 해주는 것이 좋다고 한다.
```

- nullpointException 은 호출했을때 가리키는 값이 없을때 즉 . 에서만 발생. 에러뜨면 .(점) 만 찾아라 ex) this.scanner.(이 점)nextLine()

- 생성자: "나 이런게 있어야 객체가 될 수 있어!!" 나 이런게 필요해!! 이런다.

- However,
  절차지향 이란? -> 라면 봉지 뒤에 라면 끓이는 법 그것이 절차 지향. 물을 끓여라 그다음 면을 넣어라 그다음 스푸를 넣어라 이렇게

- 깨진창문 이론. 프로그램을 짤때는 규칙을 코딩에 엄격하게 적용하는게 좋다. 하나라도 예외가 생기면 계속 예외가 생기고 체제와 규칙이 결국 무너지게 된다.

* 자바는 클래스 기반으로 많이 바뀌고 있다고 한다.
* 실제 데이터, 실체, 실제 객체 => 인스턴스
  * 인스턴스라는 용어는 클래스를 사용하는 언어에서만 사용한다. ★★★
  * 반대로 객체라는 용어는 상당히 광범위한 단어이다.
  * 메모리를 차지하는 모든것을 객체라고 한다.

## 자바

* 클래스 기반(base)
  * 데이터 위주의 클래스
    * 시작 => 구조체(여러개의 데이터가 흩어지지 않게 뭉쳐 놓는것, 데이터를 한 뭉탱이로 보고 싶다.)
    * 식별자: 구분하기 위한 식별 속성
    * 명사 / 액션(이것은 나중에)  -----> 용어 (주문, 회원, 상품)
    * 명사 - 클래스명
    * 멤버 변수 - 구조 -> 역시 명사들
    * 생성자 - 필수 데이터(제약 조건)
  * 로직(기능) 위주의 클래서
    * 기능들을 묶고 -> 상태 -> 돼지 저금통 계속 상태 유지(?)
    * 메서드부터 설계
    * 역할에 따라서 구분(3tier) ★★★★★★ -> 3 Tier Architecture(3계층 구조) 검색
    * 프레젠테이션
    * 비지니스
    * 영속
    * 로직 위주의 클래스에서 생성자의 의미는 준비!!

* 프로토타입 기반(base)

* 기타 등등(base)

<hr>


의존성 주입

<hr>


## TDD

Test Driven Development 테스트 주도 개발

test폴더에 Java 와 같은 페키지를 만들고 ServiceTests java소스를 마듬

메서드 test1 선언하고 위에다가 @Test 붙이기

```
Assertions.assertEquals(1,1);
```

이거 그냥 메소드 선언하고 ex test1 test2 test3 물론 위에다가 @Test 꼭 붙이고

이렇게 할때 각각의 테스트 메소드가 main메서드라고 생각하면 된다.

메서드별로 따로따로 실행이 가능하다. 메서드 옆에 실행버튼 누르면 된다.



## 람다식

▼ 밑에 것은 StoreDTO[] 안에 한 가게의 메뉴가 메뉴가 딱 하나일때만 사용 가능 즉 현실 불가능

```java
Optional<StoreDTO> result=
    Arrays.stream(this.storeDTOS).filter(storeDTO -> storeDTO.getMenu().equals(menuName)).findFirst();
```

▼ 밑에 것은 StoreDTO[] 안에 한 가게의 메뉴가 여러게 있을때 그 가게 메뉴들중 입력값이 하나라도 포함하면 그 가게 출력

```
Optional<StoreDTO> result=
    Arrays.stream(this.storeDTOS).filter(storeDTO -> storeDTO.getMenu().indexOf(menuName) >=0 ).findFirst();
```



## 객체지향의 5대 원칙 -> OOP의 5대 원칙 - which is the recommendation for coding

S O L I D 

Singgle responsibility prinicple 단일 책임의 원칙- 한 클래스는 하나의 책임만 가져야 한다.

Open/closed principle

   /

# 자바 코딩 설계 순서 - in detail

## 문제 출제 파트

quizDTO 클래스 만들기(클래스명 은 대표적인 명사)

- 멤버 변수 선언(명사들)

- lombok 사용 

  - @Getter ==> 내용 못바꾸고 가져만 갈 수 있게 getter만 해준다.
  - @AllArgsConstructor
  - @ToString

  

QuizService 클래스 만들기

- makeQuiz(); 메서드 만들기

- makeQuiz();메서드 안에다가  
  - int num1 = (int)(Math.random()*100)+1;
  - int num2 = (int)(Math.random()*100)+1;
  - QuizDTO quizDTO = new QuizDTO(num1, num2);
  - return quizDTO

- ★★★ OR 좀더 간단하게!!!  마우스 오른쪽 버튼 Extract method 사용해서 위에 랜덤 extract하기 ★★★

- 그 다음

- ```
  return new QuizDTO(getNum(),getNum());  //이런식으로 하면 한줄로 끝난다.
  
  private int getN1() {
          return (int)(Math.random()*100)+1;
      }
  ```

그다음 test폴더에 quiz 폴더 만들어서 QuizServiceTests 만들기.

- @test

- public void test1() {

  	QuizService service = new QuizService();
  	
  	QuizDTO quiz = service.makeQuiz();
  	
  	sysout(quiz);

  }

QuizUI 클래스 만들기

- 의존관계 먼저 걸어줄것임.
- private Scanner scanner;
- private Quizservice quizservice;
- @AllArgsConstructor -> 모든 멤버변수를 담은 생성자 생성.
- 그 다음 solve() 메서드 선언
  - 그 안에다가 ▼gogo
  - sysout("문제 풀래?") 만들어주고
  - String choice = scanner.nextLine();
  - 문제 가져와야하니까 QuizDTO quizDTO = quizService.makeQuiz(); 해주고
  - 이제 숫자 보여줘야하니까 sysout(quizDTO.getNum1 "+"  quizDTO.getNum2);

Main으로 이동

- QuizService service = new QuizService();
- Scanner scanner = new Scanner(System.in);
- QuizUI quizUI = new QuizUI(Scanner, service);
- quizUI.resolve();

## 제귀호출 파트

QuizUI 가서

- resolve(); 메서드에다가 ▼ gogo

  - this.resolve();

- String choice 밑에 

  - if (choice.equalsIgnoreCase("n")) {

    return;

    }

## 채점 기능 추가 파트

채점 기능은 QuizService 안에 있어야 한다고 한다.

메서드 이름은 grading(); 파라미터랑 리턴 고민하자

- 사용자 이름과 점수가 있어야함
- 누가 풀었나?

grading(); 메서드에다가 파라미터가 Quiz currentQuiz, int answer 이 두개면 채점이 가능. 사실 3개 왜냐 숫자가 2개니까(?)

리턴은 정답인지 아닌지 만 리턴하면 된다. boolean 맞다 틀리다. - String 안좋다.

QuizService 밑에 getNum 밑에

- public boolean grading(QuizDTO quizDTO, int answer) {	

  	int correctAnswer = quizDTO.getNum1() + quizDTO.getNum2();
  	return anser == correctAnswer; // 맞으면 true로 리턴, false로 리턴

  }

QuizUI로 가서

this.resolve 위에다가

- sysout("정답 입력하셈");

- int userAnswer = integer.parseInt(Scanner.nextLine());

- boolean result = quizService.grading(quizDTO, userAnswer);

- if (result) {

  sysout("정답입니다!")

  } else {

  sysout("다시 풀어 주세요!")

  }



## ★★★★★★★★이론 공부  필요★★★★★★★★

getter/setter 왜 사용하나?

java.lang.Object클래스 기능들

List / ArrayList / LinkedList / Stack 공부 요망!!!!!

Overloading / Overriding  -> 오버로딩 / 오버라이딩