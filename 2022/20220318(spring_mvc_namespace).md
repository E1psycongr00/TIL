# 20220318(Spring_mvc_namespace)

유형: SpringFramework
작성일시: 2022년 3월 18일 오후 10:48

# MVC namespace

ViewResolver를 등록하거나 단순히 URL을 통해 뷰로 접근하는 경우 mvc namespace를 이용해 쉽게 관리 할 수 있다.

```xml
<!--  mvc namespace 사용시 2가지 태그는 필수 -->
	<mvc:annotation-driven />
	<mvc:default-servlet-handler/>
```

MVC namespace 사용시 등록해야 할 2가지 태그이다.

**<mvc:annotation-driven />**

태그는 어노테이션 방식의 MVC를 사용 시 필요한 빈들을 자동으로 등록해주는 태그로 생각하면 된다.

1. RequestMappingHandlerMapping
2. RequestMappingHandlerAdapter
3. ExceptionHandlerExceptionResolver

**<mvc:default-servlet-handler/>**

default-servlet은 톰캣 서블릿이다. 만약 dispatcher servlet에서 처리 할 수 없는 정적 파일(js, css, html)과 같은 것들은 dispatcher서블릿이 인식하지 못하는 경우(dispatcherservlet 매핑을 /로 하면 ) tomcat servlet이 처리 할 수 있도록 해주는 핸들러라고 볼 수 있다. 

<aside>
💡 **<mvc:annotation-driven />
<mvc:default-servlet-handler/>
이 2가지는 mvc를 이용한다면 그냥 필수적으로 등록해놓자...**

</aside>

# view-controller

단순하게 요청매핑을 통해 뷰를 띄우고 싶을 때 사용한다.

**<mvc:view-controller path="/main" view-name="index"/>**

이 때 path는 요청매핑이고 view-name은 실제 접근할 jsp 이름이다.

위에 뷰 컨트롤러를 해석해보면 /main으로 접근하면 index.jsp를 호출하시오 라는 뜻이다.

# 참고

[22. Web MVC framework](https://docs.spring.io/spring-framework/docs/4.3.30.RELEASE/spring-framework-reference/html/mvc.html#mvc-config-enable)

[web.xml url-pattern / 와 /* 의 차이점](https://lng1982.tistory.com/97)