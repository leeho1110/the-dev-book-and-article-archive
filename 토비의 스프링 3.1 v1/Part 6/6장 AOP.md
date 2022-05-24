# 6장: AOP

- AOP가 무엇이고, 왜 등장한 것일까.
- 가장 인기있는 AOP 적용 대상은 단언코 선언적 트랜잭션 기능이다. 앞선 5장에서는 서비스 추상화를 통해 트랜잭션 경계 설정의 근본적인 문제를 해결했었다.
- 이번엔 AOP를 통해 바꿔보자. 그리고 거기서 why도 확인하자.

---

### 1. 트랜잭션 코드의 분리

- 우리가 앞서 UserService에 작성한 코드는 다음과 같은 특징을 갖는다.
    - 트랜잭션 경계설정 코드와 비즈니스 로직 코드는 서로 주고받는 정보가 없다.
    - 또한 둘의 경계가 뚜렷하게 구분된다.
    - 그렇다면 이를 분리할 수 있지 않을까?
        
        ```java
        // 트랜잭션 경계 설정 코드
        public void upgradeLevels() throws Exception {
        		TransactionStatus status = trasactionManager.getTransaction(new DefaultTransactionDefinition());
        		
        		try {
        				upgradeLevelsIntenal();
        				this.trasactionManager.commit(status);
        		} catch (Exception e) {
        				this.trasactionManager.rollback(status);
        				throw e;
        		}
        }
        
        // 비즈니스 로직
        private void upgradeLevelsIntenal() {
        		...
        }
        ```
        
- 비즈니스 로직 코드를 별도의 메서드로 분리했다. 그렇다면 이를 사라지게 할 수 있을까?
    - 트랜잭션 경게 설정 코드를 클래스 밖으로 뽑아버리자.
    - 그런데 이렇게 트랜잭션을 클래스 밖으로 뽑으면 다른 클래스가 UserService.upgradeLevels()를 사용할 때 트랜잭션이 없는 상태로 사용하게 된다.
        - 지금은  UserServiceTest가 UserService를 사용할 때 직접 구체 클래스를 참조한다. 이 때문에 클라이언트(UserServiceTest)와 강한 결합도가 존재한다.
    - 이것이 바로 구체적인 구현 클래스를 직접 사용할 때의 문제점이다.
- 이를 해결하기 위해 우린 DI를 통해 결합도를 낮게 유지할 수 있으며, 인터페이스를 사용할 수 있었다.
    - 우리가 보통 인터페이스를 사용한 이유는 런타임에 구체 클래스를 바꾸기 위함이었다.
    - 하지만 이렇게 생각해보자.
        - 우리는 책임을 둘로 나눴다. 트랜잭션 코드에 대한 책임과, 비즈니스 로직에 대한 책임으로
        - 이 둘은 결국 같이 실행되어야 하며, 비즈니스 코드는 트랜잭션 내부에서 실행되어야 한다.
- 그렇다면 트랜잭션 경계를 설정하는 구체 클래스가 트랜잭션 경계 설정을 한 뒤, 비즈니스 로직을 담당하는 구체 클래스를 호출하면 되지 않나?
    - 자, 트랜잭션 코드를 담당하는 UserService 구현 클래스가 비즈니스 로직 코드를 담당하는 UserService를 호출하도록 해보자.
- 우선 기존 UserService 구현체는 인터페이스를 구현해야 하므로 이름을 변경하자. 그리고 비즈니스 로직과 트랜잭션 경계 설정을 구현하는 구체 클래스를 생성해보자.
    
    ```java
    // 트랜잭션을 설정하는 코드는 메서드의 세부 구현이라는 사실을 잊지 말자
    public interface UserService {
    		void add(User user);
    		void upgradeLevels();
    }
    
    // 비즈니스 로직 코드를 구현한 구체 클래스
    public class UserServiceImpl implements UserService {
    		UserDao userdao;
    		MailSender mailSender;
    
    		publc void upgradeLevels() {
    				...
    				// 비즈니스 로직
    		}
    }
    ```
    
    ```java
    public class UserServiceTx implements UserService {
    		// UserServiceTx가 트랜잭션 경계 설정을 한 뒤 비즈니스 로직을 호출할 UserService 구현체다.
    		UserService userService;
    
    		// 트랜잭션 경계 설정에 필요한 Transactional Manager
    		PlatformTransactionManager transactionManager;
    
    		public void setTransactionalManager(PlatformTransactionManager transactionManager) {
    				this.transactionManager = transactionManager
    		}
    
    		public void setUserService(UserService userService) {
    				this.userService = userService
    		}
    
    		public void add(User user) {
    				this.userService.add(user);
    		}
    
    		public void upgradeLevels() {
    				// UserServiceTx의 트랜잭션 경계 설정 로직
    				TransactionStatus status = this.trasactionManager.getTransaction(new DefaultTransactionDefinition());
    
    				// UserServiceImpl의 비즈니스 로직 '시작'
    				try {
    						this.userService.upgradeLevels();
    						// UserServiceImpl의 비즈니스 로직 '끝'
    
    						this.transactionManager.commit();
    				} catch (RuntimeException e) {
    						this.transactionManager.rollback();
    						throw e;
    				}
    		}
    }
    ```
    

- 이렇게 함으로써 우리가 얻는 장점이 무엇일까?
    - 우리는 이제 트랜잭션 코드와 비즈니스 로직을 완전히 분리했다.
    - 또한 클라이언트는 구체 클래스가 아닌 인터페이스에 의존한다
    - 우리가 만약 비즈니스 로직을 추가하고 싶다면 UserServiceImpl만 건들면 된다. 이것은 단일 책임 원칙과도 상응한다.
    - 스포일러긴 하지만, 항상 이렇게 스프링은 객체지향의 원칙을 자연스럽게 반영한다. 또한 개발자가 비즈니스 로직에만 집중할 수 있도록 도와준다.

