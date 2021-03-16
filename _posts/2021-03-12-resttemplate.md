---
title: "RestTemplate 정리"
excerpt: "RestTemplate 사용법 정리"

categories:
 - tech
tags:
 - restemplate
---

# RestTemplate 정리

Object Storage, SKM, 통합 로그인 API 호출 등에서 사용했던 RestTemplate에 대해 정리해보겠습니다.

## RestTemplate이란?

- Spring에서 제공하는 http 통신에 유용하게 사용할 수 있는 템플릿
- REST API 호출 이후 응답을 받을 때까지 기다리는 동기 방식
- Spring 3.0부터 지원
- HTTP Connection으로 동작

## RestTemplate 동작 원리
![rest_template](https://user-images.githubusercontent.com/38952187/111241070-bc96e780-863f-11eb-877a-32bb70519856.png)

1. 어플리케이션이 RestTemplate를 생성하고, URI, HTTP 메소드 등의 헤더를 담아 요청
2. RestTemplate는 HttpMessageConverter를 사용하여 requestEntity를 요청메세지로 변환
3. RestTemplate는 ClientHttpRequestFactory로 부터 ClientHttpRequest를 가져와서 오쳥을 보냄
4. ClientHttpRequest는 요청 메세지를 만들어 HTTP 프로토콜을 통해 서버와 통신
5. RestTemplate는 ResponseErrorHandler로 오류를 확인하고 있다면 처리로직을 태움
6. ResponseErrorHandler는 오류가 있다면 ClientHttpResponse에서 응답데이터를 가져와서 처리함
7. RestTemplate는 HttpMessageConverter를 이용해서 응답메세지를 java object(Class responseType)로 변환
8. 어플리케이션에 반환

## RestTemplate 메서드

| 메서드 | HTTP | 설명 |
|---|---|---|
| getForObject | GET | 주어진 URL 주소로 HTTP GET 메소드로 요청하고 객체를 결과로 반환받는다 |
| getForEntity | GET | 주어진 URL 주소로 HTTP GET 메소드로 요청하고 ResponseEntity를 결과로 반환받는다 |
| postForLocation | POST | POST 요청을 보내고 결과로 헤더에 저장된 URI를 결과로 반환받는다 |
| postForObject | POST | POST 요청을 보내고 객체를 결과로 반환받는다 |
| postForEntity | POST | POST 요청을 보내고 ResponseEntity를 결과로 반환받는다 |
| delete | DELETE | 주어진 URL 주소로 HTTP DELETE 메소드를 보낸다 |
| headForHeaders | HEADER | 헤더의 모든 정보를 얻을 수 있으면 HTTP HEAD 메소드를 사용한다 |
| put | PUT | 주어진 URL 주소로 HTTP PUT 메소드를 실행한다 |
| patchForObject | PATCH | 주어진 URL 주소로 PATCH 메소드를 실행한다 |
| optionsForAllow | OPTIONS | 주어진 URL 주소에서 지원하는 HTTP 메소드를 조회한다 |
| exchange | any | HTTP 헤더를 새로 만들 수 있고 어떤 HTTP 메소드도 사용 가능하다 |
| execute | any | Request/Response 콜백을 수정할 수 있다 |

## 예제

### getForEntity

```java
    RestTemplate restTemplate = new RestTemplate();
    String url = appUrl + appKey + "/secrets/" + keyId;
    ResponseEntity<String> responseEntity = restTemplate.getForEntity(url, String.class);
```

`getForEntity()` 메소드의 경우에는 응답을 `ResponseEntity` 객체로 받게 됩니다. `getForObject()`와 달리 HTTP 응답에 대한 추가 정보를 담고 있어서 GET 요청에 대한 응답 코드, 실제 데이터를 확인할 수 있습니다. 또한 `ResponseEntity<T> 제네릭 타입`에 따라서 응답을 String이나 Object 객체로 받을 수 있습니다.

### getForObject

```java
    RestTemplate restTemplate = new RestTemplate(); 
    Object obj = restTemplate.getForObject("요청할 URI 주소", "응답 Type", "uriVariables ...");
```

`getForObject()` 메소드의 경우에는 응답을 `Object` 객체로 받게 됩니다.

### exchange

```java
    String url = this.getUrl(containerName, objectName);

    // 오버라이드한 RequestCallback을 사용할 수 있도록 설정
    SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();
    requestFactory.setBufferRequestBody(false);
    this.restTemplate = new RestTemplate(requestFactory);

    // 헤더 생성
    HttpHeaders headers = new HttpHeaders();
    headers.add("X-Auth-Token", token);
    headers.setAccept(Arrays.asList(MediaType.APPLICATION_OCTET_STREAM));

    HttpEntity<String> requestHttpEntity = new HttpEntity<>(null, headers);

    // API 호출, 데이터를 바이트 배열로 받음
    ResponseEntity<byte[]> response = this.restTemplate.exchange(url, HttpMethod.GET, requestHttpEntity, byte[].class);

    // 바이트 배열 데이터를 InputStream으로 만들어 반환
    return new ByteArrayInputStream(response.getBody());
```

 해당 코드는 Object Storage 오픈소스 `downloadObject` 메소드입니다.  
 `HttpEntity`는 header를 새로 만들어 요청하기 위한 객체인데, header를 새로 만들 필요가 없다면 exchange 메소드를 호출할 때 `@Nullable`이기 때문에 `null`도 가능합니다.  
 주어진 URI 변수가 있는 경우 파라미터가 확장됩니다.

### execute

```java
    String url = this.getUrl(containerName,  objectName);

    // InputStream을 요청 본문에 추가할 수 있도록 RequestCallback 오버라이드
    final RequestCallback requestCallback = request -> {
        request.getHeaders().add("X-Auth-Token", token);
        IOUtils.copy(inputStream, request.getBody());
    };

    // 오버라이드한 RequestCallback을 사용할 수 있도록 설정
    SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();
    requestFactory.setBufferRequestBody(false);
    this.restTemplate = new RestTemplate(requestFactory);

    HttpMessageConverterExtractor<String> responseExtractor
            = new HttpMessageConverterExtractor<>(String.class, restTemplate.getMessageConverters());

    // API 호출
    restTemplate.execute(url, HttpMethod.PUT, requestCallback, responseExtractor);
```

 해당 코드는 Object Storage 오픈소스 `uploadObject` 메소드입니다.  
 execute()는 `콜백을 통해 요청 준비와 응답 추출을 완벽하게 제어`하여 요청을 수행하는 가장 일반적인 메서드를 RestTemplate에서 제공합니다. 그래서 getForObject(), put()등은 내부적으로 execute() 메서드를 호출하게 되어 있습니다.  
 주어진 URI 변수가 있는 경우 파라미터가 확장됩니다.

### HTTP Request

```java
    HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory(); 
    factory.setReadTimeout(5000); // 읽기시간초과, ms 
    factory.setConnectTimeout(3000); // 연결시간초과, ms 
    HttpClient httpClient = HttpClientBuilder.create() 
        .setMaxConnTotal(100) // connection pool 적용 
        .setMaxConnPerRoute(5) // connection pool 적용 
        .build(); 
    factory.setHttpClient(httpClient); // 동기실행에 사용될 HttpClient 세팅 
    RestTemplate restTemplate = new RestTemplate(factory);
```

 `HttpComponentsClientHttpRequestFactory`와 `HttpClient`를 사용하여 필요한 설정을 적용하여 HTTP 요청을 보낼 수 있습니다.



## Reference
[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html)
[https://flowarc.tistory.com/entry/Spring-RestTemplate](https://flowarc.tistory.com/entry/Spring-RestTemplate)
[https://advenoh.tistory.com/46](https://advenoh.tistory.com/46)
[https://sjh836.tistory.com/141](https://sjh836.tistory.com/141)
[https://velog.io/@soosungp33/%EC%8A%A4%ED%94%84%EB%A7%81-RestTemplate-%EC%A0%95%EB%A6%AC%EC%9A%94%EC%B2%AD-%ED%95%A8](https://velog.io/@soosungp33/스프링-RestTemplate-정리요청-함)