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

