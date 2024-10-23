---
title: Vue3 相比 Vue2 的主要变化
author: zeo
date: 2024-8-17
categories: [知识点, Vue, Vue3 相比 Vue2 的主要变化]
tags: [Vue]
render_with_liquid: false
---
# Vue 3 相比 Vue 2 引入了许多新特性和改进，主要在性能、架构、语法以及开发体验等方面做了优化。

### 1. **性能提升**
- **更快的渲染**: Vue 3 在渲染速度上有了显著提升，得益于编译时的优化和更高效的虚拟 DOM 实现。
- **更小的打包体积**: Vue 3 使用了 Tree-Shaking 机制，只打包实际使用的功能模块，减少了最终的代码体积。

### 2. **组合式 API（Composition API）**
- **`setup` 函数**: Vue 3 引入了组合式 API，核心是 `setup` 函数，它在组件实例创建之前调用，用于声明响应式状态、计算属性、方法等，提供了更灵活的逻辑组织方式。
- **逻辑复用**: 组合式 API 让逻辑复用变得更加容易，相比于 Vue 2 的混入（Mixins），组合式 API 可以避免命名冲突，更直观地组织和复用代码。

### 3. **新组件定义方式**
- **`<script setup>` 语法**: 在 Vue 3 中，组合式 API 的语法可以通过 `<script setup>` 来简化书写，这种方式使得组件的定义更加简洁，避免了模版和脚本分离的问题。

### 4. **全新的响应式系统**
- **基于 Proxy**: Vue 3 使用 ES6 的 `Proxy` 替代了 Vue 2 中的 `Object.defineProperty`，使得响应式数据更加健壮，能够处理数组、嵌套对象等复杂情况，并且能够捕获所有 JavaScript 对象的操作。
- **更好的性能**: 新的响应式系统减少了对不必要的依赖追踪和副作用，提升了整体的性能。

### 5. **Fragment 和 Teleport**
- **Fragment**: Vue 3 支持在组件中返回多个根元素（Fragment），不再需要一个根元素包裹所有内容，增强了模板的灵活性。
- **Teleport**: 通过 `Teleport` 组件，可以将组件的 DOM 节点渲染到组件树外部的指定位置，比如渲染模态框到 `body` 之外。

### 6. **全新的生命周期钩子**
- **名称变更**: 一些生命周期钩子在 Vue 3 中进行了重命名，例如 `beforeDestroy` 和 `destroyed` 分别更名为 `beforeUnmount` 和 `unmounted`，以更好地表达其功能。
- **新的生命周期钩子**: 新增了如 `onBeforeMount`, `onMounted`, `onBeforeUnmount` 等组合式 API 的钩子函数。

### 7. **TypeScript 支持**
- **更好的 TypeScript 支持**: Vue 3 是从底层开始设计以支持 TypeScript，因此 TypeScript 的支持更加完善。开发者可以更轻松地在 Vue 项目中使用类型检查和智能提示。

### 8. **自定义渲染器**
- **可扩展的渲染器 API**: Vue 3 提供了基于 `createRenderer` 的自定义渲染器 API，使得 Vue 可以用于 Web 以外的环境（如原生应用、游戏引擎等）。

### 9. **全新的 Vue Router 和 Vuex**
- **Vue Router 4**: Vue Router 在 Vue 3 中进行了重写，支持了组合式 API，并且提升了类型支持和性能。
- **Vuex 4**: Vuex 也进行了更新，以支持 Vue 3 的组合式 API，增强了与 TypeScript 的集成。

### 10. **开发工具**
- **Vue Devtools**: Vue 3 的 Devtools 工具得到了增强，可以更好地调试组合式 API 和 Vue 3 的新特性。

### 11. **Tree Shaking**
- **按需加载**: Vue 3 支持组件的按需加载，未使用的代码在生产环境下不会被打包，从而减少打包体积。

### 12. **更好的 Error Handling**
- **改进的错误处理**: Vue 3 提供了更强大的错误处理机制，允许在全局或组件级别捕获错误，并提供了更详细的错误信息。

### 总结
Vue 3 带来了很多令人兴奋的变化和改进，它在性能、灵活性和开发体验上都有显著提升，同时也引入了许多现代化的特性，使得 Vue 在大型项目中的使用更加顺畅和高效。如果你在使用 Vue 2，迁移到 Vue 3 可以带来更好的开发体验和性能。