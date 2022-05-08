# Clean Code - Robert C. Martin : PART 7

### PART 7 : 오류 처리

오류 처리는 프로그램에서 반드시 필요한 요소이다. 잘못된 가능성은 늘 존재하며, 바로 잡는 것은 개발자의 책임이다. 깨끗하고 튼튼한 코드에 다가갈 수 있는 단계인 오류를 처리하는 기법과 고려 사항 몇가리를 살펴본다.

1. 오류 코드보다 예외를 사용하라
    1. 논리가 오류 처리 코드와 뒤섞이지 않도록 예외 처리 코드를 통해 호출자 코드를 깔끔하게 만들어라. 
2. `Try-Catch-Finally` 문부터 작성하라
    1. Try 블록은 트랜잭션과 비슷하다. Try 블록에서 무슨 일이 생기든 catch 블록에서 프로그램 상태를 일관성 있게 유지시켜야 한다. 
    2. 즉, 예외가 발생할 수 있는 코드를 짤 떄에는 `try-cathc-finallly` 문으로 시작하는 것이 낫다.
3. 미확인 예외를 사용하라
    1. 예전엔 확인된 에외를 사용했다. 메소드에서 발생할 수 있는 모든 예외를 열거했다. 
        1. `OCP(Open Closed Principle, 개방 폐쇄 원칙)` 을 위반한다.

            *ex) `catch` 블록이 세 단계 위에 있다면 그 사이의 모든 메소드의 선언부를 수정해야한다.*

    2. 때로는 확인된 예외도 유용하다. 하지만 일반적인 애플리케이션은 의존성이라는 비용이 이익보다 크다.
4. 예외에 의미를 제공하라
    1. 예외를 던질 때는 전후 상황을 충분히 덧붙혀 오류가 발생한 원인과 위치를 찾기 쉽게 작성하라.
5. 호출자를 고려해 예외 클래스를 정의하라
    1. 프로그램에게 가장 중요한 관심사는 **오류를 잡아내는 방법**이 되어야 한다.
    2. 외부 라이브러리가 던질 모든 에러를 잡아내는 것이 아닌 Wrapper 클래스를 생성하여 외부 API를 감싼 뒤 발생할 수 있는 에러 여러개를 하나로 묶는 것이 최선이다.
        1. 라이브러리 변경에 비용이 적다.
        2. 설계에 발목 잡히지 않는다.
6. 정상 흐름을 정의하라
    1. 특정 상황이 존재한다면 특수 사례를 처리하는 코드를 작성하라

        *ex) 비용의 총계를 계산하는 애플리케이션을 작성한다고 가정했을때, 비용이 발견되지 않는 경우는 식비를 더한다면 ?*

        ```java
        try {
        	MealExpenses expenses = expenseReportDAO.getMeals(employess.getId());
        	m_total += expenses.getTotal();
        } catch(MealExpensesNotFound e) {
        	m_total += getMealPerDiem();
        }
        ```

        이 경우 예외처리가 논리를 따라가기 어렵게 만든다. 특수 상황을 처리하지 않도록 변경해보자

        ```java
        MealExpenses expenses = expenseReportDAO.getMeals(employess.getId());
        m_total += expenses.getTotal();

        public class PerDiemMealExpenses implements MealExpenses {
        	public int getTotal() {
        		// 일일 식비를 default 값으로 반환.
        	}
        }
        ```

        이렇게 `특수 사례 패턴 SPECIAL CASE PATTERN` 을 작성하게 되면 예외적인 상황을 캡슐화하여 예외적인 상황을 처리하지 않아도 된다.

7. null을 반환하지 마라

    ```java
    public void registerItem(Item item) {
    	if( item != null ) {
    		ItemRegistry registry = per.getItemRegistry();
    		if( registry != null ) {
    				// 코드
    		}
    }
    ```

    아주 나쁜 코드다. 한 줄 건너 하나씩 null을 확인하는 코드는 절대 나쁘다.
    만약 null을 반환하고싶다면, 예외를 던진다거나 특수 사례 객체를 반환하는 것을 선택해라.
8. null을 전달하지 마라
    1. null을 반환하는 방식도 나쁘지만, null을 전달하는 방식이 훨씬 더 나쁘다.
    2. 메소드로 null을 전달하는 코드는 최대한 피해야 한다.
    3. 대다수의 프로그래밍 언어는 호출자가 실수로 넘기는 null을 적절히 처리하는 방법이 없다. 따라서 차라리 애초부터 null을 넘기지 못하도록 금지하는 정책을 사용하는 것이 훨씬 합리적이다.

9. 결론
    1. 깨끗한 코드는 읽기도 좋아야 하지만, 안정성도 높아야 한다. 둘은 상충하는 것이 아니라, 오류 처리를 프로그램 논리와 분리해 독자적인 사안으로 고려애햐 한다.
