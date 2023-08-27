# 28장 Number

표준 빌트인 객체 Number는 원시 타입인 숫자를 다룰 때 유용한 프로퍼티와 메서드를 제공한다.

## 28.1 Number 생성자 함수

표준 빌트인 객체인 Number 객체는 생성자 함수다.  
따라서 new연산자와 함께 호출하여 Number 인스턴스를 생성할 수 있다.

```js
const numObj = new Number();
console.log(numObj); // Number {[[PrimitiveValue]]: 0}

const numObj = new Number(10);
console.log(numObj); // Number {[[PrimitiveValue]]: 10}

let numObj = new Number("10");
console.log(numObj); // Number {[[PrimitiveValue]]: 10}

numObj = new Number("Hello");
console.log(numObj); // Number {[[PrimitiveValue]]: NaN}
```

- Number 생성자 함수를 new 연사자와 함께 호출하면 인수를 숫자로 변환 한 `레퍼 객체`가 생성된다.

### 래퍼 객체(Wrapper object)

- 원시 데이터 유형은 일반적으로 객체가 아니다.
- 래퍼 객체는 원시 데이터 유형을 객체처럼 다룰 수 있게 하는 특별한 객체다.
- 즉, 원시 데이터에서 메서드를 호출할 수 있게 해준다.
- 원시 값에 메서드를 사용하면 자바스크립트는 암죽적으로 원시값의 래퍼 객체를 생성한다.

```js
var str = "Hello, World!";
var length = str.length; // 이 때, 문자열 원시 값이 일시적인 String 객체로 변환된다.
```

> 래퍼 객체는 원시 값을 객체로 다루기 위해 일시적으로 생성되고 메서드 사용 후 가비지 컬렉션의 대상이 된다.

Number 생성자 함수를 new 연산자 없이 호출하면 Number 인스턴스가 아닌 숫자를 반환한다.

```js
// 문자열 타입 => 숫자 타입
Number("0"); // -> 0
Number("-1"); // -> -1
Number("10.53"); // -> 10.53

// 불리언 타입 => 숫자 타입
Number(true); // -> 1
Number(false); // -> 0
```

## 28.2 Number 프로터피

### 28.2.1 Number.EPSILON

- `Number.EPSILON`은 `매우 작은 양수 상수`다.
- 1과 1보다 큰 숫자 중에서 가장 작은 숫자와의 차이와 같다.
- 이 값은 `부동 소수점` 연산에서 발생할 수 있는 반올림 오차를 비교하거나 처리할 때 유용하다.

정수는 2진법으로 저장 가능하지만, 부동 소수점은 2진법으로 변활했을 때 무한 소수가 되버린다.

```js
0.1 + 0.2; // -> 0.30000000000000004
0.1 + 0.2 === 0.3; // -> false
```

`Number.EPSILON`를 사용하여 부동 소수점으로 발생하는 오차를 해결할 수 있다.

```js
function isEqual(a, b) {
  // a와 b를 뺀 값의 절대값이 Number.EPSILON보다 작으면 같은 수로 인정한다.
  return Math.abs(a - b) < Number.EPSILON;
}

isEqual(0.1 + 0.2, 0.3); // -> true
```

### 28.2.2 Number.MAX_VALUE

- `Number.MAX_VALUE`는 자바스크립트에서 표현할 수 있는 가장 큰 양수 값이다.
- `Number.MAX_VALUE`보다 큰 숫자는 Infinity다.

```js
Number.MAX_VALUE; // -> 1.7976931348623157e+308
Infinity > Number.MAX_VALUE; // -> true
```

### 28.2.3 Number.MIN_VALUE

- `Number.MIN_VALUE`는 자바스크립트에서 효현할 수 있는 가장 작은 양수 값이다.
- `Number.MIN_VALUE`보다 작은 숫자는 0이다.

```js
Number.MIN_VALUE; // -> 5e-324
Number.MIN_VALUE > 0; // -> true
```

### 28.2.4 Number.MAX_SAFE_INTEGER

`Number.MAX_SAFE_INTEGER`는 자바스크립트에서 안전하게 표현할 수 있는 가장 큰 정수값이다.

```js
Number.MAX_SAFE_INTEGER; // -> 9007199254740991
```

### 28.2.5 Number.MIN_SAFE_INTEGER

