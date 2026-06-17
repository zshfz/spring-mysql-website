# CRUD 웹사이트
## 1. 프로젝트 개요
* Thymeleaf + Spring + MySQL을 웹사이트를 개발함으로써 Spring MVC 구조에 대한 감각을 기르고 로그인/회원가입, 게시글 CRUD, 댓글 등 웹 서비스의 핵심 기능을 구현
* ~~배포 url : [http://zshfzwebsite-env.eba-xdz9qrxw.ap-northeast-2.elasticbeanstalk.com/](http://zshfzwebsite-env.eba-xdz9qrxw.ap-northeast-2.elasticbeanstalk.com/)~~ AWS 비용 이슈로 배포 중단

## 2. 브랜치
* main : 메인 베포 브랜치, 세션 로그인 방식
* jwt : [jwt 로그인 방식](https://github.com/zshfz/spring-mysql-website/tree/jwt)

## 3. 개발환경
* 프론트엔드 : Thymeleaf, CSS
* 백엔드 : Spring Boot
* DB : MySQL (Azure Cloud DB)
* 배포 환경 : AWS Elastic Beanstalk

## 4. 사용 기술
### 프론트엔드
* Thymeleaf
  * `implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'` ⇒ HTML에 백엔드 데이터를 동적으로 바인딩
  * navbar, footer를 재사용 가능하게 만들어 전체 페이지에 공통 UI 유지
  * `implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity6:3.1.1.RELEASE'` ⇒ HTML에서 로그인 여부에 따라 HTML 랜더링 제어
* CSS
  * Nesting 사용

### 백엔드
* Spring Boot
  * 메인 프레임워크
  * Controller, Service, Repository 등 계층 분리를 통한 MVC 구조
* Spring Security
  * `implementation 'org.springframework.boot:spring-boot-starter-security'` ⇒ 로그인 인증 및 URL 접근 권한 제어를 위한 보안 프레임워크
  * `CustomUser`를 통해 사용자 정보 확장 저장
  * `@PreAuthorize` 및 `SecurityConfig`를 통해 요청별 인증 처리 및 리다이렉션 구성
* Spring Session JDBC
  * `implementation 'org.springframework.session:spring-session-jdbc'` ⇒ 로그인 세션 정보를 메모리에 저장하는것이 아니라 MySQL DB에 저장
* Spring Data JPA
  * `implementation 'org.springframework.boot:spring-boot-starter-data-jpa'`
  * JpaRepository 기반의 CRUD 메소드 제공
* MySQL
  * `implementation 'mysql:mysql-connector-java:8.0.33'`
  * Azure 클라우드 기반 MySQL 인스턴스 사용
  * @OneToMany, @ManyToOne 과 같은 양뱡향 연관관계를 통한 게시글-댓글-회원 구조 설계
* AWS
  * `implementation 'io.awspring.cloud:spring-cloud-aws-starter-s3:3.1.1'` ⇒ AWS S3 presigned-url 기반 이미지 업로드 구현
  * 게시글 이미지 및 프로필 이미지를 S3에 저장
  * `S3Presigner` 을 통해 presigned URL 생성하고 그 URL에 PUT 요청으로 이미지 업로드
* 입력값 검증
  * `implementation 'org.springframework.boot:spring-boot-starter-validation'` ⇒ `@Valid`, `@NotBlank`, `@Size`을 사용하여 서버 사이드 유효성 검사 수행
  * `BindingResult`를 통해 오류메시지를 화면에 출력
* Lombok
  * `compileOnly 'org.projectlombok:`
  * `lombok'annotationProcessor 'org.projectlombok:lombok'`
  * getter, setter, 생성자 등 반복되는 코드를 줄이기 위해 사용

## 5. 주요 기능
* 회원가입, 로그인, 로그아웃
* 게시글 CRUD
* 댓글
* 회원 프로필 보기 및 수정
* 이미지 업로드
* 검색
* 페이지네이션

## 6. 폴더 구조
```
src
└── main
    ├── java
    │   └── com.example.website
    │       ├── advice 
    │       │   ├── GlobalControllerAdvice //로그인된 사용자 정보 전역 주입         
    │       │   └── GlobalExceptionHandler //공통 예외 처리        
    │       ├── controller
    │       │   ├── CommentController //댓글 작성 처리              
    │       │   ├── MemberController //로그인, 회원가입, 프로필 수정              
    │       │   ├── PostController //게시글 CRUD, 검색, 페이지네이션                 
    │       │   └── S3Controller //presigned-url 요청 처리                   
    │       ├── dto
    │       │   ├── CommentRequest
    │       │   ├── EditProfileRequest
    │       │   ├── PostRequest
    │       │   └── RegisterRequest
    │       ├── entity
    │       │   ├── Comment
    │       │   ├── Member
    │       │   └── Post
    │       ├── repository
    │       │   ├── CommentRepository
    │       │   ├── MemberRepository
    │       │   └── PostRepository
    │       ├── security
    │       │   ├── CustomUser //User 클래스 상속받아 확장된 CustomUser                     
    │       │   ├── MyUserDetailsService //사용자 인증 처리         
    │       │   └── SecurityConfig //Spring Security 설정                 
    │       ├── service
    │       │   ├── CommentService
    │       │   ├── MemberService
    │       │   ├── PostService
    │       │   └── S3Service
    │       └── WebsiteApplication                 
    └── resources
        ├── static                                
        ├── templates                              
        ├── application.properties                


```
* 📁controller : 각 주요 도메인의 웹 요청 처리
* 📁service : 비즈니스 로직 담당
* 📁repository : JPA 기반 데이터 액세스 계층
* 📁dto : 직접 엔티티에 접근하는것은 보안상 좋지 않으므로 데이터 전달 객체 사용
* 📁entity : DB 테이블에 매핑되는 JPA 엔티티 클래스
* 📁security : Spring Security 관련 설정
* 📁advice : @ControllerAdvice 을 사용한 전역 설정
* 📁resources : Tyymeleaf 템플릿, 이미지, 설정파일

## 7. API 명세서
| 구분 | 기능 | HTTP METHOD | API URL | 요청 | 응답 | 에러 |
|------|------|-------------|---------|------|------|------|
| [회원] | 회원가입 | GET | `/register` |  | 회원가입 화면 반환 |  |
|  |  | POST | `/register` | `displayName`,<br />`username`,<br />`password`,<br />`profileImageUrl` | 메인페이지로 리다이렉트 | - 아이디 중복 시 400<br />- 유효성 검사 실패 시 HTML에 오류 표시 |
|  | 로그인 | GET | `/login` |  | 로그인 화면 반환 |  |
|  |  | POST | `/login` | `username`,<br />`password` | 메인페이지로 리다이렉트 | - 아이디 or 비밀번호 불일치 → 로그인 화면에 에러 메시지 |
|  | 로그아웃 | GET | `/logout` |  | 메인페이지로 리다이렉트 |  |
|  | 내 프로필 보기 | GET | `/profile` | 로그인된 사용자 세션 정보 | 프로필 화면 반환 | - 비로그인으로 접근 시 403<br />- 회원정보를 찾을 수 없는 경우 400 |
|  | 프로필 수정 | GET | `/edit-profile/{id}` | 로그인된 사용자 세션 정보,<br />사용자 id | 프로필 수정 화면 반환 | - 비로그인이거나 본인 외 접근 시 403<br />- 존재하지 않는 회원 ID일 경우 400 |
|  |  | POST | `/edit-profile/{id}` | 로그인된 사용자 세션 정보,<br />사용자 id,<br />`displayName`,<br />`profileImageUrl` | 프로필 화면으로 리다이렉트 | - 비로그인이거나 본인 외 접근 시 403<br />- 이미 사용 중인 닉네임으로 수정할 경우 400<br />- 유효성 검사 실패 시 HTML에 오류 표시 |
| [게시글] | 전체 글 목록 조회 | GET | `/` |  | 게시글 목록 화면 반환 |  |
|  | 페이지네이션 | GET | `/board/page/{id}` | 현재 보고싶은 페이지 번호(id) | 해당 페이지의 게시글 목록 반환 |  |
|  | 글쓰기 화면 불러오기 | GET | `/write` |  | 글쓰기 화면 반환 | - 비로그인으로 접근 시 403 |
|  | 글쓰기 | POST | `/write` | `title`,<br />`content`,<br />`postImageUrl`,<br />로그인된 사용자 세션 정보 | 메인화면으로 리다이렉트 | - 비로그인으로 접근 시 403<br />- 작성자 정보 찾을 수 없을 경우 400<br />- 유효성 검사 실패 시 HTML에 오류 표시 |
|  | 글 상세 불러오기 | GET | `/post/{id}` | 게시글 id | 게시글 상세 화면 반환 | - 존재하지 않는 게시글일 경우 400 |
|  | 글 수정화면 불러오기 | GET | `/edit/{id}` | 게시글 id,<br />로그인된 사용자 세션 정보 | 게시글 수정 화면 반환 | - 비로그인이거나 본인 외 접근 시 403 |
|  | 글 수정 | POST | `/edit/{id}` | `title`,<br />`content`,<br />`postImageUrl`,<br />게시글 id | 게시글 상세 화면으로 리다이렉트 | - 비로그인이거나 본인 외 접근 시 403<br />- 유효성 검사 실패 시 HTML에 오류 표시 |
|  | 글 삭제 | POST | `/delete/{id}` | 게시글 id,<br />로그인된 사용자 세션 정보 | 메인화면으로 리다이렉트 | - 비로그인이거나 본인 외 접근 시 403<br />- 게시글이 존재하지 않을 경우 400 |
|  | 검색 | POST | `/search` | `searchText` | 게시글 목록 화면 반환 |  |
| [댓글] | 댓글 작성 | POST | `/comment/{id}` | 게시글 id,<br />`content`,<br />로그인된 사용자 세션 정보 | 게시글 상세 화면으로 리다이렉트 | - 비로그인 시 403<br />- 유효성 검사 실패 시 HTML에 오류 표시<br />- 게시글이 존재하지 않거나 작성자 정보를 찾을 수 없으면 400 |
| [이미지] | presigned-url 발급 | GET | `/presigned-url` | `filename` | presigned URL 문자열 | - `filename` 누락 시 400<br />- AWS 연결 실패 시 500 |

## 8. ERD 다이어그램
![Image](https://github.com/user-attachments/assets/c740abc4-07d9-4478-8c2e-57a227224bc4)
| 테이블명           | 설명                                                           |
|----------------|--------------------------------------------------------------|
| member         | - 회원 테이블<br>- member 테이블은 post, comment 테이블과 각각 1:N 관계       |
| post           | - 게시글 테이블<br>- member_id 컬럼은 member 테이블의 id 컬럼과 연결<br>- post 테이블은 comment 테이블과 1:N 관계 |
| comment        | - 댓글 테이블<br>- comment 테이블은 member, post 테이블과 각각 N:1 관계<br>- member_id, post_id는 각각 member 테이블의 id, post 테이블의 id와 연결 |
| spring_session | - 로그인 사용자 세션 정보를 저장<br>- Spring Session JDBC가 자동 생성          |

## 9. 화면별 기능
| 회원가입 화면                                                      |
|--------------------------------------------------------------|
| ![Image](https://github.com/user-attachments/assets/84b17b29-90eb-489b-9229-332c390351c7) |
- 사용자는 아이디, 비밀번호, 닉네임, 프로필 이미지를 선택해서 회원가입
- 중복된 아이디일 경우 예외를 발생시켜 `error.html`로 이동 (`error.html` 만들어두면 Thymeleaf가 자동으로 화면 전환시켜줌)
- 이미지는 AJAX 요청으로 presigned-url을 받아오고 그 url에 PUT 요청을 통해 AWS S3 버킷에 업로드
- member 테이블의 profileImageUrl 컬럼엔 S3에 업로드된 이미지의 url 들어있음
- 비밀번호는 Spring Security의 Bcrypt로 해싱 처리
- 프로필 이미지를 선택하지 않았을 경우 S3에 저장되어 있는 기본 이미지가 프로필 이미지로 설정
- `@Valid`와 `BindingResult` 사용해서 유효값 검사 => HTML에서 `hasError()` 메소드로 오류 메시지 출력

| 로그인 화면                                                       |
|--------------------------------------------------------------|
| ![Image](https://github.com/user-attachments/assets/877cb53d-94c5-4057-bab7-70091b21f01c) |
- 사용자는 아이디, 비밀번호 입력
- `SecurityConfig`에서 `formLogin()` 설정에 따라 POST 요청은 Spring Security 내부에서 처리됨
- `MyUserDetailsService`가 호출되어 DB에 사용자가 입력한 username과 일치하는 행이 있는지 확인
- 인증 성공시 `CustomUser` 객체에 저장
- 로그인 성공시 메인화면으로 리다이렉트되고 Spring Session 테이블에 세션 정보 저장
- 실패시 Spring Security가 자동으로 `/login?error`로 리다이렉트 시켜주는데 HTML에서 타임리프 `th:if` 문법으로 오류 메세지 노출
- 로그인에 성공하면 타임리프의 `sec:authorize`문법으로  navbar에 프로필 사진 출력 
- 로그인에 성공하면 `@ControllerAdvice`을 통해 세션 정보를 CustomUser 타입으로 형변환해서 전역적으로 리턴하여 어디서든지 사용자 정보를 꺼내 쓸 수 있도록 구현
- 로그아웃도 `SecurityConfig`에서 logout()이 호출되서 Spring Security 내부에서 자동으로 처리

| 프로필 화면                                                       |
|--------------------------------------------------------------|
| ![Image](https://github.com/user-attachments/assets/1591f919-4def-400e-aa0c-d01966d57b59) |
- `@PreAuthorize("isAuthenticated()")`설정으로 로그인된 사용자만 접근할 수 있도록 구현
- navbar 오른쪽에 프로필 사진 누르면 프로필 정보 화면으로 이동
- Spring Security 세션에서 현재 사용자 정보를 추출해 `profile.html`에 주입
- 프로필 수정 버튼을 누르면 프로필 수정 화면으로 이동
- 입력 폼에 기존 이미지와 닉네임이 채워져 있도록 구현
- 자신의 프로필만 수정할 수 있도록 구현
- 중복된 닉네임일 경우 `error.html`로 이동
- `@Valid`와 `BindingResult` 사용해서 유효값 검사 => HTML에서 `hasError()` 메소드로 오류 메시지 출력
- `@OneToMany,` `@ManyToOne`으로 컬럼이 연결되어 있기 때문에 `.`연산자로 화면에 출력시켜 내가 쓴 게시물, 내가 단 댓글 확인 가능, 클릭하면 해당 게시글로 이동

| 홈 화면                                                         |
|--------------------------------------------------------------|
| ![Image](https://github.com/user-attachments/assets/b57815cc-a091-4274-afda-0060be681ef4) |
- 전체 게시글 목록 출력
- `Page<Post> findPageBy(Pageable pageable);`로 페이지네이션 구현
- `findPageBy()`의 현재 페이지, 전체 페이지 같은 속성들을 HTML에 주입해서 사용
- n-gram parser를 이용한 full text index를 만들어서 검색 기능 구현
- native query 문법으로 `@Query(value = "SELECT * FROM shop.item WHERE MATCH(title) AGAINST(?1)",  nativeQuery = true)`라고 작성해서 full text index 사용해서 검색하도록 구현

| 글쓰기 화면                                                       |
|--------------------------------------------------------------|
| ![Image](https://github.com/user-attachments/assets/1340f1ab-1495-4e2c-aec1-cab985549dac) |
- `@PreAuthorize("isAuthenticated()")`설정으로 로그인된 사용자만 접근할 수 있도록 구현
- `@Valid`와 `BindingResult` 사용해서 유효값 검사 => HTML에서 `hasError()` 메소드로 오류 메시지 출력
- 제목과 글내용, 이미지 첨부해서 글쓰기 가능
- 게시글에 포함할 이미지도 회원가입과 마찬가지로 presigned-url 방식

| 게시글 상세 화면                                                    |
|--------------------------------------------------------------|
| ![Image](https://github.com/user-attachments/assets/1864d49b-ee63-4381-b7f6-17db2d2bacd1) |
- 게시글 수정, 삭제는 게시글을 작성한 사람만 가능하고 게시글을 작성하지 않는 사용자가 수정, 삭제 버튼을 눌렀을 경우 `error.html`로 이동
- 게시글 수정, 삭제의 경우 게시글의 id를 가지고 전체 행을 가져와서 그 행 안에 있는 username과 현재 로그인 되어있는 사용자의 username을 비교해서 일치할 경우에만 수정, 삭제 가능하도록 구현


| 댓글 화면                                                        |
|--------------------------------------------------------------|
| ![Image](https://github.com/user-attachments/assets/bc39b82b-d4fe-447a-b921-bf7af6a57162) |
- CASCADE 설정을 걸어놨기 때문에 게시글을 삭제할 경우 그 게시글에 달린 댓글도 모두 삭제
- `@PreAuthorize("isAuthenticated()")`설정으로 로그인된 사용자만 댓글을 달 수 있도록 구현

10. 로그인 방식 코드 설명
> **로그인 기능 만들기**
- 세션 방식의 경우 Spring Security 라이브러리 사용해서 셋팅 코드만 설정해주면 기능 구현 끝
  1. 로그인용 html 페이지 만들고
  2. 폼으로 로그인하겠다고 설정해놓고
  3. DB에 있는 사용자 정보 꺼내오는 코드 작성

> **로그인용 html 페이지**
```
<form th:action="@{/login}" method="POST">
    <h1>로그인</h1>
    <input type="text" placeholder="아이디" name="username"/>
    <input type="password" placeholder="비밀번호" name="password"/>
    <button type="submit">로그인</button>
    <div class="error" th:if="${param.error}">
        <h4>아이디나 비밀번호를 잘못 입력하셨습니다.</h4>
    </div>
</form>
```
- Spring Security가 아이디는 username, 비밀번호는 password로 해놓기로 강제함

> **폼으로 로그인하겠다고 설정**
```
http.formLogin((formLogin)
        -> formLogin.loginPage("/login")
        .defaultSuccessUrl("/")
);
```
- SecurityConfig 클래스에 위의 코드 추가
- 로그인 성공시 홈화면으로 이동
- 로그인 실패할경우 /login?error 페이지로 이동하는데 타임리프의 th:if 문법으로 HTML에서 오류 메시지 출력

> **DB에 있는 사용자 정보 꺼내오는 코드 작성**

![Image](https://github.com/user-attachments/assets/086b816d-464d-490d-8943-f83f7f33eca5)
```
public class MyUserDetailsService implements UserDetailsService {

    private final MemberRepository memberRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Optional<Member> result = memberRepository.findByUsername(username);
        if (result.isEmpty()) {
            throw new UsernameNotFoundException("존재하지 않는 아이디 입니다.");
        }
        Member member = result.get();

        List<GrantedAuthority> authorities = new ArrayList<>();
        authorities.add(new SimpleGrantedAuthority("ROLE_USER"));

        CustomUser customUser = new CustomUser(member.getUsername(), member.getPassword(), authorities);
        customUser.setDisplayName(member.getDisplayName());
        customUser.setProfileImageUrl(member.getProfileImageUrl());
        return customUser;
    }
}
```
- 사용자가 폼으로 username, password 라는 이름의 데이터 제출하면 위의 그림처럼 클래스들을 타고 이동해서 UserDetailsService 라는 클래스에 도착
- UserDetailsService 에서 유저정보 DB에서 꺼내주기만 하면 DaoAuthenticationProvider가 알아서 사용자가 제출한 비밀번호와 DB에서 꺼낸 DB랑 비교해줌
- 이상한게 없으면 입장권용 쿠키를 하나 생성해서 사용자에게 보내주고 세션 데이터도 저장
- `loadUserByUsername()`에 파라미터 추가하면 사용자가 로그인할 때 제출한 username 들어옴
- 그래서 DB에서 해당 username과 일치하는 사용자 찾아와서 그 회원정보를 `new User()`에 담아서 리턴해주라고 코드 짜면 끝
- 컨트롤러에서 Authentication authentication 변수 선언해주면 거기에 현재 로그인 정보 들어있음
- authentication 변수에 더 많은 정보 집어넣고 싶으면 User 클래스 상속받는 CustomUser 클래스 만들기

## 11. 개선 목표
* RESTapi로 개발해보기
* JWT, OAuth2 사용해서 소셜 로그인 구현해보기
* 아이디/비밀번호 찾기, 이메일 인증, 대댓글, 좋아요, 실시간 인기 게시글, 쪽지, 실시간 채팅 등의 일반적인 웹 사이트 기능을 모두 갖춘 사이트 만들어보기
