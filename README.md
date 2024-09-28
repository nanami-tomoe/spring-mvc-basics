# Spring MVC - 기본 기능

## 프로젝트 생성

- packaging은 jar로 설정
  - JSP를 사용하지 않기 때문에 jar로 설정
  - JSP를 사용하려면 war로 설정
  - JSP를 사용하지 않는 이유는 스프링 부트에서 권장하지 않기 때문
  - JSP 대신 Thymeleaf를 사용
  - Jar를 사용하면 항상 내장 서버(톰캣등)를 사용하고, `webapp` 경로도 사용하지 않습니다. 내장 서버 사용에 최적 화 되어 있는 기능
  - 최근에는 주로 이 방식을 사용
  - War를 사용하면 내장 서버도 사용가능 하지만, 주로 외부 서버에 배포하는 목적으로 사용

### [Welcome 페이지 만들기](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/resources/static/index.html)

- 스프링 부트에 `Jar` 를 사용하면 `/resources/static/` 위치에 `index.html` 파일을 두면 Welcome 페이지로 처리해준다.
- 스프링 부트가 지원하는 정적 컨텐츠 위치에 `/index.html` 이 있으면 된다.

> **참고** <br>
> 스프링 부트 Welcome 페이지 지원
> [https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot- features.html\#boot-features-spring-mvc-welcome-page]()

## 로깅 간단히 알아보기

### 로깅 라이브러리

- 인터페이스: `SKF4J`
- 구현체: `Logback`

### 로그 선언

- `private Logger log = LoggerFactory.getLogger(getClass());`
- `private static final Logger log = LoggerFactory.getLogger(Xxx.class)`
- `@Slf4j` : 롬복 사용 가능

### 로그 호출

- `log.info("hello")`

### 테스트 [LogTestController](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/LogTestController.java)

- 로그가 출력되는 포멧 확인
  - 시간, 로그 레벨, 프로세스 ID, 쓰레드 명, 클래스명, 로그 메시지
- 로그 레벨 설정을 변경해서 출력 결과 보기
  - LEVEL: `TRACE > DEBUG > INFO > WARN > ERROR`
  - 개발 서버는 `DEBUG` 레벨로 설정
  - 운영 서버는 `INFO` 레벨로 설정
- @Slf4j 사용
  - `private static final Logger log = LoggerFactory.getLogger(Xxx.class)` 대신 `@Slf4j` 사용
  - 롬복이 제공하는 기능
  - `private static final Logger log = LoggerFactory.getLogger(Xxx.class)` 코드를 자동으로 생성해준다.
  - 롬복을 사용하면 `private static final Logger log = LoggerFactory.getLogger(Xxx.class)` 코드를 자동으로 생성해준다.

### [로그 레벨 설정](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/resources/application.properties)

### 올바른 로그 사용법

- `log.debug("data="+data);`
  - 로그 출력 레벨을 info로 설정해도 해당 코드는 여전히 남아 있음
- `log.debug("data=", data);`
  - 로그 출력 레벨을 info로 설정하면 해당 코드는 더 이상 로그 메시지를 생성하지 않음

### 로그 사용시 장점

- 쓰레드 정보, 클래스 이름 같은 부가 정보를 함께 볼 수 있고, 출력 모양을 조정할 수 있다.
- 로그 레벨에 따라 개발 서버에서는 모든 로그를 출력하고, 운영 서버에서는 출력하지 않는 등 로그를 상황에 맞게 조절할 수 있다.
  - 코드는 수정하지 않고 설정만 바꾸면 로그 레벨 변경 가능
- 시스템 아웃 콘솔에 출력하는 것이 아니라 파일이나 네트워크 등 로그를 별도의 위치에 남길 수 있다.
  - 특히 파일로 남길 때는 일별, 특정 용량에 따라 로그를 분할하는 것도 가능하다.
- 성능도 일단 System.out보다 좋다.(내부 버퍼링, 멀티 쓰레드 등등) 실무에서는 꼭 로그 사용!

### 더 공부하실 분

- 로그에 대해서 더 자세한 내용은 slf4j, logback을 검색해보자.
  - SLF4J - http://www.slf4j.org
  - Logback - http://logback.qos.ch
- 스프링 부트가 제공하는 로그 기능은 다음을 참고하자.
  - https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-logging

## 요청 매핑

### [MappingController](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/requestmapping/MappingController.java)

- 위 클래스 참고

### 매핑 정보

- `@RestController`
  - `@Controller` 는 반환 값이 `String` 이면 뷰 이름으로 인식된다. 그래서 **뷰를 찾고 뷰가 랜더링** 된다.
  - `@RestController` 는 반환 값으로 뷰를 찾는 것이 아니라, **HTTP 메시지 바디에 바로 입력**한다. 따라서 실행 결과로 ok 메세지를 받을 수 있다. `@ResponseBody` 와 관련이 있는데, 뒤에서 더 자세히 설명한다. `@RequestMapping("/hello-basic")`
- `/hello-basic` URL 호출이 오면 이 메서드가 실행되도록 매핑한다.
  - 대부분의 속성을 `배열[]` 로 제공하므로 다중 설정이 가능하다. `{"/hello-basic", "/hello-go"}`

### 스프링 부트 3.0 이전과 이후

- **이전**
  - 매핑: `/hello-basic`
  - URL 요청: `/hello-basic` , `/hello-basic/`
- **이후**
  - 매핑: `/hello-basic` -> URL요청: `/hello-basic`
  - 매핑: `/hello-basic/` -> URL요청: `/hello-basic/`
- **정리**: 이전에는 슬래시를 끝에 붙이지 않아도 처리가 되었지만, 이후에는 정확하게 매핑해야 한다.

### @PathVariable(경로 변수) 사용

- 최근 HTTP API는 다음과 같이 리소스 경로에 식별자를 넣는 스타일을 선호한다.
  - `/mapping/userA`
  - `/users/1`
- `@RequestMapping` 은 URL 경로를 템플릿화 할 수 있는데, `@PathVariable` 을 사용하면 매칭 되는 부분을 편리하게 조회할 수 있다.
- `@PathVariable` 의 이름과 파라미터 이름이 같으면 생략할 수 있다.
- 다중 매핑도 가능하다.

### 요청 매핑 예시

- [MappingController](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/requestmapping/MappingController.java)
  - 위 클래스 참고
- 회원 관리 API
  - 회원 목록 조회: GET `/users`
  - 회원 등록: POST `/users`
  - 회원 조회: GET `/users/{userId}`
  - 회원 수정: PATCH `/users/{userId}`
  - 회원 삭제: DELETE `/users/{userId}`

## HTTP 요청

> **HTTP 요청은 다음과 같이 분류할 수 있다.**
>
> - 기본 헤더 조회
> - 요청 데이터 조회
>   - 요청 파라미터
>     - **GET - 쿼리 파라미터**
>     - **POST - HTML Form**
>   - 요청 메세지 (body)
>     - **HTTP message body**에 데이터를 직접 담아서 요청

### HTTP 요청 - 기본, 헤더 조회

[**RequestHeaderController**](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/request/RequestHeaderController.java)

```java
@RequestMapping("/headers")
public String headers(HttpServletRequest request,
                      HttpServletResponse response,
                      HttpMethod httpMethod,
                      Locale locale,
                      @RequestHeader MultiValueMap<String, String> headerMap,
                      @RequestHeader("host") String host,
                      @CookieValue(value = "myCookie", required = false) String cookie
) {

  log.info("request={}", request);
  log.info("response={}", response);
  log.info("httpMethod={}", httpMethod);
  log.info("locale={}", locale);
  log.info("headerMap={}", headerMap);
  log.info("header host={}", host);
  log.info("myCookie={}", cookie);

  return "ok";
}
```

- `HttpServletRequest`
- `HttpServletResponse`
- `HttpMethod` : HTTP 메서드를 조회한다. `org.springframework.http.HttpMethod`
- `Locale` : Locale 정보를 조회한다.
  - 사용자 인터페이스(UI)에서 사용되는 언어(ISO 639-1 codes 표준 형식), 지역 설정(ISO 3166-1 표준 형식), 출력 방식(Character Set 또는 ISO 8859-1, UTF-8 등의 인코딩 식별자) 등을 정의하는 문자열이다
  - 형식: `language[_territory][.codeset][@modifier]`
  - 예시: `en`, `en_US`, `en_US.UTF-8`, `ko_KR.euc-kr`
- `@RequestHeader MultiValueMap<String, String> headerMap`
  - 모든 HTTP 헤더를 MultiValueMap 형식으로 조회한다.
- `@RequestHeader("host") String host`
  - 특정 HTTP 헤더를 조회한다.
  - 속성
    - 필수 값 여부: `required`
    - 기본 값 속성: `defaultValue`
- `@CookieValue(value = "myCookie", required = false) String cookie`
  - 특정 쿠키를 조회한다.
  - 속성
    - 필수 값 여부: `required`
    - 기본 값: `defaultValue`
- `MultiValueMap`
  - Map과 유사한데, 하나의 키에 여러 값을 받을 수 있다.
  - HTTP header, HTTP 쿼리 파라미터와 같이 하나의 키에 여러 값을 받을 때 사용한다.
    - **keyA=value1&keyA=value2**

```java
MultiValueMap<String, String> map = new LinkedMultiValueMap();
map.add("keyA", "value1");
map.add("keyA", "value2");
//[value1,value2]
List<String> values = map.get("keyA");
```

> **참고** <br>
> `@Controller` 의 사용 가능한 파라미터 목록은 다음 공식 메뉴얼에서 확인할 수 있다. <br>
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments

> **참고** <br>
> `@Controller` 의 사용 가능한 응답 값 목록은 다음 공식 메뉴얼에서 확인할 수 있다. <br>
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types

### HTTP 요청 데이터 - 개요

**클라이언트에서 서버로 요청 데이터를 전달할 때는 주로 다음 3가지 방법을 사용한다.**

- **GET - 쿼리 파라미터**
  - /url**?username=hello&age=20**
  - 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
  - 예) 검색, 필터, 페이징등에서 많이 사용하는 방식