`Number.MIN_SAFE_INTEGER`는 자바스크립트에서 안전하게 표현할 수 있는 가장 작은 정수값이다.

```js
Number.MIN_SAFE_INTEGER; // -> -9007199254740991
```

### 28.2.6 Number.POSITIVE_INFINITY

`Number.POSITIVE_INFINITY`는 양의 무한대를 나타내는 숫자값 Infinity와 같다.

```js
Number.POSITIVE_INFINITY; // -> Infinity
```

### 28.2.7 Number.NEGATIVE_INFINITY

`Number.NEGATIVE_INFINITY`는 음의 무한대를 나타내는 숫자값 Infinity와 같다.

```js
Number.NEGATIVE_INFINITY; // -> - Infinity
```

### 28.2.8 Number.NaN

`Number.NaN`은 숫자가 아님(Not-a-Number)를 나타내는 숫자값이다.

```js
Number.NaN; // -> NaN
```

## 28.3 Number 메서드

### 28.3.1 Number.isFinite

ES6에서 도입된 `Number.isFinite` 정적 메서드는 인수로 전달된 숫자값이 정상적인 `유한수`인지 검사하여 그 결과를 불리언 값으로 반환한다.

```js
// 인수가 정상적인 유한수이면 true를 반환한다.
Number.isFinite(0); // -> true
Number.isFinite(Number.MAX_VALUE); // -> true
Number.isFinite(Number.MIN_VALUE); // -> true

// 인수가 무한수이면 false를 반환한다.
Number.isFinite(Infinity); // -> false
Number.isFinite(-Infinity); // -> false

// 인수가 NaN이면 false를 반환한다.
Number.isFinite(NaN); // -> false
```

`Number.isFinite`메서드는 `빝트인 전역 함수 isFinite`와 차이점이 있다.

```js
// Number.isFinite는 인수를 숫자로 암묵적 타입 변환하지 않는다.
Number.isFinite(null); // -> false

// isFinite는 인수를 숫자로 암묵적 타입 변환한다. null은 0으로 암묵적 타입 변환된다.
isFinite(null); // -> true
```

### 28.3.2 Number.isInteger

ES6에서 도입된 `Number.isInteger` 정적 메서드는 인수로 전달된 숫자값이 `정수`인지 검사하여 그 결과를 불리언 값으로 반환한다.

```js
// 인수가 정수이면 true를 반환한다.
Number.isInteger(0); // -> true
Number.isInteger(123); // -> true
Number.isInteger(-123); // -> true

// 0.5는 정수가 아니다.
Number.isInteger(0.5); // -> false
// '123'을 숫자로 암묵적 타입 변환하지 않는다.
Number.isInteger("123"); // -> false
// false를 숫자로 암묵적 타입 변환하지 않는다.
Number.isInteger(false); // -> false
// Infinity/-Infinity는 정수가 아니다.
Number.isInteger(Infinity); // -> false
Number.isInteger(-Infinity); // -> false
```

### 28.3.3 Number.isNaN

ES6에서 도입된 `Number.isNaN` 정적 메서드는 인수로 전달된 숫자값이 NaN인지 검사하여 그 결과를 불리언값으로 반환한다.

```js
// 인수가 NaN이면 true를 반환한다.
Number.isNaN(NaN); // -> true
```

❗️ `Number.isNaN` 메서드는 `빌트인 전역 함수 isNaN`과 차이점이 있다.

- `Number.isNaN 메서드`는 값이 정확히 NaN인 경우에만 true를 반환한다.
- `전역 함수 isNaN`은 값이 `숫자로 변환`될 수 없으면 true를 반환한다.

```js
// Number.isNaN 사용
console.log(Number.isNaN(NaN)); // true
console.log(Number.isNaN("Hello")); // false
console.log(Number.isNaN(undefined)); // false
console.log(Number.isNaN(123)); // false

// 전역 isNaN 함수 사용
console.log(isNaN(NaN)); // true
console.log(isNaN("Hello")); // true (문자열이 숫자로 변환될 수 없음)
console.log(isNaN(undefined)); // true (undefined가 숫자로 변환될 수 없음)
console.log(isNaN(123)); // false
```

> 💡 값이 정확히 NaN인지 확인하려면 `Number.isNaN`을 사용하는 것이 더 안전하고 정확하다.

### 28.3.4 Number.isSafeInteger

