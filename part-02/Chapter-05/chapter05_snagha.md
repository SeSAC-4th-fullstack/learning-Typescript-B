# Chapter 05

> 함수
> 

# 함수 매개 변수

---

### 1) 필수 매개변수

: 타입스크립트에서는 선언된 모든 매개변수가 필수라고 가정한다.

```tsx
function singTwo(first: string, second: string) {
    console.log(`${first} // ${second}`);
}

singTwo("param1");                     // Error

singTwo("param1", "param2")            // Ok

singTwo("param1", "param2", "param3"); // Error
```

- 자바스크립트에서는 함수에서 정의된 매개변수와 관계없이 함수를 호출할 수 있다.
정의되지 않은 변수를 사용하면 undefined로 정의된다.
- 그러나, **타입스크립트에서는 함수에서 정의된 매개변수와 정확하게 입력되어야 한다.**

### 2) 선택적 매개변수

```tsx
function announceSong(song: string, singer?: string) {
    console.log(`Song : ${song}`);

    if(singer) {
        console.log(`Singer : ${singer}`);
    }
};

announceSong("ABC");            // Ok
announceSong("ABC", undefined)  // Ok
announceSong("ABC", "Sia")      // Ok
```

- **선택적 매개변수 항상 | undefined가 유니온 타입을 추가되어 있다.**

```tsx
function announceSong(song: string, singer: string | undefined) {
    console.log(`Song : ${song}`);

    if(singer) {
        console.log(`Singer : ${singer}`);
    }
};

announceSong("ABC");            // Error
announceSong("ABC", undefined)  // Ok
announceSong("ABC", "Sia")      // Ok
```

- 선택적 매개변수와 비슷하지만, 
**선택적 매개변수가 아닌 string | undefined로 타입이 정의된 매개변수 반드시 정의해야 한다.**

```tsx
function announceSong(song?: string, singer: string) {
    console.log(`Song : ${song}`);
};
```

- **선택적 매개변수는 앞에 선언 될 수 없다.**

### 3. 기본 매개변수

```tsx
function rateSong(song: string, rating = 0) {
    console.log(`${song} get ${rating}/5 stars!`)
};

rateSong("ABC");            // Ok
rateSong("ABC", 5);         // Ok
rateSong("ABC", undefined)  // Ok   
rateSong("ABC", "100")      // Error
```

- **선택적 매개변수에는 기본적으로 값이 제공되기 때문에 해당 타입스크립트 타입에는 | undefined 유니언 타입이 추가된다.**
- **Error**
: rating=0 은 기본값이 0이자 number 타입을 받는다.

### 4. 나머지 매개변수

```tsx
function singAllTheSong(singer: string, ...songs: string[]) {
    for(const song of songs){
        console.log(`${song} By ${singer}`);
    }
};

singAllTheSong("JPark");       // Ok
singAllTheSong("JPark", "songA", "songB", "songC", "songD");    // Ok
singAllTheSong("JPark", 1000)  // Error
```

- **스프레드 연산자 ( …)**
: 스프레드 연산자는 매개변수 가장 마지막에 올 수 있다. 
: 나머지 매개변수가 존재하지 않아도 괜찮다.
**: 나머지 부분을 해당 타입의 배열로 가져오게 된다.**
- **Error**
: 나머지 매개변수로 string 타입의 배열이 와야한다.

# 2. 반환 타입

---

> **반환 타입**
> 

```tsx
function singSong(songs: string[]) {
    for(const song of songs) {
        console.log(`${song}`);
    }

    return songs.length;
}

let a = singSong(["A", "B", "C"]);    // number로 유추된다.
```

```tsx
function getSongAt(songs: string[], index: number) {
    return index < songs.length
            ? songs[index]
            : undefined;
};

