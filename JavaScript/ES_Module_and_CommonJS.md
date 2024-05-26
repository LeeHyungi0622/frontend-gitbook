(3) ES 모듈과 CommonJS 차이



### 명명 내보내기(named export) 사용

```javascript
// src/App.tsx 또는 src/App.js
export const App = () => {
  // 컴포넌트 로직
};

// 사용
import { App } from './App';
```

### 기본 내보내기(default export) 사용

```javascript
// src/App.tsx 또는 src/App.js
const App = () => {
  // 컴포넌트 로직
};

export default App;

// 사용
import App from './App';
```