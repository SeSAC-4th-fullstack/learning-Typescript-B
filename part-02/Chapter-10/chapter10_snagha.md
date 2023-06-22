# Chapter 10

> 제네릭
> 

# 1. 제네릭 함수

---

> **제네릭 (Generic)**
> 

*: 타입 간의 관계를 알아낸다.*

*: 타입스크립트에서 함수와 같은 구조체는 **제네릭 타입 매개변수**를 원하는 수만큼 선언할 수 있다.*

*: 타입 매개변수는 구조체의 각 인스턴스에 대해 **타입인수**라고 하는 서로 다른 타입 제공*

```tsx
function identity<T>(input: T) {
    return input;
}

const numeric = identity("Me")  // 타입 : "Me"
const stringy = identity(123)   // 타입 : 123
```

```tsx
const identity = <T>(input: T) => input;

identity(123)   // 타입: 123
```

- 화살표 함수도 제네릭 사용이 가능하다.

*⇒ 제네릭을 사용하면 타입 안정성을 유지하고 any 타입을 피하면서 다른 입력과 함께 재사용 가능*

### 1) 명시적 제네릭 호출 타입

```tsx
function logWrapper<Input>(callback: (input: Input) => void) {
    return (input: Input) => {
        console.log("Input : ", input);
        callback(input);
    }
}

// 타입 : (input: string) => void;
logWrapper((input: string) => {
    console.log(input.length);      // Ok
})

// 타입 : (input: unknown) => void
logWrapper((input) => {
    console.log(input.length);      // Error
})

// 타입: (input: string) => void
logWrapper<string>((input) => {
    console.log(input.length);      // Ok
})

// 타입: 
logWrapper<string>((input: boolean) => {
                                    // Error
})

// 타입: (input: string) => void
logWrapper<string>((input: string) => {
    console.log(input.length);      // Ok
})
```

- **Case 2 - Error**
: input의 타입이 명시적으로 정해지지 않아서 unknow 타입으로 할당 된다. 따라서, unknown 타입은 속성에 접근할 수 없기에 에러가 발생한다.
- **Case 2 - Ok**
: 타입이 정해지지 않아도 <string> 통해 명시적으로 타입을 정한다.
- **Case 4 - Error**
: <string>으로 통해 명시적으로 타입을 정했는데, input: boolean으로 또 타입을 지정해주면 충돌이 발생한다.

### 2) 다중 함수 타입 매개변수

```tsx
function makeTuple<First, Second>(first: First, second: Second) {
    return [first, second] as const;
}

// 타입 : [boolean, string]
let tuple = makeTuple(true, "ABC")
```

- 여러 개의 타입 매개변수를 <First, Second, …>과 같이 , 기호를 통해 여러 개 선언할 수 있다.

```tsx
function makePair<Key, Value>(key: Key, value: Value) {
    return {key, value};
}

// Ok : 타입 인수가 둘 다 제공되지 않음
// 타입 : { key: string; value: number}
makePair("ABC", 123);

// Ok : 타입 인수가 둘 다 제공됨
makePair<string, number>("ABC", 123)    // 타입 : { key: string; value: number}
makePair<"ABC", 123>("ABC", 123)        // 타입 : { key: "ABC";  value: 123}

// Error : 타입 인수가 제공되지 않음
makePair<string>("ABC", 123);

// Error : 타입의 충돌
makePair<string, number>(123, "ABC");
```

- 함수에서 선언된 제네릭 매개변수가 제공되려면 모두 제공되거나, 타입의 충돌이 없어야 한다.

# 2. 제네릭 인터페이스

---

> **인터페이스에서 제네릭 사용**
> 

```tsx
interface Box<T> {
    inside: T;
}

let stringyBox: Box<string> = {
    inside: "abc",
}

let numberBox: Box<number> = {
    inside: 123,
}

let incorrectBox: Box<number> = {
    inside: false,      // Error
}
```

