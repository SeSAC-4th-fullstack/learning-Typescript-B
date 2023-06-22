# Chapter 08

> 클래스
> 

# 1. 클래스 메서드

---

> **클래스 인수**
> 

```tsx
class Greeter {
    greet(name: string) {
        console.log(`${name}, do your stuff!`);
    }
}

new Greeter().greet("Kim"); // Ok

new Greeter().greet();      // Error
```

- greet의 매개변수 name이 할당되지 않았다.

> **constructor (생성자)**
> 

```tsx
class Greeted {
    constructor(message: string) {
        console.log(`As I always say : ${message}`);
    }
}

new Greeted("Hell World");  // Ok

new Greeted();              // Error
```

- 생성자에 매개변수 message가 할당되지 않았다.

# 2. 클래스 속성

---

> **클래스 속성**
> 

```tsx
class FieldTrip {
    destination: string;

    constructor(destination: string) {
        this.destination = destination  // Ok
        console.log(`We're going to ${this.destination}`);

        this.nonexistent = destination; // Error
    }
}

const trip = new FieldTrip("Seoul");

trip.destination // Ok
trip.nonexistent // Error
```

- nonexistent 속성은 class에서 선언되지 않았기 때문에 사용할 수 없다.
- 따라서, 외부에서도 선언되지 않은 속성에 접근하려면 에러를 발생시킨다.

### 1) 함수 속성 ⛔

```tsx
class WithMethod {
    myMethod() { }
}

new WithMethod().myMethod === new WithMethod().myMethod;
```

```tsx
class WithProperty {
    myProperty: () => {}
}

new WithProperty().myProperty === new WithProperty().myProperty;
```

- 항상 클래스 인스턴스를 가리켜야 하는 화살표 함수에서는 this 스코프를 사용하면 
클래스 인스턴스당 새로운 함수를 생성하는 시간과 메모리 비용 측면에서 유리하다.

```tsx
class WithPropertyParameters {
    takesParameters = (input: boolean) => input ? "Yest" : "No";
}

const instance = new WithPropertyParameters();

instance.takesParameters(true);  // Ok
instance.takesParameters(123);   // Error
```

- takesParameter에는 boolean 타입의 매개변수가 와야 한다.

### 2) 초기화 검사

```tsx
class WithValue {
    immediate = 0;      // Ok 
    later: number;      // Ok (constructor에서 선언)
    mayBeUndefined: number | undefined      // Ok (undefined가 되는 것을 허용)
    unused: number;     // Error (선언되지 않음)

    constructor() {
        this.later = 1;
    }
}
```

- 타입스크립트에서는 다음 초기화 검사를 한다.
1. 속성에 값이 할당 되었는지
2. 생성자(constructor)를 통해서 생성이 되었는지
3.  undefined로 타입이 포함되었는지

```tsx
class MissingInitializer {
    property: string;
}

new MissingInitializer().property.length;
```

```tsx
// tsconfig.json
"strictPropertyInitialization": true
```

- 엄격한 초기화 검사가 실행되지 않으면 올바르게 컴파일 되지만, 자바스크립트 런타임시 에러를 발생시킨다.

> **확실하게 할당된 속성 (!)**
> 

*: 클래스 속성을 의도적으로 할당하지 않은 경우도 있다. 따라서 ! 를 추가하여 검사를 피한다.*

```tsx
class ActivitiesQueue {
    pending!: string[];

    initialize(pending: string[]){
        this.pending = pending;
    }

    next() {
        return this.pending.pop();
    }
}

const activities = new ActivitiesQueue();

activities.initialize(["Kim", "Lee", "Park"]);
activities.next();
```

- ! 어서션은 나중에 반드시 값이 선언될 것을 의미한다.

### 3) 선택적 속성 : ?

```tsx
class MissingInitializer {
    property?: string;
}

new MissingInitializer().property?.length;  // Ok
// property 타입 : string | undefined

