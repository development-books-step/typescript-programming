# 타입스크립트 1주차

![image](https://user-images.githubusercontent.com/49897409/118777625-39ef0a80-b8c4-11eb-88e8-ae7e3fe69d8e.png)

위 사진은 타입스트립트에서 활용 가능한 모든 타입이 있는 타입 계층도 입니다. 여러가지 문법의 조합으로 타입을 정의하여 사용할 수 있습니다. 

크게 모든 타입의 슈퍼셋인 Unknown과 모든 타입의 서브셋인 never에 대해서 알아보도록 하겠습니다.

**Unknown**

```markdown
Unknown 타입은 typescript version 3.0 부터 생긴 타입 입니다.
Unknown이 있기 전까지는 Any 타입이 그 역할을 대신했습니다.
```

일반적으로 모든 타입은 `Unknown` 이라는 슈퍼셋의 하위에 존재하게 됩니다. 물론 `Any` 역시 타입스크립트의 모든 타입을 포함하는 슈퍼셋의 역할을 하지만 엄밀히 따지자면 최상위 계층의 슈퍼셋은 `Unknown` 이 됩니다.

```jsx
const anything1: unknown = {};
const anything2: unknown = {};
```

`Unknown` 타입을 만나고 `Any` 타입에 익숙한 우리는 자연스럽게 둘의 차이점이 크게 다르지 않다는 걸 느낄 수 있습니다.
그렇다면 `Unknown` 의 특징은 무엇이고 어떻게 활용 가능한지 알아보는 일은 의미가 있다고 생각합니다.

첫번째 차이점은 `Unknown` 은 다른 타입으로 선언된 변수에 할당이 불가능하다. 라는 특징이 있습니다.

```jsx
let value: unknown;

// 어느 타입이든 unknown으로 지정된 형태에 할당이 가능하다.
value = {};
value = [];
value = 1;
value = "hello world";

// 다른 타입으로 지정된 값에 unknown을 지정할 수는 없다.
let otherSpecifiedType1: string = value;
// Type 'unknown' is not assignable to type 'string'.

let value2: any;

value2 = {};
value2 = [];
value2 = 1;
value2 = "hello world";

// 별다른 에러 없이 otherSpecifiedType2 변수에 할당 가능하다 !
let otherSpecifiedType2: string = value2;
```

두번째 차이점은 `Unknown` 은 어떤 타입도 할당 가능하지만 어떤 형태도 모두 허용하는건 아니다 입니다. 말그대로 메서드가 있는 객체 형태를 `Unknown` 변수에 할당한다면 어떤 형태도 허용하는 동시에 알려지지 않은 타입이라고 판단하기에 그렇습니다.

```jsx
let variable1: any;

// 어떤 타입이든 허용하는 실제로 변수에 메서드가 있는지 판별하지 않는다.
variable1.method.func();

let variable2: unknown;

variable2 = {
  method: {
    func: function () {},
  },
};

// 알려지 않은 타입이라고 판별된 unknown은 해당 변수에 어떤 메서드가 있을지 추측이 불가능 하기 때문에 Error를 뿜는다.
variable2.method.func();
```

아래와 같이 타입 가더를 함께 사용하면 함수라고 확실히 판별된 값이라고 할 수 있으니 함수 호출이 가능해 집니다.

```jsx
let variable: unknown;

declare function isFunction(x: unknown): x is Function;

if (isFunction(variable)) {
  variable();
}
```

두단계의 타입을 확인하는 가더 역시 설계 가능하며 아래와 같이 활용 가능합니다.

```jsx
let variable: unknown;

declare function isNumberArray(arg: unknown): arg is number[];

if (isNumberArray(variable)) {
// 조건식을 만족했기에 Array method 사용 가능
  variable.forEach(function (number) {
// 조건식을 만족했기에 number method 사용 가능
    number.toString();
  });
}
```

특정 타입이 아니면 조건문 내부에 들어올 수 없는 상황을 만들어 준다면 타입 가더가 아니더라도 에러를 뿜지 않습니다.

```jsx
let variable: unknown;

if (typeof variable === "function") {
  variable(); //   let variable: Function
}
```

**never**

`never` 타입은 모든 타입의 서브셋 입니다. 가장 최하위 개념이면서 어떤 타입도 포함하지 않습니다.
이 말을 그대로 해석하자면 `never` 로 정의된 타입은 "그 어떤 타입도 사용할 수 없다"로 해석할 수 있습니다.

`never` 의 타입을 활용할 수 있는 예시로는 타입 추론에 대한 예외를 제거할 수 있다는 것 입니다.

```jsx
// **어떤 타입도 return 하지 않는 printErrorLog**

function printErrorLog(msg: string): never {
  throw new Error(msg);
}

// **printErrorLog를 호출하는 fetchFail 함수의 return 타입은 never로 추론**

function fetchFail(msg: string) {
  return printErrorLog(msg);
}

//**TS에 내장된 NonNullable 제네릭 타입은 아래와 같이 null|undefined 가 true라면 never 타입을 선언한다.**

type NonNullable<T> = T extends null | undefined ? never : T;

**// 원하는 유형의 값이 아니라면 never type으로 선언**

type DisabledAddress = null | undefined | false | number | object;
type EnabledAddress = string;

type AddressValidate = string extends DisabledAddress ? never : EnabledAddress;

const userAddress1: AddressValidate = "행신"; // Success
const userAddress2: AddressValidate = null; // Type 'null' is not assignable to type 'string'.
```

위의 예제에서는 아무런 타입도 return하지 않는다는 정의로 `never` 타입을 정의해 줬습니다. 하지만 아무런 결과를 return하지 않는 함수는 `void` 타입을 리턴한다고 하는데 제가 보기에는 이 둘이 유사한 동작을 한다고 생각했고 두 타입에 대해서 좀 더 자세히 알아볼 필요가 있었습니다.

**void**

일반적으로 아무런 값을 return하지 않는 함수는 아래와 같이 작성됩니다.

```jsx
function printLog(message: string): void {
  console.log(message);
}
```

자바스크립트에서 아무것도 return하지 않는 경우에는 `undefined` 를 return하고 `void`를 return 한다고 명시된 함수의 경우에는 함수의 기본 동작과 같습니다. `void`를 return 한다는 함수가 아무런 값도 return하지 않음과 동시에 해당 함수가 종료됨을 알 수 있습니다.

**never**

`never` 는 어떠한 타입도 정의할 수 없다를 의미합니다. 함수는 기본적으로 `undefined` 를 return하는데 `never` 를 return하는 함수는 결국에 함수가 종료되는 시점까지 가지 않는 함수를 의미합니다.

```jsx
// **어떤 타입도 return 하지 않는 printErrorLog**

function printErrorLog(msg: string): never {
	// throw new Error는 현재 함수의 실행을 중단한다. 즉 아무런 타입도 return하지 않게 된다.
  throw new Error(msg);
}

// **printErrorLog를 호출하는 fetchFail 함수의 return 타입은 never로 추론**

function fetchFail(msg: string) {
  return printErrorLog(msg);
}

**// 무한루프에 빠지는 함수는 어떤 값도 return할 수 없습니다.**
function loop(): never {
  while (true) {}
}
```

### 익명 인터페이스

만약에 인터페이스를 여러곳에서 활용할 필요가 없이 단순이 어떤 타입의 형태를 가지는지 표현을 원한다면 익명 인터페이스를 활용할 수 있습니다. 익명 인터페이스는 한눈에 타입과 정의된 객체의 속성을 볼 수 있는 장점이 있지만 객체의 형태가 복잡해 질 땐 가독성의 측면에서 떨어지는 부분이 존재합니다.

```jsx
interface User {
  name: string;
  age: number;
  address: string;
}

const user1: {
  name: string,
  age: number,
  address: string,
} = {
  name: "호이",
  age: 27,
  address: "행신동",
};

const user2: User = {
  name: "호이",
  age: 27,
  address: "행신동",
};
```

### class

class는 추가적인 키워드를 사용하여 타입과 추상 클래스 프로퍼티를 정의 할 수 있습니다. 또한 상속 제한자라는 키워드를 통해서 프로퍼티와 메서드가 어느 영역까지 접근 가능하게 할지도 아래와 같은 키워드를 통해서 제어가 가능합니다.

| 접근 제한자               | public | private | protected |
| ------------------------- | ------ | ------- | --------- |
| 클래스 내부               | O      | O       | O         |
| 상속받은 자식 클래스 내부 | O      | X       | O         |
| 클래스 인스턴스           | O      | X       | X         |

**추상 클래스**

추상 클래스의 개념은 사실 JavaScript에서는 익숙한 개념은 아니지만, 여러 정적 타이핑 언어에서는 상당히 익숙한 개념입니다.
추상화된 클래스에서 공동된 속성을 추출해서 사용하는 것도 가능하며 잘 추상화된 클래스는 슈퍼셋 클래스의 느낌과 비슷하게 활용이 가능합니다.

추상 클래스의 간단한 활용을 코드로 표현해 봤습니다.

`Beverage` 는 음료를 준비해서 서빙하고 마시는 일련의 과정을 추상적으로 포함한 추상 클래스 입니다.
아래의 예제에서는 `음료를 준비(pourToCup)` `고객에게 전달(conveyToCustomer)` `음료를 마신다(drink)` 와 같은 공통적인 메서드를 가지지만 경우에 따라서 어떤 음료는 서빙을 위해서 뜨거운 물을 통해서 내려야 하는 과정이 있을 수 있고, Beverage는 공동적인 동작과 프로퍼티만을 정의해 둡니다.

```jsx
abstract class Beverage {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  pourToCup() {}
  drink() {}
  conveyToCustomer() {}
}

class Coke extends Beverage {
  super(name: string) {
    this.name = name;
  }

  pourToCup() {
    console.log(`${this.name}을 잔에 따라요.`);
  }

  drink() {
    console.log(`${this.name}을 마셔요.`);
  }

  conveyToCustomer() {
    console.log(`${this.name}을 손님에게 서빙해요.`);
  }
}

class Coffee extends Beverage {
  super(name: string) {
    this.name = name;
  }

  drip() {
    console.log(`${this.name}을 내려요.`);
  }

  pourToCup() {
    console.log(`${this.name}을 잔에 따라요.`);
  }

  drink() {
    console.log(`${this.name}을 마셔요.`);
  }

  conveyToCustomer() {
    console.log(`${this.name}을 손님에게 서빙해요.`);
  }
}
```
