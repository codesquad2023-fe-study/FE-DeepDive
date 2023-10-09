# 44장 REST API

## 44.1 REST란?

REST는 HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처이며  
REST API는 REST 기반으로 API를 구현한 것을 의미한다.

> REST(Representational State Transfer)는 웹 서비스의 아키텍처적인 스타일이며,  
> 클라이언트와 서버 간의 `통신을 위한 규칙`과 `제약 조건`을 정의한다.

### 44.1.1 REST의 역사

REST 이전에 WEB이 등장했고 사람들은 WEB(인터넷)에서 정보를 공유하는 방법을 찾았고 그렇게 정보를 하이퍼텍스트로 연결하는 방식이 나타났다.

1. WEB(1991)  
   Q : 어떻게 인터넷에서 정보를 공유할 것인가?  
   A : 정보들을 하이퍼텍스트로 연결한다.

- 표현형식 : HTML
- 식별자 : URI
- 전송방식 : HTTP

> 클라이언트와 서버 간의 정보 교환을 위한 프로토콜인 HTTP의 등장  
> HTTP : HyperText Transfer Protocol

2. HTTP / 1.0 (1994-1996)  
   그렇게 HTTP는 급속도로 성장하고 전세계에서 사용하게 되었고,  
   로이 필딩(Roy Fielding)이 HTTP 1.0을 정립하고 명세를 정리한 논문을 발표하는데 그것이 HTTP Object Model로 나중에 REST가 되었다.

3. REST (2000)  
   로이 필딩은 이미 전세계로 퍼진 Web을 망가트리지 않으면서 확장성과 성능을 개선하기 위해 HTTP의 기존 설계 원칙을 재사용하는 방법인 REST 아키텍처 스타일을 소개했다.

> REST는 `자원(URI)의 표현`과 `상태(State) 전달`을 위한 통신을 기반으로 하며,  
> 웹의 기본 원칙을 따르고, 분산 시스템과 클라이언트-서버 간의 상호작용을 단순화 하는 아키텍쳐 스타일을 제공한다.

4. 웹 API  
   2000년대 후반부터 웹 서비스의 중요성이 증가하면서, `RESTful한 웹 API`의 수요가 커지게 되었다.  
   REST를 따르는 웹 API는 클라이언트 서버 간의 데이터 교환을 위한 표준 인터페이스를 제공하며,  
   다양한 애플리케이션과 플랫폼 간의 통합을 가능하게 해준다.

### 44.1.2 RESTful한 웹 API란?

- REST 아키텍처 스타일을 따르는 웹 API를 의미한다.
- 클라이언트와 서버 간의 통신을 위한 규칙과 제약 조건을 따르며,
- 자원의 표현과 상태 전달을 기반으로 한다.
- ex) Twitter API, GitHub API, Google Maps API 등

## 44.2 REST API의 구성

- 자원(resource), 행위(verb), 표현(representation)의 3가지 요소로 구성된다.
- REST는 자체 표현 구조(self-descriptiveness)로 구성되어 REST API만으로 HTTP 요청의 내용을 이해할 수 있다.

| 구성 요소 | 내용                           | 표현 방법        |
| --------- | ------------------------------ | ---------------- |
| 자원      | 자원                           | URI(엔드포인트)  |
| 행위      | 자원에 대한 행위               | HTTP 요청 메서드 |
| 표현      | 자원에 대한 행위의 구체적 내용 | 페이로드         |

## 44.3 REST API 설계 원칙

- REST의 중요한 기본 원칙은 **URI는 리소스를 표현**하는데 집중하고 **행위에 대한 정의는 HTTP 요청 메서드**를 통해 하는 것이다.

### URI는 리소스를 표현해야 한다.

- URI는 리소스를 표현하는데 중점을 두어야 한다.
- 리소스를 식별할 수 있는 이름은 명사를 사용한다.
  - 이름에 get 같은 행위에 대한 표현이 들어가서는 안된다.

```shell
# bad
GET /getTodos/1
GET /todos/show/1

# good
GET /todos/1
```

### 리소스에 대한 행위는 HTTP 요청 메서드로 표현한다.

- HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적(리소스에 대한 행위)을 알리는 방법이다.
- 주로 5가지 요청 메서드를 사용하여 CRUD를 구현한다.

  | HTTP 요청 메서드 | 종류           | 목적                  | 페이로드 |
  | ---------------- | -------------- | --------------------- | -------- |
  | GET              | index/retrieve | 모든/특정 리소스 취득 | ❌       |
  | POST             | create         | 리소스 생성           | ⭕️      |
  | PUT              | replace        | 리소스 전체 교체      | ⭕️      |
  | PATCH            | modify         | 리소스 일부 수정      | ⭕️      |
  | DELETE           | delete         | 모든/특정 리소스 삭제 | ❌       |

- 리소스에 대한 행위는 HTTP 요청 메서드를 통해 표현하며 URI에 표현하지 않는다.

  ```shell
  # bad
  GET /todos/delete/1

  # good
  DELETE /todos/1
  ```

## 44.4 JSON Server를 이용한 REST API 실습

### 44.4.1. JSON Server란?