let a = getSongAt(["A", "B", "C"], 2);  // string | undefined로 유추된다.
```

- 함수에 다른 값을 가진 여러 개의 반환문을 포함하고 있다면, 
**타입스크립트는 반환 타입을 가능한 모든 반환 타입의 조합으로 유추한다.**

### 1) 명시적 반환 타입

> ***함수의 반환 타입을 정의하지 않는 것이 좋지만, 유용한 경우가 있다***
> 
1. 가능한 반환값이 많은 함수가 항상 동일한 타입의 값을 반환하도록 강제합니다.
2. 타입스크립트는 재귀 함수의 반환 타입을 통해 타입을 유추하는 것을 거부합니다.
3. 수백 개 이상의 타입스크립트 파일이 있는 매우 큰 프로젝트에서 타입스크립트 타입 검사 속도를 높일 수 있습니다.

> **반환 타입을 정의하는 방식**
> 
1.  파라미터 끝에 붙이는 방식 ) 다음에
2. => 전에 붙이는 방식

```tsx
// Case 1
// 파라미터 끝에 붙이는 방식 ) 다음에
function singSongRecursive1(songs: string[], count = 0): number {
    return songs.length 
            ? singSongRecursive1(songs.slice(1), count + 1)
            : count;
}

// Case 2
// => 전에 붙이는 방식
const singSongRecursive2 = (songs: string[], count = 0): number => 
    songs.length 
            ? singSongRecursive2(songs.slice(1), count + 1)
            : count;
```

```tsx
function getSongRecord(song: string): Date | undefined {
    switch(song) {
        case "Strange Fruit":
            return new Date('April 20, 2023');  // Ok
        case "Greensleeves":
            return "unknown";                   // Error
        default:
            return undefined                    // Ok
    }
}
```

- **Error**
: getSongRecord 함수의 반환 타입은 Date | undefined 이여야 한다. string 타입은 반환 타입으로 할당할 수 없다.

# 3. 함수 타입

---

> **함수 타입**
> 

: 함수처럼 생긴 “타입” 이다.

```tsx
// Case 1
// 타입: () => string
let nothingInGivenString: () => string;

// Case 2
// 타입: (songs: string[], count?: number) => number
let inputAndOut: (songs: string[], count?: number) => number;
```

```tsx
const songs = ["Juice", "Shake It Off", "What's up"];

function runOnSongs(getSongAt: (index: number) => string) {
    for(let i=0; i<songs.length; i += 1) {
        console.log(getSongAt(i));
    }
}
function getSongAt(index: number) {
    return `${songs[index]}`;
}
runOnSongs(getSongAt);      // Ok

function logSong(song: string) {
    return `${song}`;
}
runOnSongs(logSong);        // Error
```

- **Error**
: runOnSongs 함수의 매개변수에는 (index: number) => string 형태의 함수 타입이 와야 한다.

> **해석하는 과정**
> 
1. '(song: string) => string' 형식의 인수는 '(index: number) => string' 형식의 매개 변수에 할당될 수 없습니다.
2. 'song' 및 'index' 매개 변수의 형식이 호환되지 않습니다.
3. 'number' 형식은 'string' 형식에 할당할 수 없습니다.

### 1) 함수 타입 괄호

```tsx
// 타입은 string | undefined 유니언을 반환하는 함수
let returnStringOrUndefined: () => string | undefined;

// 타입은 undefined나 string을 반환하는 함수
let maybeReturnString: (() => string) | undefined;
```

- 함수 타입을 괄호로 묶어 다른 타입과 함께 사용할 수 있다.

### 2) 매개변수 타입 추론

**: 타입스크립트는 선언된 타입의 위치에 제공된 함수의 매개변수 타입을 유추할 수 있다.**

```tsx
let singer: (song: string) => string;

singer = function(song) {
    return `Singing : ${song.toUpperCase()}`  // OK
}
```

- singer은(song: string) => string 형태의 함수 타입이다. 따라서, song이 string 타입이라는 것을 유추할 수 있다.

```tsx
const songs = ["Call Me", "Jolene", "The Chain"];

songs.forEach((song, index) => {
    console.log(song.toUpperCase(), index.toFixed());
})
```

```tsx
// lib.es5.d.ts 참고
forEach(callbackfn: (value: T, index: number, array: readonly T[]) => void,
        thisArg?: any
       ): void;
```

- forEach의 정의에 따르면 index는 number 타입, song은 string 타입임을 유추할 수 있다.

### 3) 함수의 타입 별칭

: 함수 타입도 별칭을 줄 수 있다.

```tsx
type StringToNumber = (input: string) => number;

let stringToNumber: StringToNumber;

stringToNumber = (input) => input.length;           // Ok