- **POST - HTML Form**
  - content-type: application/x-www-form-urlencoded
  - 메시지 바디에 쿼리 파리미터 형식으로 전달 username=hello&age=20 예) 회원 가입, 상품 주문, HTML Form 사용
- **HTTP message body**에 데이터를 직접 담아서 요청
  - HTTP API에서 주로 사용, JSON, XML, TEXT
  - 데이터 형식은 주로 JSON 사용
  - POST, PUT, PATCH

### HTTP 요청 파라미터 - 쿼리 파라미터, HTML Form

- **GET, 쿼리 파라미터 전송**

  - `http://localhost:8080/request-param?username=hello&age=20`
- **POST, HTML Form 전송**

  - ```
    POST /request-param ...
    content-type: application/x-www-form-urlencoded

    username=hello&age=20
    ```
- GET 쿼리 파리미터 전송 방식이든, POST HTML Form 전송 방식이든 둘다 형식이 같으므로 구분없이 조회할 수 있다.
- 이것을 간단히 **요청 파라미터(request parameter) 조회**라 한다.

[**RequestParamController**](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/request/RequestParamController.java)

```java
    @RequestMapping("/request-param-v1")
    public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        log.info("username={}, age={}", username, age);

        response.getWriter().write("ok");
    }
```

- **request.getParameter()**
  - 여기서는 단순히 HttpServletRequest가 제공하는 방식으로 요청 파라미터를 조회했다.
