# 20220309(State Pattern)

작성일시: 2022년 3월 9일 오후 6:29

# 상태 패턴

기본적으로 . 외부에서 객체를 생성해서 끼워주는 패턴은 전략 패턴과 매우 유사하다. 그러나 활용 용도가 조금 다르다. 전략 패턴은 동일한 업무가 주어지고 mode에 따라 동일한 업무가 조금씩 차이가 있다. 그러나 상태 패턴의 경우 상태에 따라 업무가 다르고 상태 또한 변화시킨다는 차이점이 있다. 상태의 변화는 실제 동작을 수행하는 interface 객체에서 일어 날 수 도 있고 아니면 interface  인스턴스 변수를 가지고 있는 객체에서 분기문으로 상태를 변화 시킬 수 있다. 이 방법은 개발자의 자유이다.

![Untitled](20220309(StatePattern)/Untitled.png)

위 코드는 가스 버너를 예제로 한 코드이다. 버너는 불을 킨 상태, 그리고 불을 끈 상태 이렇게 2가지 상태가 있다. 이 예제의 경우 버너의 상태를 modeState로 정의하고 인터페이스를 implement한  2개의 클래스를 정의해주었다.

여기선 toggle로 상태 처리 해주었지만 기본적으로 전략패턴과 패턴 자체는 동일하다. 인터페이스를 통해 여러 state를 모듈화해서 관리하고 외부에서 객체를 생성한다음 setter를 이용해 넣어준다.

OCP(Open /Closed Principle) 과 SRP (Single Responsibility Principle) 2가지를 모두 만족한다.

그리고 실제로 동작할 메서드는 ModeState 객체에서 호출해준다.

# 코드

**ModeState 인터페이스**

```java
public interface ModeState {
    public void toggle(Burner burner);
}

class ModeStateOn implements ModeState {
    @Override
    public void toggle(Burner burner) {
        System.out.println("불을 끄겠습니다.");
        // ........
        // 기능 구현
        // ........
        burner.setModeState(new ModeStateOFF());
    }
}

class ModeStateOFF implements ModeState {
    @Override
    public void toggle(Burner burner) {
        System.out.println("불을 올리겠습니다.");
        // ........
        // 기능 구현
        // ........
        burner.setModeState(new ModeStateOn());
    }
}
```

**버너 클래스**

```java
package com.burner;

public class Burner {
    ModeState modeState = new ModeStateOFF();

    // modeState setter
    public void setModeState(ModeState modeState) {
        this.modeState = modeState;
    }

    public void toggle() {
        modeState.toggle(this);
    }

}
```

**Test 코드**

```java
package com.burner;

public class test {
    public static void main(String[] args) {
        Burner burner = new Burner();
        burner.toggle();
        burner.toggle();
    }
}
```