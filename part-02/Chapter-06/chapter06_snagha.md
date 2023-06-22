# Chapter 06

> 배열
> 

# 1. 배열 타입

---

> **배열**
> 

**: 타입스크립는 초기 배열에 어떤 데이터 타입이 있는지 기억하고, 배열이 해당 타입에서만 작동하도록 제한한다.**

```tsx
let arrayOfString: string[] = ["A", "B", "C"];

arrayOfString.push("D");    // Ok

arrayOfString.push(true)    // Error
```

- **Error**
: arrayOfString는 string[ ] 타입으로 boolean 타입이 들어올 수 없다.

```tsx
let arrayOfNumbers: number[];

arrayOfNumbers = [1, 2, 3, 4, 5];
```

- 배열의 선언은 “타입[ ]”으로 한다.

### 1) 배열과 함수 타입

```tsx
// 타입은 string 배열을 반환하는 함수
let creatStrings: () => string[];

// 타입은 각각의 string을 반환하는 함수 배열
let stringCreators: (() => string)[];
```

- creatStrings는 **반환 타입이 string[ ] 인 함수 타입니다.**
- stringCreators은 **( ) => string 형태의 함수 타입의 배열이다.**

### 2) 유니언 타입 배열

```tsx
// string 또는 number의 배열
let stringArrayOfNumber: string | number[];

stringArrayOfNumber = "Hello";
stringArrayOfNumber = [1, 2, 3, 4];

// 각각 number 또는 string인 요소의 배열
let arrayOfStringOrNumber: (string | number)[];

arrayOfStringOrNumber = ["A", "B", "C"];
arrayOfStringOrNumber = [1, 2, 3, 4];
arrayOfStringOrNumber = ["A", 1, "B", 2, "C", 3];
```

```tsx
const nameMaybe = [
    "JPark",
    "Kim",
    undefined,
]

// nameMaybe: (string | nudefined)[]
```

### 3) any 배열의 진화

**: 초기에 빈 배열로 설정된 변수에서 타입 애너테이션을 포함하지 않으면 타입스크립트는 배열을 any[ ]로 취급한다.**

```tsx
// 배열 안의 값 타입이 any로 정의 된다.
let values = [];

values.push('A');  // string 타입도 가능

values[0] = 0;     // number 타입도 가능 
```

*⇒ any의 사용은 타입스크립트의 사용성을 무력화한다.*

### 4) 다차원 배열

```tsx
let arrayOfArrayOfNumber: number[][];

arrayOfArrayOfNumber = [
    [1, 2, 3],
    [1, 2, 3],
    [1, 2, 3],
]

// 다음과 같이 타입이 추론 된다.
let arrayOfArrayOfNumbers: (number[])[];

// 같다
number[][] === (number[])[]
```

# 2. 배열 멤버

---

> **배열 멤버**
> 

: 타입스크립트는 배열의 멤버를 찾아서 해당 배열의 타입 요소를 되돌려주는 전형적인 
**인덱스 기반 접근 방식**을 이해하는 언어

```tsx
// Case 1
const defenders = ["Clarenze", "Dina"];

const defender = defenders[0];  // string 타입

// Case 2
const soldierOrDates = ["Samsung", new Date(2023, 10, 10)];

const soldierOrDate = soldierOrDates[0];    // string | Date 타입
```

### 1) 주의 사항 : 불안정한 멤버

**: 타입스크립트는 모든 배열의 멤버에 대한 접근이 해당 배열의 멤버를 반환한다고 가정**

```tsx
function withElements(elements: string[]) {
    console.log(elements[9001].length); // 타입 오류 없음
}

withElements(["A", "B", "C"]);
```

- 자바스크립트에서는 배열의 길이보다 큰 인덱스로 배열 요소에 접근하면 undefined를 반환한다.
- 타입스크립트에서는 이러한 타입의 불안정성을 고려하지 않고, 타입 오류로 판단하지 않는다
- 런타임 시 에러를 발생시킨다.

# 3. 스프레드와 나머지 매개변수

---

### 1) 스프레드 (…)

```tsx
const solidiers = ["Kim", "Park", "Lee"];           // 타입 : string[]
        
const solidierAge = [10, 20, 30];                   // 타입 : number[]

const conjoined = [...solidiers, ... solidierAge];  // 타입 : (string | number)[]
```

- 두 배열의 타입을 합친다.

### 2) 나머지 매개변수 스프레드

```tsx
function logWarriors(greeting: string, ...names: string[]) {
    for(const name of names) {
        console.log(`${greeting}, ${name}`);
    }
}

const warriors = ['Kim', "Park", "Lee"];

logWarriors("Hello", ...warriors);              // Ok
logWarriors("Hello", "Kim", "Park", "Lee");     // Ok

const birthYears = [2020, 2010, 2000] ;

logWarriors("Born in", ...birthYears);          // Error
```

- **… 배열** : 배열을 스프레드 방식으로 바꾼다.
- **Error**
: logWarriors의 …name 매개변수에는 sting[ ] 타입이 스프레드 되어 들어와야 한다.

# 4. 튜플

---

> **튜플**
> 

: 고정된 크기의 배열을 가진다.

**: 튜플 타입을 선언하는 구문은 배열 리터럴처럼 보이지만 요소의 값 대신 타입을 적는다.
: ex) [ 타입, 타입, 타입 ]**

```tsx
let yearAndWarrior: [number, string];

yearAndWarrior = [500, "Kim"];  // Ok

yearAndWarrior = [false, "Lee"] // Error

yearAndWarrior = [530]          // Error
```

- **Case 2 - Error**
: [false, "Lee"]는 [boolean, string] 타입으로 [number, string] 타입에 할당할 수 없다.
- **Case 3 - Error**
: [530]은 [number, string] 타입에 할당할 수 없다.

