# Chapter 08

## 8.1 클래스 메서드

타입스크립트는 독립 함수를 이해하는 것과 동일한 방식으로 메서드를 이해한다.

```tsx
class Greeter {
	greet(name: string) {
		console.log(`${name}, do your stuff!`);
	}
}
```

클래스 생성자 constructor는 매개변수와 관련하여 클래스 메서드처럼 취급된다. 

인수가 올바른 수로 제공되는지 확인하기 위해 타입 검사를 실시한다. 즉, 클래스의 인스턴스 생성에 관여하고 올바르게 생성되는지 검사한다.

```tsx
class Greeted {
	constructor(message: string) {
		console.log(`I said ${message}`);
	}
}

new Greeted("take chances, make mistakes, get messy"); // OK
new Greeted(); // Error
```

## 8.2 클래스 속성

클래스의 속성을 읽거나 쓰려면 클래스에 명시적으로 선언해야 한다. 클래스의 속성은 인터페이스와 동일한 구문을 사용하여 작성할 수 있다.

클래스 속성은 다음 4가지 특징이 있다.

- **클래스 속성**
    - **함수 속성**: 메서드 선언 구문, 속성 선언 구문
    - **초기화 검사**: 속성 선언후 초기화 되지 않은 속성들은 클래스 생성자 내부에서 할당되었는지 검사
    - **선택적 속성**: ? 연산자 사용
    - **읽기 전용 속성**: readonly 키워드 사용

### 함수 속성

클래스의 멤버를 호출 가능한 함수로 선언하는 2가지 방법이 있다.

- 메서드 선언
- 속성 선언

메서드 선언은 클래스 프로토타입에 할당되기 때문에 모든 인스턴스가 동일한 함수 정의를 사용하지만, 속성 선언은 클래스 인스턴스마다 할당되기 때문에 모든 인스턴스가 동일한 함수 정의를 사용하지 않는다.

```tsx
class WithMethod {
	myMethod() {}
}

class WithProperty{
	myProperty: () => {}
}

new WithMethod().myMethod === new WithMethod().myMethod // true
new WithProperty().myProperty === new WithProperty().myProperty // false
```

### 초기화 검사

클래스 멤버가, 클래스 생성자 안에서, 초기화 되지 않으면 타입 오류를 내보낸다.

이러한 기능은 *‘엄격한 컴파일러 설정’* 이 활성화 되어야 동작한다.

```tsx
class WithValue {
	immediate = 0; // OK
	later: number; // OK(constructor에서 초기화 됨)
	mayBeUndefined: number | undefined; // OK(undefined 허용)
	unused: number; // Error.
	// 초기화 안됨, constructor에서도 초기화 안됨

	constructor() {
		this.later = 1;
	}
}
```

### 선택적 속성

선택적 속성은 | undefined를 포함하는 유니언 타입과 거의 동일하게 작동한다.

초기화 검사는 선택적 속성을 명시적으로 설정하지 않아도 문제 없이 넘어간다.

```tsx
class MissingInitializer{
	property?: string;
}

new MissingInitializer().property?.length; // OK
new MissingInitializer().property.length; // Error.
// property가 undefined이면 length를 알 수 없음
```

### 읽기 전용 속성

 클래스의 읽기 전용 속성은 readonly 키워드를 붙여서 선언할 수 있다.

```tsx
class Quote {
    readonly text: string;

    constructor(text: string) {
        this.text = '';
    }
}

const quote = new Quote ("Tomorrow is a lie");
quote.text = "Hi!"; // Error
```

```tsx
class RandomQuote {
    readonly explicit: string 
    = "The opposite for courage is not cowardice, it is conformity.";
    readonly implicit
    = "They may forget what you said, but they never forget how you made them feel.";

    constructor() {
        if(Math.random() > 0.5) {
            this.explicit = "Let's go DDC.";
            this.implicit = "No Way"; // Error
        }
    }
}

const quote = new RandomQuote();

quote.explicit; // 타입: string
quote.implicit; // 타입: 리터럴
```

## 8.3 타입으로서의 클래스

클래스도 타입 애너테이션에서 사용할 수 있다.

타입스크립트는 클래스의 모든 멤버와 동일한 객체를 클래스에 할당할 수 있는 것으로 간주한다.

```tsx
class SchoolBus {
    getAbilities() {
        return ["magic", "shape"];
    }
}

function withSchoolBus(bus: SchoolBus) {
    console.log(bus.getAbilities());
}

withSchoolBus(new SchoolBus()); // OK(bus가 SchoolBus 클래스 타입)

// 클래스의 멤버를 모두 포함하는 객체 타입을 할당.
// 타입스크립트는 동일한 멤버를 모두 포함하는 객체를 클래스에 할당할 수 있는 것으로 간주함.
withSchoolBus({
    getAbilities: () => ["transmogrification"], // OK(SchoolBus 클래스로 간주)
});

withSchoolBus(
    getAbilities: () => 123, // Error: SchoolBus 클래스와 멤버가 불일치하므로 타입 오류.
);
```

## 8.4 클래스와 인터페이스

