# 20220311(Adapter Pattern)

작성일시: 2022년 3월 11일 오전 12:06

# 어댑터 패턴

어댑터 패턴은 매우 간단하다. 메인 로직을 수행하는 인터페이스가 있다고 가정하자. 근데 다른 외부에서 기능을 구현한 인터페이스를 가져오려고 한다. 인터페이스가 다르기 때문에 기존에 인터페이스에 적용해서 사용 할 수는 없지만 그렇다고 코드를 완전히 수정 하고 싶지도 않다. 이럴 때 어댑터 패턴이 유용하다.  어댑터 패턴은 다른 로직의 클래스 또는 인터페이스를 우리 로직에서 사용 할 수 있도록 한 번 랩핑을 한 것이라 보면 된다. 아래의 예를 보자

![731CC0B2-0A9A-4C36-999B-21E8B5EE0558.jpeg](20220311(adapter_pattern)/1.jpeg)

어댑터 클래스를 하나 정의 하고 기존의 SearchStratedy 의 인터페이스를 implement 받는다. 그 이유는 searchButton이 SearchStrategy를 이용한 전략 패턴이기 때문에 어댑터를 searchButton이 인식하려면 SearchStrategy 상속이거나 implement 상태이여야 하기 때문이다. 이때 어댑터 클래스는 다른 인터페이스인 FindAlgorithm을 인스턴스 변수로 가진다. 그 이유는 SearchStrategy의 오버라이드 메서드인 search()에서 findAlgorithm 인터페이스의 메서드를 호출하기 위함이다. 그렇게 되면 어댑터는 SeachStrategy의 인터페이스니 SearchButton에서 실행하는데 무리 없고 실제 기능은 FindAlgorithm에서 동작하기 때문이다. Test.java를 보면 어댑터가 어떻게 응용되는지 쉽게 파악 할 수 있다.

# 코드

**FindAlgorithm 인터페이스 및 구현 클래스**

```java
public interface FindAlgorithm {
    public void find(boolean condition);
}

class MovieFindAlgorithm  implements  FindAlgorithm{

    @Override
    public void find(boolean condition) {
        if(condition == true) {
            System.out.println("동영상 찾기~~!!!");
        } else {
            System.out.println("not found");
        }

    }
}
```

**SearchStrategy를 implement 받은 어댑터 클래스**

```java
public class SearchAdapter implements SearchStrategy {

    private FindAlgorithm findAlgorithm;

    public SearchAdapter(FindAlgorithm findAlgorithm) {
        this.findAlgorithm = findAlgorithm;
    }

    @Override
    public void search() {
        findAlgorithm.find(true);
    }
}
```

**Test 코드**

```java
public class Test {
    public static void main(String[] args) {
        SearchButton searchButton = new SearchButton();
        searchButton.setSearchStrategy(new SearchAdapter(new MovieFindAlgorithm()));
        searchButton.onClick();
    }
}
```