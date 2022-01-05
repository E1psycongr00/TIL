# 20220105(Interface)

작성일시: 2022년 1월 5일 오후 7:11

# 오늘 배운것

## 인터페이스

- **Interface** 인터페이스 : 스스로는 불안전하기 때문에 객체를 스스로 생성 할 수 없다.
    - 상수 : int size → public static final size
    - 추상 메서드 : 상속시 **반드시** Override 해주어야 한다.
        - 추상 메서드를 하나만 명시적으로 선언하는 **@FunctionalInterface**를 이용하는 방법이 있다.
        - 만약 하나만 명시적으로 선언 했다면 람다를 활용 할 수 있다.
    - default 메서드 : body를 구현해주고 상속시 반드시 구현할 필요 없다.
    - static 메서드 : 스태틱 메서드로서 사용한다. 바디로 메서드를 초기화해준다

- 인터페이스의 관계
    - 인터페이스와 클래스는 구현관계에 있다. 점선으로 표현하고 다중상속이 가능하다.
    
    ![DC7FD29E-674E-49A3-80DF-AEAADEA14F73.jpeg](20220105(Interface)/DC7FD29E-674E-49A3-80DF-AEAADEA14F73.jpeg)
    
    - 인터페이스와 인터페이스는 상속관계이다.
    
    ![9F788C12-BEF7-4187-8AA0-EAF5DBCA4548.jpeg](20220105(Interface)/9F788C12-BEF7-4187-8AA0-EAF5DBCA4548.jpeg)
    

- **인터페이스 왜쓰지???????**
    - 인터페이스를 쓰는 이유는 상속시 메서드를 강제해 유지보수를 편하게 하기 위함이다.
    - 기능 별로 메서드를 강제해 구현 할 수 있다.
        - ex) 예를 들어 슈퍼맨, 비행기, 새 이 3개의 공통점은 무엇일까? 바로 날아 다닐 수 있다는 것이다. 그렇다면 각 객체를 따로따로 메서드를 관리 할 필요 없이 Interface flyer 이런식으로 날아다니는 동작의 기능 메서드를 abstract로 지정해준다면 각 객체에 상속 메서드를 강제해서 편하게 관리 할 수 있다.
    - 인터페이스와 다형성을 잘 활용하면 디커플링을 할 수 있다.
        - 디커플링:  서로 객체간의 종속성을 줄이는 작업이다.
            - DB 연동 구조 (No Interface) [비추]
                
                ![CF96BEB6-A98C-4B80-B74B-17AD5EDD99C1.jpeg](20220105(Interface)/CF96BEB6-A98C-4B80-B74B-17AD5EDD99C1.jpeg)
                
                - 만약 내가 오라클 DAO를 사용하고 있었는데 이유가 생겨서 MYSQL DAO를 사용한다고 가정하자. 위와 같은 구조라면 ORACLE DAO 객체를 MYSQL객체로 수정해서 사용해야 한다. MYSQL DAO 클래스를 사용하기 위해서 Service 객체의 메서드와 인스턴스 변수를 수정해야 하므로 유지보수가 힘들어지고 비효율적이다.
            - DB 연동 구조 (use Interface) [추천]
                
                ![98AECEC5-CD39-4C7C-9CE0-0754B306CF04.jpeg](20220105(Interface)/98AECEC5-CD39-4C7C-9CE0-0754B306CF04.jpeg)
                
                - **Interface**를 이용해 오라클DAO와 MYSQL를 구현하면 Service 클래스 수정없이 다형성을 이용해서 서로간에 자유롭게 스위칭이 가능하다. 이 방법을 이용하면 다른상황이 오더라도 쉽게 유지보수가 가능하다. 역시 **다형성!!!!**

**DAO의 다형성 코드를 분석해보자**

```java
public class OracleDAO {

	public void connect() {
		System.out.println("oracleDAO.connectOracle");
		
	}
}

public class MySqlDAO {
	
	public void connect() {
		System.out.println("MySQL.connectMySQL");
	}
}

public class DBService {
	
	OracleDAO dao;  // 만약 dao가 바뀐다면 다른 객체이기 때문에 수정해줘야됨
	
	public void setDAO(OracleDAO dao) { // 이부분도 수정해줘야함
		this.dao = dao;
	}
	
	public void connect() {
		dao.connect();
	}
}
```

기존 코드의 경우 메서드 매개변수 타입과 인스턴스 타입 모두 수정해줘야 한다. 프로젝트가 커진다면 일일히 수정하는데 불편함을 느낄 것이다.

```java
public interface DBDAO {

	public abstract void connect();
}

public class MySqlDAO implements DBDAO{
	
	@Override
	public void connect() {
		System.out.println("MySQL.connectMySQL");
	}
}

public class OracleDAO implements DBDAO{

	@Override
	public void connect() {
		System.out.println("oracleDAO.connectOracle");
	}
}

public class DBService {
	
	DBDAO dao;
	
	public void setDAO(DBDAO dao) { // 다형성
		this.dao = dao;
	}
	
	public void connect() {
		dao.connect();
	}
}
```

이렇게 쓰는 경우 다형성 덕분에 바껴도 내부 클래스 코드를 바꿀 필요 없이 호출할때 변수만 다르게 해주면 다형성에 의해서 알아서 잘 동작할 것이다.

- 익명의 인터페이스 혹은 추상 클래스와 람다
    - 인터페이스 혹은 추상 클래스의 추상 메서드가 하나라면 람다를 활용 할 수 있다.
    
    ```java
    @FunctionalInterface
    interface Flyer {
    	public abstract void fly();
    }
    
    ```
    
    ```java
    Flyer f1 = new Flyer() {
    			
    			@Override
    			public void fly() {
    				System.out.println("익명 인터페이스");
    			}
    		};
    Flyer f1 = () -> {
    			System.out.println("람다.fly");
    		}; // 반드시 interface의 abstract method가 하나여야 한다.
    		f2.fly();
    ```
    

# 오늘 느낀점

- 오늘 인터페이스를 다시 공부하면서 전에 만들었던 간이 프로젝트가 생각났다. Interface로 전기요금의 틀을 잡고 세부 자식 클래스를 주택용, 산업용으로 나눴다면 요금 계산할 때 다형성을 좀더 쉽게 활용 할 수 있을 것 같다는 생각이 들었다.
- 람다는 쉽게 표현해주지만 가독성이 내겐 아직 조금 떨어지는 것 같다. 익숙해져야겠다.
- 다음 프로젝트 때는  도메인 설계시 상속과 인터페이스를 이용한 구현을 조금 더 깊이 고민해봐야겠다.