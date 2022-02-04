# 20220203(json,객체분할할당,template)

작성일시: 2022년 2월 3일 오후 9:38

# 오늘 배운 것

## 사용자 정의 객체(JSON)

```jsx
var user = {
		username: "홍길동"
		age: 20
		// Getter Setter
		getUserName: function(){return this.username;}
		getAge: function() {return this.age;}
		setUserName: function(name) {this.username = name;}
		setAge: function(age) {this.age = age;}
};
```

json은 해쉬맵 같아 보이지만 다르다. getter setter 와 같은 메서드 또한 지정해서 사용가능하다.

```jsx
var user = {
		username,
		age:20,
		getUserName:()=>{
			return this.username;
		},
		getAge(){
			return this.age;
		},
		setUserName(name) {
			this.username = name;
		},
		setUserAge(age) {
			this.age = age;
		}
	}
```

이런 식으로 사용도 가능하다. 새롭게 js에 등장한 문법이다. 뭔가 자바와 비슷해진 문법이라는데 이게 난 더 헷갈린다. 뭔가 js 스럽지 않달까.....

## JSON은 Key를 변경 할 수 있다.

```jsx
var key_name = "sport";
var sports = {[key_name+"01"]:"야구", [key_name+"02"]:"농구"
```

결과: {sport01:”야구”, sport02: “농구”}

## String2JSON ↔ JSON2String

```jsx
var json_arr = [10, 20, 30];
var json_str = JSON.stringify(json_arr);
```

결과: “[10, 20, 30]”

```jsx
var json_object = {name:"홍길동",age:20};
var json_str = JSON.stringify(json_object);
// 결과 "{name:"홍길동",age:20}"
var json_object2 = JSON.parse(json_str);
// 결과 {name:"홍길동",age:20}
```

이걸 어디에 쓰지??? 서버와 클라이언트간에 데이터 교환시 ajax라는 것이 사용되는데 ajax는 html을 사용하는 것이 아니라 string으로 전송한다. 이때 string이 JSON 형태이기 때문에 parse해서 사용하면 된다.

## 객체 분해 할당

배열 응용 예시

```jsx
var arr = [100, 200, 300]
	var [n1, n2] = arr;
	var [, , n3] = arr;
	var[, ...rest] = arr;
	console.log(n1, n2, n3);
	console.log(rest);
```

JSON 응용예시

```jsx
var json = {name:"홍길동", age:20};
	console.log(json["name"], json["age"]);
	
	// 객체 할당
	
	var {name, age} = {name:"홍길동", age:20};
	
	console.log(name, age);
	var {name, age2} = {name:"홍길동", age:20};
	console.log(name, age2);
	
	var {name, age2=10} = {name:"홍길동", age:20};
	console.log(name, age2);
```

함수 파라미터 응용 예시

```jsx
// 이전 방식
	function f(n) {
		console.log(n[0], n[1]);
	}
	
	f([10, 20]);
	
	// 객체 분해 할당
	function f([n, n2]) {
		console.log(n, n2);
	}
	f([100, 200]);
	
	function f2([n1, n2, n3]) {
		console.log(n1, n2, n3);
	}
	f2([100, 200]);
	
	function f3([n1, n2,n3, ...rest]) {
		console.log(n1, n2, rest);
	}
	f3([100, 200, 300, 400, 100, 200]);
	
	function f4({name, age}) {
		console.log(name, age);
	}
	f4({name:"홍길동",age:13});
```

## Template 리터럴

```jsx
var username = "홍길동";
	var age = 20;
	 //1. table태그를 "" 로 만들기
	 var table = "<table><tr><th>이름</th>";
	     table += "<th>나이</th></tr>";
	     table += "<tr><td>"+username+"</td><td>"+age+"</td></tr></table>";
	 console.log(table);
	 
	 var username = "유관순";
	 var age = 28;
     var table = `
                 <table>
                    <tr>
                     <th>이름</th>
                     <th>나이</th>
                    </tr>
     				<tr>
     				 <td>${username}</td>
     				<td>${age}</td>
     				</tr>
     			   </table>
                   `;
      console.log(table);
```

**html이나 여러 줄 또는 변수를 포함해서 리턴 할 때 유용하다.**

개인적으로 파이썬의 f”{}~~~” 랑 조금 닮은 구석이 있다. 리터럴 문자열은 back_tick을 이용해 사용한다. back_tick은 1번 숫자키 왼쪽에 물결 키랑 작은 점이 찍혀있는데 작은 점이 back_tick이다.

# 오늘 느낀점

- JS 의 편리한 문법이 파이썬과 뭔가 많이 닮아 있는 것 같다. 객체 분해 할당과 template 리터럴 등이 그렇다.