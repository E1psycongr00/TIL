# 20220321(Exception_Handler)

유형: SpringFramework
작성일시: 2022년 3월 21일 오후 8:42

# ExceptionHandler

에러 발생시 다루기 위한 이벤트 핸들러라고 이해하면 편할 듯 하다. error.jsp를 따로 만들어서 에러 발생시 핸들러에 의해서 자동으로 error.jsp를 호출하도록 할 수 있다.

## 사용하기

```java
@ExceptionHandler({ArithmeticException.class, IOException.class}) // 다중 캐치문
	public String errorPage() {
		return "error";
	}
```

@ExceptionHandler 의 중괄호 안에는 여러 에러타입들을 넣을 수 있다. 다중 캐치문이라 생각하면 이해하기 편하다.

# JSP의 내장 객체

a.jsp ⇒ a.java로 변환하면서 자동으로 등록되는 내장변수가 몇가지 있다.

1. request   
2. response  
3. session
4. application
5. out   :    출력
6. exception(선택)   : 에러 관련 객체
    1. 이 내장변수를 사용하려면 isErrorPage=”true” 로 설정해주어야 한다.
    
    error.jsp
    
    ```html
    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8" isErrorPage="true"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
    <h1>error.jsp</h1>
    ${exception}
    </body>
    </html>
    ```