new MissingInitializer().property.length    // Error
```

- 선택적 속성으로 정의된 속성은 생성자에서 값을 할당하지 않아도 된다.
- 그러나, 속성에 접근 할 때는 undefined 타입으로도 유추가 되므로 ? 를 붙여줘야 한다.

### 4) 읽기 전용 속성 : readonly

```tsx
class Quote {
    readonly text: string;
    
    constructor(text: string) {
        this.text = "";
    }

    emphasize() {
        this.text += "!";   // Error 
    }
}

const quote = new Quote("Hello");

quote.text = "hi"           // Error
```

- readonly가 붙은 읽기 전용 속성은 “선언된 위치” 또는 “생성자”에서만 초기값이 할당 될 수 있다.

```tsx
class RandomQuote {
    readonly explicit: string = "Hello";
    readonly implicit = "Bye";

    constructor() {
        if(Math.random() > 0.5) {
            this.explicit = "Hello my name is Kim"; // Ok
            this.implicit = "Bye See you later"     // Error : 타입이 맞지 않음
        }
    }
}

const quote = new RandomQuote();

quote.explicit; // 타입 string
quote.implicit; // 타입 "Bye"
```

- explicit는 string 타입에 초기값이 선언된거지만,
- implicit는 “Bye”라는 리터럴 타입으로 타입 정의 + 초기값 선언이 되었다.

# 3. 타입으로서의 클래스

---

> **클래스 타입**
> 

```tsx
class Teacher {
    sayHello() {
        console.log(`Hello Teacher!`);
    }
}

let teacher: Teacher;

teacher= new Teacher();

teacher = "Wahoo"   // Error: string은 Teacher 타입에 할당할 수 없다
```

```tsx
class SchoolBus {
    getAbilities() {
        return ["magic", "shapeshifing"];
    }
}

function withSchoolBus(bus: SchoolBus) {
    console.log(bus.getAbilities());
}

// Ok
withSchoolBus(new SchoolBus());

// Ok
// : SchoolBus와 같은 getAbilities 메서드를 가지고 있어 SchoolBus 타입에 할달 할 수 있다.
withSchoolBus({
    getAbilities: () => ["transmogrification"],
})

// Error
// : SchoolBus와 다른 getAbilities 메서드를 가지고 있어 SchoolBus 타입에 할달 할 수 없다.
withSchoolBus({
    getAbilities: () => 123
})
```

- 클래스의 동일한 멤버를 포함하는 모든 객체 타입을 클래스에 할당할 수 있는 것으로 간주합니다.

# 4. 클래스와 인터페이스

---

```tsx
interface Learner {
    name: string;
    study(hours: number): void;
}

// Ok
class Student implements Learner {
    name: string;

    constructor(name: string) {
        this.name = name;
    }

    study(hours: number) {
        for(let i=0; i<hours; i+=1) {
            console.log("... Studying")
        }
    }
}

// Error
class Slacker implements Learner {
    
}
```

- Leaner에 필요한 name, study(hours: number): void 속성이 빠져있다.

```tsx
interface Learner {
    name: string;
    study(hours: number): void;
}

class Slacker implements Learner {
    name;           // Error: any 타입
    study(hours);   // Error: 
}
```

- Learner를 implement 했더라도, 타입을 지정해주지 않으면 any 타입으로 간주하기 때문에 
에러를 발생시킨다.

### 1) 다중 인터페이스 구현

```tsx
interface Graded {
    grades: number[];
}

interface Reporter {
    report: () => string;
}

// Ok
class ReportCard implements Graded, Reporter {

    grades: number[];

    constructor(grades: number[]) {
        this.grades = grades;
    }

    report() {
        return this.grades.join(", ");
    }

}

// Error
class Empty implements Graded, Reporter {

}
```

- 여러 개의 인터페이스를 implement 할 때는 , 로 이어서 추가한다.
- implement 하는 인터페이스의 속성을 모두 가져야 한다.

```tsx
interface AgeIsNumber {
    age: number;
}

interface AgeIsNotNumber {
    age: () => string;
}

class AsNumber implements AgeIsNumber, AgeIsNotNumber {
    // Error
    age = 0;    
}

