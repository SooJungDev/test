## 스프링 프레임워크 핵심기술
- 백기선님의 인프런 강의 듣고 공부한 내용 정리
- 차근차근 열심히듣자!

## 학습 목표
- 스프링 프레임워크의 핵심 기술 IoC,AOP,PSA를 이해합니다.
- 스프링 프레임워크 Ioc 컨테이너의 다양한 기능을 사용 할 수 있습니다.
- 다양한 방법으로 빈을 정의하고 의존관계를 주입 할 수 있습니다.
- 스프링 AOP를 사용하여 Aspect를 모듈화 할 수 있습니다.
- 그밖에 다양한 스프링 핵심 기술을 이해하고 활용 할 수 있습니다.

## 학습 목차
- IoC 컨테이너와 빈
- 리소스
- Validation
- 데이터 바인딩
- SpEL
- 스프링 AOP
- Null-Safety

## 스프링이란?
- 소규모 애플리케이션 또는 기업용 애플리케이션을 자바로 개발 하는데 있어 유용하고 편리한 기능을 제공하는 프레임워크

- 스프링 프레임워크 그자체
- 스프링 프레임워크 포함 모든 스프링 프로젝트(스프링부트, 스프링데이터, 스프링시큐리티..)
- 이강좌에서는 스프링은 "스프링프레임워크"

스프링의 역사
- 2003년 등장(개발은 이미 이전부터 진행됬음)
    - 등장시 Java EE 표준과 싸우는것처럼 보였지만 실제론 JEE 스팩 구현 모음체(+알파)
    - Servlet, WebSocket, Bean Vaildation, JPA, Dependency Injection, ...
- 최근까지 주로 서블릿 기반 애플리케이션을 만들때 사용해 옴.
- 스프링 5부터는 WebFlux 지원으로 서블릿 기반이 아닌 서버 애플리케이션도 개발 할 수 있게됨

디자인 철학
- 모든 선택은 개발자의 몫(예, 스프링이 특정 영속화 기술을 강요하지않는다.)
- 다양한 관점을 지향한다.(유연성)
- 하위 호환성을 지킨다(노력)
- API를 신중하게 설계한다.(공들인다.)
- 높은 수준의 코드를 지향한다(자랑?...ㅎ)


## 참고사이트
- [인프런 백기선님 강의 - 스프링 프레임워크 핵심 기술](https://www.inflearn.com/course/spring-framework_core/)
- [스프링 프레임워크 레퍼런스](https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html)
- [스프링이란?](https://docs.spring.io/spring/docs/current/spring-framework-reference/overview.html#overview)
