아래 작성한 내용은 반복 연습을 통해서 익숙해져야하는 내용들이다.
(이전에 학습한 Test 코드와 관련된 내용들을 복습하며, 아래와 같이 명확하게 순서를 인지하며 반복 숙달한다.)

### 테스트 코드 수정사항에 대해 즉시 반영하여 console에서 확인할 수 있도록 설정

```zsh
"watch:test": "jest --watchAll",
```

만약에 위의 `watch:test` script command를 실행했는데 아래와 같은 에러가 발생한다면,

```zsh
> frontend-survival-week07-answer@1.0.1 watch:test
> jest --watchAll

● Validation Error:

  Module <rootDir>/src/setupTests.ts in the setupFilesAfterEnv option was not found.
         <rootDir> is: /Users/mikelee/Documents/dev/self-study/assignment/7-week/frontend-survival-week07

  Configuration Documentation:
  https://jestjs.io/docs/configuration
```

jest.config.js 파일에서 정의한 setupTests.ts 파일에 대한 설정을 다시 확인해줘야 한다.

<u>test 관련해서 이외에 설정이 필요한 부분에 대해서는 추가적으로 정리를 하도록 하자.</u>

<br/>

`src/setupTests.ts`
```ts
import 'reflect-metadata';

// eslint-disable-next-line import/no-extraneous-dependencies
import 'whatwg-fetch';

import server from './mocks/server';

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));

afterAll(() => server.close());

afterEach(() => server.resetHandlers());
```

### mocks directory 생성 및 `handlers.ts`, `server.ts` 파일 작성

프론트엔드에서 개발할때 프론트엔드 프로젝트 내부 개발환경에서 API 요청을 Mocking하기 위해 사용되는 라이브러리입니다. 

MSW는 실제 API 서버와의 통신을 흉내내어, 개발자들이 네트워크 요청을 처리하고 테스트할 수 있도록 도와줍니다.

`handlers.ts` : 다양한 API 요청에 대한 응답을 정의

`server.ts` : 작성한 handlers 정보를 setupServer나 setupService 메서드를 사용해서 server 정의

```
1. 서비스 워커 기반
MSW는 서비스 워커(Service Worker)를 사용하여 클라이언트 측에서 API 요청을 가로채고, 이를 모킹된 응답으로 처리한다. 이로 인해, 네트워크 요청을 실제 서버로 보내지 않고도 테스트할 수 있습니다.

2. 유연한 Mocking
다양한 요청 메서드(GET, POST, PUT, DELETE 등)와 경로에 대해 모킹 응답을 정의할 수 있습니다. 이를 통해 다양한 API 시나리오를 쉽게 테스트할 수 있습니다.

3. 개발 및 테스트 환경 지원
MSW는 개발환경에서의 빠른 피드백 루프를 지원하며, 테스트 환경에서도 동일한 Mocking 설정을 사용할 수 있어, 일관된 테스트가 가능합니다.

4. 타사 서비스 모킹
외부 서비스(API)와의 통신을 Mocking하여, 외부 서비스의 상태나 응답에 의존하지 않고도 안정적인 테스트를 수행할 수 있습니다.
```

`handlers.ts`
```ts
// eslint-disable-next-line import/no-extraneous-dependencies
import { rest } from 'msw';

import fixtures from '../../fixtures';

const BASE_URL = 'http://localhost:3000';

const handlers = [
  rest.get(`${BASE_URL}/restaurants`, (req, res, ctx) => {
    const { restaurants } = fixtures;

    return res(
      ctx.status(200),
      ctx.json({ restaurants }),
    );
  }),
];

export default handlers;
```

`server.ts`

```ts
// eslint-disable-next-line import/no-extraneous-dependencies
import { setupServer } from 'msw/node';

import handlers from './handlers';

const server = setupServer(...handlers);

export default server;
```

- setupServer(본래 설정 파일에서 사용) : 이 메서드는 Node.js환경에서 모킹을 설정합니다. 주로 서버 사이드 렌더링(SSR) 애플리케이션이나 테스트 환경에서 사용됩니다.

- setupWorker : 이 메서드는 Service worker를 사용하여 브라우저 환경에서 Mocking을 설정합니다. 주로 클라이언트 사이드 애플리케이션(웹 브라우저)에서 사용됩니다.

