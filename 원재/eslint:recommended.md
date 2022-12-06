> 누구나 설정된대로 따르기만 하던 eslint를 좀 더 효율적으로 관리하는 방법에 대해 공부하고자 자주 사용하는 extends가 어떤 옵션을 갖고 있는지 정리한 문서입니다.
> 
> 원문 링크: https://github.com/jsx-eslint/eslint-plugin-react/blob/master/configs/recommended.js

이번 단에서는 eslint:recommended만 다루려고 합니다.<br>
eslint:recommended에 eslint:all이 포함되어 있지만 all의 내용은 너무 방대하기 때문입니다.

```js
// eslint:recommended
'use strict';

// 기본적인 모든 rules
const all = require('./all');

// 0: off
// 1: warn
// 2: error
module.exports = Object.assign({}, all, {
  rules: {
    'react/display-name': 2,
    'react/jsx-key': 2,
    'react/jsx-no-comment-textnodes': 2,
    'react/jsx-no-duplicate-props': 2,
    'react/jsx-no-target-blank': 2,
    'react/jsx-no-undef': 2,
    'react/jsx-uses-react': 2,
    'react/jsx-uses-vars': 2,
    'react/no-children-prop': 2,
    'react/no-danger-with-children': 2,
    'react/no-deprecated': 2,
    'react/no-direct-mutation-state': 2,
    'react/no-find-dom-node': 2,
    'react/no-is-mounted': 2,
    'react/no-render-return-value': 2,
    'react/no-string-refs': 2,
    'react/no-unescaped-entities': 2,
    'react/no-unknown-property': 2,
    'react/no-unsafe': 0,
    'react/prop-types': 2,
    'react/react-in-jsx-scope': 2,
    'react/require-render-return': 2,
  },
});
```

## `react/display-name`

> displayName을 사용하면 에러

### Rule Option (default)

```js
...
"react/display-name": [ "error", {
    // false일 경우 displayName을 요구하지 않음
    "ignoreTranspilerName": false
    }
]
...
```

### correct case

```js
// class component
export default class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.name}</div>;
  }
}

// function component
export default function Hello({ name }) {
  return <div>Hello {name}</div>;
}
```

### incorrect case

```js
// class component
export default class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.name}</div>;
  }
}
Hello.displayName = 'Hello';

// function component
export default function Hello({ name }) {
  return <div>Hello {name}</div>;
}
Hello.displayName = 'Hello';
```

## `react/jsx-key`

> key prop에 관련된 에러

### Rule Option (default)

```js
...
"react/jsx-key": [ "error", { 
    // false일 경우 FragmentShorthand(<></>)를 사용해도 됨
    "checkFragmentShorthand": false,
    // false일 경우 key는 spread한 뒤에 작성할 수 있음
    // <span {...spread} key="any-key" />
    "checkKeyMustBeforeSpread": false,
    // false일 경우 중복된 키를 사용해도 됨
    // 이 부분은 왜 false가 기본값인지 모르겠음
    // true이면 더 좋을듯?
    "warnOnDuplicates": false,
    }
]
...
```

### correct case

```js
[<Hello key="first" />, <Hello key="second" />, <Hello key="third" />];

data.map((x) => <Hello key={x.id}>{x}</Hello>);

<Hello key={id} {...{ id, caption }} />
```

### incorrect case

```js
[<Hello />, <Hello />, <Hello />];

data.map(x => <Hello>{x}</Hello>);

<Hello {...{ key: id, id, caption }} />
```

## `react/jsx-no-comment-textnodes`

> jsx 문법에서 주석을 사용하는 방법에 대한 rule

### Rule Option (default)

```js
...
none
...
```

### correct case

```js
var Hello = createReactClass({
  displayName: 'Hello',
  render: function() {
    return <div>{/* empty div */}</div>;
  }
});

var Hello = createReactClass({
  displayName: 'Hello',
  render: function() {
    return <div /* empty div */></div>;
  }
});

var Hello = createReactClass({
  displayName: 'Hello',
  render: function() {
    return <div className={'foo' /* temp class */}></div>;
  }
});
```

### incorrect case

```js
var Hello = createReactClass({
  render: function() {
    return (
      <div>// empty div</div>
    );
  }
});

var Hello = createReactClass({
  render: function() {
    return (
      <div>
        /* empty div */
      </div>
    );
  }
});
```

## `react/jsx-no-duplicate-props`

