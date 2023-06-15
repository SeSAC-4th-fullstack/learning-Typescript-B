# 2.1 타입의 종류

**타입**은 자바스크립트에서 다루는 **값의 형태**에 대한 설명이다.

여기서 형태는 **값에 존재하는 속성과 메서드** 그리고 내장되어 있는 `typeof` **연산자가 설명하는 것**을 의미한다.

- 타입스크립트의 가장 기본적인 타입은 자바스크립트의 일곱가지 기본 원시타입(primitive type)과 동일하다.
  | type | ex |
  | --------- | ------------------- |
  | null | null; |
  | undefined | undefined; |
  | boolean | true 혹은 false |
  | string | “Louise”; |
  | number | 1337; |
  | bigint | 1337n; |
  | symbol | Symbol(”Franklin”); |

1. 타입 시스템

   > 타입 시스템은 프로그래밍 언어가 프로그램에서 가질 수 있는 타입을 이해하는 방법에 대한 규칙 집합이다.

   ### 타입스크립트의 타입 시스템의 작동 방식

   1. 코드를 읽고 존재하는 모든 타입과 값을 이해한다.
   2. 각 값이 초기 선언에서 가질 수 있는 타입을 확인한다.
   3. 각 값이 추후 코드에서 어떻게 사용될 수 있는지 모든 방법을 확인한다.
   4. 값의 사용법이 타입과 일치하지 않으면 사용자에게 오류를 표시한다.

**위의 작동 방식을 토대로한 타입 추론 과정 예시**

```tsx
/*
1. 코드를 읽고 firstName이라는 변수를 이해한다.
2. 초깃값이 "Whitney"이므로 firstName이 string 타입이라고 결론을 짓는다.
3. firstNme의 .length 멤버를 함수처럼 호출하는 코드를 확인한다.
4. string의 .length 멤버는 함수가 아닌 숫자라는 오류를 표시한다. 즉 함수처럼 호출할 수 없다.
*/

let firstName = "Whitney";
firstName.length();
// Error : This expression is not callable.
//   Type 'Number' has no call signatures.
```

<br>

1. 타입스크립트에서 자주 접하게 되는 오류 두가지의 차이점

   > 구문 오류 : 타입스크립트가 자바스크립트로 변환되는 것을 차단한 경우 <br>
   > 타입 오류 : 타입 검사기에 따라 일치하지 않는 것이 감지된 경우

   ### ✔️ 구문오류

   - 타입스크립트가 코드로 이해할 수 없는 잘못된 구문을 감지할 때 발생한다.
   - 타입스크립트가 타입스크립트 파일에서 자바스크립트 파일을 올바르게 생성할 수 없도록 차단한다.
   - 그러나 tsc 설정에 따라 자바스크립트 코드를 얻을 수는 있지만 원하던 결과는 아닐 수 있다.

   ### ✔️ 타입 오류

   - 타입스크립트의 타입 검사기가 프로그램의 타입에서 오류를 감지했을 때 발생한다.
   - 오류가 발생했다고 해서 타입스크립트 구문이 자바스크립트로 변환되는 것을 차단하지는 않는다.
   - 출력된 자바스크립트 코드가 원하는 대로 실행되지 않을 가능성이 있다는 신호를 타입 오류로 알려준다.

<br>

# 2.2 할당 가능성

> 할당 가능성(assignability)
> 타입스크립트에서 함수 호출이나 변수에 값을 제공할 수 있는지 여부를 확인하는 것

```tsx
let lastName = "King";
lastName = true;

// Error : Type 'boolean' is not assignable to type 'string'.
```

‘**Type … is not assignable to type …’** 형태의 오류는

타입스크립트 코드를 작성할 때 만나게 되는 가방 일반적인 오류 중 하나이다.

해당 오류 메세지에서 언급된

첫번째 type은 코드에서 변수에 할당하려고 시도하는 값이다.

두번째 type은 값이 할당 되는 변수이다.

위의 코드 스니펫에서

- 첫번째 type : true
- 두번째 type : lastName

<br>

# 2.3 타입 애너테이션

타입스크립트는 나중에 사용할 변수의 초기 타입을 파악하려고 시도하지 않는다.

- 초기 타입을 유추할 수 없는 변수는 **진화하는 any**라고 부른다.
- 특정 타입을 강제하는 대신 새로운 값이 할당될 때마다 변수 타입에 대한 이해를 발전시킨다.
- any 타입을 사용해 any 타입으로 진화하는 것을 허용하게 되면 타입스크립트의 타입 검사 목적을 부분적으로 쓸모없게 만든다.
- 타입스크립트는 값이 어떤 타입인지 알고 있을 때 가장 잘 작동한다!

```tsx
// 초기 타입을 암묵적으로 any 타입으로 간주
let rocker; // any 타입
rocker = "Joan Jett"; // string 타입

// 문자열을 대문자로 변환해서 반환해준다.
rocker.toUpperCase();

// 여기서 rocker 변수는 any 타입에서 number 타입으로 진화했다.
rocker = 19.58;

// 매개변수로 전달된 숫자만큼 수의 길이를 string으로 반환해준다.
rocker.toPrecision(1);

rocker.toUpperCase();
// Error : 'toUpperCase' dose not exist on type 'number'.
```

