---
title: 理解 类组件 生命周期方法
author: zeo
categories: [知识点, React]
tags: [React, componentDidMount, componentDidUpdate, componentWillUnmount]
render_with_liquid: false
---

# React 的组件生命周期方法：`componentDidMount`、`componentDidUpdate`、`componentWillUnmount`

在 React 的类组件中，生命周期方法是指在组件的不同阶段调用的一系列方法，它们允许开发者在组件的创建、更新和销毁过程中执行特定的操作。React 提供了一些常用的生命周期方法，其中包括 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount`。通过这些方法，开发者可以管理组件的副作用（如数据获取、事件订阅和清理工作）。

## 1. 组件的生命周期阶段

React 的生命周期大致可以分为三个阶段：

- **挂载阶段（Mounting）**：组件第一次被渲染到页面上。
- **更新阶段（Updating）**：组件的状态或属性发生变化时，重新渲染组件。
- **卸载阶段（Unmounting）**：组件从页面中被移除。

每个阶段都有相应的生命周期方法，允许开发者在适当的时机执行代码。

---

## 2. 挂载阶段：`componentDidMount`

`componentDidMount` 是在组件挂载到 DOM 之后立即调用的方法，它只在组件第一次渲染后调用一次。这个方法常用于以下场景：

- **数据获取**：通过网络请求从服务器获取数据并将其设置到组件的状态中。
- **订阅外部事件**：监听窗口大小变化或其他全局事件。
- **DOM 操作**：执行需要依赖 DOM 的操作，如获取元素的大小或设置动画。

### 使用示例：

```jsx
class MyComponent extends React.Component {
  componentDidMount() {
    // 例如：发起网络请求获取数据
    fetch('https://api.example.com/data')
      .then(response => response.json())
      .then(data => this.setState({ data }));

    // 例如：监听窗口大小变化
    window.addEventListener('resize', this.handleResize);
  }

  handleResize = () => {
    // 窗口大小变化时的操作
    console.log('窗口大小发生了变化');
  };

  render() {
    return (
      <div>
        <h1>我的组件</h1>
        {this.state.data ? <p>数据: {this.state.data}</p> : <p>加载中...</p>}
      </div>
    );
  }
}
```

在上面的例子中，`componentDidMount` 用于获取数据并设置事件监听。

---

## 3. 更新阶段：`componentDidUpdate`

`componentDidUpdate` 方法在组件的属性或状态更新后立即被调用。这个方法允许你在组件更新后执行某些操作，例如重新获取数据、更新 DOM 或与外部系统同步。

`componentDidUpdate` 通常用于以下场景：

- **响应属性或状态变化**：根据新的属性或状态进行后续操作，如重新获取数据或更新 DOM。
- **处理副作用**：在组件更新时执行依赖于更新后的 DOM 或数据的逻辑。
  
### 使用示例：

```jsx
class MyComponent extends React.Component {
  componentDidUpdate(prevProps, prevState) {
    // 检查属性是否发生变化
    if (this.props.userId !== prevProps.userId) {
      // 例如：用户 ID 变化时重新获取用户数据
      this.fetchUserData(this.props.userId);
    }

    // 检查状态是否发生变化
    if (this.state.someValue !== prevState.someValue) {
      // 执行基于状态变化的操作
      console.log('状态更新了: ', this.state.someValue);
    }
  }

  fetchUserData(userId) {
    // 假设这是获取用户数据的函数
    fetch(`https://api.example.com/users/${userId}`)
      .then(response => response.json())
      .then(user => this.setState({ user }));
  }

  render() {
    return (
      <div>
        <h1>用户信息</h1>
        {this.state.user ? <p>用户名: {this.state.user.name}</p> : <p>加载中...</p>}
      </div>
    );
  }
}
```

在上面的例子中，`componentDidUpdate` 用于根据 `userId` 属性的变化重新获取用户数据。

---

## 4. 卸载阶段：`componentWillUnmount`

`componentWillUnmount` 在组件即将从 DOM 中移除时调用，通常用于清理工作。这是防止内存泄漏的重要步骤，特别是当组件在挂载阶段注册了事件或订阅时，应该在卸载时取消这些订阅。

常见的清理工作包括：

- **移除事件监听器**：取消挂载时添加的全局事件。
- **清除定时器**：销毁组件前清除通过 `setTimeout` 或 `setInterval` 创建的定时器。
- **取消网络请求**：在组件卸载前取消任何进行中的网络请求。

### 使用示例：

```jsx
class MyComponent extends React.Component {
  componentDidMount() {
    // 监听滚动事件
    window.addEventListener('scroll', this.handleScroll);

    // 设置一个定时器
    this.timer = setInterval(() => {
      console.log('定时器触发');
    }, 1000);
  }

  componentWillUnmount() {
    // 组件卸载时移除事件监听器
    window.removeEventListener('scroll', this.handleScroll);

    // 清除定时器
    clearInterval(this.timer);
  }

  handleScroll = () => {
    console.log('页面滚动');
  };

  render() {
    return (
      <div>
        <h1>我的组件</h1>
        <p>滚动页面以触发事件</p>
      </div>
    );
  }
}
```

在上面的例子中，`componentWillUnmount` 用于在组件卸载前移除 `scroll` 事件监听器并清除定时器。

---

## 5. 生命周期方法的使用场景

### 5.1 `componentDidMount`
- **数据获取**：首次加载组件时，通常在 `componentDidMount` 中发起网络请求来获取所需的数据。
- **订阅事件**：挂载后订阅外部事件，例如窗口大小变化、滚动事件或 WebSocket 连接等。
- **DOM 操作**：在组件加载完毕后，进行任何需要与真实 DOM 交互的操作，例如第三方库的初始化。

### 5.2 `componentDidUpdate`
- **响应数据或属性的变化**：组件更新后，基于新的属性或状态做出响应。例如，当用户点击按钮时，重新获取某些数据。
- **与外部系统同步**：当组件更新时，可能需要向外部系统同步某些信息，如通过 API 更新数据。

### 5.3 `componentWillUnmount`
- **清理副作用**：当组件从界面中移除时，清理任何之前创建的副作用，如事件监听、定时器、取消订阅等。
- **防止内存泄漏**：确保在组件销毁时，所有的资源都被正确释放，以避免内存泄漏问题。

---

## 6. 结论

在 React 中，生命周期方法是管理组件副作用的重要工具。通过 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount`，开发者可以在组件的不同阶段执行特定操作，确保组件能够正确处理副作用、优化性能并避免内存泄漏。在实际开发中，合理使用这些生命周期方法能够帮助你编写更加高效、健壮的 React 组件。