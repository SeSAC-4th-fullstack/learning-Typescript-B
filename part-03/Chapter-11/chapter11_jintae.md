# Chapter 11

## 11.1 선언 파일

.d.ts 확장자를 사용하여 타입만 선언할 수 있다. 런타임 코드를 포함 할 수 없다. 

선언 파일은 ‘앰비언트 컨텍스트’ 라는 코드 영역을 생성한다. ***앰비언트 컨텍스트는 타입만 선언 할 수 있다.***

선언 파일도 import 해서 사용할 수 있다.

```tsx
// types.d.ts
export interface Character {
	catchphrase?: string;
	name: string;
}
```

```tsx
// index.ts
import { Character } from "./types"; // 선언 파일을 import

export const character: Character = {
	catchphrase: "Yahoo!",
	name: "꾸러기",
};
```

## 11.2 런타임 값 선언

선언 파일에서 declare 키워드를 사용하면 런타임 값이 존재하는 것처럼 선언할 수 있다. 단, declare로 변수를 선언하면 변수의 초기값을 허용하지 않는다.

여담으로, 모듈 또는 스크립트 파일에서도 declare 키워드를 사용할 수 있다. declare 키워드가 붙은 변수는 전역 변수로 취급한다.

```tsx
// types.d.ts
declare let a: string; // a 변수, string 타입. 선언.
declare let b: string = "b"; // Error

declare const c: string; // c 변수, string 타입. 선언.
declare const d = "d"; // d 변수, 리터럴 타입. 선언.
declare const e: string = "e"; // Error
```

선언 파일에서 함수와 클래스도 유사하게 선언되지만, 함수의 본문과 메서드의 본문이 없다.

인터페이스의 경우 declare를 붙이나 안붙이나 상관 없이 허용된다.

```tsx
// fairies.d.ts
declare function canGrantWish(wish: string): boolean; // OK.

class Fairy{
	canGrantWish(wish: string): boolean; // OK.
	
	grantWish(wish: string) { // Error.
		return true;
	}
}

declare interface Writer {} // OK.
interface Writer {} // OK.
```

import 또는 export 문이 없는 타입스크립트 파일은 스크립트 취급되기 때문에, 여기에 선언된 타입을 포함한 구문은 전역으로 확장될 수 있다.

```tsx
// globals.d.ts
declare const version: string; // 전역으로 존재.
```

```tsx
// version1.ts
function logVersion() {
	console.log(`Version: ${version}`); // OK. 스크립트에서 접근 가능.
}
```

```tsx
// version2.ts
export function logVersion() {
	console.log(`Version: ${version}`); // OK. 모듈에서 접근 가능.
}
```

import 또는 export 문이 없는 선언 파일은 전역 스크립트 컨텍스트를 갖기 때문에, 여기서 인터페이스를 선언하면 전역으로 확장되고, 인터페이스 특성에 의하여 동명의 인터페이스는 병합 될 수 있다.

아래에서, 인터페이스 병합을 이용하면 Window 타입의 전역 window 변수에 존재하는 변수를 선언할 수 있도록 허용한다.

```tsx
// types/window.d.ts
interface Window {
	myVersion: string;
}
```

```tsx
// index.ts
export function logWindowVersion() {
	console.log(`Window Version is: ${window.myVersion}`);
}
```

```html
<script type="text/javascript">
	window.myVersion = "3.1.1";
</script>
```

declare global 코드 블록 구문을 사용하면, 모듈에서도 전역 컨텍스트를 부분적으로 사용할 수 있다.

```tsx
// types/data.d.ts
export interface Data {
	version: string;
}
```

```tsx
// types/globals.d.ts
import { Data } from "./types/data";

declare global { // 전역 컨텍스트에 등록
	const globallyDeclared: Data;
}

declare const locallyDeclared: Data; // 모듈 컨텍스트에 등록
```

```tsx
// index.ts
import { Data } from "./types/data";

function logData(data: Data) {
	console.log(`Data version is ${data.version}`);
}

logData(globallyDeclared); // OK.
logData(locallyDeclared); // Error. 
```

## 11.3  내장된 선언

전역 객체는 실행되는 런타임 코드에 의해 제공된다.

자바스크립트 런타임에 존재하는 Array, Function 같은 내장된 전역 객체는 ***lib.[target].d.ts*** 파일 이름으로 선언된다. 여기에서 target은 프로젝트에서 대상으로 하는 ES5, ES2020 같은 자바스크립트의 최소 지원 버전을 말한다. lib 파일은 타입스크립트 npm 패키지의 일부로 배포된다.

