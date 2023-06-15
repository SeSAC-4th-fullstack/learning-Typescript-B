# Chapter 03

> 유니언과 리터럴
> 

# 1. 유니언(Union)

---

### 1) 유니언

```tsx
const bestsong = Math.random() > 0.5
								? undefined
								: "Hello";

// bestsong: 'hello" | undefined
```

- 값에 허용된 타입을 두 개 이상의 가능한 타입으로 확장하는 것

### 2) 유니언 속성

```tsx
const physicist = Math.random() > 0.5
								? "Hello"
								: 20;

physicist.toString();

physicist.toUpperCase();   // Error : String 타입에서만 사용가능 속성

physicist.toFixed();  // Error : number 타입에서만 사용이 가능한 속성
```

- 선언된 여러가지 타입에서 공통으로 존재하는 속성만 사용이 가능하다.

# 2. 내로잉(Narrowing)

---

### 1) 내로잉

: 값에 허용된 타입이 하나 이상의 가능한 타입이 되지 않도록 좁히는 것

### 2) 타입 가드

: 타입을 좁히는데 사용하는 논리적인 검사

### 3) 내로잉 방법

```tsx
let admiral: number | string;

admiral = "Hello";

admiral.toUpperCase();

admiral.toFixed();  // Error : admiral이 string 타입으로 선언이 되었다.
```

- **값 할당을 통한 내로잉**
    
    : 직접 값을 선언함으로 변수의 타입을 지정해준다.
    

```tsx
let scientist = Math.random() > 0.5
							? "Hello"
							: 100;

if(scientis == "Hello") {
	scientist.toUpperCase();
}

scientist.toUpperCase();  // Error : 타입 추론이 불분명하다.
```

- **조건 검사를 통해 내로잉**
    
    : 조건을 통해 변수의 타입을 좁힘으로서 타입을 지정한다.
    

```tsx
let researcher = Math.random() > 0.5
								? "Hello"
								: 100;

if(typeof researcher == "string") {
	researcher.toUpperCase();
}
```

- **typeof 검사를 통한 내로잉**
    
    : typeof를 통해 조건을 걸어 변수의 타입을 좁힌다.
    

```tsx
let student = Math.random() > 0.5
						? "JPark"
						: undefined;

if(student) {
	student.toUpperCase();
}

	student.toUpperCase();  // Error : student의 타입이 불분명하다.

// 참 검사
student && student.toUpperCase();
student?.toUpperCase();
```

- **참 검사를 통한 내로잉**
    
    : 변수의 타입이 null 또는 undefined 일 경우, 참 검사를 통해 타입을 좁힌다.
    

# 3. 리터럴 타입(Literal Type)

---

### 1) 리터럴 타입

```tsx
const student = "JPark";
```

- **리터럴 타입**
    
    : string, number, boolean… 과 같이 원시 타입이 아닌 특정 원시값으로 지정 되어 있는 타입
    

# 4. 엄격한 Null 검사

---

### 1) strictNullChecks 옵션 (tsconfig.json 파일)

```tsx
let student = Math.random() > 0.5
						? "JPark"
						: undefined;

student.toUpperCase();  // Error
```

- **strictNullChecks = true**
    
    : null 또는 undefined를 철저히 확인한다.
    
    : 따라서 undefined가 올 수 있는 student에는 toUpperCase()를 사용할 수 없다.
    

```tsx
let student = Math.random() > 0.5
						? "JPark"
						: undefined;

student.toUpperCase();  // Ok
```

- **strictNullChecks = false**
    
    : null 또는 undefined를 가능성을 확인하지 않는다.
    
    : 따라서, undefined가 올 수 있어도 toUpperCase()가 사용가능하다.
    

### 2) 초기값이 없는 변수

```tsx
let student: string;

student.toUpperCase();  // Error

student = "Sam";
student.toUpperCase();  // Ok
```

- 값이 할당되기도 전에 타입의 속성에 접근하려고 하면 오류가 발생한다.
- 대신, 변수 타입이 undefined가 포함되어 있으면 오류가 발생하지 않는다.

# 5. 타입 별칭

---

### 1) 타입 별칭(Type Alias)

```tsx
type A = string | number | boolean;

const a1: A = "hello";
const a2: A = 100;
const a3: A = true; 
```

- 타입 별칭은 자바스크립트 코드가 아니기 때문에 런타입 코드에서 참조할 수 없다.
- 즉, 생성된 JS 파일에는 타입에 대한 코드는 존재하지 않는다.

### 2) 별칭 결함

```tsx
// Case 1
type A = string;
type B = A | number | boolean;

// Case 2
type B = A | number | boolean;
type A = string;
```

- 별칭 결함은 순서대로 선언할 필요 없다.