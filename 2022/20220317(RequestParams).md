# 20220317(RequestParams)

유형: SpringFramework
작성일시: 2022년 3월 17일 오후 3:21

# RequestMapping(데이터 받고 출력하기)

## 폼 태그 구성

login.jsp 

```html
<h1>로그인 화면</h1>
	<form action="login" method="post">
	아이디:<input type="text" name="userid">
	비번: <input type="text" name="passwd">
	<input type="submit" value = "로그인">
	</form>
```

![Untitled](20220317(Requestparams)/1.png)

아이디와 비번을 입력하고 누르면 userid : aa, passwd : bb  이런 값으로 폼을 통해 login 으로 post 전동하는 코드이다. 이 때 이 데이터를 받는법.... !!

## HttpServletRequest

```java
@RequestMapping(value="/login", method=RequestMethod.POST)
	public String login(HttpServletRequest request) {
		String userid = request.getParameter("userid");
		String passwd = request.getParameter("passwd");
		System.out.println(userid + " " + passwd);
		return "login";
	}
```

서블릿/jsp 에서 사용하던 request 방식을 이용해 값을 받는다.

**request.getParameter( key ) 를 이용해** key에 맵핑된 value값을 받아온다.

## @RequestParam

```java
@RequestMapping(value="/login", method=RequestMethod.POST)
	public String login(@RequestParam String userid, 
			@RequestParam String passwd) {
		System.out.println(userid + " " + passwd);
		
		return "login";
}

// 위 코드와 사실상 같은 코드
@RequestMapping(value="/login2", method=RequestMethod.POST)
	public String login(@RequestParam("userid") String userId, 
			@RequestParam("passwd") String passwd) {
		System.out.println(userId + " " + passwd);
		
		return "login";
	}
```

폼 태그 전송시 name과 똑같이 파라미터 네임을 맞추면서 받는 방법이다.

그러나 이 방법의 단점이 있다면 이런식으로 값을 가져오면 @RequestParam의 속성은 기본적으로 required이기 때문에 예외를 발생시킨다. 그래서 required 속성을 false로 바꾸어 줄  필요가 있다.

### @RequestParam(value=’ ‘, required=false)

```java
// requestParam이 있으면 필수적으로 폼에서 값을 넘겨줘야 한다.
	// 가능하게 만드는 방법은 required 속성을 false로 해주면 된다.
	@RequestMapping(value="/login", method=RequestMethod.POST)
	public String login(@RequestParam(value="userid", required = false, defaultValue = "기본값") String userId, 
			@RequestParam("passwd") String passwd) {
		System.out.println(userId + " " + passwd);
		return "login";
	}
```

required = false로 지정하면 해당하는 name 값이 없다면 null을 반환한다. 이 때 defaultValue로 null 대신 다른 값을 얻어 올 수도 있다.

## DTO를 이용해 받아오는 방법

```java
@RequestMapping(value="/login", method=RequestMethod.POST)
	public String login(LoginDTO dto) {
		System.out.println(dto);
		return "login";
	}
```

특별한 설정 필요 없이 DTO를 통해 받아 올 수 있다.  만약 DTO 인스턴스 변수 네임에 맞는 key 값이 없다면 null 또는 0으로 초기화 된다.

## Map을 이용해 받아오는 방법

```java
@RequestMapping(value="/login", method=RequestMethod.POST)
	public String login(@RequestParam Map<String, String> map) {
		System.out.println(map);
		
		return "login";
	}
```

DTO 설정이 귀찮다면 Map을 이용해서 한번에 받아 올 수 있다.

# 키 하나에 여러개의 Value가 있는 경우

체크 박스와 같이 동일 네임에 여러 value 값을 전송하는 경우 dto를 이용해 쉽게 받아 올 수 있다.

```java
// getter setter를 써주어야 한다.
public class LoginDTO {

	String userid;
	String passwd;
	
	String [] email;
	List<String> phone;
}
```

getter / setter를 쓰면 코드가 길어져서 가독성을 위해 생략 했다. 여러 값을 받아와야 하는 경우 배열 또는 컬렉션 리스트로 선언해주면 쉽게 해결 할 수 있다.

```java
@RequestMapping(value="/login", method = RequestMethod.POST)
	public String login(LoginDTO dto) {
		System.out.println("###" + dto);
		return "login";
	}
```