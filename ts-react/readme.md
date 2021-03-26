# TS React

```command
npm init
npm i typescript
npm i react react-dom
npm i webpack webpack-cli -D
npm i awesome-typescript-loader -D
npm i @types/react @types/react-dom
npm i react-hot-loader

npx webpack
```

- 웹팩과 타입스크립트를 연결해주는 로더

  - ts-loader
  - awesome-typescript-loader
    - 타입스크립트와 바벨을 연결해서 사용하는 경우가 있다.

- 설치 바뀐 내용

```command
npm rm react-hot-loader awesome-typescript-loader
npm i ts-loader -D
npm i fork-ts-checker-webpack-plugin -D
npm i @pmmmwh/react-refresh-webpack-plugin -D
npm i ts-node react-refresh babel-loader -D
npm i webpack-dev-server -D
npm i @types/webpack @types/webpack-dev-server -D
```

- 제네릭은 타입이 제대로 안잡혔을 때 그 타입을 제대로 잡아주는 역할(수동으로)

- axios, fetch와 같이 사용법이 정해져있는 경우는 타입 추론이 잘 되는 반면, redux, mobx 같은 경우는 타입 추론이 어렵다.
- 자유도가 높을수록 어렵다.

- 빈 배열은 never[]가 되기 때문에 타입을 붙여줘야한다.

```tsx
const [result, setResult] = useState<number[]>([]);
```

## keyof typeof

```tsx
const rspCoords = {
  바위: "0",
  가위: "-142px",
  보: "-284px",
} as const;

type imgCoords = typeof rspCoords[keyof typeof rspCoords];
```

- keyof typeof rspCoords 는 "바위" | "가위" | "보" 가 되고,
- typeof rspCoords[keyof typeof rspCoords] 는 "0" | "-142px" | "-284px" 가 된다.

## 참고

[DefinitelyTyped | React](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/react/index.d.ts)

## 강좌

4-3
