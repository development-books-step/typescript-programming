### 추가적으로 확인하고 싶었던 점

1. 익명 인터페이스
   1. 익명 인터페이스를 실제로 사용하는가?
2. any 사용
   1. any를 현업에서 실제로 사용하는가? (noImplicitAny → true?)
3. Interface Naming Convention
   1. 인터페이스 네이밍 컨벤션은 무엇일까?

### 익명 인터페이스

결론은 익명 인터페이스는 사용하지 않는 것으로!
익명 인터페이스는 아래와 같이 복잡하게 느껴질 수 있다.

```jsx
// define a plain object
let ross = {
	firstName: 'Ross',
	lastName: 'Geller',
	age: 32,
	getSalary: ( base: number ) => base * 12
};

// function that accepts object as an argument
let getPersonInfo = ( arg: {
	firstName: string;
	lastName: string;
	age: number;
	getSalary: (base: number) => number;
} ): string => {
	return `Hello, ${arg.firstName} ${arg.lastName}.`;
};

// get person info
console.log(getPersonInfo(ross));
-> Hello, Ross Geller.
```

익명을 사용하지 않은 수정본은 아래와 같다.

```jsx
// define `Person` interface
interface Person {
	firstName: string;
	lastName: string;
	age: number;
	getSalary: (base: number) => number;
};

// define an object of type `Person`
let ross: Person = {
	firstName: 'Ross',
	lastName: 'Geller',
	age: 32,
	getSalary: ( base: number ) => base * 12
};

// function that returns info of a `Person` object
let getPersonInfo = (person: Person): string => {
  return `Hello, ${person.firstName} ${person.lastName}.`;
};

// get person info
console.log(getPersonInfo(ross));
-> Hello, Ross Geller.
```

### any 사용

비지니스와 상황에 따라 다르겠지만 any사용을 대부분 '지양' 하는 것으로 본다.
지양하는 이유는 대부분 협업할때 다른 누군가가 작성한 코드를 any로 인해 이해하기 어렵다고 한다.
그리고 또 다양한 이유가 있었지만 그 중 하나를 아래 정리했다.

type을 추가하게 된다면 코드양이 많아지니 any를 사용해서 코드양을 줄일 수 있다고 생각하지만 결국은 아니다. 타입없이 코드를 짜게되면 어차피 방어 코드를 추가적으로 짜야하기에 코드양이 준다고 볼 수 없고, 그리고 중요한 것은 아래 예제와 같이 any는 null이나 undefined와 같은 체크를 하지 않기에 방어코드가 필요하다.

```jsx
// version 1 with `any`
const fullName = (user: any) => {
  if (user?.firstName && user?.lastName) {
    return `${user.lastName}, ${user.firstName}`
  }

  return user?.firstName || ""
}

// version 1 without `any`
interface User {
  firstName: string
  lastName?: string
}

const fullName = ({ firstName, lastName }: User) => {
  if (lastName === undefined) {
    return firstName
  }

  return `${lastName}, ${firstName}`;
}

```

### Interface Naming Convention

인터페이스 네이밍을 보통 어떻게 가져가는 지 검색해 보았으나 명쾌한 설명이 나온 글을 찾지 못했다.
package나 boilerplate와 같은 것을 열어보고 확인하는 방법도 좋을 거라고 생각한다.
일단 찾아본 것으로 가져올 만한 것은 아래와 같다.

**prefix로 I 사용하지 말 것!**
책에는 예제로 인터페이스 앞에 I를 다 붙이고 있는데 이 방법은 좋지 않다고 한다. (아래 링크 참조)

[https://stackoverflow.com/questions/31876947/confused-about-the-interface-and-class-coding-guidelines-for-typescript](https://stackoverflow.com/questions/31876947/confused-about-the-interface-and-class-coding-guidelines-for-typescript)

**props**

props의 경우는 component 이름 뒤에 Props 를 붙여서 사용한다.

```jsx
interface PostProps {
  title: string;
  body: string;
}

const Post: React.FC<PostProps> = ({ title, body }) => {};
```
