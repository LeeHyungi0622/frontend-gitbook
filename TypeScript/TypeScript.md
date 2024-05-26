### (참고) TypeScript 공식 사이트
- https://www.typescriptlang.org/ko/docs/handbook/intro.html


### TypeScript

    ```typescript
    let name: string;
    let age: number;

    name = '이현기';
    age = 13;

    // 아래 두 값 대입에서 에러 발생
    name = 13;
    age = '이현기';

    let human: {
        name: string,
        age: number
    }

    human = {name: '이현기', age: 35}
    ```

    #### 타입 정의하기 (Defining Types)

```typescript
type Human = {
    name: string;
    age: number;
};

let boy: Human;

boy = { name: '이현기', age: 13 };

interface Person {
    name: string;
    age: number;
}

let girl: Person;

girl = { name: '이현기', age: 13};

function add(x: number, y: number): number {
    return x + y;
}

add(1, 2)

add('Hello', 'World')

function sub(x: number, y: number):string {
    return x - y;
}

// 정해진 값으로만 타입 지정하기
let category: 'food';

category = 'food';

// 배열 타입 지정
let numbers: number[];

numbers = [1, 2, 3];

// 배열보다 깐깐하게 타입을 관리하기 위해 Tuple 사용하기
let anythings: any[];

anythings = ['hg', 256];

let pair: [string, number];

pair = ['hg', 256]
```

#### 타입 추론

```typescript
const name: string = '이현기'

// 타입 추론이 가능한 변수의 경우에는 타입 정의를 생략해도 된다.
const name = '이현기';
```

#### Union Type

```typescript
type bool = true | false;

let flag: bool;

flag = true;

flag = false;

// type error
flag = 3;

// 매개변수를 제한하거나 할때 유용하게 사용
type Category = 'food' | 'toy' | 'bag';

function fetchProducts({ category } : { category: Category }) {
    console.log(`Fetch ${category}`)
}

let target: string | number;

target = '이현기';

target = 13;

// type error
target = null;

// type error
target = undefined;

let targetName: string | undefined;

targetName = '이현기';

targetName = undefined;
```

#### Optional Parameter 지정하기

```typescript
// 기본값 잡아주는 방법1
function greeting(name?: string): string {
    return `Hello, ${name || 'world'}`;
}

// 기본값 잡아주는 방법2
function greeting(name?: string = 'world'): string {
    return `Hello, ${name}`;
}

function greeting({ name, age } : { name: string; age?: number; }): string {
    return age ? `${name} (${age})` : name;
}

greeting()

greeting({ name: '이현기' })
greeting({ name: '이현기', age: 33 })
```

#### 타입 확장하기

```typescript
type Human = {
    name: string;
    age: number;
};

type Creature = {
    hp: number;
    mp: number;
};

type Person = Human & Creature;

let person:l Person;

person = { name: '이현기', age: 33, hp: 01063204954, mp: 01063204954 };
```

#### Generic 활용하기

```typescript
function identity<Type>(arg: Type): Type {
    return arg;
}
```