### <u>setupService()로 웹 브라우저에서 서비스 워커 구동시 동작</u>

CSR 애플리케이션 구동시에 브라우저상에서

`worker.start()`가 호출되면, MSW 서비스 워커가 등록되고, 활성화된 후에 이후의 모든 네트워크 요청에 대해 MSW에 의해 가로채지고, 설정된 핸들러에 따라 응답이 반환된다.

```js
// 아래와 같이 웹 브라우저 환경에서 Mocking을 설정할때 사용된다.
import { worker } from './mocks/browser';

// 개발 환경에서만 서비스 워커 시작
if (process.env.NODE_ENV === 'development') {
  worker.start();
}

// 이를 통해 애플리케이션이 프로덕션 환경에서는 실제 서버와 통신하도록 하며, 개발 환경에서만 모킹된 응답을 사용하도록 한다.
```

<br/>

간단하게 정리하면, Node.js 환경에서 테스트를 설정하려면, `setupServer`메서드를 사용하며, 환경과 용도에 따라서 특정 환경에 맞게 MSW를 설정하는데 사용됩니다.


### Service worker

#### 모킹을 위해 MSW와 일반 서비스워크를 둘 다 사용하는 경우, 아래와 같이 디랙토리 구조를 가져간다.

```
/project-root
│
├── /public
│   ├── index.html
│   ├── service-worker.js
│   └── manifest.json
│
├── /src
│   ├── /components
│   │   └── App.js
│   │
│   ├── /mocks (if using MSW for mocking)
│   │   ├── browser.js
│   │   ├── handlers.js
│   │   └── server.js
│   │
│   ├── /utils
│   │   └── pushSubscription.js
│   │
│   ├── index.js
│   ├── App.js
│   └── setupTests.js (if using Jest for testing)
│
├── package.json
└── README.md

```

`setupWorker`를 사용하여 설정한 서비스워커와 위에서 설명한 `일반적인 Service worker`는 같은 개념입니다. 

둘 다 웹 애플리케이션의 백그라운드에서 실행되며, 네트워크 요청을 가로채고 처리할 수 있습니다. 그러나 그들의 목적과 사용 방식에는 차이가 있습니다.

#### 목적
- MSW : 주로 개발 및 테스트 환경에서 API요청을 모킹하여, 실제 서버와의 통신 없이 클라이언트 측 코드를 테스트하거나 개발할 수 있도록 합니다.

- 일반 서비스 워커 : 주로 프로덕션 환경에서 애플리케이션의 성능 향상, 오프라인 지원, 캐싱 및 푸시 알림 등의 기능을 제공하기 위해서 사용됩니다.

#### 설정 및 사용
- MSW : `setupWorker`를 통해 서비스 워커를 설정하고, 다양한 네트워크 요청에 대해 미리 정의된 응답을 반환하도록 구성합니다.

- 일반 서비스 워커 : `serviceWorker.register`를 통해 등록되고, 주로 캐싱 전략, 백그라운드 동작, 오프라인 지원 등을 위해 사용됩니다.

#### 핸들링
- MSW : 네트워크 요청을 가로채고, 미리 정의된 핸들러를 통해 응답을 반환합니다.

- 일반 서비스 워커 : 네트워크 요청을 가로채고, 캐시된 자원이나 네트워크에서 응답을 가져오며, 오프라인 지원이나 성능 최적화를 목적으로 합니다.


<br/>

# (STEP 1) 기본 라우터 구성시 테스트 진행

우선 초기에는 가장 기본이 되는 Outline 잡는 작업을 한다.

모든 페이지의 최상단에는 별도의 페이지가 어떤 페이지인지 알 수 있도록 Title text를 입력해주고, 해당 텍스트는 css를 사용해서 화면상에서는 숨기도록 처리한다.

```html
<div className="hidden" data-testid="hidden-element" aria-hidden="false">
    OOO 페이지 (화면상에는 표시되지만, 스크린 리더가 읽을 수 있는 텍스트)
</div>
```

```css
.hidden {
    display: none;
}
```

