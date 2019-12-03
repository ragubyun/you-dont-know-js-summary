## 1. 타입

### 1.1 타입 그 실체를 이해하자.

### 1.2 내장 타입

총 7가지

- null
- undefined
- boolean
- number
- string
- object
- symbol(ES6부터 추가)

`object`를 제외한 이들을 '원시타입<sup>primitives</sup>'이라 한다.

`typeof` 결과의 타입은 `string` 타입

``` javascript
typeof number === 'string'; // true
```

**null**

``` javascript
typeof null === 'object'; // true -> null 값 확인 안됨
var a = null;
!a && typeof a === 'object'; // true -> null 값을 정확하게 확인하려면 조건이 하나 더 필요하다.
```

**function**

``` javascript
typeof function a() {} === 'function'; // true
```

`object`의 하위타입

호출 가능한 객체(내부 프로퍼티 `call`로 호출할 수 있는 객체)

함수에 선언된 인자 개수는 함수 객체의 `length` 프로퍼티로 알 수 있다.

``` javascript
function a(b, c) {}

a.length; // 2
```

**배열**

``` javascript
typeof [1, 2, 3] === 'object' // true
```

`object`의 하위 타입

키로 숫자 인덱스를 가지며 `length` 프로퍼티가 자동으로 관리되는 등, 추가특성을 가진 객체

### 1.3 값은 타입을 가진다.

`값`에는 타입이 있지만, `변수`에는 따로 타입이 없다.

`변수`는 언제라도, 어떤 형태의 값이라도 가질 수 있다.

#### 1.3.1 값이 없는 vs 선언되지 않은

``` javascript
var a;

a; // undefined
b; // ReferenceError: b is not defined
```

``` javascript
var a;

typeof a; // 'undefined'
typeof b; // 'undefined'
```

`b`는 분명 선언하지 않은 변수인데 `typeof b`를 해도 브라우저는 오류 처리를 하지 않는다.

`typeof`만의 독특한 안전 가드

#### 1.3.2 선언되지 않은 변수

여러 스크립트 파일의 변수들이 전역 네임스페이스<sup>namespace</sup>를 공유할 때 `typeof`의 안전 가드는 의외로 쓸모가 있다.

``` javascript
// DEBUG 전역변수가 있는지 확인하는 경우
if (DEBUG) { // ReferenceError: DEBUG is not defined
    console.log('디버깅을 시작합니다');
}

if (typeof DEBUG !== 'undefined') { // 안전
    console.log('디버깅을 시작합니다');
}
```
