# 웹사이트 (Thymeleaf + Spring + MySQL)
## 1. 프로젝트 개요
* Thymeleaf + Spring + MySQL을 웹사이트를 개발함으로써 Spring MVC 구조에 대한 감각을 기르고 로그인/회원가입, 게시글 CRUD, 댓글 등 웹 서비스의 핵심 기능을 구현
* ~~배포 url : [http://zshfzwebsite-env.eba-xdz9qrxw.ap-northeast-2.elasticbeanstalk.com/](http://zshfzwebsite-env.eba-xdz9qrxw.ap-northeast-2.elasticbeanstalk.com/)~~ AWS 비용 이슈로 배포 중단

## 2. 브랜치
- main : 메인 베포 브랜치, [세션 로그인 방식](https://github.com/zshfz/spring-mysql-website)
- jwt : jwt 로그인 방식

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
* JsonWebToken
  * `implementation 'io.jsonwebtoken:jjwt-api:0.12.5'`
  - `implementation 'io.jsonwebtoken:jjwt-gson:0.12.5'`
  - `implementation 'io.jsonwebtoken:jjwt-impl:0.12.5'`
  - jwt 생성, 검증을 도와주는 라이브러리
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
    │       │   └── JwtFilter //필터 설정  	
    │       │   └── JwtUtil //jwt 생성, 조회
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
![Image](https://github.com/user-attachments/assets/c2ef60fe-f5d9-4f22-ad57-9750084b7476)
| 테이블명    | 설명                                                           |
|---------|--------------------------------------------------------------|
| member  | - 회원 테이블<br>- member 테이블은 post, comment 테이블과 각각 1:N 관계       |
| post    | - 게시글 테이블<br>- member_id 컬럼은 member 테이블의 id 컬럼과 연결<br>- post 테이블은 comment 테이블과 1:N 관계 |
| comment | - 댓글 테이블<br>- comment 테이블은 member, post 테이블과 각각 N:1 관계<br>- member_id, post_id는 각각 member 테이블의 id, post 테이블의 id와 연결 |

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
- 로그인 기능은 사용자가 아이디, 비밀번호를 서버에 보내면 DB에 있는 아이디, 비밀번호와 비교하고 일치하면 입장권을 발급해줌
- 로그인 후 사용자가 서버에 GET, POST로 데이터 요청시 입장권도 함께 제시하는데 서버는 이 입장권을 확인하고 데이터나 페이지를 리턴 => 회원기능
- 입장권은 로그인 완료시 “이 사람은 누구고 언제 로그인 했습니다” 이런 문자를 만들고 사용자에게 보내서 계속 사용하게 만들면 그것이 입장권
- jwt 방식은 사용자가 로그인하면 사용자에게 입장권을 발급해줄 때 입장권에 사용자 아이디, 로그인 날짜, 유효기간 등의 정보를 적어두고 암호화해서 보내줌 (따로 DB에 저장하는것은 X)
- 사용자가 GET, POST 요청시 서버는 입장권의 정보들을 까보고 이상 없으면 통과시켜줌
- 장점은 서버에 GET, POST 요청할 때 마다 DB 조회할 필요가 없어서 DB 부담이 적음
- JWT를 다른 사용자가 훔쳐가면 그 사용자의 로그인을 막을 수 없음 
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

## 10. 로그인 방식 코드 설명
> **로그인 기능 만들기**
- 세션방식을 쓰면 입장권에 DB에 저장된 세션 아이디 정도만 기록해둔다고 했는데 jwt 방식을 사용하면 입장권에 username, displayName, 유효기간, 권한 등의 정보를 다 기록해두고 이 입장권만 보고 로그인 여부를 판단함
- 입장권 위조를 막기 위해서 입장권에 써있는 정보와 서버에서 정한 비밀번호를 더해서 해싱을 해주면 랜덤 문자열이 나오는데 그걸 입장권에 함께 적어둠 => 사용자가 입장권을 위조해서 제출했을 경우 그 입장권에 써있는 랜덤 문자와 서버에서 방금 연산해본 랜덤 문자를 비교해서 위조 여부 판단

> **Security Config 설정**
```
http.sessionManagement((session) -> session
            .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
    );
```
- Security Config 파일에 위와 같은 코드를 넣어줌으로써 세션 데이터 생성을 막음
- 수동 로그인을 구현할 것이기 때문에 `http.formLogin()` 부분도 지워서 자동으로 로그인 시켜주는 것을 꺼줌

> **로그인 폼 만들기**
```
<form>
  <input name="username" id="username">
  <input name="password" type="password" id="password">
</form>
<button onclick="loginJWT()">JWT방식로그인</button>

<script>
  function loginJWT(){
    fetch('/login/jwt', {
      method : 'POST',
      headers : {'Content-Type': 'application/json'},
      body : JSON.stringify({
        username : document.querySelector('#username').value,
        password : document.querySelector('#password').value
      })
    }).then(r => r.text()).then((r)=>{ console.log(r) })
  }
</script>
```
- 기존 form 방식 폼 대신 AJAX 요청으로 아이디, 비밀번호를 서버에 전송하기 위해 새로운 폼 생성

> **수동으로 로그인 시켜주기**
```
@GetMapping("/login/jwt")
@ResponseBody
public String loginJWT(@RequestBody Map<String, String> data){
  
  var authToken = new UsernamePasswordAuthenticationToken(
    data.get("username"), data.get("password")
  );
  var auth = authenticationManagerBuilder.getObject().authenticate(authToken);
  SecurityContextHolder.getContext().setAuthentication(auth);

  return "";
}
```
- 유저를 수동으로 로그인시켜주려면 `new UsernamePasswordAuthenticationToken(아이디, 비번)`을`new AuthenticationManagerBuilder().getObject().authenticate()` 안에 넣으면 됨 (의존성 주입 필요)
- 유저 정보를 API들의 Authentication authentication 파라미터에 추가하고 싶으면SecurityContextHolder 안에 추가
- 이제 로그인 후 jwt를 만들어서 사용자에게 보내주면 됨

> **jwt 생성**
```
@Component
public class JwtUtil {

    static final SecretKey key =
            Keys.hmacShaKeyFor(Decoders.BASE64.decode(
                    "jwtpassword123jwtpassword123jwtpassword123jwtpassword123jwtpassword"
            ));

    // JWT 만들어주는 함수
    public static String createToken(Authentication authentication) {
        CustomUser customUser = (CustomUser) authentication.getPrincipal();
        //리스트안에 있는걸 꺼내서 ,로 연결시켜서 문자열로 만들라는 코드
        String authorities = authentication.getAuthorities().stream().map(a -> a.getAuthority()).collect(Collectors.joining(","));
        String jwt = Jwts.builder()
                .claim("username", customUser.getUsername())
                .claim("displayName", customUser.getDisplayName())
                .claim("authorities", authorities)
                .issuedAt(new Date(System.currentTimeMillis()))
                .expiration(new Date(System.currentTimeMillis() + 1000 * 60 * 10)) //유효기간 10분
                .signWith(key)
                .compact();
        return jwt;
    }

    // JWT 까주는 함수
    public static Claims extractToken(String token) {
        Claims claims = Jwts.parser().verifyWith(key).build()
                .parseSignedClaims(token).getPayload();
        return claims;
    }

}
```
- `createToken()`는 사용자 정보를 넣어서 jwt 만들어주는 함수, 이 함수에 사용자 username, displayName, 권한 (GrantedAuthority 형식으로 형 변환 필요) 넣어야 함

```
@GetMapping("/login/jwt")
@ResponseBody
public String loginJWT(@RequestBody Map<String, String> data){
  
  (생략)
  var auth2 = SecurityContextHolder.getContext().getAuthentication();
  var jwt = JwtUtil.createToken(auth2);
  return jwt;

}
```
- 로그인시 `createToken()`을 사용해서 jwt만들고 전송
- `SecurityContextHolder.getContext().getAuthentication()` 이거 쓰면 Authentication authentication 변수 사용하는 것과 동일

> **jwt를 쿠키에 저장해주자**
```
@PostMapping("/login/jwt")
@ResponseBody
public String loginJWT(@RequestBody Map<String, String> data, HttpServletResponse response){
  
  (생략)
  var jwt = JwtUtil.createToken(auth2);

  var cookie = new Cookie("jwt", jwt);
  cookie.setMaxAge(10);
  cookie.setHttpOnly(true);
  cookie.setPath("/");
  response.addCookie(cookie);
  
  return jwt;

}
```
- `new Cookie()`로 쿠키하나를 만들 수 있는데 쿠키이름, 값을 파라미터로 넣을 수 있음
- `setMaxAge()`에는 초단위로 쿠키 유효기간 설정이 가능
- `setHttpOnly(true)`로 나쁜사람이 자바스크립트로 쿠키를 조작하는걸 어렵게 만들 수 있음
- `setPath()`로 쿠키가 자동전송될 경로도 설정 가능, 모든 페이지 접속시 쿠키전송되게 만들고 싶으면 슬래시만
- response라는 파라미터에 `.addCookie()` 하면 쿠키하나를 유저 브라우저에 강제로 집어 넣어줌

> **필터 개념**

![Image](https://github.com/user-attachments/assets/990a1abb-25fa-4c73-9cca-2d3d447ebd57)
- 마이페이지 기능(로그인한 사람만 사용할 수 있는 기능)의 경우 로그인 여부를 검사해줘야 하는데 그럴려면 사용자가 제출한 jwt를 까보고 유효기간이 지나지 않았을때 마이페이지를 보내달라고 코드 짜면 됨
- 로그인한 사람만 사용할 수 있는 기능이 엄청 많을 수도 있을거기 때문에 jwt 검사하는 코드를 함수로 만들고 재사용하는 방식 사용 => Filter
- 위 그림처럼 서버로 사용자 요청이 들어오면 바로 API들이 실행되는 것이 아니라 중간에 거치는 곳이 많음
- 그중 Filter 또는 Interceptor라는 곳에 개발자 마음대로 코드를 집어넣을 수 있는데 그러면 API 실행전에 항상 그 코드를 실행하고 지나갈 수 있음
- 보통 회원인증, 로그찍기, 검열용 코드 넣음 => middleware

> **필터 만들기**
```
public class JwtFilter extends OncePerRequestFilter {

  @Override
  protected void doFilterInternal(
      HttpServletRequest request, 
      HttpServletResponse response, 
      FilterChain filterChain
  ) throws ServletException, IOException {
    
    //요청들어올때마다 실행할코드~~
    filterChain.doFilter(request, response);
  }

}
```
- 필터 만들고 싶으면 `extends OncePerRequestFilter` 붙인 클래스 하나 만들면 됨
- @Component 붙이면 자동으로 적절한 위치에서 필터가 실행되는데 그게 싫으면 필터를 실행할 위치를 지정해 줄 수 있음
- SecurityConfig 클래스에 `http.addFilterBefore(new JwtFilter(), ExceptionTranslationFilter.class);` 추가
- 이러면 ExceptionTranslationFilter가 실행되기 전에 내가 만든 JwtFilter 사용됨
- 필터 안에서는
  1. 유저가 보낸 쿠키중 “jwt”라는 이름의 쿠키를 꺼내보고
  2. jwt가 유효한지 확인해보고
  3. 유효하면 Authentication authentication에 유저 정보 추가

> **유저가 보낸 쿠키중 “jwt”라는 이름의 쿠키를 꺼내보고**
```
public class JwtFilter extends OncePerRequestFilter {

  @Override
  protected void doFilterInternal(
      HttpServletRequest request, 
      HttpServletResponse response, 
      FilterChain filterChain
  ) throws ServletException, IOException {
    
    Cookie[] cookies = request.getCookies();
    if (cookies == null){
      filterChain.doFilter(request, response);
      return;
    }
    
    
    var jwtCookie = "";
    for (int i = 0; i < cookies.length; i++){
      if (cookies[i].getName().equals("jwt")){
        jwtCookie = cookies[i].getValue();
      }
    }
    
    filterChain.doFilter(request, response);
  }

}
```
- request 변수에는 사용자가 제출한 쿠키 들어있음
- `getCookies()`하면 배열로 반환되기 때문에 반복문 돌리면서 “jwt”라는 이름의 쿠키 찾음
- 쿠키가 비어있을 경우도 있기 때문에 그럴 경우엔 `filterChain.doFilter(request, response);`써서 다음 필터 실행하도록 코딩

> **jwt 유효한지 확인**
```
 @Override
  protected void doFilterInternal() {
    
    (생략)

    Claims claim;
    try {
      claim = JwtUtil.extractToken(jwtCookie);
    } catch (Exception e) {
      System.out.println("유효기간 만료되거나 이상함");
      filterChain.doFilter(request, response);
      return;
    }

    filterChain.doFilter(request, response);
  }
```
- JwtUtil에 만들어둔 `extractToken()`로 jwt 까보고 유효기간이 만료되거나 이상한 경우 다음 필터 실행되도록 코딩
- jwt안에 들어있는 내용은 `claim.get("displayName").toString()`이런식으로 꺼내쓸 수 있음

> **유효하면 Authentication authentication에 유저 정보 추가**
```
  @Override
  protected void doFilterInternal() {
    
    (생략)

    var authToken = new UsernamePasswordAuthenticationToken(
               claim.get("username").toString(),
               null,
               authorities 
    );
    authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
    SecurityContextHolder.getContext().setAuthentication(authToken);
    
    filterChain.doFilter(request, response);
  }
```
- `new UsernamePasswordAuthenticationToken()` 이걸 .setAuthentication() 안에 집어넣어서 Authentication authentication에 유저 정보 추가
- 근데 이런식으로만 하면 `authentication.getPrincipal()`했을 때 username 밖에 안나옴
- 더 많은 정보를 집어넣고 싶으면 CustomUser 사용

```
@Override
  protected void doFilterInternal() {
    
    (생략)

    var customUser = new CustomUser();
    customUser.displayName = claim.get("displayName").toString();

    var authToken = new UsernamePasswordAuthenticationToken(
               customUser,
               null,
               authorities
    );
    authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
    SecurityContextHolder.getContext().setAuthentication(authToken);
    
    filterChain.doFilter(request, response);
  }
```
- `new CustomUser()`사용시엔 아이디, 비밀번호, 권한 3개 파라미터로 집어넣야 함
- 권한은 List 형태로 집어넣어줘야 함

```
 @Override
  protected void doFilterInternal() {
    
    (생략)

    var arr = claim.get("authorities").toString().split(",");
    var authorities = Arrays.stream(arr)
       .map(a -> new SimpleGrantedAuthority(a)).toList();
    var customUser = new CustomUser(jwtUsername, "none", authorities);
    customUser.displayName = claim.get("displayName").toString();

    var authToken = new UsernamePasswordAuthenticationToken(
               customUser,
               null,
               authorities
    );
    authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
    SecurityContextHolder.getContext().setAuthentication(authToken);
    
    filterChain.doFilter(request, response);
  }
```
- 권한들을 array로 변환한 다음 array안에 들어있는 내용 전부 `new SimpleGrantedAuthority()`안에 넣고 그걸 List로 변환

## 11. 개선 목표
* RESTapi로 개발해보기
* OAuth2 사용해서 소셜 로그인 구현해보기
* 아이디/비밀번호 찾기, 이메일 인증, 대댓글, 좋아요, 실시간 인기 게시글, 쪽지, 실시간 채팅 등의 일반적인 웹 사이트 기능을 모두 갖춘 사이트 만들어보기