> 중복된 prop을 사용하면 에러

### Rule Option (default)

```js
...
none
...
```

### correct case

```js
<Hello firstname="John" lastname="Doe" />;
```

### incorrect case

```js
<Hello name="John" name="John" />;
```

## `react/jsx-no-target-blank`

> 링크와 관련된 에러

### Rule Option (default)

```js
...
"react/jsx-no-target-blank": ["error", {
    // false일 경우 noreferrer를 필수로 사용해야 됨
    "allowReferrer": false,
    // always일 경우 동적으로 href를 바인딩하면 안됨
    // never일 경우 동적으로 href를 바인딩해도 됨
    "enforceDynamicLinks": "always",
    // false일 경우 props에 모든 spread 연산자를 사용해도 됨
    "warnOnSpreadAttributes": false,
    // true일 때 아래와 같이 사용하면 에러
    // <a target='_blank' href="https://example.com/"></a>
    "links": true,
    // true일 때 아래와 같이 사용하면 에러
    // <form target="_blank" action="https://example.com/"></form>
    "forms": false,
}]
...
```

### correct case

```js
// 너무 복잡해서 pass
```

### incorrect case

```js
// 너무 복잡해서 pass
```

## `react/jsx-no-undef`

> 정의되지 않은 Component를 사용하려하면 에러

### Rule Option (default)

```js
...
"react/jsx-no-undef": ["error", { "allowGlobals": true }]
...
```

### correct case

```js
var Hello = require('./Hello');

<Hello name="John" />;
```

### incorrect case

```js
// will ignore Text in the global scope and warn
var Hello = React.createClass({
  render: function() {
    return <Text>Hello</Text>;
  }
});
module.exports = Hello;
```

## `react/jsx-uses-react`

### Rule Option (default)

> 반드시 react를 import 하고 사용해야함

```js
...
none
...
```

### correct case

```js
var React = require('react');

var Hello = <div>Hello {this.props.name}</div>;```

### incorrect case

```js
var React = require('react');

// nothing to do with React
```

## `react/jsx-uses-vars`

### Rule Option (default)

> 선언된 변수는 무조건 사용해야 함

```js
...
none
...
```

### correct case

```js
var Hello = require('./Hello');

<Hello name="John" />;
```

### incorrect case

```js
var Hello = require('./Hello');
```

## `react/no-children-prop`

> prop으로 children에 바인딩하지 말고 jsx 문법에 맞게 사용해야 함

### Rule Option (default)

```js
...
"react/no-children-prop": ["error", {
  // 함수는 children으로 허용하지 않음
  "allowFunctions": false
}]
...
```

### correct case

```js
<div>Children</div>

<MyComponent>Children</MyComponent>

<MyComponent>
  <span>Child 1</span>
  <span>Child 2</span>
</MyComponent>

React.createElement("div", {}, 'Children')
React.createElement("div", 'Child 1', 'Child 2')
```

### incorrect case

```js
<div children='Children' />

<MyComponent children={<AnotherComponent />} />
<MyComponent children={['Child 1', 'Child 2']} />

React.createElement("div", { children: 'Children' })
```

## `react/no-danger-with-children`

> dangerouslySetInnerHTML prop과 children을 같이 사용하면 안됨

### Rule Option (default)

```js
...
none
...
```

### correct case

```js
<div dangerouslySetInnerHTML={{ __html: "HTML" }} />
```

### incorrect case

```js
<div dangerouslySetInnerHTML={{ __html: "HTML" }}>
  Children
</div>
```

## `react/no-deprecated`

> deprecated된 메서드를 사용하면 안됨

### Rule Option (default)

```js
...
none
...
```

### correct case

```js
ReactDOM.render(<MyComponent />, root);

// When [1, {"react": "0.13.0"}]
ReactDOM.findDOMNode(this.refs.foo);

import { PropTypes } from 'prop-types';

UNSAFE_componentWillMount() { }
UNSAFE_componentWillReceiveProps() { }
UNSAFE_componentWillUpdate() { }
```

### incorrect case

```js
React.render(<MyComponent />, root);

React.unmountComponentAtNode(root);

React.findDOMNode(this.refs.foo);

React.renderToString(<MyComponent />);

React.renderToStaticMarkup(<MyComponent />);

React.createClass({ /* Class object */ });

const propTypes = {
  foo: PropTypes.bar,
};

//Any factories under React.DOM
React.DOM.div();

import React, { PropTypes } from 'react';

// old lifecycles (since React 16.9)
componentWillMount() { }
componentWillReceiveProps() { }
componentWillUpdate() { }
```

