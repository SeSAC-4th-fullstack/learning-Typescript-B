# Chapter 02

> 타입 시스템
> 

## 1. 타입의 종류

1) 원시 타입

| 타입 | 예 |
| --- | --- |
| null |  |
| undefined |  |
| boolean | true, false |
| string | “”, “Hello World” |
| number | 1, 3, 0.1, -5 |
| bigint | 0n, 2n, -4n |
| symbol | Symbol(), Symbol(“hi”) |

2) 타입 해석 과정

<aside>
1️⃣ 코드를 읽고 존재하는 모든 타입과 값을 이해한다.

</aside>

<aside>
2️⃣ 각 값이 초기 선언에서 가질 수 있는 타입을 확인합니다.

</aside>

<aside>
3️⃣ 각 값이 추후 코드에서 어떻게 사용될 수 있는지 모든 방법을 확인합니다.

</aside>

<aside>
4️⃣ 값의 사용법이 타입과 일치하지 않으면 사용자에게 오류 표시합니다.

</aside>

3) 오류의 종류

<aside>
💡 **구문오류**

: 타입스크립트가 자바스크립트로 변환되는 것을 차단한 경우

</aside>

<aside>
💡 **타입오류**

: 타입 검사기에 따라 일치하지 않는 것이 감지된 경우

: 단, 타입에 오류가 있다고 해서 Javascriptㅀ로 변환을 차단하지는 않는다.

</aside>

*“JS 코드를 생성하기 전에 타입 검사를 진행하는 것이 좋다!”*

---

## **2. 할당 가능성**

1) 할당 가능성

<aside>
💡 **할당 가능성**
: 함수 호출이나 변수에 값을 제공할 수 있는지 여부를 확인하는 것.

</aside>

2) 할당 가능성 오류

<aside>
💡 **<Type1> is not assignable to type <Type2>**

: Type1 -> 할당을 시도하는 타입

: Type2 -> 이미 할당되어 있는 타입

</aside>

---

## **3. 타입 애너테이션 (Type Annotation)**

1) 진화하는 Any

![Untitled](Chapter%2002%20490c309c67e743109a3e060080801bad/Untitled.png)

<aside>
💡 변수에 초기 값이 선언되지 않으면, 타입은 Any 타입으로 지정되고

할당되는 값에 따라 타입이 계속 변하게 된다.

→  *타입스크립트의 타입검사가 쓸모 없어진다.*

</aside>

2) 타입 애너테이션 (:)

![Untitled](Chapter%2002%20490c309c67e743109a3e060080801bad/Untitled%201.png)

---

## **4. 타입의 형태**

1) ECMA 2015

<aside>
💡 **ECMA 2015**

: 파일 간에 가져오고(export) 내보내는(import) 구문을 표준화하기 위해 ECMA 스크립트 모듈이 추가 되었다.

</aside>

2) 모듈  & 스크립트

![Untitled](Chapter%2002%20490c309c67e743109a3e060080801bad/Untitled%202.png)

<aside>
💡 한 모듈에서 선언된 변수가 같을지라도, 다른 파일에서 가져오지(import) 않는 이상 충돌 나지 않는다.

그러나, 파일이 스크립트이면 해당 파일을 전역 스코프로 간주하므로, 충돌이 발생한다.

</aside>