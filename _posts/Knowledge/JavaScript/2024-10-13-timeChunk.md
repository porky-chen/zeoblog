---
title: 高阶函数：分时函数
author: zeo
categories: [知识点, Javascript]
tags: [高阶函数, 分时函数]
render_with_liquid: false
---

**分时函数（Time-Slicing Function）** 是一种优化方法，用于在短时间内批量处理大量任务，避免一次性执行大量计算，造成浏览器卡顿。分时函数的核心思想是将大任务分割成多个小任务，按时间间隔逐步执行，从而减少单次任务的执行时间，提升页面的响应速度。

**分时函数的工作原理**

分时函数的原理是在一段时间内仅执行一部分任务，让出控制权，使浏览器可以在任务之间进行渲染和响应用户的操作。例如，假设要将上万个元素批量渲染到页面上，而直接执行会导致页面无响应或“卡死”，分时函数可以将渲染任务分成若干小批次，每次执行一小部分，缓解浏览器的压力。

**实现分时函数的方式**

分时函数通常利用 setTimeout 或 requestAnimationFrame，将一组需要批量执行的任务拆分，并在一定的时间间隔内逐步执行。

以下是一个分时函数的基本实现：
```js
function timeSlice(tasks, batchSize = 10, delay = 50) {
    let index = 0;

    function executeBatch() {
        const batch = tasks.slice(index, index + batchSize); // 取出当前批次的任务

        // 执行当前批次的任务
        batch.forEach(task => task());

        index += batchSize; // 更新索引，准备执行下一批

        // 如果还有未完成的任务，继续安排下一次执行
        if (index < tasks.length) {
            setTimeout(executeBatch, delay);  // 延迟执行下一批任务
        }
    }

    executeBatch();  // 启动分时任务
}
```
**示例用法**

假设我们有一批任务，任务的目的是创建并添加 1000 个 div 元素到页面上。直接执行会导致浏览器可能卡顿，因此可以通过分时函数分批添加元素：
```js
// 构建任务队列
const tasks = Array.from({ length: 1000 }, (_, i) => () => {
    const div = document.createElement('div');
    div.innerText = `Item ${i + 1}`;
    document.body.appendChild(div);
});

// 使用分时函数执行任务
timeSlice(tasks, 20, 50);  // 每次处理 20 个任务，每 50ms 执行一次
```
在上面的代码中，每 50 毫秒会执行 20 个任务，直到完成全部任务，这样页面的响应会更流畅，浏览器不会因任务堆积出现无响应的情况。

**优化点：使用 requestAnimationFrame**

在现代浏览器中，我们可以使用 requestAnimationFrame 来替代 setTimeout。requestAnimationFrame 在每次浏览器重绘之前调用，频率会被限制在每秒 60 次左右（每帧约 16ms），可以更自然地与浏览器渲染同步：
```js
function timeSlice(tasks, batchSize = 10) {
    let index = 0;

    function executeBatch() {
        const batch = tasks.slice(index, index + batchSize);
        batch.forEach(task => task());

        index += batchSize;

        if (index < tasks.length) {
            requestAnimationFrame(executeBatch);
        }
    }

    executeBatch();
}
```
**分时函数的使用场景**

1.	**大量 DOM 操作**：当需要批量更新或创建大量 DOM 元素时，分时函数可以避免一次性操作带来的卡顿感。例如，在渲染大量数据项或更新大批量元素样式时，可以分批处理，提升用户体验。
2.	**数据处理**：对于需要处理大量数据（如循环计算、数组遍历）等耗时任务，可以通过分时函数分批计算，避免阻塞主线程。例如，数据分析、文本处理等任务也可以分批进行。
3.	**动画效果**：在某些场景中，可以将复杂动画拆分为多个小步骤，逐步实现。这种方式在创建大量帧时可以让出渲染控制权，不会影响页面的平滑性。

**分时函数与函数节流、防抖的区别**

- **函数节流**：在设定的时间间隔内限制函数执行的频率，通常在滚动、窗口大小调整等事件上使用。
- **函数防抖**：延迟函数的执行，直到事件停止一段时间再执行，多用于搜索框输入、表单验证等事件。
- **分时函数**：分批执行任务，每次执行一小部分任务，让出控制权，适用于大量任务的批量处理。

**总结**

分时函数是一种高效的任务分解方法，可以解决在短时间内批量执行大量任务的性能问题。通过将任务拆分并逐步执行，分时函数可以减轻单次执行的压力，从而提升页面的响应速度，适用于大量 DOM 操作和数据处理等耗时任务。