## `react/no-direct-mutation-state`

> 싱태를 변경할때는 반드시 setState를 사용해야함

### Rule Option (default)

```js
...
none
...
```

### correct case

```js
var Hello = createReactClass({
  componentDidMount: function() {
    this.setState({
      name: this.props.name.toUpperCase();
    });
  },
  render: function() {
    return <div>Hello {this.state.name}</div>;
  }
});

class Hello extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      foo: 'bar',
    }
  }
}
```

### incorrect case

```js
var Hello = createReactClass({
  componentDidMount: function() {
    this.state.name = this.props.name.toUpperCase();
  },
  render: function() {
    return <div>Hello {this.state.name}</div>;
  }
});

class Hello extends React.Component {
  constructor(props) {
    super(props)

    // Assign at instance creation time, not on a callback
    doSomethingAsync(() => {
      this.state = 'bad';
    });
  }
}
```

## `react/no-find-dom-node`

> findDOMNode는 앞으로 react에서 개선될 메서드이기 때문에 사용하면 안됨

### Rule Option (default)

```js
...
none
...
```

### correct case

```js
class MyComponent extends Component {
  componentDidMount() {
    this.node.scrollIntoView();
  }
  render() {
    return <div ref={node => this.node = node} />
  }
}
```

### incorrect case

```js
class MyComponent extends Component {
  componentDidMount() {
    findDOMNode(this).scrollIntoView();
  }
  render() {
    return <div />
  }
}
```

## `react/no-is-mounted`

> isMounted 메서드는 안티패턴이며 앞으로 deprecated 될 메서드 이기 때문에 사용하면 안됨

### Rule Option (default)

```js
...
none
...
```

### correct case

```js
var Hello = createReactClass({
  render: function() {
    return <div onClick={this.props.handleClick}>Hello</div>;
  }
});
```

### incorrect case

```js
var Hello = createReactClass({
  handleClick: function() {
    setTimeout(function() {
      if (this.isMounted()) {
        return;
      }
    });
  },
  render: function() {
    return <div onClick={this.handleClick.bind(this)}>Hello</div>;
  }
});
```

## `react/no-render-return-value`

> render 된 value로 부가적인 행동을 하려하지 말라
> 앞으로 react에서는 간혹 render의 return값이 비동기적으로 수행될 경우가 있기 때문에 추후 정상동작하지 않을 수 있으므로!

### Rule Option (default)

```js
...
none
...
```

### correct case

```js
ReactDOM.render(<App ref={doSomethingWithInst} />, document.body);

ReactDOM.render(<App />, document.body, doSomethingWithInst);
```

### incorrect case

```js
const inst = ReactDOM.render(<App />, document.body);
doSomethingWithInst(inst);

```

## `react/no-string-refs`

> ref prop에 string을 사용하지 말라

### Rule Option (default)

```js
...
"react/no-string-refs": ["error", {"noTemplateLiterals": ?}]
...
```

### correct case

```js
var Hello = createReactClass({
  componentDidMount: function() {
    var component = this.hello;
    // ...do something with component
  },
  render() {
    return <div ref={(c) => { this.hello = c; }}>Hello, world.</div>;
  }
});
```

### incorrect case

```js
var Hello = createReactClass({
 render: function() {
  return <div ref="hello">Hello, world.</div>;
 }
});

var Hello = createReactClass({
  componentDidMount: function() {
    var component = this.refs.hello;
    // ...do something with component
  },
  render: function() {
    return <div ref="hello">Hello, world.</div>;
  }
});
```

## `react/no-unescaped-entities`

> `>`, `"`, `\`, `}`를 문자로 사용하려할때 반드시 escape해라

### Rule Option (default)

```js
...
// forbid: ['>', '"', '\', '}']
"react/no-unescaped-entities": ["error", { "forbid": Array<string> }]
...
```

### correct case

```js
<div> &gt; </div>
<div> {'>'} </div>
```

### incorrect case

```js
<div> > </div>
```

## `react/no-unknown-property`

> 알려지지 않은 prop을 사용하지 마라

### Rule Option (default)

```js
...
"react/no-unknown-property": ["error", { ignore: <string> }]
...
```

### correct case

```js
var React = require('react');

