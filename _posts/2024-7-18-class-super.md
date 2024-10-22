---
title: Class & Super
author: zeo
date: 2024-7-18
categories: [知识点, JavaScript, Class & Super]
tags: [JavaScript, ES6, Class, Super, getPrototypeOf, setPrototypeOf]
render_with_liquid: false
---

# 学习Class 类、Super、getPrototypeOf() 和 setPrototypeOf()

ES6 引入了许多增强 JavaScript 面向对象编程的特性，**类（Class）**、**`super`**、**`Object.getPrototypeOf()`** 和 **`Object.setPrototypeOf()`** 是其中的核心概念。它们为对象继承和原型链操作提供了更加简洁、直观的语法，改善了开发者使用 JavaScript 进行面向对象编程的体验。

## 一、Class 类

在 ES6 之前，JavaScript 使用构造函数和原型来实现对象继承。而 ES6 的 **`class`** 语法提供了一个更接近传统面向对象编程语言的定义方式，同时其底层仍然基于 JavaScript 的原型机制。

### 1. Class 的定义

**`class`** 是用来定义对象模板的语法，可以包含构造函数、方法和静态方法。

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} makes a sound.`);
  }
}

const animal = new Animal('Dog');
animal.speak();  // 输出: Dog makes a sound.
```

在这个例子中，我们定义了一个名为 `Animal` 的类，并通过 `constructor` 函数初始化类的属性。`speak` 是该类的一个方法，所有通过 `Animal` 类创建的实例都可以调用这个方法。

### 2. 类的继承

通过 `extends` 关键字可以实现类的继承。继承使得一个类可以获取另一个类的属性和方法，并可以通过子类进行扩展或重写。

```javascript
class Dog extends Animal {
  constructor(name, breed) {
    super(name);  // 调用父类的构造函数
    this.breed = breed;
  }

  speak() {
    console.log(`${this.name} barks.`);
  }
}

const dog = new Dog('Buddy', 'Golden Retriever');
dog.speak();  // 输出: Buddy barks.
```

这里的 `Dog` 类继承了 `Animal` 类，通过 `super` 调用父类的构造函数，继承了 `name` 属性。我们还重写了 `speak` 方法，使 `Dog` 类具有自己独特的行为。

### 3. 静态方法

使用 `static` 关键字，可以定义类的静态方法。静态方法属于类本身，而不是类的实例。

```javascript
class Utility {
  static calculateAge(birthYear) {
    return new Date().getFullYear() - birthYear;
  }
}

console.log(Utility.calculateAge(1990));  // 输出: 当前年份 - 1990
```

在这个例子中，`calculateAge` 是 `Utility` 类的静态方法，可以通过类本身调用，而不需要实例化对象。

## 二、Super

**`super`** 关键字用于调用父类的构造函数或方法。在继承关系中，子类需要调用父类的构造函数以确保父类的属性得以正确初始化。

### 1. 在构造函数中使用 `super`

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);  // 调用父类构造函数
    this.breed = breed;
  }
}

const dog = new Dog('Buddy', 'Golden Retriever');
console.log(dog.name);  // 输出: Buddy
console.log(dog.breed);  // 输出: Golden Retriever
```

在这个例子中，`Dog` 类继承自 `Animal` 类，`super(name)` 确保了 `name` 属性被父类的构造函数初始化。

### 2. 在方法中使用 `super`

除了在构造函数中，`super` 还可以用于调用父类中的方法。

```javascript
class Animal {
  speak() {
    console.log('Animal makes a sound.');
  }
}

class Dog extends Animal {
  speak() {
    super.speak();  // 调用父类的 speak 方法
    console.log('Dog barks.');
  }
}

const dog = new Dog();
dog.speak();
// 输出:
// Animal makes a sound.
// Dog barks.
```

在这个例子中，`super.speak()` 用于调用父类的 `speak` 方法，然后在子类 `Dog` 中追加自己的行为。

## 三、getPrototypeOf() 与 setPrototypeOf()

JavaScript 的原型机制是实现对象继承的核心，而 ES6 提供了 **`Object.getPrototypeOf()`** 和 **`Object.setPrototypeOf()`** 来操作对象的原型。

### 1. Object.getPrototypeOf()

**`Object.getPrototypeOf()`** 方法返回指定对象的原型（即其内部的 `[[Prototype]]`）。

```javascript
const obj = {};
const proto = Object.getPrototypeOf(obj);
console.log(proto);  // 输出: Object.prototype
```

这个方法可以让我们明确知道对象的原型是什么，它是对象继承关系中的一个重要概念。

### 使用场景

- **检查对象的继承关系**：通过 `Object.getPrototypeOf()`，可以验证某个对象是否继承自另一个对象。
  
  ```javascript
  class Animal {}
  const dog = new Animal();
  console.log(Object.getPrototypeOf(dog) === Animal.prototype);  // 输出: true
  ```

### 2. Object.setPrototypeOf()

**`Object.setPrototypeOf()`** 方法用于设置对象的原型。它允许我们动态地改变对象的继承关系。

```javascript
const obj = {};
const newProto = {
  greet() {
    console.log('Hello from new prototype!');
  }
};

Object.setPrototypeOf(obj, newProto);
obj.greet();  // 输出: Hello from new prototype!
```

通过 `Object.setPrototypeOf()`，我们可以将 `obj` 的原型动态修改为 `newProto`，从而使 `obj` 继承 `newProto` 的属性和方法。

### 使用场景

- **动态修改原型链**：在某些复杂场景中，可能需要在运行时动态修改对象的原型链来调整行为。
  
- **实现继承链的调整**：例如，在某些设计模式中，你可能希望在运行时改变对象的继承路径。

## 四、实际使用场景

### 1. 构建对象继承关系

通过 **`class`** 和 **`super`**，可以轻松实现类与类之间的继承关系。无论是用于创建面向对象的系统、游戏开发中的角色继承，还是在构建复杂的用户界面组件时，`class` 和 `super` 提供了清晰的结构化继承方式。

### 2. 原型链的动态操作

**`Object.getPrototypeOf()`** 和 **`Object.setPrototypeOf()`** 可以帮助开发者在运行时调整对象的继承关系。它们对于某些需要动态改变对象行为的场景特别有用，例如插件系统中动态扩展对象功能。

### 3. 静态方法的应用

静态方法特别适合定义与实例无关的工具函数。例如，常见的数学计算、日期格式化等都可以通过静态方法实现，从而避免创建冗余的对象实例。

## 五、总结

ES6 的 **`class`**、**`super`**、**`Object.getPrototypeOf()`** 和 **`Object.setPrototypeOf()`** 提供了一种更加直观和简洁的方式来处理 JavaScript 中的面向对象编程。类的继承和静态方法的使用极大简化了代码的可读性，而 `getPrototypeOf` 和 `setPrototypeOf` 则为动态原型链操作提供了灵活性。

理解并熟练使用这些特性，能够让我们在构建复杂应用程序时，编写出更加优雅、简洁和高效的代码。