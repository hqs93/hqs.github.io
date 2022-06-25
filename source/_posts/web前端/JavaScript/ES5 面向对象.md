---
title: ES5 面向对象
categories: 
- [web前端, JavaScript, ES5 面向对象]
---

# ES5 面向对象

<!-- more -->

# 类

> JS封装或者说是JS创建类，会有以下功能：

- 构造器
- 静态属性，静态方法
- 共有属性，共有方法
- 私有属性，私有方法

## 混合模式

```javascript
var Utils = (function () {
  function _utils () {
    var _this = this
    if (_this instanceof _utils) {
      Cookie.apply(_this, arguments)
      VerifyCode.apply(_this, arguments)
      Mixin.apply(_this, arguments)
    } else {
      return new _utils()
    }
  }
  _utils.prototype = {
    constructor: _utils
  }
  return _utils
})()
```

## 安全模式

```javascript
//利用闭包实现单例模式
var Person = (function(){
    //静态私有属性、方法
    var home = "China";
    function sayHome(name){}
    //构造函数,约定“_”开头首字母小写为构造函数
    function _person(name, age){
        var _this = this;
        //构造函数安全模式，避免创建时候丢掉new关键字
        if(_this instanceof _person){
            //共有属性、方法
            _this.name = name;
            _this.getHome = function(){
                //内部访问私有属性、方法
                sayHome(_this.name);
            };
            _this.test = sayHome; //用于测试
            //构造器
            _this.setAge = function(age){
                _this.age = age + 12;
            }(age);
        }else{
            return new _person(name, age);
        }
    }
    //静态共有属性、方法
    _person.prototype = {
      	//重新指向constructor构造函数
        constructor: _person,
        drink: "water",
        sayWord: function(){
            console.log("ys is a boy");
        }
    }
  	//返回类
    return _person;
})();
```

## 掺元类

```javascript
/*
*掺元类
*常用属性及方法
*/
var Mixin = (function () {
  // 静态私有
  var propsType = 'inlay'
  // 静态公共(常用方法、属性)
  _mixinInstance.prototype = {
    constructor: _mixin,
    propsType: propsType,
  // 构造函数,安全模式
  function _mixin () {
    var _this = this
    if (_this instanceof _mixin) {
      _this.propsType = propsType
    } else {
      return new _mixin()
    }
  }
  return _mixin
})()
```

***



# 设计模式

https://github.com/hqs93/JS-advance/tree/master/designPattern

***



# 继承

## 继承方式

### 定义父类

想要继承,就必须提供父类(继承谁,提供继承的属性和方法)

```javascript
function Person (name) {
	this.name = name
	this.sum = function () {
		console.log(this.name)
	}
}
Person.prototype.age = 10
```



### 1.原型链继承

- 重点：让新实例的原型等于父类的实例。
- 特点：1、实例可继承的属性有：实例的构造函数的属性，父类构造函数属性，父类原型的属性。（新实例不会继承父类实例的属性！）
- 缺点：1、新实例无法向父类构造函数传参。
  　　　2、继承单一。
        　　　3、所有新实例都会共享父类实例的属性。（原型上的属性是共享的，一个实例修改了原型属性，另一个实例的原型属性也会被修改！）

```javascript
function Per() {
	this.name = "key"
}
Per.rpototype = new Person()
var per = new Per()
console.log(per instanceof Person) // true
console.log(per.age) // 10
```



### 2.借用构造函数继承

- 重点：用.call()和.apply()将父类构造函数引入子类函数（在子类函数中做了父类函数的自执行（复制））　　　　
- 特点：1、只继承了父类构造函数的属性，没有继承父类原型的属性。
  　　　2、解决了原型链继承缺点1、2、3。
        　　　3、可以继承多个构造函数属性（call多个）。
        　　　4、在子实例中可向父实例传参。
- 缺点：1、只能继承父类构造函数的属性。
  　　　2、无法实现构造函数的复用。（每次用每次都要重新调用）
        　　　3、每个新实例都有父类构造函数的副本，臃肿。

```javascript
function Sub () {
  Person.call(this, 'jak')
  Person.apply(this, ['jak', 'hny', 'lik'])
  this.age = 12
}
var sub = new Sub()
console.log(sub instanceof Person) // false
console.log(sub.age) // 12
```



### 3.组合继承

- 重点：结合了两种模式的优点，传参和复用
- 特点：1、可以继承父类原型上的属性，可以传参，可复用。
  　　　2、每个新实例引入的构造函数属性是私有的。
- 缺点：调用了两次父类构造函数（耗内存），子类的构造函数会代替原型上的那个父类构造函数。

```javascript
function Sub () {
  Person.call(this, name) // 借用构造函数模式
}
Sub.prototype = new Person() // 原型链继承
var sub = new Sub('jar')
console.log(sub.name) // jar继承了构造函数属性
console.log(sub.age) // 10继承了父类原型的属性
```



### 4.原型式继承

- 重点：用一个函数包装一个对象，然后返回这个函数的调用，这个函数就变成了个可以随意增添属性的实例或对象。object.create()就是这个原理。
- 特点：类似于复制一个对象，用函数来包装。
- 缺点：1、所有实例都会继承原型上的属性。
  　　　2、无法实现复用。（新实例属性都是后面添加的）

```javascript
// 先封装一个函数容器,用来输出对象和承载继承的原型.
function extendHandle (subClass) {
  var Super = function () { }
  Super.prototype = subClass // 继承传入的参数
  return new Super() // 返回实例化对象
}
var person = new Person() // 实例化父类的实例
var sub = extendHandle(person)
console.log(sub.age) // 10继承了父类函数的属性
```



