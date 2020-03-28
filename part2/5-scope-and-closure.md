## 5. 스코프와 클로저

렉시컬 스코프를 완벽하게 이해하지 못했다면 이해하고 이 챕터에 들어가야 한다.
​
### 5.1 깨달음

클로저는 자바스크립트의 모든 곳에 존재하고, 렉시컬 스코프에 의존해 코드를 작성한 결과로 그냥 발생한다.

이용하려고 굳이 의도적으로 클로저를 생성할 필요도 없다. 모든 코드에서 클로저는 생성되고 사용된다.
​
### 5.2 핵심
​
클로저는 함수가 속한 렉시컬 스코프를 기억하여 함수가 렉시컬 스코프 밖에서 실행될 때에도 이 스코프에 접근할 수 있게 하는 기능을 뜻한다.​

``` javascript
function foo() {
    var a = 2;
    function bar() {
        console.log(a);
    }
    return bar;
}

var baz = foo();
baz(); // 2
```
​
함수 `bar()`는 `foo()`의 렉시컬 스코프에 접근할 수 있고, `bar()` 함수 자체를 값으로 넘긴 결과 `bar()`는 의심할 여지없이 실행된다. 그러나 이 경우에 함수 `bar()`는 함수가 선언된 렉시컬 스코프 밖에서 실행된다.

선언된 위치 덕에 `bar()`는 `foo()` 스코프에 대한 렉시컬 스코프 클로저를 가지고, `foo()`는 `bar()`가 나중에 참조할 수 있도록 스코프를 살려둔다.

> 즉, `bar()`는 여전히 해당 스코프에 대한 참조를 가지는데, 그 참조를 바로 **클로저**라고 부른다.

어떤 방식이든 함수를 값으로 넘겨 다른 위치에서 호출하는 행위는 모두 클로저가 작용한 예다.

``` javascript
function foo() {
    var a = 2;
    function baz() {
        console.log(a); // 2
    }
    bar(baz);
}

function bar(fn) {
    fn(); // foo 함수의 내부 스코프에 대한 참조를 가진 클로저
}
```

``` javascript
var fn;

function foo() {
    var a = 2;
    function baz() {
        console.log(a);
    }
    fn = baz;
}

function bar() {
    fn();
}

foo(); // baz가 fn에 할당됨
bar(); // 2
```

### 5.3 이제 나는 불 수 있다

``` javascript
function wait(message) {
    setTimeout(function timer() {
        console.log(message);
    }, 1000);
}
wait('Hello, closure!');
```

`wait()` 실행 1초 후, wait의 내부 스코프는 사라져야 하지만 익명의 함수가 여전히 해당 스코프에 대한 클로저를 가지고 있다.

**클로저**

> 흔히 IIFE 자체가 드러난 클로저의 예라고 하는데, 나는 동의하지 않는다. IIFE만으로는 앞에서 정의한 요소를 갖추지 못하기 때문이다.

``` javascript
var a = 2;
(function IIFE() {
    console.log(a); // 2
})();
```

클로저는 기술적으로 보면 선언할 때 발생하지만, 바로 관찰할 수 있는 거은 아니다. 누군가 말했던 것처럼 아무도 듣는 사람 없이 쓰러진 숲 속의 나무와 같다.

> "숲속에서 나무가 쓰러지는데, 아무도 그 소리를 듣지 못한다면 쓰러지는 나무가 소리를 낸다고 말할 수 있는가? 존재하는 것은 지각되는 것이다." - *철학자 비숍 조지 버클리*

IIFE는 클로저가 사용할 수 있는 스코프를 만드는 가장 흔한 도구의 하나다. IIFE 자체가 클로저를 작동시키지는 않아도 확실히 클로저와 연관이 깊다.

### 5.4 반복문과 클로저

``` javascript
for (var i = 1; i <= 5; i++) {
    setTimeout(function timer() {
        console.log(i);
    }, i * 1000);
}
```

결과는 일초마다 한번씩 '**6**'이 5번 출력되는데, 이는 반복문이 끝났을 때의 `i` 값을 반영한 것이다.

`setTimeout` 함수의 콜백은 반복문이 끝나고 나서야 작동한다.

문법적으로 기대한 것과 같이 이 코드를 작동시키려면 반복마다 각각의 `i` 복제본을 '**잡아**'두는 것이다. 구체적으로 말하면 반복마다 새로운 스코프가 필요하다.

``` javascript
for (var i = 1; i <= 5; i++) {
    (function() {
        setTimeout(function timer() {
            console.log(i);
        }, i * 1000);
    })();
}
```

IIFE를 이용해 스코프를 생성하였지만 작동하지 않는다. IIFE는 빈 스코프일 뿐이다.

``` javascript
for (var i = 1; i <= 5; i++) {
    (function() {
        var j = i; // i 값을 저장하는 변수
        setTimeout(function timer() {
            console.log(j);
        }, j * 1000);
    })();
}
```

성공!

