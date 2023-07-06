# 15 ) 타입 운영

---

> 이번장에서 사용되게 될 개념은 제네릭과 마찬가지로 과도하게 사용되면 읽기 어려울 수 있으니 유념하자.

## Table of Contents

---

## 매핑된 타입

---

타입스크립트는 다른 타입의 속성을 기반으로 새로운 타입을 생성하는 구문을 제공하는데, 이가 하나의 타입에서 다른 타입으로 `매핑 ( Mapping )` 한다. 타입스크립트의 `매핑된 타입 ( Mapped type )` 은 다른 타입을 가져와서 `**해당 타입의 각 속성에 대해 일부 작업을 수행**`하는 타입이다.

매핑된 타입은 키 집합의 각 키에 대한 새로운 속성을 만들어 새로운 타입을 생성한다.

인덱스 시그니처와 유사한 구문을 사용하지만, `[ i : string ]` 과 같이 `:` 를 사용한 정적 키 타입을 사용하는 대신

`[ K in OriginalType ]` 과 같이 `in` 을 사용해 `**다른 타입으로 부터 계산된 타입**`을 사용한다.

```tsx
type Animals = 'alligator' | 'baboon' | 'cat';

type AnimalCounts = {
  [K in Animals]: number;
};

// 다음과 같음

// {
//     alligator : number;
//     baboon : number;
//     cat : number
// }
```

존재하는 유니언 리터럴을 기반으로 하는 매핑된 타입은 큰 인터페이스를 `선언하는 공간을 절약`하는 편리한 방법이며, 타입은 다른 타입에 대해 작동하고, `멤버에서 제한자를 추가하거나 제거할 수 있을때 가장 유용해진다.`

---

### `타입에서 매핑된 타입`

일반적으로 매핑된 타입은 존재하는 타입에 `keyof` 연산자를 사용해 키를 가져오는 방식으로 작동한다.

존재하는 타입의 키를 매핑하도록 타입에 지시하면 `새로운 타입으로 매핑`한다.

```tsx
interface AnimalVariants {
  alligator: number;
  baboon: number;
  cat: string;
}

type AnimalCounts = {
  [K in keyof AnimalVariants]: number;
};

// 다음과 같음

// 새로운 타입으로 매핑 되었음.
// 원래 타입의 멤버에서 원래의 key 값을 사용하였음.

// type AnimalCounts = {
//     alligator: number;
//     baboon: number;
//     cat: number;
// }
```

이전 스니펫에서 K 로 명명된 keyof 에 매핑된 새로운 타입 키는 원래 타입의 키로 알려져 있다.

즉, `각 매핑된 타입 멤버 값은 동일한 키 아래에서 원래 타입의 해당 멤버 값을 참조할 수 있다.`

```tsx
interface BirdVariants {
  dove: string;
  eagle: boolean;
}

type NullableBirdVariants = {
  // BirdVariants 의 타입을 매핑해 유니온 null 을 추가해 nullable 하게 만들어줌.

  [K in keyof BirdVariants]: BirdVariants[K] | null;
};

// 다음과 같음

type NullableBirdVariants = {
  dove: string | null;
  eagle: boolean | null;
};
```

각 필드를 원본타입에서 임의의 수의 다른 방법으로 어렵게 복사하는 대신, 매핑 된 타입은 멤버 집합을 한 번 정의하고 필요한 만큼 여러번 새로운 버전을 다시 생성할 수 있다.

## 매핑된 타입과 시그니처

---

타입스크립트가 인터페이스 멤버를 함수로 선언하는 두가지 방법

- `member () : void 같은 **메서드 구문**` : 인터페이스의 멤버가 객체의 멤버로 호출되도록 의도된 함수 선언
- `member : () ⇒ void 같은 **속성 구문**` : 인터페이스의 멤버가 독립 실행형 함수와 같다고 선언

