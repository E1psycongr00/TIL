# 20220317(Spring Cookie)

유형: SpringFramework
작성일시: 2022년 3월 17일 오후 9:15

# 쿠키

## 쿠키 세팅하기

```java
@RequestMapping("/set")
	public String header(HttpServletRequest request, HttpServletResponse response) {
		
		Cookie c = new Cookie("my", "hello");
		c.setMaxAge(60*60);
		response.addCookie(c);
		
		return "index";
	}
```

쿠키를 생성해서 추가 할 때는 spring api에는 따로 없고 servlet 방식을 이용해야 한다.

## 쿠키 값 받아오기(서블릿 방식)

```java
@RequestMapping("/get")
	public String header2(HttpServletRequest request, HttpServletResponse response) {
	
		Cookie [] cookies = request.getCookies();
		for (Cookie c : cookies) {
			if("my".equals(c.getName())) {
				System.out.println(c.getValue());
			}
		}
		return "index";
	}
```

## 쿠키 값 받아오기(스프링 방식)

```java
//2. Spring 버전
	@RequestMapping("/get2")
	public String header3(@CookieValue("my") String m) {
		System.out.println("<<" + m);
		return "index";
	}
```