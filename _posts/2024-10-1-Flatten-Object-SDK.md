---
title: Flatten Object
author: zeo
categories: [小工具, SDK]
tags: [JavaScript, 对象扁平化]
render_with_liquid: false
---

# Flatten Object SDK

`flatten-object-sdk` 是一个轻量级的 JavaScript 库，专为开发者简化复杂的嵌套对象管理而设计。它提供了将多层嵌套对象平展为一维键值对并重建原始结构的方法，非常适合数据存储、处理或传输等场景，在这些场景中，嵌套对象的结构会增加数据操作的复杂性。

## 背景

在处理 API 数据、复杂数据结构或数据库数据时，开发者通常会遇到深层嵌套的对象，操作这些嵌套对象可能非常麻烦，尤其是在将它们转换为平展结构用于存储或数据索引时。`flatten-object-sdk` 通过将嵌套结构转为平展结构并支持反向操作，使处理数组、符号等复杂数据类型更加简便。

## 功能特性

- **嵌套对象平展**：将复杂的多层对象转换为一维平展结构。
- **恢复嵌套结构**：将平展的对象重建为原始嵌套结构。
- **属性管理**：提供属性检查、添加和删除的辅助方法。
- **支持数组与符号**：优雅地处理数组、符号和其他 JavaScript 数据类型。
- **灵活的符号支持**：支持点号和括号符号，便于读写操作。

## 安装

可以通过 NPM 安装：

```bash
npm install flatten-object-sdk

使用方法

要使用 flatten-object-sdk，首先导入并初始化 SDK：

const ObjectManipulator = require('flatten-object-sdk');
const objManipulator = new ObjectManipulator();

示例

下面是一些主要功能的使用示例：

1. 平展一个复杂对象

假设我们有一个包含数组和子对象的嵌套对象：

const complexObject = {
  a: 1,
  b: 'Joy',
  cell: {
    c_a: '2',
    c_b: 'xxx',
    deep: {
      deep_a: 'inner',
      deep_b: 10
    }
  },
  arr: [1, 2, 'apple', 'ios', 'JavaScript', { arr_a: 'node', arr_b: 'TS' }]
};

const flatObject = objManipulator.flatten(complexObject);

console.log(flatObject);

输出：

{
  "a": 1,
  "b": "Joy",
  "cell.c_a": "2",
  "cell.c_b": "xxx",
  "cell.deep.deep_a": "inner",
  "cell.deep.deep_b": 10,
  "arr[0]": 1,
  "arr[1]": 2,
  "arr[2]": "apple",
  "arr[3]": "ios",
  "arr[4]": "JavaScript",
  "arr[5].arr_a": "node",
  "arr[5].arr_b": "TS"
}

这种平展结构特别适合存储在键值数据库中，或用于 API 传输，能将嵌套数据结构转为平展形式。

2. 将平展对象还原为嵌套结构

使用 unflatten 方法可将平展对象还原为原始结构：

const flatObject = {
  "a": 1,
  "b": "Joy",
  "cell.c_a": "2",
  "cell.c_b": "xxx",
  "cell.deep.deep_a": "inner",
  "cell.deep.deep_b": 10,
  "arr[0]": 1,
  "arr[1]": 2,
  "arr[2]": "apple",
  "arr[3]": "ios",
  "arr[4]": "JavaScript",
  "arr[5].arr_a": "node",
  "arr[5].arr_b": "TS"
};

const complexObject = objManipulator.unflatten(flatObject);

console.log(complexObject);

输出：

{
  a: 1,
  b: 'Joy',
  cell: {
    c_a: '2',
    c_b: 'xxx',
    deep: {
      deep_a: 'inner',
      deep_b: 10
    }
  },
  arr: [
    1,
    2,
    'apple',
    'ios',
    'JavaScript',
    { arr_a: 'node', arr_b: 'TS' }
  ]
}

这在接收平展数据并恢复为嵌套对象时非常有用。

3. 其他属性操作

该 SDK 还提供方法来检查、添加和删除对象中的属性。

检查属性是否存在

const exists = objManipulator.hasProperty(complexObject, 'cell.deep.deep_a');
console.log(exists);  // 输出: true

添加新属性

objManipulator.setProperty(complexObject, 'newProperty', 'value');
console.log(complexObject.newProperty);  // 输出: 'value'

删除属性

objManipulator.deleteProperty(complexObject, 'a');
console.log(complexObject.a);  // 输出: undefined

高级用法

支持自定义符号

flatten-object-sdk 支持点号（.）和方括号（[]）符号，便于数组与对象的混合操作，开发者可以自定义数据结构并灵活控制数据访问方式。

错误处理

SDK 对无效或未定义的属性路径具有容错处理能力，确保在不同的运行环境中能保持稳定的表现。