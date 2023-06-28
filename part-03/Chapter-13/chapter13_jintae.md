# Chapter 13

## 13.1 tsc 옵션

타입스크립트 파일을 컴파일 할 때 tsc 명령에서 — 플래그를 통해 컴파일 옵션을 줄 수 있다.

```bash
$> tsc index.ts --noEmit
```

## 13.2 TSConfig 파일

tsc 명령에서 — 플래그를 통해 옵션으로 조절하는 것 대신, tsconfig.json 파일에 구체적으로 명시하여 조절할수 있다.

tsconfig.json 파일이 존재하는 디렉토리는 프로젝트의 루트임을 나타낸다.

```bash
# tsconfig.json 생성 명령어
$> tsc --init
```

```json
// tsconfig.json
{
	"compilerOptions": {
		// ...
		// ...
		"strict": true,
		// ...
	}
}
```

## 13.3 파일 포함

tsc는 기본적으로 현재 디렉터리와 하위 디렉터리에 있는 숨겨지지 않은 모든 .ts 파일을 컴파일한다.

숨겨진 디렉터리와 node_modules는 무시한다.

tsconfig.json에서 include 속성을 사용하면, 실행할 파일을 사용자가 직접 수정할 수 있다. 아래에는 src/ 아래의 모든 타입스크립트 소스 파일을 재귀적으로 포함하는 예시이다.

```json
{
	"include" : ["src"]
}
```

tsconfig.json에서 exclude 속성을 사용하면, 실행할 파일을 의도적으로 제외할 수 있다. 단, include 된 파일 목록들 중에서만 제외가 가능하다.

*: 0개 이상의 문자와 일치(디렉터리 제외)

?: 1개 이상의 문자와 일치(디렉터리 제외)

**/: 중첩된 모든 디렉터리와 일치

```json
{
	"exclude" : ["**/external", "node_modules"],
	"include" : ["src"]
}
```

## 13.4 대체 확장자

타입스크립트는 기본적으로 확장자가 .ts인 모든 파일을 읽을 수 있다. 

때에 따라서, JSON 모듈이나 jsx 확장자를 읽을 수 있어야 한다.

먼저 jsx 확장자를 읽어야 할 경우, 다음 두 가지를 수행해야 한다.

- “jsx” 컴파일러 옵션 활성화
- .tsx 확장자로 파일 이름 지정

```json
// tsconfig.json에서 jsx 컴파일러 옵션 활성화
{
	"compilerOptions": {
		"jsx": "preserve"
	}
}
```

```bash
# CLI에서 jsx 컴파일러 옵션 활성화
$> tsc --jsx preserv
```

.tsx 확장자에서 제네릭 화살표 함수를 사용할 때는, JSX 구문의 T태그와 충돌하지 않도록 타입 인수에 제약 조건을 걸어서 조치를 취해야 한다.

```tsx
const identity = <T = unknown>(input: T) => input;
```

다음으로, JSON 모듈을 읽는 경우, resolveJsonModule 컴파일러 옵션을 활성화 해야 한다.

이렇게 하면 .json 파일을 마치 객체를 내보내는 .ts 파일인 것처럼 가져오고 해당 객체의 타입을 const 변수인 것처럼 유추한다.

객체가 포함된 JSON 파일이라면 구조 분해 가져오기를 사용한다.

```json
// activitst.json
{
	"activist": "Mary Astell"
}
```

```tsx
// useActivist.ts
import { activist } from "./activist.json"; // 구조 분해 가져오기

console.log(activist); // Mary Astell
```

## 13.5 자바스크립트로 내보내기

tsc 명령을 사용해 자바스크립트를 출력하는 작업에 대해서 알아보자.

### outDir

outDir 컴파일러 옵션을 사용하면 출력 파일의 루트 디렉터리를 다르게 지정할 수 있다.

```bash
$> tsc --outDir 디렉터리
```

**실행 전**

- fruits/
    - apple.ts
- vegetables/
    - zucchini.ts

**실행 후**

- dist/
    - fruits/
        - apple.js
    - vegetables/
        - zucchini.js
- fruits/
    - apple.ts
- vegetables/
    - zucchini.ts

### target

자바스크립트 코드 구문을 지원하기 위해 어느 버전까지 변환해야 하는지를 지정하는 컴파일러 옵션이다.

tsc —init에서 기본적으로 “es2016” 구문을 지원한다.

```json
{
	"compilerOptions": {
		// ...
		"target": "es2016",
		// ...
	}
}
```

```bash
tsc -- target es2015
```

### 내보내기 선언