var Hello = <div className="hello">Hello World</div>;
var Button = <button disabled>Cannot click me</button>;
var Img = <img src={catImage} alt="A cat sleeping on a keyboard" />;

// aria-* attributes
var IconButton = <button aria-label="Close" onClick={this.close}>{closeIcon}</button>;

// data-* attributes
var Data = <div data-index={12}>Some data</div>;

// React components are ignored
var MyComponent = <App class="foo-bar"/>;
var AnotherComponent = <Foo.bar for="bar" />;

// Custom web components are ignored
var MyElem = <div class="foo" is="my-elem"></div>;
var AtomPanel = <atom-panel class="foo"></atom-panel>;
```

### incorrect case

```js
var React = require('react');

var Hello = <div class="hello">Hello World</div>;
var Alphabet = <div abc="something">Alphabet</div>;

// Invalid aria-* attribute
var IconButton = <div aria-foo="bar" />;
```

## `react/no-unsafe`

> UNSAFE 메서드를 사용하지 말라

### Rule Option (default)

```js
...
"react/no-unsafe": ["off", { "checkAliases": false }]
...
```

### correct case

```js
class Foo extends Bar {
  UNSAFE_componentWillMount() {}
  UNSAFE_componentWillReceiveProps() {}
  UNSAFE_componentWillUpdate() {}
}
```

### incorrect case

```js
class Foo extends React.Component {
  UNSAFE_componentWillMount() {}
  UNSAFE_componentWillReceiveProps() {}
  UNSAFE_componentWillUpdate() {}
}
```

## `react/prop-types`

> propTypes에 대한 설정

### Rule Option (default)

```js
...
"react/prop-types": ["error", {
  // 무시할 prop으로 구성된 optional array
  ignore: <ignore>,
  // propTypes의 유효성 검증에 사용되는 optional array
  customValidators: <customValidator>,
  // propTypes 블록이 선언된 구성 요소에 대해서만 오류가 발생하는 boolean값
  skipUndeclared: <skipUndeclared>
  }
]
...
```

### correct case

```js
function Hello({ name }) {
  return <div>Hello {name}</div>;
}
Hello.propTypes = {
  name: PropTypes.string.isRequired
}

var Hello = createReactClass({
  propTypes: {
    name: PropTypes.string.isRequired,
  },
  render: function() {
    return <div>Hello {this.props.name}</div>;
  },
});

// Or in ES6:
class HelloEs6 extends React.Component {
  render() {
    return <div>Hello {this.props.name}</div>;
  }
}
HelloEs6.propTypes = {
  name: PropTypes.string.isRequired,
};

// ES6 + Public Class Fields (draft: https://tc39.github.io/proposal-class-public-fields/)
class HelloEs6WithPublicClassField extends React.Component {
  static propTypes = {
    name: PropTypes.string.isRequired,
  }
  render() {
    return <div>Hello {this.props.name}</div>;
  }
}
function Hello() {
  return <div>Hello World</div>;
}

// Referencing an external object disable the rule for the component
function Hello({ name }) {
  return <div>Hello {name}</div>;
}
Hello.propTypes = myPropTypes;
```

### incorrect case

```js
...
```

## `react/react-in-jsx-scope`

> jsx 문법을 사용하지 위해 creatElement메서드를 대체할 값을 올바르게 선언해둬야 함

### Rule Option (default)

```js
...

...
```

### correct case

```js
import React from 'react';

var Hello = <div>Hello {this.props.name}</div>;

var React = require('react');

var Hello = <div>Hello {this.props.name}</div>;

/** @jsx Foo.bar */
var Foo = require('foo');

var Hello = <div>Hello {this.props.name}</div>;
```

### incorrect case

```js
var Hello = <div>Hello {this.props.name}</div>;

/** @jsx Foo.bar */
var React = require('react');

var Hello = <div>Hello {this.props.name}</div>;
```

## `react/require-render-return`

> render 메서드에 반드시 return 값이 있어야 함

### Rule Option (default)

```js
...
none
...
```

### correct case

```js
var Hello = createReactClass({
  render() {
    return <div>Hello</div>;
  }
});

class Hello extends React.Component {
  render() {
    return <div>Hello</div>;
  }
}
```

### incorrect case

```js
var Hello = createReactClass({
  render() {
    <div>Hello</div>;
  }
});

class Hello extends React.Component {
  render() {
    <div>Hello</div>;
  }
}
```