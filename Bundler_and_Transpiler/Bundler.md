### Parcel
- **목적**: 웹 애플리케이션 번들러
- **특징**:
  - **설정이 필요 없는 번들러**: 설정 파일 없이 자동 작동
  - **빠른 번들링**: 병렬 처리 및 파일 시스템 캐싱으로 빠른 번들링
  - **지원 파일 형식**: JavaScript, HTML, CSS, 이미지, 폰트 등 다양한 파일 형식 지원
  - **사용 사례**: 간단한 설정으로 빠르게 웹 애플리케이션 번들링
  - **장점**: 설정 없이 빠르게 시작 가능, 다양한 파일 형식 지원

  #### Parcel의 암묵적 처리
    - package.json의 source: "index.html"파일을 Parcel을 사용해서 start를 해주게 되면, 아래와 같이 Parcel이 script 태그를 넣어준다. 
        
      ```html
      <body>
          <script src="/my-project.3464ddca.js"></script>
      </body>
      ```

      ```html
      <body>
          <p>Hello Hyungi</p>
          <!-- bundler를 썼기 때문에 parcel이 한 번 변환을 해준다. -->
          <script type="module" src="./src/main.tsx"></script>
      </body>
      ```

### Vite
- **목적**: 차세대 프런트엔드 툴링
- **특징**:
  - **빠른 개발 서버**: ES 모듈을 활용해 빠른 핫 모듈 리플레이스먼트 제공
  - **최적화된 번들링**: 프로덕션 빌드를 위한 빠른 번들링 제공
  - **설정 파일**: 사용자 정의 가능하지만 기본 설정으로도 강력
  - **사용 사례**: Vue.js, React 등의 모던 프레임워크와 사용, 빠른 개발 환경 필요 시 유용
  - **장점**: 개발 중 빠른 피드백 제공, 모던 JavaScript 기능 지원

### SWC (Speedy Web Compiler)
- **목적**: JavaScript/TypeScript 컴파일러 및 번들러
- **특징**:
  - **속도**: Rust로 작성되어 매우 빠름
  - **사용 사례**: Babel과 유사한 역할을 하지만 더 빠른 컴파일 속도를 제공
  - **장점**: 빠른 컴파일 속도와 성능 향상에 집중  