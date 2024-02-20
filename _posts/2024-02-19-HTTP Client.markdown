---
layout: post
title: HTTP Client
date: 2024-02-19 14:12:20 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img:  # Add image post (optional)
fig-caption: mac.jpg
tags: [BackEnd]
---
# Rest Client
RestTemplate 이라는 클래스를 의존한다.

RestTemplate.get/postForObject: 주어진 Url로 HTTP GET/POST 요청을 보내고, 서버로부터 받은 응답을 지정한 클래스 타입으로 변환하여 반환하는 메서드이다.  
주로 RESTful, API 엔트포인트에 요청을 보내고 응답 데이터를 자바 객체로 반환하는데 사용된다.  
매개변수: String url, Class<T> responseType, urlValiables

RestTemplate.get/postForEntity: ForObject와 비슷하지만, 반환되는 값이 ResponseEntity로 감싸진다는 점에서 차이가 있다. HTTP응답의 모든 정보를 포함하고 있으며, 이를 통해 응답의 상태코드, 헤더, 본문 등을 확인할 수 있다.

exchange: RequestEntity를 사용하여 요청을 보내고, ResponseEntity를 반환한다. 이를 통해 요청과 응답의 모든 정보를 명시적으로 다룰 수 있다.  
exchange의 주요 매개변수:  
1. url: 요청을 보낼 url
2. method: Http 메서드
3. requestEntity: 요청 본문(Body)와 헤더를 포함하는 request 객체.(Post, Put 요청이 Body를 포함하여 보내야 한다.)
4. responseType: 응답 데이터의 타입을 나타내는 ParameterizedTypeReference이다. 응답 본문의 타입을 지정하는데 사용된다.

즉, url 엔트포인트로 get/post/put/delete 요청을 보내고, 받은 Json 응답 데이터를 4번에 작성한 대로 변환아여 ResponseEntity로 반환한다.

### Rest Client_crate, readOne
```java
public class ArticleTempletClient {
  private final RestTemplate restTemplate;
    public ArticleDto create(ArticleDto dto) {
        // 객체를 받기 위해 POST 요청을 한다.
        // postForObject: Response Body만 필요로 할 때 사용는 방법.
        // postForEntity: ResponseEntity와 응답된 헤더, 응답된 status code 등을 보고 싶을 경우 사용.
        ArticleDto response = restTemplate.postForObject(
                // 요청 url
                "/articles",
                // Request body
                dto,
                // Response Body의 자료형
                ArticleDto.class
        );
        log.info("<postForObject> response: {}", response);

        // postForEntity: ResponseEntity를 받기 위해 POST 요청을 한다.
        ResponseEntity<ArticleDto> responseEntity = restTemplate.postForEntity(
                // 요청 url
                "/articles",
                // Request body
                dto,
                // Response Body의 자료형
                ArticleDto.class
        );
        log.info("<postForEntity> body: {}", responseEntity);
        log.info("<postForEntity> responseEntity: {}", responseEntity);
        log.info("<postForEntity> status code: {}", responseEntity.getStatusCode());
        log.info("<postForEntity> headers: {}", responseEntity.getHeaders());

        response = responseEntity.getBody();
        return response;
    }

    // GET
    public ArticleDto readOne(Long id) {
        // 객체를 받기 위해 GET 요청을 한다.
        ArticleDto response = restTemplate.getForObject(
                // Body가 없다. url만 있다.
                String.format("/articles/%d", id), ArticleDto.class
        );
        log.info("response: {}", response);
        // with urlVariables
        // getForObject: 객체를 받기 위해 GET 요청을 한다.
//        response = restTemplate.getForObject(
//                "/articles/{d}", ArticleDto.class
//        );
//        log.info("<getForObject>response: {}", response);

        // getForEntity:  ResponseEntity를 받기 위해 GET 요청을 보낸다.
        ResponseEntity<ArticleDto> responseEntity = restTemplate.getForEntity(
                String.format("/articles/%d", id), ArticleDto.class
        );
        log.info("<getForEntity> responseEntity: {}", responseEntity);
        log.info("responseEntity: {}", responseEntity);
        log.info("status code: {}", responseEntity.getStatusCode());

        // getForObject-Object
        Object responseObject = restTemplate.getForObject(
                String.format("/articles/%d"), Object.class);
        log.info("<readOne getForObject-Object response object: {}", responseObject.getClass());
        return response;
    }
}
```

### Rest Client_readAll
1. 배열로 getForObject
    ```java
    //1. 배열로 getForObject
    ArticleDto[] response = restTemplate.getForObject(
        "/articles", ArticleDto[].class
    );
    log.info("response type: {}", response.getClass());
    ```
2. 배열로 getForEntity
    ```java
    //2. 배열로 getForEntity
    ResponseEntity<ArticleDto[]> responseEntity = restTemplate.getForEntity(
        "/articles", ArticleDto[].class
    );
    log.info("<배열로 gerForEntity> responseEntity: {}", responseEntity);
    log.info("<배열로 gerForEntity> status code: {}", responseEntity.getStatusCode());
    ```
