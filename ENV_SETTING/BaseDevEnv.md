# 나만의 개발환경 세팅 만들기

## 매일 매일 반복해서 바닥부터 프로젝트 설정하는 연습하기

## Node

- Deno(데노 -> 디노)를 사용하면 훨씬 간단하게 개발환경을 셋팅할 수 있지만, 대부분 현업에서 Node.js를 기반으로 프로젝트를 세팅하기 때문에 상대적으로 구축이 어렵다.

- 개발환경은 항상 트렌드가 바뀌기 때문에 `전체적인 흐름을 파악하고, 앞으로 개발환경이 바뀌면 그에 맞춰 유연하게 대응할 수 있는 능력`을 키우는데 집중하자!

- Node는 반드시 최신 버전이 아닌, `LTS 버전`으로 설치하도록 한다. Long Term Support의 약자로, 장기간 지원 가능한 버전이라는 의미!

- NVM(Node Version Manager)는 많이 사용해봤는데, [FNM](https://github.com/Schniz/fnm)라는 더 빠른 사용이 가능하다.

### 개발환경 구축 순서 (STEP 1 ~ STEP 10)

#### <ins><b>(STEP 1)</b> 패키지 관리를 위한 `package.json` 파일 생성하기</ins>

생성한 이후에는 package.json 파일 내 이름, 버전, 설명 등을 나만의 프로젝트에 맞게 설정하도록 한다.


#### <ins><b>(STEP 2)</b> `.gitignore` 파일 생성하기</ins>

github에 올리지 말아야 할 리스트를 .gitignore 파일에 작성하도록 한다.

[Node - gitignore](https://www.toptal.com/developers/gitignore/api/node)

#### <ins><b>(STEP 3)</b> TypeScript 관련 설정하기</ins>

TypeScript는 JavaScript의 Superset으로, 자바스크립트 언어에 정적인 타입을 입혀 개발과정에서 실수를 줄일 수 있다는 장점이 있다.

- 타입스크립트를 devdependency 항목으로 추가하기 (타입스크립트는 개발을 위한 도구로, dependency가 아닌 devdependency 항목으로 추가)

    ```zsh
    npm i -D typescript
    ```

- 타입 스크립트 설정파일(`tsconfig.json`) 파일을 추가하기
    npx를 사용하면 캐시에 저장된 npm module을 사용해서 명령어를 실행할 수 있다.

    ```zsh
    npx tsc --init
    ```

- `tsconfig.json` 파일 내에서 JSX 문법을 허용하기
    tsconfig.json 파일 내에서 "jsx" 항목을 아래와 같이 "react-jsx"로 수정한다.

    ```zsh
    "jsx": "react-jsx",
    ```

#### <ins><b>(STEP 4)</b> ESLint 관련 설정하기</ins>

- 오타 및 기타 문제 해결, 그리고 코드 스타일 가이드를 해주기 위해 ESLint를 사용하도록 한다.
  아래 명령을 통해 eslint와 관련된 dependencies를 설치한다.

    ```zsh
    npm i -D eslint@8.57.0 eslint-config-xo@0.44.0 eslint-config-xo-typescript@4.0.0 eslint-plugin-react@7.34.1
    ```

- `.eslintrc.js` 파일 추가

    ```zsh
    npx eslint --init
    ```
    위 명령을 통해 eslint를 초기화해주게 되면, 강의에서 나온 질문들과 다른 부분이 있어, 일단 ㅜ이의 과정에서 ESLint 관련 dependencies를 버전과 일치하게 설치해준다. (이 부분은 나중에 최신 버전에 맞춰서 수정해서 설정하는 방법 따로 정리하도록 하자!)

- `.eslintrc.js` 파일 내용 수정하기

    JSX 문법에서는 lint를 제외해주도록 하고, jest를 사용하는 경우에는 아래와 같이 jest: true를 설정해주도록 한다.

    ```javascript
    module.exports = {
        env: {
        browser: true,
        es2021: true,
        jest: true,
        },
        extends: [
        'xo',
        'plugin:react/recommended',
        'plugin:react/jsx-runtime',
        ],
        parser: '@typescript-eslint/parser',
        parserOptions: {
        ecmaFeatures: {
            jsx: true,
        },
        ecmaVersion: 12,
        sourceType: 'module',
        },
        plugins: [
        'react',
        '@typescript-eslint',
        ],
        settings: {
        'import/resolver': {
            node: {
            extensions: ['.js', '.jsx', '.ts', '.tsx'],
            },
        },
        },
        rules: {
        indent: ['error', 2],
        'no-trailing-spaces': 'error',
        curly: 'error',
        'brace-style': 'error',
        'no-multi-spaces': 'error',
        'space-infix-ops': 'error',
        'space-unary-ops': 'error',
        'no-whitespace-before-property': 'error',
        'func-call-spacing': 'error',
        'space-before-blocks': 'error',
        'keyword-spacing': ['error', { before: true, after: true }],
        'comma-spacing': ['error', { before: false, after: true }],
        'comma-style': ['error', 'last'],
        'comma-dangle': ['error', 'always-multiline'],
        'space-in-parens': ['error', 'never'],
        'block-spacing': 'error',
        'array-bracket-spacing': ['error', 'never'],
        'object-curly-spacing': ['error', 'always'],
        'key-spacing': ['error', { mode: 'strict' }],
        'arrow-spacing': ['error', { before: true, after: true }],
        'import/no-extraneous-dependencies': ['error', {
            devDependencies: [
            '**/*.test.js',
            '**/*.test.jsx',
            '**/*.test.ts',
            '**/*.test.tsx',
            ],
        }],
        'import/extensions': ['error', 'ignorePackages', {
            js: 'never',
            jsx: 'never',
            ts: 'never',
            tsx: 'never',
        }],
        'react/jsx-filename-extension': [2, {
            extensions: ['.js', '.jsx', '.ts', '.tsx'],
        }],
        },
    };
    ```

- Root 경로에 `.eslintignore` 파일 생성 및 .gitignore과 동일한 내용으로 작성해준다.

#### <ins><b>(STEP 5)</b> VSCode에서 저장할 때 마다 ESLint 검사하도록 설정 추가하기</ins>

- `.vscode` 폴더에 `settings.json` 파일을 생성하고, 아래 내용을 추가한다.

    ```json
    {
    <!-- 코드 길이를 80자로 설정하고 다음 라인으로 줄바꿈 -->
    "editor.rulers": [
        80
    ],
    <!-- 저장시 eslint에 맞게 수정 -->
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
    },
    <!-- 코드 끝에 불필요한 띄어쓰기 제거 -->
    "trailing-spaces.trimOnSave": true
    }
    ```

#### <ins><b>(STEP 6)</b> React 관련 설치하기</ins>

- React와 React rendering을 위해 필요한 react-dom을 설치

    ```zsh
    npm i react react-dom
    ```
- 오래된 라이브러리의 경우, 라이브러리 내부에 타입 선언이 없기 때문에 아래와 같이 타입 파일도 같이 설치해주도록 한다.
    ```zsh
    npm i -D @types/react @types/react-dom
    ```

#### <ins><b>(STEP 7)</b> Jest 관련 설치하기</ins>

- 프론트엔드 테스팅을 위한 라이브러리인 `Jest`를 설치한다.
    ```zsh
    npm i -D jest @types/jest @swc/core @swc/jest \
    jest-environment-jsdom \
    @testing-library/react @testing-library/jest-dom@5.16.4
    ```

- `jest.config.js` 파일 추가

    ```js
    module.exports = {
        testEnvironment: 'jsdom',
        <!-- 테스트 코드에서 QueryByText 없는 것을 확인할 때 이 부분이 필수로 설정해야 한다.(구체적인 이유에 대해서 찾아서 정리하기) -->
        setupFilesAfterEnv: [
        '<rootDir>/src/setupTests.ts',
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
        testPathIgnorePatterns: [
            '<rootDir>/node_modules/',
            '<rootDir>/dist/',
        ],
    };
    ```
    - `src/setupTests.ts` 경로에 파일을 생성하고 생성한 파일에 아래 내용을 추가한다.
        ```typescript
        /* eslint-disable import/no-unresolved */
        import '@testing-library/jest-dom';
        ```

    - `@testing-library/jest-dom` 6.0.0 버전부터 변경된 내용이 있다.

        최신버전을 사용하는 경우, `jest.config.js` 파일 속 `setupFilesAfterEnv`의 `@testing-library/jest-dom/extend-expect` 설정 대신 `jest-setup.js` 파일에 `import '@testing-library/jest-dom'`를 추가한다.

#### <ins><b>(STEP 8)</b> Parcel 관련 세팅하기</ins>

웹 애플리케이션 번들러인 Parcel를 설치해서 자바스크립트 파일들을 하나로 합쳐준다.

- Parcel 설치
    ```zsh
    npm i -D parcel
    ```

- 정적 asset을 추가할 수 있도록 설정하기

    정적 asset을 추가하기 위해 `parcel-reporter-static-files-copy` 패키지를 설치한다.

    ```bash
    npm install -D parcel-reporter-static-files-copy
    ```

- `.parcelrc` 파일 설정을 추가하여 static 폴더를 정적 파일 루트로 설정한다.
    ```json
    {
        "extends": ["@parcel/config-default"],
        "reporters":  ["...", "parcel-reporter-static-files-copy"]
    }
    ```

#### <ins><b>(STEP 9)</b> 배포하기</ins>

- 아래 명령어로 dist 폴더 아래에 배포할 정적 파일들을 생성한다.
    ```zsh
    npx run build
    ```

- dist 폴더 생성되면, dist 폴더로 이동하여 서버로 띄우기
    ```zsh
    npx servor
    ```

- dist 폴더 외부에서 서버로 띄우기 위해서는 아래 명령어를 실행한다.
    ```zsh
    npx servor dist
    ```

#### <ins><b>(STEP 10)</b> package.json 스크립트 명령어 수정하기</ins>

parcel로 서버 실행, 배포, ESLint 체크 및 TypeScript 체크, Jest 테스트 간편 명령어 설정을 위해 npm script를 설정한다.

```json
{
 "source":"./index.html",
 "scripts": {
      "start": "parcel --port 8080",
      "build": "parcel build",
      "check": "tsc --noEmit",
      "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
      "test": "jest",
      "coverage": "jest --coverage --coverage-reporters html",
      "watch:test": "jest --watchAll"
    },
}
```
기본적으로 parcel 서버 실행 명령어가 `npm parcel index.html`이지만, index.html을 계속 입력해서 작성하기 어렵기 때문에 `"source": "./index.html"`을 `"main": "index.js"` 대신해서 작성하도록 한다.


### <ins>에러해결</ins>

#### <ins>(에러 1)</ins>
`npm run lint` 명령 실행시에 아래와 같은 에러가 발생하는 경우,

```zsh
  1:1  error  Definition for rule 'import/no-extraneous-dependencies' was not found  import/no-extraneous-dependencies
  1:1  error  Definition for rule 'import/extensions' was not found                  import/extensions
```

우선 `npm i -D eslint-plugin-import` 설치를 한 뒤에 `.eslintrc.js`파일을 수정해주도록 한다.

```javascript
module.exports = {
  plugins: ['import'], // Specify the ESLint plugin
  extends: [
    // Extend recommended configurations
    'eslint:recommended',
    'plugin:import/recommended',
  ],
  rules: {
    // Additional rules or overrides as needed
  },
};
```

#### <ins>(에러 2)</ins>

```zsh
Warning: React version not specified in eslint-plugin-react settings. See https://github.com/jsx-eslint/eslint-plugin-react#configuration .
```

`.eslintrc.js` 파일의 아래 부분에 다음과 같이 수정한다.

```javascript
:
settings: {
    react: {
        version: 'detect',
    },
    :
```

### Markdown-cli 설치 및 사용

.vscode/settings.json

```json
{
  "editor.rulers": [
    80
  ],
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "markdownlint.autoFix": true
  },
  "trailing-spaces.trimOnSave": true
}
```

```zsh
npm i -g markdownlint-cli

markdownlint --fix README.md
```