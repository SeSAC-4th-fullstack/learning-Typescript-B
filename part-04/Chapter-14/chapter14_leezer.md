# 14 ) 구문 확장

---

오늘날, 타입스크립트와 같은 상위 집합 언어에 특정 새로운 런타임 기능으로 자바스크립트 구문을 확장하는 방식은 다음과 같은 이유로 인해 나쁜 사례로 간주한다.

- `런타임 구문 확장이 최신버전 자바스크립트의 새로운 구문과 충돌할 수 있다는 점이 가장 중요하다.`
- `언어를 처음 접하는 프로그래머가 자바스크립트가 끝나는 곳과 다른 언어가 시작하는 곳을 이해하기 어렵게 만든다.`
- `상위 집합 언어 코드를 ㅅ용하고 자바스크립트를 내보내는 트랜스파일러의 복잡성을 증가시킨다.`

## 클래스 매개변수 속성

---

> 클래스를 많이 사용하는 프로젝트나 클래스 이점을 갖는 프레임워크가 아니라면 클래스 매개변수 속성을 사용하지 않는 것이 좋다.

자바스크립트 클래스에서는 생성자에서 매개변수를 받고 즉시 클래스 속성에 할당하는 것이 일반적.

```tsx
class Engineer {
  readonly area: string;

  constructor(area: string) {
    this.area = area;
    console.log(`I work in the ${area} area.`);
  }
}

new Engineer('mechanical').area; // 'mechanical'
```

타입스크립트는 이러한 종류의 `매개변수 속성` 을 선언하기 위한 단축 구문을 제공한다.

속성은 클래스 생성자의 시작부분에 동일한 타입의 멤버 속성으로 할당된다.

생성자의 매개변수 앞에 `readonly`, `public`, `protected`, `private` 제한자중 하나를 배치하면 타입스크립트가 동일한 이름과 타입의 속성도 선언하도록 지시한다.

```tsx
// 위의 코드를 재작성

class Engineer {
  constructor(readonly area: string) {
    console.log(`I work in the ${area} area.`);
  }
}

new Engineer('mechanical').area;
```

매개변수 속성은 클래스 생성자의 맨 처음에 할당된다.

( 기본 클래스로 부터 파생되는 경우는 `super()`를 호출한 이후 할당 )

매개변수 속성은 다른 매개변수 또는 클래스 속성과 혼합될 수 있다.

```tsx
class NamedEngineer {
  fullName: string;
  area: string;
  constructor(name: string, area: string) {
    this.area = area;
    this.fullName = `${name}, ${area} enginner`;
  }
}
```

매개변수 속성이 없는 이와 동등한 타입스크립트 코드는 비슷해 보이지만, area 를 명시적으로 할당하기 위한 코드가 몇줄 더 추가된다.

추가로, 매개변수 속성은 타입스크립트 커뮤니티에서 가끔 논의되는 주제인데, `대부분의 프로젝트에서는 런타임 구문 확장이므로` 앞에서 언급했던 단점으로 인해 어려움을 겪기 때문에,

`매개변수를 완전히 사용하지 않는것을 선호`

하지만, 클래스 생성을 매우 선호하는 프로젝트에서는 매개변수 속성을 사용하면 좋다.

매개변수속성은 매개변수 속성 이름과 타입을 두번 선언해야하는 편의 문제를 해결한다.

또한 매개변수 속성은 새로운 `#` 클래스 private 필드구문과 함께 사용할 수 없다.

![Screenshot 2023-07-01 at 9.31.30 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fd18667f-2072-4563-a5cb-fca82f976c28/Screenshot_2023-07-01_at_9.31.30_PM.png)

---

## 실험적인 데코레이터

---

> ECMA 스크립트 버전이 데코레이터 구문으로 승인될때까지 가능하면 데코레이터를 사용하지 않는 것이 좋다.

클래스를 포함하는 많은 다른 언어에서는 클래스와 클래스의 멤버를 수정하기 위한 일종의 런타임 로직으로 주석을 달거나 데로레이팅 할 수 있다.

데코레이터는 함수는 `@ 와 함수을 먼저 배치해` 클래스와 멤버에 주석을 달 수 있도록 하는 자바스크립트를 위한 제안이다.

```tsx
@myDecorator
class MyClass {...}
```

ECMA 스크립트에서 데코레이터를 아직 승인하지 않았으나 타입스크립트 버전 4.7.2 에서 `experimentalDecorators` 옵션으로 활성화 할 수는 있다.