`하지만, 매핑된 타입은 객체 타입의 메서드와 속성 구문을 구분하지 않고, **메서드를 원래 타입의 속성으로 구분**한다.`

```tsx
interface Researcher {
  researchMethod(): void;
  researchProperty: () => void;
}

// 제네릭 T 에 객체 형태가 들어갈 것을 예상 가능.
type JustProperties<T> = {
  [K in keyof T]: T[K];
  // 		key : object[key]
};

type ResearcherProperties = JustProperties<Researcher>;

// 다음과 같음

type ResearcherProperties = {
  researchMethod: () => void;
  researchProperty: () => void;
};
```

대부분의 실용적인 타입스크립트 코드에서 메서드와 속성의 차이는 잘 나타나지 않고, 클래스 타입을 갖는 매핑된 타입을 실제로 사용하는 경우는 매우 드문 일이다.

---

### `제한자 변경`

매핑된 타입은 원래 타입의 멤버에 대해 접근 제어 제한자인 `readonly` 와 `?` 도 변경이 가능하다.

```tsx
interface Environmentalist {
  area: string;
  name: string;
}

// Mapped readonly
type ReadonlyEnvironmentalist = {
  readonly [K in keyof Environmentalist]: Environmentalist[K];
};

//. 다음과 같음

type ReadonlyEnvironmentalist = {
  readonly area: string;
  readonly name: string;
};

// Mapped optionally

type OptionalEnvironmentalist = {
  [K in keyof Environmentalist]?: Environmentalist[K];
};

// 다음과 같음

type OptionalEnvironmentalist = {
  area?: string | undefined;
  name?: string | undefined;
};

// Mapped optional readonly

type OptionalReadonlyEnvironmentalist = {
  [K in keyof ReadonlyEnvironmentalist]?: ReadonlyEnvironmentalist[K];
};

// 다음과 같음

type OptionalReadonlyEnvironmentalist = {
  readonly area?: string | undefined;
  readonly name?: string | undefined;
};
```

새로운 타입의 제한자 앞에 `-` 를 추가해 `제한자를 제거`할 수도 있다.

`readonly` 나 `?:` 를 작성하는 대신 `-readonly` 나 `-?:` 를 사용한다.

```tsx
interface Conservationist {
  name: string;
  catchphrase?: string;
  readonly born: number;
  readonly died?: number;
}

// readonly 제한자 제거

type WritableConservationist = {
  -readonly [K in keyof Conservationist]: Conservationist[K];
};

// 다음과 같음

type WritableConservationist = {
  name: string;
  catchphrase?: string | undefined;
  born: number;
  died?: number | undefined;
};

// ? 옵셔널 제거
type RequiredConservationist = {
  [K in keyof Conservationist]-?: Conservationist[K];
};

// 다음과 같음

type RequiredConservationist = {
  name: string;
  catchphrase: string;
  readonly born: number;
  readonly died: number;
};

// ? 옵셔널과 readonly 제한자 제거

type RequiredWritableConservationist = {
  -readonly [K in keyof Conservationist]-?: Conservationist[K];
};

// 다음과 같음

type RequiredWritableConservationist = {
  name: string;
  catchphrase: string;
  born: number;
  died: number;
};
```

---

### `제네릭 매핑된 타입`

매핑 된 타입은 제네릭과 결합해 단일 타입의 매핑을 다른 타입에서 재사용할 수 있을 때 완전한다.

매핑 된 타입은 매핑 된 타입 자체의 타입 매개변수를 모함해 `keyof 로 해당 스코프에 있는 모든 타입 이름에 접근`할 수 있다.

`제네릭 매핑된 타입`은 데이터가 애플리케이션을 통해 흐를때 데이터가 어떻게 변형되는지 나타낼때 유용하다.

예를들어, 애플리케이션 영역이 기존 타입의 값을 가져올 수 는 있지만, 데이터를 수정하는 것은 허용하지 않는 것이 좋다.