- [**Post Form 페이지 생성**](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/resources/static/basic/hello-form.html)
  - 테스트용 HTML Form 생성
  - 리소스는 `/resources/static` 아래에 두면 스프링 부트가 자동으로 인식한다.

> **참고** <br>
> `Jar` 를 사용하면 `webapp` 경로를 사용할 수 없다. 이제부터 정적 리소스도 클래스 경로에 함께 포함해야 한다.

### HTTP 요청 파라미터 - @RequestParam

**requestParamV2**

```java
    @ResponseBody // RestController와 같은 역할
    @RequestMapping("/request-param-v2")
    public String requestParamV2(@RequestParam("username") String memberName,
                                 @RequestParam("age") int memberAge) {

        log.info("username={}, age={}", memberName, memberAge);
        return "ok";
    }
```

- `@RequestParam` : 파라미터 이름으로 바인딩
- `@ResponseBody` : View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력

**@RequestParam**의 `name(value)` 속성이 파라미터 이름으로 사용

- `@RequestParam("username") String **memberName**`
- -> `request.getParameter(*username")` 과 동일하게 동작

**requestParamV3** - `실무에서 가장 많이 쓰는 방법`

```java
    @ResponseBody // RestController와 같은 역할
    @RequestMapping("/request-param-v3")
    public String requestParamV3(@RequestParam String username,
                                 @RequestParam int age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }
```