클래스 뒤에 implements 키워드를 사용하여 인터페이스 이름을 넣으면, 클래스의 해당 인스턴스가 인터페이스를 준수한다고 선언할 수 있다.

```tsx
interface Learner {
    name: string;
    study(hours: number): void;
}

class Student implements Learner {
    name: string;

    constructor(name: string) {
        this.name = name;
    }

    study(hours: number) {
        for(let i = 0; i < hours; i += 1) {
            console.log("...studying...\n");
        }
    }
}

class Slacker implements Learner { // Error..
    name = "Rocky";

    // study 메서드 없음
}
```

extends 키워드처럼, 쉼표를 이용하여 여러 개의 인터페이스를 implements 할 수 있다.

여러 개의 인터페이스를 implements 하려면 클래스가 인터페이스의 속성을 모두 지녀야 한다.

```tsx
interface Graded {
	grades: number[];
}

interface Reporter {
	report: () => string;
}

class ReportCard implements Graded, Reporter {
	grades: number[];
	constructor(grades: number[]) {
		this.grades = grades;
	}
	report() {
		return this.grades.join(", ");
	}
}
```

## 8.5 클래스 확장

타입스크립트는, 다른 클래스를 확장하거나 하위 클래스를 만드는 자바스크립트 개념에, 타입 검사를 추가한다.

기본 클래스에 선언된 모든 메서드나 속성은, 하위 클래스에서 사용할 수 있다.

```tsx
class Teacher {
	teach() {
		console.log("The surest test of discipline is its absence.");
	}
}

class StudentTeacher extends Teacher {
	learn() {
		console.log("I cannot afford the luxury of a closed mind.");
	}
}

const teacher = new StudentTeacher();
teacher.teach(); // OK.. 기본 클래스에 선언됨
teacher.learn(); // OK.. 하위 클래스에 선언됨
```

즉, 하위 클래스는 기본 클래스의 멤버를 상속 받는다.

하위 클래스의 인스턴스는 기본 클래스의 모든 멤버를 가지므로 기본 클래스의 인스턴스가 필요한 곳에서 사용할 수 있다.

```tsx
class Lesson {
	subject: string;
	constructor(subject: string) {
		this.subject = subject;
	}
} // 기본 클래스 선언

class OnlineLesson extends Lesson {
	url: string;
	constructor(subject: string, url: string) {
		super(subject);
		this.url = url;
	}
} // 하위 클래스 선언

let lesson: Lesson;
lesson = new Lesson("coding"); // OK
lesson = new OnlineLesson("coding", "mdn.com"); // OK
// 하위 클래스의 인스턴스는 기본 클래스의 멤버를 모두 상속받음.

let online: OnlineLesson;
online = new Lesson("coding"); // Error:
// 기본 클래스에서 하위 클래스가 갖는 멤버가 없음.

online = new OnlineLesson("coding", "sessac.com"); // OK
```

하위 클래스에서는 재정의된 생성자, 재정의된 메서드, 재정의된 속성을 사용할 수 있다.

- 하위 클래스
    - 재정의된 생성자: 하위 클래스에서 자체적으로 생성자를 선언 가능
    - 재정의된 메서드: 기본 클래스와 동일한 이름의 메서드에, 새 메서드를 다시 선언 가능
    - 재정의된 속성: 기본 클래스와 동일한 이름의 속성에, 할당 가능한 타입을 다시 선언 가능

### 재정의된 생성자

하위 클래스에서 자체적으로 생성자를 선언 가능하다.

하위 클래스가 자체적으로 생성자를 갖기 위해서는, super 키워드를 통해 기본 클래스 생성자를 먼저 호출해야 한다.

하위 클래스에서 자체적으로 생성자를 갖지 않으면, 암묵적으로 기본 클래스의 생성자를 사용한다.

*주의: 자바스크립트 규칙에 따라, super 키워드를 호출하기 전에 this를 사용할 수 없다.*

```tsx
class GradeAnnouncer {
	message: string;
	constructor(grade: string) {
		this.message = grade >= 65 ? "You Pass!!" : "Next time..";
	}
}

class PassingAnnouncer extends GradeAnnouncer {
	constructor() {
		super(100);
	}
}

class FailingAnnouncer extends GradeAnnouncer {
	constructor() {} // Error: 하위 클래스 생성자는 반드시 super()를 호출해야 함.
}
```

### 재정의된 메서드

기본 클래스와 동일한 이름의 메서드에, 새 메서드를 다시 선언 가능하다.

하위 클래스에서 메서드를 선언하는 행위 자체가 재정의된 메서드를 의미한다. 왜냐하면, 클래스 확장 때문에, 하위 클래스 메서드는 기본 클래스의 메서드를 모두 상속받기 때문이다.

*주의: 재정의된 메서드의 타입은 기본 클래스에서 동일한 이름의 메서드 대신 사용할 수 있도록 해야 한다.*

