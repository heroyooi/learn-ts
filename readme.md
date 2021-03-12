# Learn TS

## TS 설명

- TS는 JS의 superset이다.
- TS는 JS의 변수, 함수의 매개변수, 함수의 리턴 값에 타입이 명시적으로 붙은 것이다.

- 간단한 TS 설치 및 JS 파일 변환
```command
npm init
npm i typescript
npm i -g typescript
tsc lecture.ts -w
```
- 글로벌로 설치해서 실행
- 이렇게 할 경우 나중에 TS 버전이 달라질 경우 문제가 될 수 있다.

```command
npx tsc lecture.ts -w
```
- npx로 실행
- 프로젝트 버전과 명령어 버전을 일치시킬 수 있다.

- 나중에 확장성을 고려해서 복잡하게 될 것 같을 때 TS를 도입하는 것이 좋다.
- 간단한 것은 TS를 도입할 필요 없다.
- TS는 가독성이 안 좋다.

## TS 설정

- tsconfig.json 파일 생성 후 아래 옵션들 정의
  - compilerOptions
    - outDir: 컴파일된 JS파일을 다른 폴더에 옮기고 싶을 경우
    - target: 중요! 기본적으로 ES3로 되어있음. IE11까지면 ES5로 설정하면 된다.
      - ES6로 하면 IE에서 안 돌아감. 그 때는 바벨로 추가적으로 컴파일한다.
    - types, typeRoots: 내가 만든 커스텀 d.ts 경로를 지정해준다.
    - strict: 엄격하게 체크하기 위해서 true로 하는 것이 좋다.
    - module: IE를 지원할 경우 CommonJS
    - allowJs: JS 파일 컴파일
    - checkJs: JS 에러도 체크

  - include: 어떤 파일들을 컴파일 할 것인지 설정
  - exclude: 어떤 파일들을 컴파일 목록에서 제외할 것인지를 설정
  - extends: 여러개 프로젝트가 있는 경우 사용

## 타입 표기법

- 배열 타입
  - 기본 표기법 2가지
```TS
let arr: number[] = [1, 2, 3]
let arr: Array<number> = [1, 2, 3]
```

  - 여러가지 타입으로 들어가는 경우
```TS

let arr: (string | number | boolean)[] = [true, 2, '3'];

// 더 염격하게 자리까지 맞춤, Tuple: 정확하게 갯수를 지정해줌
let arr: [boolean, number, string] = [true, 2, '3'];

// 더 엄격하게 값까지 맞춤
let arr: [boolean, 2, string] = [true, 2, '3'];

// 아예 상수처럼 안 바뀌게 하고 싶을 경우, 뒤에 as const를 붙여준다.
let arr = [true, 2, '3'] as const;
```

- 객체 타입
```TS
const obj: { a: string, b: number } = { a: 'b', b: 3 };

// b가 있을 수도 없을 수도 있는 경우
const obj: { a: string, b?: number } = { a: 'b' };
// 나중에 타입 부분을 interface로 빼게 된다.

// 복잡한 경우
const obj2: { a: (b: number) => string } = {
  a(b: number) {
    return 'hello'
  }
}
```

- enum 타입
```TS
enum Color { Red, Green, Blue }
let c: Color = Color.Green;

Color[0] === 'Red';
Color['Red'] === 0;
Color[1] === 'Green';
Color['Green'] === 1;
```

- 함수 타입
```TS
function add(a: number, b: number): number {
  return a + b;
}

// 리턴 없는 함수
function add(a: number, b: number): void { // void는 생략 가능
  console.log(a, b);
}

// 고차 함수
function add(a: number, b: number): (c: string) => number { // 함수 자체를 타입으로 사용
  return (c: string) => {
    return 3;
  }
}

// 고차 함수, 더 복잡한 경우
function add(a: number, b: number): (c: string) => (d: string) => boolean {
  return (c: string) => {
    return (d: string) => {
      return false;
    }
  }
}
```

## 오버 로딩

```TS
const obj: { a: (b: number, c?: string) => string } = {
  a(b: number, c?: string) {
    return 'hello'
  }
}

obj.a() // X, 매개 변수 b가 없기 때문에 에러가 난다.
obj.a(3) // O
obj.a(3, 'hello') // O
```
- 오버로딩 관계를 명확하게 표시해야한다.

## never any

```TS
const arr: [] = [];
arr.push(3); // X, never 타입이기 때문에 에러가 난다.
```
- 거의 대부분의 경우 배열을 잘못 만든 경우 never라는 에러가 난다.

```TS
const hi: any = [];
```
- any는 아무거나 들어간다.
- TS를 도입하는 사람은 any를 쓰면 의미가 없어진다.
- 남이 만들어둔 d.ts 파일에서 타입을 잘못 만든 경우, any를 사용할 때가 있다.

## 타입 캐스팅

```TS
const hello: number = 3; // 남이 타입을 잘못 선언한 경우

(hello as unknown as string).substr(1, 2); // 타입 캐스팅1, 강제로 hello의 타입을 string으로 바꾸어서 사용한다.
(<string><unknown>hello).substr(1, 2); // 타입 캐스팅2
```

```TS
const div: HTMLDivElement = document.createElement('div');
const a = div as HTMLElement
```
- unknown을 붙이지 않아도 된다.
- 이유는 d.ts 파일에 아래와 같이 관계가 정의 되어있기 때문
```TS (lib.dom.d.ts)
interface HTMLDivElement extends HTMLElement {} // 상속 관계
```

- 남이 만든 것은 타입 추론을 쓰고, 내가 만든 변수엔 타입을 쓴다.
  - 남이 만든 것은 패키지 업데이트 할 때 바뀔 수가 있기 때문에
```TS
const result: HTMLHeadingElement = document.createElement('h1');
```
  - 미래에 남이 만든 HTMLHeadingElement 타입이 바뀔 수도 있으니깐

## 인터페이스, 타입 alias

```TS
interface hello {
  a: string;
  b?: number;
}
interface helloChild extends hello {
  c?: boolean;
}

type hello2 = {
  a: string;
  b?: number;
}
type stringOfNumber = string | number;
```

## 참고 주소
- [TS Compiler Options](https://www.typescriptlang.org/docs/handbook/compiler-options.html#compiler-options)
- [DefinitelyTyped | lodash](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/lodash)
- [DefinitelyTyped | jQuery](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/jquery)

## ZeroCho 저장소

- [ts-lecture](https://github.com/ZeroCho/ts-lecture)
- [ts-react](https://github.com/ZeroCho/ts-react)
- [ts-nodebird](https://github.com/ZeroCho/ts-nodebird)

## 공부 계획

- learn-ts (webgame, react, nodebird)
- learn-react (nodebird, slack)
- learn-vue (nodebird)
  - 세세한 내용을 readme에 작성
  - 코드에 상세 주석

## 강좌

- 타입스크립트 강좌 3-1