- typescript 5.0 버전에서도 decorators 함수에 대한 언급이 있다. ( [링크](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-0.html) )
- https://github.com/tc39/proposal-decorators
- `[참고링크](https://inpa.tistory.com/entry/TS-%F0%9F%93%98-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0-%EA%B0%9C%EB%85%90-%EC%82%AC%EC%9A%A9%EB%B2%95-%EC%A0%95%EB%A6%AC)`

데코레이터의 각 사용법은 데코레이팅 하는 엔티티가 생성되자마자 한 번 실행됩니다.

각 종류의 데코레이터 ( 접근자, 클래스, 메서드, 매개변수, 속성 ) 은 데코레이팅하는 엔티티를 성명하는 서로 다른 인수 집합을 받는다.

```tsx
function logOnCall(target: any, key: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;
  console.log('[logOnCall] I am decorating', target.constructor.name);

  descriptor.value = function (...args: unknown[]) {
    console.log(`[descriptor.value] Calling ${key} with:`, ...args);

    return original.call(this, ...args);
  };
}

class Greeter {
  @logOnCall
  greet(message: string) {
    console.log(`[greet] Hello, ${message}`);
  }
}

new Greeter().greet('you');
```

원래의 greet 메서드를 호출하기 전에 descriptor.value 를 수정해 greet 메서드를 데코레이팅 한다.

---

## 열거형

> 자주 반복되는 리터럴 집합이 있고, 그 리터럴 집합을 공통이름으로 설명할 수 있으며, 열거형으로 전환했을때 훨신 더 읽기 쉽지않은 경우라면 열거형을 사용해서는 안된다.

열거형은 `각 값에 대해 친숙한 이름을 사용한 객체에 저장된 리터럴 값 집합`으로 생각할 수 있다.

```tsx
const StatusCodes = {
  InternalServerError: 500,
  NotFound: 404,
  ok: 200,
} as const;

StatusCodes.InternalServerError;
```

타입스크립트에서 열거형 같은 객체를 사용할 때 까다로운 점은 값이 해당 객체의 값 중 하나여야 함을 나태내는 훌륭한 타입 시스템 방법이 없다는 것이다.

한 가지 일반적인 방법은 `타입 제한자` `keyof` 와 `typeof` 타입 제한자를 사용홰 하나의 값을 해킹하는 것이지만, 이렇게 하려면 상당한 양의 구문을 입력해야 한다.

```tsx
type StatusCodeValue = (typeof StatusCodes)[keyof typeof StatusCodes];

let statusCodeValue: StatusCodeValue;

statusCodeValue = 200;

statusCodeValue = -1; // StatusCodeValue 타입에 존재하지 않음
```

타입스크립트는 타입이 `string` `number` 인 리터럴 값들을 갖는 객체를 생성하기 위한 `enum` 구문을 제공한다.

열거형은 `enum` 키워드로 시작해 객체 이름 그다음 쉼표로 구분된 `키를 포함한 { } 객체`이고, 각 키는 `초깃값 앞에 선택적으로 = 을 사용할 수 있다`.

```tsx
enum StatusCode {
  InternalServerError = 500,
  NotFound = 404,
  Ok = 200,
}

StatusCode.InternalServerError;

let statusCode: StatusCode;

statusCode = StatusCode.Ok; // OK

statusCode = 500; // OK
```

컴파일된 자바스크립트에서 열거형은 이에 상응하는 객체로 컴파일된다.

열거형의 각 멤버는 해당값을 갖는 객체 멤버 키가 되고 그 반대의 경우도 마찬가지 이다.

```tsx
// 컴파일된 JS 코드

'use strict';
var StatusCode;
(function (StatusCode) {
  StatusCode[(StatusCode['InternalServerError'] = 500)] = 'InternalServerError';
  StatusCode[(StatusCode['NotFound'] = 404)] = 'NotFound';
  StatusCode[(StatusCode['Ok'] = 200)] = 'Ok';
})(StatusCode || (StatusCode = {}));
StatusCode.InternalServerError;
let statusCode;
statusCode = StatusCode.Ok; // OK
statusCode = 500; // OK
```

열거형은 타입스크립트 커뮤니티에서 다소 논쟁의 여지가 있는데, `자바스크립트에 새로운 런타임 구문을 절대 추가하지 않는다` 라는 타입스크립트의 일밙거인 만트라 ( mantra) 를 위반하기 때문이다.

