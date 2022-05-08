# 오브젝트 PART 6 - 메시지와 인터페이스

훌륭한 객체지향의 핵심은 클래스가 아니다. 클래스에 집착하지말고 자율적인 객체를 바라보아라. 애플리케이션은 클래스로 구성되지만 메시지를 통해 정의된다는 사실을 기억하라.

---

### 클라이언트-서버 모델

웹 애플리케이션 개발하는 개발자라면 사실 클라이언트-서버 모델은 아주 익숙할 것이다. 클라이언트는 서버에게 HTTP 메시지를 통해 작업을 요청하고 서버는 클라이언트에게 요청한 작업을 처리한 결과를 응답한다.

객체지향에서도 사실 이것과 비슷한 모델이 적용되어 있다. 객체 사이의 협력 역시 어떤 객체가 다른 객체에게 메시지를 통해 무언가를 요청하면서 시작된다. 이 때 메시지를 전송하는 객체가 클라이언트 역할, 수신하는 객체가 서버 역할을 맡는 구조인 것일뿐이다.

웹에서는 클라이언트가 HTTP 메시지를 통해 무언가를 서버에게 요청하며 협력 관계를 구성하고, 객체지향 세계에서는 메시지를 통해 다른 객체에게 협력을 요청한다. 우선은 '메시지'가 객체가 사용할 수 있는 유일한 의사소통이라는 것과 이것이 협력을 가능케 하는 기반이라는 것을 기억하며 다음 문단으로 넘어가자.

---

### 메시지와 메시지 전송

객체는 책임을 혼자 처리할 때도 있지만 다른 객체와 협력하기도 한다. 이 때 객체들은 메시지를 전송하며 의사소통한다. 이 때 전송되는 메시지의 요소들은 아래와 같은 구조를 갖는다.

```java
// Movie 객체가 DiscountCondition 객체에게 할인조건에 만족하는지를 메시지를 통해 요청한다.
- 수신자 /  오퍼레이션 / 인자
condition.isSatisfiedBy(screening);
```

위 예시에서 메시지를 받은 DiscountCondition 인스턴스에서 수행되는 함수를 메서드라고 부른다. 여기서 주의해야할 것은 우리는 DiscountCondition 에게 isSatisfiedBy() 메서드를 보냈지만, 어떤 객체로 실체화(SequenceCondition, PeriodCondition)되어있는 지에 따라 실행되는 메서드가 달라질 수 있다는 것이다.

이 경우 메시지 전송자와 수신자는 서로 자세한 세부정보 없이 단지 메시지만 믿고 가는 형식을 취한다. 이렇게 실행 시점에 메시지와 메서드를 바인딩하는 메커니즘은 두 객체 사이의 결합도를 낮추고 유연하며 확장 가능한 코드를 작성할 수 있도록 돕는다. 

---

### 퍼블릭 인터페이스와 오퍼레이션

우리는 객체가 자율적임과 동시에 다른 객체가 본인의 내부를 헤집어놓지 못하도록 내부를 감춘다고 했다. 이는 객체는 외부와 내부가 뚜렷하게 구별되어 있다는 것을 의미한다. 즉 외부의 객체는 객체가 공개하는 메시지로만 객체와 상호작용할 수 있다는 것이다. 

어떤 객체가 실제로 A라는 책임을 처리할 수 있다고 해도, 만약 해당 책임에 대한 메시지를 받지 않는다면 절대 객체에게 해당 책임을 위임할 수 없다. 이렇게 객체가 의사소통을 위해 외부에 공개하는 메시지들의 집합을 우린 **퍼블릭 인터페이스**라고 부른다. 그리고 이는 프로그래밍 관점에서는 **오퍼레이션(operation)**이라고 부른다. 흔히 오퍼레이션은 구현 코드를 제외한 메시지 관련 시그니처를 가리키는 경우가 많다. 

자 다시 프로그래밍 언어의 관점에서 위 내용을 다시 정리해보자. 객체가 다른 객체에게 메시지를 전송하면 런타임 시스템은 메시지 전송을 오퍼레이션 호출로 해석한다. 이어서 메시지를 수신한 객체의 실제 타입을 기반으로 적절한 메서드를 찾아 실행한다.

---

### 용어 정리 (본문 인용)

1. **메시지**
	객체가 다른 객체와 협력하기 위해 사용하는 의사소통 메커니즘. 일반적으로 객체의 오퍼레이션이 실행되도록 요청하는 것을 "메시지 전송"이라고 부른다. 메시지는 협력에 참여하는 전송자와 수신자 양쪽 모두를 포함하는 개념이다.

