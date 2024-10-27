---
title: 标签模板
author: zeo
date: 2024-7-17
categories: [知识点, JavaScript]
tags: [JavaScript, 标签模板, ES6]
render_with_liquid: false
---

# 深入理解ES6的“标签模板”（Tagged Template）

“标签模板”（Tagged Template）就是其中之一。它为模板字符串添加了强大的功能，允许你将字符串与函数结合，创建出更复杂和灵活的字符串处理方式。

本文将介绍什么是标签模板、如何使用它、一些实用例子，以及它在哪些场景中可以发挥作用。

## 什么是“标签模板”？

ES6中的**模板字符串**使用反引号 (`` ` ``) 来定义，允许嵌入变量或表达式。这种方式非常适合字符串拼接和动态内容的生成。而**标签模板**是模板字符串的进一步增强，允许在模板字符串前添加一个函数（称为“标签”），以便对字符串及其嵌入的表达式进行自定义处理。

### 标签模板的语法

```javascript
tag`Template String`
```

- `tag` 是一个函数，它会接收模板字符串的各个部分以及嵌入的表达式。
- `Template String` 是一个模板字符串。

当你使用标签模板时，`tag` 函数会在模板字符串解析之前被调用，这意味着你可以控制它的输出格式、执行逻辑等。

### 标签模板的工作机制

当标签模板被调用时，`tag` 函数会接收两个主要的参数：
1. **第一个参数**：一个数组，包含模板字符串的“静态部分”。
2. **剩余的参数**：嵌入的表达式的结果，它们依次传递给标签函数。

### 一个简单的例子

```javascript
function tag(strings, ...values) {
    console.log(strings);  // 输出: ["Hello ", " world!"]
    console.log(values);   // 输出: ["beautiful"]
    return strings[0] + values[0] + strings[1];
}

const word = "beautiful";
console.log(tag`Hello ${word} world!`);  // 输出: Hello beautiful world!
```

在上面的例子中，`tag` 函数接收了模板字符串的两个静态部分 `"Hello "` 和 `" world!"`，以及嵌入的变量 `word` 的值 `"beautiful"`。通过处理这些输入，标签函数可以控制最终的输出。

## 标签模板的常见使用场景

1. **安全字符串处理（防止XSS攻击）**
   在处理用户输入时，标签模板可以用于过滤或转义特殊字符，防止恶意注入。例如，处理HTML字符串时可以防止XSS攻击。

   ```javascript
   function escapeHTML(strings, ...values) {
       return strings.reduce((result, str, i) => {
           const escaped = String(values[i - 1] || '')
               .replace(/&/g, '&amp;')
               .replace(/</g, '&lt;')
               .replace(/>/g, '&gt;')
               .replace(/"/g, '&quot;')
               .replace(/'/g, '&#39;');
           return result + escaped + str;
       });
   }

   const unsafeInput = "<script>alert('XSS');</script>";
   const output = escapeHTML`<div>${unsafeInput}</div>`;
   console.log(output);  // 输出: <div>&lt;script&gt;alert('XSS');&lt;/script&gt;</div>
   ```

2. **国际化（i18n）**
   标签模板可以用于处理多语言内容，通过替换模板中的动态变量来支持不同的语言。

   ```javascript
   function i18n(strings, ...values) {
       const translations = {
           hello: "Hola",
           world: "mundo"
       };
       return strings.reduce((result, str, i) => result + str + (values[i] ? translations[values[i]] : ""), "");
   }

   const greeting = i18n`Hello ${"hello"} ${"world"}!`;
   console.log(greeting);  // 输出: Hola mundo!
   ```

3. **格式化输出**
   对于格式化货币、日期等输出，标签模板提供了一种灵活的方式来控制显示格式。

   ```javascript
   function currency(strings, ...values) {
       return strings.reduce((result, str, i) => {
           const value = values[i - 1] !== undefined ? `$${parseFloat(values[i - 1]).toFixed(2)}` : "";
           return result + value + str;
       });
   }

   const amount = 1234.5;
   const formatted = currency`The total amount is ${amount}.`;
   console.log(formatted);  // 输出: The total amount is $1234.50.
   ```

4. **日志格式化**
   通过标签模板，可以在日志中自动格式化时间戳、级别和消息，增强日志的可读性。

   ```javascript
   function log(strings, ...values) {
       const timestamp = new Date().toISOString();
       return `[${timestamp}] ${strings.reduce((result, str, i) => result + str + (values[i - 1] || ""), "")}`;
   }

   const level = "INFO";
   const message = "Application started";
   console.log(log`${level}: ${message}`);  // 输出: [2024-10-22T08:35:12.345Z] INFO: Application started
   ```

## 标签模板的实际应用

- **处理模板语言**：可以用于编写类似于Handlebars或Mustache的模板引擎，动态生成内容。
- **查询语句生成器**：例如生成SQL查询语句时，可以通过标签模板防止SQL注入攻击，确保安全性。
- **动态内容渲染**：在前端应用中，标签模板可以用来处理复杂的HTML结构、数据绑定和动态内容渲染。
  
### 总结

ES6的**标签模板**为JavaScript的字符串处理带来了极大的灵活性和扩展性。通过结合模板字符串和函数，你可以创建更加安全、简洁和可读的代码，无论是用于格式化输出、国际化、多语言支持，还是安全处理用户输入，标签模板都能发挥巨大的作用。

理解并掌握标签模板的用法，可以让你的代码更加灵活和安全，并帮助你在处理复杂的字符串操作时保持代码简洁。