## 5. 문법

### 5.1 문과 표현식

자바스크립트에서 문<sup>Statement</sup>과 표현식<sup>Expression</sup>은 아주 중요한 차이가 있으므로 명확하게 분별해야 한다.

문<sup>Statement</sup>은 문장<sup>Sentence</sup>, 표현식<sup>Expression</sup>은 어구<sup>Phrase</sup>, 연산자는 구두점/접속사에 해당된다.

``` javascript
1: var a = 3 * 6;
2: var b = a;
3: b;
```

- 표현식
  - 1: 3 * 6
  - 2: a
  - 3: b
- 선언문<sup>Declaration Statement</sup>
  - 1: var a = 3 * 6
  - 2: var b = a
- 할당 표현식<sup>Declaration Statement</sup>
  - 1: a = 3 * 6
  - 2: b = a
- 표현식문<sup>Expression Statement</sup>
  - b

#### 5.1.1 문의 완료 값

모든 문은(그 값이 `undefined`라 해도) 완료 값<sup>Completion Value</sub>을 가진다는 사실을 의외로 모르는 사람들이 많다.

> ES5.1, 12장. "문 도입부에 문의 평가 결과는 항상 완료 값이다.(The result of evaluating a Statement is always a Completion value.)"라고 기술되어 있습니다.

브라우저 개발자 콘솔 창은 가장 최근에 실행된 **문의 완료 값**을 기본적으로 출력하게 되어 있다.

`var b = a` 같은 문의 완료 값은? `var` 문 자체의 완료값은 `undefined`이다. 명세에 그렇게 적혀있기 때문이다.

``` javascript
var b;

if (true) {
    b = 4 + 38;
}
// 42, 하지만 내부 프로그램에서 사용할 수 있는 값은 아니다.
```

보통의 `{}` 블록은 내부의 가장 마지막 문/표현식의 완료 값을 자신의 완료값으로 반환한다.

완료 값을 순간 포착할 방법은?

``` javascript
var a, b;

a = eval(`if (true) {
    b = 4 + 38;
}`);
// 42, eval()을 사용해선 안되지만 잘 동작한다.
```

> eval()은 사용하지 말자.

#### 5.1.2 표현식의 부수 효과<sup>Side Effects</sup>

대부분의 표현식에는 부수 효과가 없다.

``` javascript
function foo() {
    a = a + 1;
}

var a = 1;
foo();
// undefined
```

부수 효과: `a`가 변경됨.

``` javascript
var a = 42;
var b = a++;

a; // 43
b; // 42
```

표현식 `a++`이 하는 일은 두가지다.

1. `a`의 현재 값 42를 반환(그리고 `b`에 할당하는 것까지)
2. `a`값을 1 증가시킨다.

`b`를 43으로 착각하는 개발자들이 생각보다 많다.

++를 후위<sup>Postfix</sup> 연산자로 사용하면 **값을 반환한 후** 값을 1만큼 증가시키는 부수 효과가 있다.

++를 전위<sup>Prefix</sup> 연산자로 사용하면 **값이 반환되기 전**에 값을 1만큼 증가시키는 부수 효과가 있다.

``` javascript
var a = 42, b;
b = (a++, a); // 두번째 a 표현식을 a++ 표현식의 부수 효과가 발생한 이후에 평가한다

a; // 43
b; // 43
```

문을 나열하는<sup>Statement-Series</sup> 콤마 연산자 `,`를 사용하면 다수의 개별 표현식을 하나의 문으로 연결할 수 있다.

``` javascript
var obj = {
    a: 42
};

obj.a; // 42
delete obj.a; // true
obj.a; // undefined
```

- `delete` 연산자의 결괏값: 유효한/허용된 연산일 경우 `true`, 그 외에는 `false`
- `delete` 연산자의 부수 효과: 프로퍼티를 제거


``` javascript
var a;
a = 42; // 42
a; // 42
```

- `=` 연산자의 결괏값: 할당된 값
- `=` 연산자의 부수 효과: 값을 할당하는 자체

이렇게 할당 표현식/문 실행 시 할당된 값이 완료 값이 되는 작동 원리는 다음과 같은 연쇄 할당문<sup>Chained Assignment</sup>에서 특히 유용하다.

``` javascript
var a, b, c;
a = b = c = 42;
```

또 다른 할당 연산자의 부수 효과 예

``` javascript
function vowels(str) {
    var matches;
    if (str) {
         // 모든 모음을 추출한다.
        matches = str.match(/[aeiou]/g);
        if (matches) {
            return matches;
        }
    }
}

vowels('Hello World'); // ['e', 'o', 'o']
```

``` javascript
function vowels(str) {
    var matches;
    if (str && (matches = str.match(/[aeiou]/g))) {
        return matchesl
    }
}

vowels('Hello World'); // ['e', 'o', 'o']
```

