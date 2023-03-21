> 참조 문서
> [useMemo 공식문서](https://react.dev/reference/react/useMemo)

# useMemo란?

> 리액트가 제공하는 내장 훅 중 하나로서 종속성 배열을 확인해 값을 캐싱할 수 있는 기능을 갖고 있다.
> 동작 방식 자체는 단순하다.

1. 종속성 배열 비교 작업 수행
2-1. 종속성 배열 값 중 이전 값과 다른 값이 있다면 함수를 실행해 새로운 값 반환
2-2. 종속성 배열 값이 이전 값과 모두 일치한다면 캐싱된 값 반환

# 리액트가 제안하는 용법

> [공식문서](https://react.dev/reference/react/useMemo)를 참조함

1. 값을 도출할 때 리소스 비용이 큰 경우
2. 리렌더링을 건너뛰고 싶은 경우
3. 다른 hook의 종속성 메모이제이션
4. 함수 메모이제이션

## 값을 도출할 때 리소스 비용이 큰 경우

> 일반적으로 수천 개의 개체를 만들거나 반복하지 않는 한 비용이 많이 들지 않는다.
> 소요되는 시간에 대한 확신을 얻고 싶다면 `console.time`과 `console.timeEnd`를 활용해서 측정해봐라

```js
console.time('filter array');
const visibleTodos = filterTodos(todos, tab);
console.timeEnd('filter array');
```

### 주의해야할 점

- `useMemo`는 첫 번째 렌더링을 더 빠르게 만들지는 않으며, 불필요한 재계산 작업을 건너뛰는데 도움이 될 뿐이다.
- 항상 새로운 값이 도출된다면 `useMemo`를 사용하지 않는 것이 좋다.

### 메모이제이션을 쓸모 없게 만드는 요소들

- `children`에 `JSX`를 전달받으면 `react`는 이미 해당 `children`을 리렌더링 할 필요가 없다는 것을 알고 있다.
- 가능한 지역 상태를 선호하고 상태 끌어올리기를 지양해라
- 렌더링 로직을 순수하게 유지해라
- 불필요한 상태 업데이트를 지양해라
- 불필요한 의존성 요소를 배제해라

## 리렌더링을 건너뛰고 싶을 경우

> `react`에서는 기본적으로 컴포넌트가 리렌더링 될 때 모든 자식 컴포넌트를 재귀적으로 리렌더링한다.

만약 아래와 같이 `memo`되어 있는 컴포넌트가 있다고 쳤을 때 전달받은 props의 참조값이나 원시값이 변하지 않았다면 자식 컴포넌트를 재귀적으로 리렌더링하지 않을 것이다.

```jsx
import { memo } from 'react';

const List = memo(function List({ items }) {
  // ...
});
```

### 1. `useMemo`를 사용하지 않았을 경우

> visibleTodos의 참조값은 항상 바뀌기때문에 List 컴포넌트는 항상 다른 값이 넘어온다고 판단해 리렌더링을 수행한다.

```jsx
function TodoList({ todos, tab, theme }) {
  const visibleTodos = filterTodos(todos, tab);

  return (
    <div className={theme}>
      <List items={visibleTodos} />
    </div>
  );
}
```

### 2. `useMemo`를 사용하는 경우

> 의존 배열이 이전과 동일하다면 이전과 동일한 참조값을 가진 배열을 반환하기 때문에 memo한 List 컴포넌트에서는값이 바뀌지 않았다고 판단해 리렌더링하지 않는다.

```jsx
export default function TodoList({ todos, tab, theme }) {
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );

  return (
    <div className={theme}>
      <List items={visibleTodos} />
    </div>
  );
}
```

### 주의해야 할 점

- 만약 전역상태를 사용하고 있다면 위험할 수 있다.
  - `이 부분은 공식 문서의 입장이 아닌 개인적인 의견이기 때문에 참고만 했으면 한다. -> 하지만 이전에 공식문서에서 지역상태를 선호하라고 한 것에 대해 이 이유도 포함되어 있지 않을까 싶다.`
  - 예를 들어 memo한 컴포넌트 내부에서 전역 상태를 사용하고 있는데 memo한 컴포넌트에서 전달받은 prop이 변하지 않았다면 재귀적으로 리렌더링하지 않기 때문에 업데이트되어야할 전역상태가 업데이트되지 않을 수 있지 않을까 싶다.
  - `이 부분은 추후 동작을 확인한 후 수정하도록 하겠다.
  
### 추가 내용

> 아래 두 코드는 동작이 동일하다.

```jsx
const List = memo(function List({ items }) {
  // ...
});

function TodoList({ todos, tab, theme }) {
  const visibleTodos = filterTodos(todos, tab);

  return (
    <div className={theme}>
      <List items={visibleTodos} />
    </div>
  );
}
```

```jsx
export default function TodoList({ todos, tab, theme }) {
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  const children = useMemo(() => <List items={visibleTodos} />, [visibleTodos]);
  return (
    <div className={theme}>
      {children}
    </div>
  );
}
```

## 다른 hook의 종속성 메모이제이션

> 이전에 언급했듯 `useMemo`는 종속성 배열이 같다면 같은 참조값을 가진 값을 반환한다.
> 이를 활용하여 다른 종속성 배열에 참조값(배열, 객체)을 사용할 때 useMemo를 사용하면 유용할 수 있다.

> 개인적으로는 이 방안을 활용하면 상태를 보다 순수하게 관리할 수 있다고 생각한다.
> 대신 코드가 좀 더러워질 것이다...

```jsx
function Dropdown({ allItems, text }) {
  
  // 이 값은 text가 이전과 동일할 경우 같은 참조값을 반환한다.
  const searchOptions = useMemo(() => {
    return { matchMode: 'whole-word', text };
  }, [text]);

  // 때문에 여기서 사용되는 searchOptions는 text값이 변하지 않았다면 이전과 동일한 참조값을 갖고 있기 때문에 이전과 같은 값이라고 판단한다.
  const visibleItems = useMemo(() => {
    return searchItems(allItems, searchOptions);
  }, [allItems, searchOptions]); // ✅ Only changes when allItems or searchOptions changes
  // ...
```

## 함수 메모이제이션

> 함수는 일반 값에 비해 덩치가 크다.
> 이는 함수 역시 일급 객체이기 때문에 내장하고 있는 것들이 많고 함수 몸체가 크면 클 수록 덩치가 커지기 때문
> 이 때문에 함수는 메모이제이션 해두면 메모리상 이점을 많이 얻을 수 있다.

이 부분은 따로 정리하지 않아도 `useCallback`이라는 내장 훅이 존재할만큼 중요하게 여겨지는 부분이기 때문에 사족은 달지 않겠다.
