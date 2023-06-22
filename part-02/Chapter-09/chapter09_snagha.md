# Chapter 09

> 타입 제한다
> 

# 1. top 타입

---

### 1) any 다시보기

```tsx
let anyValue: any;

anyValue = "Kim";   // Ok
anyValue = 1000;    // Ok

console.log(anyValue);
```

- any 타입은 모든 타입을 받을 수 있어 top 타입처럼 작동할 수 있다.
- 단, any 타입은 할당 가능성 또는 타입 검사를 수행하지 않도록 명시적으로 지시한다.

```tsx
function greetComdeian(name: any) {
    // Error
    console.log(`Announcing ${name.toUpperCase()}`);
}

greetComdeian({name: "Kim"});
```

- name이 any 타입으로 정의 되었기 때문에 오류는 발생하지 않는다. 
그러나 실행 시 오류가 발생한다.

### 2) unknown

> **unknown**
> 

*: 타입스크립트는 unknown 타입의 값을 훨씬 더 제한적으로 취급한다.*

*: 타입스크립트는 unknown 타입의 속성에 직접 접근할 수 없다.*

*: unknown 타입은 top 타입이 아닌 타입에는 할당할 수 없다.*

```tsx
function greetComdeian(name: unknown) {
    // Error
    console.log(`Announcing ${name.toUpperCase()}`);
}

greetComdeian({name: "Kim"});
```

- unknown 타입인 name은 타입 속성에 접근할 수 없다.

```tsx
function greetComedianSafety(name: unknown) {
    if(typeof name === "string") {
        console.log(`Announcing ${name.toUpperCase()}`);    // OK
    } else {
        console.log("Well...");
    }
}

greetComedianSafety("Kim");
greetComedianSafety({})
```

- unknown의 타입을 좁히기 위해서는 instanceof / typeof / 타입 어서션을 사용한다.

# 2. 타입 서술어

---

```tsx
function isNumberOrString(value: unknown) {
    return ['number', 'string'].includes(typeof value);
}

function logValueIfExists(value: number | string | null | undefined) {
    if(isNumberOrString(value)) {
        value.toString()    // Error
    } else {
        console.log("Value does not exist : ", value);
    }
}
```

- isNumberOrString의 반환 값은 true / false로만 주어질 뿐, value의 타입을 파악하지는 못한다.

> 타입 서술어
> 

*: 타입스크립트에는 인수가 특정 타입인지 여부를 나타내기 위해 boolean 값을 반환하는 함수*

*: ‘사용자 정의 타입 가드’ 라고도 불린다.* 

*: instanceof,  typeof*

```tsx
function isNumberOrString(value: unknown): value is number | string {
    return ['number', 'string'].includes(typeof value);
}

function logValueIfExists(value: number | string | null | undefined) {
    if(isNumberOrString(value)) {
        // value : number | string
        value.toString();
    } else {
        // value : null | undefined
        console.log("Value does not exist : ", value);
    }
}
```

- 타입 서술어는 단순히 boolean 값을 반환하는 것이 아니라 구체적인 타입임을 나타낸다.

```tsx
interface Comedian {
    funny: boolean;
}

interface StandupComedian extends Comedian {
    routine: string;
}

// value가 StandupComedian 타입임을 확인하는 타입가드가 된다. 
function isStandupComedian(value: Comedian): value is StandupComedian {
    return 'routine' in value;
}

function workWithComedian(value: Comedian) {
    if(isStandupComedian(value)) {
        // Ok
        console.log(value.routine); 
    }

    // Error : 단순히 Comedian 타입일 뿐 , StandupComedian 타입임은 증명 불가
    console.log(value.routine);
}
```

```tsx
function isLongString(input: string | undefined): input is string {
    return !!(input && input.length >= 7);
    // 길이가 7이상이면 true
    // 길이가 7미만이면 false
}

function workWithText(text: string | undefined) {
    if(isLongString(text)) {
        // OK : text는 string 타입이라고 추론된다.
        console.log(`Long text : `, text.length);
    } else {
        // Error: text는 undefined 타입
        // never 형식에 length 속성이 없다.
        console.log("Short text : ", text?.length);
    }
}
```

- never 형식에 length 속성이 없다. ⛔

# 3. 타입 연산자

---

### 1) keyof