두 조건이 서로 분명히 연관되어 있음을 두번째 코드가 더 잘 보여준다. 하지만 스타일이고 개인 취향일 뿐이다.

#### 5.1.3 컨텍스트 규칙

자바스크립트에서 중괄호 `{ }`가 나올 법한 곳은 크게 두 군데다.

- 객체 리터럴
- 레이블 문<sup>Labeled Statement</sub>
  - `goto` 문과 동일하진 않지만 유사한 기능으로 피하는게 상책이다. 점프를 할 바에야 차라리 함수 호출이 더 낫다.

**객체 분해<sup>Destructuring Assignments</sup>**

새로운 임시 변수를 선언하는 수고를 덜어줄 뿐만 아니라 코드가 짧아져 유용하다.

**else if와 선택적 블록**

``` javascript
if (a) {
    // ...
} else if (b) {
    // ...
} else {
    // ..
}
```

실은 `else if` 같은 건 없다. 자바스크립트 문법의 숨겨진 특성이다. `if`와 `else` 문은 실행문이 하나밖에 없는 경우 블록을 감싸는 `{ }`를 생략해도 된다.

정확히 동일한 문법 규칙이 `else` 절에도 적용되어 위 코드는 실제로 항상 이렇게 파싱된다.

``` javascript
if (a) {
    // ...
} else {
    if (b) {
        // ...
    } else {
        // ...
    }
}
```

`else` 이후의 `if` 문은 단일문이므로 `{ }`로 감싸든 말든 상관없다.

즉, `else if`라고 쓰는 건 표준 스타일 가이드의 위반 사례가 된다. 물론 `else if`는 이미 누구나 다 쓰는 관용 코드이다. 하지만 `else if`를 정확한 문법 규칙인 양 넘겨짚지는 말자.

### 5.2 연산자 우선순위

모든 프로그래밍 언어에는 연산자 우선순위 리스티가 있다.

> 아쉽게도 자바스크립트 명세서에는 한눈에 알아보기 쉽게 잘 정리된 연산자 우선순위 리스트가 없으므로 스스로 분석해보고 문법 규칙을 이해해야 한다.

#### 5.2.1 단락 평가<sup>short circuiting</sup>

`&&`, `||` 연산자는 좌측 피 연산자의 평가 결과만으로 전체 겨로가가 이미 결정될 경우 우측 피연산자의 평가를 건너뛴다.

#### 5.2.2 끈끈한 우정

``` javascript
a && b || c ? c || b ? a : c && b : a
```

다음 둘 중에서 어느쪽으로 처리될까?

``` javascript
a && b || (c ? c || (b ? a : c) && b : a // 1
(a && b || c) ? (c || b) ? a : (c && b) : a // 2
```

정답은 2

`&&`와 `||`는 `?`와 `:`보다 서로 더 끈끈한 우정을 나눈 사이라고 할 수 있다. 그 반대도 또같이 적용된다.

#### 5.2.3 결합성

일반적으로 연산자는 좌측부터 그룹핑<sup>Grouping</sup>이 일어나는지 우측부터 그룹핑이 일어나는지에 따라 좌측 결합성<sup>Left-Associative</sup> 또는 우측 결합성<sup>Right-Associative</sup>을 가진다.

> 우측부터(혹은 좌측부터) 결합한다는 말은 우측 -> 좌측 순서로 평가가 아닌, 그룹핑을 그렇게 한다는 뜻이다. 평가의 순서는 엄격히 좌측 -> 우측을 따른다.

#### 5.2.4 분명히 하자

규칙에 따라 코딩하는 사람도 있고 규칙은 무시한 채 명시적인 코딩을 하는 이들도 있다. 이 문제는 매우 주관적인 데다 *4장 강제 변환* 만큼 지루한 논란의 대상이기도 하다.

연산자 우선순위/결합성을 적절히 활용하여 짧고 깔끔한 코드를 작성하되 혼동을 줄이고 분명히 밝혀야 할 부분은 `( )`로 예쁘게 감싸 주기 바란다.

### 5.3 세미콜론 자동 삽입

ASI<sup>Automatic Semicolon Insertion</sup>는 자바스크립트 프로그램의 세미콜론(;)이 누락된 곳에 엔진이 자동으로 `;`을 삽입하는 것을 말한다. 생각보다 엔진이 그리 관대하지 않기 때문에 단 하나의 `;`이라도 누락되면 자바스크립트 프로그램은 돌아가지 않는다.

#### 5.3.1 에러 정정

대부분 세미콜론은 선택사항이다.

자바스크립트 커뮤니티에서는 ASI에 전적으로/완전히 의존해야 하는가가 뜨거운 논란거리다.

- 찬성
  - 보다 간결한 코드를 작성하게 도와준다.
  - `;` 없이 작성한 프로그램이나 `;`을 기재하여 작성한 프로그램은 별반 다르지 않다.
