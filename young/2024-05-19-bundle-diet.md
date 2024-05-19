## [토스 SLASH 21 JavaScript Bundle Diet](https://www.youtube.com/watch?v=EP7g5R-7zwM&list=PL1DJtS1Hv1PiGXmgruP1_gM2TSvQiOsFL&index=58)

**번들 최적화가 중요한 이유**
- 이미지는 다운로드 + 디코딩만 거침
- JS는 다운로드, 파싱, 컴파일, 실행까지 여러 단계를 거쳐 같은 용량이어도 처리 비용이 더 높음

**번들 사이즈 문제를 찾는 것**
- Webpack기준 - Webpack Analyse, Webpack Visualizer, **Webpack Bundle Analyzer(강사추천)**
    - Webpack Bundle Analyzer는 용량별로 시각화 기능을 제공, 어떤 라이브러리가 많이 사용되는지 알 수 있음

**번들 용량 줄이기 시작**
- 용량이 큰 라이브러리는 가벼운 라이브러리로 대체
- 용도가 곂치면 하나의 라이브러리로 대체

**같은 라이브러리지만 버전이 다른 경우가 존재**
- npm의 특성 때문, A,B란 라이브러리가 C라는 같은 라이브러리를 쓰지만 버전이 다른 경우(Dependency conflict)
    - npm은 트리 구조로 필요한 버전을 모두 받는 방식을 선택해서 유저의 선택이 불필요
    - 중복 모듈이 쌓이면 node_modules도 과도하게 큰 용량을 차지하게됨
    - npm은 node_modules가 과도하게 커지는 문제를 해결하기 위해 시멘틱 버전(semver)를 지킨다고 가정함.
        - samver를 지킨다면 메이저버전이 바뀌지 않는 한, 더 높은 버전을 사용해도 문제가 없어 더 높은 버전만 받아 중복을 줄이고 있음
    - `npm dedupe` 중복된 모듈을 줄일 수 있는 명령어 제공
    - yan을 사용한다면 yarn-deduplicate 모듈을 통해 중복 제거
        - yarn2에선 `npm dedupe`처럼 `yarn dedupe`을 제공

**라이브러리 중복 줄이기**
- babel-plugin-transform-imports 모듈을 이용하면 소스코드를 수정하지 않고도 결과물을 최적화할 수 있음
    - 모듈 자체를 임포트하지 않고 필요한 부분만 임포트하게 변환해주는 플러그인

**더 가벼운 라이브러리 사용하기**
- lodash는 생각보다 용량이 큼
    - shorthuands 표현, 캐싱 등을 지원하기 때문
    - **가능한 네이티브 함수를 사용하거나, 더 가벼운 함수를 구현하여 사용**
- Webpack은 필요한경우 node와 브라우저 통일성을 위해 polyfill을 추가할 수 있음
    - 그러나 polyfill의 크기가 커서 추가될 경우 더 느려지는 경우도 발생 가능
    - **명시적으로 polyfill을 끄거나, browser를 잘 판단하는 라이브러리를 사용**

**Bundle Phobia**
- 라이브러리 검색할 수 있는 웹사이트

**라이브러리를 만들 때 중요한 것**
- 트리쉐이킹(tree-shaking)
    - 정적분석을 통해 필요한 코드만 가져오는 기술
    - 제거해도 side effect가 없을때만 가능
    - webpack은 rollup에 비해 side effects 판단을 어려워하는편
- 라이브러리를 만들 때 webpack에게 side effect 여부를 알려줘야 함
    - presesrveModules
- [terser](https://terser.org/) 라이브러리를 이용, side effect 유무를 판단해서 코드를 제거(pure annotation)

**webpack stats**
- 라이브러리가 의도와 다르게 side effects가 발생하는 경우 찾기 힘듦
- webpack-cli stats, nextjs사용 시 webpack-stats-plugin을 사용
    - json 포맷으로 추출, webpack analyse서비스에 올리면 모듈과 모듈의 관계를 분석할 수 있음

**라이브러리 영향 줄이기**
- Single Common Chunk
    - 라이브러리 추가 시 모든 페이지 용량이 증가됨
    - nextjs는 구글 제안에 따라 여러 청크로 나눔
        - Framework, Commons, Shared AB, Page A, Page B, Page C…
    - 청크를 나누면 라이브러리를 사용하는 페이지에서만 받기 때문에 영향 최소화 가능
- 필요할 때만 받는 **dynamic import**
    - webpack magic comment를 이용하면 prefetch 기능을 이용할 수 있음
        - prefetch 기능은 미리 받아두는 기능

## 최대 콘텐츠 렌더링 시간(LCP, Largest Contentful Paint)](https://www.youtube.com/watch?v=3rAf_Yx7R_g)
- 브라우저는 텍스트보다 이미지를 그리는데 시간이 오래걸림
    1. 이미지 찾고
    2. 다운로드 하고
    3. 그리고
- `loading` - 언제 리소스를 패치할건지(lazy | eager)
- `fetchpriority` - 리소스를 패치하는게 얼마나 중요한지(high | low | auto)
    - loading=lazy, fetchpriority=high는 같이 쓰지 말 것
    - loading=lazy - 화면에 표시되지 않는다면 사용
    - fetchpriority=high 표시된 화면에서 매우 중요한 이미지라면 사용
    - 확실치 않다면 둘 다 기본으로 쓰지 말것
- 주의점 - 브라우저는 기본적으로 우선순위를 잘 설정하기 때문에 위 기능은 오버라이드 하게 됨
    - 꼭 필요한 이유가 있을 때 사용할 것
    - 모두 high를 설정하면 우선순위는 의미없게 됨

## Chrome DevTools - Google IO](https://www.youtube.com/watch?v=nIO_q1u1u0E)
- 네트워크 에러 항목을 잡고 우클릭 - override content, 서버 데이터를 모킹해서 테스트 가능
    - header 내용도 모킹가능
- 특정 행동을 했을 때만 UI 디버깅이 가능할 때
    - 클릭했을 때만 표시되는 드랍다운 등
    - Styles탭 `Emulate a focused page` 체크
		- 이제 CSS명에 마우스 호버 시 툴팁으로 설명표시됨
- 에러 발생 시 우측 상단에 에러 설명(AI로)을 해주는 버튼이 추가됨
- minified 됐어도 소스 variable로 eval한 값을 볼 수 있음
- 크롬 익스텐션이 만드는 노이즈 로그들을 숨길 수 있음
- 코드 디버깅할 때 프레임워크나 외부 모듈의 스택트레이스를 숨길 수 있음


## 실제로 제품에 적용해본 내용

- `npm dedupe`를 사용해서 배포 사이즈 50kb 정도 축소함.ㅓ
- 라이트하우스로 성능 측정 후, LCP(Largest Contentful Paint) 로드 지연을 발견, 반응형 배너 이미지 로드 시점이 우선순위가 낮아 발생하여 첫 번째 캐러셀 이미지의 `fetchpriority`를 high로 변경해서 로드지연을 축소함.(로드지연의 비중이 50% 정도에서 -> 30% 미만으로 축소됨)
