# Clean Code - Robert C. Martin

### PART 2 : 의미 있는 이름

1. 의도를 분명히 밝혀라
    1. 단순한 함수라도 좋은 이름으로 각 코드가 하는 일을 명확하게 알 수 있도록 해라
2. 그릇된 정보를 피하라
    1. 나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용하지 말아라
    2. 일관성이 떨어지는 표기법은 지양해라
3. 검색하기 쉬운 이름을 사용해라
    1. 이름을 의미있게 지으면 함수가 길어진다. 하지만 찾기가 훨씬 쉬워진다.
4. 클래스 이름
    1. 명사, 명사구를 사용하고 동사는 사용하지 않는다.

        ex) `Custoemr`, `Account` → Good / `Manager`, `Processor`, `Data`, `Info` → Bad

5. 메소드 이름
    1. 동사, 동사구가 적합하며 접근자, 변경자, 조건자는 javabean 표준에 따라 앞에 `get`, `set`, `is`를 붙인다.

    ```java
    String name = employee.getName();
    customer.setName("mike");
    if (payCheck.isPosted())...
    ```

6. 한 개념에는 한 단어를 사용하라
    1. 동일 코드에는 `controller`, `manager`, `driver` 섞어 쓰기 X , `Controller` 하나로 통일
7. 기술 개념에는 기술 이름을 사용하라
    1. 코드를 짜는 사람도, 읽는 사람도 프로그래머라는 사실
    2. 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 등을 사용해도 괜찮다

        ex) `JobQueue`

8. 맥락의 중요성
    1. 의미있는 맥락을 추가해라
        1. 클래스, 함수 등을 활용해라

            firstName, lastName, street, houstNumber, city, state, zipcode 가 같이 있다면 주소라는 사실을 알아챌 수 있는지만 달랑 'state'만 있다면 알아채기 힘들다.

            이런 경우 addrFirstName, addrLastName, addrState 라고 작성하면 맥락이 좀 더 분명해진다. 혹은 Address 클래스의 멤버 필드로 사용하면 된다.