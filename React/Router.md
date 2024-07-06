# React Router

일반적인 웹 사이트는 URL에 따라 다른 웹 페이지를 보여준다.

하나의 웹 페이지를 하나의 컴포넌트로 만들고, URL에 따라 적절한 컴포넌트가 보이게 구현해주도록 도와주는 친구가 바로 Router이다.

`window 객체`

```js
window.location

window.location.pathname

location.host
// localhost:8080

location.hostname 
// localhost

location.hash URI뒤에 #뒤에 붙인 형태로 붙는 것
decodeURI(location.hash)
encodeURI('예제')

location.pathname
```

### pages[path]와 Reflect.get(pages, path)의 사용

Pages를 따로 key-value 형태의 객체로 써서 선언해서 사용하기

사용 1) pages에 대해서 강제로 타입을 지정해서 선언해준다. (페이지 경로가 되는 URI는 string으로, 페이지 컴포넌트가 되는 value는 React.FC타입으로 지정)
```js
const pages: Record<string, React.FC> = {
  '/': HomePage,
  '/about': AboutPage
};

// 아래 코드는 함수 컴포넌트 내에서 정의
const Page = pages[path] || HomePage;
```

사용 2) pages 변수에 강제로 반환되는 값에 대한 타입을 지정하지 않고, 아래와 같이 Reflect.get()을 사용해서 대체할 수 있다.

```js
const pages = {
    '/': HomePage,
    '/about': AboutPage
};

const Page = Reflect.get(pages, path) || HomePage;
```

## BrowserRouter와 MemoryRouter의 사용

개발할때는 main.tsx에서 최 상위 컴포넌트인 `<App />`를 `<BrowserRouter>`로 감싸서 jsx를 작성해주도록 하며, 테스트 코드를 작성할때에는 `<MemoryRouter>`를 사용해서 컴포넌트를 감싸서 렌더링을 해주도록 한다. 

## (React router 6.4부터 지원) Router 객체 만들어서 사용하는 방법

아래 `<App />`컴포넌트에서는 전체적인 레이아웃의 구조와 라우팅 경로에 대한 설정이라는 두 가지 측면에서의 역할이 혼재되어 있다.

```ts
import Footer from "./components/Footer";
import Header from "./components/Header";
import AboutPage from "./pages/AboutPage";
import HomePage from "./pages/HomePage";
import { Routes, Route } from 'react-router-dom';

export default function App() {

  return (
    <div>
      <Header />
      <main>
        <Routes>
          <Route path="/" element={<HomePage />}/>
          <Route path="/about" element={<AboutPage />}/>
        </Routes>
      </main>
      <Footer />
    </div>
  );
}
```

아래와 같이 정의함으로써 실제로는 `<App />`컴포넌트를 사용하지 않게 구조를 만들 수 있다.

`App.tsx`
```js
import Footer from "./components/Footer";
import Header from "./components/Header";
import AboutPage from "./pages/AboutPage";
import HomePage from "./pages/HomePage";
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

const routes = [
  { path: '/', element: <HomePage /> },
  { path: '/about', element: <AboutPage />},
];

const router = createBrowserRouter(routes);

export default function App() {
  return (
    <RouterProvider router={router}/>
  )
}

```

하지만, 위와같이 하면, 전체 페이지가 다시 렌더링이 되기 때문에 아래와 같이

별도로 Layout에 대한 functional component를 따로 빼주고, 

위의 routes에서 각 경로에 대한 컴포넌트를 매칭할때 해당 매칭 컴포넌트를 출력할

`<Outlet />`영역에 대해 정의한 위의 Layout 컴포넌트에 대해 추가로 작성해준다.

`Layout.tsx`

```js
import Footer from "./components/Footer";
import Header from "./components/Header";
import AboutPage from "./pages/AboutPage";
import HomePage from "./pages/HomePage";
import { createBrowserRouter, RouterProvider, Outlet } from 'react-router-dom';

function Layout() {
  return (
    <div>
      <Header />
      <main>
        <Outlet />
      </main>
      <Footer />
    </div>
  );
}

const routes = [
  {
    element: <Layout />,
    children: [
      { path: '/', element: <HomePage /> },
      { path: '/about', element: <AboutPage />},
    ], 
  },
];

const router = createBrowserRouter(routes);

export default function App() {
  return (
    <RouterProvider router={router}/>
  )
}
```

위 구조에서 routes.ts와 components/Layout.tsx 로 파일로 빼서 관리를 하면 좋다. (테스트시에도 router에 대해서만 테스트를 작성해줄 수 있기 때문)

`<App />`컴포넌트에서는 RouterProvider만 반환해주고 있기 때문에, 해당 부분을 `main.tsx`에서 처리하고, `<App />`컴포넌트는 제거해줘도 된다.

이제 위와 같이 작성하면, `<App />`컴포넌트에 대해서 렌더링을 통해 테스트 코드를 작성하는 것을 할 수 없다.