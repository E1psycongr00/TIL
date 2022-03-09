# 20220308(SpringFramework)

작성일시: 2022년 3월 8일 오전 9:20

# Spring Framework 시작하기

![Untitled](20220308(S%2024785/Untitled.png)

![Untitled](20220308(S%2024785/Untitled%201.png)

![Untitled](20220308(S%2024785/Untitled%202.png)

## 메이븐 의존성

[](https://mvnrepository.com/)

Dependency 코드 제공

![Untitled](20220308(S%2024785/Untitled%203.png)

pom.xml을 누르면 의존성에 대한 코드가 나오는데 man레포에서 다운 받아서 쓰면 된다.

![Untitled](20220308(S%2024785/Untitled%204.png)

JDK version Dependency가 있다.

## 메이븐 의존성 패치가 자동을 안될때

![Untitled](20220308(S%2024785/Untitled%205.png)

update project 해주면 된다.

## 프로젝트 폴더 분석

![Untitled](20220308(S%2024785/Untitled%206.png)

main/java 부분에 java 파일

main/resource 부분에는 java 외의 파일을 넣어준다. ex) *.xml, *.jpg.......

# Spring Framework 프로젝트시 확인할 스펙들

Spring Framework를 활용하기 이전에 먼저 spring Framework 스펙을 먼저 알아볼 필요가 있다.

spring Framework를 사용 할 때 크게 체크해야 할 버전은 대략 7개 정도 정해봤다.

1. Spring Framework  version :  ex) 4.3.30 release
2. JDK version                            ex) 1.8
3. DB                                          ex) oracle 11g Express Edition
4. 서블릿/JSP version
5. Build Tool                               ex) Gradle, Maven
    1. 빌드 툴의 역할
        1. 외부 lib
        2. 컴파일
        3. 단위 테스트(습관화)
        4. 패키징(압축)
        5. 배포
6. 로킹                                       ex) logback
7. 형상관리(버전관리)               ex) Git / Github

# 스프링 프레임워크 개념 용어 정리

## POJO

Plain Old Java Object 의 줄인 말로 implements나 extends 없이 순수한 클래스로 이루어진 객체를 POJO라 부른다. **Spring 환경에 의존적이지 않다.** 그 이유는 상속이나 구현시 의존성이 발생하기 때문이다. 그 말은 특정 플랫폼에 종속 될 수 있음을 의미한다.

```java
public class Student {
    //............
}
```

## IOC

Inversion of Control 의 줄인 말로 우리말 표현으로는 제어의 역전을 의미한다. 제어의 역전?????

객체의 호출 작업을 개발자가 결정하는 것이 아닌  외부에서 결정되는 것을 말한다.

## Bean

빈 클래스를 의미

## IOC Container

- 빈의 생성부터 소멸까지 관리한다.
- 외부에서 빈을 생성하고 필요시 주입
- XXXApplicationContext가 보통 IOC 컨테이너이다.

![1472BAB2-937A-41E2-B8B7-41F0B79F44CB.jpeg](20220308(S%2024785/1472BAB2-937A-41E2-B8B7-41F0B79F44CB.jpeg)

## 스프링 프레임 워크 개발 방법

1. XML 이용: 빈등록, 주입 .... (Spring 2.x)
2. 어노테이션 + Spring Framework
3. 어노테이션 + Java Coding Configuration    (Spring 3.x, 스프링 부트 방식)

## AOP

Aspect Oriented Programming 의 줄인 말로 방법론 중에 하나이다. AOP를 한 문장으로 표현하자면 핵심기능에 필요시 부가기능을 주입해서 개발하는 방법론이라 볼 수 있다.

로직은 크게 2가지로 구분 할 수 있다. 첫 번째는 핵심기능 로직, 두 번째는 부가기능 로직이다.

예를 들어 살펴보자

쇼핑몰 사이트 프로젝트를 진행하는데 핵심기능과 부가기능을 정하려 한다.

핵심기능 : 로그인 처리, 장바구니 처리, 회원등록

부가기능: 로깅 처리, 보안 처리, 트랜잭션 처리

A class는 로그인 처리+ 로깅

B class는 장바구니 처리 + 트랜잭션 처리

c class는 회원등록 + 로깅

로깅이 중복된다. 이러면 여러 클래스를 만들 때마다 부가 기능이 계속 중복되게 되는 문제가 발생한다. 중복이 많아지면 유지보수가 어렵고 코드가 더러워진다. 이 때 이를 AOP 방법을 이용하면 쉽게 해결 할 수 있다. A, B, C 의 핵심 로직을 우선 모듈화한다. 모듈화란 하나의 로직을 수행하는 최소 단위로 이해하면 되겠다. 그리고 로깅, 트랜잭션, 보안 처리도 각각 모듈화를 수행하고 A,B,C에 끼워 넣어주기만 하면 된다. 끼워 넣어주기만 하면 되므로 관리도 쉽고 중복 코드도 없앨 수 있다.

...

## Spring 뷰

뷰를 생성하는 방법이 다양하다.

ex) JSP/Servlet, velocity, tbymeleaf(spring boot에서 사용, html) , ....pdf, excel...

사용하는 방법 ⇒ ViewResolver 빈

<aside>
💡 JSP/Servlet 전담 ViewResolver ⇒ IntervalResourceViewResolver()

</aside>

## 빈 간 서로 사용하려면??

외부에서 주입 ⇒ 의존성 주입  DI(Dependency Injection)

DI 방식에는 2 가지가 있다.

1. 생성자 주입 방법
2. 메서드로 주입 방법

## xml을 활용한 Spring Framework 실행 순서

1. 빈생성
2. xml 등록
3. IOC Container 내에 xml 인식
4. 빈 얻기