### 5.寄生式继承

- 重点：就是给原型式继承外面套了个壳子。
- 优点：没有创建自定义类型，因为只是套了个壳子返回对象（这个），这个函数顺理成章就成了创建的新对象。
- 缺点：没用到原型，无法复用。

```javascript
// 原型式继承
function extendHandle (subClass) {
  var Super = function () { }
  Super.prototype = subClass
  return new Super()
}
var person = new Person()
// 给原型式继承再套个壳子传递参数就是寄生式继承
function subExtend (obj) {
  var Super = extendHandle(obj)
  Super.name = 'js'
  return Super
}
var sub = subExtend(person)
console.log(sub.name) // js返回了Super对象,继承了Super的属性
```



### 6.寄生组合继承(常用)

- 寄生：在函数内返回对象然后调用

- 组合：1、函数的原型等于另一个实例。

  ​			2、在函数中用apply或者call引入另一个构造函数，可传参　

- 重点：修复了组合继承的问题

​	**继承这些知识点与其说是对象的继承，更像是函数的功能用法，如何用函数做到复用，组合，这些和使用继承的思考是一样的。上述几个继承的方法都可以手动修复他们的缺点，但就是多了这个手动修复就变成了另一种继承模式。
​	这些继承模式的学习重点是学它们的思想，不然你会在coding书本上的例子的时候，会觉得明明可以直接继承为什么还要搞这么麻烦。就像原型式继承它用函数复制了内部对象的一个副本，这样不仅可以继承内部对象的属性，还能把函数（对象，来源内部对象的返回）随意调用，给它们添加属性，改个参数就可以改变原型对象，而这些新增的属性也不会相互影响。**

```javascript
// 寄生
function extendHandle (obj) {
  var Super = function () {}
  Super.prototype = obj
  return new Super()
}
// 组合
function Sub (obj) {
  Person.call(this, obj) // call传入每一项
  Person.apply(this, arguments) // apply传入伪数组
}
var person = extendHandle(Person.prototype) // 实例化 
Sub.prototype = person // 继承
person.constructor = Sub // 重新指向构造函数,修复实例
var sub = new Sub()
```

```javascript
/* 原型式继承 */
function inheritObject (o) {
  function F () {}
  F.prototype = o
  return new F()
}
/* 
*寄生式继承
*subClass 子类
*superClass 父类
*/
function inheritPrototype (subClass, superClass) {
  var p = inheritObject(superClass.prototype)
  p.constructor = subClass
  subClass.prototype = p
}
```

- 推荐的方式,ES新增了Object.create()方法来替代原型式继承,更简洁

```javascript
/* 
*寄生组合式继承
*subClass 子类
*superClass 父类
*/
function inheritPrototype (subClass, superClass) {
  var parent = Object.create(superClass.prototype)
  parent.constructor = subClass
  subClass.prototype = parent
}
```



## 继承封装

#### 1.单一继承(继承方法、属性)

```javascript
/* 
*方式一(推荐),Object.create()继承,默认接受2个参数,之后的参数必须为类
*Object.create()方法实现了对实例属性的隔离,更加安全,当被继承的类发生原型上的属性、方法修改时,不会对父类、兄弟类产生影响
*subClass 子类
*superClass 父类
*/
function extend (subClass, superClass) {
  if (arguments.length === 2) {
    // 单一继承的场景下,setPrototypeOf附加原型链比create更适合,不用再去指向constructor
    Object.setPrototypeOf(subClass.prototype, superClass.prototype)
    // subClass.prototype = Object.create(superClass.prototype)
    // subClass.prototype.constructor = subClass // 重新指向constructor
  } else {
    console.error('只接收2个参数!')
  }
}
/* 方式二 手动继承 */
function extend1 (subClass, superClass) {
	var Super = function () { }
  Super.prototype = superClass.prototype
  subClass.prototype = new Super()
  subClass.prototype.constructor = subClass
  if (subClass.prototype.constructor === Object.prototype.constructor){
  	superClass.prototype.constructor = superClass
	}
}
```

#### 2.多重继承(JS本质上是不支持多重继承的)

```javascript
/*
*多重继承
*subClass: 子类
*superClass: 父类
*超过2个参数必须为类
*/
function extends (subClass, superClass) {
  if (arguments.length > 2) {
    for (var i = 1; i < arguments.length; i++) {
      if (arguments[i] instanceof Object && arguments[i].prototype) {
        Object.assign(subClass.prototype, arguments[i].prototype)
      } else {
        console.error('参数' + (i + 1) + '需要引用类型')
      }
    }
  } else {
    Object.assign(subClass.prototype, superClass.prototype)
  }
  subClass.prototype.constructor = subClass
}
```

#### 3.选择继承

```javascript
/*
*选择继承,默认接受2个参数,后面的参数都是方法或者属性名
*receivingClass 被扩充类
*givingClass 扩充类(掺元类)
*超过2个参数为方法名或属性,可选择性继承扩充类的指定属性和方法
*/
function receiving (receivingClass, givingClass) {
	if (arguments[2]) {
    // 选择继承
    for (var i = 2; i < arguments.length; i++) {
      if (Object.prototype.toString.call(arguments[i]) === '[object String]') {
        receivingClass.prototype[arguments[i]] = givingClass.prototype[arguments[i]]
      } else {
        console.error('参数' + (i - 1) + '需要字符串类型!')
      }
    }
  } else {
    // 多继承
    Object.assign(receivingClass.prototype, givingClass.prototype)
  }
  receivingClass.prototype.constructor = receivingClass
}

```