다른 한편으로는, 알려진 값 집합을 명시적으로 선언하는데 열거형이 매우 유용하기도 하다.

---

### `자동 숫잣값`

열거형의 멤버는 명시적인 초깃값을 가질 필요가 없다.

`값이 생략되면 타입스크립트는 첫번재 값을 0 으로 시작하고, 후속값을 1씩 증가시킨다.`

열거형 멤버의 값이 고유하고 키 이름과 연결되는 것 외에는 중요하지 않다면, 타입스크립트에서 열거형 멤버의 값을 선택하도록 하는 좋은 옵션이다.

```tsx
enum VisualTheme {
  Dark,
  Light,
  Sytstem,
}
// 런타임에서 컴파일된 JS 코드

('use strict');
var VisualTheme;
(function (VisualTheme) {
  VisualTheme[(VisualTheme['Dark'] = 0)] = 'Dark';
  VisualTheme[(VisualTheme['Light'] = 1)] = 'Light';
  VisualTheme[(VisualTheme['Sytstem'] = 2)] = 'Sytstem';
})(VisualTheme || (VisualTheme = {}));
```

숫자값이 있는 열거형에서 명시적 값을 갖지 않는 모든 멤버는 이전 값보다 1 더 큰 값을 갖는다.

```tsx
enum VisualTheme {
  TOP = 3,
  Bottom,
  Left,
  Right,
}

// 컴파일된 JS 코드

('use strict');
var VisualTheme;
(function (VisualTheme) {
  VisualTheme[(VisualTheme['TOP'] = 3)] = 'TOP';
  VisualTheme[(VisualTheme['Bottom'] = 4)] = 'Bottom';
  VisualTheme[(VisualTheme['Left'] = 5)] = 'Left';
  VisualTheme[(VisualTheme['Right'] = 6)] = 'Right';
})(VisualTheme || (VisualTheme = {}));
```

> 열거형의 순서를 수정하면 기본 번호가 변경된다. `열거형의 순서를 변경하거나 항목을 제거하는것에 주의가 필요한데, 저장된 번호가 더 이상 코드가 예상하는것과 같지 않기때문에 데이터가 갑자기 손상될 수도 있다.`

---

### `문자열 값을 갖는 열거형`

열거형은 멤버로 숫자 대신 문자열 값을 사용할 수 있다.

```tsx
enum LadStyle {
  AsNeeded = 'as-needed',
  Eager = 'eager',
}

// 컴파일된 JS 코드

('use strict');
var LadStyle;
(function (LadStyle) {
  LadStyle['AsNeeded'] = 'as-needed';
  LadStyle['Eager'] = 'eager';
})(LadStyle || (LadStyle = {}));
```

숫잣값을 멤버로 갖는 열거형과 구조적으로 동일하다.

문자열값을 갖는 열거형은 읽기 쉬운 이름으로 공유 상수의 별칭을 지정하는데 유용하다.

`문자열값의 한가지 단점은 타입스크립트에 따라 자동으로 계산할 수 없다는 것이다. 숫잣값 이 있는 멤버 뒤에 오는 열거형 멤버만 자동으로 계산할 수 있다.`

```tsx
enum Wat {
  FirstString = 'first',
  SomeNumber = 9000,
  ImplicitNumber, // OK
  AnotherString = 'another',
  NotAllowed, // Enum member must have initializer
}

// 문자열값을 갖는 멤버 이후의 멤버는 유추될 수 없기때문에 초깃값을 가져야 한다.
```

---

### `const 열거형`

열거형은 런타임 객체를 생성하므로 리터럴 값 유니언을 사용하는 일반적인 전략보다 더 많은 코드를 생성한다 `( 번들의 크기가 커진다. )`

타입스크립트는 `const 제한자로 열거형을 선택해 컴파일된 자바스크립트 코드에서 객체 정의와 속성 조회를 생략하도록 지시한다.`

```tsx
const enum DisplayHint {
  Opaque = 0,
  Semitransparent,
  Transparent,
}

let displayHint: DisplayHint.Transparent;

// 컴파일된 JS 코드

('use strict');
let displayHint;

// const 제한자로 열거형이 선언되어서 JS 코드로 컴파일되지 않았음.
```

preserveConstEnums 옵션을 사용하면, const enum으로 선언된 객체도 JS 코드로 컴파일이 가능하다.