2. **오퍼레이션**
객체가 다른 객체에게 제공하는 추상적인 서비스다. 메시지가 전송자와 수신자 사이의 협력 관계를 강조하는데 비해 오퍼레이션은 메시지를 수신하는 객체의 인터페이스를 강조한다. 다시 말해서 메시지 전송자는 고려하지 않은 채 메시지 수신자의 관점만을 다룬다. 메시지 수신이란 메시지에 대응되는 객체의 오퍼레이션을 호출하는 것을 의미한다.

3. **메서드**
메시지에 응답하기 위해 실행되는 코드 블록을 메서드라고 부른다. 메서드는 오퍼레이션의 구현이다. 동일한 오퍼레이션이라고 해도 메서드는 다를 수 있다. 오퍼레이션과 메서드의 구분은 다형성의 개념과 연결된다.

4. **퍼블릭 인터페이스**
객체가 협력에 참여하기 위해 외부에서 수신할 수 있는 메시지의 묶음. 클래스의 퍼블릭 메서드들의 집합이나 메시지의 집합을 가리키는데 사용된다. 객체를 설계할 때 가장 중요한 것은 훌륭한 퍼블릭 인터페이스를 설계하는 것이다.

5. **시그니처**
시그니처는 오퍼레이션이나 메서드의 명세를 나타낸 것으로, 이름과 인지의 목록을 포함한다. 대부분의 언어는 시그니처의 일부로 반환 타입을 포함하지 않지만 반환 타입을 시그니처의 일부로 포함하는 언어도 존재한다.

---


### 퍼블릭 인터페이스의 품질

