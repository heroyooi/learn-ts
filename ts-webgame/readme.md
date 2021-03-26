# TS Webgame

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
npx tsc -w
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

## interface, type aliases

### interface

```TS
interface RSP {
  readonly ROCK: '0';
  readonly SCISSORS: '-142px';
  readonly PAPER: '-284px';
}
interface Example extends RSP {} // 확장 가능
const rsp: Example = { // 상속 받아서 사용
  ROCK: '0';
  SCISSORS: '-142px';
  PAPER: '-284px';
}
function computerChoice(imgCoords): keyof RSP { // 'ROCK' | 'SCISSORS' | 'PAPER'
  return Object.keys(rsp).find((k) => rsp[k] === imgCoords);
}
```

- 상속 받는 것이 가능하다.
- keyof 인터페이스: 키들만 뽑아서 정의할 수도 있다.

```TS
interface Example {
  a: 3,
  b: 7,
  [key: string]: number;
}

const example: Example = {
  a: 3,
  b: 7,
  c: 1,
}
```

- 무엇이 들어올 지 모르는 경우 [key: string] 로 여유를 둘 수 있다.

```TS
interface RSP {
  readonly ROCK: '0';
}
interface RSP {
  readonly SCISSORS: '-142px';
  readonly PAPER: '-284px';
}
```

- 이렇게 2번 선언하면 RSP 2가지 내용이 합쳐진다.

```TS
interface Example {
  add: (a: number, b: number) => number
}
const ex: Example = {
  add: (a, b) => {
    return a + b;
  }
}
```

- 객체 안에서 함수를 가질 경우

### type aliases

```TS
type Hello = {
  ROCK: string;
  PAPER: string;
} | string;
type Hello2 = string | number

const hi: Hello = {
  ROCK: 'a',
  PAPER: 'b',
};
```

- 타입은 2번 선언할 수 없다. 다양하게 정의할 수 있다.

### interface VS type aliases

- 타입이 좀 더 넓은 범위

  - 인터페이스는 주로 객체, 타입은 좀 더 다재다능한 개념
  - 타입은 새로운 타입을 만들어 낼 수 있다.

- 객체는 인터페이스로만 쓰겠다고 정하는 것이 좋다.

## d.ts 문제 해결하기

```TS
return (Object.keys(rsp) as ['ROCK', 'SCISSORS', 'PAPER']).find((k) => rsp[k] === imgCoords)!;
```

- find의 d.ts 파일을 보면 undefined의 가능성을 갖고 있기 때문에 !로 프로그래머가 보증을 해준다.

```TS
btn.addEventListener('click', function(this: HTMLButtonElement, e: Event) {
  const myChoice = this.textContent as keyof RSP;
});
```

- 함수 내부에서 this를 사용하는 경우 첫번째 매개변수를 this로 타입과 함께 정의해줘야한다.

## strictNullChecks

- null과 undefined를 구분해야하는 경우가 있고, 안해야하는 경우가 있다.

- strictNullChecks: false인 경우 구분을 안한다.

```TS
interface Player {
  heroData?: Card // Card | null | undefined 다 허용
  chosenCard?: HTMLDivElement // ?는 HTMLDivElement | undefined과 동일
}
```

- strictNullChecks: true인 경우 구분을 한다.
  - strict를 true로 하면 자동으로 true로 설정된다.

```TS
interface Player {
  heroData: Card | null
  chosenCard?: HTMLDivElement | null
}
```

## Class 타이핑

```TS
class Card {
  public att: number;
  protected hp: number;
  private cost: number;
}
```

- private: 내 클래스 안에서만 접근 가능
- protected: 내 클래스, 나를 상속받은 자식들만 접근 가능
- public: 내 클래스, 나를 상속받은 장식, 내 인스턴스에서도 접근 가능

```TS
interface ICard {
  att?: number;
  hp?: number;
}

class Card implements ICard {
  public att?: number;
  public hp?: number;
}
```

- class가 지켜야하는 것을 implements로 표현할 수 있다.

## 제네릭

- 한단어로 표현하자면 짝맞추기

```TS
interface obj<T> {
  add: (a: T, b: T) => T;
}
const a: obj<number> = {
  add: (a, b) => a + b,
};
const b: obj<string> = {
  add: (a, b) => a + b,
}
a.add(1, 2)
b.add('a', 'b');
```

- T 선언은 여유롭게, 실제로 interface를 사용할 때 T를 정의

```TS
function forEach<T>(arr: T[], callback: (item: T) => void): void {
  for (let i: number = 0; i < arr.length; i++) {
    callback(arr[i]);
  }
}

forEach<number>([1, 2, 3], (item) => {});
```

- 제네릭 개념을 사용하여 forEach 함수를 만듦

## 타입 가드

```TS
interface Card {
  att: number;
  hp: number;
  mine: boolean;
  cost?: number;
  field?: boolean;
  hero?: boolean;
}

class Sub implements Card {
  public att: number;
  public hp: number;
  public field: boolean = false;
  public cost: number;
  public mine: boolean;
  constructor(mine: boolean) {
    this.mine = mine;
    this.att = Math.ceil(Math.random() * 5);
    this.hp = Math.ceil(Math.random() * 5);
    this.cost = Math.floor((this.att + this.hp) / 2);
  }
}

const isSub = function (data: Card): data is Sub {
  if (data.cost) {
    return true;
  }
  return false;
};
```

- 넓은 타입(Card)을 좁은 타입(Sub)로 좁혀줄 수 있다.

## 기타

```TS
cardEl.querySelector(".card-cost") as HTMLDivElement
```

- querySelector를 사용하면 보통 위와같이 형변환을 해준다.

## 참고 주소

- [TS Compiler Options](https://www.typescriptlang.org/docs/handbook/compiler-options.html#compiler-options)
- [DefinitelyTyped | lodash](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/lodash)
- [DefinitelyTyped | jQuery](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/jquery)

## 강좌

- 타입스크립트 강좌 4-7