```tsx
type MakeReadonly<T> = {
  readonly [K in keyof T]: T[K];
};

interface Species {
  genus: string;
  name: string;
}

type ReadonlySpecies = MakeReadonly<Species>;

// 다음과 같음

type ReadonlySpecies = {
  readonly genus: string;
  readonly name: string;
};
```

개발자들이 일반적으로 표현해야 하는 또 다른 변환은 임의의 수의 인터페이스를 받고, 그 인터페이스의 완전히 채워진 인스턴스를 반환하는 함수이다.

```tsx
interface GenusData {
  family: string;
  name: string;
}

type MakeOptional<T> = {
  [K in keyof T]?: T[K];
};

type OptionalGenustData = MakeOptional<GenusData>;

// 다음과 같음

type OptionalGenustData = {
  family?: string | undefined;
  name?: string | undefined;
};

function createGenusData(overrides?: MakeOptional<GenusData>): GenusData {
  return {
    family: 'unknown',
    name: 'unknown',
    ...overrides,
  };
}

// Utility 타입인 Partial<T> 와 동작이 같다.
function createGenusDataPartially(overrides?: Partial<GenusData>): GenusData {
  return {
    family: 'unknown',
    name: 'unknown',
    ...overrides,
  };
}

// 인자로 들어갈 수 있는 객체의 속성이 optional 한지를 보여주는 것 같음
const newGenusData = createGenusData({ name: 'lee' });

const partialGenusData = createGenusDataPartially({ family: '가족' });

console.log(newGenusData);
```

```tsx
// 컴파일된 JS 코드가 동일함.

function createGenusData(overrides) {
  return Object.assign({ family: 'unknown', name: 'unknown' }, overrides);
}

function createGenusDataPartially(overrides) {
  return Object.assign({ family: 'unknown', name: 'unknown' }, overrides);
}
```

타입 스크립트는 제네릭 매핑된 타입을 즉시 사용할 수 있는 `Partial<T>` 라는 유틸리티 타입을 제공한다.

---

### `조건부 타입`

타입스크립트 `타입 시스템은 논리 프로그래밍 언어` 의 한 예이다.

타입스크립트의 타입 시스템은 이전 타입에 대한 논리적인 검사를 바탕으로 새로운 구성을 생성한다.

`조건부 타입 ( conditional type )`의 개념은 기존 타입을 바탕으로 두 가지 가능한 타입중 하나로 확인되는 개념이다.

```tsx
LeftType extends RightType ? IfTrue : IfFalse
```

`**조건부 타입에서 논리적 검사는 항상 extends 의 왼쪽 타입이 오른쪽 타입이 되는지 또는 할당 가능한지 여부에 있다.**`

```tsx
// 타입은 false
type CheckStringAgainstNumber = string extends number ? true : false;
```

각각은 어떤 타입을 취하고 `두가지 가능한 결과중 하나`를 얻는다.

---

### `제네릭 조건부 타입`

조건부 타입은 조건부 타입 자체의 타입 매개변수를 포함한 해당 스코프에서 모든 타입 이름을 확인 할 수 있다.

즉, 모든 다른 타입을 기반으로 새로운 타입을 생성하기 위해 재사용 가능한 제네릭 타입을 작성할 수 있다.

```tsx
type CheckStringAgainstNumber<T> = T extends number ? true : false;

// string !== number
// 타입은 false
type CheckString = CheckStringAgainstNumber<'parakeet'>;

// typeof 1891 === 'number'
// 타입은 true
type CheckString = CheckStringAgainstNumber<1891>;

// number === number
// 타입은 true
type CheckString = CheckStringAgainstNumber<number>;
```

