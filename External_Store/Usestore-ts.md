# usestore-ts 사용

## (Step 1) Store 작성하기

```js
import { singleton } from 'tsyringe';

import { Store, Action } from 'usestore-ts';


@singleton()
@Store()
export default class CounterStore {

    count = 0;

    @Action()
    increase(step = 1) {
        this.count += step;
    }

    @Action()
    decrease() {
        this.count -= 1;
    }
}
```

## (Step 2) Custom hook 작성하기

```js
import { container } from 'tsyringe';

import { useStore } from 'usestore-ts';

import CounterStore from '../stores/CounterStore';

export default function useCounterStore() {
    // tsyringe의 container를 사용해서 CounterStore 정의 
    const store = container.resolve(CounterStore);
    
    return useStore(store);
}
```

## (Step 3) @Action() decorator 사용에 유의하며 복수개의 Store class 작성하기
비동기 함수에 @Action decorator를 붙이면 다르게 작동할 수 있다.
따라서 별도의 액션을 만들어서 예상치 못한 이슈에 대비하자.

```js
@singleton()
@Store()
class PostStore {
    posts: Post[] = [];
    
    asyn fetchPosts() {
        this.startLoading();
        // 여기에 @Action()을 주게 되면, 비동기 결과를 바로 
        // @Action()으로 반환하기 때문에 fetchPosts() 자체에 @Action() decorator를 달지 말고, fetchPosts() method와 같이 @Action() decorator를 가진 method를 분리해서 작성한다.
        const posts = await fetchPosts();

        this.completeLoading(posts);
    }

    @Action()
    startLoading() {
        this.posts = [];
    }

    completeLoading(posts: Post[]) {
        this.posts = posts;
    }
}
```

## (Step 4) PostStore를 사용하기 위한 custom hook 작성하기

```js
import { container } from 'tsyringe';

import { useStore } from 'usestore-ts';

import PostStore from '../stores/PostStore';

export default function usePostStore() {
    const store = container.resolve(PostStore);
    return useStore(store)
}
```

(Step 1)에서 (Step 4)까지 