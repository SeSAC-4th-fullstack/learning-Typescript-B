# 5) 함수

---

## **함수 매개변수**

```tsx
function sing(song) {
  console.log(`Singing : ${song}`);
}
```

sing 함수를 작성한 개발자가 song 매개변수를 제공하기 위해 의도한 값의 타입은 무엇일까 ?

명시적 타입 정보가 선언되지 않는다면, 타입스크립트가 이를 `any` 타입으로간주하기 때문에 매개변수의 타입은 무엇이든 될 수 있다.

---

### `**필수 매개변수**`

자바스크립트에서는 인수의 수와 상관없이 함수를 호출할 수 있다. 하지만 타입스크립트는 함수에 선언된 모든 매개변수가 필수라고 가정한다.

함수가 잘못된 수의 인수로 호출되면, 타입스크립틍는 타입 오류의 형태로 이의를 제기한다.

( `함수가 너무 적가나 많은 인수로 호출되면 타입스크립트는 인수의 개수를 계산한다`. )

```tsx
function singTwo(first: string, second: string) {
  console.log(`${first} / ${second}`);
}

singTwo('Ball and Chain'); // logs : 'Ball and Chain' / undefind

// Error : arguments 2개를 기대했지만, 한개의 변수밖에 존재하지 않는다.

singTwo('I will survive', 'Higher Love'); // OK

singTwo('I will survive', 'Higher Love', 'Dreams');
// Error : 2개의 arguments를 기대했지만, 3개의 인자가 존재 )
```

모든 인수값이 존재하는지 확인 하지 못하면 이전 singTwo 함수가 undefined를 로그로 남기거나 인수를 무시하는 것과 같이 코드에서 예기치 않은 동작이 발생한다.

---

### `**선택적 매개변수**`

자바스크립트에서는 함수 매개변수가 제공되지 않으면 함수 내부의 인수값은 `undefined` 로 기본값이 설정된다.

때로는, 함수 매개변수를 제공할 필요가 없을 때도 있고, `undefined` 값을 위해 의도적으로 사용할 수도 있다.

이 경우에 타입스크립트에서는 선택적 객체 타입 속성과 유사하게 타입 애너테이션의 `:` 앞에 `?` 를 표기해 `매개변수가 선택적`이라고 표시한다.

선택적 매개변수에는 항상 `| undefined` 가 유니언 타입으로 추가되어 있기 때문에, 함수 호풀에 선택적 매개변수를 제공할 필요는 없다.

```tsx
function announceSong (song : string, singer ?: string) {
		console.log(`Song : ${song}`);

		if(singer) {
		console.log(`Singer : ${singer});
	}
};