```js
// <App /> component 관련 테스트 코드
describe('App', () => {
    // 만약에 브라우저에 입력된 path가 '/'인 경우,
    context('when the current path is "/"', () => {
        // 우선 모든 컴포넌트들을 아우르는 <App /> component를 렌더링한다.
        it('renders the home page', () => {
            render((
                <MemoryRouter initialEntries={['/']}>
                <App />
                </MemoryRouter>
            ));
        });
        // <App /> component를 렌더링 하면, 화면에 있는 텍스트를 찾아
        // 맞는지 예상되는 결과와 맞는지 비교한다.
        const hiddenTitleElement = screen.getByTestId('hidden-element');

        // 요소가 DOM에 존재하는지 확인
        expect(hiddenTitleElement).toBeInTheDocument();

        // 요소가 올바른 텍스트를 포함하고 있는지 확인
        expect(hiddenTitleElement).toHaveTextContent('OOO 페이지 (화면상에는 표시되지만, 스크린 리더가 읽을 수 있는 텍스트)');
        
        // RTL screen을 사용해서 화면의 텍스트 확인
        screen.getByText('OOO 페이지 (화면상에는 표시되지만, 스크린 리더가 읽을 수 있는 텍스트');    
    });
});
```

페이지 기본 router를 구성할 때 위와 같이 작성을 해주나, 만약에 구조를 좀 더 세분화해서

Outlet을 적용한 Layout 컴포넌트를 빼는 경우,

아래와 같이 렌더링하는 처리를 별도의 함수로 빼서 공통처리를 해 줄 수 있다.

<br/>

`routes.test.tsx`
```ts
import { render, screen } from '@testing-library/react';

import { RouterProvider, createMemoryRouter } from 'react-router';
import routes from './routes';

const context = describe;

// <App /> component 관련 테스트 코드
describe('App', () => {
  // test helper 작성하기
  function renderRouter(path: string) {
    const router = createMemoryRouter(routes, { initialEntries: [path]});
    render(<RouterProvider router={router}/>);
  }
  
  // 만약에 브라우저에 입력된 path가 '/'인 경우,
  context('when the current path is "/"', () => {
    // 우선 모든 컴포넌트들을 아우르는 <App /> component를 렌더링한다.
    it('renders the home page', () => {
        renderRouter('/');  
        
        // <App /> component를 렌더링 하면, 화면에 있는 텍스트를 찾아
        // 맞는지 예상되는 결과와 맞는지 비교한다.
        const hiddenTitleText = screen.getByTestId('hidden-element');
        
        expect(hiddenTitleText).toBeInTheDocument();
        expect(hiddenTitleText).toHaveTextContent('홈');
        screen.getByText('홈');
      });
    });
    
    context('when the current path is "/about"', () => {
      renderRouter('/about');
    })
    it('renders without crash', () => {
      renderRouter('/about');
    });
});
```

# (중요!) 테스트 코드 작성하기 전 준비

add(x, y)와 같이 더하더가 하는 로직적인 부분을 interface(인터페이스)로 정의한다.

따라서 테스트 코드를 먼저 작성하는, 즉 구현보다는 인터페이스와 스펙을 먼저 정의함으로써 개발을 진행하는 방식이다.

- Red 

실패하는 테스트 코드를 작성한다. (인터페이스와 스펙에 집중)

- Green

재빨리 테스트를 통과시킨다. 죄악을 저질러도 괜찮다.

- Refactor

리팩터링을 통해 코드를 올바르게 만든다. (동작은 바꾸지 않고 코드를 바꾼다)

TDD에서 가장 중요한 부분이지만, 간과될 때가 많다.

1~10분 이내로 한 사이클 돌릴 수 있는 작은 단위를 찾아서 테스트 코드를 작성하고, Green 단계로 넘어간다. 

단, 만약에 Green 단계에서 테스트 코드를 작성하는 것이 어려운 경우에는 Red로 돌아가서 더 작고 쉬운 문제를 정의하고, Refactor 단계를 위해 의도를 드러내고 중복을 찾아서 제거하는 연습이 필요하다. 

`단순 TDD에 국한된 문제가 아니라 개발 자체에서 매우 중요한 한 부분으로, 전체 개발을 큰 하나의 부분으로 봤을 때 각 각의 작은 개발단위로 나눠서 이해하고 파악하는 것이 중요하다.`

