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
// 42, eval()을 사용해선 안되지만 잘 돌아간다.
```

ㅑ