announceSong('Greensleeves'); //OK
announceSong('Greensleeves', undefined); //OK
announceSong('Greensleeves', 'Sia'); //OK
```

이러한 선택적 매개변수는 항상 암묵적으로 `undefined` 가 될 수 있다. 이전 코드에서 singer 는 `singer | undefined` 타입으로 시작한 후 if 문에 따라 `string` 타입으로 좁혀진다.

선택적 매개변수는`| undefined` 를 포함하는 유니언 타입 매개변수와는 다르다 `?` 로 표시된 선택적 매개변수가 아닌 매개변수는 값이 명시적으로 `undefined` 일지라도 항상 제공되어야 한다.

```tsx
unction announceSong (song : string, singer : string | undefined) {
		console.log(`Song : ${song}`);

		if(singer) {
		console.log(`Singer : ${singer});
	}
};

announceSong('Greensleeves');
// Error : 2개의 arguments 를 예상했지만, 1개만 받음

announceSong('Greensleeves', undefined); //OK
announceSong('Greensleeves', 'Sia'); //OK
```

함수의 모든 선택적 매개변수는 마지막 매개변수 여야만 한다.

```tsx
function announceSinger(singer?:string , song : strong){...}
// Error : 옵셔널 매개변수는 마지막 매개변수 여야한다.
```

---

### `기본 매개변수`

타입스크립트의 타입 추론은 초기 변숫값과 마찬가지로 기본 함수 매개변수에 대해서도 유사하게 작동한다. 매개`변수에 기본값이 있고 타입 애너테이션이 없는 경우, 타입스크립트는 해당 기본값을 기반으로 매개변수의 타입을 추론한다.`

```tsx
function rateSong(song: string, rating = 0) {
  console.log(`${song} gets ${rating}/5 stars`);
}

rateSong('Photograph'); // OK
rateSong('Set Fire to the Rain', 5); // OK
rateSong('Set Fire to the Rain', undefined); // OK

rateSong('At Last!', '100');

// Error : "100" argument 의 타입이 'number | undefined" 에 할당 가능하지 않음
```

---

### `나머지 매개변수`

자바스크립트의 일부 함수는 임의의 수의 인수로 호출할 수 있도록 만들어진다. `...` 스프레드 연산자는 함수 선언의 마지막 매개변수에 위치하며, 해당 매개변수에서 시작해 함수에 전달된 `나머지 ( rest )` 인수가 모두 단일 배열에 저장되어야 한다.

타입스크립트의 이러한 `나머지 매개변수` 타입을 일반 매개변수와 유사하게 선언할 수 있다. 단 인수 배열을 나타내기 위해 끝에 `[ ]` 구문이 추가된다는 점이 다르다.

```tsx
function singAllTheSongs(singer: string, ...songs: string[]) {
  for (const song of songs) {
    console.log(`${song}, by ${singer}`);
  }
}

singAllTheSongs('Alicia Keys'); // OK
singAllTheSongs('Lady Gaga', 'Bad Romance', 'Just Dance', 'Poker Face'); // OK

singAllTheSongs('Ella Fitzgerald', 2000);
// Error : number 타입은 string 타입에 할당될 수 없다.
```

## 반환 타입

---

타입스크립트는 `지각적  ( perspective )` 이다. 함수가 반환할 수 있는 가능한 모든 값을 이해하면, 함수가 반환하는 타입을 알 수 있다.

```tsx
// Type : (songs : string[], index : number) => string | undefined;

function singSongs(songs: string[]) {
  for (const song of songs) {
    console.log(`${song}`);
  }

  return songs.length;
}

// 다음 코드의 예제에서는 함수가 두 가지 가능한 반환값이 각각 string 과 undefined 이기 때문에,
// string | undefined 타입을 반환하는 것으로 유추된다.

// Type : (songs : string[], index : number) => string | undefined
function getSongsAt(songs: strong[], index: number) {
  return index < songs.length ? songs[index] : undefind;
}
```

---

### `**명시적 반환 타입**`

변수와 마찬가지로 타입 애너테이션을 사용해 함수의 반환 타입을 명시적으로 선언하지 않는 것이 좋다. 그러나 특히 함수에서 반환타입을 명시적으로 선언하는 방식이 매우 유용할때가 종종 있다.

- `가능한 반환값이 많은 함수가 항상 동일한 타입의 값을 반환하도록 강제한다.`
- `타입스크립트는 재귀 함수의 반환타입을 통해 타입을 유추하는것을 거부한다.`
- `수백 개 이상의 타입스크립트 파일이 있는 매우 큰 프로젝트에서 타입스크립트 검사 속도를 높일 수 있다.`

함수 선언 반환 타입 애너테이션은 매개변수 목록이 끝나는 `)` 다음에 배치되거나,

함수 선언의 경우에는 `{` 앞에 배치된다.

```tsx
// 함수 선언식
function singSongsRecursive = (songs : string[] , count = 0 ) : numbner {
		return songs.length ? singSongsRecursive(songs.slice(1), count +1 ) : count;
}

// 화살표 함수
 const singSongsRecursive = (songs : string[] , count = 0 ) :numbner  =>
	 songs.length ? singSongsRecursive(songs.slice(1), count +1 ) : count;
```

함수의 반환문이 함수의 반환 타입으로 할당할 수 없는 값을 반환하는 경우 타입스크립트는 할당 가능성 오류를 표시한다.

다음의 예제에서는 함수가 `Date | undefined` 를 반환하도록 명시적으로 선언되었지만, 반환문 중 하나가 `string` 을 반환하도록 잘못 제공하고 있다.

```tsx
function getSongRecodingDate(song: string): Date | undefined {
  switch (song) {
    case 'Strange Fruit':
      return new Date('April 20, 1939'); // OK
    case 'Greensleeves':
      return 'unknown'; // string 타입은 Date 타입에 할당가능하지 않음.
    default:
      return undefined; // OK
  }
}
```

---

## 함수 타입

---

자바스크립트에서는 함수를 값으로 전달할 수 있다. 즉 함수를 가지기 위한 매개변수 또는 변수의 타입을 선언하는 방법이 필요하지만, 함수타입 구문의 화살표 함수와 유사하지만, 함수 본문 대신 타입이 있다.

```tsx
let nothingGivesString = () => string;

let inputAndOutput: (songs: string[], count?: number) => number;
```

함수 타입은 콜백 매개변수 를 설명하는데 자주 사용된다.

```tsx
const songs = ['Juice', 'Shake It Off', "What's Up"];

function runOnSongs(getSongsAt: (index: number) => string) {
  for (let i = 0; i < songs.length; i += 1) {
    console.log(getSongAt(i));
  }
}

function getSongsAt(index: number) {
  return `${songs[index]}`;
}

runOnSongs(getSongsAt); // OK

function logSong(song: string) {
  return `${song}`;
}

runOnSongs(logSong); // OK

// (songs : string) => string 은 (index : number) => string 타입에 할당할 수 없다.
// song 과 index 는 호환가능하지 않다
// number 타입이 string 타입에 할당 가능하지 않음
```

오류 메시지는 할당 가능성 오류의 예로 몇가지 상세한 단계까지 제공한다.

- `첫 번째 들여쓰기 단계는 두 함수의 타입을 출력한다.`
- `다음 들여쓰기 단계는 일치하지 않는 부분을 지정한다.`
- `마지막 들여쓰기 단계는 일치하지 않는 부분에 대한 정확한 할당 가능성 오류를 출력.`

---

### `함수 타입 괄호`

유니언 타입을 포함해, 함수 타입은 다른 타입이 사용되는 모든곳에 배치할 수 있다. →

```tsx
// 타입은 string | undefined 유니언을 반환하는 함수
let returnsStringOrUndefined => string | undefined;

// 타입은 undefined 나 string 을 반환하는 함수
let maybeReturnsString : ( () => string) | undefined;
```

---

### `매개변수 타입 추론`

매개변수로 사용되는 `인라인 함수` 를 포함하여 작성한 모든 함수에 대해 매개변수를 선언해야 한다면 번거롭기 때문에, 타입스크립트는 선언된 타입의 위치에 제공된 함수의 매개변수 타입을 유추 할 수 있다.

```tsx
let singer: (song: string) => string;

singer = function (song) {
  // song : string 타입
  return `Singing : ${song.toUpperCase()}`; //OK
};
```

함수를 매개변수로 갖는 함수에 인수로 전달된 함수는 해당 매개변수 타입도 잘 유추할 수 있다.

```tsx
songs.forEach((song, index) ) => {
  console.log(`${song} is at index ${index}``)
}
```

---

### `함수 타입 별칭`

함수 매개변수도 함수 타입을 참조하는 별칭을 입력할 수 있다.

```tsx
type NumberToString = (input: number) => string;

function usesNumberToString(numberToString: NumberToString) {
  console.log(`The String is: ${numberToString}`);
}

usesNumberToString((input) => `${input}! Hooray`);

usesNumberToString((input) => input * 2);

// Error : string 타입에 number 타입이 할당될 수 없다.
```

`타입 별칭`은 특히 함수 타입에 유용하다.

타입 별칭을 이용하면 반복적으로 작성하는 매개변수와 반환 타입을 갖는 코드 공간을 많이 절약할 수 있다.

---

## 그 외 반환타입

---

### `void 반환 타입`

일부 함수는 어떤 값도 반환하지 않는데, 예를 들면 `return` 문이 없는 함수이거나, 값을 반환핮비 않는 `return` 문을 가진 함수일 경우이다.

타입 스크립트는 `void` 키워드를 사용해 값이 없는 반환타입을 확인할 수 있다.

```tsx
function longSong(song: string | undefined): void {
  if (!song) {
    return; // OK
  }

  console.log(song);

  return true;
  // Error : Boolean 타입은 void 타입에 할당할 수 없다.
}
```

함수 타입을 선언할 때 `void` 를 사용하면, 함수에서 반환되는 모든 값은 무시된다.

```tsx
let songLogger = (song : string) => void;

songLogger  = (song) => {
    console.log(songs)
}

songLogger("Heart of Glass"); // OK
```

**자바스크립트에서 함수는 실젯값이 반환되지 않으면 기본으로 모두 `undefined` 를 반환하지만 `void` 는 `undefined` 와 동일하지 않다.**

`**void** 타입은 반환타입이 무시된다는 것을 의미하고`, `**undefined** 는 반환되는 리터럴 값이다`.

undefined 를 포함하는 대신 void 타입의 값을 할당하려고 하면 타입 오류가 발생한다.

```tsx
function returnsVoid() {
  return;
}

let lazyValue: string | undefined;

lazyValue = returnsVoid();

// Error : void 타입은 string | undefined 에 할당 가능하지 않다.
```

`undefined` 와 `void` 를 구분해서 사용하면 매우 유용한데, 특히 `void` 를 반환하도록 선언된 타입 위치에 전달된 함수가 반환된 모든 값을 무시하도록 설정할 때 유용하다.

```tsx
const records: string[] = [];

function saveRecords(newRecords: string[]) {
  newRecords.forEach((record) => records.push(record));
}

saveRecords(['21', 'Come Over', 'The Bodyguard']);
```

- `자바스크립트의 **forEach 함수는 void**를 반환하지만, **map 함수는 새로운 배열타입**을 반환한다.`
- **`void** 타입은 자바스크립트가 아닌 함수의 반환타입을 선언하는데 사용하는 타입스크립트 키워드이다.`
- **`void** 타입은 함수의 반환값이 자체적으로 반환될 수 있는 값도 아니고, 사용하기 위한것도 아니다.`

---

### `never 반환 타입`

일부 함수는 값을 반환하지 않을 뿐만 아니라 반환할 생각도 전혀 없다.

`never` 반환 함수는 `의도적으로 항상 오류를 발생시키거나 무한루프를 실행시키는 함수이다.`

함수가 절대 반환하지 않도록 의도하려면 명시적으로 `never` 타입 애너테이션을 추가해 해당 함수를 호출한 후 모든 코드가 실행되지 않음을 나타낸다.

```tsx
function fail(message: string): never {
  throw new Error(`Invaricant failure : ${messgage}`);
}

function workWithUnsafeParam(param: unknown) {
  if (typeof param !== 'string') {
    fail(`param should be a string, not ${typeof param}`);
  }

  // param 의 타입은 string 이다.
  param.toUpperCase();
}
```

> **_`void 는 아무것도 반환하지 않는 함수를 위한 것이고, never 는 절대 반환하지 않는 함수를 위한 것이다.`_**

---

### `함수 오버로드`

일부 자바스크립트 함수는 선택적 매개변수와 나머지 매개변수만으로 표현할 수 없는 매우 다른 매개변수들로 호출될 수 있다. 이러한 함수는 `오버로드 시그니처` 라고 불리는 타입스크립트 구문으로 설명이 가능하다.

즉, 하나의 `구현 시그니처` 와 그 함수의 본문 아ㅠㅍ에 서로 다른 버전의 함수 이름, 매개변수, 반환 타입을 여러번 선언한다.

```tsx
function createDate(timestamp: number): Date;
function createDate(month: number, date: numebr, year: number): Date;
function createDate(monthOrTimestamp: number, day?: number, year?: number) {
  return day === undefined || year === undefined
    ? new Date(monthOrTimestamp)
    : new Date(year, monthOrTimestamp, day);
}

createDate(554223414); // OK
createDate(7, 27, 1987); // OK

createDate(4, 1);
// Error : 1 개 혹은 3개의 arguments 를 기대하는 함수 오버로드는 존재하지만,
// 2개의 arguments 를 기대하는 오버로드 함수는 존재하지 않음
```

타입스크립트를 컴파일해 자바스크립트로 출력하면, 다른 타입 시스템 구문 처럼 오버로드 시그니처도 지워진다.

위의 코드 스니펫의 함수는 다음과 같이 컴파일 된다.

```tsx
function createDate(monthOrTimestamp, day, year) {
  return day === undefined || year === undefined
    ? new Date(monthOrTimestamp)
    : new Date(year, monthOrTimestamp, day);
}
```

---

### `호출 시그니처 호환성`

오버로드된 함수의 구현에서 사용되는 구현 시그니처는 매개변수 타입과 반환 타입에 사용하는 것과 동일하다.

따라서, 함수의 오버로드 시그니처에 있는 반환타입과 각 매개변수는 구현 시그니처에 있는 동일한 인덱스의 매개변수에 할당할 수 있어야 합니다.

`구현 시그니처는 모든 오버로드 시그니처와 호환되어야 한다.`

```tsx
function foramt(data: string): string; // OK
function foramt(data: string, needle: string, haystack: string); // OK

function format(getData: () => string): string;

// Erorr : This overlaod signature is not compativle with its implementation signature

function format(data: string, needle?: string, haystack?: string) {
  return needle && haystack ? data.replace(needle, haystack) : data;
}
```

---

### 요약

- 타입 애너테이션으로 함수 매개변수 타입 선언하기
- 타입 시스템의 동작을 변경하기 위한 선택적 매개변수, 기본 매개변수, 나머지 매개변수 선언하기
- 타입 애너테이션으로 함수 반환 타입 선어하기
- void 타입으로 사용 가능한 값을 반환하지 않는 함수 알아보기
- never 타입으로 절대 반환하지 않는 함수 알아보기
- 함수 오버로드를 사용해서 다양한 함수 호출 시그니처 설명하기