3. 배열을 사용하지 않고 responseEntity를 List로써 결과를 받아보고 싶을 때.
    1. 제네릭 타입을 표현하기 위한 ParameterizedTypeReference<T>를 사용하면 List로 반환된다.
        // 응답 본문의 타입을 List<>로 하기 위함.  
        // ParameterizedTypeReference<T>을 사용하면 제네릭 타입을 클래스로 묘사할 수 있다.
    2. exchange 사용.

    결과로 배열을 사용한 것과 List의 형태로 사용한 것을 볼 수 있다.  
    ![Rest_Client_readAll_exchange](../assets/img/HTTP_Client/Rest_Client_exchangeAndArray.png)

    ```java
    //3. 배열을 사용하지 않고 responseEntity를 List로써 결과를 받아보고 싶을 때.
    // + 제네릭 타입을 클래스로 묘사하기 위한 ParameterizedTypeReference<T>를 사용하면 List로 반환된다.
    // + exchange 사용.
    ResponseEntity<List<ArticleDto>> responseListEntity = restTemplate.exchange(
        "/articles",
        HttpMethod.GET,
        null, // get이어서 Body가 필요없다.
        new ParameterizedTypeReference<>(){}
        // 응답 본문의 타입을 List<>로 하기 위함.
        // ParameterizedTypeReference<T>을 사용하면 제네릭 타입을 클래스로 묘사할 수 있다.
    );
    log.info("<exchange> response parameterized: {}", responseListEntity.getBody().getClass());
    ```

4. Object로 응답 받기.
    getForObject - Object: 응답 자체를 Object로 받는다.  
    List<> 형태로 들어온다. 단점은 내부의 타입을 모른다.

    ```java
    // 4. getForObject - object
    Object responseObject = restTemplate.getForObject(
        "/articles", Object.class
    );
    // 자연스럽게 ArrayList() 형태로 들어온다.
    log.info("<getForObject-Object> response Object: {}", responseObject.getClass());
    ```
    결과  
    ![Rest_Client_readAll_exchange](../assets/img/HTTP_Client/Rest_Client_readAll_Object.png)

### Rest Client Put, Delete
html form 기반의 요청 응답을 사용하던 시절에는 get, post가 주된 http 메서드였다.  
Rest Client도 마찬가지이다. 그래서 put과 delete를 보냈을 때 응답이 어떻게 오는지 안 정해져 있다.  
putForObject, deleteForObject 이런게 없다.  
 * 여담: 코들린에서는 직접 putForObject를 만들어 사용할 수 있다. extentionObject라고 코틀린만의 특수한 비밀 기능 히힛. 

- 응답이 없는 put을 사용
    ```java
    public ArticleDto update(Long id, ArticleDto dto) {
        restTemplate.put(String.format("/articles/%d", id), dto);
        return dto;
    }
    // 업데이트를 했을 때 id가 null이 나온다:
    // 업데이트를 했을 때 Article프로젝트에서 돌려 받은 데이터가 아니라 (RestClient에서 put 응답을 받을 수 없음)
    // 가지고 있는 데이터를 가지고 응답을 하니깐.
    ```

    ![RestClient_put_response_null](../assets/img/HTTP_Client/Rest_Client_Put_response%20null.png)

- 응답을 받는 방법: exchange를 사용해야 한다.
    ```java
    public ArticleDto update(Long id, ArticleDto dto) {
    ResponseEntity<ArticleDto> responseEntity = restTemplate.exchange(
        String.format("/articles/%d", id),
        HttpMethod.PUT,
        new HttpEntity<>(dto),
        ArticleDto.class
        );
    // => articles/{id} 엔드포인트로 put 요청을 보내고, 
    // 받은 Json 응답 데이터를 ArticleDto 클래스의 객체로 변환하여
    // ResponseEntity로 반환한다.
    log.info("status code: {}", responseEntity.getStatusCode()); 
    // Article 프로젝트에서 보낸 응답을 바탕으로 응답객체를 만들어 놓은 상황이어서
    // id가 나온다.
    return responseEntity.getBody();
    }
    ```

    ![RestClient_put_response_success](../assets/img/HTTP_Client/Rest_Client_Put_response%20success.png)

### urlVaiavles
url에는 허용되고 허용되지 않은 글자가 있다.  
우리는 쿼리 파라미터를 전송할 때 url에 &를 붙인다. /search?q=&page=10 이런식으로.  
서로 다른 두개의 파라미터를 구분하기 위함.  
그런데 api에서 데이터를 요청하기 위해 url에 파라미터를 적어야 한다면 &이런식으로 넣어서 요청을 보낼 것이다.  
그러면 실제 url에서는 /search?q=&&page=10 이렇게 될 것이다. 이게 파라미터일까? 파라미터를 구분하기 위한 and 일까?

그래서 incoding을 해준다. 엔드라고 표시는 해야하는데 실제로 엔드라고 표시를 못하니까 엔드로 읽어보자라는 의미에서 변환을 해준다.  
/search?q=%26&page=10 퍼센트 방식으로 인코딩을 하겠다. 퍼센트 인코딩, url 인코딩이라고 한다.

urlVariables는 RestTemplate에서 URL의 일부를 동적으로 설정하기 위한 매개변수입니다. 이를 사용하면 URL의 일부를 문자열로 지정하고 나중에 해당 위치에 동적으로 값을 대입할 수 있습니다.  
URL에 동적으로 변경되는 값을 전달해야 할 때 사용됩니다. 

![Rest_Client_urlValiables](../assets/img/HTTP_Client/Rest_Client_urlValiables.png)

