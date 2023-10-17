# 43장. Ajax

Ajax란, 자바스크립트를 사용하여 브라우저가 데이터를 비동기로 요청하고, 서버에서 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식을 말한다.

이전의 웹페이지는 변경이 필요없는 페이지까지 모두 포함한 HTML을 서버로 부터 매번 다시 전송받기때문에 불필요한 데이터 통신이 발생했고 이로인해 화면전환이 일어날 때마다 깜빡이는 현상이 발생하였다. Ajax는 이러한 문제를 해결하여 서버로부터 변경이 필요한 데이터만 비동기로 전송받고 변경할 필요가 없는 부분은 리렌더링하지 않고 변경할 필요가 있는 부분만 한정적으로 렌더링하는 방식으로 동작한다.

#### Ajax의 장점
1. 변경에 필요한 데이터만 서버로부터 전달받아 불필요한 데이터 통신이 발생하지 않는다.
2. 변경이 필요없는 부분은 리렌더링하지 않아 깜빡이는 현상이 없다.
3. 서버와 클라이언트가 비동기로 동작하기 때문에 서버에 요청후 블로킹이 발생하지 않는다.

## JSON

클라이언트와 서버간의 HTTP 통신을 위한 텍스트 데이터 포맷으로 대부분의 언어에서 사용할 수 있다.  
반드시 key와 string은 큰따옴표로 사용해야한다.

#### JSON 표기방식
자바스크립트 객체 리터럴과 유사하게 키와 값으로 구성된 순수한 텍스트

```js
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"]
}
```


### `JSON.stringify()`
> 객체를 JSON 포맷의 문자열로 변환하는 메서드


#### 직렬화
클라이언트가 서버로 객체를 전송할 때 객체를 문자열화하는 것

```js
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis']
}

const json = JSON.stringify(obj);
console.log(typeof json, json); // string, {"name":"Lee","age":20,"alive":true,"hobby":["traveling","tennis"]}
```


### `JSON.parse()`
> JSON 포맷의 문자열을 객체로 변환하는 메서드


#### 역직렬화
서버로부터 클라이언트가 전달받은 문자열의 JSON 데이터를 객체화하는 것

```js
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis']
}

const json = JSON.stringify(obj); // 🌟 객체를 JSON 포맷의 문자열로 변환 (직렬화)
const parsed = JSON.parse(json); // 🌟 JSON 포맷의 문자열을 객체로 변환 (역직렬화)
```

<br>

### XMLHttpRequest
> 자바스크립트를 사용하여 HTTP 요청을 전송할 수 있도록 메서드와 프로퍼티를 제공하는 객체


#### XMLHttpRequest 생성자 함수

```js
const xhr = new XMLHttpRequest();
```


#### XMLHttpRequest의 프로퍼티와 메서드

프로토타입 프로퍼티 |	설명
--|--
readyState | HTTP 요청의 현재 상태를 나타내는 정수, 다음과 같은 XMLHttpRequest의 정적 프로퍼티를 값으로 갖는다.  (UNSENT: 0 / OPENED: 1 / HEADERS_RECEIVED: 2 / LOADING: 3 / DONE: 4)
status | HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는 정수 ex) 200
statusText | HTTP 요청에 대한 응답 메시지를 나타내는 문자열 ex) "OK"
responseType | HTTP 응답 타입 ex) document, json, text, blob, arraybuffer
response | HTTP 요청에 대한 응답 몸체. responseType에 따라 타입이 다르다.
responseText | 서버가 전송한 HTTP 요청에 대한 응답 문자열


#### XMLHttpRequest의 이벤트 핸들러 프로퍼티

이벤트 핸들러 프로퍼티 |	설명
--|--
onreadystatechange | readyState 프로퍼티 값이 변경된 경우
onloadstart | HTTP 요청에 대한 응답을 받기 시작한 경우
onprogress | HTTP 요청에 대한 응답을 받는 도중 주기적으로 발생
onabort | abor 메서드에 의해 HTTP 요청이 중단된 경우
onerror | HTTP 요청에 에러가 발생한 경우
onload | HTTP 요청이 성공적으로 완료한 경우
ontimeout | HTTP 요청 시간이 초과한 경우
onloadend | HTTP 요청이 완료한 경우. HTTP 요청이 성공 또는 실패하면 발생


## HTTP 요청 전송 과정

HTTP 요청을 전송하는 경우 다음과 같은 순서로 진행된다.
1. `XMLHttpRequest.prototype.open` 메서드로 HTTP 요청을 초기화
    ```js
    xhr.open(method, url[, async])
    ```
    - method: HTTP 요청 메서드 (클라이언트가 서버에게 요청의 종류와 목적을 알리는 수단)
      - GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE, CONNECT 를 사용하여 CRUDF를 구현
    - url: HTTP 요청을 전송할 URL
    - async: 비동기 방식으로 요청을 전송할지 여부를 지정하는 불리언 값. 기본값은 true
2. 필요할 경우 `XMLHttpRequest.prototype.setRequestHeader` 메서드로 특정 HTTP 요청의 헤더 값을 설정
   - 반드시 open 메서드를 호출한 이후 호출되어야한다.
   - 자주사용하는 헤더로는 Content-type, Accept, Authorization 등이 있다.
   - Content-type
     - 전송하는 데이터의 MIME 타입을 지정
     - text/*, application/json, multipart/formed-data 등이 있다.
3. `XMLHttpRequest.prototype.send` 메서드로 HTTP 요청을 전송
   - 초기화된 HTTP요청을 서버에 전송
   - 이때 페이로드가 객체인경우에는 반드시 JSON.stringify로 직렬화한다음 전달해야함
   - GET 메서드의 경우 : 데이터를 URL의 일부인 쿼리 스트링으로 전송
   - POST 메서드의 경우 : 데이터를 request body에 담아 전송


## HTTP 응답 처리 과정

XMLHttpRequest 객체가 발생시키는 이벤트를 캐치하여 HTTP 응답을 처리해야한다. XMLHttpRequest 객체는 onreadystatechange, onload, onerror 같은 이벤트 핸들러 프로퍼티를 갖는다. 이 이벤트 핸들러 프로퍼티 중에서 HTTP 요청의 현재 상태를 나타내는 readyState 프로퍼티 값이 변경된 경우 발생하는 readystatechange 이벤트를 캐치하여 다음과 같이 HTTP 응답을 처리할 수 있다.


send메서드를 통해 HTTP요청을 전송하면 서버에서 응답을 반환한다. 이때 서버에서 반환한 응답의 상태를 확인하는 방법으로 readystatechange 이벤트 또는 load 이벤트를 사용할 수 있다.
- readystatechange 이벤트 : HTTP 요청의 현재 상태 readyState 프로퍼티가 변경될 때마다 발생하는 이벤트
  - onreadystatechange 이벤트 핸들러 프로퍼티에 할당한 이벤트 핸들러는 HTTP 요청의 현재 상태를 나타내는 xhr.readyState가 XMLHttpRequest.DONE인지 확인하여 HTTP 요청이 완료되었는지 확인한다.
  - 서버 응답 완료시 HTTP 상태코드를 나타내는 xhr.status = 200 을 확인하여 응답이 성공적으로 완료된 경우 xhr.response에서 서버가 전송한 데이터를 취득할 수 있게되고 200 이 아닐 경우 에러를 처리할 수 있다.
- load 이벤트 : HTTP 요청이 성공적으로 완료된 경우 발생 (xhr.readyState, XMLHttpRequest.DONE인지 확인 과정이 필요하지 않음)