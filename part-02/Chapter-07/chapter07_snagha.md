# Chapter 07

> 인터페이스
> 

# 1. 타입 별칭 vs 인터페이스

---

> **type과 interface**
> 

```tsx
type Poet = {
    born: number;
    name: string;
}

interface Poet {
    born: number;
    name: string;
}
```

- 두 구문은 동일한 구문이다.

```tsx
interface Poet {
    born: number;
    name: string;
}

let valuelater: Poet;

// Ok
valuelater = {
    born: 1935,
    name: "Sara Teasdale",
}                   

// Error
valuelater = {
    born: true,
    name: "Sara Teasdale",
}                   

// Error
valuelater = "Kim";
```

- **인터페이스에 대한 타입스크립트의 할당 가능성 검사와 오류 메시지는 객체 타입에서 실행되는 것과 동일합니다.**

> **인터페이스와 타입 별칭 사이 차이점**
> 
1. 인터페이스는 속성 증가를 위해 병합할 수 있다. 이 기능은 내장된 전역 인터페이스 또는 npm 패키지와 같은 외부코드를 사용할 때 특히 유용합니다.
2. 인터페이스는 클래스가 선언된 구조의 타입을 확인하는 데 사용할 수 있지만, 타입 별칭은 사용할 수 없다. 
3. 일반적으로 인터페이스에서 타입스크립트 타입 검사기가 더 빨리 작동한다. 내부적으로 더 쉽게 캐시할 수 있는 명명된 타입을 선언한다.
4. 인터페이스는 이름 없는 객체 리터럴의 별칭이 아닌 이름 있는 객체로 간주되므로 어려운 특이 케이스에서 나타나는 오류 메시지를 좀 더 쉽게 읽을 수 있다.

*⇒ 가능하다면 인터페이스 사용을 추천한다.*

# 2. 속성 타입

---

### 1) 선택적 속성

```tsx
interface Book {
    author?: string;
    pages: number;
}

// Ok
const b1: Book = {
    author: "Kim",
    pages: 100,
}

const b2: Book = {
    pages: 200
}
```

- 객체 타입과 마찬가지로 ? 를 통해 선택적 속성을 지정 할 수 있다.

### 2) 읽기 전용 속성

```tsx
interface Page {
    readonly text: string;
}

function read(page: Page) {
    console.log(`${page.text}`);

    page.text += "New Text"; // Error
}

const pageIsh = {
    text: "Hello World",
};
pageIsh.text += "!";    // Ok

read(pageIsh);          // Ok
```

- readonly를 통해 해당 속성을 읽기 전용을 전환한다.
- **readonly 제한자는 인터페이스에서만 사용이 가능하다.**

⛔ P135p

: Page 예제에서 text 속성의 부모 객체는 함수 내부에서 text로 명시적으로 사용되지 않았기 때문에 함수 밖에서 속성을 수정할 수 있습니다. 쓰기 가능한 속성을 readonly 속성에 할당할 수 있으므로 pagIsh는 Page로 사용할 수 있습니다. 

### 3) 함수와 메서드

```tsx
interface HasBothFunctionTypes {
    property: () => string;     // 속성 구문
    method(): string;           // 메서드 구문
}

const hasBoth: HasBothFunctionTypes = {
    property: () => "",
    method() {
        return "Lee"
    }
};

hasBoth.property();     // Ok
hasBoth.method():       // Ok
```

- **메서드 구문** : memeber( ): string;
- **속성 구문** : member: ( ) ⇒ string;

```tsx
interface OptionalReadOnlyFunctions {
    optionalProperty?: () => string;
    optionalMethod(): string;
}
```

- 선택적 메서드도 사용할 수 있다.

> **메서드와 속성의 차이점**
> 
1. 메서드는 readonly로 선언할 수 없지만, 속성은 가능하다.
2. 인터페이스 병합은 메서드와 속성을 다르게 처리한다.
3. 타입에서 수행되는 일부 작업은 메서드와 속성을 다르게 처리한다.