- HTTP 파라미터 이름이 변수 이름과 같으면 `@RequestParam(name="xx")` 생략 가능

**requestParamV4**

```java
    @ResponseBody
    @RequestMapping("/request-param-v4")
    public String requestParamV4(String username, int age) { // 파라미터와 이름이 같으면 @RequestParam 생략 가능
        log.info("username={}, age={}", username, age);
        return "ok";
    }
```

- `String` , `int` , `Integer` 등의 단순 타입이면 `@RequestParam` 도 생략 가능

> **주의** <br>
> `@RequestParam` 애노테이션을 생략하면 스프링 MVC는 내부에서 `required=false` 를 적용한다. `required` 옵션은 바로 다음에 설명한다.

> **참고** <br>
> 이렇게 애노테이션을 완전히 생략해도 되는데, 너무 없는 것도 약간 과하다는 주관적 생각이 있다. <br>
> `@RequestParam` 이 있으면 명확하게 요청 파리미터에서 데이터를 읽는 다는 것을 알 수 있다.

**파라미터 필수 여부 - requestParamRequired**

```java
    /**
     * @RequestParam.required
     * /request-param-required -> username이 없으므로 예외 *
     * 주의!
     * /request-param-required?username= -> 빈문자로 통과 *
     * 주의!
     * /request-param-required
     * int age -> null을 int에 입력하는 것은 불가능, 따라서 Integer 변경해야 함(또는 다음에 나오는
    defaultValue 사용) */
    @ResponseBody
    @RequestMapping("/request-param-required")
    public String requestParamRequired(
            @RequestParam(required = true) String username,
            @RequestParam(required = false) Integer age) {
        log.info("username={}, age={}", username, age);
        return "ok";
    }
```

- `@RequestParam.required`
  - 파라미터 필수 여부
  - 기본값이 파라미터 필수(`true`)이다.
- 필수 파라미터 없어 요청하면 `400` 예외가 발생한다.

> **주의! - 파라미터 이름만 사용** <br>
> `/request-param-required?username=` 요청
>
> - 파라미터 이름만 있고 값이 없는 경우 빈문자로 통과
> - `null`과 `""`는 다른 의미 -> `null`은 값이 없음을 의미, `""`는 값이 있는 것으로 인식

> **주의! - 기본형(primitive)에 null 입력** <br>
> `/request-param` 요청
>
> - `@RequestParam(required = false) int age`
> - `null` 을 `int` 에 입력하는 것은 불가능(`500` 예외 발생)
> - 따라서 `age`에 값을 안넣어도 되게 설정하려면 `null` 을 받을 수 있는 `Integer` 로 변경하거나, 또는 다음에 나오는 `defaultValue` 사용

**기본 값 적용 - requestParamDefault**

```java
    /**
     * @RequestParam
     * - defaultValue 사용 *
     * 참고: defaultValue는 빈 문자의 경우에도 적용 * /request-param-default?username=
     */
    @ResponseBody
    @RequestMapping("/request-param-default")
    public String requestParamDefault(
            @RequestParam(required = true, defaultValue = "guest") String username,
            @RequestParam(required = false, defaultValue = "-1") int age) {
        log.info("username={}, age={}", username, age);
        return "ok";
    }
```

- 파라미터에 값이 없는 경우 `defaultValue` 를 사용하면 기본 값을 적용할 수 있다.
- 이미 기본 값이 있기 때문에 `required` 는 의미가 없다.
- `defaultValue` 는 빈 문자의 경우에도 설정한 기본 값이 적용된다.
- `/request-param-default?username=`

**파라미터를 Map으로 조회하기 - requestParamMap**

