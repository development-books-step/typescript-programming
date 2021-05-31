# 타입스크립트 2주차

### 읽어 봐야 할 Document

[https://developer.mozilla.org/ko/docs/Web/JavaScript/Inheritance_and_the_prototype_chain](https://developer.mozilla.org/ko/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

본 글은 Do it! 타입스크립트 프로그래밍의 <b>Chapter 05</b> 의 내용까지 담고 있습니다. 🙇‍♀️

자바스크립트의 함수는 추상화 계층을 구축하거나 클래스 모방, 캡슐화, 모듈에 대한 방법을 제공해 주고 있습니다. <br />`리터럴이나 함수 활용, 프로토타입 방식을 말합니다!` <br/>
타입스크립트의 함수는 <b>여전히 이 일을 어떻게 수행할 것인지</b>를 설명하는데 있어 핵심 역할을 수행하고 있고, 표준 자바스크립트 함수가 작업을 수월하게 하도록 <b>몇 가지의 새로운 기능을 추가</b>합니다.
<br><br/>

---

### Q1. 표현식과 표현식 문의 차이점은?

호이스팅과 같이 알아 두면

### Q2. 메서드 체이닝의 장점은?

<br>

## 함수

### 함수 시그니처

변수에도 타입이 있듯이 함수 또한 타입이 있는데, 함수의 타입을 <b>함수 시그니처</b>라고 합니다. <br/>
다음 코드는 `testFunction` 함수의 시그니처를 보여 주는 예시입니다. `testFunction` 함수는 `string`과 `number` 타입의 매개변수가 있고, 반환 타입은 `void` 인 것을 알 수 있습니다! <br/>
따라서 함수 시그니처는 `(string, number) => void` 네요! <br/>

```jsx
let testFunction: (string, number) => void = function (
  food: string,
  quantity: number
): void {};
```

<br/>

### 타입 별칭

타입스크립트는 `type` 이라는 키워드를 제공하여, 기존에 존재하는 타입을 이름만 바꾸어서 사용할 수 있게 해 줍니다. 이러한 기능을 <b>타입 별칭</b>이라고 하는 거죠! <br/>
아래와 같이 `(string, number) => void` 의 함수 시그니처를 사용하여 서로 각기 다른 이름의 타입 별칭을 만들어 줄 수 있습니다. 이렇게 된다면 <b>타입 주석을 붙이기 더 쉬워지는 거</b>겠죠!

```jsx
type foodQuantityFunc = (string, number) => void;

let cookieFunc: foodQuantityFunc = function (
  cookie: string,
  quantity: number
): void {};

let chickenFunc: foodQuantityFunc = function (
  chicken: string,
  quantity: number
): void {};
```

> 함수 시그니처를 명시하게 되면, 매개변수의 개수나 타입, 반환 타입이 다른 함수를 선언하는 실수를 방지할 수 있습니다!

<br/>

### 선택적 매개변수

함수의 매개변수에 다음처럼 이름 뒤에 물음표를 붙이면 선택 속성을 부여해 줄 수 있습니다. 이를 <b>선택적 매개변수</b>라고 합니다. 선택적 매개변수는 항상 그 값이 `undefined` 로 고정됩니다!

```jsx
type func = (string, number?) => void

function testFunc (arg1: string, arg2?: number): void {}
```

<br/>

### 일등 함수

일등 함수란, <b>함수와 변수를 구분하지 않는다 (혹은 차별하지 않는다.) 는 의미</b>로 프로그래밍 언어가 일등 함수 기능을 제공하면 <b>'함수형 프로그래밍 언어'</b>라고 할 수 있습니다. <br/><br/>
예를 들어, 아래의 코드와 같이 쓰였을 때 심벌 test 는 변수인지 함수인지 구분할 수 없습니다. 이것이 <b>변수와 함수를 차별하지 않는다</b> 즉, <b>일등 함수</b>라는 겁니다. `📌 개념이 부족했다는 것을 깨달으며......`

```jsx
let test = function (arg1, arg2) {
  return arg1 - arg2;
};

test = function (arg1, arg2) {
  return arg1 + arg2;
};
```

<br />

### 실행식과 표현식 문

타입스크립트에서는 관습적으로 표현식 문에는 <b>세미콜론</b>을 붙이지 않습니다! 실행식과 표현식 문은 아래의 코드와 같은 차이점으로 구분할 수 있습니다.

```jsx
const executionFunc = (arg1: number, arg2: number): number => {
  return arg1 + arg2;
};

const expressionFunc = (arg1: number, arg2: number): number => arg1 + arg2;
```

<br/>

### 고차 함수와 클로저

고차 함수는 함수 내에서 또 다른 함수를 리턴하는 구조를 말합니다. 아래의 예제 코드를 보면 number 타입을 매개변수로 받고, 반환해 주는 NumberToNumerFunc 시그니처를 만들었습니다. addFunc 함수는 NumberToNumerFunc 타입을 반환해 주는 함수로, 중첩 함수로 구현하고 있습니다. <br/>

> 고차 함수는 이처럼, 중첩 함수를 반환할 수 있다는 사실! <br/>

여기서 유심히 봐야 할 코드는, arg1 은 addFunc 의 매개변수이고, arg2 는 \_add 의 매개변수라는 사실입니다. 즉, \_add 함수의 입장에서 보면 arg1 은 함수 외부에서 선언된 매개변수로 함수형 프로그래밍 언어에서는 다음과 같은 형태를 <b>클로저</b>라고 부릅니다. `📌 개념이 부족했다는 것을 깨달으며......`

```jsx
type NumberToNumerFunc = (number) => number; /* 함수 시그니처 */

export const addFunc = (arg1: number): NumberToNumberFunc => {
  const _add: NumberToNumberFunc = (arg2: number): number => {
    return arg1 + arg2; /* 클로저 (Closure) */
  };

  return _add;
};
```

<br/>

### 객체를 반환하는 화살표 함수

컴파일러는 중괄호 {} 를 객체가 아닌 복합 실행문으로 인식하기 때문에, 컴파일러가 {} 를 객체로 인식하게 하려면 아래처럼 객체를 소괄호 () 로 감싸 주어야 합니다. <br/>

```jsx
export type Person = { name: string, age: number };

export const testFunc = (name: string, age: number): Person => ({
  name,
  age,
});
```

<br/><br/>

## 배열과 튜플

### 제네릭 방식 타입

배열을 다루는 함수를 작성할 때에는 number[] 과 같이 특정 타입으로 고정된 함수를 만들기 보다는 T[] 형태로 배열의 아이템 타입을 한꺼번에 표현해 주는 것이 편리합니다. 이와 같이 타입을 `T` 와 같은 일종의 변수로 취급하는 것을 `제네릭 타입`이라고 합니다. <br/>
다음은 자바스크립트 함수에 타입스크립트의 제네릭 타입을 사용하는 방법입니다!

```jsx
    const isEmpty = <T>(array: T[]): boolean => arrayLength<T>(array) === 0

    console.log(isEmpty[]); /* true */
    console.log(isEmpty[1]); /* false */

```

<br/>

원칙적로는 <b>제네릭 형태로 구현된 함수</b>는 타입 변수를 `함수 이름<타입 변수>(매개변수)` 와 같은 형태로 명시해 주어야 하지만, 타입스크립트는 타입 변수 부분을 생각할 수 있게 지원해 주고 있습니다. 따라서, <b>타입 변수가 생략된 제네릭 함수를 만나게 되면 타입 추론을 통해 생략된 타입을 찾아내게 됩니다. </b>

<br/>

### 순수 함수

함수형 프로그래밍에서 함수는 <b>'순수 함수'</b>라는 조건을 만족해야 하지만, 타입스크립트의 Array 클래스에는 순수 함수 조건에 부합하지 않는 메서드가 많습니다. <br/>
