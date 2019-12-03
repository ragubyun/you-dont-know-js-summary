## 3. 함수 vs 블록 스코프

### 3.1 함수 기반 스코프

``` javascript
function foo(a) {
    var b = 2;

    function bar() {
        // ...
    }

    var c = 3;
}

bar(); // ReferenceError
console.log(a, b, c) // all 3 ReferenceError
```

글로벌 스코프: foo 확인자 포함

`foo()`의 스코프 버블: 확인자 a, b, c, bar 포함

`bar()`의 스코프 버블: 자체 스코프 및 `foo()` 스코프 접근 가능

### 3.2 일반 스코프에 숨기

작성한 코드에서 임의 부분을 함수 선언문으로 감싸면 해당 코드를 '숨기는' 효과를 낸다.

``` javascript
function doSomething(a) {
    b = a + doSomethingElse(a * 2);

    console.log(b * 3);
}

function doSomethingElse(a) {
    return a - 1;
}

var b;
doSomething(2); // 15
```

변수 `b`와 `doSomethingElse()`에 '접근'할 수 있도록 내버려 두는 것은 불필요할 뿐 아니라 '위함'할 수 있다.

더 '적절하게' 설계하려면 비공개 부분은 `doSomething()` 스코프 내부에 숨겨야 한다.

``` javascript
function doSomething(a) {
    function doSomethingElse(a) {
        return a - 1;
    }

    var b = a + doSomethingElse(a * 2);

    console.log(b * 3);
}

doSomething(2); // 15
```

이제 `b`와 `doSomethingElse()`는 외부에서 접근할 수 없고 오직 `doSomething()`만이 이들을 통제한다.

일반적으로 이것이 더 나은 코드라고 본다.

#### 3.2.1 충돌 회피

변수와 함수를 스코프 안에 '숨기는 것'의 또 다른 장점은 **같은 이름을 가졌지만 다른 용도를 가진 두 확인자**가 충돌하는 것을 피할 수 있다는 점이다.

``` javascript
function foo() {
    function bar(a) {
        i = 3;

        console.log(a + i);
    }

    for (var i = 0; i < 10; ++i) {
        bar (i * 2); // 무한 반복
    }
}

foo();
```

변수 `i`의 값이 3으로 고정되어 영원히 `i < 10`인 상태로 머문다.

``` javascript
function bar(a) {
    var i = 3; // 섀도잉(shadowing) 으로 문제 해결 가능
    ...
}
```

**글로벌 '네임스페이스' 사용**

여러 라이브러리를 한 프로그램에서 불러오면 서로 쉽게 충돌한다.

이런 라이브러리는 일반적으로 글로벌 스코프에 하나의 고유 이름을 가지는 객체를 생성하여 해당 라이브러리의 '네임스페이스'로 이용된다. 네임스페이스를 통해 최상위 스코프의 확인자가 아니라 속성 형태로 라이브러리의 모든 기능이 노출된다.

``` javascript
var MyReallyCoolLibrary = {
    awesome: 'stuff',
    doSomething: function() {
        ...
    },
    doAnotherThing: function() {
        ...
    }
}
```

**모듈 관리**

### 3.3 스코프 역할을 하는 함수

``` javascript
var a = 2;

function foo() {
    var a = 3;
    console.log(a); // 3
}
foo();

console.log(a); // 2
```

이 방식은 작동하기는 하지만 이상적인 방식은 아니다.

1. `foo`라는 이름의 함수를 선언해야 해서 그 이름을 둘러싸고 있는 스코프(여기서는 글로벌 스코프)를 '오염'시킨다.
2. 함수를 직접 이름(`foo()`)으로 호출해야만 실제 감싼 코드를 실행할 수 있다.

함수를 이름 없이 혹은 그 이름이 둘러싸인 스코프(글로벌 스코프)를 오염시키지 않고 선언하고 자동으로 실행된다면 더 이상적일 것이다.

**두가지 문제를 다 해결한 코드**

``` javascript
var a = 2;

// 표현식으로 취급되는 함수 사용 방법
(function foo() {
    var a = 3;
    console.log(a); // 3
})();

console.log(a); // 2
```

함수가 '선언문<sup>declarations</sup>'이 아니라 '표현식<sup>expressions</sup>'으로 취급되고, 마지막 `()` 문법으로 바로 실행한다.

#### 3.3.1 익명 vs 기명

``` javascript
// 함수 표현식을 콜백 인자로 사용하는 익숙한 사례
setTimeout(function() {
    console.log('I waited 1 second!');
}, 1000);
```

`function() { ... }`에 확인자 이름이 없는 방식을 '익명 함수 표현식'이라고 부른다.

함수 표현식은 이름 없이 사용할 수 있지만, 이름없는 함수 선언문은 자바스크립트 문법에 맞지 않다.

함수 표현식을 사용할 때도 이름을 사용하는 것이 가능하므로 이름을 항상 쓰는 것이 좋다.

``` javascript
setTimeout(function timeoutHandler() {
    console.log('I waited 1 second!');
}, 1000);
```

**장점**