```java
    /**
 * @RequestParam Map, MultiValueMap
 * Map(key=value)
 * MultiValueMap(key=[value1, value2, ...]) ex) (key=userIds, value=[id1, id2])
 */
@ResponseBody
@RequestMapping("/request-param-map")
public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
  log.info("username={}, age={}", paramMap.get("username"),
          paramMap.get("age"));
  return "ok";
}
```

- 파라미터를 Map, MultiValueMap으로 조회할 수 있다.
- `@RequestParam Map` ,
  - `Map(key=value)`
- `@RequestParam MultiValueMap`
  - `MultiValueMap(key=[value1, value2, ...] ex) (key=userIds, value=[id1, id2])`
  - 하나의 키에 여러 값을 수 있다.
  - ex) `userIds=id1&id2`
- 파라미터의 값이 1개가 확실하다면 `Map` 을 사용해도 되지만, 그렇지 않다면 `MultiValueMap` 을 사용하자.

> **💡실무에서는?**
>
> - 보통 파라미터는 하나를 쓴다.
> - 애매하게 두개 이상 쓰는 경우는 많지 않다.

### HTTP 요청 파라미터 - @ModelAttribute

- 실제 개발을 하면 요청 파라미터를 받아서 필요한 객체를 만들고 그 객체에 값을 넣어주어야 한다.
- 스프링은 이 과정을 완전히 자동화해주는 `@ModelAttribute` 기능을 제공한다.

**바인딩 받을 객체 - HelloData**

```java
package hello.springmvc.basic;
import lombok.Data;
@Data
public class HelloData {
private String username;
private int age;
}
```

**@ModelAttribute 적용 - modelAttributeV1**

```java
    /**
     * @ModelAttribute 사용
     * 참고: model.addAttribute(helloData) 코드도 함께 자동 적용됨, 뒤에 model을 설명할 때 자세히
    설명
     */
    @ResponseBody
    @RequestMapping("/model-attribute-v1")
    public String modelAttributeV1(@ModelAttribute HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(),
                helloData.getAge());
        return "ok";
    }
```

- `HelloData` 객체가 생성되고, 요청 파라미터의 값도 모두 들어가 있다.
- 스프링MVC는 `@ModelAttribute`가 있으면 다음을 실행한다.
  - `HelloData` 객체를 생성
  - 요청 파라미터의 이름으로 `HelloData` 객체의 프로퍼티를 찾고 해당 프로퍼티의 setter를 호출해서 파라미터의 값을 입력(바인딩) 한다.
  - 예) 파라미터의 이름이 username이면 `setUsername()` 메서드를 호출하면서 값을 입력한다.

**프로퍼티**

- 객체에 `getUsername()` , `setUsername()` 메서드가 있으면, 이 객체는 `username` 이라는 프로퍼티를 가지고 있 다.
- `username` 프로퍼티의 값을 변경하면 `setUsername()` 이 호출되고, 조회하면 `getUsername()` 이 호출된다. ```

```java
class HelloData {
getUsername();
setUsername();
}
```

**바인딩 오류**

- `age=abc` 처럼 숫자가 들어가야 할 곳에 문자를 넣으면 `BindException` 이 발생한다. 이런 바인딩 오류를 처리하는 방법은 검증 부분에서 다룬다.

**@ModelAttribute 생략 - modelAttributeV2**

```java
    /**
     * @ModelAttribute 생략 가능
     * String, int 같은 단순 타입 = @RequestParam
     * argument resolver 로 지정해둔 타입 외 = @ModelAttribute */
    @ResponseBody
    @RequestMapping("/model-attribute-v2")
    public String modelAttributeV2(HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(),
                helloData.getAge());
        return "ok";
    }