```tsx
interface Ratings {
    audience: number;
    critics: number;
}

function getRating(ratings: Ratings, key: string): number {
    return ratings[key];    // Error
}

const ratings: Ratings = { audience: 66, critics: 84 };

getRating(ratings, "audience"); // Ok

getRating(ratings, "not valid") // 허용되지만 사용하면 안 됨
```

- Ratings 타입인 ratings의 key로 string 타입이 들어오는 것은 너무 포괄적이다.

```tsx
interface Ratings {
    audience: number;
    critics: number;
}

function getRating(ratings: Ratings, key: "audience" | "critics"): number {
    return ratings[key] // Ok
}

const ratings: Ratings = { audience: 66, critics: 84 };

getRating(ratings, "audience"); // Ok

getRating(ratings, "not valid") // Error
```

- key의 타입을 Ratings의 멤버의 집합은 리터럴 타임으로 잡는다.
- 단, Rating의 멤버가 많으면 지정하기 힘들다.

```tsx
interface Ratings {
    audience: number;
    critics: number;
}

function getCountKeyof(ratings: Ratings, key: keyof Ratings): number {
    return ratings[key];    // Ok
}

const ratings: Ratings = { audience: 66, critics: 84 };

getCountKeyof(ratings, "audience"); // Ok

getCountKeyof(ratings, "not valid") // Error
```

- keyof를 통해 Ratings의 모든 멤버에 접근할 수 있다.

### 2) typeof

```tsx
const original = {
    medium: "movie",
    title: "Mean Girls",
};

let adaptation: typeof original;

if(Math.random() > 0.5) {
    adaptation = {...original, medium: "play"}  // Ok
} else {
    adaptation = {...original, medium: 2};      // Error
}
```

- adaptation의 타입은 original이다. 따라서 medium에 number는 할당할 수 없다.

> **typeof**
> 

*: 자바스크립트의 typeof 연산자는 타입에 대한 문자열 이름을 반환하는 런타임 연산이다.* 

*: 반면, 타입스크립트의 typeof 연산자는 타입스크립트에서만 사용할 수 있다.* 

> **keyof typeof**
> 

*: 타입이 없는 객체의 키를 편리하게 사용할 수 있다.*

```tsx
const ratings = {
    imdb: 8.4,
    metacritic: 82,
}

function logRating(key: keyof typeof ratings) {
    console.log(ratings[key]);
}

logRating("imdb");      // Ok

logRating("invalid")    // Error
```

- keyof typeof를 통해 key는 rating의 멤버 중 하나라고 명시된다.

# 4. 타입 어서션

---

> **타입 어서션**
> 

*: 다른 타입을 의미한다고 알린다.  ex) as string*

*: 타입 시스템은 어서션을 따르고 값을 해당 타입으로 처리한다.*

```tsx
const rawData = '["Kim", "Lee"]';

// JSON.parse는 의도적으로 any를 반환한다.

// 타입 : any
const a1 = JSON.parse(rawData);
// 타입 : string[]
const a2 = JSON.parse(rawData) as string[];
// 타입 : [string, string]
const a3 = JSON.parse(rawData) as [string, string];
// 타입 : ["Kim", "Lee"]
const a4= JSON.parse(rawData) as ["Kim", "Lee"];
```

### 1) 포착된 오류 타입 어서션

```tsx
try {
    
} catch (error) {
    console.warn("Oh no!", (error as Error).message)
}
```

- error 가 Error 타입임을 명시해준다.

```tsx
try {
    // 오류를 발생시키는 코드
} catch (error) {
    console.warn("Oh no!", error instanceof Error ? error.message : error)
}
```

- instanceof로 Error임을 명시할 수 있다.

### 2) non-null 어서션

*: null 또는 undefined를 포함할 수 있는 변수에서 null 과 undefined를 제거할 때 사용한다.*

*: ! 를 통해 null 과 undefined가 아니라고 명시한다.*

```tsx
// 타입 유추 : Date | undefined
let maybeDate = Math.random() > 0.5 
                ? undefined
                : new Date();

// 타입이 Date 타입이라고 간주
maybeDate as Date;

// 타입이 Date 타입이라고 간주
maybeDate!;
```

