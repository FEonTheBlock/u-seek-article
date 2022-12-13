# Stitches

## radix-ui

- [https://github.com/radix-ui/design-system/tree/master/components](https://github.com/radix-ui/design-system/tree/master/components)

## API

### styled

- 스타일과 그 변형을 만드는 함수
    
    ```tsx
    const Button = styled('button', {
      // base styles
    
      variants: {
        variant: {
          primary: {
            // primary styles
          },
          secondary: {
            // secondary styles
          },
        },
      },
    });
    
    // Use it
    <Button>Button</Button>
    <Button variant="primary">Primary button</Button>
    ```
    

### css

- 스타일 객체로부터 클래스 이름을 생성하는 함수
    
    ```tsx
    const button = css({
      // base styles
    
      variants: {
        variant: {
          primary: {
            // primary styles
          },
          secondary: {
            // secondary styles
          },
        },
      },
    });
    
    // Use it
    <div className={button()}>Button</div>
    <div className={button({ variant: 'primary' })}>Primary button</div>
    ```
    

### globalCss

```tsx
const globalStyles = globalCss({
  body: { margin: 0 },
});

() => {
  globalStyles();
  return <div />;
};
```

### keyframes

```tsx
'0%': { transform: 'scale(1)' },
  '100%': { transform: 'scale(1.5)' },
});

const Button = styled('button', {
  '&:hover': {
    animation: `${scaleUp} 200ms`,
  },
});
```

### getCssText

- 서버 사이드에 스타일을 생성하는 함수

```tsx
<style id="stitches" dangerouslySetInnerHTML={{ __html: getCssText() }} />
```

### defaultThemeMap

- 기본 매핑과 병합하려고 할 때 사용할 수 있다.

```tsx
import { createStitches, defaultThemeMap } from '@stitches/react';

createStitches({
  themeMap: {
    ...defaultThemeMap,
    width: 'space',
    height: 'space',
  },
});
```

### createStitches

- 스티치를 구성하는 데 사용됨. 기본 테만 토큰, 커스텀 유틸리티, 미디어 쿼리 등을 추가해야 하는 경우에 사용된다.
    
    ```tsx
    import { createStitches } from '@stitches/react';
    
    createStitches({
      theme: object,
      media: object,
      utils: object,
      prefix: string,
      themeMap: object,
    });
    ```
    
    - `styled`: a function to create React components with styles.
    - `css`: a function to create CSS rules.
    - `globalCss`: a function to create global styles.
    - `keyframes`: a function to create keyframes.
    - `theme`: a function for accessing default theme data.
    - `createTheme`: a function for creating additional themes.
    - `getCssText`: a function get styles as a string, useful for SSR.
    - `config`: an object containing the hydrated config.

### theme

- 디자인 토큰을 셋업
    
    ```tsx
    createStitches({
      theme: {
        colors: {},
        space: {},
        fontSizes: {},
        fonts: {},
        fontWeights: {},
        lineHeights: {},
        letterSpacings: {},
        sizes: {},
        borderWidths: {},
        borderStyles: {},
        radii: {},
        shadows: {},
        zIndices: {},
        transitions: {},
      },
    });
    ```
    

### media

- 재사용 가능한 중단점 설정
    
    ```tsx
    createStitches({
      media: {
        bp1: '(min-width: 640px)',
        bp2: '(min-width: 768px)',
        bp3: '(min-width: 1024px)',
    		toBp2: '(width <= 768px)',
      },
    });
    ```
    

### utils

- DX를 향상시키기 위한 커스텀 유틸
    
    ```tsx
    createStitches({
      utils: {
        // A property for applying width/height together
        size: (value) => ({
          width: value,
          height: value,
        }),
    
        // A property to apply linear gradient
        linearGradient: (value) => ({
          backgroundImage: `linear-gradient(${value})`,
        }),
    
        // An abbreviated property for background-color
        bg: (value) => ({
          backgroundColor: value,
        }),
      },
    });
    ```
    

### prefix

- 전역 충돌을 방지하기 위한 모든 클래스 네임 CSS 변수들의 접두사를 설정한다.
    
    ```tsx
    createStitches({
      prefix: 'radix',
    });
    ```
    

### themeMap

- 테마 토큰에 대한 CSS 속성의 사용자 지정 매핑을 정의한다.
    
    ```tsx
    createStitches({
      themeMap: {
        // Map these properties to the `space` scale
        width: 'space',
        height: 'space',
      },
    });
    ```
    

### theme

- 기본 테마 데이터를 포함하는 객체
    
    ```tsx
    const { theme } = createStitches({
      theme: {
        colors: {
          background: 'white',
          foreground: 'black',
        },
      },
    });
    ```
    
- 다수의 테마를 사용할 때 유용한 기본 테마 클래스 적용
    
    ```tsx
    <div className={theme}>Content here</div>
    ```
    
- 또한, 테마 객체를 사용해 토큰을 읽어 사용할 수 있다.
    
    ```tsx
    // default theme
     theme.colors.foreground.value; // black
     theme.colors.foreground.token; // foreground
     theme.colors.foreground.scale; // colors
     theme.colors.foreground.variable; // --colors-foreground
     theme.colors.foreground.computedValue; // var(--colors-foreground)
    ```
    

### createTheme

- 기존테마를 재정의 하는 함수
    
    ```tsx
    export const darkTheme = createTheme({
      colors: {
        background: 'black',
        foreground: 'white',
      },
    });
    ```
    
- 첫 번째 인수로 전달해 사용할 테마 클래스를 정의할 수 있다.
    
    ```tsx
    export const darkTheme = createTheme('dark-theme', {...});
    ```
    

### style object

- JS 객체로 작성된 CSS
    
    ```tsx
    {
      color: 'red',
      backgroundColor: 'red',
      fontSize: '13px',
    
      '&:hover': {
        color: 'black'
      },
    
      '&.chained': {
        color: 'black'
      },
    
      '& .descendant': {
        color: 'black'
      },
    
      '@media (min-width: 400px)': {
        fontSize: '16px'
      }
    }
    ```
    

### locally scoped tokens

- `$$`를 접두사로 붙여 스타일 객체 내에 직접 토큰을 만들 수 있다.
    
    ```tsx
    const Button = styled('button', {
      $$shadowColor: 'red',
      boxShadow: '0 0 0 15px $$shadowColor',
    });
    ```
    

### scale-prefixed tokens

- 사용 가능한 테마 스케일 중 하나에 스케일 이름 앞에 토큰을 붙여 선택할 수 있다.
    
    ```tsx
    const Button = styled('button', {
      // apply a color token to a locally scoped token
      $$shadowColor: '$colors$purple500',
      boxShadow: '0 0 0 15px $$shadowColor'
    
      // use a token from the sizes scale
      marginTop: '$sizes$1'
    })
    ```
    

### as prop

- as prop을 통해 다른 요소로 바꾸어 렌더링 할 수 있다.
    
    ```tsx
    const Button = styled('button', {
      // base styles
    });
    
    () => (
      <Button as="a" href="https://github.com/stitchesjs/stitches">
        GitHub
      </Button>
    );
    ```
    

## Styling

### Object syntax

- 번들 크기를 최소화하기 위해 스티치는 문자열 구문보다는 자바스크립트 객체 표기법을 사용한다.

### Global styles

```tsx
const globalStyles = globalCss({
  '*': { margin: 0, padding: 0 },
});

() => {
  globalStyles();

  return <div ... />
};
```

### Base styles

```tsx
const Button = styled('button', {
  backgroundColor: 'gainsboro',
  borderRadius: '9999px',
  fontSize: '13px',
  border: '0',
});

() => <Button>Button</Button>;
```

- Pseudo-calss
    
    ```tsx
    const Button = styled('button', {
      // base styles
    
      '&:hover': {
        backgroundColor: 'lightgray',
      },
    });
    
    () => <Button>Button</Button>;
    ```
    
- Pseudo-element
    
    ```tsx
    const Button = styled('button', {
      // base styles
    
      '&::before': {
        content: `''`,
        display: 'block',
        backgroundImage: 'linear-gradient(to right, #1fa2ff, #12d8fa, #a6ffcb)',
        position: 'absolute',
        top: '-3px',
        left: '-3px',
        width: 'calc(100% + 6px)',
        height: 'calc(100% + 6px)',
        borderRadius: 'inherit',
        zIndex: -1,
      },
    });
    
    () => <Button>Button</Button>;
    ```
    
- Class selector
    
    ```tsx
    const Button = styled('button', {
      // base styles
    
      '&.custom-class': {
        boxShadow: '0 0 0 3px blueviolet',
      },
    });
    
    () => <Button className="custom-class">Button</Button>;
    ```
    
- Descendant Select
    
    ```tsx
    const Button = styled('button', {
      // base styles
    
      '& svg': {
        display: 'inline-block',
        verticalAlign: 'bottom',
        height: '13px',
        marginLeft: '5px',
      },
    });
    
    () => (
      <Button>
        Button <svg>...</svg>
      </Button>
    );
    ```
    
- Combinator selectors
    
    ```tsx
    const Button = styled('button', {
      // base styles
    
      '& + button': {
        marginLeft: '10px',
      },
    });
    
    () => (
      <div>
        <Button>Button</Button>
        <Button>Button</Button>
      </div>
    );
    ```
    
- Target a Stitches component
    
    ```tsx
    const Icon = styled('svg', {
      display: 'inline-block',
      marginLeft: '5px',
      width: '16px',
    });
    
    const Button = styled('button', {
      // base styles
    
      [`& ${Icon}`]: {
        marginLeft: '5px',
      },
    });
    
    () => (
      <Button>
        Button
        <Icon>...</Icon>
      </Button>
    );
    ```
    
- Target a React Component
    
    ```tsx
    const RightArrow = () => (
      <svg className="right-arrow" ... />
    );
    
    // add a `toString` method
    RightArrow.toString = () => '.right-arrow';
    
    const Button = styled('button', {
      // base styles
    
      [`& ${RightArrow}`]: {
        display: 'inline-block',
        marginLeft: '5px',
        width: '16px',
      },
    });
    
    () => <Button>Button <RightArrow /></Button>;
    ```
    
- Keyframe animations
    
    ```tsx
    const scaleUp = keyframes({
      '0%': { transform: 'scale(1)' },
      '100%': { transform: 'scale(1.5)' },
    });
    
    const Button = styled('button', {
      // base styles
    
      '&:hover': {
        animation: `${scaleUp} 200ms`,
      },
    });
    ```
    
- At-rule statements
    
    ```tsx
    const Button = styled('button', {
      // base styles
    
      '@media (prefers-reduced-motion)': {
        transition: 'none',
      },
    });
    ```
    
- Import rules
    - globalCss 함수에서 @import 사용할 수 있다.
    
    ```tsx
    const globalStyles = globalCss({
      '@import': 'custom.css',
    });
    
    ---
    
    const globalStyles = globalCss({
      '@import': ['custom1.css', 'custom2.css'],
    });
    ```
    
- Font face rules
    
    ```tsx
    const globalStyles = globalCss({
      '@font-face': {
        fontFamily: 'CustomFont',
        src: 'local("CustomFont"), url("CustomFont.woff2")',
      },
    });
    ```
    
    ```tsx
    const globalStyles = globalCss({
      '@font-face': [
        {
          fontFamily: 'CustomFont1',
          src: 'local("CustomFont1"), url("CustomFont1.woff2")',
        },
        {
          fontFamily: 'CustomFont2',
          src: 'local("CustomFont2"), url("CustomFont2.woff2")',
        },
      ],
    });
    ```
    
- Supports rule
    
    ```tsx
    const globalStyles = globalCss({
      '@supports (display: grid)': {
        body: {
          display: grid,
        },
      },
    });
    
    const Grid = styled('div', {
      '@supports (display: grid)': {
        display: grid,
      },
    });
    ```
    

### Token aware values

- 스티치는 [property-to-token mapping](https://stitches.dev/docs/tokens#property-mapping)을 사용해서 토큰을 인식하고 있으며 `$`을 사용해서 적용할 수 있다.
    
    ```tsx
    
    import { createStitches } from '@stitches/react';
    
    const { styled } = createStitches({
      theme: {
        colors: {
          blue: 'royalblue',
        },
      },
    });
    
    const Button = styled('button', {
      backgroundColor: '$blue',
    });
    ```
    

### Locally scoped tokens

- `$$`을 정의해 로컬 범위의 토큰을 만들어낼 수 있다.
    
    ```tsx
    const Button = styled('button', {
      $$shadow: 'blueviolet',
      boxShadow: '0 0 0 3px $$shadow',
    
      '&:hover': {
        $$shadow: 'royalblue',
      },
    });
    ```
    

### Theme specific styles

- 동적으로 생성된 theme 클래스에 스타일을 추가할 수 있다.
- theme 객체는 클래스 네임을 반환하기 때문에 셀렉터에 `.`을 추가해줘야 한다.

```tsx
import { createStitches } from '@stitches/react';

const { styled, createTheme } = createStitches({
  theme: {},
});

const myTheme = createTheme({});

const Button = styled('button', {
  borderRadius: '9999px',
  fontSize: '13px',
  border: '0',

  [`.${myTheme} &`]: {
    backgroundColor: '$blue',
  },
});

() => (
  <div className={myTheme}>
    <Button>Button</Button>
  </div>
);
```

### Numeric values

- 스티치는 CSS 속성이 단위 없는 값을 허용하지 않는 경우 px로 변환한다.

## Variants

### Adding variants

- variants key를 통해 배리언트 추가 가능
    
    ```tsx
    const Button = styled('button', {
      // base styles
    
      variants: {
        color: {
          violet: {
            backgroundColor: 'blueviolet',
            color: 'white',
            '&:hover': {
              backgroundColor: 'darkviolet',
            },
          },
          gray: {
            backgroundColor: 'gainsboro',
            '&:hover': {
              backgroundColor: 'lightgray',
            },
          },
        },
      },
    });
    
    () => <Button color="violet">Button</Button>;
    ```
    

### Multiple variants

```tsx
const Button = styled('button', {
  // base styles

  variants: {
    color: {
      violet: { ...violetStyles },
      gray: { ...grayStyles },
    },
    size: {
      small: {
        fontSize: '13px',
        height: '25px',
        paddingRight: '10px',
        paddingLeft: '10px',
      },
      large: {
        fontSize: '15px',
        height: '35px',
        paddingLeft: '15px',
        paddingRight: '15px',
      },
    },
  },
});

() => (
  <Button color="violet" size="large">
    Button
  </Button>
);
```

### Boolean variants

- true 키를 사용해 booleans 속성이 될 수 있다.

```tsx
const Button = styled('button', {
  // base styles

  variants: {
    outlined: {
      true: {
        borderColor: 'lightgray',
      },
    },
  },
});

() => <Button outlined>Button</Button>;
```

### Compound variants

- 다른 배리언트의 조합을 기반으로 스타일을 설정해야 하는 경우, 컴파운트 배리언트 기능을 사용할 수 있다.
    
    ```tsx
    const Button = styled('button', {
      ...styles,
    
      variants: {
        color: {
          violet: { ...violetStyles },
          gray: { ...grayStyles },
        },
        outlined: {
          true: { ...outlineVariants },
        },
      },
    
      compoundVariants: [
        {
          color: 'violet',
          outlined: true,
          css: {
            color: 'blueviolet',
            borderColor: 'darkviolet',
            '&:hover': {
              color: 'white',
            },
          },
        },
        {
          color: 'gray',
          outlined: true,
          css: {
            color: 'gray',
            borderColor: 'lightgray',
            '&:hover': {
              color: 'black',
            },
          },
        },
      ],
    });
    
    () => (
      <Button color="violet" outlined>
        Button
      </Button>
    );
    ```
    

### Default variants

```tsx
const Button = styled('button', {
  ...styles

  variants: {
    color: {
      violet: { ...violetStyles },
      gray: { ...grayStyles }
    },
  },

  defaultVariants: {
    color: 'violet'
  }
});

() => <Button>Button</Button>
```

### Responsive variants

```tsx
const Button = styled('button', {
  // base styles

  variants: {
    color: {
      violet: { ...violetStyles },
      gray: { ...grayStyles },
    },
  },
});

() => (
  <Button
    color={{
      '@initial': 'gray',
      '@bp1': 'violet',
    }}
  >
    Button
  </Button>
);
```

## Responsive Styles

### Setting an initial variant

- 브레이크 포인트를 적용하기 전에 @initial 브레이크 포인트를 사용해야 한다.

```tsx
const Button = styled('button', {
  // base styles

  variants: {
    color: {
      violet: {},
      gray: {},
    },
  },
});

() => (
  <Button
    color={{
      '@initial': 'gray',
      '@bp2': 'violet'
    }}
  >
    Button
  </Button>
);
```

### Using bps in the style objects

- 대부분의 경우 반응형 프레이크 포인트를 인라인으로 적용하지 않는 것이 좋다.

```tsx
const Button = styled('button', {
  // base styles

  '@bp1': {
    // Styles for bp1
  },
});
```

## Overriding Styles

- 소비층에서 스타일을 오버라이딩 하는 방법
- 스티치는 `css` 프롭을 통해 스타일 오버라이딩을 제공한다.
- style attribute와 비슷하지만 토큰을 지원한다.

### The css prop

```tsx
() => (
  <Button
    css={{
      borderRadius: '0',
      '&:hover': {
        backgroundColor: 'black',
        color: 'white',
      },
    }}
  >
    Button
  </Button>
);
```

```tsx
() => (
  <Button
    css={{
      borderRadius: '$1',
      '&:hover': {
        backgroundColor: '$blue9',
      },
    }}
  >
    Button
  </Button>
);
```

### Merging css prop on custom components

```tsx
const PageTitle = styled('h1', {});

function BlogTitle({ css, ...props }) {
  return (
    <PageTitle
      css={{
        ...css,
        '@bp1': {
          ...css['@bp1'],
          lineHeight: 1.2,
        },
        '@bp2': {
          ...css['@bp2'],
          lineHeight: 1.4,
        },
      }}
      {...props}
    />
  );
}

() => (
  <BlogTitle
    css={{
      marginBottom: 10,
      '@bp1': { marginBottom: 20 },
      '@bp2': { marginBottom: 30 },
    }}
  >
    Blog title
  </BlogTitle>
);
```

### Overriding the HTML tag

- as props

```tsx
const Button = styled('button', {});

() => (
  <Button as="a" href="#">
    Button
  </Button>
);
```

## Composing Components

- 스티치 컴포넌트는 styled 함수를 통해 구성될 수 있다.
    
    ```tsx
    const BaseButton = styled('button', {});
    
    const CheckoutButton = styled(BaseButton, {
      borderRadius: 0,
      backgroundColor: 'hotpink',
      color: 'white',
      '&:hover': {
        backgroundColor: 'deeppink',
      },
    });
    
    () => (
      <>
        <BaseButton>Base button</BaseButton>
        <CheckoutButton>Checkout button</CheckoutButton>
      </>
    );
    ```
    
- 배리언트를 가진 컴포넌트를 구성하면 상속될 수 있다.
    
    ```tsx
    const BaseButton = styled('button', {
      // base styles
    
      variants: {
        size: {
          small: {},
          large: {},
        },
      },
    });
    
    const CheckoutButton = styled(BaseButton, {
      // checkout styles
    });
    
    () => (
      <>
        <BaseButton size="small">Base button</BaseButton>
        <CheckoutButton size="small">Checkout button</CheckoutButton>
        <BaseButton size="large">Base button</BaseButton>
        <CheckoutButton size="large">Checkout button</CheckoutButton>
      </>
    );
    ```