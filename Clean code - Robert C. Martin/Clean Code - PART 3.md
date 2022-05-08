# Clean Code - Robert C. Martin : PART 3

### PART 3 : 함수

1. 함수는 무조건 작게 만들어라
    1. 중첩 구조가 생길만큼 함수가 커지 말아야 한다. 들여쓰기 수준이 1,2 depth 이하가 되도록.
2. 한가지만 해라
    1. 함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야 한다.
3. 함수 당 추상화 수준은 하나로
    1. 각 메소드의 추상화 수준을 근본, 세부개념을 섞지말고 하나로 만들어라
        1. `getHTML()` → 높다
        2. `PathParser.render()` → 중간
        3. `.append()` → 낮음
        
    2. 내려가기 규칙
        1. 코드는 위에서 아래로 이야기처럼 읽혀야 하면 한 함수 내에는 추상화 수준이 한 단계 낮은 함수가 등장한다. 
        	<br> **내려가기** 규칙이다. 준수하도록 하자.
        2. 다시 한번 핵심! 함수는 짧으면서도 **'한 가지'** 만 해야 한다
        
    3. 83p `Switch`문 : **다시 한번 읽기**
    
4. 함수 인수
    1. 가장 이상적인 개수는 0개, 무항이다. 다음은 1개, 2개. 3개 이상은 피한다.
    2. 플래그 인수 (`boolean`) : 추하다. 아주 끔찍한다.
    3. 단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄여 한다.

         ex) `writeField(name)`

5. 부수 효과를 일으키지마라
    1. 한 가지의 함수는 한 가지의 일만 해라
        1. `checkPassword()` 는 비밀번호를 확인하는 일만 수행하라. 

            ```java
            public boolean checkPassword(String userName, String password){
            	// decode
            	if("VAlid PassWord".equals(password){
            		// initialize() 작업은 비밀번호를 확인하는 일에서 벗어난다
            		Session.initialize();
            		return true;
            	}
            	return false;
            }
            ```

            Session.initialize() 를 수행하는 것은 만약 `checkPassword()` 라는 메소드의 이름만 보고 호출했다면 `Session` 이 초기화되는 부수 효과가 발생한다. 이런 일이 없도록 ***하나의 함수는 한 가지의 일*** 만 해라.

6. 명령과 조회를 분리하라
    1. 함수는 뭔가를 수행하거나 답하거나 둘 중 하나만 해야 한다.
    2. 객체 상태를 변경하거나, 객체 정보를 반환하거나 둘 중 하나다.

          ```java
          public boolean set(String attribute, String value)
          ```
          위 함수가 만약 아래처럼 들어간다면 value 값을 attribute에 단순히 set하는지, set을 성공한다면 조건문을 수행하는지 모호하다.
          ```java
          if(set("username","unclebob"))
          ```

          명령과 조회는 혼란을 야기하기 때문에 꼭 분리해야 한다.