```

- `@ModelAttribute`는 생략할 수 있다.
- 그런데 `@RequestParam`도 생랼할 수 있으니 혼란이 발생할 수 있다.
- 위와 같은 문제에서 스프링은 다음 규칙을 적용
  - `Stringg`, `int`, `Integer` 같은 단순 타입 = @RequestParam
  - 나머지 = `@ModelAttribute` (argument resolver 로 지정해준 타입 외)
    - `argument resolver`는 뒤에서 설명한다.

> **✅️ 정리** <br>
> 지금까지 한 것은 HTTP 요청 방법 중
>
> 1. GET - 쿼리 파라미터
> 2. POST = HTML Form
> 3. HTTP message body에 데이터를 직접 담아서 요청
>
> 위 3가지 중 1, 2번인 요청 파라미터를 알아본 것이고 아래부터는 3번인 HTTP message body에 데이터를 직접 담아서 요청하는 방법을 알아본다.

### HTTP 요청 메시지 - 단순 텍스트

- **HTTP message body**에 데이터를 직접 담아서 요청
  - HTTP API에서 주로 사용, JSON, XML, TEXT
  - **데이터 형식은 주로 JSON 사용**
  - POST, PUT, PATCH
- 요청 파라미터와 다르게, HTTP 메시지 바디를 통해 데이터가 직접 넘어오는 경우는 `@RequestParam` , `@ModelAttribute` 를 사용할 수 없다. (물론 HTML Form 형식으로 전달되는 경우는 요청 파라미터로 인정된다.)
- 먼저 가장 단순한 텍스트 메시지를 HTTP 메시지 바디에 담아서 전송하고, 읽어보자.
- HTTP 메시지 바디의 데이터를 `InputStream` 을 사용해서 직접 읽을 수 있다.
  - 스프링MVC에서는 `@RequestBody` 를 사용하면 HTTP 메시지 바디 정보를 편리하게 조회할 수 있다.

[**RequestBodyStringController**](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/request/RequestBodyStringController.java)

- 위 클래스 참고

**HttpEntity - requestBodyStringV3**

```java
/**
 * HttpEntity: HTTP header, body 정보를 편리하게 조회
 * - 메시지 바디 정보를 직접 조회(@RequestParam X, @ModelAttribute X)
 * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용 *
 * 응답에서도 HttpEntity 사용 가능
 * - 메시지 바디 정보 직접 반환(view 조회X)
 * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
 */
@PostMapping("/request-body-string-v3")
public HttpEntity<String> requestBodyStringV3(HttpEntity<String> httpEntity) {
  String messageBody = httpEntity.getBody();
  log.info("messageBody={}", messageBody);
  return new HttpEntity<>("ok");
}
```

- **스프링 MVC는 다음 파라미터를 지원한다.**

  - **HttpEntity**: HTTP header, body 정보를 편리하게 조회
    - 메시지 바디 정보를 직접 조회
    - 요청 파라미터를 조회하는 기능과 관계 없음 `@RequestParam` X, `@ModelAttribute` X
  - **HttpEntity는 응답에도 사용 가능**
    - 메시지 바디 정보 직접 반환
    - **헤더 정보 포함 가능**
    - view 조회X
- `HttpEntity` 를 상속받은 다음 객체들도 같은 기능을 제공한다.

  - **RequestEntity**
    - HttpMethod, url 정보가 추가, 요청에서 사용
  - **ResponseEntity**
    - **HTTP 상태 코드 설정 가능**, 응답에서 사용
    - `return new ResponseEntity<String>("Hello World", responseHeaders, HttpStatus.CREATED)`

> **참고** <br>
> 스프링MVC 내부에서 HTTP 메시지 바디를 읽어서 문자나 객체로 변환해서 전달해주는데, 이때 HTTP 메시지 컨버터( `HttpMessageConverter` )라는 기능을 사용한다. 이것은 조금 뒤에 HTTP 메시지 컨버터에서 자세히 설명한다.

**@RequestBody - requestBodyStringV4**  - `실무에서 가장 많이 사용`

```java
/**
 * @RequestBody
 * - 메시지 바디 정보를 직접 조회(@RequestParam X, @ModelAttribute X)
 * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용 *
 * @ResponseBody
 * - 메시지 바디 정보 직접 반환(view 조회X)
 * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
 */