```tsx
// 바뀌어야함

type CallableSettings<T> =
  // 왼쪽 타입이 오른쪽 타입에 할당 가능하거나 |  같냐 를 검증
  T extends () => any ? T : () => T;

// 타입은  () => number[]
// any 타입에 () => number[] 이 할당가능하지 않기 때문에 T 그대로를 내보내 준다.
type GetNumbersSetting = CallableSettings<() => number[]>;

// 타입은 () => string
// any 타입은 top 타입 이므로 string 타입이 any 타입에 할당 가능해 () => T 로 변환되어서 내보내 준다.
type StringSetting = CallableSettings<string>;
```

조건부 타입은 객체 멤버 검색 구문을 사용해서 제공된 타입의 멤버에 접근할 수 있고, `extends` 절과 결과 타입에서 그 정보를 사용할 수 있다.

자바스크립트 라이브러리에서 사용하는 패턴 중 조건부 제네릭 타입에도 적합한 한 가지 패턴은 함수에 제공된 옵션 객체를 기반으로 함수의 반환 타입을 변경하는 것이다.

```tsx
interface QueryOptions {
  throwIfNotFound: boolean;
}

type QueryResult<Options extends QueryOptions> =
  // 인자로 throwIfNotFound 를 옵셔널 하게 받아 value 가 true 이면 string 주어지지 않거나 false 이면 string | undefined
  Options['throwIfNotFound'] extends true ? string : string | undefined;

declare function retrieve<Options extends QueryOptions>(
  key: string,
  optiosn?: Options
): Promise<QueryResult<Options>>;

async function executes() {
  // 타입은 string | undefined
  // throwIfNotFound 가 주어지지 않았기 때문에 string | undefined
  const birute = await retrieve('Biruté Galdikas');

  // 타입은 string | undefined   string | undefined
  // Math.random() > 0.5 에 의해 throwIfNotFound : false 가능성을 내재 하고 있으므로, 완전한 true가 아니기 때문에.
  const jane = await retrieve('Jane Goodal', {
    throwIfNotFound: Math.random() > 0.5,
  });

  // 타입은 string
  // throwIfNotFound 가 true 이기 때문에 원형의 string 타입
  const dian = await retrieve('Dian Fossey', { throwIfNotFound: true });

  return Promise.all([birute, jane, dian]);
}

executes();
```

조건부 타입을 제네릭 타입 매개변수와 결합하면 retrieve 함수는 프로그램의 제어 흐름을 어떻게 변경할 것인지 타입 시스템에 더 정확히 알릴 수 있다.

---

### `타입 분산`

조건부 타입은 유니언에 `분산 ( distribute )` 된다.

`결과 타입은 각 구성 요소 ( 유니언 타입 안의 타입 ) 에 조건부 타입을 적용하는 유니언이 됨을 의미`

즉, `ConditionalType< T | U > 는 ConditionalType<T> | ConditionalType<U>` 와 같다.

```tsx
type ArrayifyUnlessString<T> = T extends string ? T : T[];

// 타입은 stirng | number[]
type HalfArrayfied = ArrayifyUnlessString<string | number>;

// 아래와 같이 해석할 수 있음.
type HalfArrayfied =
  | ArrayifyUnlessString<string>
  | ArrayifyUnlessString<number>;

// 첫번째 유니온의 경우에 string 에 할당 가능하므로 그대로 T 를 반환
// 두번째 유니온 number 타입은 string에 할당 가능하지 않으므로 number[] 를 반환
```

조건부 타입은 전체 유니언 타입이 아니라 `유니언의 각 구성 요소에 로직을 적용`한다.

---

### `유추된 타입`

제공도니 타입의 멤버에 접근하는 것은 타입의 멤버로 저장된 정보에 대해서는 잘 작동하지만, 함수 매개변수 또는 반환 타입과 같은 다른 정보에 대해서는 알 수 없다.

조건부 타입은 extends 절에 infer 키워드를 사용해 조건의 임의의 부분에 접근한다.

extends 절에 `타입에 대한 infer 키워드와 새 이름을 배치하면 조건부 타입이 true 인 경우에 새로운 타입을 사용`함을 알 수 잇다.

