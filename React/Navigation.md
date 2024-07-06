# Navigation

HTML5 history.pushState();

```js

(방법1) 아래와 같이 # 또는 #!을 붙이면 네트워크 탭의 request들이 통으로 바뀌지 않는다.
// /#!/
// /#!/about

// (방법2) 아래와 같이 기본적으로 html tag의 anchor 태그의 onClick event를 넣어서 기본적으로 발생하는 모든 이벤트를 막아버리고, 
const handleClick = (event: SyntheticEvent) => {
    event.preventDefault();
    const state = {};
    const title = '';
    const url = '/about';
    history.pushState(state, title, url);
};
```

페이지가 이동하면, 주소표시줄의 URL이 자동으로 수정된다.

## (방법1) `<Link to="/">Home</Link> 사용하기`
위의 handleClick() method를 HTML5부터 지원되는 history.pushState()를 사용해서 만들어 쓰지 말고, React가 기본적으로 제공해주는 `<Link to="/">Home</Link>`를 사용해서 구현해주도록 한다.


전체 바뀌지 않고, 페이지가 바뀐다.
나중에 Lazy loading이 아닌이상 컴포넌트를 다 불러온 상태이다.

아무것도 로딩없이 처리가 되는 것을 확인할 수 있다.

<br/>

## (방법2) `<NavLink to="/">Home</NavLink>` 사용해서 현재 표시된 페이지의 이동 링크에 대해 스타일을 적용해줄 수 있다.

```css
.active {
    font-size: 2rem;
}
```

## Navigate 사용해서 Redirection 처리하기

```js
import { Navitage } from 'react-router-dom';

export default function LogoutPage() {
    return (
        // 무조건 HomePage component로 redirect 처리를 해준다.
        <Navigate to="/" />
    );
}
```

### 로그아웃 처리에 대한 테스트 코드작성

아래와 같이 /logout으로 라우팅했을때, 화면상에 '홈'이라는 텍스트가 있는지에 대해 검사를 해주도록 한다.
```js
context('when the current path is "/logout"', () => {
    it('renders the logout page', () => {
        renderRouter('/logout');
        screen.getByText('홈');
    });
});
```

### `ReferenceError: Request is not defined`에러가 발생하면, `"whatwg-fetch"`를 import해서 해결할 수 있다. (polyfill)

`setupTests.ts`
```js
import 'whatwg-fetch';
```

## 가장 많이 사용되는 방법은 `useNavigate`를 사용해서 사용한다.

```js
import { useNavigate } from "react-router"

export default function Footer() {
    
    const navigate = useNavigate();

    const handleClick = () => {
        navigate('/about');
    };

    return (
        <div>
            <footer>
                <hr />
                Footer
                <button type="button" onClick={handleClick}>Press</button>
            </footer>
        </div>
    )
}
```

## Header쪽의 기존의 내용을 아래와 같이 수정해보도록 한다.

`(AS-IS)`
'/' 또는 '/about' 이라는 주소를 갔다가 나올 필요가 없다.
```js
export default function Header() {
    return (
        <header>
            <ul>
                <li>
                    <a href="/">Home</a>
                </li>
                <li>
                    <a href="/about">About</a>
                </li>
            </ul>
        </header>
    )
}
```

`(TO-BE)`
따라서 아래와 같이 기존에 anchor 태그를 button으로 바꿔서 작성을 한다.
```js
import { useNavigate } from "react-router";

export default function Header() {
    const navigate = useNavigate();

    const onClickHandler = () => {
        navigate('/logout');               
    };

    return (
        <header>
            <ul>
                <li>
                    <a href="/">Home</a>
                </li>
                <li>
                    <a href="/about">About</a>
                </li>
                <li>
                    <button type="button" onClick={onClickHandler}>Logout</button>
                </li>
            </ul>
        </header>
    )
}
```