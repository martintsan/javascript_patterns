# 面向对象的重要概念

本章主要叙述面向对象的重要概念在 JavaScript 中的实现方法

## 继承

由于 JavaScript 语言本身不具备继承的机制，因此其继承往往是通过原型链来实现，代码如下：

```javascript
// 父类 Animal
function Animal() {}
// 将被继承的方法
Animal.prototype.yelp = function () {
  return '我会叫';
};

function Cat() {}
Cat.prototype = new Animal();

var cat = new Cat();
var result = cat.yelp(); // 我会叫
```

`URL` <https://jsfiddle.net/Martin_nett/t1uq4fyb/3/>

从以上代码可以看出，`Cat` 并没有 `yelp` 方法，该方法是通过指定了原型为 `Animal` 来继承得到的。其过程为 `Cat` 首先查找自身有没有 `yelp` 方法，如果没有则到其原型中查找，由于这时它的原型为 `Animal` ，而 `Animal` 正好拥有 `yelp` 方法，从而程序将执行该方法。JavaScript 通常以此实现方法来模拟面向对象的继承机制。

## 多态

关于多态性，我们先来看一段代码：

```javascript
function Cat() {}
function Dog() {}
function AnimalSound() {}
AnimalSound.prototype.sound = function (animal) {
  if (animal instanceof Cat) {
    return '喵~';
  }else if (animal instanceof Dog) {
    return '汪~';
  }
};

var as = new AnimalSound();
as.sound(new Cat()); // 喵~
as.sound(new Dog()); // 汪~
```

以上这段代码是一种多态的实现方法，当调用 `sound` 方法时，它确实能根据不同类型的对象，发出不同的叫声，但这样的实现方法的缺点在于不能提供很好地可扩展性，如果需要在添加一种动物的叫声，我们不得不修改 `sound` 方法。

**更好的方法是：**

找到业务中「不变的」部分以及「可变的」部分，这里「不变的」是动物会叫这件事本身，而哪种动物会叫，以及怎么叫是「可变的」。我们需要把「不变的」部分剥离出来，将「可变的」部分封装起来，这样就能更好地提供程序的可扩展性，同时也更符合开放-封闭原则。修改后的代码如下：

```javascript
// 父类 Animal
// 利用父类抽象出动物会叫这个不变的事实
function Animal() {}
// 将被继承的方法
Animal.prototype.yelp = function () {};

// 哪种动物以及分别怎么叫，则交给子类实现
function Cat() {}
Cat.prototype = new Animal();
Cat.prototype.yelp = function () {
  return "喵~";
};

function Dog() {}
Dog.prototype = new Animal();
Dog.prototype.yelp = function() {
  return "汪~";
}

function AnimalSound() {}
AnimalSound.prototype.sound = function (animal) {
  // 调用时封装抽象层的叫方法，
  // 以适应所有子类的叫方法，从而体现多态
  if (animal instanceof Animal) {
    return animal.yelp();
  }
};

var as = new AnimalSound();
as.sound(new Cat()); // 喵~
as.sound(new Dog()); // 汪~
```

`URL` <https://jsfiddle.net/Martin_nett/20g1bras/1/>

## 封装

封装往往指的是隐藏数据及实现细节，所谓隐藏数据，更直观的就是诸如 `public` `private` 及 `protected` 的关键字，然而 JavaScript 并未提供这样的支持，因此我们只能利用变量的作用域来模拟 `public` 及 `private` 这两种数据封装特性（无法模拟 `protected`）

```javascript
var man = (function() {
  var _name = 'martin'; // 私有变量
  return {
    getName: function() { // 公开方法
      return _name;
    }
  }
})();

console.log(man.getName()); // martin
console.log(man._name); // undefined
```
