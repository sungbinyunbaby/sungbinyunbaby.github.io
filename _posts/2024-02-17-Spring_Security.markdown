---
layout: post
title: Spring_Security
date: 2024-02-17 14:12:20 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img:  # Add image post (optional)
fig-caption: mac.jpg
tags: [BackEnd]
---
# Security 만들기

![security_Base](../assets/img/security/security_Base.png)

### 순서 
1. UsernamepasswordAuthenticationFiler 구현 - 로그인 진행
2. AuthenticationManager에 전달 - 데이터베이스로 부터 회원 정보를 가져와 검증 진행.
3. 
    1. 검증이 성공을 한다면 successfulAuthentication이 동작. - 동작을 할 때 jwt를 생성을 해서 사용자게 응답을 해주면 된다.
    2. 검증에 실패를 한다면 unsuccessfulAuthentication이 동작 - jwt를 생성하지 않고 401 에러를 내준다.

### 회원가입, 로그인, jwt발급, jwtFilter 설정

1. jwt 의존성 설정
    ```java
    dependencies {

    implementation 'io.jsonwebtoken:jjwt-api:0.12.3'
    implementation 'io.jsonwebtoken:jjwt-impl:0.12.3'
    implementation 'io.jsonwebtoken:jjwt-jackson:0.12.3'
    }
    ```
2. webSecurityConfig class 만들기 
    > SecurityFilterChain을 빈으로 등록 
    > 세션 stateless 
    > BCryptPasswordEncoder 등록 - 따로 클래스 만들어서 빈으로 등록 후 사용.
3. DB 연결 및 Entity 설정
    1. ```java
        spring:
            datasource:
            driver-class-name: com.mysql.cj.jdbc.Driver
            url: <jdbc:mysql://아이피:3306/이름>
            username: <username>
            password: <password>

        jpa:
            database: mysql
            hibernate:
            ddl-auto: none //jpa와 디비를 연결하려면 create로 설정 후 연결 > 다시 none
            show-sql: true
        ```
    2. 엔티티, Repository 만든 후 연결.

4. 회원가입 로직 구현
    ![회원가입로직](../assets/img/security/회원가입%20로직.png)
    1. JoinDto 만들기 - username, password
    2. JoinController 만들기.
    3. JoinService 만들기 - userRepository와 BCryptPasswordEncoder 의존성 받아서 회원가입 메서드 만들기 > 입력받은 joinDto를 userEntity에 저장.

5. 로그인 필터 구현
    1. 아이디, 비밀번호 검증을 위한 커스텀 필터 작성.
        UsernamePasswordAuthentication 필터 작성 
        > UsernamePasswordAuthentication 상속 받기.
        > UsernamePasswordAuthenticationToken 사용 - 인증하기 위함.
        > UsernamePasswordAuthenticationToken을 authenticationManager에 반환.
        > successfulAuthentication, unsuccessfulAuthentication 오버라이딩하기.
        > webSecurityConfig에 .addFilterAt 설정. - 커스텀 로그인 필터 등록.
        > AuthenticationManager Bean 등록
        ```java
        @Configuration
        @EnableWebSecurity
        public class SecurityConfig {

                //AuthenticationManager가 인자로 받을 AuthenticationConfiguraion 객체 생성자 주입
                private final AuthenticationConfiguration authenticationConfiguration;

            public SecurityConfig(AuthenticationConfiguration authenticationConfiguration) {

                this.authenticationConfiguration = authenticationConfiguration;
            }

                //AuthenticationManager Bean 등록
                @Bean
            public AuthenticationManager authenticationManager(AuthenticationConfiguration configuration) throws Exception {

                return configuration.getAuthenticationManager();
            }
        }
         ```
    2. DB에 저장되어 있는 정보를 기반으로 검증할 로직 작성.
    3. 로그인 성공시 jwt를 반환할 success 핸들러 생성.
    4. 커스텀 필터 SecurityConfig에 등록.