### ✔️ 타입 애너테이션

> 초깃값을 할당하지 않고도 변수의 타입을 선언할 수 있는 구문
> 변수 이름 뒤에 배치되며 콜론(:) 과 타입 이름을 차례대로 기재한다.

```tsx
// 타입 애너테이션
let rocker: string;
rocker = "Joan Jett";

// JS로 컴파일 된 후
let rocker;
rocker = "Joan Jett";
```

- 타입 애너테이션은 타입스크립트에만 존재하고 tsc 명령어를 실행해 타입스크립트 소스 코드를 자바스크립트 코드로 컴파일하면 해당 코드가 삭제된다.
- 런타임 코드에 영향을 주지 않는다.
- 유효한 자바스크립트 구문이 아니다.
- 타입 애너테이션은 타입스크립트가 자체적으로 수집할 수 없는 정보를 타입스크립트에 제공할 수 있다.

```tsx
let rocker: string;
rocker = 19.58;

// Error: Type 'number' is not assignable to 'string'.
```

- 변수에 타입 애너테이션으로 정의한 타입 외의 값을 할당하면 타입 오류가 발생한다.

<br>

1. 불필요한 타입 애너테이션

   - 타입을 즉시 유추할 수 있는 변수에도 타입 애너테이션을 사용할 수 있는데 중복으로 사용하게 되는것이다.
   - 초깃값이 있는 변수에 타입 애너테이션을 추가하면 타입스크립트는 변수에 할당된 값의 타입이 일치하는지 확인한다.
   - 많은 개발자들은 아무것도 변하지 않는 변수에는 타입 애너테이션을 추가하지 않기를 선호한다.

   ```tsx
   // 값이 string이기 때문에 string으로 추론이 가능
   // 타입 애너테이션을 붙여도 타입 시스템은 변경되지 않는다
   let firstName: string = "Tina";
   ```

<br>

# 2.4 타입 형태

- 타입스크립트는 객체에 어떤 멤버 속성이 존재하는지 알고 있다.
- 변수의 속성에 접근하려고 한다면 타입스크립트는 접근하려는 속성이 해당 변수의 타입에 존재하는지 확인한다.
- 타입스크립트는 객체의 형태에 대한 이해를 바탕으로 할당 가능성뿐만 아니라 객체 사용과 관련된 문제도 알려준다.

```tsx
let cher = {
  firstName: "Cherilyn",
  lastName: "Sarkisian",
};

cher.middleName;
// ~~~~~~~~~~~~

// Error: Property 'middleName' does not exist on type
// '{ firstName: string; lastName: string; }'.
```

<br>

### ✔️ 모듈

- ECMA스크립트 2015에는 파일 간 가져오고(import) 내보내는(export) 구문을 표준화하기 위해 ECMA스크립트 모듈(ESM)이 추가되었다.

> 모듈 : export 또는 import가 있는 파일 <br>
> 스크립트 : 모듈이 아닌 모든 파일

- 모듈 파일에 선언된 모든 것은 해당 파일에서 명시한 export 문에서 내보내지 않는 한 모듈 파일에서만 사용할 수 있다.
- 한 **모듈에서** 다른 파일에 선언된 변수와 **동일한 이름으로 선언된 변수는** 다른 파일의 변수를 가져오지 않는 한 **이름 충돌로 간주하지 않는다**.

```tsx
// a.ts
export const shared = "Cher";

// b.ts
export const shared = "Cher";

// a.ts에서 shared변수를 c.ts이라는 하나의 파일에 가져왔는데
// c.ts에 이미 선언되어있는 shared변수와 충동되어 타입 오류가 발생한다

// c.ts
import { shared } from "./a";
//     ~~~~~~~~~~
// Error: Import declaration conflicts with local declaration of 'shared'.

export const shared = "Cher";
//           ~~~~~~
// Error: Individual declarations in merged declaration
// 'shared' must be all ecported or all local.
```

### 그러나 파일이 스크립트면!

- 해당 파일을 전역 스코프(scope)로 간주해 모든 스크립트가 파일의 내용에 접근할 수 있다.
- 스크립트 파일에 선언된 변수는 다른 스크립트 파일에 선언된 변수와 동일한 이름을 가질 수 없다.

```tsx
// a.ts
const shared = "Cher";
//           ~~~~~~
// Error: Cannot redeclare block-scoped variable 'shared'.

// b.ts
const shared = "Cher";
//           ~~~~~~
// Error: Cannot redeclare block-scoped variable 'shared'.
```

<br>

### ❗️

- ECMA스크립트 사양에 따라 export 또는 import 문 없이 파일을 모듈로 만들어야한다면 파일의 아무 곳에나 export{};를 추가해 강제로 모듈이 되도록 만들어준다.
- 타입스크립트는 CommonJS와 같은 이전 모듈을 사용해서 작성된 타입스크립트 파일의 import, export 형태는 인식하지 못한다.
- 일반적으로 CommonJS 스타일의 require 함수에서 반환된 값을 any 타입으로 인식한다.
