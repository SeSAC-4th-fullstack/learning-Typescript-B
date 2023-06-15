# Chapter 04

> 객체
> 

# 1. 객체타입

---

### 1) 객체 선언

```tsx
const poet = {
    born: 1995,
    name: "Kim",
};

poet['born'];
poet.name;

poet.end;  // Error : poet에는 없는 속성이다.
```

- 객체에서 정의되지 않은 속성은 사용할 수 없다.

### 2) 객체의 타입 선언

```tsx
let poetLater: {
    born: number;
    name: string;
};

poetLater = {
    born: 1995,
    name: "Mark"
}

poetLater = "kim"  // Error
```

- 변수의 타입이 정해지면, 그 타입에 맞게 변수를 지정해줘야한다.

### 3) 객체의 타입 선언: 별칭으로

```tsx
type Poet = {
    born: number;
    name: string;
};

let poetLater: Poet;

poetLater = {
    born: 1995,
    name: "Kim",
};

poetLater = "Park";  // Error 
```

- 타입의 별칭으로도 변수의 타입 설정이 가능하다.

# 2. 구조적 타이핑

---

1) 구조적 타이핑

```tsx
type WithFirstName = {
    firstName: string;
};
type WithLastName = {
    lastName: string;
};

const hasBoth = {
    firstName: "Kim",
    lastName: "Jooho"
};

let withFirstName: WithFirstName = hasBoth;
let withLastName: WithLastName = hasBoth;
```

- **구조적으로 타입화 (Structurally Typed)**
    
    : 타입스크립트의 타입 시스템은 구조적으로 타입화 되어 있다.
    
    : 타입을 충족하는 모든 값을 해당 타입의 값으로 사용할 수 있다.
    
- **덕 타이핑 (Duck Typing)**
    
    : 자바스크립트는 “덕 타입”이다.
    
    : 오리처럼 보이고 오리처럼 꽥꽥거리면, 오리일 것이다… 에서 유래
    
    : 런타임에서 사용될 때 까지 타입 검사하지 않음
    

### 2) 사용검사

```tsx
type FirstAndLastName = {
    first: string;
    last: string;
};

const player1: FirstAndLastName = {
    first: "Park",
    last: "Jisung",
};

const player2: FirstAndLastName = {
    first: "Son",   // Error : 해당 타입의 필수요소는 모두 가지고 있어야한다.
};

const player3: FirstAndLastName = {
    first: "Lee",
    last: 100,      // Error : 선언된 타입과 일치해야 한다.
};
```

- 타입이 정해지면,
    
    : 해당 타입의 필수요소는 모두 가지고 있어야 하고
    
    : 선언된 타입과 일치해야 한다.
    

### 3) 초과 속성검사

```tsx
type FirstAndLastName = {
    first: string;
    last: string;
};

const player4: FirstAndLastName = {
    first: "Jung",
    last: "Sunwoo",
    position: "FW", // Error: 해당 타입에서 선언되지 않은 변수는 포함할 수 없다.
};

const player5 = {
    first: "Jung",
    last: "Sunwoo",
    position: "FW", 
};

const MrSon: FirstAndLastName = player5;    // Ok
```

- 타입이 정해지면,
    
    : 해당 타입에서 선언되지 않은 변수는 포함할 수 없다.
    
    : 초과 속성 검사는 타입이 선언되는 위치에서 생성되는 객체에게만 일어난다.
    

### 4) 중첩된 객체타입 : *객체 안에 객체 형태로 구성되어 있는 경우*

```tsx
type Poem = {
    author: {
        firstName: string;
        lastName: string;
    };
    name: string;
};

const poemMatch1: Poem = {
    author: {
        firstName: "Kim",
        lastName: "Jinsoo",
    },
    name: "Park",
};

const poemMatch2: Poem = {
    author: {
        name: "Lee"     // Error
    },
    name: "Park",
};
```

```tsx
type Author = {
    firstName: string;
    lastName: string;
};
type Poem = {
    author: Author;
    name: string;
};

const poemMatch: Poem = {
    author: {
        name: "Lee",    // Error
    },
    name: "Park",
}

```

- 중첩된 객체 타입의 경우에도 ,
    
    객체 안에 생성된 객체의 타입들이 일치해야한다.
    
    그리고, 타입을 별칭으로 주면 코드가 더 간결해보인다.
    

### 5) 선택적 속성

```tsx
type Book = {
    author?: string;
    pages: number;
};

const book1: Book = {
    pages: 100,     // Ok
};
const book2: Book = {
    author: "Kim"   // Error : 필수 속성이 존재하지 않는다.
}
```

- ? 기호를 붙임으로서 타입의 필수값을 선택적인 값으로 바꿀 수 있다.

