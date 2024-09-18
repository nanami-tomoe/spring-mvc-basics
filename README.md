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
### Welcome 페이지 만들기
- 스프링 부트에 `Jar` 를 사용하면 `/resources/static/` 위치에 `index.html` 파일을 두면 Welcome 페이지로 처리해준다. 
- 스프링 부트가 지원하는 정적 컨텐츠 위치에 `/index.html` 이 있으면 된다.
> **참고** <br>
> 스프링 부트 Welcome 페이지 지원
> [https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot- features.html\#boot-features-spring-mvc-welcome-page]()
