---
title: "코드 리뷰 정리"
excerpt: "Basecamp 교육"

categories:
 - study
tags:
 - code review
---
# 코드 리뷰 & 정적 분석 결과
[출처: 코드 리뷰 & 정적 분석 결과 - 정명주 책임님]

### 코드 리뷰
- e.printStackTrace() 사용
    - 서버 로그로만 남기 때문에 오류가 발생해도 처리가 안된다.
    - io를 동기로 사용한다. (상용화 서비스에서 사용할 수 없음)
    - 1000건의 트랜잭션에 대해서 에러를 처리하면 io디스크에 문제가 발생할 수 있다.
    - e.printStackTrace()보다 log.info()를 사용하는 것이 좋다.

- System.out / System.err 사용
    - io를 동기로 사용한다. (성능적인 이슈가 발생한다.)
    - 자료구조를 조회하는 부분을 System.out을 찍으면 안된다. (이 출력 코드에서 nullpointexception이 날 수 있다.)
    - System.out은 git의 전 commit을 확인할 수 있기 때문에 주석할 필요가 없다.
    
- 넓은 스코프
    - 스코프는 최대한 좁게 해야 한다. -> ```private```

- 사용하지 않는 필드
- 언어마다 고유한 네이밍 규칙을 따라야 한다.
- 줄임말 지양 / 접두어도 지양 / '_' 지양
    - 동적과 정적의 차이 (컴파일 유무) (ex) m_xxxx )
- 일관성 없는 네이밍룰 -> 네이밍룰을 강제하는 tool이 있음
    - 축약어라도 앞글자만 대문자로 쓰는것이 요즘 트랜트임.
- 명세서를 읽어보자
- 들여쓰기 신경쓰기
- 타입은 가능한 인터페이스로 하는 것이 좋다
- 기술 혼재는 지양 ( ex) JSONObject / Gson )
- git을 사용하기 때문에 코드를 정리하여 주석을 없애는 것이 좋다.
- 배포 환경 별 주석 변경은 지양   

```java
registrations.add(
    ClientRegistration
        .withRegistrationId("tomoro")
        .clientAuthenticationMethod(ClientAuthenticationMethod.POST)
        .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
        .redirectUriTemplate("{baseUrl}/welcome")
        .scope("read", "write")
        //.authorizationUri("http://10.161.106.22:8080/oauth/authorize")
        //.tokenUri("http://10.161.106.22:8080/oauth/token")
        //.userInfoUri("http://10.161.106.22:8080/user")
        .authorizationUri("http://localhost:8080/oauth/authorize")
        .tokenUri("http://localhost:8080/oauth/token")
        .userInfoUri("http://localhost:8080/user")
        .userNameAttributeName("userId")
        .clientName("tomoro")
        .clientId(tomoroClientId)
        .clientSecret(tomoroClientSecret)
        .build()
```

- 너무 자세한 주석은 필요없다 (네이밍이 구체적이면 괜찮음)
- 자바에서 == 는 메모리 주소 비교이기 때문에 equals로 비교(값)해야한다. (숫자 비교에도 사용)
    - Objects.equals(A, B)를 사용하면 null이여도 오류가 나지 않음
- magic number로 예외처리 하지 않고 Exception을 상위로 throws 해줘야 한다.
- 응집력 있게 구현하는 것이 좋다.
    - ex) receiver.getLocalPart() + "@" + receiver.getDomain() 보다 receiver.getEmail()로 하는 것이 좋다.
- 백엔드에서는 데이터 위주로 하고 표현을 위한 부분은 프론트앤드에게 전가하는 것이 좋을 수 있다.
- if != 0 보다 !{}.isEmpty()가 좋다.
- 불필요한 else는 가독성을 따지더라도 빼는 것이 좋다.
- Boolean을 return값으로 사용하는 것은 지양 -> boolean으로 return 하는 것이 좋음
- ~~~Check는 메소드보다는 isValid로 하는 것이 좋음 (명사 동사)
- ||와 &&는 잘 사용해야함
- if else는 indention이 들어가면 들어갈 수록 좋지 않다.
    - 부정적인 상황을 처리해주는 것이 좋음
