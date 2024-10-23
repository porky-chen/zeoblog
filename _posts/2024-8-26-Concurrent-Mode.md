---
title: React 18 的并发特性（Concurrent Mode）
author: zeo
categories: [知识点, React]
tags: [React, 并发特性, Concurrent Mode]
render_with_liquid: false
---
React 18 引入了一种新的并发特性，称为并发模式（Concurrent Mode），旨在提高应用的响应性和性能，使得 React 能够更好地处理 UI 更新。这个特性让开发者能够构建更复杂的用户界面，同时仍然保持流畅的用户体验。本文将深入探讨 React 18 的并发特性，包括其工作原理、使用方法以及应用场景。

## 1. 什么是并发模式？

并发模式是 React 18 引入的一种新特性，允许 React 在处理 UI 更新时更加灵活。传统的 React 更新是单线程的，这意味着在进行一项更新时，React 会阻止其他更新的进行。而并发模式则允许 React 预先处理某些更新，使其能够根据用户的互动和网络状态更智能地调度更新，从而改善用户体验。

### 1.1 主要特点

- **非阻塞渲染**：React 可以在后台进行渲染，即使用户正在进行其他操作，也不会造成界面冻结。
- **优先级调度**：React 可以根据不同任务的优先级，动态调整更新的顺序，以保证用户界面的流畅性。
- **Suspense 的增强**：与并发模式结合使用时，Suspense 组件可以更好地处理异步数据加载。

## 2. 如何启用并发模式？

在 React 18 中，启用并发模式非常简单。只需要在创建根组件时调用 `createRoot` 方法。以下是一个示例：

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

通过 `createRoot`，你就启用了并发模式，接下来可以使用并发特性，如 `Suspense` 和 `startTransition`。

## 3. 使用 `Suspense`

在并发模式下，`Suspense` 组件能够更灵活地处理异步操作，允许你在等待某个资源加载时提供备用 UI。

### 3.1 示例代码

```jsx
import React, { Suspense } from 'react';

const LazyComponent = React.lazy(() => import('./LazyComponent'));

const App = () => {
  return (
    <div>
      <h1>欢迎使用 React 18 的并发模式</h1>
      <Suspense fallback={<div>加载中...</div>}>
        <LazyComponent />
      </Suspense>
    </div>
  );
};

export default App;
```

在这个示例中，当 `LazyComponent` 正在加载时，用户将看到 “加载中...” 的提示。

## 4. 使用 `startTransition`

`startTransition` 是 React 18 新增的 API，用于标记某些更新为“过渡”更新。这种更新具有较低的优先级，React 会在空闲时间处理这些更新，从而避免影响用户的交互体验。

### 4.1 示例代码

```jsx
import React, { useState, startTransition } from 'react';

const App = () => {
  const [inputValue, setInputValue] = useState('');
  const [items, setItems] = useState([]);

  const handleInputChange = (e) => {
    const newValue = e.target.value;
    setInputValue(newValue);
    
    startTransition(() => {
      // 过渡更新
      const newItems = generateItems(newValue);
      setItems(newItems);
    });
  };

  return (
    <div>
      <input type="text" value={inputValue} onChange={handleInputChange} />
      <ul>
        {items.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
};

const generateItems = (value) => {
  // 模拟生成大量数据
  return Array.from({ length: 1000 }, (_, i) => `${value} - Item ${i + 1}`);
};

export default App;
```

在这个示例中，`startTransition` 被用来标记生成项目的操作为过渡更新。用户输入时，输入框保持响应，而列表的更新将在后台进行。

## 5. 适用场景

并发模式适用于以下场景：

- **复杂用户交互**：在用户输入、滚动等交互时保持 UI 流畅。
- **异步数据加载**：结合 `Suspense` 处理 API 调用和资源加载。
- **大规模组件树更新**：在大型应用中，逐步更新组件树，以减少卡顿现象。

## 6. 注意事项

- **兼容性**：并发模式在某些情况下可能与现有的 React 代码产生不兼容的行为。在启用并发模式时，务必进行充分的测试。
- **学习成本**：并发模式引入了一些新的 API，可能需要开发者进行学习和适应。

## 7. 结论

React 18 的并发模式为开发者提供了更强大的工具来构建高效和响应迅速的用户界面。通过非阻塞渲染、优先级调度以及与 `Suspense` 的结合使用，开发者可以构建出更加流畅的应用体验。
掌握并发模式的使用，能够让你在面对复杂应用时更加游刃有余。