### 4) 호출 시그니처

> **호출시그니처**
> 

*: 값을 함수처럼 호출하는 방식에 대한 타입 시스템*

```tsx
type FunctionAlias = (input: string) => number;

interface CallSignature {
    (input: string): number;
}

// 타입: (input: string) => number
const typedFunctionAlias: FunctionAlias = (input) => input.length;

// 타입: (input: string) => number
const  typedCallSignature: CallSignature = (input) => input.length;
```

```tsx
interface FunctionWithCount {
    count: number;
    (): void;
}

let hasCallCount: FunctionWithCount;

// Case 1
function keepsTrackOfCalls() {
    keepsTrackOfCalls.count += 1;
    console.log(`I've been called ${keepsTrackOfCalls.count} times!`);
}

keepsTrackOfCalls.count = 0;        // count 속성이 주어짐

hasCallCount = keepsTrackOfCalls;   // Ok

// Case 2
function doesNothaveCount() {
    console.log("No idea");
}

hasCallCount = doesNothaveCount;    // Error
```

- keepsTrackOfCalls에 count 속성이 주어져 FunctionWithCont 인터페이스에 할당 할 수 있다.

### 5) 인덱스 시그니처

> **인덱스 시그니처**
> 

*: 인터페이스의 객체가 임의의 키를 받고, 해당 키 아래 특정 타입을 반환할 수 있음을 나타낸다.*

```tsx
interface WordCounts {
    [i: string]: number;
}

const counts: WordCounts = {};

counts.apple = 0;       // Ok
counts.banana = 1;      // Ok

counts.cherry = false   // Error
```

- 타입이 string인 키를 받고 number을 반환한다.

*인덱스 시그니처는 객체에 값을 할당할 때는 편리하지만,* 

*타입 안정성을 완벽하게 보장하지는 못한다.* 

```tsx
interface DatesByName {
    [i: string]: Date;
}

const publishDates: DatesByName = {
    Frankenstein: new Date("1 January 1818");
};

publishDates.Frankenstein;  // 타입: Date
console.log(publishDates.Frankenstein.toString());

publishDates.Beloved;       // 타입은 Date이지만 런타임 값은 undefined
console.log(publishDates.Beloved.toString());
```

- Beloved가 Date 타입이라고 추론 되지만, 실제로는 undefined이기 때문에 런타임 오류가 발생한다.

> **속성과 인덱스 시그니처 혼합**
> 

```tsx
interface HistoricalNovels {
    Oroonoko: number;
    [i: string]: number;
}

// Ok
const novels: HistoricalNovels = {
    Outlander: 1991,
    Oroonoko: 1688,
}

// Error
const missingOroonoko: HistoricalNovels = {
    Outlander: 1991,
}
```

- 필수 속성인 Oroonoko가 존재하지 않아서 Error가 발생한다.
- 인덱스 시그니처의 원시 속성(string, number, ..) 보다 
명명된 속성에 대해 더 구체적인 속성 타입 리터럴(”Hello” , “Cat”, …) 을 사용하는 것이다.

```tsx
interface ChapterStarts {
    preface: 0;
    [i: string]: number;
}

const correctPreface: ChapterStarts = {
    preface: 0,
    night: 1,
    shopping: 5,
}

const wrongPreface: ChapterStarts = {
    preface: 1,     // Error
}
```

- preface의 타입은 리터럴 타입으로 0으로 정의 되어있다. 1을 넣을 수는 없다.

> **숫자 인덱스 시그니처**
> 

```tsx
// Ok
interface MoreNarrowNumbers {
    [i: number]: string;
    [i: string]: string | undefined;
}

// Ok
const mixesNumberAndStrings: MoreNarrowNumbers = {
    0: "Kim",
    key1: "Lee",
    key2: "Park",
}

