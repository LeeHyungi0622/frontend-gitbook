## JSX?

`"XML-like syntax extension to ECMAScript"`
React를 사용할때 자주 접하게 되는 개념으로, `ECMAScript에서 확장한 XML 같은 문법`

Java가 Oracle이 가지고 있는 상표이기 때문에 피하기 위해서 ECMAScript에서  문법 확장

## React에서 JSX를 사용하는 목적
React의 부산물이지만, Vue에서도 JSX를 사용할 수 있다. (원래 템플릿을 사용하지만, JSX를 쓸 수 있다), Solid도 JSX를 사용한다.

ATOM이라는 텍스트 에디터에서 Electron이라는 것이 부산물로 나옴

부산물로 나온 Electron을 가지고 만든것이 Visual Studio code(MS)이다.

부산물이 꼭 연결되는 것이 무조건 사용으로 연결되는 것이 아니다.

## Syntactic sugar


## React.createElement
JSX는 XML처럼 작성된 부분을 React.createElement을 쓰는 JavaScript 코드로 변환한다. 중괄호를 써서 JavaScript 코드를 그대로 쓸 수 있고, 결국은 JavaScript 코드와 1:1로 매칭된다.

### SWC와 Babel

위의 변환을 확인하기 위해 SWC가 아닌 Babel을 사용해서 전환한다. 

#### babel 변환 확인하기
[Babel 변환기](https://babeljs.io/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.21&spec=false&loose=false&code_lz=DwBwfAEgpgNjD2AaABAd3gJxgEwITAHpwg&debug=false&forceAllTransforms=false&modules=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2&prettier=false&targets=&version=7.24.6&externalPlugins=%40babel%2Fplugin-transform-react-jsx%407.24.6&assumptions=%7B%7D)

preset에서 React를 선택하고, Plugin에서 @babel/plugin-transform-react-jsx을 추가하면 변환된 코드를 확인할 수 있다.

```javascript
// 변환 전
/* @jsx React.createElement */
// JSX 파일에 /* @jsx 어쩌고 */ 주석을 추가하면 React.createElement 대신 “어쩌고”를 쓰게 된다.
<p>Hello, world</p>

// 변환 후
/*#__PURE__*/React.createElement("p", null, "Hello, world!");
```

```javascript
// 변환 전
<Greeting name="world" />

// 변환 후
/*#__PURE__*/React.createElement(Greeting, {
  name: "world"
});
```

```javascript
// 변환 전
<Button type="submit">Send</Button>

// 변환 후
/*#__PURE__*/React.createElement(Button, {
  type: "submit"
}, "Send");

React.createElement(<태그 타입>, <태그 속성>, <태그가 감싸고 있는 콘텐츠>)

태그 타입의 경우, HTML 기본 Element라면 "button"과 같이 쌍따옴표 안에 들어간다.

// 변환 전
<Greeting name="world" age={{ 'key': 'value' }} />

// 변환 후
/*#__PURE__*/React.createElement(Greeting, {
  name: "world",
  age: {
    'key': 'value'
  }
});

// 변환 전
<button type="submit" onClick={() => console.log('Clicked')}>Send</button>

// 변환 후
/*#__PURE__*/React.createElement("button", {
  type: "submit",
  onClick: () => console.log('Clicked')
}, "Send");

// 복수개의 태그를 넣는 경우

//변환 전
<React.Fragment>
  <p>Hello, world!</p>
  <Button type="submit">Send</Button>
</React.Fragment>

//변환 후
/*#__PURE__*/React.createElement(React.Fragment, null, /*#__PURE__*/React.createElement("p", null, "Hello, world!"), /*#__PURE__*/React.createElement(Button, {
  type: "submit"
}, "Send"));

//변환 전
<div>
  <p>Count: {count}!</p>
  <button type="button" onClick={() => setCount(count + 1)}>Increase</button>
</div>


//변환 후 
/*#__PURE__*/React.createElement("div", null, /*#__PURE__*/React.createElement("p", null, "Count: ", count, "!"), /*#__PURE__*/React.createElement("button", {
  type: "button",
  onClick: () => setCount(count + 1)
}, "Increase"));
```


## React Element
JSX대신 그냥 React.createElement를 써서 React element 트리를 갱신하는데 사용할 수 있다.

`JSX Runtime`은 _jsx라는 함수를 사용하고, Preact는 h란 함수를 직접 지원한다.

## React Developer Tools
전체 DOM element가 출력되지는 않지만, React component의 구조를 파악하기 위해서 용이하다.

## React StrictMode
Strict mode로 컴포넌트를 감싸주면, 컴포넌트에 표기된 경고표시가 없어진다.

## VDOM(Virtual DOM)이란?

메모리상에 올라가있는 DOM 구조와 수정한 DOM 구조를 비교해서 다른 부분이 있으면 해당 부분을 다시 랜더링한다.

유지보수가 가능한 애플리케이션 개발을 만들도록 해주고, 모든 경우에 있어, 충분히 빠르다.

## DOM이란?

## DOM과 Virtual DOM의 차이
VDOM을 사용한다고 해서 빠른건 아니다. 다만 유지보수가 가능한 애플리케이션을 만들기 위해서이다.
```
1 2 3 4 5 6

2 3 4 5 6 7
```
위 구조에서 윗줄의 맨 앞에 1과 아래줄에 마지막 7을 재거해주면 같은 리스트가 된다. (key값 사용해서 정의)

## Reconciliation(재조정) 과정은 무엇인가?