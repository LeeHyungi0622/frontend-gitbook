### jest 사용을 위한 jest.config.js 파일 작성

jest를 사용하기 위해서는 jest.config.js 파일 설정을 해줘야 한다. 

아래 설정을 보면, Babel 대신에 SWC를 사용해서 설정을 해주고 있는 것을 알 수 있다.
SWC는 Babel을 대체할 수 있는 고성능 JavaScript/TypeScript 컴파일러로, 매우 빠른 속도와 저메모리 사용을 자랑한다.
SWC를 사용하여 Babel을 대체할 수 있으며, Jest와 함께 사용될 수 있다.

```javascript
module.exports = {
    testEnvironment: 'jsdom',
    setupFilesAfterEnv: [

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
            legacyDecorator: true,
            decoratorMetadata: true,
          },
        },
      }],
    },
  };
```

#### Jest를 사용하는 상황에서 `SWC` or `Babel` 설정을 하는 이유?

Vite는 ES 모듈과 최신 JavaScript 기능을 사용하여 빠른 개발환경을 제공하지만, Jest는 Jest는 Node.js기반의 테스트 러너로서, 기본적으로 ES 모듈과 최신 JavaScript 문법을 지원하지 않는다. 
따라서 Babel이나 SWC를 사용해서 최신 JavaScript 및 JSX 코드를 구버전의 JavaScript로 변환할 필요가 있습니다.

#### `Jest`와 `RTL`(React Testing Library)

Jest는 거의 모든 것을 갖춘 테스팅 도구로, Mocha와 Chai처럼 RSpec의 describe-it을 지원하고, expect로 단언(assertion)할 수 있다. Mocking도 다양한 레벨에서 쉽게 사용할 수 있다.

그리고 RTL(React Testing Library)는 UI 테스트에 특화된 라이브러리로, 거의 E2E Test처럼 사용할 수 있다. 

단, `F/E 테스트 = only React 컴포넌트 테스트`가 되는 상황은 최대한 피하는 것이 좋다. 본질에 집중하지 못하고 너무 많은 테스트 코드를 작성할 위험이 있다. 유지보수를 돕기 위해 테스트 코드를 작성하는데, 테스트 코드를 잘 못 작성하면 오히려 유지보수를 저해할 수 있다.

#### RTL 사용법

```javascript
import { render, screen, fireEvent } from "@testing-library/react";


```

#### Test 결과 항목에 대한 이해
- Test Suites
    - 정의: 테스트 파일 단위입니다. 하나의 테스트 파일은 하나의 테스트 스위트로 간주됩니다.

    - 설명: 테스트 스위트는 관련된 여러 테스트를 그룹화하여 관리하는 단위입니다. describe 블록을 사용하여 논리적으로 관련된 테스트를 그룹화할 수도 있지만, Jest에서는 개별 테스트 파일 자체가 테스트 스위트로 간주됩니다.

    - 출력: 1 passed, 1 total는 총 1개의 테스트 스위트가 있었고, 그 중 1개가 통과했음을 의미합니다.

- Tests
    - 정의: 개별 테스트 단위입니다. 각 test 또는 it 함수가 하나의 테스트를 나타냅니다.

    - 설명: 테스트 스위트 내에서 개별적인 테스트 케이스를 정의합니다. expect 함수로 실제 코드의 동작을 검증합니다.

    - 출력: 2 passed, 2 total는 총 2개의 테스트가 있었고, 그 중 2개가 모두 통과했음을 의미합니다.

- Snapshots
    - 정의: 스냅샷 테스트에 사용되는 파일입니다. UI 컴포넌트와 같은 특정 상태를 캡처하여 나중에 비교할 수 있는 정적인 파일입니다.

    - 설명: 스냅샷 테스트는 주로 React 컴포넌트의 렌더링 결과를 검증하는 데 사용됩니다. 컴포넌트의 현재 렌더링 결과를 스냅샷으로 저장하고, 이후 테스트에서 이 스냅샷과 비교하여 변경 사항이 없는지 확인합니다.

    - 출력: 0 total는 현재 스냅샷 테스트가 없음을 의미합니다. 만약 스냅샷 테스트가 있었다면, 총 스냅샷 수와 일치하는지 여부를 표시합니다.