`TDD FAQ` : [https://megaptera.notion.site/TDD-FAQ-edcaa36e8cf246d9a2aa546d99810202](https://megaptera.notion.site/TDD-FAQ-edcaa36e8cf246d9a2aa546d99810202)

`Jest를 이용한 간단한 TDD 예제` : [https://megaptera.notion.site/Jest-TDD-4149c9f23b2d4402ad44d4d569a3ae13](https://megaptera.notion.site/Jest-TDD-4149c9f23b2d4402ad44d4d569a3ae13)

## 도구를 사용하기 위한 학습

Facebook에서 만든 테스팅 도구인 `Jest`가 있다.

### <u>테스트 코드 파일 확장자</u>
App.spec.ts (`BDD 스타일의 테스트 코드 작성시 spec으로 네이밍 / 하지만 test.ts로 통일해서 사용해도 무방`)

### <u>테스트 코드 작성 방식 1 (Given-When-Then)</u>
어떤 상황인지 명확하게 파악할 수 있는 방식이다.

```
Given - 700와트 전자렌지를 준비하고, 3분이란 시간이 주어졌을 때
When - 주어진 시간동안 전자렌지를 돌리면
Then - 요리가 완성된다.
```

### <u>테스트 코드 작성 방식 2 (Describe-Context-it)</u>
BDD 스타일로 테스트 코드를 작성할때 사용하는 방식이다.

```
Describe - 
Context - 
it - 
```

`(테스팅 도구들의 관계)`

RSpec(Ruby로 작성) -> `(영향)` -> Mocha -> `(영향)` -> Jest

테스트 케이스를 정의할 때 크게 두 가지 방법을 사용한다.

1. test 함수로 개별 테스트를 나열하는 방식으로 테스트 코드를 작성한다.

2. BDD 스타일로 주체-행위 중심으로 테스트를 조직화하는 방식으로 테스트 코드를 작성한다.

`(1번 test 함수로 개별 테스트 나열방식으로 테스트 코드 작성 예시)`

(STEP 1) `Red 단계` add() 함수가 정의되어 있지 않기 때문에 에러가 발생한다.
```js
test('add', () => {
    expect(add(1, 2)).tobe(3);
})
```

(STEP 2) `Green 단계` 무조건적으로 숫자 3을 반환하는 add() 함수를 작성해준다. 
(빠르게 통과되는 테스트 코드 작성)

```js
function add(x: number, y: number): number {
    return 3;
}
```

만약에 *.test.ts 파일에서 작성한 테스트 코드를 js코드로 인식해서 에러가 발생한다면, `jest.config.js`파일을 추가해서 타입스크립트를 작성할 수 있도록 한다.

<br/>

`jest.config.js`

```js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: [
    '@testing-library/jest-dom/extend-expect',
  ],
  transform: {
    '^.+\\.(t|j)sx?$': ['@swc/jest', {
      jsc: {
        parser: {
          syntax: 'typescript',
          jsx: true,
          decorators: true,
        },
        transform: {
          react: {
            runtime: 'automatic',
          },
        },
      },
    }],
  },
};
```

프론트 개발할 때에는 별도의 외부 터미널 애플리케이션에서 `npx jest --watchAll` 명령 실행을 통해 실시간으로 프로젝트 내 작성한 테스트 코드가 통과되는지 확인해가면서 개발을 진행하도록 해보자!

<br/>

(STEP 3) `Refactor 단계` 의도(인자로 넘겨받은 x와 y를 가지고 더한값을 반환하도록 한다)를 드러내는 형태로 add() 함수를 작성한다.

```zsh
function add(x: number, y: number): number {
    return x + y;
}
```

위에서 작성한 test()함수를 활용한 테스트 코드 내용을 `BDD 방식`으로 작성해보도록 한다.

우선적으로 테스트 코드는 아래와 같이 작성한다.

아래 코드에서 기존 `1 + 2`에서 `x + y`로 바꾸고, 만약에 인자로 복수 개 인자가 들어오는 경우, 아래와 같이 number[] 타입의 numbers 인자를 넘겨 받아서 더하는 형태로 코드를 계속 작성한다. 

코드 작성하는 중간 과정에서는 아래와 같이 `(numbers[numbers.length - x]) + ...` 형태로 반복이 되고 있음을 확인할 수 있다.

따라서 이 반복 코드를 어떻게 수정해줄지에 대해서는 그 다음 코드블럭에서 작성을 해보도록 한다.


```js
function add(...numbers: number[]): number {
    return (numbers[numbers.length - 2] ?? 0) + (numbers[numbers.length - 1] ?? 0);
}

test('add', () => {
    expect(add(1, 2)).toBe(3);
});

const context = describe;

describe('add', () => {
    context('with two numbers', () => {
        it('returns sum of two numbers', () => {
            expect(add(1, 2)).toBe(3);
        });
    });
    context('with only one args', () => {
        it('returns the same number', () => {
            expect(add(2)).toBe(2); 
        });
    });
    context('with no args', () => {
        it('returns zero', () => {
            expect(add()).toBe(0); 
        });
    });
})


```
<br/>

반복 코드를 아래와 같이 또 다른 형태로 코드를 작성해준다.

```js
function add(...numbers: number[]): number {
    if (numbers.length === 0) {
        return 0;
    }

    if (numbers.length === 1) {
        return numbers[0];
    }

    if (numbers.length === 2) {
        return numbers[0] + numbers[1];
    }

    if (numbers.length === 3) {
        return add(numbers[0], numbers[1]) + numbers[2]; 
    }

    return (numbers[numbers.length - 2] ?? 0) + (numbers[numbers.length - 1] ?? 0);
}
```

다음 단계를 통해 좀 더 패턴화를 간략화 할 수 있다.

```js
function add(...numbers: number[]): number {
    if (numbers.length === 0) {
        return 0;
    }

    if (numbers.length === 1) {
        return numbers[0];
    }

    if (numbers.length === 2) {
        return numbers[0] + numbers[1];
    }

    if (numbers.length === 3) {
        // slice() 함수를 사용해서 0부터 numbers.length - 2 index 값까지의 합을 재귀형태로 호출
        return add(...numbers.slice(0, numbers.length -1)) + numbers[numbers.length - 1]; 
    }

    return (numbers[numbers.length - 2] ?? 0) + (numbers[numbers.length - 1] ?? 0);
}
```

위 코드를 더 간략하게 하면, 아래와 같이 최종적으로 피보나치 수열의 형태로 코드를 간략하게 수식화해서 작성할 수 있다.

```js
function add(...numbers: number[]): number {
    if (numbers.length === 0) {
        return 0;
    }

    return add(...numbers.slice(0, numbers.length -1)) + numbers[numbers.length - 1]
}
```

위의 코드로 재귀함수를 구현한 것을 `reduce()`함수를 사용해서 더 간단하게 작성할 수 있다.

```js
// reduce 함수의 초기값을 0으로 주었기 때문에 인자로 받은 numbers length가 0없는 경우에는 initial value로 설정한 0이 반환되어 아래와 같이 작성할 수 있다. 
function add(...numbers: number[]): number {
    return numbers.reduce((acc, number) => acc + number, 0);
}
```

위와 같은 코드 리팩토링이 안정적으로 가능했던 것은, 테스트 코드를 기반으로 로직을 수정하면서 로직 자체가 깨지지 않는지 확인할 수 있었기 때문이다.

`최종 작성한 테스트 코드는 아래와 같다.`

```js
describe('add', () => {
    // describe 외부에 아래 context와 add() function을 선언하게 되면,
    // Jest와 같은 테스트 러너는 각 테스트 파일을 별도의 모듈로 로드하지만,
    // 일부 모듈이나 변수가 캐싱될 수 있고, 이러한 경우에는 특정 변수가
    // 전역 범위에 정의되면 다른 테스트 파일에서도 접근할 수 있다.
    const context = describe;
    
    function add(numbers: number[] = [0]): number {
        return numbers.reduce((acc, number) => acc + number, 0);
    }
    context('with no args', () => {
        it('returns zero', () => {
            expect(add()).toBe(0); 
        });
    });
    context('with only one args', () => {
        it('returns the same number', () => {
            expect(add([2])).toBe(2); 
        });
    });
    context('with two numbers', () => {
        it('returns sum of two numbers', () => {
            expect(add([1, 2])).toBe(3);
        });
    });
    context('with many numbers', () => {
        it('returns zero', () => {
            expect(add([1, 2, 3, 4, 5, 6, 7])).toBe(28); 
        });
    });
})
```

<br/>

## RTL(React Testing Library)

React DOM을 테스팅하기 쉽게 할 수 있게 나온 것이다.

Jest가 돌아가는 Node환경에서 document 객체를 사용해서 테스트를 할 수 있도록 해준다.

하지만, 브라우저상에서는 돌아가는 테스트가 아니기 때문에 E2E 테스트는 아니다.

# (STEP 2) 각 각의 컴포넌트들을 작성 할 때 작성하는 단위 테스트 코드

`TextField 컴포넌트에 대해서 테스트 (사용자 입장에서 어떻게 되는지 잘 드러나는 케이스)`

components 폴더 하위에 따로 tests라는 폴더를 만들거나 그 상위 위치에 `__Tests`폴더를 만들어서 테스트용 파일을 생성할 수 있다.

하지만, 가장 좋은 방법은 테스트하고자 하는 파일과 같은 경로나 같은 경로에 tests 폴더를 만들어서 테스트 코드 작성을 위한 파일을 만드는 것이 좋다.

`src/components/TextField.tsx`와 동일한 경로에 `src/components/TextField.test.tsx`를 만든다.

```js

```

테스트 하고자 하는 컴포넌트인 `<TextField />`컴포넌트에서 handleChange() event method에 컴파일 에러가 발생해도 테스트 코드가 통과되는 것을 볼 수 있다.

이는 실제 테스트 코드 작성한 곳에서는 직접 생성한 onChange 정의 method를 주입해주기 때문이다.

`TextField.test.tsx`
```ts
import { fireEvent, render, screen } from "@testing-library/react";
import TextField from "./TextField";

test('TextField', () => {
    // given
    const label = 'Search';
    const text = 'Tester';
    const placeholder = 'Input your name';

    // 실제로 Input Element를 사용하는 곳에서만 setFilterText()로 정의하고,
    // 테스트할 때에는 범용적으로 사용되는 이름인 setText()로 정의해서 사용한다.
    
    // setText가 불렸는지 테스트
    let called = false;

    const setText = () => {
        called = true;
    };
    
    // when
    render((
        <TextField 
            label={label}
            placeholder={placeholder}
            text={text}
            setText={setText}
        />))

    // then
    // 지정한 label 텍스트와 연결된 input element가 잡힌다.
    // 정규표현식으로도 잡아줄 수 있다.
    screen.getByLabelText(/Sear/);

    const input = screen.getByLabelText(label) as HTMLInputElement;
    expect(input.value).toBe(text);
    expect(input.placeholder).toBe(placeholder);

    // 아래와 같이 해주거나 
    screen.getByDisplayValue(text);
    // 아래와 같이 해주면 된다.
    // placeholder 테스트 하기 


    // when
    fireEvent.change(screen.getByLabelText(label), {
        target: { value: 'New Name' },
    })

    // then
    // setText() method가 실행이 되었는지 확인하는 테스트 구문
    // useState hook을 사용해서 테스트하는 것이 아닌, 
    // TextField 컴포넌트에 넘겨준 setText() 함수가 실행이 되는지
    // 테스트를 한다.
    expect(called).toBeTruthy();
});
```

`TextField.tsx`

```ts
import { useRef } from "react";

type TextFiledProps = {
    label: string;
    placeholder: string;
    text: string;
    setText: (value: string) => void;
}

export default function TextField({
    label, placeholder, text, setText,
}: TextFiledProps) {
    const id = useRef(`input-${Math.random()}`);
    
    const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
        const { value } = event.target;
        setText(value);
    };

    return (
        <div>
            <label htmlFor={id.current}>
                { label }
            </label>
            <input 
                type="text" 
                id={id.current}
                placeholder={placeholder}
                value={text}  
                onChange={handleChange} 
            />
        </div>
    )
}
```

테스트코드를 작성하면서, 컴포넌트를 사용하는 코드를 작성하면서 해당 컴포넌트의 인터페이스의 기본적인 부분을 점검할 수 있다!

label에 대한 부분과 text와 같은 범용적인 표현을 사용하지 않은 문제, 이러한 문제들은 테스트부터 작성을 했다면 사전에 문제를 발견해서 수정할 수 있었을 것이다.

나중에 수정해야지.. 이런다면, 시간이 지나면서 해당 코드에 대한 지식이 감소하고, 자신감 또한 감소하기 때문에 건드리기 힘든 코드가 된다!

이제부터라도 테스트 코드부터 작성하면서 좀 더 깊이 있게 컴포넌트를 고려하는 개발을 하도록 하자!!!

### <u>위에서 작성한 `setText()` 메서드가 실행되는지 테스트하는 부분은 아래와 같이 좀 더 간편하게 처리해줄 수 있다.</u>


```ts
// const setText = () => {
//     called = true;
// };

// (Case 1) jest.fn()으로 해당 함수가 호출되었는지만 확인
const setText = jest.fn();

expect(setText).toBeCalled();


// (Case 2) 직접 input element에 입력값을 넣어서 입력된 값과 함께
//          setText() 함수가 같이 호출되었는지 확인
// when
fireEvent.change(screen.getByLabelText(label), {
    target: { value: 'NewName' },
});

// then
// setText() method가 실행이 되었는지 확인하는 테스트 구문
// useState hook을 사용해서 테스트하는 것이 아닌, 
// TextField 컴포넌트에 넘겨준 setText() 함수가 실행이 되는지
// 테스트를 한다.
expect(setText).toBeCalledWith('NewName');


// (Case 3) jest.fn()만 선언해도 되지만, 내부적으로 called 변수의
// 값을 변경함으로써 변경된 변수를 통해 호출 여부를 판단할 수 있다.
const setText = jest.fn(() => {
    called = true;
});

expect(called).toBeTruthy();
```

### <u>이젠 BDD 스타일로 코드를 바꾸고, 입력 등이 잘 작동하는지 확인!</u>

```tsx
import { fireEvent, render, screen } from "@testing-library/react";
import TextField from "./TextField";

const context = describe;

describe('TextField', () => {
    // given
    const label = 'Search';
    const text = 'Tester';
    const placeholder = 'Input your name';

    // mock한건 아래 두 가지 테스트 케이스에서 모두 실행되기 때문에
    // 반드시 
    const setText = jest.fn();

    beforeEach(() => {
        // 각 테스트 실행할때마다 mocking한 부분을 초기화해준다.
        // setText.mockClear();
        // 모든 mocking 변수를 전부 초기화 시켜준다.
        jest.clearAllMocks();
        // 위 둘 중에 하나만 작성해주면,mocking한 모든 변수에 대해서 초기화를 시켜준다.
    });

    function renderTextField() {
        render(
            <TextField 
                label={label}
                placeholder={placeholder}
                text={text}
                setText={setText}
            />
        );
    }

    it('renders elements', () => {
        // when
        renderTextField();
    
        // then
        // 지정한 label 텍스트와 연결된 input element가 잡힌다.
        // 정규표현식으로도 잡아줄 수 있다.
        screen.getByLabelText(label);
        screen.getByPlaceholderText(placeholder);
        screen.getByDisplayValue(text);
    });
    
    context('when user types name', () => {
        // given
        it('calls setText() handler', () => {
            renderTextField();
    
            // when
            fireEvent.change(screen.getByLabelText(label), {
                target: { value: 'NewName' },
            })
    
            // then
            expect(setText).toBeCalledWith('NewName');
        });
    });
});

`React Testing Library 강의 (24:05)`
```

beforeEach 함수를 통해 `given`으로 주어진 변수들을 상이한 값으로 선언해서 여러 케이스로 나누어 
테스트 진행 

```js
beforeEach(() => {
    // 이 영역에 given으로 주어진 text, placeholder 등의 변수들을
    // 상위 scope에서는 let으로 선언을 해주고,
    // 여기서 각기 다른 변수값을 선언해서 테스트를 다르게 진행해줄 수 있다.
})
```

`given`(테스트를 하기위해 기본적으로 주어지는 조건) 영역은 기본적으로, context와 it 영역 사이에 
beforeEach() 함수를 두고 해당 영역에 given에서 주어진 기본 영역값을 선언한다.

```ts
context('when user types name', () => {
    beforeEach(() => {
        // given
        renderTextField();
    });
    it('calls setText() handler', () => {
        // when
        fireEvent.change(screen.getByLabelText(label), {
            target: { value: 'NewName' },
        });
        // then
        expect(setText).toBeCalledWith('NewName');
    });
});
```
<br/>

`BDD Test code format`
```tsx
// ~한 경우에
context('[상황에 대해 명시]', () => {
    beforeEach(() => {
        // given
        // [이것들이 주어지면]
    })
    it('[context에서 정의한 행동을 하면, 따라오는 그 다음 행동]', () => {
        // when
        // 이런 것들을 해주는 경우에는

        // then
        // 이런 결과를 확인할 수 있다.
    })
})
```

만약에 fireEvent.change() method를 별도의 function으로 빼고 싶은 경우에는 아래와 같이 
별도의 함수를 describe 함수 하위에 선언해서 재사용성을 높여도 괜찮다.

```tsx
function inputText(value: string) {
    fireEvent.change(screen.getByLabelText(label), {
        target: { value },
    });
}
```

명시적으로 드러내고 싶은경우에는 별도의 함수로 빼지 않아도 된다.

숫자만 입력받는 Input element에 대한 테스트

swc가 타입 붙어있는것들을 모두 제거하고, 테스트 코드를 돌린다.

타입에 대한 검증을 하려면, 아래와 같이 typescript compiler를 `--noEmit`옵션을 붙여서 작성을 해준다.

```zsh
# 컴파일한 결과가 js파일로 생성되지 않게 처리한다.
npx tsc --noEmit
```

아래와 같이 하면,`npm run check`명령을 통해서 잘못 작성된 부분에 대해서 확인을 할 수 있다.
```
npm run lint
npm run check
```

### 외부 서버에 대한 요청을 통해 받은 결과값을 화면에 뿌려주는 경우에는 서버에 요청을 보내서 값을 가져오는 별도의 hook을 사용해서 값을 정의하게 되는데, 아래와 같이 mocking을 해서 임의로 반환되는 값을 정의할 수 있다.

```tsx
import { render, screen } from "@testing-library/react"
import App from "./App"

// useFetch module을 작성해준다.
// 아래와 같이 useFetchData() hook을 mocking해서 반환값을 문자열 ㅂ
jest.mock('./hooks/useFetchData', () => () => "dummy data");

test('App', () => {
    render(<App />);
    screen.getByText('dummy data');
});
```

위와 같이 작성을 해주면, App 컴포넌트를 렌더링 할 때 화면에 표시되는 HomePage 컴포넌트 내에서 사용되는 useFetchData() hook 함수가 호출되게 되는데, 위와 같이 임의로 useFetchData 함수에 대한 반환값을 임의로 지정할 수 있다.

useFetchData() 함수를 통해 반환되는 값을 임의로 "dummy data"로 정의를 하였기 때문에, 
실제로 App 컴포넌트를 렌더링 한 후에 화면에 보여지는 데이터를 검사하면, 임의의 값 "dummy data"가 출력되는 것을 확인할 수 있다.

### src 디렉토리와 동일 레벨에 fixtures 디렉토리 생성

기본적으로 프론트엔드 개발에서 사용되는 dummy 데이터의 경우에는 아래와 같이 fixtures/products.ts 파일을 만들어서 index.ts 파일을 통해 일괄적으로 export default 해주도록 한다.

### hooks 디렉토리 하위에 `__mocks__` 디렉토리 생성
__mocks__ 폴더를 만들어서 mocking할 대상 파일인 useFetchData.ts 파일을 붙여넣고, 아래와 같이 작성을 해준다.

```ts
const useFetchData = jest.fn(() => "dummyData");
export default useFetchData;
```

위와 같이 임의의 __mocks__ 폴더를 만들어서 hook을 특정값을 반환하는 값과 함께 mocking을 해주게 되면, 아래와 같이 별도로 반환되는 값을 함수로써 정의하지 않아도 된다.
```ts
// (AS-IS)
jest.mock('./hooks/useFetchData', () => () => fixtures.dummyData);

// (TO-BE)
jest.mock('./hooks/useFetchData');
``` 

위와 같이 jest.mock()을 사용해서 hook들을 mocking 해 줄 수 있다.

useFetch() 자체를 mocking해서 url에 따라 어떤 응답을 해줄지에 대해서 처리를 해줄 수 있다.

# MSW를 사용해서 백엔드와의 통신을 별도로 
# (STEP 3) E2E 테스트 코드

