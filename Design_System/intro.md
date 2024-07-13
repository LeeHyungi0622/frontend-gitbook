디자인 시스템이라는 개념은 리액트로 컴포넌트를 만드는 개념이 잘 맞아 떨어지기 때문에 알아두어야 한다.


공유되는 언어를 만들고,(예시. Accordion) 시각적인 일관성을 만든다.

재사용 가능한 컴포넌트와 패턴을 가지고 만든다.

### 디자인 시스템 관련 포스팅

[Laura Kalbag의 "Design System" 소개](https://24ways.org/2012/design-systems/)

[Laura Kalbag의 "Design Systems" 슬라이드](https://speakerdeck.com/laurakalbag/design-systems-1)

[Nielsen Norman Group의 "Design Systems 101"](https://www.nngroup.com/articles/design-systems-101/)


### 디자인 시스템 구현하기 
위 디자인 시스템 관련 포스팅 중 좀 더 실용적으로 참고될 수 있는 내용이 `Nielsen Norman Group의 "Design System 101"`이다.

이 내용을 간단하게 정리하자면, 

`(요약)`

디자인 시스템은 각기 다른 페이지와 채널들에서 공유되는 언어(shared language)와 시각적 지속성(visual consistency)을 만들어가면서 불필요한 요소들을 줄여가며 디자인을 관리하기 위한 표준들의 집합으로 볼 수 있다.

`(정의)`

디자인 시스템은 `재사용 가능한 컴포넌트`와 `패턴`을 사용해서 대규모 디자인을 관리한기 위한 의도의 표준들의 완성된 집합이다.

```
Summary: A design system is a set of standards to manage design at scale by reducing redundancy while creating a shared language and visual consistency across different pages and channels.

Definition: A design system is a complete set of standards intended to manage design at scale using reusable components and patterns.
```

아래 두 가지 이외에도 개념들이 많지만, 기본적으로 아래 두 가지 개념을 통해서 우리는 디자인 시스템을 구축할 수 있다.

- Theme

- Component

### UI/UX에서 영향력 있는 인물들

Jakob Nielsen과 Donald Norman은 UX 분야에서 전설적인 인물들이다. Alan Cooper도 참고하면 좋은 인물이다.

#### 제이콥 닐슨 (Jakob Nielsen)
#### 도널드 노먼 (Donald Norman)
#### 앨랜 쿠퍼 (Alan Cooper)


### 참고하면 좋은 사례들

- #### [Atlassian Design System](https://atlassian.design/)

- #### [Google Material Design](https://m3.material.io/)

- #### [Base Web(Uber)](https://baseweb.design/)
    - React에서 쓸 수 있게 만들어 놓은 것도 있다.

- #### [Polaris(Shopify)](https://polaris.shopify.com/)
    - 우리나라에서는 별로 유명하지 않지만, 외국에서는 절대적인 위치를 갖고 있다.

- #### [Lightening Design System(Salesforce)](https://www.lightningdesignsystem.com/)
    - Accordion이 일관된 형태로 작동하도록 하나의 컴포넌트로써 관리한다.

- #### [Mailchimp Pattern Library](https://ux.mailchimp.com/patterns/color)
    - 컬러나 페이지에 있는 캐릭터의 성격에 따라 페이지에 표기된 메시지를 보여준다.

- #### [Ant Design](https://ant.design/)
    - Figma에서 끌어다가 쓸 수 있도록 할 수 있다.

    - Ant Design For Figma

#### Framer X를 사용해서 UI 컴포넌트 만들기
- 네이버나 Toss도 Framer를 사용해서 개발을 하고 있다. 

- [(참고) 디자인 시스템 UI 컴포넌트를 Framer X를 이용해서 만들기](https://medium.com/harbor-school/%EB%94%94%EC%9E%90%EC%9D%B8-%EC%8B%9C%EC%8A%A4%ED%85%9C-ui-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EB%A5%BC-framer-x%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-%EB%A7%8C%EB%93%A4%EA%B8%B0-a574060ed10a)

### 디자인 시스템 Gallery

- #### [Design Systems Gallery](https://designsystemsrepo.com/design-systems/)

- #### [Design System](https://www.designsystems.com/open-design-systems/)

### Atomic Design

- #### [Atomic Design 소개 글](https://bradfrost.com/blog/post/atomic-web-design/)

- #### [Atomic Design 전자책](https://atomicdesign.bradfrost.com/)

    - Atomic Design은 디자인 시스템을 만들기 위한 방법론이다.


## Style Basic

### Basic: Class

[스타일링과 CSS](https://ko.legacy.reactjs.org/docs/faq-styling.html)

의미있는 Markup을 하는 것이 중요하다! (tailwindcss처럼 class로 스타일을 지정하면, inline style을 적용한 것과 별반 다르지 않다!)

### inline-style

아래와 같이 스타일을 객체로 선언해서 스타일을 적용할 수 있다.

```js
export default function Greeting() {
    const style = {
        color: '#00F',
    };

    return (
        <p style={style}>
            Hello world!
        </p>
    )
}
```

### CSS-in-JS
JavaScript를 이용해서 스타일 컴포넌트를 만들어서 사용하는 방식이다.

#### CSS-in-JS를 사용함으로써 아래 장점을 가져올 수 있다.

[(참고) CSS in JS](https://en.wikipedia.org/wiki/CSS-in-JS)

[(참고) React: CSS in JS (2014)](https://blog.vjeux.com/2014/javascript/react-css-in-js-nationjs.html)

```
1. Global Namespace
2. Dependencies
3. Dead Code Elimination
4. Minification
5. Sharing Constants
6. Non-deterministic Resolution
7. Isolation
```

<br/>

[A Unified Styling Language (2017)](https://megaptera.notion.site/1bda981f17224c058b612598d4323c63)

```
1. Scoped styles
2. Critical CSS
3. Smarter optimisations
4. Package management
5. Non-browser styling
```

<br/>

[All you need to know about CSS-in-JS (2017)](https://megaptera.notion.site/CSS-in-JS-d5904960ac564b0c8cdf5aa3bc2df5da)

```
1. Thinking in components
2. CSS-in-JS leverages the full power of the JavaScript ecosystem to enhance CSS.
3. True rules isolation
4. Scoped selectors
5. Vendor Prefixing
6. Code sharing
7. Only the styles which are currently in use on your screen are also in the DOM (react-jss).
8. Dead code elimination
9. Unit tests for CSS
```

<br/>

[Most Popular CSS-in-JS libraries](https://npmtrends.com/aphrodite-vs-emotion-vs-glamorous-vs-jss-vs-radium-vs-styled-components-vs-styletron)

```
→ 2024년 7월 기준으로 styled-components, JSS, Emotion 순서.
```

## 성능 이슈

[CSS-in-JS와 성능 (2021)](https://megaptera.notion.site/CSS-in-JS-0afa302408f74f7a9712924ead6318be)

CSS 파일과 JS 파일 로딩의 차이

[Why we're breaking up with CSS-in-JS](https://megaptera.notion.site/CSS-in-JS-4022ede93d06407ea6474a40be4cd906?pvs=4)

## 대안
[Linaria](https://linaria.dev/)

- CSS를 평범한 텍스트로 작성
- React에 종속적이지 않지만, React Styled Component도 지원함.

[vanilla-extract](https://vanilla-extract.style/)

- CSS를 오브젝트 형태로 표현. React의 Inline Style과 유사함
- React와 무관하게 사용 가능.

## styled-components

[styled-components](https://styled-components.com/)

[Babel Plugin](https://styled-components.com/docs/tooling#babel-plugin)

[@swc/plugin-styled-components](https://github.com/swc-project/plugins/tree/main/packages/styled-components)

[vscode-styled-components extension](https://marketplace.visualstudio.com/items?itemName=styled-components.vscode-styled-components)

`스타일이 적용된 컴포넌트를 쉽게 만들 수 있는 도구`

반드시 VS Code Extension을 설치해서 쓰자. 도구에서 제대로 지원 안하면 사람이 쓸 게 못 된다. 

Babel Plugin을 SWC에서 쓸 수 있도록 포팅한 것도 함께 설치하자 (SSR 지원 등을 위한 공식 권장사항)

```zsh
npm i styled-components
npm i -D @types/styled-components @swc/plugin-styled-components
```

