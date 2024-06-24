# Express.js로 node 서버 만들기

### 패키지 초기화
```zsh
npm init -y
```

### TypeScript 
```zsh
npm i -D typescript
npx tsc --init
```

### ESLint
```zsh
npm i -D eslint
npx eslint --init
```

### ts-node 설치
타입스크립트 파일을 실행하기 위해서 설치
```zsh
npm i -D ts-node
```

### Express 설치
```zsh
npm i express
npm i -D @types/express
```

### nodemon 설치
```zsh
npm i -D nodemon
```

### REST API
Resource와 HTTP Verb만 도입한 수준으로 사용하도록 한다.

CREATE - POST /products `(상품 추가)`

READ - GET /products `(상품 목록 확인)`

READ - GET /products/{id} `(특정 상품 정보 확인)`

UPDATE - PUT 또는 PATCH /products/{id} `(특정 상품 정보 변경(JSON 정보와 함께 전달)`

DELETE - DELETE /products/{id} `(특정 상품 삭제)`



### 기본 코드 작성

#### 클라이언트에서 보낸 JSON 데이터를 Server에서 제대로 파싱할 수 있도록 아래와 같이 코드를 작성한다.
```javascript
app.use(express.json());
```


