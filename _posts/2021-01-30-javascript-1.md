---
title: "Javascript 정리 - 1"
excerpt: "Javascript"

categories:
 - study
tags:
 - web
 - javascript
---

#### 출처: 모던 자바스크립트 Deep Dive-자바스크립트의 기본 개념과 동작원리

## 4장 변수
### 4.1 변수란?
- 변수 : 하나의 값을 저장하기 위해 확보한 메모리 공간 자체 또는 그 메모리 공간을 식별하기 위해 붙인 이름(값의 위치를 가리키는 상징적인 이름)

- 변수에 값을 저장하는 방법
    ```javascript
    var userId = 1;
    var userName = 'Lee';
    //자료구조를 사용하면 여러 개의 값을 저장할 수 있다.
    var user = { id: 1, name: 'Lee'};

    var users = [
        {id = 1, name: 'Lee'},
        {id = 2, name: 'Kim'}
    ];
    ```

- 할당과 참조  
    할당 : 변수에 값을 저장하는 것(대입, 저장)  
    참조 : 변수에 저장된 값을 읽어 들이는 것  

### 4.2 식별자
- 식별자 : 어떤 값을 구별해서 식별할 수 있는 고유한 이름  
-> 메모리 공간에 저장되어 있는 값을 구별해서 식별할 수 있어야 함

 ```text
result --> 0x0669F913   [30]
변수 이름(식별자)   메모리 주소    메모리
```
- 선언 : 자바스크립트 엔진에 식별자의 존재를 알림

### 4.3 변수 선언
- 변수 선언 : 변수를 생성하는 것  
-> 메모리 공간을 확보하고 변수 이름과 확보된 메모리 공간의 주소를 연결해서 저장할 수 있게 준비하는 것
- 변수 선언 키워드 : var, let, const
- ES5 vs ES6
    ```text
    var의 단점은 블록 레벨 스코프를 지원하지 않고 함수 레벨 스코프를 지원한다는 것이다. 이 문제는 의도치 않게 전역 변수가 선언되어 심각한 부작용이 발생하기도 한다.
    단점을 보완하기 위해 let, const 키워드를 도입했다. 하지만, var 키워드가 폐기된 것은 아니다. 왜냐하면, ES6는 ES5 상위 집합이기 때문이다.
    ES6는 기본적으로 하위 호환성을 유지하면서 ES5의 기반 위에 새로운 기능을 추가한 것이다.
    ```
- 키워드
    ```text
    자바 스크립트 코드를 해석하고 실행하는 자바스크립트 엔진이 수행할 동작을 규정한 일종의 명령어
    ```
- 변수 선언 단계
    1. 선언 단계: 변수 이름을 등록해서 자바스크립트 엔진에 변수의 존재를 알림
    2. 초기화 단계: 값을 저장하기 위한 메모리 공간을 확보하고 암묵적으로 undefined를 할당해 초기화  
    -> var는 암묵적으로 초기화 단계를 수행
- 변수이름은 어디에 등록되는가?
    ```text
    변수 이름을 비롯한 모든 식별자는 실행 컨텍스트에 등록된다.
    실행 컨텍스트는 자바스크립트 엔진이 소스코드를 평가하고 실행하기 위해 필요한 환경을 제공하고 코드의 실행 결과를 관리하는 영역
    자바스크립트는 실행 컨텍스트를 통해 식별자와 스코프를 관리
    변수 이름과 변수 값은 실행 컨텍스트 내에 key/value 형식인 객체로 등록되어 관리
    ```
=> 선언을 하지 않은 식별자에 접근하면 ReferenceError(참조 에러)가 발생

### 4.4 변수 선언의 실행 시점과 변수 호이스팅
```javascript
console.log(score); // undefined

var score; // 변수 선언문
```
-> 자바스크립트 코드는 인터프리터에 의해 한 줄씩 순차적으로 실행되므로 console.log(score)가 가장 먼저 실행되고 순차적으로 실행된다. 따라서 console.log(socre)가 실행되는 시점에는 아직 score 변수의 선언이 실행되지 않았으므로 참조 에러가 발생할 것처럼 보인다. 하지만 참조 에러가 발생하지 않고 undefined가 출력된다.  
 그 이유는 __변수 선언이 소스코드가 한 줄씩 순차적으로 실행되는 시점, 즉 런타임이 아니라 그 이전 단계에서 먼저 실행되기 때문__
