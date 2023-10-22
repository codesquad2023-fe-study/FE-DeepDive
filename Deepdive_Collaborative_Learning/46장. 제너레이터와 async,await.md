# 46장 제너레이터와 async/await

## 46.1 제너레이터란?

> ES6에 도입된 제어레이터는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수다.

- 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다.
- 제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다.
- 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.

## 46.2 제너레이터 함수의 정의

- 제너레이터 함수는 `function*` 키워드로 선언한다.
- 그리고 하나 이상의 yield 표현식을 포함한다.
- 이것을 제외하면 일반 함수를 정의하는 방법과 같다.

```js
// 제너레이터 함수 선언문
function* genDecFunc() {
  yield 1;
}

// 제너레이터 함수 표현식
const genExpFunc = function* () {
  yield 1;
};

// 제너레이터 메서드
const obj = {
  *genObjMethod() {
    yield 1;
  },
};

// 제너레이터 클래스 메서드
class MyClass {
  *genClsMethod() {
    yield 1;
  }
}
```

> 단, 제너레이터 함수는 화살표 함수로 정의할 수 없고, new 연산자와 함께 생성자 함수로 호출할 수 없다.

## 46.3 제너레이터 객체

```js
function* generatorFunction() {
  // Yield expressions
  yield value1;
  yield value2;
  // ...
}
```

- 이 함수는 호출되면 제너레이터 객체를 반환한다.
- 이 객체는 이터러블이면서 동시에 이터레이터다.
- 이터레이터는 next() 메서드를 가지며, 이 메서드를 호출하면 {value, done} 형태의 객체를 반환한다.
- 여기서 value는 yield로 반환된 값이고, done은 제너레이터 함수가 끝났는지 여부를 나타낸다.

```js
function* simpleGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = simpleGenerator();
console.log(gen.next()); // {value: 1, done: false}
console.log(gen.next()); // {value: 2, done: false}
console.log(gen.next()); // {value: 3, done: false}
console.log(gen.next()); // {value: undefined, done: true}
```

## 46.4 제너레이터의 일시 중지와 재개

- 제너레이터(generator) 함수는 yield 키워드를 사용하여 함수의 실행을 일시 중지(pause)하고,
- 다음에 함수가 호출될 때 일시 중지된 부분부터 계속하여 실행을 재개(resume)할 수 있다.

```js
function* pauseAndResumeGenerator() {
  console.log("Generator started");
  yield 1;
  console.log("Generator resumed");
  yield 2;
  console.log("Generator ended");
}

const gen = pauseAndResumeGenerator();
console.log(gen.next()); // Generator started \n { value: 1, done: false }
console.log(gen.next()); // Generator resumed \n { value: 2, done: false }
console.log(gen.next()); // Generator ended \n { value: undefined, done: true }
```

## 46.5 제너레이터의 활용

### 비동기 처리

- 제너레이터 함수는 next 메서드와 yield 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있다.
- 이 특성을 활용하면 프로미스를 사용한 비동기 처리를 `동기 처리`처럼 구현할 수 있다.
- 즉, 프로미스 후속처리 메서드 then/catch/finally 없이 비동기 처리 결과를 반환하도록 구현할 수 있다.

```js
// node-fetch는 node.js 환경에서 window.fetch 함수를 사용하기 위한 패키지다.
// 브라우저 환경에서 이 예제를 실행한다면 아래 코드는 필요 없다.
// https://github.com/node-fetch/node-fetch
const fetch = require("node-fetch");

// 제너레이터 실행기
const async = (generatorFunc) => {
  const generator = generatorFunc(); // ②

  const onResolved = (arg) => {
    const result = generator.next(arg); // ⑤

    return result.done
      ? result.value // ⑨
      : result.value.then((res) => onResolved(res)); // ⑦
  };

  return onResolved; // ③
};

async(function* fetchTodo() {
  // ①
  const url = "https://jsonplaceholder.typicode.com/todos/1";

  const response = yield fetch(url); // ⑥
  const todo = yield response.json(); // ⑧
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
})(); // ④
```

## 46.6 async / await

- 위 제너레이터를 사용해서 비동기 처리를 동기 처리 처럼 동작하게 구현했지만 가독성이 나쁘다.
- ECMAScript 2017에서 가독성 좋게 async/await를 도입했다.

```
async/await는 프로미스를 기반으로 동작한다.
여기서 후속처리를 담당했던 then/catch/finally의 사용없이
동기 처리 처럼 프로미스가 처리 결과를 반환 하도록 구현할 수 있게 한다.
```

```js
const fetch = require("node-fetch");

async function fetchTodo() {
  const url = "https://jsonplaceholder.typicode.com/todos/1";

  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
}

fetchTodo();
```

### async

- async 함수는 async키워드를 사용해 정의하며 언제나 프로미스를 반환한다.
- async 함수는 암묵적으로 반환값을 resolve하는 프로미스를 반환한다.

```js
// async 사용 예시

// 함수선언문
async function foo(n) {
  return n;
}

// 함수 표현식
const bar = async function (n) {
  return n;
};

// 화살표 함수
const baz = async (n) => n;

// async 메서드
const obj = {
  async foo(n) {
    return n;
  },
};

// 클래스 메서드
class Myclass {
  async bar(n) {
    return n;
  }
}
```

### await

- await 키워드는 프로미스가 settled 상태가 될 때까지 대기하다가
- settled 상태가 되면 프로미스가 resolve한 처리 결과를 반화한다.

```js
// async와 await 사용 예시
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function getApple() {
  await delay(1000);
  return "🍎";
}

async function getBanana() {
  await delay(1000);
  return "🍌";
}

// 반복되는 콜백 함수들을
// function pickFruits() {
//   return getApple().then((apple) => {
//     return getBanana().then((banana) => `${apple} + ${banana}`);
//   });
// }

// 동기적으로 보이게끔 (비동기로 작동) 해준다.
async function pickFruits() {
  const apple = await getApple();
  const banana = await getBanana();
  return `${apple} + ${banana}`;
}

pickFruits().then(console.log);
```

### 에러처리

- 앞서 콜백 패턴의 단점 중 하나가 try...catch 문을 사용해서 에러를 캐치할 수 없다는 것이다.

```js
try {
  setTimeout(() => {
    throw new Error("Error!");
  }, 1000);
} catch (e) {
  // 에러를 캐치하지 못한다
  console.error("캐치한 에러", e);
}
```

- 하지만 async/await에서 에러 처리는 try...catch 문을 사용할 수 있다.

```js
async function fetchData() {
  try {
    const response = await fetch("https://api.example.com/data");

    // 응답 상태 확인
    if (!response.ok) {
      throw new Error("Network response was not ok: " + response.statusText);
    }

    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error("There has been a problem with your fetch operation:", error);
  }
}

fetchData();
```