- interface에 따르면, 
제네릭 매개변수로 number가 선언되었으므로, inside도 number 타입이여야 한다.

> **내장 함수는 Array도 제네릭 인터페이스로 정의 된다.**
> 

```tsx
interface Array<T> {

    // 배열에서 마지막 요소를 제거하고 그 요소를 반환
    // 배열이 비어 있는 경우 undefined를 반환하고 배열은 수정되지 않음
    pop(): T | undefined;

    // 배열의 끝에 새로운 요소를 추가하고 배열의 길이를 반환
    push(...item: T[]): number;
}
```

### 1) 유추된 제네릭 인터페이스 타입

```tsx
interface LinkedNode<Value> {
    next?: LinkedNode<Value>;
    value: Value;
}

function getLast<Value>(node: LinkedNode<Value>): Value {
    return node.next
            ? getLast(node.next)
            : node.value;
}

// 유추된 Value 타입 인수 : Date
let lastDate = getLast({
    value: new Date("09-13-1993"),
})

// 유추된 Value 타입 인수: string
let lastFruit = getLast({
    next: {
        value: "banana",
    },
    value: "apple",
})

// 유추된 Value 타입 인수: number
let lastMismatch = getLast({
    next: {
        value: 123
    },
    value: false,       // Error
})
```

```tsx
// LinkedNode 형식
interface LinkedNode<Value> {
    next?: {
        next?: {
            next?: {
                next?: LinkedNode<Value>;
                value: Value;
            };
            value: Value;
        };
        value: Value;
    };
    value: Value;
}
```

- LinkedNode 내부 속성들은 제네릭 매개변수 Value에 묶여 하나의 타입으로 유추된다.
- getLast는 LinkedNode의 마지막 속성을 리턴한다.

```tsx
interface CreateLike<T> {
    contents: T;
}

let missingGeneric: CreateLike = {
    contents: "abc", // Erorr
}
```

- 인터페이스가 타입 매개변수를 선언하는 경우, 
해당 인터페이스를 참조하는 **모든 타입 애너테이션은 이에 상응하는 타입 인수를 제공해야한다.**

# 3. 제네릭 클래스

---

> **제네릭 클래스**
> 

*: 인터페이스처럼 클래스도 나중에 멤버에서 사용할 임의의 수의 타입 매개변수를 선언할 수 있다.*

```tsx
class Secret<Key, Value> {
    key: Key;
    value: Value;

    constructor(key: Key, value: Value){
        this.key = key;
        this.value = value;
    }

    getValue(key: Key): Value | undefined {
        return this.key === key
                ? this.value
                : undefined;
    }
}

// 타입 : Secret<number, string>
const storage = new Secret(123, "Kim"); 

// 타입 : string | undefined
const rtn = storage.getValue(123);
```

### 1) 명시적 제네릭 클래스 타입

```tsx
class CurriedCallback<Input> {
    #callback: (input: Input) => void;

    constructor(callback: (input: Input) => void) {
        this.#callback = (input: Input) => {
            console.log("Input : ", input);

            callback(input);
        }
    }

    call(input: Input) {
        this.#callback(input)
    }
}

// 타입 : CurriedCallback<string>
new CurriedCallback((input: string) => {
    console.log(input.length);      // Ok
});

// 타입 : CurriedCallback<unknown>
new CurriedCallback((input) => {
    console.log(input.length);      // Error
})  

// 타입 : 
new CurriedCallback<string>((input: boolean) => {
                                    // Error
})
```

- **Case 1 - Error
:** input의 타입을 명확히 정의하지 않아 Error가 발생한다. 따라서 , input은 unknown 타입이 되며, 속성에 접근할 수 없다.
- **Case 2 - Error**
: CurriedCallback의 제네릭 매개변수는 string이라고 명시 했는데, input에 boolean으로 들어와 충돌이 발생한다.

### 2) 제네릭 클래스 확장

