# 20220316(Spring MVC)

유형: SpringFramework
작성일시: 2022년 3월 16일 오전 9:21

# 의존성 확인

모든 프로젝트의 기본은 버전을 확인하는 것이다.

tomcat의 경우 지원하는 서블릿이나... jsp 지원에 제한이 있다. 예를 들면

servlet spec 이나 jsp spec 호환 가능한 버전이 제한 되어있는데 

자카르타 아파치에서 확인 할 수 있다.

![Untitled](20220316(spring-mvc)/1.png)

# Spring Bean Configuration

![Untitled](20220316(spring-mvc)/2.png)

# Spring MVC의 IOC 컨테이너 구성

웹 어플리케이션에서 동작하는 IOC 컨테이너는  2가지 종류로 나누어 진다.

웹과 웹이 아닌 부분을 분리 할 목적.... 하나만 사용해도 크게 상관은 없다.

1. 서블릿 어플리케이션 컨텍스트
    1. 스프링 어플리케이션의 요청을 처리하는 서블릿 안에서 생성.
2. 루트 어플리케이션 컨텍스트
    1. 웹 어플리케이션에서 생성
    2. 서블릿 레벨에 등록되는 컨테이너들의 부모 컨테이너가 된다.

![Untitled](20220316(spring-mvc)/3.png)

# Web.xml

Web.xml은 웹페이지의 환경 설정을 위한 파일이다. WAS가 최초로 구동 될 때  web.xml을 읽고 메모리에 로딩이된다.  

브라우저가 servlet에 접근하기 위해 WAS에 필요한 정보를 알려줘야 해당하는 Servlet을 호출 할 수 있는데 이를 정하는 곳이 web.xml이라고도 할 수 있을 것 같다.

SpringMVC에서 web.xml 역할

- DispatcherServlet
- ContextLoaderListener
- encodingFilter

Web.xml에 작성되는 내용

1. ServletContext의 초기 변수
2. 서블릿 및 jsp에 대한 정의 및 맵핑
3. MimeType매핑
4. Session의 대한 유효시간
5. welcome file list

## DispatcherServlet

클라이언트의 요청을 처리 할 DispatcherServlet을 등록한다.

```xml
<servlet-mapping>
		<servlet-name>appServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
```

기본적으로 xml에 서블릿 등록 할 때와 문법은 유사하고 url 패턴에 대해서 조금 분석을 해보면 / 는 항상 호출, 그리고 / 외에는 몇가지 패턴이 있다.

1. 디렉토리 패턴 : /xyz
2. 확장자 패턴: *.do

해석해 보면 URl 마지막에 url 패턴과 같은 요청이 있을 경우 appServlet 을 호출해라.... 라고 이해하면 된다.

## root-context

```xml
<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring/root-context.xml</param-value>
	</context-param>
```

보통 context-param에 root-context.xml 내용을 넣는다. 서블릿에 관한 내용은 servlet-context에 넣어준다.

## filter

```xml
<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```

필터는 jsp / 서블릿에서 xml 등록 할 때와 같다. 보통 utf-8로 인코딩을 하거나 스프링 보안 관련 내용을 작성 할 때 필터를 사용한다.

## Session-Config

```xml
<!-- Session 시간설정 (단위:분) -->
<session-config>
    <session-timeout>180</session-timeout>
</session-config>
```

세션에 대한 설정도 가능하다.

## Error Page

```xml
<error-page>
	<error-code>404</error-code>
	<location>/resources/commons/error/pageNotFound.jsp</location>
</error-page>
```

에러코드에 따라 에러페이지 출력을 설정 할 수 있다.

# MVC 기본 동작 과정

![Untitled](20220316(spring-mvc)/4.jpeg)

1. 클라이언트는 dispatcherServlet에게 요청을 한다.
2. url에 따라 handler mapping 은 그에 맞는 Controller를 리턴한다.
    1. Controller의 메서드에 requestMapping을 수행하면 handlerMapping에 등록이 되는데  dispatcher에서 url을 통해 요청하면  그에 맞는 controller를 리턴해준다.
3. Controller는 DAO로 부터 data를 받아오고 dispatcherServlet에게 전달한다.
4. 전달 받은 데이터를 ViewResolver 객체를 통해 뷰 기술에 맞게 변환하고 확장자를 결정 지어준다.
5. 데이터를 jsp에 위임하고 client에 보여준다.

# 여러개의 서블릿 컨텍스트 등록하기

web.xml

```xml
<!-- Processes application requests -->
	<servlet>
		<servlet-name>appServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>
			/WEB-INF/spring/appServlet/*-context.xml
			
			</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
```

<param-value> 부분을 보면

**/WEB-INF/spring/appServlet/*-context.xml** 으로 되어 있음을 알 수 있다.

해석 하자면**/WEB-INF/spring/appServlet 경로에** 임의의 이름-context.xml을 등록한다. 라고 이해 하면 될 것 같다.

이렇게 등록하면 spring/appServlet/~~~~.xml들을 모두 인식 할 수 있다.

![Untitled](20220316(spring-mvc)/5.png)

# InternalResourceViewResolver 빈 등록

appServlet/servlet-context.xml

```xml
<!--  jsp 처리 전담 -->
		<bean id="" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
			<property name="prefix" value="/WEB-INF/views/"/>
			<property name="suffix" value=".jsp"/>
		</bean>
```

# 다양한 @RequestMapping 패턴

```java
@Controller
public class TestController {

	@RequestMapping("/list")
	public String list() {
		System.out.println("요청:  /list");
		return "index";
	}

	@RequestMapping(value = "/list2")
	public String list2() {
		System.out.println("요청:  /list2");
		return "index";
	}

	@RequestMapping(value = { "/list3", "/list4" })
	public String list3() {
		System.out.println("요청:  /list3 /list4 ");
		return "index";
	}

	// update로 시작하는 요청맵핑값 지정
	@RequestMapping("/update*")
	public String list4() {
		System.out.println("요청:  /update*");
		return "index";
	}

	// del 경로 뒤에 임의의 경로 없거나 하나 지정 가능
	@RequestMapping("/del/*")
	public String list5() {
		System.out.println("요청:  /del/*");
		return "index";
	}

	// insert 경로 뒤에 임의의 경로 없거나 여러개 지정 가능
	@RequestMapping("/insert/**")
	public String list6() {
		System.out.println("요청:  /insert/**");
		return "index";
	}

	// sel 경로 뒤에 임의의 경로 없거나 여러개 지정 가능하고 xyz 끝나는 경로지정
	@RequestMapping("/sel/**/xyz")
	public String list7() {
		System.out.println("요청:  /sel/**/xyz");
		return "index";
	}
```

패턴은 **ANT Style Pattern**을 따른다. 

ANT 패턴은 다음과 같다.

- ?: 1개의 문자와 매칭
- *: 0개 이상의 문자와 매칭
    - 디렉토리 안에 문자열에 대해서만 적용
- **: 0개 이상의 디렉토리와 파일 매칭
    - / aa / bb  / 이와 같이 여러 디렉토리도 가능