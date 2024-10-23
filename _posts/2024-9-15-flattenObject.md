---
title: JavaScript 对象扁平化
author: zeo
date: 2024-9-15 19:46:00 +0800
categories: [小工具, 编程场景设计]
tags: [JavaScript, 对象扁平化, 递归, 遍历]
render_with_liquid: false
---

# JavaScript 对象扁平化：将多维对象转为一维结构的实用方法

在 JavaScript 开发中，我们经常会遇到复杂嵌套对象的场景。这些对象包含嵌套的子对象或数组，在处理和操作这些嵌套结构时可能会增加代码复杂度。为了简化操作，将多维对象转换为一维对象（也称为“对象扁平化”）是一个常见的解决方案。

## 一、问题描述：对象扁平化

假设我们有一个嵌套结构的对象 `obj`，包含对象、数组和其他各种类型的数据。目标是将嵌套的结构通过遍历展开成一维对象，保留原始键值对的层次关系。

### 示例输入对象

```javascript
let obj = {
  a: 1,
  b: 'Joy',
  c: true,
  d: undefined,
  f: Symbol('mySymbol'),
  g: null,
  cell: {
    c_a: '2',
    c_b: 'xxx',
    deep: {
      deep_a: 'inner',
      deep_b: 10,
    }
  },
  arr: [1, 2, 'apple', 'ios', 'Javascript'],
}
```

### 预期输出对象

```javascript
let output = {
  a: 1,
  b: 'Joy',
  c: true,
  d: undefined,
  f: Symbol('mySymbol'),
  g: null,
  'cell.c_a': '2',
  'cell.c_b': 'xxx',
  'cell.deep.deep_a': 'inner',
  'cell.deep.deep_b': 10,
  'arr[0]': 1,
  'arr[1]': 2,
  'arr[2]': 'apple',
  'arr[3]': 'ios',
  'arr[4]': 'Javascript'
};
```

## 二、实现设计：对象扁平化方法

我们将通过递归的方式遍历嵌套对象，生成以路径为键的扁平化对象。对于数组，我们将键名通过索引表示，对于对象则通过点符号 (`.`) 表示层级关系。

### 方法设计

1. **递归遍历**：对于每一个对象或数组元素，递归遍历其子元素。
2. **键名构造**：对于对象中的子元素，使用 `.` 拼接父键与子键，对于数组则通过 `[index]` 来标识索引。
3. **基础类型处理**：如果遇到基础类型（如数字、字符串、布尔值等），直接将它们加入到最终结果中。
4. **符号和未定义值**：保留 `Symbol` 和 `undefined` 的原始类型。

### 实现代码

```javascript
function flattenObject(obj, parentKey = '', result = {}) {
  // 遍历对象或数组中的每一个键值对
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      // 构建新的键名，如果有父键，拼接当前键名
      const newKey = Array.isArray(obj) ? `${parentKey}[${key}]` : parentKey ? `${parentKey}.${key}` : key;
      
      // 判断当前值是否是对象或数组，并递归处理
      if (typeof obj[key] === 'object' && obj[key] !== null && !(obj[key] instanceof Symbol)) {
        flattenObject(obj[key], newKey, result);
      } else {
        // 如果是基础类型或其他类型，直接赋值到结果对象中
        result[newKey] = obj[key];
      }
    }
  }
  return result;
}

// 测试函数
let obj = {
  a: 1,
  b: 'Joy',
  c: true,
  d: undefined,
  f: Symbol('mySymbol'),
  g: null,
  cell: {
    c_a: '2',
    c_b: 'xxx',
    deep: {
      deep_a: 'inner',
      deep_b: 10,
    }
  },
  arr: [1, 2, 'apple', 'ios', 'Javascript'],
};

let output = flattenObject(obj);
console.log(output);
```

### 输出结果

```javascript
{
  a: 1,
  b: 'Joy',
  c: true,
  d: undefined,
  f: Symbol(mySymbol),
  g: null,
  'cell.c_a': '2',
  'cell.c_b': 'xxx',
  'cell.deep.deep_a': 'inner',
  'cell.deep.deep_b': 10,
  'arr[0]': 1,
  'arr[1]': 2,
  'arr[2]': 'apple',
  'arr[3]': 'ios',
  'arr[4]': 'Javascript'
}
```

## 三、应用场景

对象扁平化技术在前端开发中有着广泛的应用，特别是在处理复杂数据结构、状态管理或表单提交时，扁平化可以极大地简化数据的处理逻辑。

### 1. **表单数据的处理**

在表单数据的提交和保存过程中，通常会遇到嵌套的对象结构。例如，用户的个人信息中可能包含地址、联系方式等嵌套结构。通过对象扁平化，可以将这些嵌套结构展平，方便处理或存储到数据库中。

```javascript
let formData = {
  name: 'John',
  address: {
    street: '123 Main St',
    city: 'New York'
  },
  phoneNumbers: ['123-456-7890', '987-654-3210']
};

// 通过扁平化可以得到：
{
  name: 'John',
  'address.street': '123 Main St',
  'address.city': 'New York',
  'phoneNumbers[0]': '123-456-7890',
  'phoneNumbers[1]': '987-654-3210'
}
```

这样扁平化后的数据结构更容易提交给后端，或存储到 NoSQL 数据库中。

### 2. **状态管理**

在前端状态管理库（如 Redux、Vuex）中，我们通常需要将状态对象保持为扁平化的结构，以便于更好地管理和更新。嵌套的状态对象容易导致复杂的深层次更新操作，而扁平化后的状态对象能够通过简单的键值操作来进行更新。

```javascript
let initialState = {
  user: {
    id: 1,
    name: 'Alice',
    preferences: {
      theme: 'dark',
      language: 'en'
    }
  }
};

// 通过扁平化转换为
{
  'user.id': 1,
  'user.name': 'Alice',
  'user.preferences.theme': 'dark',
  'user.preferences.language': 'en'
}
```

### 3. **处理 API 响应**

在处理来自 REST API 或 GraphQL 的数据时，经常会遇到嵌套的对象结构。通过扁平化处理，可以快速将嵌套的对象转换为简单的一维对象，从而便于在组件中直接使用数据或更新 UI。

## 四、总结

对象扁平化是前端开发中处理复杂数据结构时的一个有力工具。通过递归遍历嵌套对象或数组，我们能够将多维对象结构转化为一维结构，从而简化后续的处理工作。无论是表单提交、状态管理，还是 API 数据处理，扁平化都可以提高代码的可读性和维护性。