```tsx
class Quote<T> {
    lines: T;

    constructor(lines: T) {
        this.lines = lines;
    }
}

class SpokenQuote extends Quote<string[]> {
    speak() {
        console.log(this.lines.join("\n"));
    }
}

new Quote("Do your Best!").lines;   // 타입 : string
new Quote([1, 2, 3, 4, 5]).lines;   // 타입 : number[]

new SpokenQuote([ "Kim", "Park", "Lee"]).lines; // 타입 : string[]

new SpokenQuote([1, 2, 3, 4, 5]);   // Error
```

- SpokenQuote 클래스는 Quote<string[]> 타입의 확장이다.
따라서, 생성자에 들어올 수 있는 타입은 string[ ] 이다.

```tsx
class Quote<T> {
    lines: T;

    constructor(lines: T) {
        this.lines = lines;
    }
}

class AttributedQuote<Value> extends Quote<Value> {
    speaker: string;

    constructor(value: Value, speaker: string) {
        super(value);
        this.speaker = speaker;
    }
}

// Ok
new AttributedQuote(
    "The road to successs is always under construction",
    "Lily Tomlin"
)
```

- 첫 번째 매개변수로 string 타입이 들어와서 Quote<string> 타입을 확장하게 된다.

### 3) 제네릭 인터페이스 구현

```tsx
interface ActingCredit<Role>{
    role: Role;
}

class MoviePark implements ActingCredit<string> {
    role: string;
    speaking: boolean;

    constructor(role: string, speaking: boolean) {
        this.role = role;
        this.speaking = speaking;
    }
}

const part = new MoviePark("ABC", true);     // Ok    
part.role;  // 타입 : string

class IncorrectExtension implements ActingCredit<string> {
    role: boolean;
}
```

- ActingCredit<string>을 implement 했기 때문에 IncorrectExtension의 role 타입은 string으로 할당 된다.

### 4) 메서드 제네릭

```tsx
class CreatePairFactory<Key> {
    key: Key;

    constructor(key: Key) {
        this.key = key;
    }

    createPair<Value>(value: Value) {
        return { key: this.key, value }
    }
}

const factory = new CreatePairFactory("role");  // 타입: CreatePairFactory<string>

const numberPair = factory.createPair(10);      // 타입 : {key: string, value: number}

const stringPair = factory.createPair("Kim");   // 타입 : {key: string, value: string}
```

- string 타입인 “role”이 제네릭 매개변수 Key에 명시되었다. 따라서 Key는 string 타입이다.

 

### 5) 정적 클래스 제네릭

> **클래스 정적(Static) 멤버**
> 

*: 클래스의 정적 멤버는 인스턴스 멤버와 구별되고 클래스의 특정 인스턴스와 연결되어 있지 않는다.*

*: 클래스의 정적 멤버은 클래스 인스턴스에 접근할 수 없거나 타입 정보를 지정할 수 없다.*  

*: 따라서, 정적 클래스 메서드는 자체 타입 매개변수를 선언할 수 있지만 클래스에 선언된 어떤 타입 매개변수에도 접근할 수 없다.* 

```tsx
class BothLogger<OnInstance> {
    instanceLog(value: OnInstance) {
        console.log(value);
        return value;
    }

    static staticLog<Onstatic>(value: Onstatic) {
        
        // Error
        // : static인 staticLog는 다른 인스턴스에 접근할 수 없다.
        let fromInstance: OnInstance;
        
        console.log(value);
        return value;
    }
}

const logger = new BothLogger<number[]>;
logger.instanceLog([1, 2, 3])           // 타입: number[]

BothLogger.staticLog([false, true]);    // 유추된 Onstatic 타입 : boolean[]

BothLogger.staticLog<string>("Kim");    // 유추된 Onstatic 타입 : string
```

- 클래스 정적 멤버인 staticLog는 다른 인스턴스에 접근할 수 없다. 따라서, OnInstance 사용 불가

# 4. 제네릭 타입 별칭

---

> 제네릭 타입 별칭
> 