@ResponseBody
@PostMapping("/request-body-string-v4")
public String requestBodyStringV4(@RequestBody String messageBody) {
  log.info("messageBody={}", messageBody);
  return "ok";
}
```

- **@RequestBody**

  - `@RequestBody` 를 사용하면 HTTP 메시지 바디 정보를 편리하게 조회할 수 있다.
  - 참고로 헤더 정보가 필요하다면 `HttpEntity` 를 사용하거나 `@RequestHeader` 를 사용하면 된다.
  - **이렇게 메시지 바디를 직접 조회하는 기능은 요청 파라미터를 조회하는 `@RequestParam` , `@ModelAttribute` 와 는 전혀 관계가 없다.**
    - HTTP 요청에서 파라미터와 바디는 다름!
- **요청 파라미터 vs HTTP 메시지 바디**

  - 요청 파라미터를 조회하는 기능: `@RequestParam` , `@ModelAttribute`
  - HTTP 메시지 바디를 직접 조회하는 기능: `@RequestBody`
- **@ResponseBody**

  - `@ResponseBody` 를 사용하면 응답 결과를 HTTP 메시지 바디에 직접 담아서 전달할 수 있다.
  - 물론 이 경우에도 view를 사용하지 않는다.
  - HTTP 응답 편에서 자세히 설명

### HTTP 요청 메시지 - JSON

- JSON 데이터 형식은 HTTP API에서 주로 사용

[**RequestBodyJsonController**](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/request/RequestBodyJsonController.java)

- 위 클래스 참고
- `{"username":"hello", "age":20}` -> 요청 JSON 데이터
- `content-type: application/json` -> JSON 데이터 content-type

**requestBodyJsonV3 - @RequestBody 객체 변환**

```java
/**
 * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
 * HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter (content-type:application/json)
 */
@ResponseBody
@PostMapping("/request-body-json-v3")
public String requestBodyJsonV3(@RequestBody HelloData data) {
  log.info("username={}, age={}", data.getUsername(), data.getAge());
  return "ok";
}
```

- **@RequestBody 객체 파라미터**
  - `@RequestBody HelloData data`
  - `@RequestBody` 에 직접 만든 객체를 지정할 수 있다.


- `HttpEntity` , `@RequestBody` 를 사용하면 HTTP 메시지 컨버터가 HTTP 메시지 바디의 내용을 우리가 원하는 문자나 객체 등으로 변환해준다.
- HTTP 메시지 컨버터는 문자 뿐만 아니라 JSON도 객체로 변환해주는데, 서블릿 방식에서 했던 작업을 대신 처리 해준다.
- 자세한 내용은 뒤에 HTTP 메시지 컨버터에서 다룬다.
- **@RequestBody는 생략 불가능**


  - `@ModelAttribute` 에서 학습한 내용을 떠올려보자.
  - 스프링은 `@ModelAttribute` , `@RequestParam` 과 같은 해당 애노테이션을 생략시 다음과 같은 규칙을 적용한다.
    - `String` , `int` , `Integer` 같은 단순 타입 = `@RequestParam`
    - 나머지 = `@ModelAttribute` (argument resolver 로 지정해둔 타입 외)
  - 따라서 이 경우 HelloData에 `@RequestBody` 를 생략하면 `@ModelAttribute` 가 적용되어버린다.
    - `HelloData data` -> `@ModelAttribute HelloData data`
  - 따라서 생략하면 HTTP 메시지 바디가 아니라 요청 파라미터를 처리하게 된다.

## HTTP 응답

> **HTTP 요청과 마찬가지로 스프링(서버)에서 응답 데이터를 만드는 방식은 크게 3가지이다.**
> 
> - 정적 리소스
>   - 예) 웹 브라우저에 정적인 HTML, css, js를 제공할 때는, **정적 리소스**를 사용한다. 
> - 뷰 템플릿 사용
>   - 예) 웹 브라우저에 동적인 HTML을 제공할 때는 뷰 템플릿을 사용한다. 
> - HTTP 메시지 사용
>   - HTTP API를 제공하는 경우에는 HTML이 아니라 데이터를 전달해야 하므로, HTTP 메시지 바디에 JSON 같은 형식으로 데이터를 실어 보낸다.

### HTTP 응답 - 정적 리소스
- 스프링 부트는 클래스패스의 다음 디렉토리에 있는 정적 리소스를 제공한다. 
  - `/static` , `/public` , `/resources` ,`/META-INF/resources`
  - 위 이름들 하나를 골라서 사용
  - 보통 프로젝트를 생성하면 `/static`이 기본


- `src/main/resources` 는 리소스를 보관하는 곳이고, 또 클래스패스의 시작 경로이다. 
- 따라서 다음 디렉토리에 리소스를 넣어두면 스프링 부트가 정적 리소스로 서비스를 제공한다.
  - `src/main/resources` 하위에 위에 있는 클래스패스들을 넣으면 된다.


- **정적 리소스 경로** 
  - `src/main/resources/static`
  

- 다음 경로에 파일이 들어있으면
  - `src/main/resources/static/basic/hello-form.html` 
- 웹 브라우저에서 다음과 같이 실행하면 된다.
  - `http://localhost:8080/basic/hello-form.html` 정적 리소스는 해당 파일을 변경 없이 그대로 서비스하는 것이다.