```tsx
class GradeCounter {
	countGrades(grades: string[], letter: string) {
		return grades.filter(grade => grade === letter).length;
	} // 타입: (string[], string) => number
}

class FailureCounter extends GradeCounter {
	countGrades(grades: string[]) {
		return super.countGrades(grades, "F");
	} // 타입: (string[]) => number
}

class AnyFailureChecker extends GradeCounter {
	countGrades(grades: string[]) {
		return super.countGrades(grades, "F") !== 0;
	} // 타입: (stringp[]) => boolean
		// Error.
}
```

### 재정의된 속성

기본 클래스와 동일한 이름의 속성에, 할당 가능한 타입을 다시 선언 가능하다.

재정의된 메서드와 마찬가지로 기본 클래스와 구조적으로 일치해야 한다. 다시 말해서, 기본 클래스 속성의 타입에서 뻣나가는(?) 타입은 하위 클래스에서 할당할 수 없다.

```tsx
class Assignment {
	grade?: number; // 타입: number | undefined
}

class GradedAssignment extends Assignment {
	grade: number; // OK(타입이 number | undefined에서 -> number로 재정의 됨)
	constructor(grade: number) {
		super();
		this.grade = grade;
	}
}
```

```tsx
class NumericGrade {
	value: number;
}

class VagueGrade extends NumericGrade {
	value = Math.random() > 0.5 ? 1 : "non"; // Error
	// 타입이 number에서 number | string 할당 불가
}
```

## 8.6 추상 클래스

추상화 메서드 선언은 기본 클래스에서 메서드의 본문을 생략하고, 하위 클래스에서 메서드의 본문을 정의하는 것이다.

추상화는, 추상화 하려는 클래스와 메서드 앞에 abstract 키워드를 붙여서 선언할 수 있다.

```tsx
abstract class School { // 추상화 클래스
	readonly name: string; // this.name과 같은 변수
	
	constructor(name: string) { // 클래스 생성시에 입력받는 매개변수
		this.name = name;
	}

	abstract getStudentTypes(): string[]; // 추상화 메서드
}

class Preschool extends School {
	getStudentTypes() {
		return ["preschooler"];
	}
}

class Absence extends School { // Error: 추상화 클래스 School의 하위 클래스 Absence는 
	// 추상화 메서드를 반드시 정의해야 함.

	// 공백
}
```

추상 클래스는 타입 애너테이션으로 해당 클래스 타입이라는 사실을 기재할 수 있지만 직접 인스턴스화 할 수 없다.왜냐하면, 추상화 메서드가 명확하게 정의되어 있지 않기 때문이다.

```tsx
// 계속 이어서..

let school: School;

school = new Preschool("Sunnyside Daycare"); // OK
school = new School("Seoul Elementary School"); // Error
```

## 8.7 멤버 접근성

자바스크립트에서는 클래스 멤버 이름 앞에 #을 추가해 private 클래스 멤버임을 나타낸다. private 클래스 멤버는 해당 클래스의 인스턴스에서만 접근할 수 있다.

타입스크립트 역시 private 클래스 멤버, public 클래스 멤버, protected 클래스 멤버를 지원한다. 

하지만 이러한 키워드들은 타입 시스템에만 존재한다. 그러므로 이러한 키워드들은 자바스크립트 코드 컴파일 때 제거된다.

게다가, # private 와 같은 구문은 타입스크립트에서 사용할 수 없다. 왜냐하면, #과 private 키워드 모두 같은 성격의 클래스 멤버를 가리키기 때문이다.

아래의 멤버 접근성 키워드는 readonly와 함께 사용할 수 있다.

- private: 클래스 내부에서만 접근 가능
- public: 모든 곳에서 누구나 접근 가능
- protected: 클래스 내부 또는 하위 클래스에서만 접근 가능

```tsx
class Base {
	isPublicImplicit = 0;
	public isPublicExplicit = 1;
	protected isProtected = 2;
	private isPrivate = 3;
	#realPrivate = 4;
}

class Subclass extends Base {
	examples() {
		this.isPublicImplicit;
		this.isPublicExplicit;
		this.isProtected;
		this.isPrivate; // Error
		this.#realPrivate; // Error
	}
}

new Subclass().isPublicImplicit; // OK
new Subclass().isPublicExplicit; // OK
new Subclass().isProtected; // Error
new Subclass().isPrivate; // Error
new Subclass().#realPrivate; // Error
```

자바스크립트는 static 키워드를 사용해 클래스 자체에서 멤버를 선언할 수 있다. 타입스크립트 역시 static 키워드를 지원한다.

타입스크립트의 static 키워드는 readonly같은 접근성 키워드와 함께 사용할 수 있다. 작성 순서는 static, readonly 순으로 써야한다.

```tsx
class Question {
	protected static readonly answer: "bash";
	protected static readonly prompt = "What is under shell of sh?";

	guess(getAnswer: (prompt: string) => string) {
		const answer = getAnswer(Question.prompt);

        if(answer === Question.answer) {
            console.log("You got it!!");
        }
        else {
            console.log("Try Again..");
        }
	}
}

Question.answer; // Error.. answer은 protected 속성이기 때문에 클래스 외부에서 접근 불가
```