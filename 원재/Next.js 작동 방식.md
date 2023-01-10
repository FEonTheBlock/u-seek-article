# Next.js 작동 방식

> 원문: https://nextjs.org/learn/foundations/how-nextjs-works<br />
> 번역 문서(참조): https://velog.io/@surim014/how-next.js-works<br />
> 목적: Korean FE Article에 올라오는 내용을 제 방식대로 이해할 수 있도록 정리한 글입니다.

## Intro

> Next.js의 고급 기능들을 배우기 전에 Next.js 작동 방식의 기본 사항을 이해하는 것이 도움이 될 것이다.

- 리액트가 애플리케이션을 빌드하고 구조화하는 방법에 있어 상대적으로 뚜렷히 정해진 방법이 없다.
  - 라이브러리이기 때문에 React로 커스텀하게 SSR을 구현한다거나 자유롭게 사용하는 경우가 많다.
- 여기서 Next.js는 애플리케이션을 구조화하는 프레임워크로서 개발 프로세스와 최종 애플리케이션을 더 **빠르게 만드는데 도움이 되는 최적화를 제공**한다.

---

## 개발 및 프로덕션 환경

> 코드가 실행되는 환경: 개발 vs 프로덕션

**환경**이란 코드가 실행되는 컨텍스트로 생각할 수 있다.