```tsx
// 이 부분은 정확하지 않음.

type ArrayItems<T> = T extends (infer Item)[] ? Item : T;

// 타입은 string
// string 타입이 (infer Item)[] 에 충족되지 않아서 T 인 string 타입을 반환
type StringItem = ArrayItems<string>;

// 타입은 string
// string[] 타입은 (infer Item)[]에 충족함으로 Item 타입인 string 을 반환
type StringArrayItem = ArrayItems<string[]>;

// 타입은 string[]
// string[][] 타입은 (infer string[])[] 으로 해석하면 item 은 string[] 이 되어 반환된다.
type String2DItems = ArrayItems<string[][]>;
```

유추된 타입은 재귀적 조건부 타입을 생성하는데에도 사용할 수 있다.

```tsx
type ArrayItemsRecursive<T> = T extends (infer Item)[]
  ? ArrayItemsRecursive<Item>
  : T;

// 타입은 string
// 이전과 마찬가지로 string 이 (infer Item)[] 에 할당가능하지 않으므로 T 인 string 반환
type StringItem = ArrayItemsRecursive<string>;

// 타입은 String
// string[] 는 (infer Item)[] 에 충족되어 재귀적으로 ArrayItemsRecursive<string> 이 제네릭으로 들어가는 것과 같음
// 반환은 T 인 string
type StringArrayItem = ArrayItemsRecursive<string[]>;

// 타입은 string
// 위와 마찬가지로 string[] 와 재귀 반환 값인 T 인 string 이 반환
type String2DItem = ArrayItemsRecursive<string[][]>;

// 타입은 string
// 재귀적으로 배열이 깊어져도 불구하고 결국 T 인 string 을 반환
type StringXDItem = ArrayItemsRecursive<string[][][][][][]>;
```

제네릭 타입이 재귀적일 수 있는 기능을 통해 여기에서 배열의 요소 타입을 검색하는 것과 같은 변경 사항을 계속 적용할 수 있도록 한다.

---

### `매핑된 조건부 타입`

매핑된 타입은 기존 타입의 모든 멤버에 변경 사항을 적용하고, 조건부 타입은 하나의 기존 타입에 변경사항을 적용한다.

```tsx
type MakeAllMemversFunctions<T> = {
  // value 값이 함수로 들어왔을 경우에는 value 함수 그대로 반환
  // 함수에 충족하지 않는 경우는 함수 형태로 변환해서 반환

  [K in keyof T]: T[K] extends (...args: any[]) => any ? T[K] : () => T[K];
};

type MemberFunctions = MakeAllMemversFunctions<{
  alreadyFunction: () => string;
  notYetFunction: number;
}>;

// 다음과 같음

type MemberFunctions = {
  alreadyFunction: () => string;
  notYetFunction: () => number;
};
```

---

### `never`

`객체` 에서 `never` 와 `bottom` 타입을 보면 이들은 `가능한 값을 가질 수 없고, 접근 할 수 없음을 의미`

올바른 위치에 `never` 타입 애너테이션을 추가하면 타입스크립트가 이전 런타임 예제 코드 뿐만 아니라 타입 시스템에서 맞지 않는 코드 경로를 좀더 `공격적으로 탐지`한다.

---

### `never 와 교차, 유니언 타입`

- `**교차 타입** ( & ) 에 있는 **never 는 교차 타입을 never** 로 만든다.`
- `**유니언 타입** ( | ) 에 있는 **never 는 무시**된다.`

```tsx
// 타입은 never
type NeverIntersection = never & string;

// 타입은 string
type NeverUnion = never | string;
```

특히 유니언 타입에서 never 가 무시되는 동작은 조건부 타입과 매핑된 타입에서 값을 필터링 하는데 유용하다.

---

### `never 와 조건부 타입`

제네릭 조건부 타입은 일반적으로 `유니언에서 타입을 필터링 하기 위해 never 를 사용`