*: 제네릭 타입 별칭은 일반적으로 제네릭 함수의 타입을 설명하는 함수와 함께 사용됩니다.*

```tsx
type CreatesValue<Input, Output> = (input: Input) => Output;

// 타입 : (input: string) => number;
let creator: CreatesValue<string, number>;

creator = text => text.length;          // Ok

creator = text => text.toUpperCase();   // Error : number로 반환하는 타입이어야 한다.
```

- creator는 CreatesValue<string, number> 타입으로 명시 되었다.
따라서 number을 반환하는 타입으로 정의가능하다.

### 1) 제네릭 판별된 유니언

```tsx
type Result<Data> = FailureResult | SuccessfulResult<Data>;

interface FailureResult {
    error: Error;
    succeeded: false;       
}  

interface SuccessfulResult<Data> {
    data: Data;
    succeeded: true;
}

function handleResult(result: Result<string>) {
    if(result.succeeded) {
        // 타입 추론 : SuccessfulResult<string>
        console.log(`We did it! ${result.data}`);
    } else {
        // 타입 추론 : FailureResult
        console.error(`Umm... ${result.error}`)
    }

    result.data;    // Error
}
```

- **Error**
: result가 SuccessfulResult 타입인지, FailureResult 타입인지 추론할 수 없다.

# 5. 제네릭 제한자

---

### 1) 제네릭 기본값

*: 타입 매개변수 선언 뒤에 “= 기본타입”을 추가해 타입 인수를 명시적으로 제공할 수 있다.*

```tsx
interface Quote<T=string> {
    value: T;
}

let explicit: Quote<number> = { value: 123, }       // Ok

let implicit: Quote = { value: "Do your Best!" };   // Ok

let mismatch: Quote = { value: 123 };               // Error
```

- 기본적으로 타입인수가 string으로 주어졌기 때문에, 
타입 인수가 선언되지 않으면 string 타입으로 선언된다.

```tsx
interface KeyValuePair<Key, Value=Key> {
    key: Key;
    value: Value;
}

// 타입: KeyValuePair<string, number>
let allExplicit: KeyValuePair<string, number> = {
    key: "Kim", 
    value: 123,
}

// 타입: KeyValuePair<string, string>
let oneDefaulting: KeyValuePair<string> = {
    key: "Kim",
    value: "Park",
}

let firstMissing: KeyValuePair = {
    // error
    key: "rating", 
    value: 10,
}
```

- Error
: 타입인수 Value는 기본값이 선언되어 생략이 가능하지만, 타입 인수 Key는 선언해줘야 한다.

```tsx
function inTheEndM<First, Second, Third = number, Fourth = string>() {};    // Ok

function InTheMiddle<First, Second = string, Third, Fourth>() {}
```

- 기본값 선언은 끝에 위치해야 한다. 중간에 있으면 안된다.

# 6. 제한된 제네릭 타입

---

> **T extends <Type>**
> 

```tsx
interface WithLength {
    length: number;
}

function logWithLength<T extends WithLength>(input: T) {
    console.log(`Length: ${input.length}`);
    return input
}

logWithLength("Do your Best!");     // 타입: string
logWithLength([false, true]);       // 타입: boolean[]
logWithLength({length: 123});       // 타입: { length: number }

logWithLength({heigth: 123});       // Error
logWithLength(new Date());          // Error
```

- **Case 1 - Error**
: {heigth: 123} 타입은 WithLength에 할당할 수 없다.
- **Case 2 - Error**
: new Date()는 length 속성을 사용할 수 없다.

### 1) keyof와 제한된 타입 매개변수

*: extends와 keyof를 함께 사용하면 타입 매개변수를 이전 타입 매개변수의 키로 제한할 수 있다.*

```tsx
function get<T, Key extends keyof T>(container: T, key: Key) {
    return container[key];
}

const roles = {
    favorite: "Fargo",
    others: [ "Kim", "Park", "Lee"],
}

const favorite = get(roles, "favorite");    // Ok
const others = get(roles, "others");        // Ok

const missing = get(roles, "extras");       // Error
```

