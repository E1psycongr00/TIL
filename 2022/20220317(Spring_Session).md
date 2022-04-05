# 20220317(Spring_Session)

유형: SpringFramework
작성일시: 2022년 3월 17일 오후 10:32

# 스프링을 활용한 세션

## 로그인

### 로그인 jsp

![Untitled](20220317(Spring_Session)/Untitled.png)

```html
<h1>로그인 화면</h1>
<form action="login" method="post">
아이디:<input type="text" name="userid"><br>
비번:<input type="text" name="passwd"><br>
<input type="submit" value="로그인">
</form>
```

### 폼 등록시 세션에 등록

```java
@RequestMapping(value="/login", method = RequestMethod.POST)
	public String login(LoginDTO dto, HttpSession session ) {
		
		//유효성검사 체크 
		session.setAttribute("login", dto);
		return "login";
	}
```

서블릿에서 쓰던 HttpSession으로 세션에 값을 등록한다.

session.setAttribute(”login”, dto)

### 로그인 이후 로그 아웃

```java
@RequestMapping(value="/logout", method = RequestMethod.GET)
	public String logout(HttpSession session) {
		
		LoginDTO dto = (LoginDTO)session.getAttribute("login");
		String nextPage="";
		if(dto!=null) {
			session.invalidate();
			System.out.println("정상적으로 로그아웃됨.");
			nextPage = "index";
		}else {
			System.out.println("비정상적인 요청임. 다시로그인하세요.");
			nextPage ="loginForm";
		}
		return nextPage;
	}
```

session.invalidate()  ⇒ 세션을 삭제시킨다.

dto == null 이면 세션을 비우고 index.jsp로 이동하고

그렇지 않다면 login폼으로 들어간다.