# SoC(Separation of Concern)

App
- Header
- Main
    - Greeting
    - Counter
    - Posts
    - PostForm
        - TextField
    - Footer



### [아키텍처 관점]

같은 레벨에 있는 

서로 관심사가 다르고 기능이 다르다. 

Layered Architecture에서는 사용자에게 가까운 것과 사용자에게 먼 것으로 구분한다. 

가장 가까운 건 UI를 다루는 부분, Business Logic을 다루는 부분, 그 너머에는 데이터에 접근하고 저장하는 부분으로 나눌 수 있게 된다.

```
사람 - 스위치(UI) -> 모터(비즈니스 로직) -> 바퀴
```

각자 다른 역할과 

UI / 비즈니스 로직 / 데이터 이런식으로 나눈다.


### [프로세스 관점]

Input -> Process -> Output


What's your name?

Input

Process -> Hello, [name] => 메시지
```
function greeting(name: string) {
    return `Hello, ${name}`;
}
```
Output

### Flux Architecture

```
Action -> Dispatcher -> Store -> View 
```

### Redux

```javascript
const state = {
    name: 'tester'
}

const nextState = { ...state, name: 'New Name' };
```

Input -> Action + dispatch

Process -> reducer

Output -> View(React)

### External Store(React 입장에서 Store가 외부에 존재한다.)

```javascript
const [count, setCount] = useState(0);
```

위와 같이 하지 않고, 

#### 강제 렌더링 custom hook

external store의 컨셉

외부에서 useState와 같은 별도의 hook으로 관리가 안되어도 강제 랜더링 처리를 통해

실제로 external store에 저장된 상태값들이 화면을 통해 렌더링된다.

```javascript
// useForceUpdate.ts
import { useState } from "react";

export default function useForceUpdate() {
    const [state, setState] = useState(0);
    
    const forceUpdate = () => {
        setState(state + 1);
    };

    return forceUpdate;
}
```


```javascript
// Counter.tsx
import { useState } from "react";
import useForceUpdate from "../hooks/useForceUpdate";

// count가 function 밖에 있고, setState로 관리가 안되기 
// 때문에 rendering이 되서 컴포넌트가 업데이트 되지 않는다.
let count = 0;

export default function Counter() {
    // 그래서 useForceUpdate라는 custom hook을 사용해서 초기값이 0인 
    // state를 setState를 통해서 1씩 증가시키는 처리를 해주고,
    // custom hook에서는 forceUpdate 함수 (setState 실행 함수)를 반환한다. 
    // setState 함수가 내부적으로 호출되기 때문에
    // 아래에서 count가 별도의 state로 관리가 되지 않아도 강제로 rendering
    // 되고 있는 것을 알 수 있다.
    const forceUpdate = useForceUpdate();
    
    const handleClick = () => {
        count += 1;
        forceUpdate();
    }

    return (
        <div>
            <p>{count}</p>
            <button type="button" onClick={handleClick}>
                Increase
            </button>
        </div>
    );
}
```

useForceUpdate custom hook을 아래와 같이 작성해서 단순화해줄 수 있다.
새로운 객체를 업데이트 하게 되면, 아래와 같이 지속적으로 업데이트 할 수 있다.
```javascript
import { useState } from "react";

export default function useForceUpdate() {
    const [, setState] = useState({});
    
    const forceUpdate = () => {
        setState({});
    };

    return forceUpdate;
}
```

```javascript
import { useCallback, useState } from "react";

export default function useForceUpdate() {
    const [, setState] = useState({});
    return useCallback(() => { setState({});}, []);
}
```