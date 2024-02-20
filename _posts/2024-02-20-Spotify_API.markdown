---
layout: post
title: SPotify API를 활용한 프로젝트
date: 2024-02-20 14:12:20 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img:  # Add image post (optional)
fig-caption: mac.jpg
tags: [BackEnd]
---
### 순서
1. Spotify와 연결하기.(토큰 얻기)
    ![Spotify_Start조건](../assets/img/Spotify/Spotify_start.png)  
    우선 토큰을 얻어보자. 토큰을 얻기 위해서는 지정된 주소로 Body에 id와 secret을 보내주어야 한다.

    1. yaml에 Spotify와 연결할 데이터 기입.
        ```java
        //yaml
        //개발 단계이니 환경변수를 사용하지 않았다.
        spotify:
        client-id: <spotify id>
        client-secret: <spotify secret>
        ```
    2. RestClient 빈으로 등록.
        ```java
        @Configuration
        public class AuthRestClientConfig {
            @Bean
            public RestClient authRestClient() {
                return RestClient.builder()
                        .baseUrl("https://accounts.spotify.com/api/token")
                        .build();
            }
        }
        ```
    3. AccessTokenDto
        토큰, 토큰타입, 토큰 지속시간을 받을 DTO를 작성한다.

        ```java
        @Data
        public class AccessTokenDto {
            private String accessToken;
            private String tokenType;
            private String expiresIn;
        }
        ```

    4. SpotifyTokenService
        1. Spotify의 아이디와 비밀번호를 필드로 만든다.
            ```java
            public class SpotifyTokenService {
            @Value("${spotify.client-id}")
            private String clientId;
            @Value("${spotify.client-secret}")
            private String clientSecret;
            }
            ```
        2. dfsdffs
            ```java
            public AccessTokenDto getAccessToken() {
            // form 요청을 보낼 때는 같은 이름을 가진 값이 여러개 일 수 있어서,,,
            MultiValueMap<String, Object> parts = new LinkedMultiValueMap<>();
            parts.add("grant_type", "client_credentials");
            parts.add("client_id", clientId);
            parts.add("client_secret", clientSecret);

            return authRestClient.post()
                    .contentType(MediaType.APPLICATION_FORM_URLENCODED)
                    .body(parts)
                    .retrieve()
                    .body(AccessTokenDto.class);
            }
            ```
    5. testController
        토큰이 발행되었나 확인하는 테스트 컨트롤러. (실제 사용하는 컨트롤로가 아니다. 테스트용이다.)

        ```java
        @RequiredArgsConstructor
        @RestController
        public class TestController {
            private final SpotifyTokenService tokenService;
            private final SpotifyService spotifyService;

            @GetMapping("/test/token")
            public AccessTokenDto getAccessToken() {
                return tokenService.getAccessToken();
            }
        }
        ```

        결과  
        ![네임드전략](../assets/img/Spotify/spotify_null.png)  
        null인 이유: 네임드 전략이 달라서. 자바는 caramelCase의 네이밍 규칙을 사용하고 있고, Spotify는 _(언더바) 즉 snake_case를 요구하고 있다.

        해결: @JsonNaming를 사용한다: Jackson이 객체의 프로퍼티 이름을 직렬화 및 역직렬화할 때 사용할 네이밍 전략을 지정할 수 있다. 때로는 Java와 JSON 간에 네이밍 규칙이 다를 수 있다. 이것을 사용하여 프로퍼티 이름을 변환하도록 지시할 수 있다.  
        참고: [json네이밍규칙](../_posts/2024-02-20-240220.markdown)

        ```java
        @Data
        @JsonNaming(PropertyNamingStrategies.SnakeCaseStrategy.class) // 네이밍 전략
        public class AccessTokenDto {
            private String accessToken;
            private String tokenType;
            private String expiresIn;
        }
        ```

        성공  
        ![네이밍규칙으로변환](../assets/img/Spotify/spotify_token_JSON_네이밍규칙.png)
    

### 토큰이 잘 발행이 되는지 테스트를 해봤으니 적용을 해보자.
이제 실제로는 다른 서비스 군데군데에서 토큰을 발급받아서 API 요청을 날려야 한다.  
SpotifyTokenService에서 RestClient로 API 요청을 날릴 과정을 만들어 보자.

- Spotify Search API를 만들어 보자.
필요한 url
![spotify_Search](../assets/img/Spotify/Spotify_Search_need_to.png)  

필요한 파라미터들
![spotify_Search_Parameters](../assets/img/Spotify/Spotify_Search_parameters.png)

1. SpotifyRestConfig를 만들어 Search API를 사용할 url을 기입하자.
    ```java
    @Configuration
    @RequiredArgsConstructor
    public class SpotifyRestConfig {  것이다.
        private final SpotifyTokenService tokenService;

        @Bean
        public RestClient spotifyClient() {
            return RestClient.builder()
                    .baseUrl("https://api.spotify.com/v1")
                    .build();
        }
    }
    ```
2. SearchService를 만들어 search할 때 필요한 파라미터를 준비하고, 토큰을 받는다.
    ```java
    @Slf4j
    @Service
    @RequiredArgsConstructor
    public class SearchService {
        private final RestClient spotifyClient;
        private final SpotifyTokenService tokenService;

        // Object
        public Object search( // 현재 수업에서는 dto로 안하고 일단 Object로 하겠음.
                String q,
                String type,
                String market,
                Integer limit,
                Integer offset
        ) {
            // 파라미터를 다 기입하는거 대신 간단하게 파라미터를 기입해줄 수 있다.
            String url = UriComponentsBuilder.fromHttpUrl("/search")
                    .queryParam("q", q)
                    .queryParam("type", type)
                    .queryParam("market", market)
                    .queryParam("limit", limit)
                    .queryParam("offset", offset)
                    .build(false)
                    .toUriString();

            //인증하기.
            // Bearer Token 준비.
            String tokenHeaderValue
                    = "Bearer " + tokenService.getAccessToken().getAccessToken();
            return spotifyClient.get()
                    .uri(url) //url 뒤에 파라미터 붙인다.
                    .header("Authorization", tokenHeaderValue) //Authorization 헤더에 Bearer + 토큰을 넣는다.
                    .retrieve()
                    .body(Object.class);
        }
    }
    ```
3. 요청이 들어오면 헤더에 토큰이 주입되어 파라미터에 따른 결과를 반환해 준다. 확인해 보자. Controller  
    ```java
    @RestController
    @RequiredArgsConstructor
    @RequestMapping("/search")
    public class SearchController {
        private final SearchService service;

        @GetMapping
        public Object search(
                @RequestParam("q")
                String q,
                @RequestParam(value = "type", defaultValue = "album,artist,track")
                String type,
                @RequestParam(value = "market", defaultValue = "KR")
                String market,
                @RequestParam(value = "limit", defaultValue = "5")
                Integer limit,
                @RequestParam(value = "offset", defaultValue = "0")
                Integer offset
        ) {
            return service.search(q, type, market, limit, offset);
        }
    }
    ```

    ![Spotify_Search_Success](../assets/img/Spotify/Spotify_search_success.png)

         