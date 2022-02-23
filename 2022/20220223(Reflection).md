# 20220223(Reflection_API,클래스특성접근)

작성일시: 2022년 2월 23일 오전 8:07

# 오늘 배운것

## Reflection API

자바는 소스코드를 컴파일하면서 .class 파일을 생성하는데  JVM이 이를 읽을 때 클래스로더에서 Bootstrap, Extension, application 3가지 정보를 메모리에 로드합니다.  이 때  .class 저장된 클래스 정보도 로드하는데 이 로드된 정보를 강력하게 활용 할 수 있는 API가 리플렉션 API 입니다.

**타입, 변수명, 파라미터 타입, 인스턴스 등 상세한 클래스 명세정보에 접근 할 수 있습니다.**

**클래스로더가 로드하는 3인방에 대해서**

- **Bootstrap ClassLoader**
    - jre의 lib폴더에 있는 [rt.jar](https://sas-study.tistory.com/rt.jar) 파일을 탐색 후 기본 자바 API 라이브러리를 로드합니다. 가장 최우선으로 로드
- **Extension ClassLoader**
    - jre의 lib 폴더에 있는 ext 폴더에 있는 모든 확장 코어 클래스파일들을 로드합니다.
- **Application ClassLoader**
    - Extension ClassLoader의 child이며 시스템 클래스로더(System ClassLoader)라고도 불립니다. 어플리케이션 레벨에 있는 클래스들을 로드합니다.  **사용자가 직접 정의한 클래스파일들을 로드합니다.**
    

## Class 정의

```java
class DTO {
    String a;
    int b;
    String c;
    String d;

    public DTO() {}
    public DTO(String a, int b, String c, String d) {
        this.a = a;
        this.b = b;
        this.c = c;
        this.d = d;
    }

    public String getA() {
        return a;
    }

    public void setA(String a) {
        this.a = a;
    }

    public int getB() {
        return b;
    }

    public void setB(int b) {
        this.b = b;
    }

    public String getC() {
        return c;
    }

    public void setC(String c) {
        this.c = c;
    }

    public String getD() {
        return d;
    }

    public void setD(String d) {
        this.d = d;
    }

    public void f() {
        System.out.println("F 함수");
    }

    public int g() {
        return 10;
    }
    

    @Override
    public String toString() {
        return "DTO{" +
                "a='" + a + '\'' +
                ", b=" + b +
                ", c='" + c + '\'' +
                ", d='" + d + '\'' +
                '}';
    }
}
```

이런 DTO가 있다고 가정하자... 공부를 위해 따로 f와 g라는 메서드도 별도로 추가했다. 

## Reflection API 를 사용하기 위한 준비

1. 직접 선언 방법

```java
// 직접 선언 방법
Class<DTO> dtoClass = DTO.class;
```

1. 인스턴스를 활용하기(getClass())

```java
// get class 선언 방법
DTO dto = new DTO();
Class<? extends DTO> dtoClassFromInstance = dto.getClass();
```

1. class.forName() 메소드로 호출하기

```java
try {
            //주로 JDBC 예제에서 많이 봄
            Class<?> dtoClassFromPackageString = Class.forName("org.example.dto");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
}
```

## Class<?> 을 활용한 필드 조회

**필드 정보 조회**

- (Field[]) getFields() : 클래스에 선언된  public 필드들 반환
- (Field) getField(String name) : name에 해당하는 필드를 반환 (없으면 NoSuchFieldException)
- (Field[]) getDeclaredFields() : 접근자 상관 없이 클래스에 선언된 모든 필드들 반환
- (Field) getDeclaredField(String name) : name에 해당하는 필드를 반환(없으면 NoSuchFieldException)
- 접근자의 상태를 무시하고 field에 접근하려면 field 객체에서 setAccessible(true)를 활용해 접근을 허용하게 해주면 된다.

```java
public class Main {
    public static void main(String[] args) throws Exception {
        // 1. 직접 정의
        Class<DTO> dtoClass = DTO.class;
        DTO dto = new DTO("1", 2, "3", "4");
        // 2. 접근자 상관 없이 field 값들 모두 불러와서 출력하기
        Field[] fields = dtoClass.getDeclaredFields();
        Arrays.stream(fields).forEach(System.out::println);

        // 3. for문을 이용해 field 정보 (네임, 값) 가져오기
        // ex) string xxx = 4, xxx는 네임, 4 는 값
        Arrays.stream(fields).forEach(field -> {
            field.setAccessible(true); // private 접근 허용시키기
            try {
                System.out.println("field 네임:  " + field.getName());
                System.out.println("field 값:  " + field.get(dto));
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        });

        // 4. dto 클래스의 인스턴스 변수 값 수정하기
        Field f =  dtoClass.getDeclaredField("a");
        f.set(dto, "10");
        System.out.println(dto);

    }
}
```

## 메소드 접근

```java
Method[] methods = bookClass.dtoMethods();
Arrays.stream(methods).forEach(System.out::println); //Object 상속 메소드 포함.
```

## 생성자 접근

```java
Constructor[] constructors = dtoClass.getConstructors();
Arrays.stream(constructors).forEach(System.out::println);
```

## 부모 클래스 접근

```java
Class<? super DTO> superClass = DTO.class.getSuperclass();
System.out.println(superClass);
```

## 인터페이스 접근

```java
Class<? super DTO> superClass = DTO.class.getInterfaces();
System.out.println(superClass);
```

## 오늘 느낀점

- reflection api 를 활용하면 서블릿에서 DTO 에서 DB로 넘길 때 뭔가 좀 더 쉽게 넘기기가 가능 할 것 같은 느낌이 들었다. 시도해 봐야겠다.

# 참고

[JVM 메모리 구조 및 아키텍쳐(클래스 로더, 실행엔진 등)](https://sas-study.tistory.com/262)