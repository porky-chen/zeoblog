---
title: React Hooks
author: zeo
date: 2024-8-20
categories: [知识点, React]
tags: [React, Hooks]
render_with_liquid: false
---
**React Hooks** 是 React 16.8 版本引入的新特性，旨在让函数组件拥有类似类组件的状态和生命周期的能力。它简化了组件逻辑、提高了代码的复用性，并让开发者可以在函数式组件中使用 React 的各种功能。

---

## 1. 什么是 Hooks？

**Hooks** 是一类特殊的函数，允许你在函数组件中使用 React 的特性，例如状态管理、生命周期、上下文等。传统上，状态和生命周期功能只能在类组件中使用，而 Hooks 让你在函数组件中也能轻松实现这些功能。

### 为什么使用 Hooks？

1. **简化代码**：与类组件相比，Hooks 使代码更简洁，减少了 `this` 的使用，不再需要管理复杂的类组件结构。
2. **逻辑复用**：Hooks 允许你将状态逻辑提取成可复用的函数（自定义 Hooks），方便跨组件复用。
3. **更好的组织性**：通过 Hooks，可以让一个组件的状态、生命周期函数等逻辑更加集中，避免了类组件中“生命周期方法分散”的问题。

---

## 2. 常见 React Hooks 解析

React 提供了多个内置的 Hooks，以下是最常用的一些。

### 2.1 `useState`

`useState` 是 React 提供的用于状态管理的 Hook，它可以让你在函数组件中添加局部状态。

**语法：**
```javascript
const [state, setState] = useState(initialState);
```

- `state`: 当前的状态值。
- `setState`: 更新状态的函数。
- `initialState`: 状态的初始值。

**示例：**
```javascript
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>当前计数值：{count}</p>
      <button onClick={() => setCount(count + 1)}>增加</button>
    </div>
  );
}
```
在这个例子中，我们用 `useState` 创建了一个名为 `count` 的状态变量，并使用 `setCount` 更新它。

### 使用场景

- 用于跟踪组件内部状态，如表单输入、开关状态、计数器等。
- 简单的数据管理，适用于小型应用或组件。

---
### 2.2 `useEffect`

`useEffect` 用来在函数组件中处理副作用，它相当于类组件中的生命周期方法 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 的组合。常见的副作用操作包括数据请求、手动 DOM 操作、订阅或清除操作等。

**语法：**
```javascript
useEffect(() => {
  // 副作用逻辑
  return () => {
    // 可选的清理函数
  };
}, [依赖项]);
```

- `[]`: 依赖项数组，用来控制副作用的执行。如果依赖项发生变化，`useEffect` 就会重新执行。

**示例：**
```javascript
import React, { useState, useEffect } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('https://api.example.com/data')
      .then(response => response.json())
      .then(data => setData(data));
  }, []);  // 空数组意味着只在组件挂载时运行一次

  return (
    <div>
      <h1>数据加载：{data ? data.title : '加载中...'}</h1>
    </div>
  );
}
```
### 使用场景

- 数据获取、事件监听、定时器等副作用。
- 清理资源（如取消订阅）以避免内存泄漏。

---

### 2.3 `useContext`

`useContext` 用于在组件之间共享状态，而无需通过 `props` 逐层传递。它是 React 的上下文 API 的简化使用方法，能够快速访问全局状态。

**语法：**
```javascript
const value = useContext(MyContext);
```

**示例：**
```javascript
import React, { createContext, useContext } from 'react';

const ThemeContext = createContext('light');

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={theme}>主题按钮</button>;
}

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedButton />
    </ThemeContext.Provider>
  );
}
```
在这个例子中，我们通过 `useContext` 获取 `ThemeContext` 中的值，并根据该值调整按钮的样式。
### 使用场景

- 在组件树中传递全局状态，如主题、用户信息等。
- 使得深层组件能够轻松访问共享状态而无需逐层传递 props。

---

### 2.4 `useReducer`

`useReducer` 是一种替代 `useState` 的方式，适用于管理复杂状态逻辑。它和 Redux 的 reducer 概念相似，可以在状态变更时通过分派 action 来控制状态更新。

