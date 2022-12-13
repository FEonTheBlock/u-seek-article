[styled-jsx 공식문서](https://github.com/vercel/styled-jsx)

## Features

- Full CSS support, no tradeoffs in power
    
    ```jsx
    export default () => (
      <div>
        <p>only this paragraph will get the style :)</p>
    
        {/* you can include <Component />s here that include
             other <p>s that don't get unexpected styles! */}
    
        <style jsx>{`
          p {
            color: red;
          }
        `}</style>
      </div>
    )
    ```
    
- Runtime size of just **3kb** (gzipped, from 12kb)
- Complete isolation: Selectors, animations, keyframes
- Built-in CSS vendor prefixing
- Very fast, minimal and efficient transpilation (see below)
- High-performance runtime-CSS-injection when not server-rendering
- Future-proof: Equivalent to server-renderable "Shadow CSS"
- Source maps support
- Dynamic styles and themes support
    
    ```jsx
    onst Button = props => (
      <button>
        {props.children}
        <style jsx>{`
          button {
            padding: ${'large' in props ? '50' : '20'}px;
            background: ${props.theme.background};
            color: #999;
            display: inline-block;
            font-size: 1em;
          }
        `}</style>
      </button>
    )
    ```
    
- CSS Preprocessing via Plugins

- 번역
    - 완전한 CSS 지원, 성능 저하 없음
    - **단 3kb**의 런타임 크기 (12kb에서 gzip 압축)
    - 완전한 격리: 선택기, 애니메이션, 키프레임
    - 내장 CSS 벤더 프리픽스
    - 매우 빠르고 최소이며 효율적인 트랜스파일(아래 참조)
    - 서버 렌더링이 아닌 경우 고성능 런타임 CSS 주입
    - 미래 보장: 서버에서 렌더링 가능한 "Shadow CSS"와 동일
    - 소스 맵 지원
    - 동적 스타일 및 테마 지원
    - 플러그인을 통한 CSS 전처리

## Using in Next.js

Next.js automatically configures `styled-jsx` with babel or swc, you don't have to configure it manually.

## 사용예시들

```jsx
export default () => (
  <div className="root">
    <style jsx>{`
      .root {
        color: green;
      }
    `}</style>
  </div>
)
```

```jsx
export default () => (
  <div>
    <style jsx global>{`
      body {
        background: red;
      }
    `}</style>
  </div>
)
```

```jsx
import Select from 'react-select'
export default () => (
  <div>
    <Select optionClassName="react-select" />

    <style jsx>{`
      /* "div" will be prefixed, but ".react-select" won't */

      div :global(.react-select) {
        color: red;
      }
    `}</style>
  </div>
)
```

```jsx
import React from 'react'
import Link from 'some-library'

import css from 'styled-jsx/css'

const { className, styles } = css.resolve`
  a { color: green }
`

export default () => (
  <div>
    <Link className={className}>About</Link>
    {styles}
  </div>
)
```

```jsx
import Widget from '../components/Widget';

function Home() {
  return (
    <div className="container">
      <h1>Hello Next.js</h1>
      <Widget />
      <style jsx>{`
        .container {
          margin: 50px;
        }
        .container :global(.btn) {
          background: #000;
          color: #fff;
        }
      `}</style>
    </div>
  );
}

export default Home;
```

기타사항

- vscode 익스텐션을 깔아야 자동완성이나 하이라이트 됨
- sass와 함께 사용하려면 다른 설정 필요