```tsx
// lib.es5.d.ts
interface Array<T> {
	// 배열의 길이를 가져오거나 설정함
	length: number;

	// ...
}
```

자바스크립트 언어 자체 외에도, DOM 타입은 lib.dom.d.ts 파일과 다른 lib.*.d.ts 선언 파일에도 저장된다. 전역 DOM 타입은 종종 전역 인터페이스로 설명된다.

```tsx
// lib.dom.d.ts
interface Storage {
	// 키-값 쌍의 수를 반환
	readonly length: number;

	// 모든 키-값 쌍을 제거
	clear(): void;

	// ...
}
```

## 11.4 모듈 선언

선언 파일의 또 다른 중요한 기능은 모듈의 상태를 설명하는 기능이다.

declare module “모듈 이름” 을 통해 모듈을 선언하고, 모듈의 내용을 타입 시스템에 알릴 수 있다.

```tsx
// modules.d.ts
declare module "my-example-lib" {
	export const value: string;
}
```

```tsx
// index.ts
import { value } from "my-example-lib";

console.log(value); // OK.
```

모듈 선언은 와일드 카드를 포함해 해당 패턴과 일치하는 모든 모듈을 나타낼 수 있다.

```tsx
// styles.d.ts
declare module "*.module.css" {
	const styles: { [i: string]: string; };
	export default styles;
}
```

```tsx
// component.ts
import styles from "./styles.module.css";

styles.anyClassName; // styles[string]: string
```

## 11.5 패키지 타입

패키지란, npm에 업로드되는 라이브러리 및 모듈을 말한다.

타입스크립트로 작성된 프로젝트는 여전히 .js로 컴파일된 파일을 패키지에 포함시켜 배포한다. 일반적으로 .d.ts 파일을 사용해 이러한 자바스크립트 파일 뒤에 타입스크립트 타입 시스템 형태를 지원하도록 선언한다.

*타입스크립트 프로젝트: index.ts → 선언 → index.js, index.d.ts*

```tsx
// index.ts
export const greet = (text: string) => {
	console.log(`Hello ${text}`);
}
```

```jsx
export const greet = (text) => {
	console.log(`Hello ${text}`);
}
```

```tsx
// index.d.ts
export declare const greet: (text: string) => void;
```

타입스크립트는 프로젝트의 node_modules 의존성 내부에서 번들로 제공되는 .d.ts 파일을 감지하고 활용할 수 있다.

.d.ts 선언 파일과 함께 제공되는 npm 모듈은 대부분 다음과 같은 구조를 갖고 있다.

- lib/
    - index.js
    - index.d.ts
- package.json

다음의 경우, jest 패키지 내에 자체 번들 .d.ts 파일을 제공한다.

```json
// package.json
{
	"devDependencies": {
		"jest": "^32.1.0"
	}
}	
```

```tsx
// using-globals.d.ts
describe("MyAPI", () => {
	it("works", () => { /* ... */ });
});
```

```tsx
// using-imported.d.ts
import { describe, it } from "@jest/globals";

describe("MyAPI", () => {
	it("works", () => { /* ... */ });
});
```

사용자를 위한 타입을 제공하려면 패키지의 package.json 파일에 types 필드를 추가해 루트 선언 파일을 가리키면 된다.

main 필드는 반드시 .js 확장자를 사용해야 하고, types 필드는 반드시 .d.ts 확장자를 사용해야 한다.

```json
{
	"author": "Pendant Publishing",
	"main": "./lib/index.js",
	"name": "coffeetable",
	"types": "./lib/index.d.ts",
	"version": "0.5.22",
}	
```

## 11.6 DefinitelyTyped

타입스크립트 프로젝트를 위하여, 해당 패키지에 모듈의 타입 형태를 알려주는 깃헙 저장소이다. 줄여서 DT라고도 한다. DT 패키지는 타입을 제공하는 패키지와 동일한 이름으로 npm에 @types 범위로 게시된다.

@(스코프)를 사용하면, 충돌 ****없이 다른 사용자 또는 조직에 의해 작성된 패키지와 동일한 이름의 패키지를 만들 수 있다.

참고: https://docs.npmjs.com/about-scopes

dependencies와 devDependencies의 차이를 명확히 하는 게 바람직하다. 

- dependencies 필드: 프로젝트가 npm 패키지로 배포되어야 하는 경우에만 사용
- devDependencies 필드: 프로젝트가 독립 실행형 애플리케이션인 경우에만 사용

```json
// package.json
{
	"dependencies": {
		"react": "^18.1.0"
	},
	"devDependencies": {
		"@types/react": "^18.0.9"
	},
}
```