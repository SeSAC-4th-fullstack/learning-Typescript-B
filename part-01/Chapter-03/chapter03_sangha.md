# Chapter 03

> 유니언과 리터럴
> 

## 1. 유니언(Union)

1) 유니언

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled.png)

<aside>
💡 값에 허용된 타입을 두 개 이상의 가능한 타입으로 확장하는 것

</aside>

<aside>
💡 값에 허용된 타입을 두 개 이상의 가능한 타입으로 확장하는 것

</aside>

2) 유니언 속성

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%201.png)

<aside>
💡 선언된 여러가지 타입에서 공통으로 존재하는 속성만 사용이 가능하다.

</aside>

---

## 2. 내로잉(Narrowing)

1) 내로잉

<aside>
💡 값에 허용된 타입이 하나 이상의 가능한 타입이 되지 않도록 좁히는 것

</aside>

2) 타입 가드

<aside>
💡 타입을 좁히는데 사용하는 논리적인 검사

</aside>

3) 내로잉 방법

<aside>
1️⃣ **값 할당을 통한 내로잉**

: 직접 값을 선언함으로 변수의 타입을 지정해준다.

</aside>

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%202.png)

<aside>
2️⃣ **조건 검사를 통해 내로잉**

: 조건을 통해 변수의 타입을 좁힘으로서 타입을 지정한다.

</aside>

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%203.png)

<aside>
3️⃣ **typeof 검사를 통한 내로잉**

: typeof를 통해 조건을 걸어 변수의 타입을 좁힌다.

</aside>

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%204.png)

<aside>
4️⃣ **참 검사를 통한 내로잉**

: 변수의 타입이 null 또는 undefined 일 경우, 참 검사를 통해 타입을 좁힌다.

</aside>

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%205.png)

---

## 3. 리터럴 타입(Literal Type)

1) 리터럴 타입

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%206.png)

<aside>
💡 **리터럴 타입**

: string, number, boolean… 과 같이 원시 타입이 아닌 특정 원시값으로 지정 되어 있는 타입

</aside>

---

## 4. 엄격한 Null 검사

1) strictNullChecks 옵션 (tsconfig.json 파일)

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%207.png)

<aside>
💡 **strictNullChecks = true**

: null 또는 undefined를 철저히 확인한다.

: 따라서 undefined가 올수 있는 student에는 toUpperCase()를 사용할 수 없다.

</aside>

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%208.png)

<aside>
💡 **strictNullChecks = false**

: null 또는 undefined를 가능성을 확인하지 않는다.

: 따라서, undefined가 올 수 있어도 toUpperCase()가 사용가능하다.

</aside>

2) 초기값이 없는 변수

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%209.png)

<aside>
💡 값이 할당되기도 전에 타입의 속성에 접근하려고 하면 오류가 발생한다.

대신, 변수 타입이 undefined가 포함되어 있으면 오류가 발생하지 않는다.

</aside>

---

## 5. 타입 별칭

1) 타입 별칭(Type Alias)

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%2010.png)

<aside>
💡 타입 별칭은 자바스크립트 코드가 아니기 때문에 런타입 코드에서 참조할 수 없다.

</aside>

2) 별칭 결함

![Untitled](Chapter%2003%2080f991d9cec24cf3848b4e5f57059b98/Untitled%2011.png)

<aside>
💡 별칭 결함은 순서대로 선언할 필요 없다.

</aside>