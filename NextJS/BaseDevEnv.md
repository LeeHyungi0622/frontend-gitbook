# Next.js 프로젝트 기본 셋팅

create-next-app을 사용하여 next.js를 사용한 샘플 웹 애플리케이션 프로젝트 생성 (`--ts option을 주게되면, typescript 설정이 적용된 next.js 웹 애플리케이션 프로젝트를 생성할 수 있다`)

```zsh
npx create-next-app@latest --ts next-sample
```

## 프로젝트 빌드 및 실행

아래 script에 적용된 항목 중 `npm run build`명령을 실행하게 되면, next build를 통해 프로젝트를 빌드하게 되고, 그 결과를 프로젝트 디렉토리의 root 위치의 `.next` 아래에 저장을 하게 됩니다. 
그리고 `npm start`명령을 통해 빌드된 결과물인 `.next`하위의 데이터를 기반으로 서버를 기동하게 됩니다.
```zsh
# package.json 내 script 정의

"scripts": {
"dev": "next dev",
"build": "next build",
"start": "next start",
"lint": "next lint"
}
```

## 프로젝트 기본 구성

create-next-app 실행을 통한 프로젝트 생성을 하게 되면, 아래와 같은 구조로 프로젝트가 생성됩니다.
```zsh
next-sample/
├── README.md
├── next.config.js
├── package.json
├── public/
│   ├── favicon.ico
│   ├── vercel.svg
│   └── ...
├── src/
│   ├── pages/
│   │   ├── _app.js
│   │   ├── index.js
│   │   └── ...
│   └── styles/
│       ├── globals.css
│       └── Home.module.css
└── yarn.lock / package-lock.json
```

`pages` 디렉토리에는 페이지 컴포넌트나 API 코드가 배치되며, `public` 디렉토리에는 이미지 등 정적 파일을 배치합니다. 

styles 디렉토리에는 css 파일을 배치하게 되며, 애플리케이션 전체 스타일에 대한 `globals.css`와 페이지 전용의 `Home.module.css`등이 기본적으로 생성됩니다.

`*.module.css`는 컴포넌트를 정의하는 파일에서 읽어서 처리하며, 클래스명이나 ID 정보가 다른 파일에서 정의한 내용과 충돌하는 것을 사전에 방지하기 위해 파일마다 클래스명과 ID의 접두사 또는 접미사를 빌드 시 자동으로 부여한다.

## Next.js의 4가지 렌더링 방법

Next.js에서는 페이지별로 렌더링 방법을 전환할 수 있다.

- <u>SSG(Static Site Generation)</u> : 정적 사이트(Static)을 포함
    
    `(초기 화면 rendering)`

    → 빌드 시 getStaticProps라는 함수가 호출되며, 그 함수 내에서 API 호출등을 수행하고, 페이지를 그리는데 필요한 props를 반환합니다. 반환된 props를 페이지 컴포넌트에 전달해서 화면을 그리게 되며, 그린 결과는 정적 파일 형태로 빌드 결과에 저장을 하게 됩니다.

    → 빌드시에만 데이터를 얻기 때문에 초기 화면을 그릴때 오래된 데이터가 표시될 가능성이 있다. (`실시간성 콘텐츠에는 부적합`)

- <u>CSR(Client Side Rendering)</u>

   → 브라우저에서 초기 화면을 그릴때에는 SSG/SSR과 같이 정적인 파일 형태로 빌드된 결과물로 렌더링을 하게 되며, 초기 화면을 그린 이후에는 일반적인 리액트 애플리케이션과 마찬가지로 데이터를 API를 통해 동적으로 화면을 그리게 됩니다.
   
   → 빌드시에 데이터를 얻지 않고, 페이지를 화면에 그려서 저장을 우선적으로 진행한다. 이후에 초기 화면 그리기를 한 뒤, 비동기로 데이터를 얻어서 추가 데이터를 표시하게 됩니다.

   → 초기 화면을 그리는 것이 중요하지 않고, (페이지를 그리기 위해 필요한 데이터는 나중에 얻어서 반영(`SEO에 유효하지 않음`)) 실시간성이 중요한 페이지에 적합

   → `SSG, SSR, ISR을 조합`해서 종합적으로 사용한다.

- <u>SSR(Server Side Rendering)</u>

   → SSR에서는 페이지 접근이 발생할 때마다 서버에서 `getServerSideProps를 호출`하고, 그 결과 props를 기반으로 페이지를 서버측에서 그려서 클라이언트에 전달합니다.

- <u>ISR(Incremental Static Regeneration)</u>

<u>※ 모든 페이지에서 사전 렌더링이 가능한 부분은 사전 렌더링을 수행합니다.</u>

