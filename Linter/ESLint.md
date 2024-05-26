### Linter(린터)
소스코드를 분석하여 프로그램 오류, 버그, 스타일 오류를 분석해주는 정적 분석 (소스코드로 분석)

#### ESLint 설정하기

```zsh
npm init @eslint/config
```
위 명령을 통해 eslint.config.js 파일 (or eslint.config.mjs) 파일을 생성하고 lint를 실행한다.

#### VSCode에서 ESLint extension 설치하기

### .eslintrc.js와 eslint.config.mjs 파일의 차이에 대해 학습하기
- ESLint 설정에서 질문이 있는데, 강의에서 npx eslint --init을 하게 되면, .eslintrc.js 파일이 생성이 되는데, eslint.config.mjs가 생성이 됨

- .eslintrc.js
    - 형식 : CommonJS 모듈 형식을 사용
        ```javascript
        module.exports = {
            //ESLint 설정
        };
        ```
    - 사용시기 : 전통적으로 사용되어 왔으며, 대부분의 ESLint 설정 파일로 사용된다. `.eslintrc 파일 시리즈(.eslintrc, .eslintrc.json, .eslintrc.yaml, .eslintrc.yml)`

    - 호환성 : Node.js 환경에서 기본적으로 지원됨
        - ESM(ECMAScript Module) 방식이 아닌 CommonJS 방식으로 동작한다.
    
    - 이점 : 기존 프로젝트와의 호환성이 높고, 설정이 비교적 간단하다.

        ```javascript
        module.exports = {
            env: {
                browser: true,
                es2021: true,
                jest: true,
            },
            extends: [
                'airbnb',
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

- eslint.config.mjs
    - 형식 : ECMAScript Module 형식을 사용한다.
        ```javascript
        export default {
            // ESLint 설정
        }
        ```
    
    - 사용시기 : ESLint 8.21.0 이후 버전에서 도입된 새로운 설정 파일 형식으로, 최신 모듈 시스템을 지원하고, 모던 JavaScript의 표준에 맞추어 설계가 되었다.

    - 호환성 : Node.js에서 ESM을 사용하도록 설정해야 한다. `type: "module"`이 package.json에 포함되어 있어야 하거나, `.mjs` 확장자를 사용해야 한다.

    - 이점 : 최신 JavaScript 표준을 따르며, 모듈 시스템과의 호환성이 좋고, 프로젝트에서 ESM을 사용중이라면 자연스럽게 통합될 수 있다.

        ```javascript
        export default {
            env: {
                browser: true,
                es2021: true,
                jest: true,
            },
            ...
        ```

    - eslint 사용해서 파일 검사하기
        
        ```zsh
        npx eslint .

        npx eslint --fix .
        ```



