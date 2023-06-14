# 3) 유니언과 리터럴

---

타입스크립트가 해당 값을 바탕으로 추론을 수행하는 두가지 핵심 개념

- `유니언` : 값에 허용된 타입을 두개 이상의 가능한 타입으로 확정하는 것
- `내로잉` : 값에 허용된 타입이 하나 이상의 가능한 타입이 되지 않도록 좁히는 것

`유니언`과 `내로잉`은 다른 주요 프로그래밍 언어에서는 불가능 하지만, 타입스크립트 에서는 간으한 `**코드 정보에 입각한 추론**` 을 해내는 강력한 개념이다.

## **유니언 타입**

---

```tsx
let mathematician = Math.random() > 0.5 ? undefined : 'Mark Goldberg';
```

위에서 mathematician의 타입은 잠재적인 타입이긴 하지만, 무조건 `undefined` 혹은 `string` 일 수 잇다.

`유니언 타입`은 값이 정확이 `어떤 타입인지 모르지만 두 개 이상의 옵션 중 하나`라는 것을 알고 있는 경우에 코드를 처리하는 훌륭한 개념이다.

---

### `유니언 타입 선언`

```tsx
let thinker: string | null = null;

if (Math.random() > 0.5) {
  thinker = 'Susanne Langer'; // Ok
}
```

위의 예제에서 `thinker` 의 초깃값은 `null` 이지만, 잠재적으로 `null | string` 타입이 될 수 있음을 알려주고,

명시적으로 `string | null` 타입 애너테이션은 thinker 의 값으로 `string` 타입을 할당할 수 있음을 알려준다.

---

### `유니언 타입 속성`

유니언 타입으로 선언 될 경우에는 선언한 모든 타입의 멤버속성에만 접근이 가능하며, 유니언 외의 타입에 접근하려고한다면 타입 오류가 발생한다.

```tsx
let physicist = math.random() > 0.5 ? 'Marie Cruise' : 84;

physicist.toString(); // Ok

physicist.toUpperCase(); // number 타입에는 toUpperCase 속성이 존재하지 않음.

physicist.toFixed(); // string 타입에는 toFixed 속성이 존재하지 않음
```

모든 유니언 타입에 존재하지 않는 속성에 대한 접근을 제한하는 것은 안전 조치에 해당하며, 객체가 어떤 속성을 포함하는 타입으로 확실하게 알려지지 않는 경우 속성이 존재하지 않을 수 있기 때문에, 타입스크립트는 해당 속성을 사용하려고 시도하는 것이 안전하지 않다고 여긴다.

---

## 내로잉

---

타입 내로잉은 값이 정의, 선언 혹은 이전에 유축된 것보다 더 구체적인 타입임을 코드에서 유추하는 것 이다.

타입 스크립트가 `값의 타입이 이전에 알려진 것보다 더 좁혀졌다는 것`을 알게되면 `더 구체적인 타입`으로 취급한다.

타입을 좁히는데 사용할 수 있는 논리적 검사를 `**타입가드**` 라고 한다.

---

### `**값 할당을 통한 내로잉**`

변수에 값을 직접 할당하면 타입스크립트는 변수의 타입을 할당된 값의 타입으로 좁혀준다.

```tsx
let admiral: number | string;
// 변수에 유니언 타입 애너테이션이 명시되고

admiral = 'Grace Hopper';
// 초기에 값이 주어질때 타입 내로잉이 진행된다.

admiral.toUpperCase(); // Ok : string

admiral.toFixed();

// toFixed 속성은 string 타입에 존재하지 않음.
```

admiral 변수는 초기에 `number | string` 으로 선언했지만, `Grace Hopper` 값이 할당된 이후 타입스크립트는 admiral 변수가 string 타입 이라는 것을 알게 된다.

```tsx
let inventor: number | string = 'Hendy Lamarr';
// 변수에 유니언 타입 애너테이션이 명시되고
// 초기에 값이 주어질때 타입 내로잉이 진행된다.

inventor.toUpperCase(); // Ok : string

inventor.toFixed();

// toFixed 속성은 string 타입에 존재하지 않음.
```

---

### `**조건 검사를 통한 내로잉**`

