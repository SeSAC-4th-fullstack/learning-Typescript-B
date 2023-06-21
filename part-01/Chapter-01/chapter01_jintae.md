# Chapter 01

## 1.1 자바스크립트의 역사

95년생, 한국 나이로 29살..

## 1.2 바닐라 자바스크립트의 함정

```jsx
function paintPainting(painter, painting) {
	return painter
		.prepare()
		.paint(painting, painter.ownMaterials)
		.finish();
}
```

너무 높은 자유도 때문에 코드의 이해가 어렵다.

```jsx
/**
* Performs a painter painting a particular painting.
*
* @param {Painting} paainter
* @param {string} painting
* @returns {boolean} Whether the painter painted the painting.
*/
function paintPainting(painter, painting) { /* ... */ }
```

자바스크립트 코드에서 변수의 의미나 타입등을 파악하기엔 문서의 정보만으로는 부족하다. 그래서 많은 개발자들이 주석을 통해 표현하는 방식인, JSDoc를 채택하여 사용한다.

## 1.3 타입스크립트

10년생, 한국 나이로 14살..

타입스크립트는 종종 자바스크립트의 상위 집합superset 또는 타입이 있는 자바스크립트라고 소개되기도 한다.

타입스크립트는 다음 네 가지로 설명가능하다.

- 프로그래밍 언어: 고유 구문을 가진 언어
- 타입 검사기: 변수, 함수등을 이해하고 잘못 구성된 부분을 알려주는 타입 검사 프로그램
- 컴파일러: 타입 검사기를 실행한 결과물로써 자바스크립트 언어를 생성하는 프로그램
- 언어 서비스: VS Code 같은 편집기에서 개발자들에게 유용한 유틸리티 제공법을 알려주는 프로그램

## 1.4 타입스크립트 플레이그라운드

핸드북: https://www.typescriptlang.org/ko/docs/handbook/intro.html

공식 플레이그라운드: https://www.typescriptlang.org/ko/play

타입스크립트 Exercises: https://typescript-exercises.github.io/#exercise=1&file=%2Findex.ts

```tsx
interface Painter {
	finish(): boolean;
	ownMaterials: Material[];
	paint(painting: string, materials: Material[]): boolean;
}

function paintPainting(painter: Painter, painting: string): boolean { /* ... */ }
```

타입스크립트를 사용함으로써 코드를 통해 정확한 문서화가 가능하다.

타입스크립트가 타입을 검사한 후 자바스크립트를 내보냅니다. 이를 Emit이라고 표현한다.

## 1.5 로컬에서 시작하기

컴퓨터에 Node.js가 설치되어 있으면 타입스크립트를 실행할 수 있다.

실행 명령어 

$> tsc index.ts

## 1.6 타입스크립트에 대한 오해

타입스크립트는 잘못된 코드 해결책이 아니다. 정말 타입 확인 작업에만 관여한다.

즉, 타입스크립트 문법적으로 틀린 코드가 자바스크립트 코드로 변환될 수 있다. 하지만 의도하지 않은 타입의 데이터들의 동작 때문에 일어나는 에러들은 반드시 지옥으로 인도할 것이다.