```tsx
const enum DisplayHint {
  Opaque = 0,
  Semitransparent,
  Transparent,
}

let displayHint: DisplayHint;

displayHint = 2;

// 컴파일된 JS 코드

('use strict');
var DisplayHint;
(function (DisplayHint) {
  DisplayHint[(DisplayHint['Opaque'] = 0)] = 'Opaque';
  DisplayHint[(DisplayHint['Semitransparent'] = 1)] = 'Semitransparent';
  DisplayHint[(DisplayHint['Transparent'] = 2)] = 'Transparent';
})(DisplayHint || (DisplayHint = {}));
let displayHint;
displayHint = 2;
```

---

### `네임스페이스`

> 네임스페이스는 최신 자바스크립트 모듈 의미 체계와 일치하지 않기 때문에, 기존 패키지에 대한 DefinitelyTyped 타입 정의를 작성하지 않는 한 네임스페이스를 사용하지 않는 것이 좋다.

ECMA 스크립트 모듈이 승인되기 전에는 웹 애플리케이션이 출력 코드 대부분을 브라우저에 따라 로드되는 하나의 파일로 묶는것이 일반적이었다. ( `번들링` )

이러한 거대한 하나의 파일은 종종 프로젝트의 서로 다른 영역에 걸쳐서 중요한 값에 대한 참조를 위해 전역 변수를 생성했는데, `RequireJs` 와 같은 오래된 모듈 로더를 설정하는 것보다 페이지에 하나의 파일을 포함하는 것이 더 간단하고, 하나의 파일 출력을 위해 만들어진 프로젝트에는 코드 영역과 전역변수를 구성하는 방법이 필요했다.

타입스크립트 언어는 지금은 네임스페이스라 부르는 `내부  모듈` 개념을 가진 하나의 해결책을 제공했는데, 네임스페이스는 객체의 멤버로 호출할 수 있는 내보낸 콘텐츠가 있는 전역으로 사용 가능한 객체이다.

네임스페이스는 namespace 키워드와 { } 코드 블럭 으로 정의한다.

네임스페이스의 블록의 모든 코드는 함수 클로저 내에 평가된다.

```tsx
namespace Randomized {
  const value = Math.random();
  console.log(`My value is ${value}`);
}

// 컴파일된 JS 코드

('use strict');
var Randomized;
(function (Randomized) {
  const value = Math.random();
  console.log(`My value is ${value}`);
})(Randomized || (Randomized = {}));

// value 변수는 namespace 블록 안에만 존재함 으로 외부에서 접근이 불가능하다. ( 클로저 개념 )
```

---

### `네임스페이스 내보내기`

네임스페이스를 유용하게 만드는 핵심 기능ㄹ은 콘텐츠를 네임스페이스 객체의 멤버로 만들어 내보내는 기능입니다.

이 작업 덕분에 코드의 다른 영역에서 네임스페이스 이름으로 해당 멤버를 참조할 수 있다.

```tsx
namespace Settings {
  export const name = 'My Application';
  export const version = '1.2.3';

  export function describe() {
    return `${Settings.name} at version ${Settings.version}`;
  }

  console.log('Inititializing', describe());
}

console.log(Settings.describe());

// 컴파일된 JS 코드

// Settings 멤버로 참조됨을 보여준다.

('use strict');
var Settings;
(function (Settings) {
  Settings.name = 'My Application';
  Settings.version = '1.2.3';
  function describe() {
    return `${Settings.name} at version ${Settings.version}`;
  }
  Settings.describe = describe;
  console.log('Inititializing', describe());
})(Settings || (Settings = {}));
console.log(Settings.describe());
```

출력 객체에 var을 사용하고 내보낸 콘텐츠를 해당 객체의 멤버로 참조되도록 하면, 네임스페이슥다 여러 파일에 걸쳐 분할되어 작성되었더라도, 아주 잘 동작한다.

```tsx
// settings/constants.ts

namespace Settings {
  export const name = 'My Application';
  export const version = '1.2.3';
}

// settings/describe.ts
namespace Settings {
  export function describe() {
    return `${Settings.name} at version ${Settings.version}`;
  }
  console.log('Inititializing', describe());
}

console.log(Settings.describe());

// 컴파일된 JS 코드

('use strict');

// settings/constants.ts

var Settings;
(function (Settings) {
  Settings.name = 'My Application';
  Settings.version = '1.2.3';
})(Settings || (Settings = {}));

// settings/describe.ts

(function (Settings) {
  function describe() {
    return `${Settings.name} at version ${Settings.version}`;
  }
  Settings.describe = describe;
  console.log('Inititializing', describe());
})(Settings || (Settings = {}));
console.log(Settings.describe());

// 아래와 코드와 동일하게 동작

const Settings = {
  describe: function describe() {
    return `${Settings.name} at version ${Settings.version}`;
  },

  name: 'My Application',
  version: '1.2.3',
};
```