class NotAsNumber implements AgeIsNumber, AgeIsNotNumber {
    // Error
    age() {
        return ""
    }
}
```

- implement하는 여러 개의 인터페이스에서 
**중복되는 속성**이 **서로 다른 타입**을 가지고 있으면 오류를 발생한다.

# 5. 클래스 확장

---

> **클래스 확장**
> 

```tsx
// 기본 클래스
class Teacher {
    teach() {
        console.log("Do your Best!");
    }
}

// 하위 클래스
class StudentTeacher extends Teacher {
    learn() {
        console.log("Hello World!");
    }
}

const teacher = new StudentTeacher();

teacher.teach();    // Ok : 기본 클래스(Teacher)에서 정의됨
teacher.learn();    // Ok : 하위 클래스(StudentTeacher)에서 정의됨

teacher.other();    // Ok : 기본, 하위에서 정의 되지 않은 메서드
```

- extends한 클래스에도 접근 할 수 있다.

### 1) 할당 가능성 확장

```tsx
class Lesson {
    subject: string;

    constructor(subject: string) {
        this.subject = subject;
    }
}

class OnlineLesson extends Lesson {
    url: string;

    constructor(subject: string, url: string) {
        super(subject);
        this.url = url
    }
}

let lesson: Lesson;
lesson = new Lesson("coding");                      // Ok
lesson = new OnlineLesson("coding", "naver.com");   // Ok

let online: OnlineLesson;
online = new OnlineLesson("coding", "naver.com");   // Ok
online = new Lesson("coding")                       // Error

// OnlineLesson 인스턴스는 Lesson 타입에 할당 할 수 있지만,
// Lesson 인스턴스는 OnlineLesson 타입에 할당 할 수 없다.
```

- 하위 클래스 → 상위클래스 : Ok
- 상위 클래스 → 하위클래스 : Error

> **상위 클래스 → 하위클래스 가 가능한 경우**
> 

```tsx
class PastGrades {
    grades: number[] = [];
}

class LabeledPastGrades extends PastGrades {
    label?: string;
}

let subClass: LabeledPastGrades;

subClass = new LabeledPastGrades(); // Ok
subClass = new PastGrades();        // Ok
```

- 하위 클래스가 선택적 속성만 추가하는 경우,
상위클래스를 하위클래스로 할당 할 수 있다.

### 2) 재정의된 생성자

```tsx
class GradAnnouner {
    message: string;

    constructor(grade: number) {
        this.message = grade >= 65
                        ? "Maybe next Time..."
                        : "You Pass!"
    }
}

class PassingAnnouncer extends GradAnnouner {
    constructor() {
        super(100)      // super : 기본 클래스의 생성자
    }
}

class FailAnnouncer extends GradAnnouner {
    // Error
    constructor() {
        
    }
}
```

- 하위 클래스 생성자는 this 또는 super에 접근하기 전에 
반드시 기본 클래스의 생성자를 호출해야한다.

```tsx
class GradesTally {
    grades: number[] = [];

    addGrades(...grades: number[]) {
        this.grades.push(...grades);
        
        return this.grades.length;
    }
}

class ContinuedGradesTally extends GradesTally {
    // Error
    constructor(previousGrades: number[]) {
        this.grades = [...previousGrades];
    }
    super();

    console.log("Starting with length ", this.grades.length);
}
```

- super을 호출하기 전에 this.grades 에 접근하지 못한다.

### 3) 재정의된 메서드

```tsx
class GradeCounter {
    countGrades(grades: string[], letter: string) {
        return grades.filter(grade => grade === letter).length;
    }
}

class FailureCounter extends GradeCounter {
    countGrades(grades: string[]) {
        return super.countGrades(grades, "F");
    }
}

class AnyFailureChecker extends GradeCounter {
    // Error
    countGrades(grades: string[]) {
        return super.countGrades(grades, "F") !== 0;   
        // number 타입이 반환되어야 한다.
    }
}

const counter: GradeCounter = new AnyFailureChecker();  // Error

