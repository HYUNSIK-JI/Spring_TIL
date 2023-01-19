# MVC와 템플릿 엔진 와  API



* MVC: Model, View, Controller



## Controller

```java
@Controller
public class HelloController {
    
 @GetMapping("hello-mvc")
 public String helloMvc(@RequestParam("name") String name, Model model) {
     model.addAttribute("name", name);
     return "hello-template";
 }
}
```



## View

경로: resources/templates/hello-template.html

```html
<html xmlns:th="http://www.thymeleaf.org">
    <body>
        <p th:text="'hello ' + ${name}">hello! empty</p>
    </body>
</html>
```



## 실행

http://localhost:8080/hello-mvc?name=spring

![MVC,템플릿 엔진이미지](https://user-images.githubusercontent.com/59475851/213438431-99b2092e-198f-481b-a9bf-7a4e14edcf7a.png)


## API

@ResponseBody 문자 반환

```java
@Controller
public class HelloController {
    
    @GetMapping("hello-string")
    @ResponseBody
    public String helloString(@RequestParam("name") String name) {
        return "hello " + name;
    }
}
```



@ResponseBody 를 사용하면 뷰 리졸버( viewResolver )를 사용하지 않는다.

대신에 HTTP의 BODY에 문자 내용을 직접 반환(HTML BODY TAG를 말하는 것이 아님)



이는 프로퍼티 접근방식이라고도 하며 Getter, Setter를 사용하는 방식이다.



**localhost:8080/hello-api?name={name}을 입력해보면**

결과 => **Json 형식으로 출력이 된다.**


![API반환이미지](https://user-images.githubusercontent.com/59475851/213438466-6916114d-1a87-4e0c-aa25-ecc9651090f1.png)


**XML 방식이나, JSON 방식이 있는데 레거시 프로젝트가 아닌 이상 대부분 Json 방식을 쓴다.**



@ResponseBody 어노테이션이 달려있는 함수에 **객체**를 넘겨주면 HttpMessageConverter 중 JsonConverter가 선택되어 Json으로 화면에 출력이 된다.

![API 방식 이미지](https://user-images.githubusercontent.com/59475851/213438489-0447b4d4-02eb-4ffc-84c9-b48558dffd0a.png)

**@ResponseBody를 사용한다**

\- Http의 Body에 문자 내용을 직접 반환한다.

\- viewResolver 대신에 HttpMessageConverter가 동작한다.

\- 기본 문자 처리 : StringHttpMessageConverter

\- 기본 객체 처리 : MappingJackson2HttpMessageConverter

\- byte 처리 등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있다.





▪ 참고) 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 HttpMessageConverter가 선택된다.



출처 : 김영한님의 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 
