---
title: React 函数组件与类组件的区别
author: zeo
categories: [知识点, React]
tags: [React, 函数组件, 类组件, Component]
render_with_liquid: false
---

React 组件主要分为两种：**函数组件**（Functional Component）和 **类组件**（Class Component）。这两种组件的定义方式各有不同，随着 React 的发展，函数组件已经逐渐成为主流，但在某些场景下，类组件仍有其独特的优势。

---

## 1. React 类组件与函数组件的定义

### 1.1 类组件

类组件是基于 ES6 类的语法来定义的，早期 React 主要通过类组件来创建复杂的交互逻辑，尤其是在涉及到状态（state）和生命周期（lifecycle）的管理时。类组件必须继承自 `React.Component` 类，并且通常包含 `render` 方法，该方法返回 JSX 结构。

**类组件示例：**

```javascript
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
  }

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <div>
        <p>计数: {this.state.count}</p>
        <button onClick={this.increment}>增加</button>
      </div>
    );
  }
}

export default Counter;
```

在这个例子中，`Counter` 是一个类组件，具有本地状态 `count` 和一个方法 `increment` 来修改状态。在类组件中，`this.state` 用于定义状态，`this.setState()` 用于更新状态。

### 1.2 函数组件

函数组件是用 JavaScript 函数定义的，它们起初是用于渲染简单的 UI，没有状态和生命周期方法。然而，自从 React 16.8 引入了 **Hooks**，函数组件也可以拥有状态和生命周期功能，从而变得更加灵活和功能强大。

**函数组件示例：**

```javascript
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>计数: {count}</p>
      <button onClick={() => setCount(count + 1)}>增加</button>
    </div>
  );
}

export default Counter;
```

在这个例子中，`useState` Hook 用于在函数组件中管理状态。相比类组件，函数组件更加简洁，且不需要使用 `this` 关键字来访问状态或方法。

---

## 2. 类组件与函数组件的主要区别

### 2.1 语法与结构

- **类组件**: 需要继承 `React.Component`，并使用 `render()` 方法返回 JSX，涉及到状态和方法时需要使用 `this` 关键字。
- **函数组件**: 是一个普通的 JavaScript 函数，直接返回 JSX。通过 `Hooks`（如 `useState`、`useEffect`）来处理状态和副作用，不需要使用 `this`。

### 2.2 状态与生命周期

- **类组件**: 通过 `this.state` 来管理状态，使用 `this.setState()` 来更新状态。同时，类组件有一套完整的生命周期方法，如 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount`，用于处理组件挂载、更新和卸载时的副作用。
  
- **函数组件**: 通过 `useState` 来管理状态，使用 `useEffect` 来处理生命周期方法。`useEffect` 能在组件挂载、更新和卸载时执行相应的逻辑，通过依赖数组控制何时执行。

**示例：类组件中的生命周期方法：**

```javascript
class MyComponent extends Component {
  componentDidMount() {
    console.log('组件挂载');
  }

  componentDidUpdate() {
    console.log('组件更新');
  }

  componentWillUnmount() {
    console.log('组件即将卸载');
  }

  render() {
    return <div>类组件</div>;
  }
}
```

**示例：函数组件中的生命周期管理：**

```javascript
import React, { useEffect } from 'react';

function MyComponent() {
  useEffect(() => {
    console.log('组件挂载');
    return () => {
      console.log('组件即将卸载');
    };
  }, []);

  return <div>函数组件</div>;
}
```

### 2.3 性能与优化

- **类组件**: 虽然类组件功能强大，但由于 `this` 的使用，容易造成代码冗余或混乱。此外，类组件的性能优化主要通过 `shouldComponentUpdate` 或 `PureComponent` 来实现，手动控制组件更新。
  
- **函数组件**: 函数组件本身更轻量，且 Hooks 提供了更灵活的性能优化工具（如 `useMemo`、`useCallback`），避免不必要的重新渲染。函数组件的性能通常优于类组件，尤其在 React 内部对 Hooks 的优化下更具优势。

---

## 3. 使用场景对比：类组件 vs 函数组件

### 3.1 函数组件的适用场景

随着 Hooks 的引入，函数组件已经能够胜任绝大多数场景，尤其适合以下情况：
  
- **简单、轻量的组件**: 函数组件结构更简单，尤其是那些没有复杂状态管理或生命周期需求的小型组件。
- **逻辑复用**: Hooks 使得复用状态逻辑变得更加简单。例如，通过自定义 Hooks（Custom Hooks），你可以在不同组件中共享状态逻辑，而无需复杂的嵌套或继承。
- **性能敏感的场景**: 函数组件结合 Hooks，可以通过 `useMemo` 和 `useCallback` 等机制避免不必要的重新渲染，适合在性能敏感的场景中使用。

**示例：使用自定义 Hook 来复用逻辑：**

```javascript
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);

  return { count, increment, decrement };
}

function Counter1() {
  const { count, increment, decrement } = useCounter(10);

  return (
    <div>
      <p>Counter1: {count}</p>
      <button onClick={increment}>增加</button>
      <button onClick={decrement}>减少</button>
    </div>
  );
}

function Counter2() {
  const { count, increment, decrement } = useCounter(5);

  return (
    <div>
      <p>Counter2: {count}</p>
      <button onClick={increment}>增加</button>
      <button onClick={decrement}>减少</button>
    </div>
  );
}
```

### 3.2 类组件的适用场景

虽然函数组件通过 Hooks 几乎可以替代类组件的所有功能，但类组件仍然适用于一些特定场景：

- **遗留代码**: 在旧项目或大型项目中，可能已经存在大量基于类组件的代码。为了减少重构成本或保持代码的一致性，可能会继续使用类组件。
- **一些复杂的生命周期逻辑**: 尽管 `useEffect` 可以替代类组件的生命周期方法，但在某些非常复杂的场景中，类组件可能会更清晰地表达出生命周期的阶段。
  
---

## 4. 函数组件的崛起与类组件的未来

自 React 16.8 引入 Hooks 后，函数组件得到了广泛使用。它们简洁的语法和强大的功能使其成为开发者的首选。大多数新项目和库也已经默认采用函数组件的方式来构建。

### 4.1 Hooks 的优势

- **简洁性**: 函数组件不需要处理 `this`，代码更简洁、易于理解。
- **灵活的状态管理与副作用处理**: Hooks 提供了一种更灵活、可组合的方式来管理状态和副作用逻辑。
- **逻辑复用**: 自定义 Hook 使得在多个组件之间复用逻辑变得更为直观，而不需要引入高阶组件（HOC）或 render props 等复杂模式。

### 4.2 类组件的未来

虽然函数组件逐渐成为主流，但类组件仍然存在于大量遗留项目中，且在某些复杂场景下仍有其优势。因此，类组件不会被完全淘汰，但其使用场景会越来越少。

---

## 5. 何时使用类组件与函数组件？

- **函数组件优先**: 在构建新项目时，优先考虑使用函数组件。它们简洁、现代，并且与 React 生态更好地结合。
- **保持一致性**: 在已有项目中，如果项目已经广泛使用了类组件，为了保持一致性，可能会继续使用类组件，特别是在大规模重构成本较高的情况下。

---

## 总结

- **类组件** 适用于遗留项目、复杂生命周期逻辑或已有项目中的一致性需求。
- **函数组件** 是现代 React 的首选方式，尤其适合轻量、可复用、易于维护的项目。Hooks 提供了强大的功能，能够帮助开发者以更简洁的方式实现复杂的功能。