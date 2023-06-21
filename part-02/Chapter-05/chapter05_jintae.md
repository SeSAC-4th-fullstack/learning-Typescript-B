# Chapter 05

## 5.1 함수 매개변수

타입스크립트에서 함수의 매개변수는 타입 애너테이션을 통하여 명시할 수 있다. 명시하지 않는 경우는 그 파람미터를 any타입으로 간주한다.

```tsx
function sing(song: string) { // 매개변수 song에 타입 애너테이션 작성
	console.log(`Singing: ${song}`);
}
```

타입스크립트는 함수에 선언된 모든 매개변수가 필수라고 가정한다. 매개변수가 함수 선언부에서 더 많거나 적으면 타입 오류를 내보낸다. 

그럼에도 불구하고, 자바스크립트로 변환이 가능하다. 다만 예기치 않은 동작이 있을 수 있다.

```tsx
function singTwo(first: string, second: string) { // 함수 선언부
	console.log(`${first} / ${second}`);
}

singTwo("애국가", "두 사랑"); // OK
singTwo("거북이"); // Error
singTwo("비행기", "Non Stop", "Pretty Boy") // Error
```

타입스크립트에서, 타입 애너테이션 앞에 ?를 추가하여 해당 매개변수를 선택적으로 사용할 수 있다. 

선택적 매개변수의 타입은 항상 | undefined 가 추가 되어있다. 그래서 선택적 매개변수는 암묵적으로 undefined가 될 수 있다. 

*선택적 매개변수는 항상 함수 선언부의 마지막에 위치해야 한다.*

이와 달리 선택적 매개변수가 아니고, 명시적으로 undefined 타입이 작성된 경우, 반드시 undefined 값을 인자로 작성해야 한다.

```tsx
// ?를 활용한 선택적 매개변수 예시

function announceSong(song: string, singer?: string) { // ?를 활용한 선택적 매개변수
	console.log(`Song: ${song}`);

	if(singer) {
		console.log(`Singer: ${singer}`);
	}
} // 함수 선언부 끝

announceSong("Forever"); // OK
announceSong("Forever", undefined); // OK
announceSong("Jimmy Cooks", "Drake"); // OK
```

```tsx
// undefined 타입이 명시된 매개변수 예시

function announceSongBy(song: string, singer: string | undefined) { /*...*/}

announceSongBy("Teddy Bear", "STAYC"); // OK
announceSongBy("Teddy Bear", undefined); // OK
announceSongBy("Teddy Bear"); // Error
```

자바스크립트에서 선택적 매개변수를 선언할 때, =를 활용하여 기본값을 제공한다. 그래서, 타입스크립트에서는 암묵적으로 함수 내부에 | undefined 유니언 타입을 추가하여 선택적 매개변수가 선택적이도록 뒷받침한다.

매개변수에 기본값이 있고 타입 애너테이션이 없는 경우, 타입스크립트는 기본값을 통해 타입을 유추한다.

```tsx
// 기본값을 갖는 선택적 매개변수 예시

function rateSong(song: string, rating = 0) {
	console.log(`${song} gets ${rating}/5 stars!`);
}

rateSong("Photograph"); // OK
rateSong("Set Fire to the Rain", 5); // OK
rateSong("Set Fire to the Rain", undefined); // OK
rateSong("At Last", "100"); // Error : "100" 은 number | undefined 타입이 아닌..
```

임의의 수만큼 인자를 갖는 함수를 호출하기 위해서, …스프레드 연산자를 함수 선언의 마지막 매개변수에 작성하여 나머지 매개변수를 입력 받을 수 있다.

단, 타입스크립트에서는 나머지 매개변수가 단일 타입의 배열이라는 것을 명시하기 위해 []을 추가해야 한다.

```tsx
// 나머지 매개변수 예시

function singAllTheSong(singer: string, ...songs: string[]) {
	for(const song of songs) {
		console.log(`${song} by ${singer}`);
	}
}

singAllTheSong("Alicia Keys"); // OK
singAllTheSong("Alicia Keys", "New York", "LA", "2002"); // OK 
singAllTheSong("Alicia Keys", 2002); // Error
```

## 5.2 반환 타입

타입스크립트는, 함수의 반환 타입을, *가능한 모든 반환 타입의 조합*으로 유추한다.

```tsx
// 타입: (songs: string[]) => number
function singSongs(songs: string[]) {
	for(const song of songs) {
		console.log(`${song}`);
	}
	return songs.length;
}

// 타입: (songs: string[], index: number) => string | undefined
function getSongAt(songs: string[], index: number) {
	return index < songs.length ? songs[index] : undefined;
}
```

보통의 경우 함수의 반환 타입을 명시하지 않는 것이 좋다. 하지만 다음 3가지의 경우 사용하는 것이 유용하다.

1. 함수가 항상 동일한 타입의 값을 반환하도록 강제할 때
2. 재귀 함수의 반환 타입을 통해 타입을 유추하는 것을 거부할 때
3. 매우 큰 프로젝트에서 타입 검사 속도를 높이고 싶을 때

함수의 반환 타입은 매개변수 목록이 끝나는 ) 뒤에 타입 애너테이션을 통하여 선언할 수 있다. 화살표 함수의 경우 ⇒ 앞에 배치된다.