- 반대
  - 개발자가 의도하지 않은 `;`들이 삽입되면서 그 의미가 달라질 수 있다.
  - 경험이 부족한 개발자들의 경우 실수할 가능성이 높다.
  - 세미콜론 누락은 순전히 개발자의 실수에서 비롯된 것이니 관련 툴(린터<sup>linters</sup> 등)로 분명히 정정해야 한다.

명세에는 분명히 ASI가 '에러 정정<sup>Error Correction</sup>' 루틴이라고 쓰여져 있다. 여기서 에러란 구체적으로는 파서 에러<sup>Parser Error</sup>다.

> "세미콜론은 써도 되고 안 써도 되니 그냥 나는 생략하련다"라고 말하는 건 "프로그램이 잘 돌아가기만 하면 그만이니 나는 최대한 파서를 깨뜨리는 프로그램을 작성하겠다"와 같다.

타이핑을 아끼면서 '아름다운 코드'를 작성하겠다는 발상 자체가 터무니없다.

### 5.4 에러

#### 5.4.1 너무 이른 변수 사용

ES6는 임시 데드 존<sup>TDZ, Temporal Dead Zone</sup>이라는 새로운 개념을 도입했다. TDZ는 아직 초기화를 하지 않아 변수를 참조할 수 없는 코드 영역이다.

`let` 블록 스코핑이 대표적인 예다.

``` javascript
{
    a = 2; // ReferenceError (TDZ)
    let a; // 이 선언 실행 후에 비로소 TDZ가 끝나고 a에 undefined가 할당된다.
}
```

``` javascript
{
    typeof a; // undefined
    typeof b; // ReferenceError (TDZ)
    let b;
}
```

### 5.5 함수 인자

``` javascript
var b = 3;
function foo(a = 42, b = a + b + 5) {
    // ...
}
```

> 함수 인자의 디폴드 값은 마치 하나씩 좌측 -> 우측 순서로 `let` 선언을 한 것과 동일하게 처리된다. 그래서 일단 무조건 TDZ에 속하게 된다.

두번째 인자에서 좌번 `b`는 아직 TDZ에 남아 있는 `b`를 참조하려고 하기 때문에 `ReferenceError`를 던진다.

ES6에서 함수의 디폴트 인자 값이 없거나 `undefined` 값을 받거나 그게 그거다. 하지만 차이점을 엿볼 수 있는 경우도 있다.

``` javascript
function foo(a = 42, b = a + 1) {
    console.log(arguments.length, a, b, arguments[0], arguments[1])
}

foo(); // 0 42 43 undefined undefined
foo(10); // 1 10 11 10 undefined
foo(10, undefined); // 2 10 11 10 undefined
foo(10, null); // 2 10 null 10 null
```

`undefined` 인자를 명시적으로 넘기면 `arguments` 배열에도 값이 `undefined`인 원소가 추가되는데, 여기에 해당하는 디폴트 인자 값과 다르다.

ES6 이전까지 `arguments`는 함수에 전달된 인자들을 배열 형태로 추출할 수 있는 유일한 방법이었고 제법 유횽하게 잘 써왔다.

> "인자와 이 인자에 해당하는 `arguments` 슬롯을 동시에 참조하지 마라"

### 5.6 try...finally

`try` 절에 `return` 문이 있으면?

``` javascript
function foo() {
    try {
        return 42;
    } finally {
        console.log('hello');
    }

    console.log('실행될 리 없지!');
}

console.log(foo());
// hello
// 42
```

`try` 안에 `throw`가 있어도 비슷하다.

``` javascript
function foo() {
    try {
        throw 42;
    } finally {
        console.log('hello');
    }

    console.log('실행될 리 없지!');
}

console.log(foo());
// hello
// Uncaught Esception: 42
```

만약 `finally` 절에서(사고든, 고의든) 예외가 던저지면, 이전의 실행 결과는 모두 무시된다. 즉, 이전에 `try` 블록에서 생성한 완료 값이 있어도 완전히 사장된다.

``` javascript
function foo() {
    try {
        return 42;
    } finally {
        throw '어이쿠!';
    }

    console.log('실행될 리 없지!');
}

console.log(foo());
// Uncaught Esception: 어이쿠!
```

`finally` 절의 `reutrn`은 그 이전에 실행된 `try`나 `catch` 절의 `return`을 덮어쓴다.

``` javascript
function foo() {
    try {
        return 42;
    } finally {
        // 여기 return이 없군, 그럼 이전 return을 존중하자!
    }
}

function bar() {
    try {
        return 42;
    } finally {
        // 'return 42'를 무시한다.
        return;
    }
}

function baz() {
    try {
        return 42;
    } finally {
        // 'return 42'를 무시한다.
        return 'hello';
    }
}

foo(); // 42
bar(); // undefinded
baz(); // hello
```

### 5.7 switch

`switch` 표현식과 `case` 표현식 간의 매치 과정은 `===` 알고리즘과 똑같다.