- **개발**: 로컬에서 애플리케이션을 빌드하고 실행
- **[프로덕션](https://nextjs.org/docs/going-to-production)**: 애플리케이션이 배포되어 사용자가 사용할 수 있도록 준비하는 프로세스

### Next.js에 적용되는 방식

> Next.js는 애플리케이션의 개발 및 프로덕션 단계 모두에 대한 기능을 제공한다.

**개발**

> 개발자와 애플리케이션 빌드 경험을 최적화한다.<br />
> 환경마다 고려 사항과 목표가 다르기 때문에 애플리케이션을 개발 단계에서 프로덕션 단계로 이동하려면 수행해야 할 작업이 많다.
> - ex) [컴파일](https://nextjs.org/learn/foundations/how-nextjs-works/compiling), [번들링](https://nextjs.org/learn/foundations/how-nextjs-works/bundling), [경량화](https://nextjs.org/learn/foundations/how-nextjs-works/minifying), [코드 분할](https://nextjs.org/learn/foundations/how-nextjs-works/code-splitting)

- TS 및 [ESLint 통합](https://nextjs.org/docs/basic-features/eslint), [빠른 새로 고침](https://nextjs.org/docs/basic-features/fast-refresh) 등과 같은 개발자 경험을 개선하기 위한 기능을 함께 제공한다.
  - ESLint 통합이란 scripts를 제공하고 rule을 extends로 제공하는 것을 의미한다.
  - 빠른 새로 고침이란
    1. React Component(s)만 내보내는 파일을 수정했을 때 빠른 수정 반영
    2. React Component(s)가 아닌 파일을 수정했을 때는 해당 파일을 가져오는 다른 파일을 모두 다시 실행
    3. React tree 외부의 파일에서 가져온 파일을 수정한 경우 전체 리로드 수행

**프로덕션**

> 최종 사용자와 애플리케이션 사용 경험을 최적화한다.

- 성능과 접근성을 높이기 위해 코드를 변환하는 것을 목표로 한다.

### Next.js 컴파일러

> Next.js는 코드 변환 및 기본 인프라의 대부분을 처리하여 애플리케이션이 프로덕션으로 쉽게 전환되도록 한다.<br />
> 이것이 가능한 이유는 Next.js가 저수준 프로그래밍 언어인 Rust와 컴파일, 최소화, 번들링 등에 사용할 수 있는 플랫폼인 SWC로 작성된 [컴파일러](https://nextjs.org/docs/advanced-features/compiler)를 가지고 있기 때문에 가능하다.

---

## 컴파일이란 무엇일까요?

> 개발자는 JSX, TS 및 최신 버전의 JS와 같이 개발자에게 더 친숙한 언어로 코드를 작성한다.<br />
> 이러한 언어는 개발자의 효율성과 자신감을 향상시키지만, 브라우저가 이러한 언어들을 이해하기 위해서는 먼저 JS로 컴파일되어야 한다.

> 컴파일: 한 언어로 된 코드를 다른 언어 또는 해당 언어의 다른 버전으로 출력하는 프로세스

- Next.js에서 컴파일은 개발 단계 중 코드를 편집할 때 실행되며 프로덕션을 위해 애플리케이션을 준비하는 빌드 단계의 일부로 실행된다.
![image](https://user-images.githubusercontent.com/87295692/208616748-41e94952-9440-4c03-a35c-ed96170ba772.png)

---

## 경량화(Minifying)는 무엇일까요?

> 개발자는 사람의 가독성에 최적화된 코드를 작성한다.<br />
> 이 코드에는 주석, 공백, 들여쓰기, 여러 줄 등 코드 실행에 필요하지 않은 추가 정보가 포함될 수 있다.<br />
> 이를 경량화를 통해 파일 크기를 줄여 애플리케이션의 성능을 향상시키는 것이 주 목적이다.

> 경량화: 코드의 기능을 변경하지 않고 불필요한 코드 서식과 주석을 제거하는 프로세스

- Next.js에서 JS 및 CSS 파일은 프로덕션을 위해 자동으로 경량화 된다.
![image](https://user-images.githubusercontent.com/87295692/208617515-3f623635-a91f-4aa5-83af-c447b150ecae.png)

---

## 번들링이란 무엇일까요?

> 개발자는 애플리케이션을 더 큰 애플리케이션을 구축하는데 사용할 수 있는 모듈, 컴포넌트 및 함수로 **모듈화** 한다.<br />
> 이렇게 내부 모듈과 외부 서드파티 패키지를 내보내고 가져오면 복잡한 파일 종속성의 웹이 생성된다.

> 번들링: 사용자가 웹 페이지를 방문할 때 파일 요청 수를 줄이기 위해 복잡하게 얽혀있는 종속성을 풀고 파일 또는 모듈을 브라우저에 최적화된 번들로 병합하는 프로세스

![image](https://user-images.githubusercontent.com/87295692/208619248-9a6999f9-0158-4bd9-a374-eaf0af19ed92.png)

---

## 코드 분할이란 무엇일까요?

> 개발자는 일반적으로 애플리케이션을 서로 다른 URL에서 액세스할 수 있는 여러 페이지로 분할한다.<br />
> 이러한 각 페이지는 응용 프로그램에 대한 고유한 **진입점**이 된다.

> 코드 분할: 해당 페이지를 실행하는데 필요한 코드만 로드하여 애플리케이션의 초기 로드 시간을 개선하기 위해 애플리케이션의 번들을 각 진입점에 필요한 더 작은 청크로 분할하는 프로세스

- Next.js에는 코드 분할을 지원하는 기능이 내장되어 있다.
- `pages/` 디렉토리 내의 각 파일은 빌드 단계에서 자동으로 자체 JS 번들로 코드 분할된다.
![image](https://user-images.githubusercontent.com/87295692/208619755-9e0826dd-9c55-4df7-92ff-5b7975ff8d60.png)

### More

- 페이지 간에 공유되는 모든 코드는 추가 탐색에서 동일한 코드를 다시 다운로드하지 않도록 다른 번들로 분할된다.
- 초기 페이지 로드 후, Next.js는 사용자가 탐색할 가능서이 있는 다른 페이지의 [코드를 미리 로드](https://nextjs.org/docs/api-reference/next/link) 할 수 있다.
  - 페이지로 분할된 코드(JS 파일)를 미리 로드한다는 의미
- [Dynamic imports](https://nextjs.org/docs/advanced-features/dynamic-import)는 처음에 로드된 코드를 수동으로 분할하는 또 다른 방법이다.

---

## 빌드 시간 및 런타임

> 빌드 시간 또는 빌드 단계는 프로덕션을 위해 애플리케이션 코드를 준비하는 일련의 단계에 지정된 이름이다.<br />
> 애플리케이션을 빌드 할 때 Next.js는 코드를 [서버](https://nextjs.org/learn/foundations/how-nextjs-works/client-and-server)에 배포하고 사용자가 사용할 준비가 된 프로덕션 최적화 파일로 변환한다.<br />
> - 정적으로 생성된 페이지용 HTML 파일(SSG)
> - [서버](https://nextjs.org/learn/foundations/how-nextjs-works/client-and-server)에서 페이지를 [렌더링](https://nextjs.org/learn/foundations/how-nextjs-works/rendering) 하기 위한 JS 코드
> - [클라이언트](https://nextjs.org/learn/foundations/how-nextjs-works/client-and-server)에서 페이지를 동적으로 만들기 위한 JS 코드
> - CSS 파일

> 런타임(요청 시간): 애플리케이션이 빌드 및 배포된 후, 사용자 요청에 대한 응답으로 애플리케이션이 실행되는 기간

---

## 클라이언트 및 서버

> 웹 애플리케이션의 맥락에서 클라이언트는 애플리케이션 코드에 대한 요청을 서버로 보내는 사용자 장치의 브라우저를 나타낸다.<br />
> 클라이언트는 서버에서 받은 응답을 사용자가 상호 작용할 수 있는 인터페이스로 바꾼다.

> 서버: 애플리케이션 코드를 저장하고, 클라이언트의 요청을 받아 일부 게산을 수행하며, 적절한 응답을 다시 보내는 데이터 센터의 컴퓨터를 말한다.

![image](https://user-images.githubusercontent.com/87295692/208625676-2ab53392-0603-4397-8e7b-880c7a2238e2.png)

---

## 렌더링이란 무엇일까요?

> 렌더링: 리액트에서 작성한 코드를 해당 UI에 대한 HTML로 변환하기 위한 작업 단위
> - 렌더링은 원래 다양한 뜻이 있으나 Next.js에서 사용하는 렌더링이란 위와 같다.
> 렌더링은 서버 또는 클라이언트에서 발생할 수 있다.<br />
> 빌드 시간에 미리 발생하거나, 런타임 시 모든 요청에 발생할 수 있다<br />
> Next.js에서는 SSR 유형, SSG 유형 및 CSR 유형의 세 가지 렌더링 방법을 사용할 수 있다.

### Pre-Rendering

> SSR 및 SSG는 결과가 클라이언틀고 전송되기 전에 외부 데이터를 가져오고 리액트 컴포넌트를 HTML로 변환하기 때문에 Pre-Rendering이라고도 한다.

### CSR vs Pre-Rendering

> 일반적인 리액트 애플리케이션에서 브라우저는 서버로부터 빈 HTML과 함께 UI를 구성하기 위한 JS 명령을 전달받는다.<br />
> 초기 렌더링 작업이 사용자 장치에서 발생하므로 이를 CSR이라고 한다.

![image](https://user-images.githubusercontent.com/87295692/208627569-0deed9c0-90da-4123-8544-47dd11b7cd79.png)

### More

- 리액트의 `useEffect()` 또는 [useSWR](https://swr.vercel.app/)과 같은 데이터 fetching 훅을 사용하여 데이터를 가져오도록 선택해서 Next.js 애플리케이션의 특정 컴포넌트에 대해 CSR을 사용하도록 선택할 수 있다.
- 반면, Next.js는 기본적으로 모든 페이지를 미리 렌더링한다.
- 실제로 완전히 CSR로 렌더링 된 앱의 경우, 렌더링 작업이 수행되는 동안 사용자에게 빈 페이지가 표시된다.
- Pre-Rendering된 앱의 경우, 사용자는 완성된 HTML을 볼 수 있다.

![image](https://user-images.githubusercontent.com/87295692/208628209-728f6f5a-a44e-454e-ab04-7a9edf8ddfe7.png)

---

## Pre-Rendering의 두 가지 유형

### SSR

> SSR을 사용하면 페이지의 HTML이 각 요청에 대해 서버에서 생성된다.<br />
> 이후 생성된 HTML, JSON 데이터 및 페이지를 동적으로 만들기 위한 JS 명령이 클라이언트로 전송된다.

- 클라이언트에서 HTML은 빠르고 정적인 페이지를 표시하는데 사용되는 반면, 리액트는 JSON 데이터와 JS 명령을 사용하여 컴포넌트를 동적으로 만든다.(ex: 버튼에 이벤트 핸들러 연결)
  - 이러한 과정을 하이드레이션(hydration)이라고 한다.
- Next.js에서는 [getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props)를 사용하여 서버 사이드 렌더링 페이지를 선택할 수 있다.

> 참고: 리액트 18 및 Next 12에는 리액트 서버 컴포넌트의 알파 버전이 도입되었습니다. 서버 컴포넌트는 서버에서 완전히 렌더링 되며 렌더링 하기 위해 클라이언트 측 자바스크립트가 필요하지 않습니다. 또한 서버 컴포넌트를 통해 개발자는 서버에 일부 로직을 유지하고 해당 로직의 결과만 클라이언트에 전송할 수 있습니다. 이렇게 하면 클라이언트로 전송되는 번들 크기가 줄어들고 클라이언트 사이드 렌더링 성능이 향상됩니다. 리액트 서버 컴포넌트에 대한 자세한 내용은 [여기](https://reactjs.org/blog/2020/12/21/data-fetching-with-react-server-components.html)를 참조하십시오.

### SSG

> SSG를 사용하면 HTML이 서버에서 생성되지만, SSR과 달리 런타임에 서버가 없다.<br />
> 대신 콘텐츠는 애플리케이션이 배포될 때 빌드 시간에 한 번만 생성되며 HTML은 CDN에 저장되어 각 요청에 대해 재사용된다.

- Next.js에서는 [getStaticProps](https://nextjs.org/docs/basic-features/data-fetching/get-static-props)를 사용하여 페이지를 정적으로 생성하도록 선택할 수 있다.

> 참고: [Incremental Static Regeneration](https://nextjs.org/docs/basic-features/data-fetching/incremental-static-regeneration)을 사용하여 사이트를 구축한 후 정적 페이지를 생성하거나 업데이트할 수 있습니다. 즉, 데이터가 변경되더라도 전체 사이트를 재구축할 필요가 없습니다.

- Next.js의 장점은 SSG, SSR 또는 CSR 등 페이지별로 사용 사례에 가장 적합한 렌더링 방법을 선택할 수 있다는 것이다.
- 특정 사용 사례에 적합한 렌더링 방법에 대해 자세히 알아보려면 [데이터 패칭 문서](https://nextjs.org/docs/basic-features/data-fetching/overview)를 참조하면 된다.

---

## 네트워크란 무엇일까요?

> 애플리케이션 코드가 저장되고 네트워크에 배포된 후 실행되는 위치는 어디일까?<br />
> 네트워크란 리소스를 공유할 수 있는 컴퓨터(또는 서버)로 생각할 수 있다.<br />
> Next.js 애플리케이션의 경우, 애플리케이션 코드를 원본 서버, 콘텐츠 전송 네트워크(CDN) 및 Edge에 배포할 수 있다.

### 원본 서버

> 앞서 설명한 것처럼 서버란 애플리케이션 코드의 원본을 저장하고 실행하는 메인 컴퓨터이다.<br />
> 이 서버를 **CDN 서버** 및 **Edge 서버**와 같이 애플리케이션 코드가 배포될 수 있는 다른 장소와 구별하기 위해 **원본 서버**라는 용어를 사용한다.<br />
> 원본 서버는 요청을 받으면 응답을 보내기 전에 일부 계산을 수행한다.<br />
> 이 계산 작업의 결과는 CDN으로 이동할 수 있다.

### CDN(콘텐츠 전송 네트워크)

> CDN은 HTML 및 이미지 파일과 같은 정적 콘텐츠를 전 세계 여러 위치에 저장하고 클라이언트와 원본 서버 사이에 배치된다.<br />
> 새로운 요청이 들어오면 사용자와 가장 가까운 CDN 위치에서 캐시된 결과로 응답할 수 있다.

![image](https://user-images.githubusercontent.com/87295692/208632180-09fd9395-0967-4151-9e22-90ff5abbb011.png)

- 계산이 각 요청에서 발생할 필요가 없기 때문에 이렇게 하면 원본 서버의 부하가 줄어든다.
- 또한 사용자에게 지리적으로 더 가까운 위치에서 응답이 오기 때문에 사용자가 더 빠르게 작업할 수 있다.
- Next.js에서는 Pre-Rendering을 미리 수행할 수 있고 CDN은 작업의 정적 결과를 저장하는데 적합하여 콘텐츠 전송 속도가 빨라진다.

### Edge

> Edge란 사용자에게 가장 가까운 네트워크 주변부(fringe) 또는 Edge에 대해 일반화된 개념이다.<br />
> CDN은 네트워크 주변부에 정적 콘텐츠를 저장하기 때문에 Edge의 일부로 간주될 수 있다.

- CDN과 유사하게 Edge 서버는 전 세계 여러 위치에 배포된다.
- 그러나 정적 콘텐츠를 저장하는 CDN과 달리 일부 Edge 서버는 코드를 실행할 수 있다.
- 이는 캐싱과 코드 실행 모두 사용자에게 더 가까운 Edge에서 수행할 수 있음을 의미한다.
- Edge에서 코드를 실행하면 기존의 클라이언트 사이드 or 서버 사이드에서 수행되었던 작업 중 일부를 Edge로 이동할 수 있다.([Next.js의 예시 참조](https://vercel.com/features/edge-functions#:~:text=or%20steps%20required.-,CODE%20EXAMPLES,-Unlock%20the%20potential)) 이렇게 하면 클라이언트로 전송되는 코드의 양이 줄어들고 사용자 요청의 일부가 원본 서버로 다시 돌아갈 필요가 없으므로 대기 시간이 줄어들게 되고 이 덕에 애플리케이션의 성능이 향상될 수 있다.
- Next.js에서는 [미들웨어](https://nextjs.org/docs/middleware)를 사용하여 Edge에서 코드를 실행할 수 있으며 곧 [리액트 서버 컴포넌트](https://nextjs.org/docs/advanced-features/react-18/overview#react-server-components-alpha)에서도 코드를 실행할 수 있다.

> 추가 리소스: https://vercel.com/docs/concepts/edge-network/overview
