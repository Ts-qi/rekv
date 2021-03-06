# Rekv

Rekv 是一个为 React 函数式组件设计的全局状态管理器

[![Travis CI][ci-image]][ci-url]
[![Coveralls][coverage-image]][coverage-url]
[![NPM version][npm-image]][npm-url]
[![Downloads][downloads-image]][downloads-url]

### 1. 特色

- 高性能，使用 Key-Value 而不是树型结构来处理状态
- 无 Redux，无依赖，仅 state
- TypeScript 友好

### 2. 使用要求

React 版本 >= 16.8.0

### 3. 安装

> yarn add rekv

### 4. 示例

**准备工作：为全局状态设置一个初始值**

> 建议在入口文件，如 create-react-app 生成的 App.tsx 中，设置 rekv 的初始状态。

```ts
import { rekv } from 'rekv';

// setInitState
// 用于对状态进行初始化，未初始化时，所有的 key 取值都是 undefined
// 如果 key 已经存在，setInitState 将会忽略掉这个值
rekv.setInitState({
  name: 'demo',
  count: 0,
});
```

#### 4.1 在函数式组件中使用

**使用全局状态**

```tsx
import React from 'react';
import { rekv } from 'rekv';

export default function Demo() {
  const name = rekv.useState('name');
  const count = rekv.useState('count');

  return (
    <div>
      {name}, {count}
    </div>
  );
}
```

**在另一个组件内更新状态**

```tsx
import React from 'react';
import { rekv } from 'rekv';

// 重置计数器
function reset() {
  rekv.setState({ count: 0 });
}

function increment() {
  rekv.setState(state => ({ count: state.count + 1 }));
}

function decrement() {
  rekv.setState(state => ({ count: state.count - 1 }));
}

export default function Button() {
  return (
    <div>
      <button onClick={reset}>reset</button>
      <button onClick={increment}>+1</button>
      <button onClick={decrement}>-1</button>
    </div>
  );
}
```

#### 4.2 创建一个 Rekv 实例，并进行 TypeScript 类型检查

**注意：这里使用的是大写的 Rekv，小写的 rekv 是 Rekv 的一个实例**

```tsx
// store.ts
import { Rekv } from 'rekv';

interface InitState {
  name: string;
  age?: number;
}

export const store = new Rekv<InitState>({
  initState: {
    name: 'Jack',
    age: 25,
  },
});
```

```tsx
// User.ts
import React from 'react';
import { store } from './store';

export default function User() {
  const name = store.useState('name'); // name 将被推断为 string 类型
  const age = store.useState('age'); // age 将被推断为 number | undefined

  return (
    <div>
      {name}, {age}
    </div>
  );
}
```

#### 4.3 在 React 类组件中使用

**方式 1：通过订阅数据变化，并更新当前组件的状态**

```tsx
import React from 'react';
import { rekv } from 'rekv';

export default class MyComponent extends React.Component {
  state = {
    nativeCount: 0,
  };

  componentDidMount() {
    // 订阅 key 为 count 的状态，并使用 this.setState 更新当前组件的 count 状态
    rekv.bindClassComponent(this, { count: 'nativeCount' });
  }

  componentWillUnmount() {
    // 组件退出，取消当前组件的订阅
    rekv.unbindClassComponent(this);
  }

  render() {
    return <div>{this.state.nativeCount}</div>;
  }
}
```

**方式 2：通过函数式组件包裹类组件**

```tsx
import React from 'react';
import { rekv } from 'rekv';

function Demo() {
  const count = rekv.useState('count');
  return <MyComponent count={count} />;
}
```

#### 4.4 获取当前时刻的状态

```tsx
import { rekv } from 'rekv';

// 获取当前时刻的状态，如果需要订阅数据变化，可以参阅 useState
const currentState = rekv.getCurrentState();
```

[coverage-image]: https://img.shields.io/coveralls/flarestart/rekv.svg
[coverage-url]: https://coveralls.io/github/flarestart/rekv
[ci-image]: https://img.shields.io/travis/flarestart/rekv.svg?branch=master
[ci-url]: https://travis-ci.org/flarestart/rekv
[npm-image]: https://img.shields.io/npm/v/rekv.svg
[npm-url]: https://npmjs.org/package/rekv
[downloads-image]: http://img.shields.io/npm/dm/rekv.svg
[downloads-url]: https://npmjs.org/package/rekv
