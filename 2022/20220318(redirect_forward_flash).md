# 20220318(redirect_forward_flash)

유형: SpringFramework
작성일시: 2022년 3월 18일 오후 5:14

# Forward

포워드는 위임하는 형태로 URL이 변경되지 않고 매핑에 접근한다.

기본적으로 String return하는 것은 포워드형식의 위임형태이지만 약간의 차이점이 있다면 return String을 통해 리턴하면 ViewResolver에 의해서 바로 jsp를 호출한다고 포워드는 요청매핑으로 접근하기 때문에 한번 더 거쳐간다는 차이점이 있다. 물론 리다이렉트도 포워드와 마찬가지다.

```java
// 3. forward ==> main으로 위임
		@RequestMapping("/forward")
		public String a3(Model model) {
			model.addAttribute("userid", "홍길동");
			// return "main"; // main.jsp로 바로감
			return "forward:main"; // requestMapping을 한번 더 거친다.
		}

// 4. target
		@RequestMapping("/main")
		public String main() {
			return "main";
		}
```

# Redirect

포워드처럼 요청매칭으로 접근한다는 방식은 같지만 차이점은 데이터를 가져오지 못한다. 그 이유는 요청을 끊고 재요청하기 때문에 URL은 달라지지만 데이터는 소멸하기 때문이다.

```java
 // 3. forward ==> main으로 위임
		@RequestMapping("/forward")
		public String a3(Model model) {
			model.addAttribute("userid", "홍길동");
			// return "main"; // main.jsp로 바로감
			return "forward:main"; // requestMapping을 한번 더 거친다.
		}
	
	// 4. target
		@RequestMapping("/main")
		public String main() {
			return "main";
		}
```

## 플래시스코프

```java
//https://docs.spring.io/spring-framework/docs/4.3.0.RELEASE/spring-framework-reference/html/mvc.html#mvc-flash-attributes
	//2. flash scope
	/*
	 *   1)일반적인 모델 사용하지 말고 RedirectAttributes 사용
	 *   2)활성화
	 *     <mvc:~ />
	 *     	<mvc:annotation-driven />
	        <mvc:default-servlet-handler/>
	 */
	@RequestMapping("/flash")
	public String a2(RedirectAttributes xxx) {
		xxx.addFlashAttribute("userid", "홍길동");
		return "redirect:main"; 
	}
	
	//3. target
	@RequestMapping("/main")
	public String main(HttpServletRequest request) {
		Map<String, ?> flashMap = RequestContextUtils.getInputFlashMap(request);
		String s = (String)flashMap.get("userid");
		System.out.println("/main 요청");
		System.out.println(s);
		return "main";
	}
```

스프링에서 추가된 스코프로 리다이렉트를 요청하더라도 한 번은 데이터가 유지된다.  그래서 지금 위의 코드를 실행하고 새로고침을 누르면 데이터가 날라가는데 실제로 위와 같은 방식의 프로그래밍은 서비스하는데 위험하다. 즉 flashScope를 이용해서 바로 뷰를 보여주면 서비스 장애가 생길 위험이 있다.

그래서 보통 PRG(Post-Redirect-Get) 패턴을 이용해 사용한다.

# PRG 패턴(POST-REDIRECT-GET)

post방식은 보통 DML을 이용해 DB를 추가, 수정하거나 삭제 할 때 사용한다. 이때 만약 어떤 조치도 없이 새로고침을 누른다면 계속해서 DML이 작동하는 치명적인 문제점이 있을 수 있다. 예를 들어 결제의 경우 결제페이지로 이동해서 결제를 했는데 새로고침하면 또 결제된다고 생각해봐라.... 얼마나 끔찍한가...... 코드를 통해 확인해보자

```java
@Controller
class TestController {

    @GetMapping
    public String main(){
        return "main";
    }

    @PostMapping("/pay")
    public String pay(){
        // 결제 로직
        return "success";
    }
}
```

main.html

```html
<!DOCTYPE html>
<html lang="en" >
<head>
    <meta charset="UTF-8">
</head>
<body>
<form action="/pay"  method="post">
    <button>결제</button>
</form>
</body>
</html>
```

success.html

```html
<!DOCTYPE html>
<html lang="en"  >
<head>
    <meta charset="UTF-8">
</head>
<body>
성공
</body>
</html>
```

url은  간략하게 요청맵핑으로 보여주는 걸로 하자

1. 브라우저가 /main 을 요청한다. (url: /main)
2. main 에서 폼태그 전송을 눌러 post 방식으로 /pay를 요청한다. (url: /main → /pay)
3. /pay에서 결제처리를 수행후 success.html로 위임한다.(url: /pay)

여기서 문제점은 /pay는 결제를 담당하는 요청매핑인데 보이는건 success지만 url은 /pay에 있다는 점이다. 이렇게 되면 새로고침을 누를 때 /pay를 한 번 더 호출 하는 문제점이 생긴다.

이제 PRG 패턴으로 이 문제점을 개선해보자.

**PRG 패턴 코드**

```java
@Controller
class TestController {

    @GetMapping
    public String main(){
        return "main";
    }

    @PostMapping("/pay")
    public String pay(){
        // 결제 로직
        return "redirect:/success";
    }

    @GetMapping("/success")
    public String success(){
        return "success";
    }
}
```

이전 코드랑 차이점은 post - redirect - get 요청을 수행한다는 점이다. 이 코드의 수행 과정을 살펴보자.

1. 브라우저에서 /main 을 호출한다. (url : /main)
2. 메인 폼에서 전송을 이용해 /pay로 이동한다. (url: /main → /pay )
3. 결제 로직을 수행후 /success로 리다이렉트로 이동한다.(url: /pay → /success)
4. suceess.html을 보여준다. (url: /success)