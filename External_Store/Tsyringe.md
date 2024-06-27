# TSyringe (씨린지)
주사기라는 뜻을 내포하고 있으며,가벼운 의존성 주입 컨테이너(Dependency Injection Container / IoC)이며 JS와 TS를 위해 만들어졌다.

이것을 사용해서 External Store를 관리하는데 활용할 수 있다.

tsyringe와 reflect-metadata를 설치해주도록 한다.
```zsh
npm i tsyringe reflect-metadata
```

main.tsx파일과 src/setupTests.ts 파일에서 reflect-metadata를 import해준다.

```js
import 'reflect-metadata';
```

싱글톤으로 관리할 Store 클래스 준비한다.

```js
import { singleton } from 'tsyringe';

@singleton()
export default class Store {
    count = 0;
}
```

싱글톤으로 선언한 Store 클래스를 아래와 같이 사용하도록 한다.

```js
import { container } from 'tsyringe';

const counterStore = container.resolve(CounterStore);
```

TSyringe에서 관리하는 객체를 아래와 같이 초기화 할 수 있다.

```js
container.clearInstances();
```


# error 해결

```
@singleton() 
```

decorator를 사용할때 에러가 발생하면, 이는 tsconfig.json 파일에서 아래 두 항목에 대해서 true로 활성화를 시켜줘야 한다.

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
  }
}
```