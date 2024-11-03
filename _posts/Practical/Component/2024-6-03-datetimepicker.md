---
title: Vue.js Date Picker 实用技巧
author: zeo
date: 2024-6-03
categories: [实战篇, 组件]
tags: [组件, Date Picker]
render_with_liquid: false
---

# Vue.js Date Picker 实用技巧

在前端开发中，日期选择器（Date Picker）是非常常见的组件，尤其是在处理表单时。本文将介绍几种常见的日期选择需求及其实现方法，帮助你在实际项目中更高效地处理日期选择逻辑。这些例子将以 Vue.js 为基础，使用了 `disabledDate` 方法来控制日期的可选性。

## 1. 只能选择今天之后的日期
如果我们希望用户只能选择今天或之后的日期，可以通过 `disabledDate` 方法禁用今天之前的日期。以下代码将禁用所有比当前时间小的日期：

```javascript
data() {
  return {
    pickerOptions: {
      disabledDate(time) {
        return time.getTime() < Date.now();
      }
    }
  };
}
```

### 解释：
- `time.getTime()` 获取选中日期的时间戳。
- `Date.now()` 返回当前时间的时间戳。
- 通过比较这两个值，如果选中的日期比现在的时间早，返回 `true`，该日期就会被禁用。

## 2. 开始日期不得选择早于今天
在某些场景下，我们希望开始日期不能早于今天。这里的实现方式与第一个例子类似，只是加了一个小的调整来避免时间精度问题：

```javascript
data() {
  return {
    pickerOptions: {
      disabledDate(time) {
        return time.getTime() < Date.now() - 8.64e7; // 8.64e7 毫秒等于 1 天
      }
    }
  };
}
```

### 解释：
- `8.64e7` 是 24 小时的毫秒数，确保今天也可以被选中，不会因为时间误差导致禁用今天。

## 3. 只能选择今天以及以前的日期
如果需要让用户只能选择今天或以前的日期，我们可以反过来禁用今天之后的日期：

```javascript
data() {
  return {
    pickerOptions: {
      disabledDate(time) {
        return time.getTime() > Date.now() - 8.64e6; // 允许今天及以前
      }
    }
  };
}
```

### 解释：
- `Date.now() - 8.64e6` 允许用户选择到当前的具体时间，今天仍然可选。

## 4. 只能选择今天之前的日期
在某些应用场景中，可能需要限制用户只能选择今天之前的日期：

```javascript
data() {
  return {
    pickerOptions0: {
      disabledDate(time) {
        return time.getTime() > Date.now();
      }
    }
  };
}
```

### 解释：
- 该方法禁用今天和今天之后的所有日期，确保只能选择今天之前的日期。

## 5. 设置选择三个月之前到今天的日期
如果我们希望用户只能选择从三个月前到今天的日期，可以通过限制最大和最小时间范围来实现：

```javascript
data() {
  return {
    pickerOptions0: {
      disabledDate(time) {
        let curDate = new Date().getTime();
        let threeMonths = curDate - 90 * 24 * 3600 * 1000; // 三个月的时间跨度
        return time.getTime() > curDate || time.getTime() < threeMonths;
      }
    }
  };
}
```

### 解释：
- `90 * 24 * 3600 * 1000` 计算出三个月的时间长度（以毫秒为单位）。
- 该方法确保用户只能选择从三个月前到今天的日期，三个月前的日期和今天之后的日期都被禁用。

## 小结
通过这些不同的 `disabledDate` 配置，你可以轻松地根据不同的业务需求灵活地控制日期选择范围。在实际项目中，你可以结合这些方法，甚至通过额外的逻辑自定义日期选择器的行为，提升用户体验。