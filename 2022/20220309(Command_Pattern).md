# 20220309(Command Pattern)

작성일시: 2022년 3월 9일 오후 6:49

# 커맨드 패턴

커맨드 패턴은 보면 상태패턴과 전략 패턴과 비슷하다. 인터페이스를 이용해 각 기능을 모듈화하고 모듈을 객체에 삽입하는 식으로 진행한다. 커맨드 패턴이 상태 패턴 또는 전략 패턴과 다른 점은 무엇일까???

**전략 패턴: 동일한 기능이 모드에 따라 변화한다.**

**상태 패턴: 상태에 따라 기능이 변화한다.**

**커맨드 패턴: command에 따라 완전히 다른 기능을 수행한다.**

# 로봇 예제

로봇 예제를 통해 예제를 살펴보자

## 로봇 클래스 구현하기

로봇은 다음과 같은 기능을 가지고 있다.

1. 앞으로가기(space만큼)
2. 회전하기(방향)
3. 물건 집기

우리는 이 로봇의 기능을 조정할 comand를 만들 생각이다. command 추상클래스를 통해 여러 모듈의 command 를 만든다.

## 커맨드 클래스 구현하기

1. abstract class Command
    1. robot 변수 상속
    2. setter robot 상속
    3. **abstract execute()** 
2. class MoveForwardCammand extends Command
3. class TurnCommand  extends Command
4. class PickupCommand extends Command

execute 를 추상 메서드로 만들어서 자식 Command 모두 다른 기능의 execute를 구현해야한다.

이제 전에 해봤던 전략패턴의 searchbutton이나 상태패턴의 burnner 클래스처럼 command 객체를 담아두고 실행을 요청할 클래스가 필요하다. 우린 이것을 RobotKit이라 하겠다.

## 로봇 키트 구현하기

**RobotKit**

1. 로봇 인스턴스 변수 정의
2. command를 담아둘 리스트 정의
3. addCommand (Command) 메서드 정의
4. 커맨드를 차례대로 실행할 start 메서드 구현
    1. command.setRobot(robot)
    2. command.execute()

## 테스트 해보기

**Test**

```java
public class Test {
    public static void main(String[] args) {
        RobotKit robotKit = new RobotKit();
        robotKit.addCommand(new MoveForwardCommand(3));
        robotKit.addCommand(new TurnCommand(Robot.Direction.LEFT));
        robotKit.addCommand(new MoveForwardCommand(1));
        robotKit.addCommand(new TurnCommand(Robot.Direction.RIGHT));
        robotKit.addCommand(new PickupCommand());
        robotKit.start();
    }
}
```

# 코드

**Robot 클래스**

```java
public class Robot {
    public enum Direction {LEFT, RIGHT} // 방향 표시

     
    public void moveForward(int space) {
        System.out.println(space + " 칸 전진");
    }

    public void turn(Direction direction) {
        String s = (direction == Direction.LEFT ? "왼쪽" : "오른쪽") +
                "으로 전환한다.";
        System.out.println(s);
    }

    public void pickup() {
        System.out.println("앞의 물건 집어들기");
    }
}
```

**command 추상 클래스**

```java
/*
        커맨드 패턴은 로봇 객체에 의존한다.
        robot 객체를 인스턴스로 사용해서 robot의 기능을 조종한다.
 */
public abstract class Command {
    protected Robot robot;

    //setter
    public void setRobot(Robot robot) {
        this.robot = robot;
    }
    public abstract void execute();
}

class MoveForwardCommand extends Command {
    int space;
    public MoveForwardCommand(int space) {
        this.space = space;
    }

    @Override
    public void execute() {
        robot.moveForward(this.space);
    }
}

class TurnCommand extends Command {
    Robot.Direction direction;
    public TurnCommand(Robot.Direction direction) {
        this.direction = direction;
    }
    @Override
    public void execute() {
        robot.turn(direction);
    }
}

class PickupCommand extends Command {
    @Override
    public void execute() {
        robot.pickup();
    }
}
```

**RobotKit 클래스**

```java
import java.util.ArrayList;

public class RobotKit {
    private Robot robot = new Robot();
    private ArrayList<Command> commands = new ArrayList<>();

    public void addCommand(Command command) {
        commands.add(command);
    }

    public void start() {
        for(Command command : commands) {
            // setRobot 덕분에 다른 로봇도 동일한 Command로 조종이 가능하다.
            // command의 재활용성이 높아진다.
            command.setRobot(robot);
            command.execute();
        }
    }
}
```

# 참고

[https://www.youtube.com/watch?v=lJES5TQTTWE&list=WL&index=36&t=402s](https://www.youtube.com/watch?v=lJES5TQTTWE&list=WL&index=36&t=402s)