stringToNumber = (input) => input.toUpperCase();    // Error
```

- **Error**
: stringToNumber은 (input: string) => number 형태의 함수 타입이다. 따라서 반환값은 number 타입이어야 한다.

```tsx
type NumberToString = (input: number) => string;

function usesNumberToString(numberToString: NumberToString) {
    console.log(`The string is : ${numberToString(1234)}`);
}

usesNumberToString((input) => `${input}! Hooray!`);     // Ok

usesNumberToString((input) => input * 2);               // Error

```

- **Error**
: usesNumberToString은 매개변수로 number을 받고 string을 리턴하는 함수타입이여야 한다.

# 4. 그 외 반환 타입

---

### 1) void 반환 타입

```tsx
function logSong(song: string | undefined): void {
    if(!song) {
        return;     // Ok
    }

    console.log(`${song}`);

    return true;    // Error
}
```

- **void를 반환하는 함수 == 값의 반환을 허용하지 않는다.**
- **Error**
: logSong은 void를 반환하는 함수, 즉 반환값을 허용하지 않는다.

```tsx
let songLogger: (song: string) => void;

songLogger = (song) => {
    console.log(`${song}`);
}

songLogger("Heart Of Glass");   // Ok
```

```tsx
function returnsVoid() {
    return;
}

let lazyValue: string | undefined;

lazyValue = returnsVoid();  // Error
```

- **Error**
: void 타입은 string | undefined 타입에 할당할 수 없다.

```tsx
const records: string[] = [];

function saveRecords(newRecords: string[]) {
    newRecords.forEach(record => records.push(record));
}

saveRecords(['21', 'Come on over', 'ABC']);
```

**⇒ void 타입은 함수의 반환값이 자체적으로 반환될 수 있는 값도 아니고, 사용하기 위한 것도 아니라는 표시이다.**

### 2) never 반환 타입

: never 반환 함수는 의도적으로 항상 오류를 발생키기거나 무한 루프를 실행하는 함수이다.

```tsx
function fail(message: string): never {
    throw new Error(`Invariant failure: ${message}`);
}

function workWithUnsafeParam(param: unknown) {
    if(typeof param != "string") {
        fail(`param should be a string, not ${typeof param}`);
    }

    // 여기에서 param의 타입은 string으로 알려집니다.
		// never 반환함수인 fail이 조건에 걸려서 param은 반드시 string이라고 유추된다.
    param.toUpperCase();   // Ok
}
```

- **never 반환 함수는 이후 코드가 실행되지 않음을 의미하므로, 타입 추론의 방식으로도 사용된다.**

# 5. 함수 오버로드

---

> **함수 오버로드**
> 

: **오버로드 함수 호출에 대해 구문 오류를 생성할지는, 함수의 오버로드 시그니처만 확인한다. 
: 구현 시그니처는 함수의 내부 로직에서만 사용된다.**

```tsx
// 오버로드 시그니처
function createDate(timestamp: number): Date;
function createDate(month: number, day: number, year: number): Date;

// 구현 시그니처
function createDate(monthOrTimestamp: number, day?: number, year?: number) {
    return day === undefined || year === undefined
                ? new Date(monthOrTimestamp)
                : new Date(year, monthOrTimestamp, day);
}

createDate(10000);          // Ok
createDate(6, 19, 2023);    // Ok

createDate(4, 1);           // Error
```

- **Error**
: createDate(4, 1)는 어떠한 오벌로드 시그니처에도 해당하지 않는다.

⇒ 함수의 오버로드는 복잡하고 설명하기 어려운 함수 타입에 사용하는 최후의 수단입니다. 함수를 단순하게 유지하고 가능하면 오버로드를 사용하지 않는 것이 좋습니다.

### 1) 호출 시그니처 호환성

**: 구현시그니처는 모든 오버로드 시그니처와 호환이 되어야 한다.**

```tsx
// 오버로드 시그니처 1
function format(data: string): string;
// 오버로드 시그니처 2
function format(data: string, needle: string, haystack: string): string;
// 오버로드 시그니처 3 - Error
function format(getData: () => string): string;

// 구현 시그니처
function format(data: string, needle?: string, haystack?: string) {
    return needle && haystack 
            ? data.replace(needle, haystack)
            : data;
}
```

- getData: () => string 와 data: string, needle?: string, haystack?: string 는 호환되지 않는다.