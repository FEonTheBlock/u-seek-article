# CSS-in-JS 관련 논의 & 발표

### CSS in JS

- [https://github.com/streamich/freestyler/blob/master/docs/en/generations.md](https://github.com/streamich/freestyler/blob/master/docs/en/generations.md)
- 장점
    - 컴포넌트 기반 개발
    - 선택자의 충돌을 생각할 필요가 없다.
    - 자동 벤더 프리픽스
    - JS와 CSS 사이의 상수와 함수를 공유할 수 있다.
    - 트리 셰이킹 (Dead Code Elimination)
    - 유닛 테스트
    
    → 개발 친화적 ( 높은 DX )
    
- 1세대
    - [css-modules](https://github.com/css-modules/css-modules)
    - JS로 작성하지 않는다. ← CSS 전처리기 사용
- 2세대
    - [Radium](https://github.com/FormidableLabs/radium)
    - 인라인 스타일 (스타일을 Dom에 직접주입) → CSS의 모든 기능을 사용할 수 없다 (대다수의 가상 선택자, html, body 태그 등)
        
        ```tsx
        import Radium from 'radium';
        import React from 'react';
          
        function App() {
          const style={
            ':hover':{
              backgroundColor: 'green'
            }
          }
          return (
            <div>
              <h3>Now you can see hover is working in inline styling</h3>
            <button style={style}>example of radium</button>
            </div>
          );
        }
          
        export default Radium(App);
        ```
        
- 3세대
    - [aphrodite](https://github.com/Khan/aphrodite), [cssx](https://github.com/krasimir/cssx), [glamor](https://github.com/threepointone/glamor), [typestype](https://github.com/typestyle/typestyle), [styletron](https://github.com/streamich/freestyler/blob/master/docs/en/lib-styletron)
    - CSS 템플릿을 JS로 작성할 수 있고 CSS를 DOM <style> 태그에 주입한다.
    - 템플릿이 정적이므로 컴포넌트의 프롭스를 사용할 수 없다. ← 한 번만 평가돼 CSS로 변환된다.
        - zero-runtime(build-time)
    - 런타임에 변하는 변수에 접근할 수 없다. → tradeoff 성능
- 4세대
    - [styled-components](https://github.com/styled-components/styled-components), [glamorous](https://github.com/paypal/glamorous)
    - 런타임에 변하는 변수에 접근할 수 있다.
    - 일반적으로 컴포넌트의 prop or state가 변할 때 리렌더 된다.
- ~~5세대~~
    - 컴포넌트의 `.render()` 함수 스코프에서 JS 변수를 사용할 수 있다.

## The tradeoffs of CSS-in-JS

- [https://medium.com/free-code-camp/the-tradeoffs-of-css-in-js-bee5cf926fdb](https://medium.com/free-code-camp/the-tradeoffs-of-css-in-js-bee5cf926fdb)
- Social Impact
    - 자바스크립트를 모르고 웹 개발을 하는 사람도 있다.
    - CSS-in-JS는 개발자들의 워크플로우에 큰 영향을 끼쳤다.
- Runtime Cost
    - CSS가 브라우저에서 JS의 런타임에 생성된다. (런타임 오버헤드 발생)
    - 라이브러리 마다 런타임 오버헤드를 처리하고 발생하는 부분이 다르며 전략에 대한 4가지 방법이 있다.
    1. Runtime Generation only
        - JS가 문서 로드되고 CSS가 생겨난다.
    2. Runtime Generation with Critical CSS
        - 중요한 CSS를 먼저 생성 → 초기 단계에서 UI를 차단하지 않는다.
    3. Build-time extraction only
        - 일반 웹의 기본전략
        - 일부 CSS-in-JS는 빌드 시 정적 CSS를 추출할 수 있다.
        - 런타임의 JS 값(상태)에 접근할 수 없다.
        - 렌더링 차단 요소 (CSS)
    4. Build-time extraction with Critical CSS
        - 빌드 타임에 중요한 CSS를 먼저 생성
- 접근성
    - 정적 사이트가 크리티컬 CSS의 추출 없이 구현될 때, HTML은 로드되어 평가되기 전까지 웹 페이지를 그릴 수 없다.
    - 임베드 방식의 문제점
- Critical CSS 코스트
    - CSS-in-JS에서 Critical CSS를 추출하기 위해선 SSR이 필요하다.
- 렌더링 블랙박스
    - 사용하는 CSS-in-JS 마다 CSS를 렌더링 하는 프로세스가 다르다.
- 러닝 커브
- 상호 운용성
- 보안 위험
- 읽을 수 없는 클래스

## Runtime CSS-in-JS

- [https://so-so.dev/web/css-in-js-whats-the-defference/](https://so-so.dev/web/css-in-js-whats-the-defference/)
- styled-components
    
    ```tsx
    // https://github.com/styled-components/styled-components/blob/8165cbe994f6f749236244f6f7017c2f0b9afcfe/packages/styled-components/src/constructors/constructWithOptions.ts#L39-L44
    /* Modify/inject new props at runtime */
    templateFunction.attrs = <Props = OuterProps>(attrs: Attrs<Props>) =>
      constructWithOptions<Constructor, Props>(componentConstructor, tag, {
        ...options,
        attrs: Array.prototype.concat(options.attrs, attrs).filter(Boolean),
      });
    ```
    
    - 페이지에서 사용하는 css만 head에 style tag로 삽입한다.
    - 최초 렌더링 이후 prop이나 state에 의해 변경되는 스타이을 style tag에 동적으로 삽입된다. (dev에선 DOM tree 변경)
    
    > **DOM injection (styled-components:)**이 방식은 DOM(**`<head>`**또는 **`<body>`**어딘가)에 **`<style>`**tag를 추가하고 [appendChild](https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild) 를 style node를 추가하는 방식입니다. [textContent](https://developer.mozilla.org/ko/docs/Web/API/Node/textContent) , [innerHTML](https://developer.mozilla.org/ko/docs/Web/API/Element/innerHTML) 을 추가하여 스타일 시트를 업데이트합니다.
    > 
- Emotion
    - styled-components와 비슷
    - [https://junghan92.medium.com/번역-우리가-css-in-js와-헤어지는-이유-a2e726d6ace6](https://junghan92.medium.com/%EB%B2%88%EC%97%AD-%EC%9A%B0%EB%A6%AC%EA%B0%80-css-in-js%EC%99%80-%ED%97%A4%EC%96%B4%EC%A7%80%EB%8A%94-%EC%9D%B4%EC%9C%A0-a2e726d6ace6)

## zero-runtime css-in-js

- linaria → 내부적으로 css variable을 사용해 동적으로 css 변경이 가능하다.
- 추출된 Critical CSS는 주요 렌더링 경로에서 사용하므로 문서 상단에 주입하고, 나머지는 link 태그로 로드하여 렌더링을 막지 않고 비동기로 로드할 수 있다. → `link 태그 비동기 로드?`

## near zero-runtime css-in-js

- stitches
    - cssom 수정 방식
    
    > **CSSStyleSheet API (stitches.js)**
    [CSSStylesSheet.insertRule](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleSheet/insertRule)을 사용하여 CSSOM에 직접 삽입합니다. 이 접근 방식을 사용하면
    > 
    > 
    > ```
    > <style>
    > ```
    > 
    > 태그에서는 빈 내용이 보이게 되고, DevTools에서 직접 선택하여 rule을 확인해야만 결과를 볼 수 있습니다.
    > 
    - styled-components에서는 prop에 의한 완전한 동적 스타일링이 가능하지만 stitches는 사전에 정의한 variants에 의한 스타일링만 가능

## Atomic CSS

### Tailwindcss

- [https://fe-developers.kakaoent.com/2022/220210-css-in-kakaowebtoon/](https://fe-developers.kakaoent.com/2022/220210-css-in-kakaowebtoon/)
- 미리 정의된 className을 조합해 스타일링 하는 방식
- rem이 기본단위 → 유틸리티 클래스 중에 하나를 골라야 한다.
- 애니메이션, 트랜지션 사용에 제약
- 자바스크립트 상태/값 공유 불가
- purge 설정을 통해 사용하지 않는 스타일은 결과물에서 제외할 수 있지만, Critical CSS에 대해 판단을 할 수 없다. (?)

## Stitches

- [https://stitches.dev/](https://stitches.dev/)
- variants를 사용해 성능 상에 이점은 있지만 어느정도 고수준의 디자인 토큰인 variants을 만들어내야 하는 코스트가 발생한다.
- css-in-js는 다른 라이브러리에 의존해 사용법이 언제든 달라질 수 있다.
- 렌더링 블랙박스

- 반복되는 스타일에 대해 atomic class로 변환해 같은 class를 사용할 수 있도록 최적화 해준다.
    
    → 같은 스타일이더라도 내부 순서가 변경되면 같은 class를 사용하지 않는다.
    
- —> 궁금한 점 렌더링이 발생할 때 마다 직렬화가 발생하는지?

## Tailwind vs Css-in-JS

- [https://sancho.dev/blog/tailwind-and-design-systems](https://sancho.dev/blog/tailwind-and-design-systems)
- [https://fe-developers.kakaoent.com/2022/221013-tailwind-and-design-system/](https://fe-developers.kakaoent.com/2022/221013-tailwind-and-design-system/)

## Further

- [https://github.com/andreipfeiffer/css-in-js/blob/main/README.md#1-using-style-tags](https://github.com/andreipfeiffer/css-in-js/blob/main/README.md#1-using-style-tags)
- [https://velog.io/@bepyan/CSS-in-JS-라이브러리에-대한-고찰](https://velog.io/@bepyan/CSS-in-JS-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0)
- [https://blog.cometkim.kr/posts/css-optimization-in-jamstack/](https://blog.cometkim.kr/posts/css-optimization-in-jamstack/)
- [https://pustelto.com/blog/css-vs-css-in-js-perf/](https://pustelto.com/blog/css-vs-css-in-js-perf/)
- [https://ped.ro/writing/why-i-build-design-systems-with-stitches-and-radix](https://ped.ro/writing/why-i-build-design-systems-with-stitches-and-radix)
- [https://fe-developers.kakaoent.com/2022/221013-tailwind-and-design-system/](https://fe-developers.kakaoent.com/2022/221013-tailwind-and-design-system/)
- Remix vs Next.js
    - [https://blog.bitsrc.io/remix-vs-next-js-a-detailed-comparison-6ff557f7b41f](https://blog.bitsrc.io/remix-vs-next-js-a-detailed-comparison-6ff557f7b41f)
    
    > Next.js supports server-side rendering (SSR), static site generation (SSG ), Incremental site regeneration (ISR), and CSR (client-side rendering) . On the other hand, Remix only supports SSR and CSR.
    > 
- typescript incremental