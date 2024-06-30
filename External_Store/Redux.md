# Redux

```js
const dispatch = useDispatch();
const count = useSelector((state) => state.count);

// action creator
dispatch(increase());

dispatch({ type: 'increase' });
dispatch({ type: 'decrease' });

dispatch({ type: 'increase', payload: 10 });

```

Store는 기본적으로 state와 reducer를 가진다.

reducer는 state 상태값과 action을 인자로 갖는다.


(1) `src/stores/BaseStore.ts` 작성

(2) 작성한 `BaseStore.ts`를 기반으로  `src/stores/Store.ts` 작성

(3) `src/hooks/useDispatch.ts` 작성

(4) `src/hooks/useSelector.ts` 작성

(5) Action을 메서드로 처리하기 `src/stores/ObjectStore.ts`

(6) `(5)`에서 작성한 ObjectStore class를 상속해서 `src/stores/CounterStore.ts` 작성

(7) 작성한 ObjectStore를 간편하게 사용하기 위한 `src/hooks/useObjectStore.ts` hook 작성

(8) 작성한 `useObjectStore`를 활용한 `src/hooks.useCounterStore.ts` hook 작성