- 변수 호이스팅: 변수 선언문이 코드의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트의 고유의 특징

### 4.5 값의 할당
- 할당: 할당 연산자 =를 사용(대입, 저장)  
-> 변수 선언은 소스코드가 순차적으로 실행되는 시점인 런다임 이전에 먼저 실행되지만 값의 할당은 소스코드가 순차적으로 실행되는 시점인 런타임에 실행된다.  

```javascript
console.log(score); // undefined

var score = 80; // 변수 선언과 값의 할당

console.log(score); // 80
```

볌수의 선언과 값의 할당을 한 줄로 작성해도 자바스크립트 엔진은 변수의 선언과 값의 할당을 2개의 문으로 나누어 실행한다.  

<img width="568" alt="value" src="https://user-images.githubusercontent.com/38952187/106351248-7bac7300-631e-11eb-9037-861842624519.png">


```javascript
console.log(score); // undefined

score = 80; // 값의 할당
var score; // 변수 선언

console.log(score); // ??
```  
- 결과  
    undefined  
    80  


### 4.6 값의 재할당
```javascript
var score = 80; // 변수 선언과 값의 할당
score = 90; // 값의 재할당
```  
-> var키워드로 선언한 변수는 선언과 동시에 undefined로 초기화되기 때문에 엄밀히 말하면 재할당이다.

- 상수 : 값을 재할당할 수 없어서 변수에 저장된 값을 변경할 수 없음

<img width="851" alt="value2" src="https://user-images.githubusercontent.com/38952187/106351263-92eb6080-631e-11eb-8f09-533c1b42c286.png">


- 가바지 콜렉터 : 애플리케이션이 할당한 메모리 공간을 주기적으로 검사하여 더 이상 사용되지 않는 메모리를 해제하는 기능(메모리 누수 방지)

- 인매니지드 언어와 매니지드 언어
    ```text
    언매니지드 언어: 개발자가 명시적으로 메모리를 할당하고 해제하기 위해 malloc()과 free() 같은 저수준 메모리 제어 기능을 제공 (C 언어 등)
    매니지드 언어: 메모리 할당 및 해제를 위한 메모리 관리 기능을 언어 차원에서 담당, 개발자의 직접적인 메모리 제어를 허용하지 않음
    ```

### 4.7 식별자 네이밍 규칙
- 네이밍 규칙
    - 식별자는 특수문자를 제외한 문자, 숫자, 언더스코어(_), 달러 기호($)를 포함할 수 있다.
    - 단, 식별자는 특수문자를 제외한 문자, 언더스코어(_), 달러 기호($)로 시작해야 한다. 숫자로 시작하는 것은 허용하지 않는다
    - 예약어는 식별자로 사용할 수 없다.
    - 자바스크립트는 대소문자를 구별함.

- 예약어  
|   예약어명   |    예약어명   |    예약어명    |   예약어명    |   예약어명    |   예약어명    |
|:----------:|:-----------:|:-----------:|:-----------:|:-----------:|:-----------:|
|    await   |    break    |     case    |    catch   |    class    |    const    |
|  continue  |   debugger  |   default   |   delete   |     do      |     else    |
|    enum    |     export  |   extends   |   false    |   finally   |     for     |
|  function  |      if     | implements* |   import   |     in      |  instanceof |
| interface* |     let*    |     new     |    null    |   package*  |   private*  |
| protected* |   public*   |    return   |    super   |    static*  |    switch   |
|    this    |    throw    |     true    |     try    |    typeof   |      var    |
|    void    |    while    |     with    |    yield*  |             |             |

| Tables   |      Are      |  Cool |
|----------|:-------------:|------:|
| col 1 is |  left-aligned | $1600 |
| col 2 is |    centered   |   $12 |
| col 3 is | right-aligned |    $1 |

- 네이빙 컨벤션 유형
    - 카멜케이스: ex) firstName
    - 스네이크 케이스: ex) first_name
    - 파스칼 케이스: ex) FirstName
    - 헝가리언 케이스: ex) strFirstName(type + identifier)