``` javascript
for (var i = 1; i <= 5; i++) {
    (function(j) {
        setTimeout(function timer() {
            console.log(j);
        }, j * 1000);
    })(i);
}
```

인자를 이용한 다른 버전

### 5.4.1 다시 보는 블록 스코프

실제 필요했던 것은 반복 별 블록 스코프였다. [Part 2 - 3. 함수 vs 블록 스코프](./part2/3-function-vs-block-scope.md#)에서 블록 스코프에 변수를 선언하는 `let`을 배웠다. 키워드 `let`은 본질적으로 하나의 블록을 닫을 수 있는 스코프로 바꾼다.

``` javascript
for (let i = 1; i <= 5; i++) {
    setTimeout(function timer() {
        console.log(i);
    }, i * 1000);
}
```

블록 스코프와 클로저가 함께 모든 문제를 해결했다.

### 5.5 모듈

``` javascript
function CoolModule() {
    var something = 'cool';
    var another = [1, 2, 3];

    function doSomething() {
        console.log(something);
    }

    function doAnother() {
        console.log(another.join('!'));
    }

    return {
        doSomething,
        doAnother
    };
}

var foo = CoolModule();
foo.doSomething(); // cool
foo.doAnother(); // 1!2!3
```

함수 `doSomething()`과 `doAnother()`은 모듈 인스턴스의 내부 스코프에 대한 클로저를 가진다.

이 모듈 패턴을 사용하려면 두 가지 조건이 있다.

1. 하나의 최외곽 함수가 존재하고, 이 함수가 최소 한 번은 호출되어야 햔다.(호출할 때마다 새로운 모듈 인스턴스가 생성된다)
2. 최외곽 함수는 최소 한번 하나의 내부 함수를 반환해야 한다. 그래야 해당 내부 함수가 비공개 스코프에 대한 클로저를 가져 비공개 상태에 접근하고 수정할 수 있다.

아래는 약반 변형된 오직 하나의 인스턴스만 생성하는 싱글톤<sup>Singleton</sup> 패턴이다.

``` javascript
var foo = (function CoolModule() {
    var something = 'cool';
    var another = [1, 2, 3];

    function doSomething() {
        console.log(something);
    }

    function doAnother() {
        console.log(another.join('!'));
    }

    return {
        doSomething,
        doAnother
    };
})();

foo.doSomething(); // cool
foo.doAnother(); // 1!2!3
```

모듈은 함수이므로 다음처럼 인자를 받아 공개 API로 반환하는 객체에 이름을 정하는 방식도 가능하다.

``` javascript
function CoolModule(id) {
        function identify() {
        console.log(id);
    }

    return {
        identify
    };
}

var foo1 = CoolModule('foo 1');
var foo2 = CoolModule('foo 2');
foo1.identify(); // foo 1
foo2.identify(); // foo 2
```

#### 5.5.1 현재의 모듈

모듈 개념을 설명하기 위한 매우 단순한 코드

``` javascript
var MyModules = (function Manager() {
    var modules = [];

    function define(name, deps, impl) {
        for (var i = 0; i < deps.length; i++) {
            deps[i] = modules[deps[i]];
        }
        modules[name] = impl.apply(impl, deps); // 이 코드의 핵심부
    }

    function get(name) {
        return modules[name];
    }

    return {
        define,
        get
    };
})();
```

이 코드의 핵심부 설명...

> 의존성을 인자로 넘겨 모듈에 대한 정의 래퍼 함수를 호출하고 반환 값인 모듈 API를 이름으로 정리된 내부 모듈 리스트에 저장한다.

`MyModules.define()`을 이용해 모듈을 정의하는 코드

``` javascript
MyModules.define('bar', [], function() {
    function hello(who) {
        return 'Let me introduce: ' + who;
    }

    return {
        hello
    };
});

MyModules.define('foo', ['bar'], function(bar) { // 'bar'의 인스턴스를 의존성 인자로 받아 사용할 수도 있다.
    var hungry = 'hippo';

    function awesome() {
        console.log(bar.hello(hungry).toUpperCase());
    }

    return {
        awesome
    };
});

var bar = MyModules.get('bar');
var foo = MyModules.get('foo');

console.log(bar.hello('hippo')) // Let me introduce: hippo
```

모듈 관리자를 만드는 특별한 마법이란 존재하지 않는다. 모든 모듈 관리자는 앞에서 언급한 모듈 패턴의 특성을 가진다. 좀 더 쓰기 편하게 포장한다고 해도 모듈은 그저 모듈일뿐이다.

#### 5.5.2 미래의 모듈

ES6 모듈은 inline 형식을 지원하지 않고, 반드시 개별 파일(모듈당 파일 하나)에 정의되어야 한다.

모듈을 불러올 때 모듈 로더는 동기적으로 모듈 파일을 불러온다.

### 5.6 정리하기

클로저는 표준이고, 함수를 값으로 마음대로 넘길 수 있는 렉시컬 스코프 환경에서 코드를 작성하는 방법이다.