```tsx
// 함수 선언의 반환 타입 예시
function singSongRecursive(songs: string[], count = 0): number {
	console.log(songs, count);
	return songs.length ? singSongRecursive(songs.slice(1), count + 1) : count;
}
console.log(singSongRecursive(["A", "B", "C"]));
// ["A", "B", "C"] 0
// ["B", "C"] 1
// ["C"] 2
// [] 3
// 3

// 화살표 함수의 반환 타입 예시
const singSongRecursive = (songs: string[], count = 0): number => {
	return songs.length ? singSongRecursive(songs.slice(1), count + 1) : count;
};
console.log(singSongRecursive); // 3
```

## 5.3 함수 타입

함수를 값으로 전달할 수 있다. 그러므로 함수를 가지기 위한 변수의 타입을 선언하는 방법이 필요하다. 이를 함수 타입이라고 말한다. *cf. 함수 표현식*

함수 타입 구문은 *화살표 함수와 유사하지만 함수 본문 대신 타입이 있다.* 

```tsx
// 매개변수가 없고, string 타입을 반환하는 함수
let nothingInGivesString: () => string;

// 매개변수 songs와 선택적 매개변수 count를 갖고, number 타입을 반환하는 함수
let singSongRecursive: (songs: string[], count?: number) => number;
```

함수 타입은 콜백 매개변수를 설명하는 데 자주 사용된다. 

아래의 예시처럼 runOnSongs 함수의 콜백 매개변수는 index:number 를 받고 string을 반환한다. 여기서 다른 형태의 콜백 매개변수를 넣으면 타입스크립트는 오류를 내보낸다.

```tsx
const songs = ["Juice", "Shake It Off", "What's up"];

function runOnSongs(getSongAt: (index: number) => string) {
	for (let i = 0; i < songs.length; i += 1) {
		console.log(getSongAt(i));
	}
}

function getSongAt(index: number) {
	return `${songs[index]}`;
}

function logSong(song: string) {
	return `${song}`;
}

runOnSongs(getSongAt) // OK
runOnSongs(logSong) // Error
// Error: Argument of type '(song: string) => string' is not assignable to 
// parameter of type '(index: number) => string'.
```

함수 타입의 괄호는 함수 반환 타입의 혼동을 예방할 수 있다.

```tsx
// 함수 반환 타입이 string | undefined 같은 유니언 타입인 경우
let returnsStringOrUndefined: () => string | undefined;

// 함수 반환 타입이 string 이거나 undefined인 경우
let maybeReturnsString: (() => string) | undefined;
```

타입스크립트는 선언된 타입의 위치에 제공된 함수의 매개변수 타입을 유추할 수 있다. 

함수로 전달된 인수의 매개변수에 대해서도 매개변수 추론을 잘 한다.

```tsx
const songs = ["Juice", "Shake It Off", "What's up"];

songs.forEach(
	(song, index) => { console.log(`${song} is at index ${index}`); }
);
```

함수 타입 역시 타입 얼라이어스를 활용하여 별칭을 줄 수 있다.

```tsx
type StringToNumber = (input: string) => number;

let stringToNumber: StringToNumber;

stringToNumber = (input) => input.length; // OK
stringToNumber = (input) => input.toUpperCase(); 
//Error: 반환 타입이 number로 작성되었기 때문이다.
```

## 5.4 그 외 반환 타입

> void와 never 타입
> 

void 타입은 반환 하는 값이 없을 때 사용하고, never 타입은 반환 자체를 하지 않을 때 사용한다. 

never 타입은 의도적으로 오류 메세지를 출력하거나, 무한 루프에 빠뜨릴 때 주로 사용한다.

```tsx
// void 타입 예시

let songLogger: (song: string) => void;

songLogger = (song) => {
	console.log(`${song}`);
};

songLogger("IDOL"); // IDOL
```

```tsx
// void 타입 예시

const records: string[] = [];

function saveRecords(newRecords: string[]) {
	newRecords.forEach(record => records.push(record));
}

saveRecords(["21", "Come On", "The Bodyguard"]);
```

```tsx
// never 타입 예시

function fail(message: string): never {
	throw new Error(`Invariant failure: ${message}`);
}
```

## 5.5 함수 오버로드

자바스크립트의 함수는 매우 다른 매개변수들로 호출될 수 있다. 이러한 함수는 타입스크립트에서 오버로드 시그니처 구문을 통해 설명할 수 있다. 

하나의 최종 구현 시그니처를 두고, 그 함수의 본문 앞에 서로 다른 버전의 함수 이름, 매개변수, 반환 타입을 여러 번 선언한다.

오버로드 시그니처를 통해 구문 오류를 확인하고, 구현 시그니처를 통해 내부 로직 오류를 확인한다.

```tsx
// 함수 오버로드 예시

// 오버로드 시그니처
function createDate(timestamp: number): Date;
function createDate(month: number, day: number, year: number): Date;

// 구현 시그니처
function createDate(monthOrTimestamp: number, day?: number, year?: number) {
	return day === undefined || year === undefined 
		? new Date(monthOrTimestamp)
		: new Date(year, monthOrTimestamp, day);
}

createDate(554356800); // OK
createDate(7, 27, 1987); // OK
createDate(4, 1); // Error : No overload expects 2 arguments,
// but overloads do exist that expect either 1 or 3 argumetns.
```