소스 파일에서 .d.ts 출력 파일을 내보내는 컴파일러 옵션이다.

```json
{
	"compilerOptions": {
		"declaration": true
	}
}
```

```bash
$> tsc --declaration
```

### 소스 맵

소스 맵은 출력 파일의 내용이 원본 소스 파일과 어떻게 일치하는지에 대한 설명이다.

타입스크립트는 소스 맵을 출력하는 기능도 제공한다.

```bash
# .js 또는 .jsx 출력 파일과 함께 .js.map 또는 .jsx.map 소스 맵을 생성하는 옵션
$> tsc --sourceMap

# .d.ts 선언 파일에 대한 소스 맵을 생성하는 옵션
$> tsc --declarationMap
```

### noEmit

소스 파일을 컴파일 하고, 자바스크립트로 내보내는 작업을 수행하지 않는 컴파일 옵션이다. 

## 13.6 타입 검사

타입스크립트가 런타임 환경에 있다고 가정하는 전역 API는 lib 컴파일러 옵션으로 구성할 수 있다.

```bash
$> tsc --lib es2020
```

```json
{
	"compilerOptions": {
		"lib": ["es2020"]
	}
}
```

skipLibCheck 컴파일러 옵션은 소스 코드에 명시적으로 포함되지 않은 선언 파일에서 타입 검사를 건너뛰도록 한다.

```bash
$> tsc --skipLibCheck
```

```json
{
	"compilerOptions": {
		"skipLibCheck": true
	}
}
```

strict mode는 타임 검사기에 일부 추가적인 검사를 켜도록 지시한다.

```bash
$> tsc --strict
```

```json
{
	"compilerOptions": {
		"strict": true
	}
}
```

noImplicitAny 컴파일러 옵션은 암시적 any로 대체될 때, 타입 검사가 오류를 발생하도록 지시한다.

```tsx
const logMessage = (message) => { // Error. parameter implicitly has an 'any' ..
	console.log(`Msg: ${message}`); 
}
```

strictBindCallApply 컴파일러 옵션은 함수 유틸리티를 더욱 제한적인 타입으로 나타낼 수 있게 한다.

```tsx
function getLength(text: string, trim?: boolean) {
	return trim? text.trim().length : text;
}

getLength.apply; // 함수 타입: 
// (thisArg: typeof getLength, args: [text: string, trim?: boolean]) => number

getLength.bind(undefined, "abc123"); // 반환 타입: (trim?: boolean) => number

getLength.call(undefined, "abc123", true); // 반환 타입: number
```

strictFunctionTypes 컴파일러 옵션은 함수 매개변수 타입을 약간 더 엄격하게 검사한다.

매개변수가 다른 타입의 매개변수 하위 타입인 경우 함수 타입은 더 이상 다른 함수 타입에 할당 가능한 것으로 간주되지 않는다.

```tsx
function checkOnNumber(containsA: (input: number | string) => boolean) {
	return containsA;
}

function stringContainsA(input: string) {
	return !!input.match(/a/i);
}

checkOnNumber(stringContainsA) // Error. 
// type 'number' is not assignable to type 'string'
```

strictNullChecks를 활성화 하면, 변수가 null 또는 undefined가 할당될 수 없도록 한다.

```tsx
let value: string;

value = "abc123"; // OK.
value = null // Error.
```

useUnknownInCatchVariables를 활성화 하면, error의 타입을 unknown으로 애너테이션을 가능하게 한다.

```tsx
try {
	someFunction();
}
catch(error: unknown){
	error; // 타입: unknown
}
```

## 13.7 모듈

어떤 모듈 시스템으로 변환된 코드를 사용할지 결정하기 위해 module 컴파일러 옵션을 사용할 수 있다.

target 컴파일러 옵션이 “es3” 또는 “es5”인 경우 module 컴파일러 옵션의 기본값은 “commonjs”가 된다. 그렇지 않으면, “es2015”가 기본 설정이 된다.

```bash
$> tsc --module commonjs
```

```json
{
	"compilerOptions": {
		"module": "commonjs"
	}
}
```

모듈 해석(module resolution)은 import에서 가져온 경로가 module에 매핑되는 과정을 말한다.

타입스크립트는 해당 과정에 로직을 지정하는 데 사용할 수 있는 moduleResolution 옵션을 제공한다.

저자는 일반적으로 다음 두 가지 중에 하나를 제공하는 것을 선호한다.

- node: node.js와 같은 commonjs resolver에서 사용하는 동작
- nodenext: ECMA스크립트 모듈에 대해 지정된 동작에 맞게 조정

```bash
$> tsc --moduleResolution nodenext
```