```tsx
let [year, warrior] = Math.random() > 0.5
                    ? [2010, "Kim"]
                    : [2020, "Lee"];
```

### 1) 튜플 할당 가능성

```tsx
// 타입 : (boolean | number)[]
const pairLoose = [false, 12];

const pairTupleLoose: [boolean, number] = pairLoose;    // Error
```

- **Error**
: pairLoose 는 가변적인 (boolean | number)[ ] 타입으로 유추 된다.
: 따라서 [boolean, number]의 튜플 타입인 pairTupleLoose에 할당할 수 없다.

```tsx
const tupleThree: [boolean, number, string] = [false, 2020, "Kim"];

const tupleTwoExact: [boolean, number] = [tupleThree[0], tupleThree[1]];    // OK

const tupleTwoExtra: [boolean, number] = tupleThree;                        // Error
```

- **Error**
: [ boolean, number, string ] 타입을 [ boolean, number ]  타입에 할당할 수 없다.

> **나머지 매개변수로서의 튜플**
> 

**: 튜플은 구체적인 길이와 요소 타입 정보를 가지는 배열로 간주되므로 함수에 전달할 인수를 저장하는데 유용하다.**

: 타입스크립트는 … 나머지 매개변수로 전달된 튜플에 정확한 타입 검사를 제공할 수 있다.

```tsx
function logPair(name: string, value: number) {
    console.log(`${name} has ${value}`);
}

const pairArray = ["Amage", 1];
logPair(...pairArray);              // Error

const pairTupleIncorrect: [number, string] = [1, "Amage"];
logPair(...pairTupleIncorrect);     // Error

const pairTupleCorrect: [string, number] = ["Amage", 1];
logPair(...pairTupleCorrect);       // Ok
```

- **Case 1 - Error**
: pairArray는 튜플 타입이 아니기 때문에 함수의 매개변수에 값을 전달할 수 없다.
- **Case 2 - Error**
: pairTupleIncorrect는 튜플타입은 맞으나, 지정된 인덱스의 타입이 다르다.
- **매개변수를 튜플 타입으로 값을 전달하기 위해서는…**
    1. 타입이 튜플이어야 하고,
    2. 길이와 위치에 맞는 타입이 일치해야 한다.

```tsx
function logTrio(name: string, value: [number, boolean]){
    console.log(`${name} has ${value[0]} (${value[1]})`);
}

const trios: [string, [number, boolean]][] = [
    ["Kim",  [1, true]],
    ["Lee",  [2, false]],
    ["Park", [3, true]],
]

trios.forEach(trio => logTrio(...trio));    // Ok

trios.forEach(logTrio);                     // Error

/* 참고 */
// forEach(
// 	callbackfn: (value: T, index: number, array: readonly T[]) => void, thisArg?: any
// ): void;
```

- **Case 1 - Ok**
: trio는 ["Kim", [1, true]]의 형식으로 logTrio에 전달된다.
- **Case 2 - Error**
: name: string에 [string, [number, boolean]][ ] 전체가 전달 되려고 한다.

### 2) 튜플 추론

**: 타입스크립트는 생성된 배열을 튜플이 아닌 가변 길이의 배열로 취급한다.**

```tsx
// 반환 타입 :(string | number)[]
function firstCharAndSize(input: string) {
    return [input[0], input.length];
}

// firstChar : string | number 타입
// size      : string | number 타입
const [firstChar, size] = firstCharAndSize("Kim");
```

*⇒ 따라서, 일반적인 배열 타입이 아닌 튜플 타입이어야 함을 두가지 방법으로 나타낸다.*

> **방법1) 명시적 튜플 타입**
> 

```tsx
// 반환 타입 : [string | number]
function firstCharAndSizeExplicit(input: string): [string, number] {
    return [input[0], input.length];
}

// firstChar : string 타입
// size      : number 타입
const [firstChar, size] = firstCharAndSizeExplicit("Kim");
```

- 추론이 아닌, 명시적으로 결과 타입을 정의해준다.
- 함수의 리턴 타입을 :[string, number]로 정의해주어 결과 타입을 정의했다.

> **방법2) const 어서션**
> 

```tsx
// 타입: (string | number)[]
const unionArray = [2020, "Kim"];

// 타입: readonly [2020, "Kim"]
const readonlyTuple = [2020, "Kim"] as const;
```

- **as const :** 배열을 고정된 크기의 튜플로 전환 + 읽기 전용(readonly)으로 바꿈

```tsx
const pairMutable: [number, string] = [2020, "Kim"];
pairMutable[0] = 1010;  // Ok

// as const 때문에 할당 불가
const pairAlsoMutable: [number, string] = [2010, "Lee"] as const; // Error

// as const 때문에 수정 불가
const pairCount = [2000, "Park"] as const;
pairCount[0] = 1010;    // Error
```

- **Case 1 - Ok**
: pairMutable는 튜플 타입이지만, 수정은 가능하다.
- **Case 2 - Error**
: as const가 처리된 [2010, “Lee”]는 readonly가 추가되고, 수정이 가능한 [number, string] 타입에 할당할 수 없다
- **Case 3 - Error**
: as const로 인해 readonly로 바뀌었다.

```tsx
// 반환 타입 : readonly [string, number]
function firstCharAndSizeAsCount(input: string) {
    return [input[0], input.length] as const;
}

// firstChar  : string 타입
// size       : number 타입
const [firstChar, size] = firstCharAndSizeAsCount("Kim");   // Ok
```

- 읽기 전용 튜플은 함수 반환에 편리하다.
- 함수로부터 반환된 값은 즉시 구조화 되지 않으므로 읽기 전용인 튜플은 함수를 사용하는데 방해가 되지 않는다.