// 예상한 타입: number
// 실제 타입 : boolean
const count = counter.countGrades(["A", "B", "C"]);   // Error
```

- 기본 클래스의 메서드 countGrades의 반환타입은 number이다 
그런데, AnyFailureChecker 클래스의 countGrades 메서드의 반환타입은 boolean이므로 
오류가 발생한다.

### 4) 재정의된 속성

```tsx
class Assignment {
    grade?: number;  // number | undefined
}

class GradedAssignment extends Assignment {
    grade: number;

    constructor(grade: number) {
        super();
        this.grade = grade;
    }
}
```

- 하위 클래스는 상위 클래스의 속성을 받을 수 있다.
- 하위 클래스는 상위 클래스의 속성 타입을 좁혀서 사용할 수 있다.
- 그러나, 하위 클래스는 상위 클래스의 **속성 타입을 넓게 사용할 수 없다.**

```tsx
class NumericGrade {
    value = 0;
}

class VagueGrade extends NumericGrade {
    // Error
    value = Math.random() > 0.5 
            ? 1
            : "Hello"

}

const instance: NumericGrade = new VagueGrade();

instance.value;
```

- 기본클래스의 value 속성은 number 타입이여야 한다.
- 그러나 하위 클래스 VagueGrade의 value 속성은 number | string 타입으로 오류가 발생한다.

# 6. 추상 클래스

---

> 추상클래스
> 

*: 기본 클래스에서 메서드의 구현을 하지 않고, 하위 클래스에서 메서드를 구현할 것을 예상한다.*

```tsx
abstract class School {
    readonly name: string;

    constructor(name: string) {
        this.name = name;
    }

    // 하위 클래스에서 정의 될 것이다.
    abstract getStudentTypes(): string[];
}

class Preschool extends School {
    getStudentTypes() {
        return ["preschool"];
    }
}

class Absence extends School {
    
    // Error
    // 기본 클래스에 존재하는 추상 메서드를 구현하지 않았다.

}

let school:School;

school = new Preschool("Kim");  // Ok

school = new School("Lee");     // Error
```

- 추상 클래스는 인스턴스로 만들 수 없다.

# 7. 멤버 접근성

---

> **pulic (기본값)**       : 모든곳에서 누구나 접근 
**protected**             : 클래스 내부 또는 하위 클래스에서만 접근 가능
**private**                  : 클래스 내부에서만 접근 가능
> 

```tsx
class Base {
    isPulicImplicit = 0;            // public (default)
    public isPublicExplicit = 1;    // public
    protected isProtected = 2;      // protected
    private isPrivate = 3;          // private
    #truePrivate = 4;               // private (#)
}

class Subclass extends Base {
    examples() {
        this.isPulicImplicit;   // Ok
        this.isPublicExplicit   // Ok
        this.isProtected;       // Ok

        this.isPrivate      // Error
        this.truePrivate    // Error
    }
}

new Subclass().isPulicImplicit; // Ok
new Subclass().isPublicExplicit // Ok

new Subclass().isProtected      // Error: Protected는 하위 클래스에서만 사용이 가능
new Subclass().isPrivate        // Error
```

- #으로 private을 지정할 수 있다.

```tsx
class TwoKeywords {
    private readonly name: string;

    constructor() {
        this.name = "Kim";
    }

    log() {
        console.log(this.name);
    }
}

const two = new TwoKeywords();

two.name = "Lee";   // Error
```

- protected 또는 private으로 선언된 타입스크립트 클래스 멤버는 암묵적으로 public으로 선언된 것처럼 동일한 자바스크립트 코드로 컴파일 됩니다.
- 자바스크립트 런타임에서는 # pirvate 필드만 진정한 private 입니다.

### 1) 정적 필드 제한자

```tsx
class Question {
    protected static readonly answer: "bash";
    protected static readonly prompt = 
        "What's an ogre's favorite programmin language";

    guess(getAnswer: (prompt: string) => string) {
        const answer = getAnswer(Question.prompt);

        // Ok
        if(answer === Question.answer) {
            console.log("Success");
        } else {
            console.log("Fail")
        }

    }
}

Question.answer; // Error
```

- 작성 순서
: 접근성 키워드(protected, private..) → static → readonly
-