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

- 타입스크립트 강좌 1-7