### HTTP 응답 - 뷰 템플릿
- 뷰 템플릿을 거쳐서 HTML이 생성되고, 뷰가 응답을 만들어서 전달한다.
- 일반적으로 HTML을 동적으로 생성하는 용도로 사용하지만, 다른 것들도 가능하다. 
- 뷰 템플릿이 만들 수 있는 것이라 면 뭐든지 가능하다.
- **스프링 부트는 기본 뷰 템플릿 경로를 제공한다.**
- **뷰 템플릿 경로** 
  - `src/main/resources/templates`
  - `resources` 에서 `static`은 정적, `templates`는 동적
- Thymeleaf 사용
  - 스프링 부트가 기본으로 제공하는 템플릿 엔진
  - JSP와 비슷한 형식으로 HTML을 만들 수 있다.
  - Thymeleaf 공식 사이트: https://www.thymeleaf.org/

[**hello.html - 뷰 템플릿 생성**]()

[**ResponseViewController - 뷰 템플릿을 호출하는 컨트롤러**]()

```java
@RequestMapping("/response-view-v1")
public ModelAndView responseViewV1() {
  ModelAndView mav = new ModelAndView("response/hello")
          .addObject("data", "hello!");
  return mav; }

@RequestMapping("/response-view-v2") // 적당
public String responseViewV2(Model model) {
  model.addAttribute("data", "hello!!");
  return "response/hello";
}

@RequestMapping("/response/hello") // 권장 x
public void responseViewV3(Model model) {
  model.addAttribute("data", "hello!!");
}
```
- **String을 반환하는 경우 - View or HTTP 메시지**
  - `@ResponseBody` 가 없으면 `response/hello` 로 뷰 리졸버가 실행되어서 뷰를 찾고, 렌더링 한다. `@ResponseBody` 가 있으면 뷰 리졸버를 실행하지 않고, HTTP 메시지 바디에 직접 `response/hello` 라는 문자가 입력된다.
- 여기서는 뷰의 논리 이름인 `response/hello` 를 반환하면 다음 경로의 뷰 템플릿이 렌더링 되는 것을 확인할 수 있 다.
  - 실행: `templates/response/hello.html`
- **Void**를 반환하는 경우
  - `@Controller` 를 사용하고, `HttpServletResponse` , `OutputStream(Writer)` 같은 HTTP 메시지 바디를 처리하는 파라미터가 없으면 요청 URL을 참고해서 논리 뷰 이름으로 사용 요청 URL: `/response/hello`
  - 실행: `templates/response/hello.html`
  - **참고로 이 방식은 명시성이 너무 떨어지고 이렇게 딱 맞는 경우도 많이 없어서, 권장하지 않는다.**
- **HTTP 메시지**
  - `@ResponseBody` , `HttpEntity` 를 사용하면, 뷰 템플릿을 사용하는 것이 아니라, HTTP 메시지 바디에 직접 응답 데이터를 출력할 수 있다.
> v2 방법 (String) 권장

### HTTP 응답 - HTTP API, 메시지 바디에 직접 입력
- HTTP API를 제공하는 경우에는 HTML이 아니라 데이터를 전달해야 하므로, HTTP 메시지 바디에 JSON 같은 형식 으로 데이터를 실어 보낸다.
- HTTP 요청에서 응답까지 대부분 다루었으므로 이번시간에는 정리를 해보자.

> **참고** 
> - HTML이나 뷰 템플릿을 사용해도 HTTP 응답 메시지 바디에 HTML 데이터가 담겨서 전달된다. 
> - 여기서 설명하 는 내용은 정적 리소스나 뷰 템플릿을 거치지 않고, 직접 HTTP 응답 메시지를 전달하는 경우를 말한다.