JSON Server는 개발자가 간단하게 REST API를 모방하고 구축할 수 있는 도구다.  
JSON Server를 사용하면 별도의 서버를 구축하지 않고도 가짜 REST API를 생성할 수 있다.

JSON Server는 JSON 파일에 저장된 데이터를 기반으로 RESTful한 API를 제공한다.  
개발자는 JSON 파일에 필요한 데이터를 작성하고, 이를 JSON Server에 제공하면 서버가 해당 데이터를 가지고 API를 생성한다.  
이후 클라이언트는 HTTP 요청을 통해 해당 API에 접근하여 데이터를 조회, 생성, 업데이트, 삭제할 수 있다.

### 44.4.2 JSON Sever 설치

```
mkdir json-server-exam && cd json-server-exam
npm init -y
npm install json-server --save-dev
```

### 44.4.3 db.json 파일 생성

```js
// 리소스를 제공하는 데이터베이스 역할
{
  "todos": [
    {
      "id": 1,
      "content": "HTML",
      "completed": true
    },
    {
      "id": 2,
      "content": "CSS",
      "completed": false
    },
    {
      "id": 3,
      "content": "JavaScript",
      "completed": true
    }
  ]
}
```

### 44.4.4 JSON Server 실행

```js
// json-server --watch db.json로 실행한다. 터미널로 실행해도 된다.
// watch 옵션을 추가하면 JSON Server가 db.json 파일의 변경을 감지한다.
{
  "name": "json-server-exam",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "json-server --watch db.json",
  },
  "devDependencies": {
    "json-server": "^0.17.1"
    }
}
```

터미널에서 npm start로 JSON Server를 실행한다.

<img src="https://velog.velcdn.com/images/sarang_daddy/post/f6c265b0-05b7-4dcb-8967-388b7ea56178/image.png" width="50%">

### 44.4.5 GET 요청

- todos 리소스에서 모든 todo를 취득해보자.

```js
<!DOCTYPE html>
<html lang="en">
  <head> </head>
  <body>
    <script>
      const xhr = new XMLHttpRequest();

      xhr.open("GET", "/todos");

      xhr.send();

      xhr.onload = () => {
        if (xhr.status === 200) {
          document.querySelector("pre").textContent = xhr.response;
        } else {
          console.error("Error", xhr.status, xhr.statusText);
        }
      };
    </script>
  </body>
</html>
```

<img src="https://velog.velcdn.com/images/sarang_daddy/post/3c9c52be-24e6-4792-afc9-e21e26e1043a/image.png" width="50%">

- todos의 리소스에서 id로 특정 todo를 취득할 수 도 있다.

### 44.4.6 POST 요청

- todos 리소스에 새로운 todo를 생성한다.

```js
const xhr = new XMLHttpRequest();

xhr.open("POST", "/todos");

// 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입 지정
xhr.setRequestHeader("content-type", "application/json");

// 새로운 todo를 생성하기 위해 페이로드를 서버에 전송해야 한다.
xhr.send(JSON.stringify({ id: 4, content: "Angular", completed: false }));
```

<img src="https://velog.velcdn.com/images/sarang_daddy/post/5cf3aa4d-c36f-4dfc-8a29-afde5c3ec238/image.png" width="50%">

### 44.4.7 PUT 요청

- PUT은 특정 리소스 전체를 교체할 때 사용한다.

```js
const xhr = new XMLHttpRequest();

// todos 리소스에서 id로 todo를 특정하여 id를 제외한 리소스 전체를 교체
xhr.open("PUT", "/todos/4");

// 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입 지정
xhr.setRequestHeader("content-type", "application/json");

// 새로운 todo를 생성하기 위해 페이로드를 서버에 전송해야 한다.
xhr.send(JSON.stringify({ id: 4, content: "React", completed: true }));
```

<img src="https://velog.velcdn.com/images/sarang_daddy/post/014d7101-53fc-41bf-8f01-a8fa5a616b08/image.png" width="50%">

### 44.4.7 PATCH 요청

- PATCH는 특정 리소스의 일부를 수정할 떄 사용한다.

```js
const xhr = new XMLHttpRequest();

// todos 리소스에서 id로 todo를 특정하여 completed만 수정
xhr.open("PATCH", "/todos/4");

// 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입 지정
xhr.setRequestHeader("content-type", "application/json");

// 새로운 todo를 생성하기 위해 페이로드를 서버에 전송해야 한다.
xhr.send(JSON.stringify({ completed: false }));
```

<img src="https://velog.velcdn.com/images/sarang_daddy/post/939e1ca9-d8a7-4499-a03a-bdeea79c3102/image.png" width="50%">

### 44.4.8 DELETE 요청

- todo 리소스에서 id를 사용하여 todo를 삭제한다.

```js
const xhr = new XMLHttpRequest();

// todos 리소스에서 id로 todo를 삭제한다.
xhr.open("DELETE", "/todos/4");

xhr.send();
```

<img src="https://velog.velcdn.com/images/sarang_daddy/post/1edb127b-23f7-4de2-9946-a0dd137a64d6/image.png" width="50%">
