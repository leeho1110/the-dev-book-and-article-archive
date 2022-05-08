# 오브젝트 PART 8 - 의존성 관리하기

### 변경과 의존성

```java
public class PeriodCondition implements DiscountCondition {
	private DayOfWeek dayOfWeek;
	private LocalTime startTime;
	private LocalTime endTime;

	...

	public boolean isSatisfiedBy(Screening screening) {
		return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
			startTime.compareTo(screening.getStartTime().toLocalTime()) <= 0 &&
			endTime.compareTo(screening.getStartTime().toLocalTime()) >= 0;
	}
}
```

위 코드의 경우 실행 시점에 ***Screening***의 인스턴스가 존재하지 않는다면 ***PeriodCondition***의 인스턴스가 정상적으로 동작하지 않는다. 이처럼 작업을 정상적으로 수행하기 위해 다른 객체를 필요로 하는 경우 우리는 두 객체 사이에 의존성이 존재한다고 말한다. 이는 자신이 의존하는 대상이 변경될 때 함께 변경될 수 있다는 뜻을  내포한다. 또한 의존성을 방향성을 가지며 항상 단방향이라는 것을 기억하자. 

---

### 의존성 전이(Transitive dependency)

위에서 언급한 의존성은 전이될 수 있다. 위에서 ***PeriodCondition***는 ***Screening***을 의존한다. 그리고 ***Screening***는 ***Movie, LocalDateTime, Customer***에 의존한다. 이때 ***PeriodCondition***는 자동적으로 앞선 3개의 객체에 간접 의존하게 된다. 이것이 바로 의존성 전이다.

의존성은 ‘*함께 변경될 수 있는 가능성*’을 의미하기 때문에 항상 전이되는 것은 아니며, 변경 여부는 변경의 방향과 캡슐화의 정도에 따라 달라진다.

---

### 런타임 의존성과 컴파일타임 의존성

객체지향 애플리케이션에서 런타임의 주인공은 객체다. 따라서 런타임 의존성이 다루는 주제는 객체 사이의 의존성이다. 반면 컴파일타임 의존성은 코드 수준의 클래스 사이의 의존성을 다룬다.

우리는 영화 예매 시스템에서 ***Movie*** 객체에게 할인 정책을 적용하는 책임을 맡겼었다. 그리고 그 할인 조건은 추상 클래스인 ***DiscountPolicy***가 담당했었다. 아래는 해당 내용을 작성한 코드다.

```java
public class Movie {
	private DiscountPolicy discountPolicy;

	public Movie (String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
		...
		this.discountPolicy = discountPolicy;
	}

	public Money calculateMovieFee(Screening screening) {
		**return fee.minus(discountPolicy.calculateDiscountAmount(screening));**
	}
}
```

코드 수준에서는 ***Movie***는 ***DiscountPolicy***를 의존하고 있다. 하지만 우리는 금액 할인 정책 ***AmountDiscountPolicy*** 과 비율 할인 정책 ***PercentDiscountPolicy*** 을 나누어 적용했었고, *calculateDiscountAmount()* 오퍼레이션은 결국 앞선 두개의 실체화된 할인 정책의 메서드를 실행시킨다. 

코드를 작성하는 시점에서는 ***DiscountPolicy***를 의존하고 있고 할인 정책이 두개나 있다는 사실조차 모른다. 하지만 실제 실행 시점에는 ***AmountDiscountPolicy***, ***PercentDiscountPolicy*** 와 협력한다. 이것이 핵심이다. 어떤 클래스의 인스턴스와 다양한 클래스의 인스턴스와 협력하기 위해선 협력할 인스턴스의 구체적인 클래스를 알아서는 안된다. 실제로 어떤 객체와 협력할지는 런타임에 결정해야 한다. 이를 통해 유연하고 재사용 가능한 설계가 완성된다.

---

### 컨텍스트 독립성

우리는 앞서 컴파일타임 의존성과 런타임 의존성이 달라야 하는 이유를 확인했다. 클래스가 특정 문맥(금액 할인, 비율 할인 중 하나)에 강하게 결합될수록 다른 문맥에서는 사용하기 어려워진다. 

자신과 협력할 객체의 구체적인 클래스를 몰라야만 강결합을 막을 수 있었다. 이렇게 클래스가 사용될 특정 문맥에 대해 최소한의 가정만으로 이뤄져 있는 것을 **컨텍스트 독립성**이라고 부른다.

---

### 의존성 해결하기

결국 우린 컴파일타임 의존성을 런타임 의존성으로 대체시켜야 한다. 우리는 이렇게 교체하는 행위를 의존성 해결이라고 부른다. 그렇다면 실행 컨텍스트에 맞게 어떻게 교체할 수 있을까. 

- *객체를 생성하는 시점에 생성자를 통해 의존성 해결*
    
    생성자에 실체화된 객체 인스턴스를 전달
    
- *객체 생성 후 setter 메서드를 통해 의존성 해결*
    
    객체를 생성한 이후 의존 대상을 설정한다. 실행 시점에 의존 대상을 변경할 수 있기에 설계를 유연하게 만들지만, 객체가 설정되기 전까지 불완전할 수 있다. 
    