```tsx
type OnlyStrings<T> = T extends string ? T : never;

//. 타입은 'red' | 'blue'

// string 타입에 할당 가능하지 않는 타입들은 never 타입에서 필터링된다.
type RedOrBlue = OnlyStrings<'red' | 'blue' | 0 | false>;
```

never 또한 제네릭 타입에 대한 타입 유틸리티를 만들 때 유추된 조건부 타입과 결합된다.

`**infer가 있는 타입 추론은 조건부 타입이 true 가 되어야 하므로 false 인 경우를 절대 사용하지 않아야 한다.**`

`**이때 never 가 적용 가능하다**`

```tsx
// infer 이 들어간 조건부 타입에 never 가 들어감으로 함수의 첫번째 매개변수 타입 추출 가능

type FirstParameter<T extends (...args: any[]) => any> = T extends (
  arg: infer Arg
) => any
  ? Arg
  : never;

// 타입은 string
// 인자로 (arg0 : string) => void 가 충족이 되었기 때문에 그 arg 의 타입인 string 을 반환
type GetsString = FirstParameter<(arg0: string) => void>;
```

---

### `never 와 매핑된 타입`

유니언에서 never 의 동작은 `매핑된 타입에서 멤버를 필터링` 할때도 유용하다.

- `유니언에서 never 는 무시된다.`
- `매핑된 타입은 타입의 멤버를 매핑할 수 있다.`
- `조건부 타입은 조건이 충족되는 경우 타입을 never 로 변환하는데 사용할 수 있다.`

`**세가지 기능을 사용하면 원래 타입의 각 멤버를 원래 키 또는 never 타입으로 변경하는 매핑된 타입을 만들 수 있다.` ( 조건에 충족하지 않다면 never 타입으로 변경 )\*\*

```tsx
type OnlyStringProperties<T> = {
  // string이 아닌 모든 속성을 필터링 한 다음 [keyof T] 를 반환
  // (value 가 string 인 key 를 반환)
  [K in keyof T]: T[K] extends string ? K : never;
}[keyof T];

interface AllEventData {
  participants: string[];
  location: string;
  name: string;
  year: number;
}

// 타입은 'location' | 'name'
type EventDataWithNever = AllPropertiesWithNever<AllEventData>;

type AllPropertiesWithNever<T> = {
  // value 값이 string에 할당 가능하지 않는 key 들은 never 타입을 할당
  [K in keyof T]: T[K] extends string ? K : never;
};

// 다음과 같음
type EventDataWithNever = {
  participants: never;
  location: 'location';
  name: 'name';
  year: never;
};
```

---

### `템플릿 리터럴 타입`

문자열값을 입력하기 위한 두가지 전략

- `**원시 string 타입** : 값이 세상의 모든 문자열이 될 수 있는 경우`
- `**“” 와 “abc” 같은 리터럴 타입** : 값이 오직 한가지 타입만 될 수 있는 경우`

경우에 따라 일부 문자열이 패턴과 일치함을 나타내고 싶을때 사용한다.

템플릿 리터럴 타임은 템플릿 리터럴 문자열 처럼 보이지만 추정할 수 있는 `원시타입` 또는 `원시 타입 유나언`이 있다.

```tsx
type Greeting = `Hello${string}`;

// Hello 만 충족되면 이후에 어떤 문자열이 와도 충족된다.
let mathces: Greeting = 'Hello, world!';

// Greetings 타입에 충족 되지 않음.
let ourOfOrder: Greeting = 'World! Hello1';

// Greetings 타입에 충족 되지 않음.
let missingAltogether: Greeting = 'Hi';
```

```tsx
type Brightness = 'dark' | 'light';
type Color = 'blue' | 'red';

// 가능한 타입은 "dark-blue" | "dark-red" | "light-blue" | "light-red"
type BrightnessAndColor = `${Brightness}-${Color}`;

let colorOk: BrightnessAndColor = 'dark-blue'; // OK

// 할당 가능하지 않음
let colorWrongStart: BrightnessAndColor = 'medium-blue';

// 할당 가능하지 않음
let colorWrongEnd: BrightnessAndColor = 'light-green';
```