우린 앞서 객체들은 메시지들을 통해 의사소통하며 책임을 위임한다는 것을 확인했다. 퍼블릭 인터페이스는 객체가 수신할 수 있는 메시지의 집합이였고 좋은 인터페이스의 조건은 [3장-메세지가 객체를 결정한다](https://velog.io/@leeho1110/%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8-%EC%BD%94%EB%93%9C%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EB%8A%94-%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-%EC%84%A4%EA%B3%84-Part-3)에서 확인했다. 

혹시나 기억이 나지 않는 경우를 대비해 다시 얘기하자면 좋은 인터페이스는 **최소한의 인터페이스**와 **추상적인 인터페이스**라는 조건을 만족한다. 이번에는 해당 조건을 만족시키기 위한 원칙과 기법을 한번 살펴보자.

1. **디미터 법칙 (Law of Demeter)**
다음 코드는 영화 예매 시스템을 절차적으로 구현한 예제 코드중 일부이다. 
```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
        Movie movie = screening.getMovie();

        boolean discountable = false;
        for(DiscountCondition condition : movie.getDiscountConditions()) {
            if (condition.getType() == DiscountConditionType.PERIOD) {
                discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek()) &&
                        condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                        condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
            } else {
                discountable = condition.getSequence() == screening.getSequence();
            }

            if (discountable) {
                break;
            }
        }
    }
    ...
 }
```

만약 위 코드가 가진 단점은 높은 결합도였다. reserve() 메서드에서 인자로 받는 다른 객체들을 ReservationAgency가 직접 getter 메서드를 통해 필드에 접근하고 있기 때문에 각 객체들이 변경될 때 ReservationAgency 또한 변경되어야 했다.

이렇듯 협력하는 객체의 내부 구조에는 결합으로 발생하는 설계 문제가 발생한다. 따라서 이를 해결하기 위해 제안된 원칙이 바로 디미터 법칙이다. 간단하게 요약하면 객체의 내부 구조에 강하게 결합되지 않도록 협력 경로를 제한하라는 것이다. 위 예시에서 ReservationAgency는 할인 조건을 비교하기 위해 movie의 getDiscountConditions()를 통해 condition을 가져온 뒤 다시 getType()을 통해 비교한다. 뒤이어 getStartTime(), getEndTime() 등 모든 필드에 getter 필드를 통해 마구잡이로 접근한다. 

디미터 법칙은 "낯선 자에게 말하지 말라"로 요약되는데, java에서는 "오직 하나의 도트만 사용하라"라는 말로 요약된다. 하나의 도트만 사용하라는 것은 해당 객체에게 하나의 메시지만 보내 책임을 수행하라는 것이다. 아래는 디미터 법칙의 정의다.

> 모든 클래스 C와 C에 구현된 모든 메서드 M에 대해서, M이 메시지를 전송할 수 있는 모든 객체는 다음에 서술된 클래스의 인스턴스여야 한다.
> - M의 인자로 전달된 클래스 (C 자체를 포함)
> - C의 인스턴스 변수의 클래스
> 

위 예시에서는 C는 ReservationAgency, 구현된 메서드 M은 reserve()다. M이 메세지를 전송할 수 있는 객체는 C를 포함한 M의 인자로 전달된 클래스 Screening, Customer, ReservationAgency와 C의 인스턴스 변수 클래스다. 이 때 reserve()를 통해 생성된 객체나 reserve()가 호출하는 메서드에 의해 생성된 객체, 전역 변수로 선언된 객체는 M의 인자로 포함한다.
자 그렇다면 위 코드가 왜 디미터 법칙을 위반하는지 살펴보자.

- screening.get*.get\*.get\*
첫 줄의 screening.getMovie()를 보자. 위 메서드는 Movie 객체의 참조를 얻는다. 이 때 movie는 reserve()를 통해 생성된 객체가 아니. 이후 반복문에서 movie는 다시 getDiscountConditions()를 호출한다. 혹시 보이는가? 위 메서드는 사실 screening.getMovie().getDiscountConditions()와 동일하며, Movie에게 '할인 조건을 가져와라' 라고 메시지를 전송한다. Movie는 우리가 메시지를 전송할 수 있는 객체에 포함되지 않으며, 우린 명확하게 디미터 법칙을 위반한다. 

위에서 언급한 screening.getMovie().getDiscountConditions()는 보통 **기차 충돌(train wreck)** 이라고 부른다. 클래스의 내부 구현이 외부로 노출됐을 때 나타나는 전형적인 형태로 이를 통해 메시지 전송자가 메시지 수신자의 내부 정보를 자세히 알게된다. 수신자의 내부 구현이 노출되기 때문에 캡슐화는 당연지사 무너지며 전송자가 내부 구현에 결합된다. 

만약 디미터 법칙에 따르게 코드를 개선하면 자연스레 이는 개선된다. 결국 이 법칙은 객체는 자신의 상태는 자신이 처리하는 자율적인 객체라는 핵심과 맞물리게 된다. 

---

### 묻지 말고 시켜라

위에서의 문제는 객체가 다른 객체의 내부 구현에 직접 접근하며 캡슐화를 깨뜨리는 행동이었다. 그렇다면 우린 어떻게 해야 할까. **묻지 말고 시켜라** 원칙을 따르면 된다. 앞선 문제는 ReservationAgency가 Screening과 Movie까지 직접 접근하며 책임을 온전히 본인이 모두 수행했다. 이는 screening.calculateFee() 메서드를 통해 Screening에게 책임을 위임하면 깔끔하게 해결된다. 

묻지 말고 시켜라 원칙을 지키며 메시지의 수신자를 결정하면 우린 자연스럽게 정보 전문가에게 책임을 할당하며 동시에 높은 응집도를 가진 클래스가 생성된다. 본문에서는 묻지 말고 시켜라 원칙과 디미터 법칙은 훌륭한 인터페이스를 제공하기 위해 포함해야 하는 오퍼레이션에 대한 힌트를 제공한다 말한다. 그 이유는 내부의 상태를 묻는 오퍼레이션과 내부의 상태를 이용해 어떤 결정을 내리는 객체 외부의 로직은 사실 자율적인 객체에게는 필요없기 때문이다. 자율적인 객체에게는 요청하는 것(시켜라)으로 모든 일이 종결된다. 물론 시키는 것으로 모든 문제가 해결되지 않는다. 어떻게 작업하는 것이 아닌 무엇을 하는지를 서술해야하는 것을 잊지말자.

---

### 의도를 드러내는 인터페이스

켄트 백이 언급한 메서드를 명명하는 방법 두 가지를 소개한다.

1. *메서드가 작업을 어떻게 수행하는지를 나타내라*
2. *‘어떻게’가 아닌 ‘무엇을’ 하는지를 드러내라*

앞서 설계했던 영화 예매 시스템의 할인 정책 클래스를 예시로 살펴보자.

```java
public class PeriodCondition {
	public boolean isSatisfiedByPeriod(Screening screening) { ... }
}

public class SequenceCondition {
	public boolean isSatisfiedBySequence(Screening screening) { ... }
}
```

위 스타일은 첫번째, 둘다 할인 조건을 판단하는 동일한 작업을 수행하지만 메서드의 이름이 다르다. 클라이언트 관점에서는 내부 구현을 정확하게 모른다면 둘이 동일한 작업을 수행한다는 사실을 알기 어렵고 캡슐화도 위반한다. 우리는 여태까지 변경이 적은 설계를 위해 노력해왔다. 만약 Movie 객체에서 할인조건을 기간에서 순번으로 변경하다면 우리는 클래스와 메서드 이름을 변경해줘야 한다. 이는 클라이언트의 코드가 변경된다는 것을 의미한다. 즉, 책임을 수행하는 방법을 드러내는 메서드 이름은 변경에 취약할 수 밖에 없다.

그렇다면 이름을 어떻게 지어야 할까. 두번째 방법으로 가보자. 무엇을 하는지를 드러내라는 말은 간단하다. 메서드가 어떤 책임을 지고 있는지를 표현하면 된다. 이는 클라이언트가 해당 메시지를 전송하기 위한 의도와도 부합한다. 변경해보자.

```java
public interface DiscountCondition {
	boolean isSatisfiedBy(Screening screening);
}
```

두 개의 클래스를 동일한 타입으로 선언하기 위해 인터페이스로 생성하고 isSatisfiedBy 오퍼레이션을 정의했다. 이렇게 되면 클라이언트는 할인 조건을 판단하고 싶을때 DiscountCondition.isSatisfiedBy()로 실체화된 클래스에 상관없이 메시지를 전송하기만 하면 된다. 

이렇게 '어떻게'가 아니라 '무엇'을 하느냐에 따라 메서드 이름을 짓는 패턴을 의도를 드러내는 선택자**(Intention Revealing Selector)**라고 부른다. 

---

### 함께 모으기

정답을 보는 것도 중요하지만 틀린 것을 보고 왜 틀렸는지를 파악하는 것도 학습에 도움이 된다. 아래는 디미터 법칙, 묻지 말고 시켜라 스타일, 의도를 드러내는 인터페이스를 위반하는 예제 코드다.

1. **디미터 법칙**
```java
public class Theater {
	private TicketSeller ticketSeller;
    ...
    public void enter(Audience audience) {
    	if(audience.getBag().hasInvitation()){
        	Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
        	Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }    
}
```

위 예제는 디미터 법칙을 위반한다. 디미터 법칙에 의하면 메시지를 전송할 수 있는 객체는 자기 자신 Theater, 인스턴스 변수 TicketSeller, 인자 Audience다. 하지만 enter()의 첫번째 조건문을 보면 audience.getBag()을 통해 참조를 얻은 Bag에게 메시지를 전송하고 있다. 그 아래의 ticketSeller.getTicketOffice().getTicket()도 마찬가지다. 둘 모두 디미터 법칙을 위반할 때 나타나는 기차 충돌 스타일의 전형적인 모습을 보인다. 

Theater는 Bag에게 메시지를 전송하고 이는 Theater와 Audneince의 내부 구조가 결합을 유발한다. Bag은 Audience의 내부 구현이다. 디미터 법칙의 위반은 인터페이스와 구현의 분리 원칙도 위반한다. 계속해서 얘기해왔던 얘기다. 객체는 자신의 상태는 스스로 관리할 수 있는 자율적이어야 한다.  

2. **묻지 말고 시켜라**
디미터 법칙을 위반한 코드를 묻지 말고 시켜라 원칙을 지키도록 수정했을 때 객체가 과연 스스로의 상태를 스스로 제어하는 자율적인 객체가 되는지 확인해보자. 우리는 위 코드의 else문 안에서 Theater가 Audience가 Bag안에 Ticket을 넣는 부분을 제어하는 것을 확인했다. 이 부분을 TicketSeller를 통해 Audience가 Ticket를 가지게 하도록 변경해보자.  

```java
public class TicketSeller {
	public void setTicket(Audience audience){
    	if(audience.getBag().hasInvitation()){
        	Ticket ticket = ticketOffice.getTicket();
            audience.getBag().setTicket(ticket);
        } else {
        	Ticket ticket = ticketOffice.getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketOffice.plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}

public class Theater {
	private TicketSeller ticketSeller;
    ...
	public void enter(Audience audience){
    	ticketSeller.setTicket(audience);
    }
}
```

이전 로직에서는 Theater가 Audience의 Bag에 Ticket을 넣었지만, 변경된 후에는 자신의 인스턴스인 TicketSeller에게 책임을 위임해 디미터 법칙을 위반하지 않는 상태로 변경됐다. 하지만 여기서도 TicketSeller의 setTicket() 내부에서는 Bag이 메시지를 전송하며 디미터 법칙을 위반한다. TicketSeller가 원하는 것은 Audience가 Ticket을 보유하도록 만드는 것이다. 이를 Audience가 스스로 처리하도록 변경해보자.

```java
public class Audience {
	public Long setTicket(Ticket ticket){
    	if(bag.hasInvitation()){
        	bag.setTicket(ticket);
            return 0L:
        } else {
        	bag.setTicket(ticket);
            bag.minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }
}

public class TicketSeller {
	public void setTicket(Audience audience){
    	ticketOffice.plusAmount(
        	audience.setTicket(
            	ticketOffice.getTicket()
            )
        );
    }
}
```

이제 TicketSeller는 디미터 법칙을 위반하지 않는다. 자신의 인스턴스 변수인 TicketOffice와 인자로 전달된 audience에게만 메시지를 전송한다. 하지만 마지막 문제가 우릴 기다리고 있다. 바로 Audience다. 우리는 객체에게 어떤 요청을 할 때 **묻지 않고 시켜야 한다**. 하지만 audience.setTicket()에서는 bag이 inviation을 가졌는지 hasInvitation()을 통해 묻고있다. 따라서 이는 디미터 법칙을 위반한다. 묻지 않고 시키도록 audience의 setTickeT()을 Bag으로 이동시키자.

```java
public class Bag {
	...
	public Long setTicket(Ticket ticket) {
        if (hasInvitation()) {
            this.ticket = ticket;
            return 0L;
        } else {
            this.ticket = ticket;
            minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }

    private boolean hasInvitation() {
        return invitation != null;
    }

    private void minusAmount(Long amount) {
        this.amount -= amount;
    }
}

public class Audience {
	public Long setTicket(Ticket ticket){
    	return bag.setTicket(ticket);
    }
}
```
더이상 Audience는 Bag에게 묻지 않는다. 시킬 뿐이다. 리팩토링한 결과를 한번 보자. 모든 객체는 스스로의 상태를 스스로 제어하게 되는 객체가 되었다. 자신의 상태를 스스로 관리하며 결정하는 자율적인 객체로 거듭났다. 이렇게 디미터 법칙과 묻지 말고 시켜라 스타일을 따르면 자연스레 객체는 자율적으로 변경된다.

3. **인터페이스에 의도를 드러내자**
위 리팩토링을 통해 우린 객체들을 자율적으로 만들었으며 유연한 협력관계도 구축했다. 하지만 한가지를 잊고 있었다. 바로 퍼블릭 인터페이스가 클라이언트의 의도를 명확히 드러내는지에 대한 고민은 하지 않았다. 클라이언트의 의도를 좀더 명확히 드러내도록 인터페이스를 변경해보자.

```java
public class TicketSeller {
	public void sellTo(Audience audience) {...}
}
```
Theater가 TicketSeller에게 setTicket 메시지를 전송한 이유는 무엇일까? 그 목적은 Audience에게 Ticket를 판매하는 것이었다.

```java
public class Audience {
	public Long buy(Ticket ticket) {...}
}
```
Audience에게 TicketSeller가 메시지를 보낸 이유는 무엇인가? Audience가 Ticket을 사도록 하기 위해서였다. 

```java
public class Bag {
	public Long hold(Ticket ticket) {...}
}
```
마지막으로 Audience는 Bag에게 setTicket 메시지를 전송한 이유는 무엇일까? 바로 Ticket을 보관하기 위해서였다. 

변경된 인터페이스를 보면 클라이언트가 메시지 수신자에게 어떤 것을 원하는지 보다 명확히 드러난다. 그리고 이 오퍼레이션들은 협력의 문맥을 반영해야한다. 객체 자신이 아닌 클라이언트의 의도를 표현하는 이름을 가져야한다는 사실을 기억하자.

디미터 법칙을 지키면 우리는 자연스럽게 캡슐화를 위반하는 메시지가 인터페이스에 포함되지 않는 것을 확인할 수 있다. 객체에게 요청할 뿐 내부 구현에 결합되지 않기 떄문이다. 다시 한번 명심하자. 자율적인 객체는 스스로의 상태를 스스로 처리한다는 것을.

---

### 결합도와 응집도의 충돌

우리는 묻지 말고 시켜라 법칙을 통해 위임 메서드를 추가했고 이를 통해 결합도를 낮추고 객체가 스스로의 상태를 스스로 제어하도록 변경했다. 과연 이 방법이 언제든 효과적일까? 예시를 통해 확인해보자.

```java
public class PeriodCondition implements DiscountCondition {
	public boolean isSatisfiedBy(Screening screening) {
       return screening.getStartTime().getDayOfWeek().equlas(dayOfWeek) &&
       		startTIme.compareTo(screening.getStartTime().toLocalTime()) <= 0 &&
            endTime.compareTo() screening.getStartTime().toLocalTime()) >= 0;
    }
}
```

여기서 isSatisfiedBy()는 screening의 기차 충돌 코드와 질의한 뒤 수행하는 등 디미터 법칙과 묻지 말고 시켜라 원칙, 캡슐화을 위반한다. 따라서 우리는 screeing의 isDiscountable() 메서드로 위 로직을 옮기면 묻지 말고 지켜라 스타일 원칙을 지키도록 변경할 수 있다. 하지만 이게 과연 옳은 일일까? 

위 로직이 screening의 isDiscountable() 메서드로 이동한다는 것은 할인 조건을 판단하는 책임이 PeriodCondition가 아닌 Screening으로 변경된다는 것을 의미한다. 우린 여기서 과연 할인 조건을 판단하는 책임을 Screening이 담당하는 것이 맞냐는 본질적인 질문이 필요하다.

심지어 메서드가 이동하면서 PeriodCondition의 인스턴스 변수를 인자로 받게 된다. 이는 또다른 결합도를 높히는 원인이다. 따라서 전체적인 관점에서 PeriodCondition와 Screening의 결합도를 낮추고 Screening의 응집도를 높이는 것이 Screening의 캡슐화보다 중요하다.

말하고자 하는 것은 항상 묻지 말고 시키는 것이 능사가 아니라는 것이다. 가끔씩은 묻는 경우도 있어야 한다. 우리가 말하는 원칙들은 무조건 지켜야하는 법칙이 아니다. 가장 최적의 설계를 위한 이정표일 뿐 절대 맹신해서는 안된다. 설계는 항상 치열한 트레이드 오프의 산물이라는 것을 잊지 말자.

---

### 명령-쿼리 분리 원칙

명령-쿼리 분리 원칙을 알아보기 전에 명령과 쿼리가 무엇인지부터 확인하자. 둘다 오퍼레이션이다. 다만 객체의 상태를 수정하는지, 객체의 관련된 정보를 반환하는지에 따라 각각 명령과 쿼리로 부른다. 이 둘은 분리하는 이유는 '난 둘다'가 되서는 안되기 때문이다. 객체의 오퍼레이션은 객체의 상태를 변경하거나, 상태를 변경하지 않고 반환만 해야한다. 어떤 오퍼레이션도 객체의 상태를 변경함과 동시에 상태를 반환해서는 안된다. 이를 위해 우리는 지켜야할 두가지 원칙이 있다.

1. 객체의 상태를 변경하는 명령은 반환값을 가질 수 없다.
2. 객체의 정보를 반환하는 쿼리는 상태를 변경할 수 없다.

둘이 함께 해서는 안되는 이유가 무엇일까. 바로 부수효과다. 명령과 쿼리가 하나의 오퍼레이션 안에 섞이게 되면 실행 결과를 예측하기 어려워진다. 

예를 들어 해설지에 문제의 답을 알려주는 버튼을 눌렀다. 처음엔 정답이 2번으로 나왔다. 그런데 다음에는 문제의 답이 3번이 되어있었다. 예시가 이해가 안될수도 있겠지만 말하고자 하는 것은 오퍼레이션(문제의 답을 알려주는 버튼을 누름)을 실행했을 때 동일한 결과를 반환하지 않는다 경우를 말하고 싶었다. 이 경우는 오퍼레이션에 반환하는 '쿼리'와 정답을 수정하는 '명령'이 함께 있었기 때문이다.

이렇게 동일한 오퍼레이션에 다른 결과를 반환하는 부수효과는 실행 결과를 예측하는데에 큰 어려움이다. 따라서 이해하기도 어려울 뿐더러 잘못 사용할 가능성이 높고 버그까지 양산한다. 따라서 명령과 쿼리를 명확하게 분리해야만 부수효과로 인한 문제점들을 해결할 수 있다.