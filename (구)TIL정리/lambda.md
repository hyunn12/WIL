# 람다식



### 람다식

Java 8 부터 도입된 람다대수

람다대수에서 람다식은 수학의 함수를 단순하게 표현하는 방법



```java
//수학식
f(x, y) = x + y
//람다식 - 함수 이름빼고 간소하게 표현
(x, y) -> x + y
```



#### 수학에서의 람다식

이름없는 함수 의미 

```java
((x, y) -> x + y)(2, 3)
= 2 + 3
= 5
```

 



### 자바의 람다식

람다 = 람다식, 이름없는 함수 = 람다함수

자바에서의 함수 = 메소드 -> 반드시 클래스의 멤버로만 존재



```java
(		)	->	{	/*함수코드*/	}
매개변수리스트		애로우토큰		함수바디
```



#### 매개변수 리스트

함수에 전달되는 매개변수 (parameter) 나열

매개변수가 하나면 괄호 생략 가능



#### 애로우토큰 ( -> )

매개변수 리스트와 함수코드 분리

매개변수를 전달하여 함수바디에 작성된 코드를 실행시킨다 라는 의미



#### 함수 바디

함수의 코드

보통 중괄호 { } 로 감싸지만 한 문장의 경우엔 생략 가능

한 문장이어도 return문이 있으면 중괄호 필수

중괄호 내의 코드는 그냥 자바코드이므로 ; 세미콜론 필수







### 람다식 예시

#### 매개변수로 나이를 받아 출력

```java
(int age) -> { System.out.println("나이는 " + age); }
```



매개변수 타입 생략 가능

```java
(age) -> { System.out.println("나이는 " + age); }
```



매개변수 하나인경우 괄호 생략, 함수 바디가 한 문장인 경우 중괄호 생략 가능

```java
age -> System.out.println("나이는 " + age);
```



#### 매개변수로 두 정수를 받아 합을 출력

```java
(int x, int y) -> { System.out.println(x + y); }

(x, y) -> System.out.println(x + y);
```



#### return문이 있는 자바식

```java
(x, y) -> { return x + y; }  //합을 리턴하는 람다식

//Error code
(x, y) -> return x + y;
```

함수바디에서 코드가 한 줄이어도 return문일 경우엔 { } 중괄호를 생략할 수 없어서 두번째 코드는 에러코드



```java
(x, y) -> x + y;
```

이런 간단한 코드는 return 생략가능 -> 위와같이 간단하게도 표현 가능함







### 람다식 작성하고 호출

람다식은 함수형 인터페이스를 구현한 객체



#### 1. 함수형 인터페이스 작성

**함수형 인터페이스 (functional interface)**

= 추상 메소드 하나만 있는 인터페이스



```java
//추상메소드 calc() 하나만 가지는 함수형 인터페이스 MyFunction 작성

interface MyFunction { //함수형인터페이스
    
    int calc(int x, int y); //추상메소드

}
```



#### 2. 함수형 인터페이스의 추상 메소드를 람다식으로 구현

람다식 작성 = 함수형 인터페이스의 추상메소드에 코드 작성

함수형 인터페이스를 구현한 객체(익명클래스)로 다뤄짐

```
함수형 인터페이스 변수 = 람다식;
```



calc() 메소드 => 2개의 int type 매개변수를 가지고 int 값 리턴

```java
(x, y) -> { return x + y; }
```



**람다식 사용하려면 람다식을 변수 f 에 치환**

```java
MyFunction f = (x, y) -> { return x + y; }
```



위의 람다식을 컴파일러는 아래처럼 처리함

```java
int calc(int x, int y) { 
	return x + y; 
}
```



#### 3. 람다식 호출

레퍼런스 변수 f를 이용해 람다식 호출 가능

```java
System.out.println(f.calc(1, 2));
```





### 예제

#### 예제1.  매개변수 x, y의 합과 차를 출력하는 2개의 람다식

```java
interface Myfunction {
    int calc(int x, int y);
}

public class LambdaEx1 {
    public static void main(String[] args) {
        MyFunction add = (x, y) -> {return x + y; }
        MyFunction sub = (x, y) -> x - y;
        
        System.out.println(add.calc(1, 2));  //3
        System.out.println(sub.calc(1, 2));  //-1
    }
}
```



#### 예제2. 매개변수 x의 제곱을 출력하는 람다식

```java
interface MyFunction {
	int calc(int x);
}

public class LambdaEx2 {
	public static void main(String[] args) {
        MyFUnction square = x -> x * x;
        System.out.println(square.calc(2));  //4
    }
}
```

매개변수가 하나라서 매개변수 리스트의 괄호 생략



#### 예제3. 매개변수 없는 람다식

```java
interface MyFunction {
    void print;
}

public class LambdaEx3 {
    public static void main(String[] args){
        MyFunction f = () -> {
            System.out.println("Hello");
        };
        f.print();  //Hello
        
        f = () -> System.out.println("안녕);
        f.println();  //안녕               
    }
}
```





### 메소드의 매개변수로 람다식 전달

람다식 자체를 메소드의 매개변수로 사용가능



#### 람다식을 매개변수로 전달

```java
interface MyFunction {
    int calc(int x, int y);
}

public final class LambdaEx4 {
    public static void main(String[] args) {
        printMultiply(3, 4, (x, y) -> x * y);
    }
    //람다식 (x, y) -> x * y 을 매개변수로 전달
	
    static void printMultiply(int x, int y, MyFunction f) {
        //f로 (x, y) -> x * y 람다식 전달받음
        System.out.println(f.calc(x, y));
    }
}
```



#### @FunctionalInterface

함수형 인터페이스 앞에 @FunctionalInterface 라는 Annotation을 사용하여 컴파일러에게 함수형 인터페이스라는 것을 직접적으로 알려줄 수 있음

```java
@FunctionalInterface
interface MyFunction {
	int calc(int x, int y);
}
```

인터페이스가 추상메소드 하나만 있는 함수형 인터페이스라고 고정되어 잘못작성하는 것을 막아줌

```java
//컴파일 오류 발생
@FunctionalInterface
interface MyFunction {
	int calc(int x, int y);
	void print();
}
```



### 제네릭 함수형 인터페이스

함수형 인터페이스를 제네릭으로 만들면 다양한 타입을 처리할 수 있는 람다식을 만들 수 있음



#### 매개변수로 주어진 객체를 문자열로 출력하는 람다식

```java
@FunctionalInterface
interface MyFunction<T> {  //제네릭 타입 T를 가진 함수형 인터페이스
	void print(T x);  //추상메소드
}

public class LambdaEx5 {
    public static void main(String[] args) {
        MyFunction<String> f1 = (x) -> System.out.println(x.toString());
        f1.print("ABC");  //String 객체를 람다식에 전달
        
        MyFunction<Integer> f2 = (x) -> System.out.println(x.toString());
        f2.print(Integer.valueOf(100));  //Integer 객체를 람다식에 전달
    }
}
```