6. 로그인 검증 로직
    1. Repository에 Optional<UserEntity> findByUsername(String username); 추가.
    2. UserDetailsService 커스텀 구현.
        > loadUserByUsername에서 입력받은 username DB에서 조회하고 있으면 CustomUserDetails에 데이터 전달.
    2. UserDetails 구현 - CustomUserDetails
        > 권한 발급. user 데이터의 role을 권한으로 설정.
        > 나머지 return ture로 설정.
    3. http://localhost:8080/login 해보기.

7. jwt 발급 - jwtTokenUtil class 만들기
    1. 암호화
    2. jwt에 정보 넣고 jwt 발급하기.
    3. CustomUserDetailsService(UserDeatilsService 구현) - loadUserByUsername 설정.(로그인 시 필요한 정보를 저장해서 CustomUserDetails로 전달.)
    4. CustonUserDetails(UserDetails 구현) - CustomUserDetailsService에서 받는 데이터를 바탕으로 설정. 권한, getPassword(), getUsername()를 userEntity.get,,,으로 설정, 나머지 return true.
    5. jwtTokenUtils 클래스 만들어 Jwt 생성 및 발행.
        1. 암호화
        2. jwt에 정보담기 jwt 생성
        3. 발급한 jwt 토큰이 정상인지 확인
        4. jwt 토큰이 정상이면 발급.
    6. jwtTokenFilter 클래스 만들어 토큰이 있는지 확인하고 정당한 토큰인지 확인 후 필터종료.
        1. OncePerRequestFilter을 상속 받는다. doFilterInternal()를 구현.
        2. 주의점: 토큰이 존재하지 않아도 doFilter을 통과해야 한다. 토큰을 가지고 있지 않는 사용자 즉, 로그인이 되어 있지 않은 사용자도 필터를 통과해야 하기 때문이다.
    7. SecufityConfig 클래스로 가서 .addFilterBefore을 사용해서 LoginFilter 보다 먼저 실행하게 한다. => 로그인 성공의 결정적이 단계!!! 중요!!!
    
* 클라이언트가 로그인을 시도하면 로그인 필터가 사용자 자격을 확인하고, 유효한 경우 토큰을   생성하고 클라이언트에게 전달한다.

* 클라이언트가 보호된 엔드포인트에 요청을 보내면 토큰 필터가 해당 요청의 토큰을 검사하여 유효성을 확인하고, 유효한 경우 사용자 인증 정보를 SecurityContextHolder에 저장한다.

* 보호된 엔드포인트에 대한 요청이 승인되면 클라이언트는 해당 엔드포인트에 액세스할 수 있다.

* 로그인에 성공해도 토큰이 만들어지지 않았다면 로그인한거라고 볼 수 없다.

* 중요:
    1. 토큰필터
    2. 로그인필터 - 사용자 유효성 확인 -> 유효하면 토큰 생성.
    3. 보호된 엔티티에 요청.
    4. 토큰필터 - 토큰 유효성 확인 -> 토큰이 존재하고 유효하면 사용자 인증 정보를 SecurityContextHolder에 저장.
    5. 보호된 엔트포인트에 대한 요청이 승인되면 해당 엔드포인트에 엑세스 할 수 있음.
    * 토큰이 유효한 경우 로그인 필터는 더 이상 필요하지 않다. 토큰 필터만 통과하면 된다.  
    로그인이 되있다면 인증 절차를 다시 거치는 것은 불필요하다.

![보호된엔드포인트접속성공](../assets/img/security/보호된엔트포인트에접속성공.png)

# 프로젝트를 하면서 어려웠던 점
토큰의 정당성 검증을 계속 실패함.  
![토큰실패](../assets/img/security/토큰실패.png)

이유: 암호화 알고리즘에 따라 토큰의 모양이 달라진다. 나는 hmacShaKey를 사용했기 때문에 "Bearer" + token을 하면 안되고 Bearer을 빼줘야 했다.  
![토큰성공](../assets/img/security/토큰성공.png)