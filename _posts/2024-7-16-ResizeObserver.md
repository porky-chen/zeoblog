---
title: 实现跨浏览器支持的 ResizeObserver Polyfill
author: zeo
date: 2024-7-16
categories: [知识点, JavaScript]
tags: [ResizeObserver, Vue, React, 元素变化]
render_with_liquid: false
---

### 使用自定义 `ResizeObserver` 在 Vue 和 React 中监听元素尺寸变化

在前端开发中，监听元素尺寸变化是一个常见的需求。虽然现代浏览器提供了 `ResizeObserver` API，但是在一些旧版本的浏览器中可能不支持。本文将介绍如何实现一个自定义的 `ResizeObserver`，并在 Vue 和 React 中使用它来监听元素的尺寸变化。
2
### 什么是 ResizeObserver？

`ResizeObserver` 是一种新的浏览器 API，用于监听元素内容矩形的变化。当元素的尺寸（宽度或高度）发生变化时，会触发回调函数。`ResizeObserver` 是响应式设计和复杂布局的理想选择。

### 自定义 ResizeObserver 的实现

以下是一个简单的 `ResizeObserver` 实现，它使用 `window.resize` 事件来检查元素的尺寸变化：

```javascript
window.ResizeObserver = class {
  constructor(callback) {
    this.observables = [];
    this.boundCheck = this.check.bind(this);
    this.callback = callback;
  }

  observe(element) {
    if (!this.observables.some(observable => observable.element === element)) {
      this.observables.push({ element, width: element.clientWidth, height: element.clientHeight });
    }
    window.addEventListener('resize', this.boundCheck);
    this.boundCheck();
  }

  unobserve(element) {
    this.observables = this.observables.filter(observable => observable.element !== element);
    if (!this.observables.length) {
      window.removeEventListener('resize', this.boundCheck);
    }
  }

  disconnect() {
    this.observables = [];
    window.removeEventListener('resize', this.boundCheck);
  }

  check() {
    this.observables.forEach(observable => {
      const { element } = observable;
      const newWidth = element.clientWidth;
      const newHeight = element.clientHeight;
      if (newWidth !== observable.width || newHeight !== observable.height) {
        observable.width = newWidth;
        observable.height = newHeight;
        this.callback([{ target: element }]);
      }
    });
  }
};
```

### 代码解释：

1. **构造函数**：`constructor(callback)` 接受一个回调函数作为参数，并初始化 `observables` 数组、绑定的 `check` 方法和回调函数。

2. **observe(element)**：该方法用于观察指定元素的尺寸变化。如果该元素尚未被观察，则将其加入 `observables` 数组，并添加 `resize` 事件监听器。

3. **unobserve(element)**：该方法用于停止观察指定元素的尺寸变化，并从 `observables` 数组中移除。如果没有需要观察的元素，则移除 `resize` 事件监听器。

4. **disconnect()**：该方法用于停止观察所有元素的尺寸变化，并移除所有 `resize` 事件监听器。

5. **check()**：该方法遍历所有被观察的元素，检查它们的尺寸是否发生变化。如果发生变化，则更新尺寸并触发回调函数。

### 在 Vue 中使用自定义 ResizeObserver

下面是一个在 Vue 组件中使用自定义 `ResizeObserver` 的示例：

```vue
<template>
  <div ref="resizeElement" class="resize-element">
    <p>Resize the window to see changes</p>
  </div>
</template>

<script>
export default {
  name: 'ResizeObserverExample',
  data() {
    return {
      resizeObserver: null,
      elementSize: { width: 0, height: 0 }
    };
  },
  mounted() {
    this.resizeObserver = new ResizeObserver(entries => {
      for (let entry of entries) {
        const { width, height } = entry.target.getBoundingClientRect();
        this.elementSize.width = width;
        this.elementSize.height = height;
        console.log('Element resized:', this.elementSize);
      }
    });
    this.resizeObserver.observe(this.$refs.resizeElement);
  },
  beforeDestroy() {
    if (this.resizeObserver) {
      this.resizeObserver.disconnect();
    }
  }
};
</script>

<style>
.resize-element {
  width: 100%;
  height: 200px;
  background-color: #f0f0f0;
}
</style>
```

##### 代码解释：

1. **`data()`**：初始化组件的状态，包含 `resizeObserver` 和 `elementSize`。

2. **`mounted()`**：在组件挂载后，创建 `ResizeObserver` 实例并开始观察 `resizeElement` 元素的尺寸变化。

3. **`beforeDestroy()`**：在组件销毁前，断开 `ResizeObserver` 的连接，停止观察。

4. **`resizeElement`**：一个简单的 `div` 元素，当窗口尺寸变化时，其宽度和高度会被更新。

### 在 React 中使用自定义 ResizeObserver

下面是一个在 React 组件中使用自定义 `ResizeObserver` 的示例：

```jsx
import React, { useRef, useEffect, useState } from 'react';

const ResizeObserverExample = () => {
  const resizeRef = useRef(null);
  const [size, setSize] = useState({ width: 0, height: 0 });

  useEffect(() => {
    const resizeObserver = new ResizeObserver(entries => {
      for (let entry of entries) {
        const { width, height } = entry.target.getBoundingClientRect();
        setSize({ width, height });
        console.log('Element resized:', { width, height });
      }
    });
    if (resizeRef.current) {
      resizeObserver.observe(resizeRef.current);
    }

    return () => {
      if (resizeObserver) {
        resizeObserver.disconnect();
      }
    };
  }, []);

  return (
    <div ref={resizeRef} style={{ width: '100%', height: '200px', backgroundColor: '#f0f0f0' }}>
      <p>Resize the window to see changes</p>
      <p>Width: {size.width}px, Height: {size.height}px</p>
    </div>
  );
};

export default ResizeObserverExample;
```

### 代码解释：

1. **`useRef`**：创建一个 `ref`，用于引用需要观察的 DOM 元素。

2. **`useState`**：定义状态 `size`，用于存储元素的宽度和高度。

3. **`useEffect`**：在组件挂载后，创建 `ResizeObserver` 实例并开始观察 `resizeRef` 元素的尺寸变化。在组件卸载时，断开 `ResizeObserver` 的连接，停止观察。

4. **`resizeRef`**：一个 `div` 元素，当窗口尺寸变化时，其宽度和高度会被更新。

### 总结

通过自定义 `ResizeObserver`，我们可以在 Vue 和 React 组件中轻松监听元素的尺寸变化。无论是响应式设计还是复杂布局，这种方法都非常实用。如果你的项目需要兼容旧版本的浏览器，不妨尝试一下这种自定义实现。

希望这篇文章对你有所帮助！欢迎在评论区分享你的使用经验和建议。