```json
{
	"compilerOptions": {
		"moduleResolution": "nodenext"
	}
}
```

esModuleInterop 구성 옵션은 module이 ECMA스크립트 모듈 형식이 아닌 경우 타입스크립트에서 내보낸 자바스크립트 코드에 소량의 로직을 추가한다.

allowSyntheticDefaultImports 구성 옵션은 ECMA스크립트 모듈이 호환되지 않는 commonjs 네임스페이스 내보내기 파일에서 기본 가져오기를 할 수 있음을 타입 시스템에 알려준다.

## 13.8 자바스크립트

타입스크립트는 기본적으로 .js 또는 .jsx 확장자를 가진 파일을 무시하지만 allowJs와 checkJs 컴파일러 옵션을 사용하여 자바스크립트 파일을 읽고, 컴파일하고, 타입 검사도 할 수 있다.

```bash
$> tsc --allowJs
```

```json
{
	"compilerOptions": {
		"allowJs": true
	}
}
```

allowJs 컴파일러 옵션은 자바스크립트에 선언된 구문을 타입스크립트 파일에서 타입 검사를 하도록 허용한다.

```tsx
// index.ts
import { value } from "./values";

console.log(`Quotes: ${value.toUpperCase()}`);
```

```jsx
// values.js
export const value = "We cannot succed when half of us are held back.";
```

checkJs 컴파일러 옵션은 자바스크립트 파일을 타입스크립트와 관련 구문이 없는 파일인 것처럼 처리한다.

타입 불일치, 변수 이름 오타 등 타입스크립트에서 발생할 수 있는 일반적인 모든 오류를 발생시킬 수 있다.

```bash
$> tsc --checkJs
```

```json
{
	"compilerOptions": {
		"checkJs": true
	}
}
```

파일 상단에 // @ts-check 주석을 사용해 파일별로 checkJs를 활성화 할 수도 있다.

```jsx
// index.js
// @ts-check
let myQuote = "The talent for being happy is appreciating and liking what you have, 
								instead of what you don't have.";

console.log(quote); // Error. Did you mean 'myQuote' ?
```

## 13.9 구성 확장

tsconfig는 extends 구성 옵션을 사용해 다른 tsconfig에서 확장할 수 있다.

compilerOptions는 특정 옵션을 재정의 하지 않는 이상, 파생된 tsconfig로 그대로 복사된다.

- 절대 경로: @ 또는 알파벳 문자로 시작
- 상대 경로: .로 시작하는 로컬 파일 경로

```json
// tsconfig.base.json
{
	"compilerOptions": {
			"strict": true
	}
}
```

```json
// packages/core/tsconfig.json
{
	"extends": "../../tsconfig.base.json",
	"includes" : ["src"]
}
```

## 13.10 프로젝트 레퍼런스

대규모 프로젝트에서는 프로젝트 영역에서 서로 다른 구성 파일을 사용하는 것이 유용할 수 있다.

타입스크립트에서는 여러 개의 프로젝트를 함께 빌드하는 프로젝트 레퍼런스 시스템을 정의할 수 있다.

프로젝트 레퍼런스의 이점은 아래와 같다.

- 특정 코드 영역에 대해 다른 컴파일러 옵션을 지정할 수 있다.
- 개별 프로젝트에 대한 빌드 출력을 캐시 할 수 있다.
- 의존성 트리를 실행한다.(?)

composite 구성 옵션을 선택해 파일 시스템 입력과 출력이 제약 조건을 준수함을 나타낼 수 있다.

```json
// core/tsconfig.json
{
	"compilerOptions": {
		"delcaration": true
	},
	"composite": true
}
```

references 설정은 어떤 디렉토리를 입력으로 참조하게끔 설정할 수 있다. 

references 구성 옵션은 기본 tsconfig.json에서 extends를 통해 파생된 tsconfig로 복사되지 않는다.

```json
// shell/tsconfig.json
{
	"references": [
			{ "path" : "../core" }
	]
}
```

코드 영역이 프로젝트 레퍼런스를 사용하도록 한 번 설정되면 빌드 모드에서 tsc -b를 사용할 수 있다.

빌드 모드는 tsc를 프로젝트 빌드 코디네이터 같은 것으로 향상시킨다.

빌드 모드는 다음 내용을 수행한다.

- tsconfig의 참조된 프로젝트를 찾는다
- 최신 상태인지 감지한다
- 오래된 프로젝트를 올바른 순서로 빌드한다.
- 제공된 tsconfig 또는 tsconfig의 의존성이 변경된 경우 빌드한다.