일반적으로 타입스크립트 에서는 변수가 알려진 값과 같은지 확인하는 if 문을 통해 변수의 값을 좁히는 방법을 사용한다. 타입스크립트는 if 문 내에서 변수가 알려진 값과 동일한 타입인지를 확인함.

```tsx
let scientist = Math.random() > 0.5 ? 'Rosalind Franklin' : 51;

if (scientist === 'Rosalind Franklin') {
  // scientist : string 타입
  scientist.toUpperCase();
}
// scientist : string | number 타입
scientist.toUpperCase();
```

---

### `**typeof 검사를 통한 내로잉**`

타입스크립트는 직접 값을 확인해 타입을 좁히기도 하지만, `typeof` 연산자를 사용할 수도 있다.

```tsx
let researcher = Math.random() > 0.5
		? 'Rosalind Franklin'
		: 51;

if ( typeof researcher === 'string') {
	researcher.toUpperCase(); // Ok : string
}else {
	researcher.toFixed(); // Ok : number
}

// 논리적 부정과 else 문

if (!typeof researcher === 'string') {
	researcher.toFixed(); // Ok : number
}else {
	researcher.toUpperCase(); // Ok : string
}

// 삼항연산자

typeof researcher === 'string
	? researcher.toUpperCase() // string
	: researcher.toFixed()  // number

```

`typeof` 연산자를 통해 if 문에서 타입을 확인하며, `!` 를 사용한 논리적 부정과 `else` 문 그리고 삼항연산자 와도 잘 작동한다.

---

## **리터럴 타입**

`리터럴 타입`은 원시 타입 값 중 어떤 것이 아닌 `특정 원싯값`으로 알려진 타입이 리터럴 타입이다.

만약 변수를 `const` 로 선언하고, 직접 리터럴 값을 할당하면 타입스크립트는 해당 변수를 할당된 리터럴 값으로 유추합니다.

```tsx
const philosopher = 'hypatia';
```

각 원시타입은 해당 타입이 가질 수 있는 가능한 모든 리터럴 값의 전체 조합으로 생각할 수 있다.

→ `원시 타입은 해당 타입의 가능한 모든 리터럴 값의 집합`

예시 )

- `boolean` : true | false ;
- `null / undefined` : 둘 다 자기 자신 즉, 오직 하나의 리터럴 값만 가진다.
- `number` : 0 | 1 | 2 | 3 ….
- `string` : “” | “abc” | …

---

### `**리터럴 할당 가능성**`

0 과 1 처럼 동일한 원시 타입일지라도 서로 다른 리터럴 타입은 서로 할당할 수 없다.

```tsx
let lifespan: number | 'ongoing' | 'uncertain';

lifespan = 89; // number;

lifespan = 'ongoing'; // 'ongoing';

lifespan = true;
// type true 는 number | 'ongoing' | 'uncertain' 타입에 할당 가능하지 않음.;
```

---

### `**엄격한 null 검사**`

리터럴로 좁혀진 유니언의 힘은 타입스크립트에서 `**엄격한 null 검사**` 라 부르는

타입 시스템 영역인 `잠재적으로 정의 되지 않은 **undefined** 값으로 작업할 때 특히 두드러진다.`

타입스크립트에서는 십억달러의 실수 를 바로잡기 위해 `엄격한 null 검사`를 사용한다.

엄격한 null 검사가 없는 언어에서는 다음 예제 코드처럼 string 타입 변수에 null을 할당하는것이 허용된다.

```tsx
const firstName: string = null;
```

타입스크립트 컴파일러는 실행 방식을 변경할 수 있는 다양한 옵션을 제공하는데, 가장 유용한 옵션 중 하나인 `strictNullChecks` 는 `엄격한 null 검사를 활성화할지 여부`를 결정한다.

간략하게 설명하면, `strictNullChecks` 를 비활성화 하면 코드의 모든 타입에 `| null | undefined` 를 추가해야 모든 변수가 null 또는 undefined 를 할당할 수 있다.

