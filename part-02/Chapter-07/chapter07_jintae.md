# Chapter 07

## 7.1 타입 별칭 vs 인터페이스

인터페이스 역시 객체 형태를 설명하는 또 다른 방법이다. 

인터페이스는 병합이 가능하고 기타등등의 타입 별칭보다 객체를 다루기에 유용한 기능들이 존재한다.

```tsx
type Poet = {
	born: number;
	name: string;
};

interface Poet {
	born: number;
	name: string;
}

// 두 구문은 거의 동일하다.
```

## 7.2 속성 타입

인터페이스에도 타입 애너테이션 : 앞에 ?를 넣어서 선택적 속성을 나타낼 수 있다.

인터페이스에서 readonly 키워드를 통해 읽기 전용 속성을 만들 수 있다. readonly 키워드는 타입스크립트에서만 존재한다.

타입스크립트는 명시적으로 타입 애너테이션을 작성하지 않은 객체에 대해서는 읽기 전용 속성과 같은 이름의 속성을 갖는 객체로 유추한다.

```tsx
interface Book {
    author?: string; // 선택적 속성
    title: string;
    readonly pages: number; // 읽기 전용 속성
}

const book1: Book = { // Book 객체 생성
	// author는 선택적 속성이므로 생략 가능
  title: "TypeScript",
  pages: 80,
};

const book2 = {
  title: "TypeScript",
  pages: 80,
};

book1.pages = 82; // Error
book2.pages = 82; // OK
```

인터페이스의 멤버를 함수로 선언하는 방법은 2가지 방법이 있다.

- **메서드 구문**: member(): void
- **속성 구문**: member: () ⇒ void

여기서 메서드 구문은 readonly를 적용할 수 없지만, 속성 구문은 readonly를 적용할 수 있다.

```tsx
interface HasBothFunctionTypes {
	property: () => string; // 속성
	method(): stirng; // 메서드
}

const hasBoth: HasBothFunctionTypes = {
	property: () => "",
	method() {
		return "";
	}
};

hasBoth.property(); // OK
hasBoth.method(); // OK
```

인터페이스와 객체 타입은 호출 시그니처로 선언할 수 있다.

호출 시그니처는 값을 함수처럼 호출하는 방식에 대한 타입 시스템의 설명을 의미한다.

```tsx
interface FunctionWithCount {
    count: number;
    (): void;
}

let hasCallCount: FunctionWithCount;

function keepsTrackOfCalls() {
    keepsTrackOfCalls.count += 1;
    console.log(`called ${keepsTrackOfCalls.count}`)
}

keepsTrackOfCalls.count = 0; // keepsTrackOfCalls 속성 생성

hasCallCount = keepsTrackOfCalls; // OK

function doesNotHaveCount() {
    console.log("No Idea!");
}

hasCallCount = doesNotHaveCount; // Error
```

임의의 key 에 특정 value를 저장하기 위해서, 가능한 key를 사전에 모두 작성하여 인터페이스를 선언하는 것은 불가능에 가깝다.

인덱스 시그니처를 이용하면, 인터페이스의 객체가 임의의 키를 받고, 해당 키 아래의 특정 타입을 반환할 수 있다.

```tsx
interface MoreNarrowNumbers {
	[i: number]: string;
	[i: string]: string | undefined;
}

const mixesNumbersAndStrings: MoreNarrowNumbers = {
	0: '',
	key1: '',
	key2: undefined,
}

interface MoreNarrowStrings {
	[i: number]: string | undefined; // Error... // Q.왜?
	[i: string]: string;
}
```

```tsx
interface MoreNarrowStrings {
    [i: number]: string | undefined;
    [i: string]: string | undefined;
}

interface NumberDictionary {
  [index: string]: number;
 
  length: number; // OK
  name: string; // Error:
	// Property 'name' of type 'string' is not assignable 
	// to 'string' index type 'number'.
}

/*
While string index signatures are a powerful way 
to describe the “dictionary” pattern,
they also enforce that all properties match their return type. 
This is because a string index declares that obj.property 
is also available as obj["property"]. 
In the following example, 
name’s type does not match the string index’s type, 
and the type checker gives an error:
*/

// 출처: https://www.typescriptlang.org/docs/handbook/2/objects.html
```

## 7.3 인터페이스 확장

인터페이스는 extends 키워드를 통해 기본 인터페이스의 모든 멤버를 복사해서 선언할 수 있다. 또한, 확장된 인터페이스에서 기본 인터페이스의 멤버를 재정의 할 수 있다.

타입스크립트의 타입 검사기는 재정의된 속성이 기본 속성에 할당되어 있도록 강요한다.

```tsx
interface nullable_name {
	name: string | null;
}

interface non_nullable_name extends nullable_name {
	name: string // OK
}

interface numeric_name extends non_nullable_name {
	name: number | string; // Error: 
	// 'number | string' type is not assignable to 'string | null' type.
}
```

## 7.4 인터페이스 병합

두 개의 인터페이스가 동일한 이름으로 동일한 스코프에 선언된 경우, 선언된 모든 필드를 포함하는 더 큰 인터페이스가 생성된다.

병합된 인터페이스는 동일한 이름의 속성을 여러 번 선언할 수 없다. 그러나 병합된 인터페이스는 동일한 이름과 다른 시그니처를 가진 메서드는 정의할 수 있다.

```tsx
interface Merged {
	fromFirst: string;
}

interface Merged {
	fromSecond: number;
}

// 다음과 같음:
// interface Merged {
// 	fromFirst: string;
// 	fromSecond: number;
// }
```

```tsx
interface MergedProperties {
	same: (input: boolean) => string; // 속성
	diff: (input: string) => string; // 속성
}

interface MergedProperties {
	same: (input: boolean) => string; // OK
	diff: (input: number) => string; // Error
}
```

```tsx
interface MergedMethods {
	diff(input: string): string; // 메서드
}

interface MergedMethods {
	diff(input: number): string; // OK
}
```