1. 스택 추적 시 표시할 이름이 생긴다.
2. 자기 참조가 필요한 재귀 호출이 가능해진다(다른 방법으로는 deprecated된 arguments.callee 참조를 해야한다).
3. 읽을 수 있는 코드 작성에 도움이 된다.

#### 3.3.2 함수 표현식 즉시 호출하기

``` javascript
(function foo() {
    ...
})();
```

즉시 호출 함수 표현식<sup>IIFE</sup>(Immediately Invoked Function Expression)

또 다른 표현 방법

``` javascript
(function foo() {
    ...
}();)
```

두 형태 모두 똑같이 동작하고 어떤 스타일을 선호하느냐의 문제

IIFE가 결국 함수라는 사실을 이용해 여러가지 변형이 가능하다.

**IIFE의 변형 1**: 인자를 넘기는 방식

``` javascript
var a = 2;
(function IIFE(global) {
    console.log(global.a) // 2
})(window);
```

**IIFE의 변형 2**: 인자로 함수를 넘겨서 특정 인자를 가지고 실행하게 만드는 방식(UMD<sup>Universal Module Definition</sup>(범용 모듈 정의) 프로젝트에서 사용)

``` javascript
var a = 2;
(function IIFE(def) {
    def(window);
})(function def(global) {
    console.log(global.a) // 2
});
```

### 3.4 스코프 역할을 하는 블록

``` javascript
function foo() {
    for (var i = 0; i < 10; ++i) {
        ...
    }
}
```

오직 `for` 반복문에서만 사용될(또는 사용되어야만 할) 변수 `i`로 `foo()` 함수 스코프 전체를 오염시킨다.

자바스크립트는 블록 스코프를 지원하지 않는다. 물론 좀 더 파고들면 방법은 있다.

#### 3.4.1 with

비추, 자세한 설명은 생략한다.

#### 3.4.2 try/catch

`catch` 부분에서 선언된 변수는 `catch` 블록 스코프에 속한다.

#### 3.4.3 let

ES6에서 채택된 `var`와 같이 변수를 선언하는 다른 방식이다.

**비명시적 블록 스코프 스타일**

``` javascript
if (true) {
    let bar = 2;
    console.log(bar); // 2
}
console.log(bar); // ReferenceError
```

**명시적 블록 스코프 스타일**

``` javascript
if (true) {
    { // <- 명시적 블록
        let bar = 2;
        console.log(bar); // 2
    }
}
console.log(bar); // ReferenceError
```

이렇게 명시적으로 블록을 생성하면 나중에 리팩토링 하면서 `if` 문의 위치나 의미를 변화시키지 않고도 전체 블록을 옮기기가 쉬워진다.

`let`을 사용한 선언문은 속하는 스코프에서 `호이스팅` 효과를 받지 않는다. 따라서 `let`으로 선언된 변수는 실제 선언문 전에는 명색하게 '존재'하지 않는다.

``` javascript
{
    console.log(bar); // ReferenceError
    let bar = 2;
}
```

**가비지 콜렉션<sup>Garbage Collection</sup>**

``` javascript
function process(data) {
	// do something interesting
}
var someReallyBigData = { ... };
process(someReallyBigData);

var btn = document.getElementById('my_button');
btn.addEventListener( 'click', function click(evt) {
	console.log('button clicked');
}, false );
```

클릭을 처리하는 `click` 함수는 `someReallyBigData` 변수가 전혀 필요없다. 따라서 이론적으로는 `process()`가 실행된 후 많은 메모리를 먹는 자료구조인 `someReallyBigData`는 수거할 수도 있다. 그러나 `click` 함수가 해당 스코프 전체의 클로저를 가지고 있기 때문에 자바스크립트 엔진은 그 데이터를 여전히 남겨둘 것이다.

블록 스코프는 엔진에게 `someReallyBigData`가 더는 필요없다는 사실을 더 명료하게 알려서 이 문제를 해결할 수 있다.

``` javascript
function process(data) {
	// do something interesting
}

{ // 작업 후 더이상 필요없는 선언을 여기에 let으로 추가
    let someReallyBigData = { ... };
    process(someReallyBigData);
}

var btn = document.getElementById('my_button');
btn.addEventListener( 'click', function click(evt) {
	console.log('button clicked');
}, false );
```

명시적으로 블록을 선언하여 변수의 영역을 한정하는 것은 효과적인 코딩 방식이므로 익혀두면 좋다.

**let 반복문**

``` javascript
for (let i = 0; i < 10; ++i) {
    ...
}
console.log(i); // ReferenceError
```

`let`은 단지 `i`를 `for` 반복문에 묶었을 뿐만 아니라 반복문이 돌 때마다 변수를 다시 묶어서 이전 반복의 결괏값이 제대로 들어가도록 한다.

#### 3.4.4 const

ES6에서는 `let`과 함께 `const`도 추가되었다. 키워드 `const` 역시 블록 스코프를 생성하지만, 선언된 값은 고정된다(상수).

``` javascript
if (foo) {
    const a = 3;
    a = 4; // TypeError: Assignment to constant variable.
}
console.log(a); // ReferenceError
```