`strictNullChecks` 옵션을 `false` 로 설정하면당므의 코드 타입은 완벽하 안전하다고 간주되는데 하지만 실상은 그렇지ㅡ 않다. `nameMaybe` 변수가 `.toLowerCase()` 에 접근할 때 `undefined` 가 되는것은 잘못된 지정이다.

```tsx
// strictNullChecks = false;
let nameMaybe = Math.random() > 0.5 ? 'Tony Hoare' : undefined;

nameMaybe.toLowerCase();
// 잠재적 런타임 에러 : undefined 에서 toLowerCase() 속성을 찾을 수 없다.

// strictNullChecks = true;

let nameMaybe = Math.random() > 0.5 ? 'Tony Hoare' : undefined;

nameMaybe.toLowerCase();
// 객체가 undefined 일 수 있다.
```

엄격한 null 검사를 활성화 해야 `null 또는 undefined 값으로 인한 오류로 부터 안전한지` 여부를 쉽게 파악할 수 있다.

---

### `**참 검사를 통한 내로잉**`

타입스크립트는 잠재적인 값 중 truthy로 확인된 일부에 한 해서만 변수의 타입을 좁힐 수 있다.

논리 연산자 `&&` 와 `?` 는 참 여부를 검사하는 일은 잘 수행하지만, 그 외의 기능은 제공하지 않는다.

---

### `**초깃값이 없는 변수**`

자바스크립트에서 초깃값이 없는 변수는 기본적으로 `undefined` 로 초기화가 된다 ( 크롬 기준 )

타입스크립트는 값이 할당될 때까지 변수가 `undefined` 임을 이해한다.

값이 할당되기 전에 속성 중 하나에 접근하려는 것 처럼 해당 변수를 사용하려고 시도하면 다음과 같은 오류 메시지가 나타난다.

```tsx
let mathematician: string;

mathematician?.length;

// 오류 : mathematician 변수가 할당되기 이전에 사용되었음

mathematician = 'Mark Goldberg';
mathematician.length;
```

---

### `타입 별칭`

코드에서 볼 수 있는 유니언 타입 대부분은 두세 개의 구성요소를 갖지만 가끔 반복해서 입력하기 불편한 조금 긴 형태의 유니언 타입을 발견할 수 있다.

```tsx
// 타입 별칭 미 사용

let rawDataFirst: boolean | number | string | null | undefined;
let rawDataSecond: boolean | number | string | null | undefined;
let rawDataThird: boolean | number | string | null | undefined;

// 타입 별칭 사용

type RawData = boolean | number | string | null | undefined;

let rawDataFirst: RawData;
let rawDataSecond: RawData;
let rawDataThird: RawData;
```

---

### **`타입 별칭은 자바스크립트가 아니다`**

타입 별칭은 타입 애너테이션 처럼 자바스크립트 코드로 컴파일 되지 않는다.

타입 별칭은 순전히 타입 시스템에만 존재하므로 런타임 코드에서는 참조할 수 없다.

타입스크립트는 런타임에 존재하지 않는 항목에 접근하려고 하면 타입 오류로 알려준다.

---

### `타입 별칭 결합`

타입 별칭은 다른 타입 별칭을 참조할 수 있다.

유니언 타입인 타입 별칭 내에 또 다른 유니언 타입인 타이브 별칭을 포함하고 있다면, 다른 타입별칭을 참조하는 것이 좋다

```tsx
type Id = number | string;

// IMaybe 타입은 다음과 같다 : number | string | undefined | null
type IMaybe = Id | undefined | null;
```

---

### **요약**

- 유니언 타입으로 두 개 이상의 타입 중 하나일 수 있는 값을 나타내는 방법
- 타입 애너테이션으로 유니언 타입을 명시적으로 표시하는 방법
- 타입 내로잉으로 값의 가능한 타입을 좁히는 방법
- 리터럴 타입의 const 변수와 원시 타입의 let 변수의 차이점
- 십억 달러의 실수 와 타입스크립트가 엄격한 null 검사를 처리하는 방법
- 존재하지 않을 수 있는 값을 나타내는 명시적인 | undefined
- 할당되지 않은 변수를 위한 암묵적인 | undefined
- 반복적으로 사용하고 입력이 긴 유니언 타입을 타입 별칭에 저장하는 방법
