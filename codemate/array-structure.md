# 배열
순차적으로 데이터를 저장하는 구조

일반적으로 서로 연관된 데이터를 하나의 변수로 묶기 위해 사용

하나의 변수에 할당하면서 반복문 사용 시 많은 정보를 효율적으로 처리 가능

### 😈배열의 단점
`["Apple", "Banana", "Grape", "Tomato", "Peach"]`
위와 같은 배열이 존재할 때, 여기서 중간의 'Tomato'가 빠지게 된다면?

```java
String[] fruits = {"Apple", "Banana", "Grape", "Tomato", "Peach"};
System.out.println("변경 전: " + Arrays.toString(fruits));

//변경 후
fruits[3] = null;

System.out.println("변경 후: " + Arrays.toString(fruits));
```

실행 결과
```java
변경 전: [Apple, Banana, Grape, Tomato, Peach]
변경 후: [Apple, Banana, Grape, null, Peach]
```
위처럼 해당 자리에 그냥 null이 출력됨

=> 배열은 인덱스에 따라 값을 저장해 한 요소가 삭제되어도 빈 자리는 남음

그리고 배열의 길이도 그대로 유지됨

존재하지 않는 데이터를 지워버리면?

인덱스 기준으로 저장된 데이터를 일일히 앞으로 옮기고 길이도 줄여서 새로운 배열을 만들어야함

이미 다른 곳에서 기존의 인덱스값을 참조해 값을 불러오고 있었으면 에러 발생할 것

코드로도 해결이 가능하긴 하지만 매우 번거로워짐

이러한 변화가 생기는 데이터의 경우 다른 방식의 자료구조를 적용해야함!