# Chapter 04

> 객체
> 

## 1. 객체타입

1) 객체 선언

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled.png)

<aside>
💡 객체에서 정의되지 않은 속성은 사용할 수 없다.

</aside>

2) 객체의 타입 선언

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%201.png)

<aside>
💡 변수의 타입이 정해지면, 그 타입에 맞게 변수를 지정해줘야한다.

</aside>

3) 객체의 타입 선언: 별칭으로

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%202.png)

<aside>
💡 타입의 별칭으로도 변수의 타입 설정이 가능하다.

</aside>

---

## 2. 구조적 타이핑

1) 구조적 타이핑

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%203.png)

<aside>
💡 **구조적으로 타입화 (Structurally Typed)**

: 타입스크립트의 타입 시스템은 구조적으로 타입화 되어 있다.

: 타입을 충족하는 모든 값을 해당 타입의 값으로 사용할 수 있다.

</aside>

<aside>
💡 **덕 타이핑 (Duck Typing)**

: 오리처럼 보이고 오리처럼 꽥꽥거리면, 오리일 것이다… 에서 유래

: 런타임에서 사용될때 까지 타입 검사하지 않음

→ *자바스크립트는 ‘덕타입’이다.*

</aside>

2) 사용검사

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%204.png)

<aside>
💡 타입이 정해지면,

1. 해당 타입의 필수요소는 모두 가지고 있어야 하고

2. 선언된 타입과 일치해야 한다.

</aside>

3) 초과 속성검사

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%205.png)

<aside>
💡 타입이 정해지면,

3. 해당 타입에서 선언되지 않은 변수는 포함할 수 없다.

4. 초과속성 검사는 타입이 선언되는 위치에서 생성되는 객체에게만 일어난다.

</aside>

4) 중첩된 객체타입 : *객체 안에 객체 형태로 구성되어 있는 경우*

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%206.png)

<aside>
💡 중첩된 객체 타입의 경우에도 ,

객체 안에 생성된 객체의 타입들이 일치해야한다.

그리고, 타입을 별칭으로 주면 코드가 더 간결해보인다.

</aside>

5) 선택적 속성

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%207.png)

<aside>
💡 ? 기호를 붙임으로서 타입의 필수값을 선택적인 값으로 바꿀 수 있다.

</aside>

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%208.png)

<aside>
💡 undefined일지라도 필수 타입으로 설정되어 있으면 지정해줘야한다.

</aside>

---

## 3. 객체 타입 유니언

1) 유추된 객체 타입 유니언

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%209.png)

<aside>
💡 같은 속성과 같은 타입은 타입을 정확히 설정하고,

중복되지 않은 속성의 타입은 나누어서 유추한다.

</aside>

2) 명시된 객체 타입 유니언

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%2010.png)

<aside>
💡 타입을 통해 정확히 명시된 경우, 유추하여 판단하지 않는다.

→ *코드의 안전성을 지킬 수 있다.*

</aside>

3) 객체 타입 내로잉

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%2011.png)

<aside>
💡 **“pages” in poem**

: poem 변수 안에 pages라는 속성이 있는지 확인하는 구문이다.

</aside>

4) 판별된 유니언 (discriminated union)

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%2012.png)

<aside>
💡 type이라는 공통된 속성을 부여하고,

type의 갑의 통해 해당 변수의 타입을 좁혀간다.

</aside>

---

## 4. 교차 타입

1) 교차 타입

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%2013.png)

<aside>
💡 여러가지 타입을 결합해 새로운 타입을 생성한다.

</aside>

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%2014.png)

<aside>
💡 author 속성은 꼭 필요하며, (kigo, type) 또는 (meter, type) 중 하나의 타입은 가지고 있어야한다.

</aside>

2) 교차타입의 위험성

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%2015.png)

<aside>
💡 교차타입의 구조가 복잡해질수록 오류메세지도 복잡해진다.

따라서, 타입의 별칭을 설정하여 오류 메세지를 간결하게 할 수 있다.

</aside>

3) never 타입

![Untitled](Chapter%2004%20b8b385ed95164c2eb3fd926a7f3bf0d3/Untitled%2016.png)

<aside>
💡 성립될 수 없는 타입을 never 타입이라고 한다.

never 타입에는 어떠한 값도 들어올 수 없다.

</aside>