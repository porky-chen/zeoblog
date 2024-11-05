---
title: 高阶函数：惰性加载函数
author: zeo
categories: [知识点, JavaScript]
tags: [高阶函数, 惰性加载函数]
render_with_liquid: false
---
**惰性加载函数（Lazy Loading Function）** 是一种高阶函数的应用，主要用于优化函数的执行效率。惰性加载函数的核心思想是 **在首次调用函数时，判断并执行具体的逻辑**，并在执行后 **将函数重写成更简洁的版本**，以便后续调用时跳过判断逻辑，从而提高性能。

**惰性加载函数的工作原理**

在 JavaScript 中，不同的浏览器或运行环境对某些 API 的支持情况各不相同，例如事件处理方式、CSS 属性的检测等。通常情况下，为了兼容不同的环境，我们会在函数内添加条件判断逻辑。但这种重复的判断会在后续的调用中增加不必要的开销。**惰性加载函数** 通过 **在函数首次调用时确定运行环境**，并 **根据环境重写函数的实现**，从而在后续调用时避免不必要的判断。

**惰性加载函数的实现方式**

惰性加载函数的常见实现方式是通过高阶函数，将函数包装在另一个函数中。首次调用时执行逻辑判断，并在执行后直接重写该函数。

**示例：惰性加载事件绑定**

在 DOM 操作中，事件绑定方式因浏览器不同而有所不同。通过惰性加载函数，我们可以在首次调用时确定事件绑定的实现方式，后续直接使用绑定好的方法，而不再重复判断。
```js
function addEvent(element, event, handler) {
    if (element.addEventListener) {
        addEvent = function(element, event, handler) {
            element.addEventListener(event, handler, false);
        };
    } else if (element.attachEvent) {  // 兼容旧版IE
        addEvent = function(element, event, handler) {
            element.attachEvent('on' + event, handler);
        };
    } else {
        addEvent = function(element, event, handler) {
            element['on' + event] = handler;
        };
    }
    // 执行经过判断的最终方法
    addEvent(element, event, handler);
}
```
在上面的代码中，addEvent 函数首次调用时会检查 addEventListener、attachEvent 或直接赋值的方法来绑定事件，根据判断结果 **重写自身**。在后续调用中，addEvent 函数已不再包含条件判断，直接执行所选的事件绑定方式，从而提升性能。

**惰性加载函数的执行流程**
1.	**首次调用时**：进入函数后，执行判断逻辑来确定当前环境所适用的实现方式，并 **重写函数** 为选择的实现。
2.	**重写函数**：将函数改写为符合当前环境的方法，并 移除环境判断逻辑。
3.	**后续调用时**：直接使用已经确定的实现，避免重复判断，提升效率。

**惰性加载函数的优势**
1.	**性能优化**：减少不必要的条件判断，提升函数在多次调用场景下的执行效率。
2.	**代码简洁**：使用惰性加载模式可以让后续的函数逻辑更清晰，不包含不必要的兼容性判断。
3.	**动态适配环境**：首次调用时自动适配环境，一次适配后无需多次执行判断。

**其他示例**

**示例 1：惰性加载图片资源**

在现代网页中，惰性加载（Lazy Loading）通常指图片、视频等资源的按需加载，减少首次加载的资源数量，提高页面加载速度。例如，在滚动页面时仅加载用户可见区域的图片。
```js
function lazyLoadImage(img) {
    const src = img.getAttribute('data-src');
    img.src = src;
    img.onload = () => img.classList.add('loaded');
}

window.addEventListener('scroll', throttle(() => {
    document.querySelectorAll('img[data-src]').forEach(img => {
        const rect = img.getBoundingClientRect();
        if (rect.top < window.innerHeight && rect.bottom > 0) {
            lazyLoadImage(img);
            img.removeAttribute('data-src');
        }
    });
}, 200));
```
此代码中，当图片接近视口时会触发 lazyLoadImage，设置图片的 src 属性，确保只有在必要时才加载资源。

**示例 2：惰性加载计算函数**

假设有一个复杂的计算函数，它需要根据首次传入的参数计算结果，后续调用时无需重复计算，可以通过惰性加载函数缓存结果：
```js
function computeOnce() {
    let result;

    computeOnce = function() {
        return result;
    };
    
    result = Math.random() * 1000; // 假设为复杂计算
    return result;
}

console.log(computeOnce()); // 第一次计算并缓存结果
console.log(computeOnce()); // 直接返回缓存的结果
```
在这个示例中，computeOnce 函数在首次调用后被重写为直接返回缓存结果的函数，后续调用不再执行重复计算。

**使用场景**

1.	**浏览器兼容处理**：可以在事件绑定、CSS 属性检测等涉及兼容性判断的操作中应用惰性加载，首次调用后自动适配环境。
2.	**资源按需加载**：对图片、视频等资源进行按需加载，以减少首次加载的资源量和时间。
3.	**重复计算优化**：在某些计算复杂、结果稳定的场景中可以通过惰性加载缓存计算结果，避免不必要的重复计算。
4.	**条件初始化**：当某个操作仅在首次需要时才执行，比如初始化第三方库、注册全局事件等，使用惰性加载可确保操作只执行一次。

**惰性加载函数与其他高阶函数的区别**

-	**惰性加载**：在首次调用时判断并确定函数逻辑，后续直接调用，不包含判断逻辑，适用于需要确定执行逻辑的情况。
-	**节流**：限制高频事件在固定时间内只能执行一次，用于控制执行频率。
-	**防抖**：在高频触发时只执行最后一次操作，常用于表单输入等场景。
-	**分时函数**：将大任务分割成小任务分批执行，适用于大量计算或操作 DOM 的场景。

**总结**

惰性加载函数是一种性能优化技术，通过首次调用时判断并重写函数来避免重复的条件判断，提高函数执行效率。在浏览器兼容性处理、资源按需加载、复杂计算等需要动态适配环境的场景中，惰性加载函数能够显著提升性能和用户体验。