interface MoreNarrowStrings {
    [i: number]: string | undefined;
    [i: string]: string;
}
```

- 타입스크립트 인덱스 시그니처는 키로 string 대신 number 타입을 사용할 수 있지만,
- **명명된 속성은 그 타입을 포괄적인 용도의 string 인덱스 시그니처의 타입으로 할당할 수 있어야 한다.**

### 6. 중첩 인터페이스

```tsx
interface Novel {
    author: {
        name: string;
    };
    setting: Setting;
}

interface Setting {
    place: string;
    year: number;
}

let myNovel: Novel;

myNovel = {
    author: {
        name: "Kim",
    },
    setting: {
        place: "Seoul",
        year: 2023,
    },
}
```

- 인터페이스 안에 인터페이스를 중첩되게 사용할 수 있다.

# 3. 인터페이스 확장

---

> **확장된 인터페이스**
> 

: 파생된 인터페이스는 참조하는 인터페이스의 모든 속성을 포함하고 있어야 한다.

```tsx
interface Writing {
    title: string;
}

interface Novella extends Writing {
    pages: number;
}

// Ok
let myNovella: Novella = {
    pages: 195,
    title: "Ethan Frome",
}

// Error
let missingPages: Novella = {
    title: "The Awaking"    // pages 속성이 빠졌다.
}

// Error
let extrProperty: Novella = {
    pages: 300,
    title: "The King",
    style: "Black",     // Novella에 없은 속성이다.
}
```

### 1) 재정의된 속성

```tsx
interface WithNullableName {
    name: string | null;
}

// Ok
interface WithNonNullableName extends WithNullableName {
    name: string;
    // string | null 에서 string 으로 타입이 좁혀졌다.
}

// Error
interface WithNumericName extends WithNullableName {
    name: number | string;
    // string을 number | string에 할당 할 수 없다.
}
```

- 재정의 할 때, 할당이 가능한 범위의 타입만 가능하다.

### 2) 다중 인터페이스 확장

```tsx
interface WithNullableName {
    name: string | null;
}

// Ok
interface WithNonNullableName extends WithNullableName {
    name: string;
    // string | null 에서 string 으로 타입이 좁혀졌다.
}

// Error
interface WithNumericName extends WithNullableName {
    name: number | string;
    // string을 number | string에 할당 할 수 없다.
}
```

- 여러 개의 인터페이스를 , 를 통해 extends 할 수 있다.

# 4. 인터페이스 병합

---

> **인터페이스 병합**
> 

: 동일한 인터페이스가 동일한 스코프에서 선언된 경우, 두 인터페이스는 병합된다.

```tsx
interface Merged {
    fromFirst: string;
}

interface Merged {
    fromSecond: number;
}

// 다음과 같다
// interface Merged {
//     fromFirst: string;
//     fromSecond: number;
// }
```

*⇒ 인터페이스 병합을 자주 사용하면 코드 해석이 어려워지므로, 자주 사용하지 않는 것이 좋다.*

> **내장된 인터페이스 강화 용도**
> 

```tsx
interface Window {
    myEnvironmentVariable: string;
}

window.myEnvironmentVariable; // 타입: string
```

- 이미 정의된 내장된 인터페이스는 확장하여 사용하는 데 편리하다.

### 1) 이름이 충돌되는 멤버

```tsx
interface MergedProperties {
    same: (input: boolean) => string;
    different: (input: string) => string;
}

interface MergedProperties {
    same: (input: boolean) => string;       // Ok
    different: (input: number) => string;   // Error
}

// same 속성의 타입이 동일하므로 문제 없지만,
// different 속성의 타입이 달라 오류를 발생시킨다.
```

```tsx
interface MergedMethods {
    different(input: string): string;
}

interface MergedMethods {
    different(input: number): string;
}
```

- 중첩되는 부분이 메서드인 경우 “오버로드”로 처리 된다.