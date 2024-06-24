# UseRef

(1) 아래와 같이 생성되는 컴포넌트 객체들이 같은 객체를 바라보지 않고, 개별 객체로써 관리를 하고 싶을때 useRef가 사용된다.

```javascript
export default function TimerControl() {
    // 모든 TimerControl component가 각자 개별 객체를 가지고 싶을 때 사용된다.(useRef)
    const ref = useRef(1);
    ref.current += 1;
    // useState는 상태값이 바뀌면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하지만,
    // ref는 다시 렌더링을 하지 않는다.
```

(2) useRef를 사용해서 컴포넌트 내 html 요소에 id를 지정해서 사용할 수 있다.

```javascript
const id = useRef(`input-${Math.random()}`);
:
:
<div>
    <label
    htmlFor={id.current}
    style={{
        padding: '1rem',
    }}
    >
    {label}
    </label>
    <input
    type="text"
    id={id.current}
    placeholder={placeholder}
    value={filterText}
    onChange={handleChange}
    />
</div>
```


Custom hook을 사용하게 되면, 실수를 줄일 수 있다.


함수의 최상위에서만 호출해야한다.

테스트시에도 customHook을 만들어서 mocking할때 유용하게 사용될 수 있다.

