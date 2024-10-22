---
title: 原型 & 原型链
author: zeo
date: 2024-7-18
categories: [知识点, JavaScript, 原型 & 原型链]
tags: [JavaScript, ES6, 原型, 原型链, prototype, __proto__]
render_with_liquid: false
---

# 理解“原型 & 原型链”

在JavaScript中，**原型（Prototype）**和**原型链（Prototype Chain）**是理解面向对象编程的核心概念。它们提供了对象继承机制，使得对象能够共享属性和方法。尽管原型机制在ES5就已经存在，ES6对其进行了优化和增强，使得类的语法更加简洁明了。

本文将详细介绍JavaScript中的原型和原型链，并通过示例展示它们的使用场景与工作原理。

## 什么是原型（Prototype）？

在JavaScript中，每个对象都有一个隐藏的属性，称为`[[Prototype]]`，这个属性指向另一个对象。这个对象就是该对象的**原型**，它是对象继承属性和方法的来源。通过这种机制，一个对象可以访问其原型链上的属性和方法，而不必在自身定义这些属性或方法。

### 原型的核心概念

- **构造函数与原型**：每个构造函数都有一个名为`prototype`的属性，该属性指向一个对象，而这个对象就是通过该构造函数创建的实例对象的原型。
- **实例对象的`__proto__`属性**：每个实例对象都有一个`__proto__`属性，指向其构造函数的`prototype`对象。
- **共享属性与方法**：通过原型，多个对象可以共享同一个方法或属性，而不必在每个实例中重复定义。

### 一个简单的例子

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  return `Hello, my name is ${this.name}`;
};

const person1 = new Person('Alice');
const person2 = new Person('Bob');

console.log(person1.greet());  // 输出: Hello, my name is Alice
console.log(person2.greet());  // 输出: Hello, my name is Bob
```

在这个例子中，`greet`方法被定义在`Person.prototype`上，因此所有通过`Person`构造函数创建的实例（`person1`和`person2`）都共享这一方法。

## 什么是原型链（Prototype Chain）？

当试图访问一个对象的属性时，JavaScript引擎首先检查该对象本身是否有该属性。如果没有，它会沿着**原型链**向上查找，直到找到该属性或到达原型链的末端（`null`）。

原型链是对象继承的核心机制，允许对象通过其原型对象访问和继承其他对象的属性和方法。

### 原型链的工作原理

1. 当访问对象的某个属性时，JavaScript引擎首先检查该对象自身是否拥有该属性（即对象的自有属性）。
2. 如果没有找到该属性，JavaScript会检查对象的`__proto__`，即对象的原型对象。
3. 如果在原型对象中找到了该属性，则返回该属性值；否则，继续沿着原型链向上查找。
4. 原型链的尽头是`Object.prototype`，它的`__proto__`为`null`，表示查找的终止点。

### 原型链的例子

```javascript
function Animal() {
  this.type = 'Animal';
}

Animal.prototype.makeSound = function() {
  console.log('Animal sound');
};

function Dog(name) {
  this.name = name;
}

// 继承Animal
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function() {
  console.log('Woof! Woof!');
};

const dog = new Dog('Buddy');

console.log(dog.name);      // 输出: Buddy
dog.bark();                 // 输出: Woof! Woof!
dog.makeSound();            // 输出: Animal sound
console.log(dog.type);      // 输出: Animal
```

在这个例子中，`Dog`对象继承了`Animal`的属性和方法，通过`Object.create()`创建了一个新的原型链。`dog`实例不仅可以访问自己的属性`name`，还可以访问继承自`Animal`的`makeSound`方法和`type`属性。这展示了原型链如何工作以及如何实现继承。

## ES6中的原型和类

在ES6之前，JavaScript是通过构造函数和原型链来实现继承的，而ES6引入了更加符合传统面向对象编程概念的**类（class）**语法。尽管如此，ES6的类实际上仍然基于原型机制工作。

### ES6类语法示例

```javascript
class Animal {
  constructor(type) {
    this.type = type;
  }

  makeSound() {
    console.log(`${this.type} makes a sound.`);
  }
}

class Dog extends Animal {
  constructor(name) {
    super('Dog');
    this.name = name;
  }

  bark() {
    console.log(`${this.name} barks: Woof! Woof!`);
  }
}

const dog = new Dog('Buddy');

console.log(dog.name);      // 输出: Buddy
dog.bark();                 // 输出: Buddy barks: Woof! Woof!
dog.makeSound();            // 输出: Dog makes a sound.
```

在这个例子中，我们通过`class`语法定义了`Animal`和`Dog`类。`Dog`类通过`extends`关键字继承了`Animal`类，而`super()`用于调用父类的构造函数。尽管语法看起来更接近其他面向对象语言，背后仍然使用原型链来实现继承。

## 使用原型的常见场景

### 1. **共享方法**
   当多个实例需要共享相同的方法时，可以将方法定义在构造函数的`prototype`上。这样，所有实例可以共享该方法，而不是在每个实例中都创建一个新的方法副本。

   ```javascript
   function Car(brand) {
     this.brand = brand;
   }

   Car.prototype.drive = function() {
     console.log(`${this.brand} is driving.`);
   };

   const car1 = new Car('Toyota');
   const car2 = new Car('Honda');

   car1.drive();  // 输出: Toyota is driving.
   car2.drive();  // 输出: Honda is driving.
   ```

### 2. **继承和多态**
   通过原型链，可以实现对象之间的继承和方法的重写（多态）。在这种情况下，子类可以继承父类的方法和属性，并根据需要进行自定义扩展。

   ```javascript
   function Animal() {}
   Animal.prototype.makeSound = function() {
     console.log('Generic animal sound');
   };

   function Cat() {}
   Cat.prototype = Object.create(Animal.prototype);
   Cat.prototype.constructor = Cat;

   Cat.prototype.makeSound = function() {
     console.log('Meow');
   };

   const cat = new Cat();
   cat.makeSound();  // 输出: Meow
   ```

### 3. **内存优化**
   如果方法定义在对象本身而不是原型上，那么每创建一个实例都会生成该方法的副本。将方法定义在原型上可以减少内存的占用，因为所有实例共享相同的方法定义。

## 总结

**原型和原型链**是JavaScript实现对象继承的核心机制，通过这种机制，不同的对象可以共享方法和属性。ES6引入了类语法，使得继承的定义更加直观，但其底层仍然依赖原型链。通过理解和掌握原型机制，开发者可以编写更加高效和灵活的代码，特别是在构建大型应用和需要对象继承时。

无论是共享方法、内存优化还是对象继承，原型机制在JavaScript中无处不在，是前端开发工程师不可忽视的重要概念。