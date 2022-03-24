Java로 웹 프로젝트 진행할 때 DB로 data 전달할 때 주로 사용하는 방법 두가지

VO를 이용하는 방법과 Map을 이용해 전달하는 방법 2가지 존재

그런데 어떤 차이가 있고 어떤 것을 이용하는게 더 나은지?



### VO (Value Object)

VO로 data를 전달하려면?

```xml
<select id="selectToVO" resultType="kr.co.test.vo.MemberVO"> 
```

```java
// VO 클래스 필요
@Data
public class MemberVO {
	int mno;
	String id;
	String pwd;

	public int getMno() {
		return mno;
	}
	
	public void setMno(int mno) {
		this.mno = mno;
	}
	...
}

// service
public void selectToVO(MemberVO memberVO){
	...
}

// controller
MemberVO memberVO = new MemberVO();
memberVO.setId("newID");
memberVO.setPwd("newPWD");

selectToVO(memberVO);
```

이렇듯 테이블에 존재하는 모든 컬럼을 변수로 선언하고 각 변수마다 getter, setter를 설정해줘야함

외부에서 getter, setter 통해 값을 가져오고 변경할 수 있음



**장점**

테이블마다 따로 클래스를 분리 → 객체지향적 설계 가능

변수에 대한 구체적 datatype설정

 → 값할당 시 타입 매치되지 않으면 컴파일에러 발생 = 나중에 큰 오류 가능성 줄어듦 

어디서 오류가 발생했는지 한번에 파악이 쉬움

비즈니스 로직 상 data가 명확해야하는 경우 VO 권장 (금액 등)

특정 값의 key나 value가 null인 경우 VO 클래스 내에 선언된 타입으로 초기화 수행

 → null로 인한 오류 발생XX (ex. int의 경우 0으로 초기화)



**단점**

Map에 비해 소스 양 많아짐

MyBatis 이용해 select해올 시 resultType 작성 주의해야함

테이블 컬럼 변동 시 변동에 따라 변수도 바꿔줘야함

일일히 Getter/Setter 작성해야함 

 ⇒ 요즘은 lombok의 @Getter/@Setter로 간편하게 사용 가능하기도함

datatype을 처음에 신경써서 설정해줘야하는 번거로움 존재

 → 처음 설정 오류 시 바로 오류 발생해서 파악이 쉽다는 장점으로 연결되기도함





** **Getter/Setter 는 왜 쓰는데?**

변수에 새로운 값을 할당할 때마다 *validation* 검사 가능

*lazy loading* 가능

read, write *권한 다르게 설정* 가능 (public, private)

getter/setter 이용하는 것은 *캡슐화*의 가장 기본 형태

외부에서 함부로 값에 접근할 수 없도록 할 수 있음

> 객체의 상태는 오직 그 객체의 동작에 의해서만 접근/변경되어야함
한 객체가 다른 객체의 필드를 변경하려는 경우, public method 통해 객체의 상태(필드 값)를 변경해야 함 
또한 외부에서 접근 가능한 public method는 최소한으로 해야함
> 







### Map

Map으로 data를 전달하려면?

```xml
<select id="selectToMap" resultType="map">
<insert id="insertFromMap" parameterType="hashMap">
```

```java
// service
public Map<int, Object> selectToMap(Map<int, Object> param) {
	...
}

// controller
Map<int, Object> param=new HashMap();
param.put("id", "newID");
param.put("pwd", "newPWD");

selectToVO(param);
```

따로 별도의 클래스를 분리할 필요 없음

data를 전달할때 type을 전부 Map으로 전달하면 됨

Map의 Generic은 `<PK의 datatype, Object>`로 설정





---

출처

[https://luceatluxvestra.tistory.com/62](https://luceatluxvestra.tistory.com/62)

[https://www.slipp.net/questions/22](https://slipp.net/questions/22)

[http://zgundam.tistory.com/65](http://zgundam.tistory.com/65)