```tsx
type Writer = {
    author: string | undefined;
    editor?: string;
};

const writer1: Writer = {
    author: undefined,  // Ok
};

const writer2: Writer = {
    // Error : 필수 속성이 존재하지 않는다.
}
```

- undefined일지라도 필수 타입으로 설정되어 있으면 지정해줘야한다.

# 3. 객체 타입 유니언

---

### 1) 유추된 객체 타입 유니언

```tsx
const poem = Math.random() > 0.5
            ? {name: "Kim", age: 20}
            : {name: "Lee", married: true};

// 두가지 타입으로 유추된다.
// 1. {name: "Kim", age: 20, married?: boolean}
// 2. {name: "Kim", age?: 20, married: boolean}

const a1 = poem.name;    // string  
const a2 = poem.age;     // number | undefined
const a3 = poem.married; // boolean | undefined
```

- 같은 속성과 같은 타입은 타입을 정확히 설정하고,
    
    중복되지 않은 속성의 타입은 나누어서 유추한다.
    

### 2) 명시된 객체 타입 유니언

```tsx
type PoemWithPage = {
    name: string;
    pages: number;
};
type PoemWithRhymes = {
    name: string;
    rythems: boolean;
};
type Poem = PoemWithPage | PoemWithRhymes;

const poem: Poem = Math.random() > 0.5
                    ? {name: "Kim", pages: 20}
                    : {name: "Lee", rythems: true};

poem.name;

poem.pages;     // Error
poem.rythems    // Error
```

- 타입을 통해 정확히 명시된 경우, 유추하여 판단하지 않는다.
    
    → *코드의 안전성을 지킬 수 있다.*
    

### 3) 객체 타입 내로잉

```tsx
type PoemWithPage = {
    name: string;
    pages: number;
};
type PoemWithRhymes = {
    name: string;
    rythems: boolean;
};
type Poem = PoemWithPage | PoemWithRhymes;

const poem: Poem = Math.random() > 0.5
                    ? {name: "Kim", pages: 20}
                    : {name: "Lee", rythems: true};

if("pages" in poem) {
    poem.pages;     // Ok
}
```

- **“pages” in poem**
    
    : poem 변수 안에 pages라는 속성이 있는지 확인하는 구문이다.
    

### 4) 판별된 유니언 (discriminated union)

```tsx
type PoemWithPage = {
    name: string;
    pages: number;
    type: "pages";
};
type PoemWithRhymes = {
    name: string;
    rythems: boolean;
    type: "rythems";
};
type Poem = PoemWithPage | PoemWithRhymes;

const poem: Poem = Math.random() > 0.5
                    ? {name: "Kim", pages: 20, type: "pages"}
                    : {name: "Lee", rythems: true, type: "rythems"};

if(poem.type === "rythems") {
    poem.rythems;       // Ok
}
```

- type이라는 공통된 속성을 부여하고,
    
    type의 갑의 통해 해당 변수의 타입을 좁혀간다.
    

# 4. 교차 타입

---

### 1) 교차 타입

```tsx
type ArtWork = {
    genre: string;
    name: string;
};

type Writing = {
    pages: number;
    name: string;
};

type WrittenArt = ArtWork & Writing
```

- 여러가지 타입을 결합해 새로운 타입을 생성한다.

```tsx
type ShortPoem = {author: string} 
                & ({kigo: string; type: "haiku"} 
                    | {meter: number; type: "villanelle"})

const morningGlory: ShortPoem = {
    author: "Fukada",
    kigo:   "Morning Glory",
    type:   "haiku"   
};

const oneArt: ShortPoem = {
    author: "Kim",
    type:   "villanelle"

    // Error : meter 속성이 필요한다.
}
```

- author 속성은 꼭 필요하며, (kigo, type) 또는 (meter, type) 중 하나의 타입은 가지고 있어야 한다.

### 2) 교차타입의 위험성

```tsx
type ShortPoemBase = {author: string};
type Haiku = ShortPoemBase & {kigo: string; type: "haiku"};
type Villanelle = ShortPoemBase & {meter: number; type: "villanelle"};

type ShortPoem = Haiku | Villanelle;

const oneArt: ShortPoem = {
    author: "Kim",
    type:   "villanelle"

    // Error : meter 속성이
}
```

- 교차타입의 구조가 복잡해질수록 오류메세지도 복잡해진다.
- 따라서, 타입의 별칭을 설정하여 오류 메세지를 간결하게 할 수 있다.

### 3) never 타입

```tsx
type Student = string & number;

let student: Student;

// Error : student는 never 타입이다.
student = 0;
student = "Hello";
```

- 성립될 수 없는 타입을 never 타입이라고 한다.
- never 타입에는 어떠한 값도 들어올 수 없다.