- if를 서술적으로 && && 로 if 조건으로 사용하지 않고 메소드를 만들어서 사용하는 것을 권장
- 문자열 보다 클래스 사용하는 것을 권장 ("utf-8" -> StandardCharsets.UTF_8)
- 상수 클래스는 꼭 final을 사용해야 한다.
- 상수는 인스턴스 상수를 쓰는 것보다 차라리 static을 넣는 것이 더 효율적이다.
- 최대한 생성자 주입을 사용하는 것이 좋다.
- 에러들을 먼저 처리하고 마지막에 성공을 처리하는 것이 좋다.
- 상수면 UPPER_CASE를 사용
- List를 final해도 List 내의 Element는 수정될 수 있다. -> unmodifiList를 사용해야 한다.
- 상수 보다는 enum을 사용 (실수를 줄일 수 있다)
- 단수/복수 구분
- Collection 계열은 Null 대신 Empty를 반환하는 것을 권장 (JAVA) => EmptyList Collection을 반환
- 객체는 새로 생성하지 않고 되도록 재사용도록 함 (객체를 생성하는 것은 큰 비용)
- ObjectMapper는 thread safe임.
- Wrapper클래스 -> {숫자}L 권장
- 방치된 예외 처리 -> Exception은 세세하게 처리하는 것이 좋음 (최소한 Log를 찍어야 함)
- 너무 길고 복잡한 코드는 지양 (추상화 레벨을 생각해서 메소드를 작성)
- 재사용을 하지 않더라도 코드의 가독성을 위해 메소드로 나누는 것이 좋을 수 있음.
- resource close는 필수
- try-with-resources를 권장 (AutoClose)
- 여러 개의 리소스 close할 때는 각 리소스별로 try catch가 필요
- 일관성 없는 응답 ( -1은 유저가 없음 -> UserDto에서)
- 로컬변수는 StringBuffer 대신 StringBuilder를 써야함 (99% 상황에 StringBuilder 사용)
- StringBuffer은 기본적으로 동기화 처리가 된다 (인스턴스 변수로 사용 가능)
- Json을 문자열로 처리하는 것은 지양 (기술이 변할 수 있기 때문)
- Optional에 get()은 쓰면 안됨
- CascadeType.ALL은 사용하는 곳이 정해져 있음


### 좋은 코드
- Log
    - 적당한 로그 레벨 사용 (error, info 등 다양한 로그를 모두 찍게 하고 배포 때 로그레벨을 설정해주는 것이 좋다.)
    - 버퍼에 로그를 쌓은 후 한번에 출력함
    - 비동기 처리
    - 개발 시 Log를 사용하는 것이 좋다.
    - logback / log4j2 둘 중 무엇을 사용해도 괜찮음
    - 가독성을 높여라. (ex 로그인한 아이디가 누구였고 어떤 메소드를 사용했을 때 에러가 났다)
    - concat ( + )를 사용한 로그는 사용하지 않는 것이 좋다. ( 문자열을 재할당 하기 때문 )
    - formating을 사용하면 메모리 재할당이 일어나지 않는다.
    - 메소드를 호출하여 로그를 찍는 경우에는 조건을 줘서 상황에 따라 호출되게 하는 것이 좋다. (로그를 위해 자원을 많이 쓰는 것을 방지)

```java
    logger.info("cookie : " + loginCookie);
    logger.info("cookie.value : " + loginCookie.getValue());
    // 개선
    logger.info("cookie : {}", loginCookie);
    if (logger.isInfoEnable()) {
        logger.info("cookie.value : {}", loginCookie.getValue()); // 메서드가 호출되는 경우
    }
```

- 좋은 로깅
```java
    try {
        // do something
    } catch (MailParseException e) {
        log.error("[ERROR] 메일 파싱에 오류가 발생하였습니다. {}", mail.getId(), e);
    }
```

- 다양한 단어 사용
- 주석에 코드 결함을 설명
    - TODO, HACK