**语法：**
```javascript
const [state, dispatch] = useReducer(reducer, initialState);
```

- `reducer`: 一个接收当前状态和 action 并返回新状态的函数。
- `dispatch`: 用于触发状态更新的函数。
- `initialState`: 初始状态。

**示例：**
```javascript
import React, { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>计数：{state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>增加</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>减少</button>
    </div>
  );
}
```
在此例中，我们使用 `useReducer` 管理计数器的状态，并通过 `dispatch` 函数分派 `increment` 和 `decrement` 操作。

### 2.5 `useMemo`

`useMemo` 用于缓存计算结果，以避免不必要的计算。在每次渲染时，如果依赖项没有发生变化，`useMemo` 将返回上次计算的结果。

**语法：**
```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

**示例：**
```javascript
import React, { useState, useMemo } from 'react';

function ExpensiveComponent({ num }) {
  const computeExpensiveValue = (n) => {
    console.log('计算...');
    return n * 2;
  };

  const memoizedValue = useMemo(() => computeExpensiveValue(num), [num]);

  return <div>计算结果：{memoizedValue}</div>;
}

function App() {
  const [count, setCount] = useState(1);

  return (
    <div>
      <ExpensiveComponent num={count} />
      <button onClick={() => setCount(count + 1)}>增加</button>
    </div>
  );
}
```
`useMemo` 确保在 `num` 没有改变时，不会重复调用 `computeExpensiveValue`。

### 2.6 `useCallback`

`useCallback` 和 `useMemo` 类似，但它是用来缓存函数的引用。当组件重新渲染时，如果依赖项不变，`useCallback` 返回缓存的函数，而不是生成一个新的函数。

**语法：**
```javascript
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

**示例：**
```javascript
import React, { useState, useCallback } from 'react';

function Button({ onClick }) {
  return <button onClick={onClick}>点击</button>;
}

function App() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    setCount(count + 1);
  }, [count]);

  return (
    <div>
      <p>点击次数：{count}</p>
      <Button onClick={handleClick} />
    </div>
  );
}
```
使用 `useCallback` 确保 `handleClick` 在依赖项 `count` 不变时不会重新创建，从而优化性能。

---
### 使用场景

- `useMemo`：当有昂贵计算的值且需要依赖其他状态或属性时使用。
- `useCallback`：在将回调函数传递给子组件时使用，以防止不必要的渲染。

---

## 3. 自定义 Hooks

除了内置的 Hooks，React 还允许你创建 **自定义 Hooks** 来复用状态逻辑。自定义 Hooks 是一种将组件中重复的逻辑抽离出来的方式，它本质上就是一个以 `use` 开头的普通函数，内部可以使用其他 Hooks。

**示例：**
```javascript
import { useState, useEffect } from 'react';

function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);

    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return width;
}

function App() {
  const width = useWindowWidth();

  return <p>窗口宽度：{width}px</p>;
}
```

## 4. 生命周期对比：类组件 vs Hooks

| 生命周期阶段       | 类组件方法                      | Hooks 等价实现                        |
|--------------------|---------------------------------|---------------------------------------|
| **挂载阶段**       | `componentDidMount`             | `useEffect(() => {...}, [])`         |
| **更新阶段**       | `componentDidUpdate`            | `useEffect(() => {...}, [依赖项])`   |
| **卸载阶段**       | `componentWillUnmount`          | `useEffect(() => {... return () => {...} }, [])` |

通过使用 Hooks，开发者可以根据依赖项灵活地控制副作用的执行时机，使其具备了更强的灵活性。

---

## 5. 结论

React Hooks 提供了强大且灵活的工具来处理状态和生命周期。在函数组件中，`useState`、`useEffect`、`useContext`、`useMemo` 和 `useCallback` 等 Hooks 为开发者提供了管理状态、处理副作用和优化性能的能力。

- **`useState`**：用于声明状态，管理组件的内部状态。
- **`useEffect`**：用于处理副作用，如数据获取和事件监听。
- **`useContext`**：用于消费全局状态，方便组件之间共享数据。
- **`useMemo` 和 `useCallback`**：用于性能优化，缓存计算结果和回调函数。