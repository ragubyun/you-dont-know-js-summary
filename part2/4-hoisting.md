## 4. 호이스팅

한 스코프 안에서 선언된 변수는 바로 그 스코프에 속한다.

선언문이 스코프의 어디에 있는지에 따라 스코프에 변수가 추가되는 과정에 미묘한 차이가 있다.

### 4.1 닭이 먼저냐 달걀이 먼저냐

``` javascript
// 첫번째 예제
a = 2;
var a;
console.log(a); // 2
```

``` javascript
// 두번째 예제
console.log(a); // undefined
var a = 2;
```

### 4.2 컴파일러는 두 번 공격한다.

컴파일레이션 단계에서 모든 선언문을 찾아 적절한 스코프에 연결해준다. 이 과정이 렉시컬 스코프의 핵심.

자바스크립트는 아래 구문을 두개의 구문으로 본다.

``` javascript
var a = 2;
```

- `var a;` 선언문으로 컴파일레이션 단계에서 처리한다.
- `a = 2;` 대입문으로 실행 단계까지 내버려둔다.

따라서 첫번째 예제 코드는 다음과 같이 처리된다.

``` javascript
var a; // 컴파일레이션 과정
a = 2; // 실행 과정
console.log(a); // 2
```

비슷한 방식으로 두번째 예제 코드는 다음과 같이 처리된다.

``` javascript
// 두번째 예제
var a; // 컴파일레이션 과정
console.log(a); // undefined
a = 2; // 실행 과정
```

변수와 함수 선언문은 선언된 위치에서 코드의 꼭대기로 '끌어올려' 진다. 이렇게 끌어올리는 동작을 '호이스팅<sup>Hoisting</sup>'이라고 한다.

즉, 달갈(선언문)이 닭(대입문)보다 먼저다.

``` javascript
foo();

function foo() {
    console.log(a); // undefined
    var a = 2;
}
```

함수 `foo`의 선언문이 끌어올려지므로 `foo`를 첫번째 줄에서도 호출할 수 있다.

호이스팅은 스코프별로 동작한다. `foo()` 내에서도 변수 `a`가 `foo()`의 꼭대기로 끌어올려진다.

참고로, 선언문만 끌어올려지고 다른 대입문이나 실행 로직 부분은 제자리에 그대로 둔다.

호이스팅 결과

``` javascript
function foo() {
    var a;
    console.log(a); // undefined
    a = 2;
}

foo();
```

함수 선언문은 위와 같이 끌어올려지지만 함수 표현식은 다르다.

``` javascript
foo(); // not ReferenceError, but TypeError!

var foo = function bar() {
    ...
}
```

아직 값을 가지고 있지 않은 `undefined` 상태인 `foo`의 함수 실행을 시도해서 `TypeError` 오류가 발생한다.

함수 표현식이 이름을 가져도 그 이름 확인자는 해당 스코프에서 찾을 수 없다.

``` javascript
foo(); // TypeError
bar(); // ReferenceError

var foo = function bar() {
    ...
}
```

호이스팅 결과

``` javascript
var foo;

foo(); // TypeError
bar(); // ReferenceError

foo = function() {
    var bar = ...self...
    ...
}
```

### 4.3 함수가 먼저다.

먼저 함수가 끌어올려지고 다음으로 변수가 올려진다.

``` javascript
foo(); // 1
var foo;

function foo() {
    console.log(1);
}

foo = function() {
    console.log(2);
}
```

호이스팅 결과

``` javascript
function foo() {
    console.log(1);
}

// var foo; -> 중복 선언문이라서 무시됨

foo(); // 1

foo = function() {
    console.log(2);
}
```

스코프 내에서의 중복 정의는 나쁜 방식이고 혼란스러운 결과를 가져온다. 즉, 블록 내 함수 선언은 지양하는 것이 가장 좋다.
