# 5장 Spring Security와 OAuth 2.0으로 로그인 기능 구현하기

이 장에서는 Spring Security와 OAuth 2.0을 이용해서 Google, Naver 연동 로그인 기능을 구현해본다.

## 공부한 내용
- Google 연동 로그인 구현하기
  - 구글 서비스 등록 ([링크](https://console.cloud.google.com/))
  - 구글 API 프로젝트에 적용
    - `application-oauth.properties`
    - `application.properties`
  - Domain + Repository Layer
    - `User.java`
    - `Role.java`
    - `UserRepository.java`
  - Spring Security 설정
    - `build.gradle`
    - `SecurityConfig.java`: 권한 관련 설정
  - Service Layer + DTO
    - `CustomOAuth2UserService.java` extends `OAuth2UserService<OAuth2UserRequest, OAuth2User>`
    - `OAuthAttributes.java`
    - `SessionUser` implements `Serializable`
  - 로그인 관련 UI 추가
    - `index.mustache`
    - `IndexController.java`
 - 어노테이션 기반으로 코드 개선
   - 어노테이션 생성 및 프로젝트에서 쓸 수 있게 만들기
     - `LoginUser.java`
     - `LoginUserArgumentResolver.java` implements `HandlerMethodArgumentResolver`
     - `WebConfig.java` implements `WebMvcConfigurer`
   - 코드 개선
     - `IndexController.java`
- 세션 저장소로 DB 사용하기
  - jdbc 의존성 추가
    - `build.gradle`
    - `application.properties`
- Naver 연동 로그인 구현하기
  - 네이버 API 등록([링크](https://developers.naver.com/apps/#/register?api=nvlogin))
  - 네이버 API 프로젝트에 적용
    - `application.properties`
      - 구글과 달리 Spring Security에 등록되어 있지 않으므로 uri 등을 수동 등록해야 한다.
  - Spring Security 설정 추가
    - `OAuthAttributes.java`
  - 네이버 로그인 관련 UI 추가
    - `index.mustache`
- 기존 테스트에 Security 적용하기
  1. `test` 하위에도 `application.properties` 추가
     - `application.properties` in `src/test/resources`
  2. `@SpringBootTest`에서 `USER` 권한 부여
     - `PostsApiControllerTest.java`
  3. `@WebMvcTest` 관련 문제 해결
     - `HelloControllerTest.java`
     - `Application.java`
     - `JpaConfig.java`

## 궁금한 사항
- 세션이라는 것은 대체 무엇인가?
  - 실체가 무엇인가?
  - 저장 공간? 활성화/비활성화 되는 것?