---

### `고유 문자열 조작 타입`

타입 스크립트는 문자열을 가져와 문자열의 일부 조작을 적용하는 고유 제네릭 유틸리티 타입을 제공한다.

- `**Uppercase** : 문자열 리터럴 타입을 대문자로 변환`
- `**Lowercase** : 문자열 리털럴 타입을 소문자로 변환`
- `**Capitalize** : 문자열 리터럴 타입의 **첫 번째 문자**를 대문자로 변환`
- `**Uncapitalize** : 문자열 리터럴 타입의 **첫 번째 문자**를 소문자로 변환`

```tsx
// 타입은 Hello.
type FormalGreeting = Capitalize<'hello.'>;
```

---

### `템플릿 리터럴 키`

템플릿 리터럴 타입은 원시 문자열 타입과 문자열 리터럴 사이의 중간 지점이므로 여전히 문자열이다.

```tsx
type DataKey = 'location' | 'name' | 'year';

type ExistenceChecks = {
    [ K in `check${Capitalize<DataKey>}`] : () => boolean;
}

//. 다음과 같음

type ExistenceChecks = {
    checkLocation: () => boolean;
    checkName: () => boolean;
    checkYear: () => boolean;
}

function checkExistence (checks : ExistenceChecks) {
    checks.checkLocation();  // OK
    checks.checkName();  // OK

    ~~checks.checkWrong()~~ // 존재하지 않는 key 값
}
```

---

### `매핑된 타입 키 다시 매핑하기`

매핑된 타입에서 인덱스 시그니처에 대한 템플릿 리터럴 타입 다음에 `as 키워드` 를 배치하면 결과 타입의 키는 템플릿 리터럴 타입과 일치하도록 변경된다.

```tsx
interface DataEntry<T> {
  key: T;
  value: string;
}

type DataKey = 'location' | 'name' | 'year';

type DataEntryGetters = {
  // get + key 값의 새로운 key 를 가진 () => DataEntry<key> 를 가진 객체 타입을 반환
  [K in DataKey as `get${Capitalize<K>}`]: () => DataEntry<K>;
};

// 다음과 같음

type DataEntryGetters = {
  getLocation: () => DataEntry<'location'>;
  getName: () => DataEntry<'name'>;
  getYear: () => DataEntry<'year'>;
};
```

`keyof typeof` 키워드를 사용해서 객체의 타입에서 매핑된 타입을 만드는 것도 가능하다

```
const config = {
    location : 'unknown',
    name : 'anonymous',
    year : 0
};

type LazyValues = {
    [ K in keyof typeof config as `${K}Lazy`] : () => Promise<typeof config[K]>
}

// 다음과 같음

// type LazyValues = {
//     locationLazy: () => Promise<string>;
//     nameLazy: () => Promise<string>;
//     yearLazy: () => Promise<number>;
// }

async function withLazyValues(configGetter : LazyValues) {
    await configGetter.locationLazy();

    // 존재하지 않는 속성
    ~~await configGetter.missingLazy();~~

}
```

자바스크립트에서 객체 키는 `string` 또는 `Symbol` 이 될 수 있고 `Symbol 키는 원시타입이 아니므로 템플릿 리터럴 타입으로 사용될 수 없다.`

```tsx
type TurnIntogettersDirect<T> = {
  // K 의 타입은 Symbol
  // 원시타입에 Symbol 타입이 할당가능하지 않다.
  [K in keyof T as `get${K}`]: () => T[K];
};
```

`**이러한 제한 사항을 피하기 위해  string 과 교차타입 ( & ) 을 사용하여 문자열이 될 수 있는 타입만 사용하도록 강제한다.**`