```tsx
const seasonCounts = new Map([
    ["Kim", 10],
    ["Lee", 20],
])

// 타입 number | undefined
const maybeValue = seasonCounts.get("Kim");

console.log(maybeValue.toFixed);       // Error

// 타입 number
const knownValue = seasonCounts.get("Kim")!;

console.log(knownValue.toFixed);       // Ok
```

- ! 사용해 undefined 타입을 제거할 수 있다.

### 3) 타입 어서션 주의 사항

*: 타입을 피하는 하나의 도피 수단일 뿐, 자주 사용하지 않아야 한다.*

```tsx
const seasonCounts = new Map([
    ["Kim", 10],
    ["Lee", 20],
])

const knownValue = seasonCounts.get("Kim")!;

// Error
// 타입오류는 아니지만, 런타입 오류가 발생한다.
console.log(knownValue.toFixed);
```

> **어서션 vs 선언**
> 

```tsx
interface Entertain {
    acts: string[];
    name: string;
}

const declared: Entertain = {
    // Error
    name: "Kim",
}

const asserted = {
    // Ok
    // 허용되지만 런타임 오류 발생
    name: "Lee",
} as Entertain

// 런타임 오류 발생
console.log(declared.acts.join(", "));
console.log(asserted.acts.join(", "));
```

- 타입 어서션은 타입스크립트에 타입 검사 중 일부를 건너뛰도록 명시적으로 지시한다. 
따라서, 런타임 오류가 발생할 수 있다.

> **어서션 할당 가능성**
> 

*: 타입 중 하나가 다른 타입에 할당 가능한 경우에만 두 타입 간의 타입 어서션을 허용*

```tsx
let myValue = "Kim" as number;  // Error
```

- number 타입을 string 타입으로 할당 할 수 없다.

> **이중 타입 어서션**
> 

*: as unknown과 같은 top 타입으로 바꾼 뒤, 어서션을 한번 더 사용한다.*

```tsx
let myValue = "Kim" as unknown as number;  // Ok
```

- 도피 수단일 뿐 사용하면 안된다.

# 5. Const 어서션

---

> **const 어서션의 규칙**
> 
1. 배열은 가변 배열이 아니라 읽기전용 **튜플로 취급**
2. 리터럴은 일바적인 원시 타입과 동등하지 않고 **리터럴로 취급**
3. 객체 속성은 **읽기 전용으로 간주**

```tsx
// 타입 : (string | number)[]
[0, "Kim"];

// 타입 : readonly [0, "Kim"]
[0, "Kim"] as const;
```

### 1) 리터럴에서 원시타입으로

```tsx
// 타입 : () = > string
const getName = () => "Kim";

// 타입 : () => "Kim"
const getNameConst = () => "Kim" as const
```

- 특정 리터럴 값으로 명시한다.

```tsx
interface Joke {
    quote: string;
    style: "story" | "one-liner";
}

function tellJoke(joke: Joke) {
    if(joke.style === "one-liner") {
        console.log(joke.quote);
    } else {
        console.log(joke.quote.split("\n"));
    }
}

// 타입 : { quote: string, style: "one-liner"}
const narrowJoke = {
    quote: "Hello! My name is Kim",
    style: "one-liner" as const,
}

tellJoke(narrowJoke);   // Ok

// 타입 : { quote: string, style: string}
const wideObject = {
    quote: "Bye! My name is Lee",
    style: "one-liner",
}

// Error 
// : string 은 "story" | "one-liner" 타입에 할당할 수 없다.
tellJoke(wideObject);
```

### 2) 읽기 전용 객체

```tsx
function describePreference(preference: "maybe" | "no" | "yes") {
    switch (preference) {
        case "maybe":
            return "I suppose...";
        case "no":
            return "No Thanks";
        case "yes":
            return "Yes Please";
    }
}

// 타입: { movie: string, standup: string }
const preferenceMutable = {
    movie: "maybe",
    standup: "yes",
}
describePreference(preferenceMutable.movie);    // Erorr
preferenceMutable.movie = "no"                  // Ok

// 타입: {readonly movie : "maybe", readonly standup: "yes"}
const preferenceReadonly = {
    movie: "maybe",
    standup: "yes",
} as const;
describePreference(preferenceReadonly.movie);    // Ok
preferenceReadonly.movie = "no"                  // Error
```

- 객체에 as const 어서션을 붙이면, 
객체 안의 속성은 모두 리터럴 타입으로 바뀌고 + readonly 타입이 된다.