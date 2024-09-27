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
  - https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot- features.html#boot-features-logging


## 요청 매핑
### [MappingController](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/requestmapping/MappingController.java)

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

## 요청 매핑 예시

### 회원 관리 API
- 회원 목록 조회: GET `/users`
- 회원 등록: POST `/users`
- 회원 조회: GET `/users/{userId}`
- 회원 수정: PATCH `/users/{userId}`
- 회원 삭제: DELETE `/users/{userId}`

### [MappingController](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/requestmapping/MappingClassController.java)

## HTTP 요청 - 기본, 헤더 조회

### [RequestHeaderController](https://github.com/nanami-tomoe/spring-mvc-basics/blob/master/src/main/java/hello/springmvc/basic/request/RequestHeaderController.java)
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

- MultiValueMap
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