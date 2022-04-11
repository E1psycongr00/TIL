# 20220321(file-upload)

유형: SpringFramework
작성일시: 2022년 3월 21일 오전 9:52

# 파일 업로드 Dependency

```xml
<!--  file Upload -->	
	<dependency>
	    <groupId>commons-fileupload</groupId>
	    <artifactId>commons-fileupload</artifactId>
	    <version>1.4</version>
	</dependency>
	<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
	<dependency>
	    <groupId>commons-io</groupId>
	    <artifactId>commons-io</artifactId>
	    <version>2.6</version>
	</dependency>
```

# servlet-context에 빈 등록

multipartResolver 라는 빈을 등록한다. 이 때 id는 변경하면 안된다.

```xml
<bean id="multipartResolver"
        class="org.springframework.web.multipart.commons.CommonsMultipartResolver">

    <!-- one of the properties available; the maximum file size in bytes -->
    <property name="maxUploadSize" value="100000"/>

</bean>
```

# 폼태그

```html
<form method="post" action="upload" enctype="multipart/form-data">
	    <input type="text" name="theName"/>
	    <input type="file" name="theFile"/>
	    <input type="submit" value="업로드"/>
</form>
```

# 컨트롤러

```java
@Controller
public class UploadController {
	
	@RequestMapping(value="/uploadForm", method=RequestMethod.GET)
	public String uploadForm() {
		return "uploadForm";
	}
	
	@RequestMapping(value="/upload", method=RequestMethod.POST)
	public String upload(UploadDTO dto) {
		String theName = dto.getTheName();
		CommonsMultipartFile theFile = dto.getTheFile();
		
		long size = theFile.getSize();
		String name = theFile.getName();
		String originalFilename = theFile.getOriginalFilename();
		byte[] bytes = theFile.getBytes();
		
		// 실제 폴더에 저장
		File savePath = new File("c://upload", originalFilename);
		
		try {
			theFile.transferTo(savePath);
		} catch (IllegalStateException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		
		return "uploadInfo";
	}
}
```

실제 폴더에 저장 할 때 path를 잘 설정해주면 된다.

# File Download

## 서블릿 추가

FileDownServlet.java

```java
public class FileDownServlet extends HttpServlet {

	public void doGet(HttpServletRequest request , HttpServletResponse response) throws IOException, ServletException {

	        String fileName = request.getParameter( "fileName" );

	        File fNew = new File("c:\\upload", fileName);
			String sFilePath = fNew.getPath();
		//	String sFilePath = sDownloadPath + fileName;

			byte b[] = new byte[4096];
			FileInputStream in = new FileInputStream(sFilePath);

			String sMimeType = getServletContext().getMimeType(sFilePath);
System.out.println("sMimeType>>>"+sMimeType );

			if(sMimeType == null) sMimeType = "application/octet-stream";

			response.setContentType(sMimeType);

			String sEncoding = new String(fileName.getBytes("UTF-8"),"8859_1");

			response.setHeader("Content-Disposition", "attachment; filename= " + sEncoding);
			
			ServletOutputStream out = response.getOutputStream();
			int numRead;

			while((numRead = in.read(b, 0, b.length)) != -1) {
				out.write(b, 0, numRead);
			}
			out.flush(); 
			out.close();
			in.close();

	}//end
}//end class
```

## WEB.xml에 서블릿 등록

```java
<!-- FileDownServlet 등록 -->
    <servlet>
		<servlet-name>FileDownServlet</servlet-name>
		<servlet-class>com.controller.FileDownServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>FileDownServlet</servlet-name>
		<url-pattern>/filedown</url-pattern>
	</servlet-mapping>
	<!-- FileDownServlet 등록 -->
```

url-pattern을 보면 filedown 이라는 요청맵핑이 들어오면 파일다운로드 서블릿이 실행되는 구조이다.

## jsp에서 사용하기

```html
파일명:<a href="filedown?fileName=${uploadDTO.theFile.originalFilename}">${uploadDTO.theFile.originalFilename}</a><br>
```

filedown이라는 요청매핑을 사용하고 쿼리스트링을 이용해 get방식으로 넘기고 있다.

fileName을 쿼리스트링으로 사용하는 이유는 앞서서 정의한 서블릿에서 getParameter로 ‘fileName’값을 받아와서 경로안의 파일네임과 일치하는 데이터를 가져오기 때문이다.