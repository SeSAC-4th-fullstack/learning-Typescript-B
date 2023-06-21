# Chapter 06

## 6.1 배열 타입

타입스크립트에서는 요소의 타입을 지정하고, 해당 타입의 요소들만 배열에 모아 놓을 수 있다. 배열 타입 구문은 요소의 타입과 그 끝에 []을 두어 배열 타입을 선언할 수 있다.

빈 배열에 타입을 지정하지 않으면 any[]로 취급하여 모든 요소를 받을 수 있다. 진화하는 any처럼 지양하는 것이 바람직하다.

```tsx
// 타입: number array
let numberArr: number[];
numberArr = [1, 2, 3];

// 타입: string array를 반환하는 함수 타입
let createStrings: () => string[];

// 타입: string array를 반환하는 함수 타입의 array
let stringCreators: (() => string[])[];

// 타입: any[]
let val = [];
val.push('a'); // ["a"], 타입: string[]
val[1] = 0; // ["a", 0], 타입: (string | number)[]

// 타입: 2차원 number array
let arr2D: number[][]; // 또는 let arr2D: (number[])[]
arr2D = [ [1,2], [2,4], [3,6] ];
```

## 6.2 배열 멤버

타입스크립트는 배열 멤버의 타입은 배열의 타입으로 간주해버린다.

```tsx
const forwards = ["Son", "Kane"]; // 타입: string[]
const a_forward = forwards[0]; // 타입: string

const soldiersOrDates = ["Deborah", new Date(1782, 6, 3)]; // 타입: (string | Date)[]
const soldierOrDate = soldiersOrDates[0]; // 타입: string | Date
```

배열은 타입스크립트의 타입 시스템에서 매우 취약한 부분이므로 주의해야 한다. 

elem[9001]이 존재하는지에 대한 확인을 검증하지 않고 elem: string[]을 기반으로 elem[9001]을 string타입으로 간주하기 때문에 타입 오류가 발생하지 않는다.

```tsx
function withElem(elem: string[]) {
    console.log(elem[9001].length); // 타입 오류 없음
}

withElem(["It's", "Over"]);
```

## 6.3 스프레드와 나머지 매개변수

스프레드와 나머지 매개변수는, 둘 다 …연산자를 통해 동작한다. 해당 연산자를 이용하면 배열과 상호작용 할 수 있다.

스프레드는 …연산자를 통해 배열을 결합한다.

```tsx
const soldiers = ["김하사", "박중사", "이대위"];
const soldierAges = [22, 27, 33];

// 타입: (string | number)[]
const conjoined = [...soldiers, ...soldierAges];
```

나머지 매개변수를 사용하여 배열의 요소들을 매개변수로 입력받을 수 있다. 이 때, 인자로 입력되는 배열은 나머지 매개변수의 타입 애너테이션과 동일한 타입을 가져야 한다.

```tsx
function logWarriors(greeting: string, ...names: string[]) {
	for(const name of names) {
		console.log(`${greeting}, ${name}`!);
}

const warriors = ["이순신", "김유신", "이사부"];
logWarriors("안녕하세요", ...warriors); // OK

const birthYears = [1537, 1447, 1237];
logWarriors("출생연도", ...birthYears); // Error
// ...birthYears 타입은 number 이므로 string 타입에 할당 불가능
```

## 6.4 튜플

튜플이란 고정된 크기의 배열을 말한다.

튜플 타입 구문은 각 인덱스에 특정 타입을 작성한다.

```tsx
let yearAndName: [number, string];
yearAndName = [1987, "Tom"]; // OK

yearAndName = ["Tom", 1997]; // Error
yearAndName = ["Tom"]; // Error
yearAndName = [1997]; // Error
```

타입스크립트에서 튜플 타입은 가변 길이의 배열 타입을 할당할 수 없다. 왜냐하면, 타입스크립트는 일반적인 타입으로 우선 유추하여 튜플의 갯수보다 모자른 경우를 사전에 방지하기 때문이다.

```tsx
const pairLoose = [false, 123]; // 타입: (boolean | number)[]
const pairTupleLoose: [boolean, number] = pairLoose // Error
// Type '(boolean | number)[]' is not assignable to type [boolean, number].
// Target requires 2 elements but source may have fewer.
```

튜플 타입 또한 나머지 매개변수로 인수를 전달할 수 있다.

```tsx
function logPair(name: string, value: number) {
	console.log(`${name} has ${value}`);
}

const pairArray = ["Amigo", 1];
const pairTupleIncorrect = [1, "Amigo"];
const pairTupleCorrect: [string, number] = ["Amigo", 1];

logPair(...pairArray); // Error
logPair(...pairTupleIncorrect) // Error
logPair(...pairTupleCorrect) // OK
```

```tsx
function logTrio(name: string, value: [number, boolean]) {
	console.log(`${name} has ${value[0]}, ${value[1]}`);
}

const trios: [string, [number, boolean][] = [
	["A", [1, true]],
	["B", [2, false]],
	["C", [3, false]],
];

trios.forEach(trio => logTrio(...trio)); // OK, trio는 하나의 튜플

trios.forEach(logTrio); // Error..
// logTrio가 호출하는 매개변수 타입(string, [number, boolean])이 trios의 첫번째 요소 타입과
// 맞지 않기 때문에 오류 발생

// forEach()는 주어진 callback을 배열에 있는 각 요소에 대해 오름차순으로 한 번씩 실행합니다
// 삭제했거나 초기화하지 않은 인덱스 속성에 대해서는 실행하지 않습니다
// 출처: mdn
```

타입스크립트는 배열이 변수의 초기값 또는 함수에 대한 반환값으로 사용되는 경우, 튜플로 취급하지 않는다. 
오히려 길이가 가변적인 배열로 취급한다.

따라서, 튜플 타입의 추론은 2 가지 방법을 통해 나타낼 수 있다.

1. **명시적 튜플 타입**: 타입 애너테이션을 활용하여 튜플 타입을 명시하는 방법
2. **const 어서션**: as const 연산자를 통해 읽기 전용 데이터로 변환시키는 방법

```tsx
// 명시적 튜플 타입 예시
function firstCharAndSize(input: string): [string, number] {
	return [input[0], input.length];
}

// firstChar 타입: string
// size 타입: number
const [firstChar, size] = firstCharAndSize("Willy Wonka");
```

```tsx
// const 어서션 예시
const pairMutable: [number, string] = [1157, "iPhone"];
pairMutable[0] = 2010; //OK

const pairAlsoMutable: [number, string] = [2023, "visonPro"] as const; // Error
```

```tsx
// const 어서션 함수 반환값 전달 예시
function pairReturn(num: number, name: string) {
	return [num, name] as const;
}
const pair_ret = pairReturn(2023, "visionPro"); // OK
console.log(pair_ret); // [2023, "visionPro")
```