- extends keyof를 통해 Key의 타입이 “favorite” | “others” 라는 것을 확인한다.
- “extras”는 Key의 타입에 포함되자 않기 때문에 오류가 발생한다.

```tsx
function get<T>(container: T, key: keyof T) {
    return container[key];
}

const roles = {
    favorite: "Fargo",
    others: [ "Kim", "Park", "Lee"],
}

const found = get(roles, "favorite");  // 타입 string | string[]
```

- 모든 key에 대해서 return하므로 반환 타입은 roles가 가지고 있는 모든 속성의 타입이 된다.

# 7. Promise

---

> **Promise**
> 

: 네트워크 요청과 같이 요청 이후 결과를 받기까지 대기가 필요한 것을 의미한다. 

: resolve → 성공적으로 완료됨

: reject → 오류 발생

### 1) Promise 생성

```tsx
// Promise의 축소형식
class PromiseLike<Value> {
    constructor(
        executor: (
            resolve: (value: Value) => void,
            reject: (reason: unknown) => void,
        ) => void,
    ) {} 
}
```

```tsx
// 타입: Promise<unknown>
const resolveUnknown = new Promise((resolve) => {
    setTimeout(() => resolve("Done"), 1000);
})

// 타입: Promise<string>
const resolvesString = new Promise<string>((resolve) => {
    setTimeout(() => resolve("Done"), 1000); 
})
```

- 결과적으로 값을 resolve하려는 Promise를 만들려면  Promise의 타입 인수를 명시적으로 선언해야 한다. 타입 인수가 없다면 unknown으로 가정한다.

```tsx
// 타입: Promise<string>
const textEventually = new Promise<string>((resolve) => {
    setTimeout(() => resolve("Done"), 1000);
})

// 타입: Promise<number>
const lengthEventually = textEventually.then((text) => text.length);
```

- 1초 후에 string 값을 resolve 하는 textEventually
- 그리고 또 1초 후에, number를 resolve하는 lengthEventually

### 2) async 함수

*: async를 선언한 모든 함수는 Promise를 반환한다.* 

```tsx
// 타입: (text: string) => Promise<number>
async function lengthAfterSecond(text: string) {
    await new Promise((resolve) => setTimeout(resolve, 1000))
    return text.length;
}

// 타입: (text: string) => Promise<number>
async function lengthImmediately(text: string) {
    return text.length
}
```

- 그러므로 Promise를 명시적으로 언급하지 않더라도 async 함수에서 수동으로 선언된 반환 타입은 항상 Promise 타입이 된다.

```tsx
// Ok
async function givesPromiseForString(): Promise<string> {
    return "Done";
}

// Error
async function givesString(): string {
    // 비동기 함수 또는 메서드의 반환 형식은 전역 Promise<T> 형식이어야 합니다.
    return "Done";
}
```

# 8. 제네릭 올바르게 사용하기

---

> **제네릭의 사용**
> 

*: 타입을 설명하는 데 많은 도움이 되지만, 코드가 복잡해질 수 있다.*

### 1) 제네릭 황금률

: 각 함수 타입 매개변수는 매개변수에 사용되어야 하고,

: 그 다음 적어도 하나의 다른 매개변수 또는 함수의 반환타입에서도 사용되어햐 한다.

```tsx
// 변경 전 
function logInput<Input extends string>(input: Input) {
    console.log("Hi! ", input);
}

// 수정
function logInput(input: string) {
    console.log("Hi! ", input);
}
```

### 2) 제네릭 명명 규칙

*: T → U → V 순서로 사용*

*: 제네릭 상태를 S 사용*

*: 데이터 구조 K, V 사용*

```tsx
function labelBox<L, V>(l: L, v: V) {
    
}

// 제네릭 L, V 가 무엇을 의미하는지 모르기 때문에 명확안 명칭을 쓴다.
function labelBox<Label, Value>(lable: Label, value: Value) {
    
}
```