`Number.isSafeInteger` 정적 메서드는 인수로 전달된 숫자값이 정수이면서 자바스크립트에서 안전하게 표현할 수 있는 범위 내에 있는지 확인한다.

> 안전한 정수값은 -(2<sup>53</sup> - 1)과 2<sup>53</sup> - 1 사이의 정수값이다.

```js
// 0은 안전한 정수이다.
Number.isSafeInteger(0); // -> true
// 1000000000000000은 안전한 정수이다.
Number.isSafeInteger(1000000000000000); // -> true

// 10000000000000001은 안전하지 않다.
Number.isSafeInteger(10000000000000001); // -> false
// 0.5은 정수가 아니다.
Number.isSafeInteger(0.5); // -> false
// '123'을 숫자로 암묵적 타입 변환하지 않는다.
Number.isSafeInteger("123"); // -> false
// false를 숫자로 암묵적 타입 변환하지 않는다.
Number.isSafeInteger(false); // -> false
// Infinity/-Infinity는 정수가 아니다.
Number.isSafeInteger(Infinity); // -> false
```

---

아래 부터는 Number의 프로토타입 메서드다.  
❗️ 숫자 리터럴과 함께 Number 프로토타입 메서드를 사용할 경우 주의 사항이 있다.

<img src="https://velog.velcdn.com/images/sarang_daddy/post/d1533a33-3749-4951-b0e8-d33b79ccb497/image.png" width="70%">

- 숫자 뒤의 `.`은 소수 구분 기호일 수고 있고 객체 프로퍼티에 접근하기 위한 접근 연산자일 수도 있다.
- 소수 구분 기호가 아닌 접근 연산자를 사용할 의도라면 `그룹 연산자`를 사용하자

```js
console.log((77).toExponential()); // '7.7e+1'
console.log((10).toString()); // '10'
```

### 28.3.5 Number.prototype.toExponential

- `Number.toExponential` 메서드는 숫자를 `지수 표기법`으로 변환하여 `문자열`로 반환한다.
- 지수 표기법이란 매우 크거나 작은 숫자를 표기할 때 주로 사용하며 e(Exponent) 앞에 있는 숫자에 10의 n승을 곱하는 형식으로 수를 나타내는 방식이다.

```js
(77.1234).toExponential(); // -> "7.71234e+1"
(77.1234).toExponential(4); // -> "7.7123e+1"
(77.1234).toExponential(2); // -> "7.71e+1"
```

### 28.3.6 Number.prototype.toFixed

- `toFixed` 메서드는 `숫자를 반올림`하여 `문자열`로 반환한다.

```js
// 소수점 이하 반올림. 인수를 생략하면 기본값 0이 지정된다.
(12345.6789).toFixed(); // -> "12346"
// 소수점 이하 1자리수 유효, 나머지 반올림
(12345.6789).toFixed(1); // -> "12345.7"
// 소수점 이하 2자리수 유효, 나머지 반올림
(12345.6789).toFixed(2); // -> "12345.68"
// 소수점 이하 3자리수 유효, 나머지 반올림
(12345.6789).toFixed(3); // -> "12345.679"
```

### 28.3.7 Number.prototype.toPrecision

`toPrecision` 메서드는 인수로 전달받은 전체 자릿수까지 유효하도록 나머지 자릿수를 `반올림`하여 `문자열`로 반환한다.

```js
// 전체 자리수 유효. 인수를 전달하지 않으면 기본값 0이 전달된다.
(12345.6789).toPrecision(); // -> "12345.6789"
// 전체 1자리수 유효, 나머지 반올림
(12345.6789).toPrecision(1); // -> "1e+4"
// 전체 2자리수 유효, 나머지 반올림
(12345.6789).toPrecision(2); // -> "1.2e+4"
// 전체 6자리수 유효, 나머지 반올림
(12345.6789).toPrecision(6); // -> "12345.7"
```

### 28.3.8 Number.prototype.toSting

`toString` 메서드는 숫자를 문자열로 변환하여 반환한다.

```js
// 인수를 생략하면 10진수 문자열을 반환한다.
(10).toString(); // -> "10"
// 2진수 문자열을 반환한다.
(16).toString(2); // -> "10000"
// 8진수 문자열을 반환한다.
(16).toString(8); // -> "20"
// 16진수 문자열을 반환한다.
(16).toString(16); // -> "10"
```