`const 로 선언된 객체와 namespace 와의 차이점이라면, 서로 다른 파일로 분할 될 수 있고 멤버가 네임스페이스의 이름으로 여전히 참조될 수 있다는 점이다.`

---

### `중첩된 네임스페이스`

네임스페이스는 다른 네임스페이스 내에서 네임스페이스를 내보내거나 `하나 이상의 마침표를 사용`해서 무한으로 `중첩` 할 수 있다.

```tsx
// 다음 두 개의 선언이 동일하게 작동한다.
namespace Root.Nested {
  export const value1 = true;
}

namespace Root {
  export namespace Nested {
    export const value2 = true;
  }
}

// 컴파일된 JS 코드

// 구조적으로 동일하게 컴파일된다.
('use strict');
var Root;
(function (Root) {
  var Nested;
  (function (Nested) {
    Nested.value1 = true;
  })((Nested = Root.Nested || (Root.Nested = {})));
})(Root || (Root = {}));
(function (Root) {
  let Nested;
  (function (Nested) {
    Nested.value2 = true;
  })((Nested = Root.Nested || (Root.Nested = {})));
})(Root || (Root = {}));
```

중첩된 네임스페이스는 네임스페이스로 구성된 `더 큰 프로젝트의 구역들 사이에 더 자세한 설명`을 적용할 수 있는 편리한 방법이다.

---

### `타입 정의에서 네임스페이스`

`네임스페이는 DefinitelyTyped 타임 정의에서 유용하게 사용 될 수 있다.`

많은 자바스크립트 라이브러리, 와 특히 제이쿼리 같은 오래된 웹 애플리케이션에서 고정적으로 사용하는 라이브러리는 전통적인 `<script>` 태그를 사용해 웹 브라우저에 포함하도록 설정한다.

네임스페이스를 타이핑할 때는 모든 코드에 사용 가능한 전역변수. 즉, 네임스페이스로 완벽하게 감싼 구조를 생성한다는 것을 나타내야 한다.

타입스크립트 모듈 타입 정의에 `export as namespace 뒤에 전역이름을 포함하고`, 해당 이름을 사용해서 모듈을 전역으로 사용할 수 있다.

```tsx
export const value: number;
export as namespace libExample;

// src/index.ts
import * as libExample from 'my-example-lib';
const value = window.libexample.value;
```

---

### `네임스페이스보다 모듈을 선호함`

ECMA 스크립트 모듈을 사용해 최신 표준에 맞게 이전의 예제를 재작성 해볼 수 있다.

```ts
// settings/constants.ts
export const name = 'My Application';
export const version = '1.2.3';

// settings/describe.ts
import { name, version } from './constants.ts';

export function describe() {
  return `${Settings.name} at version ${Settings.version}`;
}

console.log('Initializing', describe());

// Settings 를 어디서 참조하는지 ??
```

---

### 타입 전용 가져오기와 내보내기

타입스크립트 트랜스파일러는 자바스크립트 런타임에서 사용되지 않으므로 파일의 가져오기와 내보내기 에서 타입 시스템에서만 사용되는 값을 제거한다.

타입스크립트는 `export` 와 `import` 선언에서 개별적으로 가져온 이름 또는 전체 { … } 객체앞에 type 제한자를 추가할 수 있다.

이렇게 하면 타입 시스템에서만 사용된다는 것을 나타낸다.

```tsx
export { type TypeOne, value };
export type { DefaultType, TypeTwo };

import { type TypeOne, value } from 'my-example-types';
import type { TypeTwo } from 'my-example-types';
import type DefaultType from 'my-example-types';
```

```tsx
import { ClassOne, type ClassTwo } from 'my-example-types';

new ClassOne();

new ClassTwo();

//'ClassTwo' cannot be used as a value because it was imported using 'import type'.
```

내보내진 자바스크립트에 복잡성을 더하는 대신, 타입 전용 가져오기와 내보내기는 코드 일부를 제거할 수 있을때 타입스크립트 외부의 트랜스파일러에게 명확하게 알려준다.
