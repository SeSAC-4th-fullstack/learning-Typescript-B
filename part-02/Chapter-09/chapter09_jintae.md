# Chapter 09

## 9.1 top 타입

top 타입은 시스템에서 가능한 모든 값을 나타내는 타입이다. 즉, 모든 타입은 top 타입에 할당 가능하다. 

예를 들자면, any 타입 그리고 unknown 타입이 있다. 이 둘은 모든 타입을 표현할 수 있기 때문에 top 타입이다. 이 둘의 주요한 차이점은, unknown 타입이, 타입의 값을 훨씬 더 제한적으로 다룬다는 점이다.

any 타입은 고의적으로 타입 검사를 수행하지 않는다. unknown 타입은 top 타입이 아니면 할당할 수 없고, unknown 타입 값의 속성에 직접 접근할 수 없다.

```tsx
// any 타입 예시
function greetComedian(name: any) {
	console.log(`Hello ${name.toUpperCase()}`); // 타입 에러 없음.
}

greetComedian( {name: "Bea Arthur"} ); // 런타임 에러 발생.
```

```tsx
// unknown 타입 예시
function greetComedian(name: unknown) {
	console.log(`Hello ${name.toUpperCase()}`); // 타입 에러 발생
}
```

## 9.2 타입 서술어

타입 내로잉을 위한 타입 가드는, 로직을 함수로 감싸게 되면 타입을 좁힐 수 없다.

```tsx
function isNumberOrString(value: unknown) {
    return ['number', 'string'].includes(typeof value);
}

function logValueIfExists(value: number | string | null | undefined) {
    if(isNumberOrString(value)) {
        value.toString(); // Error: 'value' is possibly 'null' or 'undefined'
    }
    else {
        console.log("Value does not exist: ", value); 
				// value 타입: number | string | null | undefined
    }
}
```

그렇기 때문에, 타입스크립트에는 인수가 특정 타입인지 여부를 나타내기 위해 *boolean 값을 반환하는 함수*를 위한 타입 서술어를 지원한다.

키워드 is를 활용하여 표현할 수 있다.

```tsx
function isNumberOrString(value: unknown): value is number | string {
    return ['number', 'string'].includes(typeof value);
}

function logValueIfExists(value: number | string | null | undefined) {
    if(isNumberOrString(value)) {
        value.toString(); // 타입: number | string
    }
    else {
        console.log("Value does not exist: ", value); // 타입: null | undefined
    }
}
```

*주의: 타입 서술어는 false 조건에서도 타입을 좁히기 때문에 예상하지 못한 동작이 발생할 수 있다.*

```tsx
function isLongString(input: string | undefined): input is string {
	return !!(input && input.length >= 7);
}

function workWithText(text: string | undefined) {
	if(isLongString(text)) {
		console.log("Long text: ", text); // text 타입: string
	}
	else {
		console.log("Short text: ", text?.length); // Error..
		// text 타입: undefined
	}
}
```

## 9.3 타입 연산자

- keyof: 타입에 허용된 모든 키를 반환
- typeof: 값의 타입을 반환

### keyof

getRatingKeyof의 매개변수 key는 인터페이스 Ratings가 갖는 key들의 유니언 타입을 생성한다.

```tsx
interface Ratings {
    audience: number;
    critics: number;
}

function getRatingKeyof(ratings: Ratings, key: keyof Ratings): number {
    return ratings[key];
}

const ratings: Ratings = { audience: 66, critics: 99 };

getRatingKeyof(ratings, 'audience'); // OK
getRatingKeyof(ratings, 'not valid'); // Error: 'not valid'는 
// keyof Ratings 타입에 할당할 수 없음.
```

### typeof

다음 adaptation은 original의 타입으로 선언되었다.

```tsx
const original = {
    medium: "movie",
    title: "Mean Girls",
};

let adaptation: typeof original;

if(Math.random() > 0.5) {
    adaptation = { ...original };
    adaptation = { medium: "play", title: "untitled" };
}
else {
		// medium: "play", title: "Mean Girls"
    adaptation = { ...original, medium: "play" };

		// Error: 'number' type is not assignable to type 'string'
    adaptation = { medium: "series", title: 2 };
}
```

keyof와 typeof를 결합해서 사용하면 명시적 인터페이스 타입이 없는 객체에, 허용된 키를 나타내는 타입에 대한 코드를 편리하게 사용할 수 있다.

```tsx
const ratings = {
	imdb: 8.4,
	metacritic: 82,
};

function logRating(key: keyof typeof ratings) {
	console.log(ratings[key]);
}

logRating("imdb"); // OK
logRating("invalid"); // Error:
// Argument of type '"invalid"' is not assignable
// to parameter of type '"imdb" | "metacritic"'
```

## 9.4 타입 어서션

값의 타입을 재정의 하는 구문이다. 

타입 어서션은 타입 검사 일부를 건너뛴다.

as 키워드를 사용한다.

```tsx
const rawData = '["grace", "frankie"]';

// JSON.parse()는 의도적으로 top 타입인 any를 반환한다.

JSON.parse(rawData); // 타입: any
JSON.parse(rawData) as string[]; // 타입: string[]
JSON.parse(rawData) as [string, string]; // 타입: [string, string]
JSON.parse(rawData) as ["grace", "frankie"]; // 타입: ["grace", "frankie"]
```

null 또는 undefined를 제거할 때 타입 어서션은 !를 사용한다.

```tsx
// 타입: undefined | Date
let maybeDate = Math.random() > 0.5 ? undefined : new Date();

maybeDate as Date; // Date 타입으로 취급
maybeDate!; // undefined를 제외한 타입으로 취급. 즉, Date 타입으로 취급
```

완전히 상관 없는 타입은, 타입스크립트가 오류를 감지하고 알려준다. 완전히 상관 없는 타입은 이중 타입 어서션을 거쳐서 재정의 할 수 있다.

```tsx
let val = "Stella" as number; // 타입 에러.

let myVal = "1997" as unknown as number // OK
```

## 9.5 const 어서션

as const 구문을 사용한다. const 어서션은 모든 값을 상수로 취급해야 함을 나타낼 때 유용하다.

- const 어서션 규칙
    - 배열 → 읽기 전용 튜플
    - 리터럴 → 리터럴
    - 객체 속성 → 읽기 전용 속성

```tsx
[0, " "]; // 타입: (number | string)[]

[0, " "] as const; // 타입: readonly [0, " "]
```

```tsx
const getName = () => "Maria"; // 타입: () => string

const getName = () => "Maria" as const; // 타입: () => "Maria"
```

```tsx
function describePreference(preference: "maybe" | "no" | "yes") {
    switch(preference) {
        case "maybe":
            return "I supoose...";
        case "no":
            return "No thanks";
        case "yes":
            return "Yes Please!";
    }
}

const preferenceMutable = {
    movie: "maybe",
    standup: "yes",
};

describePreference(preferenceMutable.movie) // Error. 리터럴 타입 위배
preferenceMutable.movie = "no" // OK. 수정 가능.

const preferenceReadonly = {
    movie: "maybe",
    standup: "yes",
} as const;

describePreference(preferenceReadonly.movie); // OK. 리터럴 타입 일치
preferenceReadonly.movie = "no" // Error. 수정 불가능.
```