- *메서드 실행 시 인자를 이용해 의존성 해결*
    
    메서드가 실행되는 동안만 일시적으로 의존 관계가 존재해도 무방하거나, 메서드가 실행될 때마다 의존 대상이 매번 달라져야 하는 경우에 유용하다.
    

---

### 의존성과 결합도, 자식이 결합을 낳는다

우리는 ***Movie***가 특정 클래스(***AmountDiscountPolicy***, ***PercentDiscountPolicy***)에 의존하는 경우는 결합도가 강하고, 추상 클래스(***DiscountPolicy***)에 의존하는 경우는 결합도가 느슨하다고 표현했다. 

즉 결합도의 정도는 한 요소가 자신이 의존하고 있는 다른 요소에 대해 얼마나 많이 알고 있는가로 결정된다.

---

### 명시적인 의존성

```java
public class Movie {
	...
	private DiscountPolicy discountPolicy;
	
	public Movie(String titlte, Duration runningTime, Money fee) {
		...
		this.discountPolicy = new AmountDiscountPolicy(...);
	}
}
```

위 예제는 인스턴수 변수를 추상 클래스인 ***DiscountPolicy*** 타입으로 선언해놓았다. ***Movie*** 객체가 과연 유연할까? 전혀 아니다. 생성자를 살펴보면 ***AmountDiscountPolicy*** 를 직접 생성하여 대입하고 있기 때문에 의존성이 생긴다. 

즉 단순히 인스턴스 변수의 타입를 추상 클래스, 인터페이스로 선언하는 것만으로는 결합도를 느슨하게 만들기 부족하다. 따라서 우린 생성자의 인수를 통해 인스턴스 변수를 전달하는 방법을 사용할 것이다.

```java
public class Movie {
	...
	private DiscountPolicy discountPolicy;
	
	public Movie(String titlte, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
		...
		this.discountPolicy = discountPolicy;
	}
}
```

생성자의 인자를 추상 클래스 타입으로 선언했다. 이젠 객체를 생성할 때 전달되는 인자에 따라 인스턴스 변수가 결정되고 의존성의 방향은 구체 클래스에서 인자로 변경됐다.

위 두 가지 방법의 차이점은 퍼블릭 인터페이스를 통해 할인 정책을 설정할 수 있는 방법을 제공하는지 여부다. 생성자의 인자로 전달받는 방법은 의존성이 외부에 노출된다. 이를 우린 **명시적인 의존성(explicit dependency)**라고 부르며, 직접 생성을 통해 의존성을 감추는 것은 **숨겨진 의존성(hidden dependency)**이라고 부른다.

숨겨진 의존성은 의존성을 파악하기 위해 내부 구현을 직접 파악해야 한다. 대규모 시스템에서 그게 과연 쉬울까? 또한 다른 컨텍스트에서 재사용하기 위해선 내부 구현을 직접 변경해야 한다. 이는 잠재적인 버그 발생 가능성을 내포시킨다.

의존성은 명시적으로 표현돼야 한다. 구현 내부에 숨겨두지 마라. 숨겨야 하는건 구현이다. 둘을 헷갈리지 마라. 

---

### new는 해롭다

결합도 측면에서 new 연산자는 아래 두가지의 이유로 해롭다

- *new 연산자를 사용하기 위해서는 구체 클래스의 이름을 직접 기술해야 한다. 따라서 new를 사용하는 클라이언트는 추상화가 아닌 구체 클래스에 의존할 수밖에 없기 때문에 결합도가 높아진다.*
- *new 연산자는 생성하려는 구체 클래스뿐만 아니라 어떤 인자를 이용해 클래스의 생성자를 호출해야 하는지도 알아야 한다. 따라서 new를 사용하면 클라이언트가 알아야 하는 지식의 양이 늘어나기 때문에 결합도가 높아진다.*

우리는 구체 클래스에 직접 의존하는 것이 결합도가 높아진다는 사실을 확인했다. 직전 예시인 직접 생성 방식을 확인해보자.

```java
public class Movie {
	...

	public Move(String title, Duration runningTime, Money fee) {
		...
		this.discountPolicy = new AmountDiscountPolicy(
														Money.wons(800),
														new SequenceCondition(1),
														new SequenceCondition(10),
														...				
	}
}
```

보이는가? ***Movie***객체에서는 단순히 인스턴스를 생성해 결합도를 높힌 것 외에도 생성자에 전달되는 인자까지 알아야 하기 때문에 훨씬 더 강한 결합도를 유발한다. 결합도가 높으면 변경에 영향을 받기 쉬워진다. 더 많이 의존할수록 더 점점 더 변경에 취약해진다. 반드시 높은 결합도를 피해야 한다.

new가 해로운 이유는 결합도를 높히기 때문이다. 단순히 구체 클래스를 결합시킬 뿐만 아니라 어떤 인자들이 필요한지도 노출시킨다. 해결방법은 역시 단순하다. 인스턴스의 생성과 사용 로직을 분리하는 것이다. 해결방법은 역시 이미 생성된 인스턴스를 인자로 전달받는 것이다.

물론 대부분의 경우에 하나의 구체 클래스에 의존하고 이외의 경우가 적다면 직접 생성하는 방식도 유용하다. 설계는 트레이드오프 활동이다. 결합도와 사용성을 적절히 확인하자.