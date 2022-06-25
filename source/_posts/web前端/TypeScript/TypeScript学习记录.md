---
title: TypeScript学习记录
categories: 
- [web前端, TypeScript, TypeScript学习记录]
---

## TypeScript

<!-- more -->

### 1.简介

​	1.以JavaScript为基础构建的语言.

​	2.一个JavaScript的超集.

​	3.可以在任何支持JavaScript的平台中执行.

​	4.TypeScript扩展了JavaScript并添加了类型.

​	5.TS不能被JS解析器直接执行.



### 2.TS新增了什么

​	1.类型.

​	2.支持ES的新特性.

​	3.添加ES不具备的新特性.

​	4.丰富的配置选项.

​	5.强大的开发工具.

***



## 一.环境与工具准备

### 1.安装node环境.



### 2.安装typescript包

```javascript
npm install -g typescript
```



### 3.编译工具ts-node

ts可以编译为任意版本的js.

```ruby
npm install -g ts-node
#执行命令
tsc xxx.ts
```



### 4.断点调试配置

```json
{
  // 使用 IntelliSense 了解相关属性。 
  // 悬停以查看现有属性的描述。
  // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "TS debugger",
      "runtimeArgs": [
        "-r",
        "ts-node/register"
      ],
      "args": [
        "${workspaceFolder}/a.ts"
      ]
    }
  ]
}
```



***



## 二.基础

### 1.变量

存储数据的容器



### 2.类型注解

是一种为变量添加类型约束的方式,如果变量的声明和赋值是同时进行的,TS可以自动对变量进行类型检测.



### 3.命名规则和规范

变量命名尽量遵守驼峰方法、纯字母组成、变量名称要有意义.

TS中声明变量是严格区分大小写的.



### 4.类型

|  类型   | 例子                      | 描述                                                         |
| :-----: | :------------------------ | ------------------------------------------------------------ |
| number  | 1,-33,2.5                 | 任意数字                                                     |
| string  | 'hello'                   | 任意字符串                                                   |
| boolean | true,false                | 布尔值                                                       |
| 字面量  | 本身                      | 限制变量的值就是该字面量的值                                 |
|   any   | *                         | 任意类型,关闭类型检测                                        |
| unknown | *                         | 类型安全的any,表示未知类型的值,不会关闭类型检测,有提示可以编译成功. |
|  void   | 空值(undefined)           | 没有值(或undefined),常用于函数返回值                         |
|  never  | 没有值                    | 不能是任何值,永远不会返回结果,用于报错函数                   |
| object  | {age: 20}                 | 任意js对象,2种语法<br />1. let a: {} 用来指定对象中可以包含哪些属性<br />2. let b: onject 可以被用来直接指定对象,属性任意. |
|  array  | [1,2,3]                   | 任意js数组                                                   |
|  tuple  | [4,5]                     | 元组,TS新增类型,固定长度数组                                 |
|  enum   | enum(A,B)                 | 枚举,TS新增类型                                              |
|   \|    | let a: boolean \| string; | 联合类型,只能是定义的2种或多种类型(字面量)的其中一种.        |
|  type   | type myType = 1 \| 2 \| 3 | 类型别名                                                     |

语法:

```typescript
let a: number = 123;
let b: string = 'hello';
let c: boolean = true;
let d: any = 111;
let e: unknown = '111';
let f: (param: string): void;
let g: (param: string): never => {
  throw '错误'
};

// 对象2种写法
let h1: object;
let h2: {};
// 属性后面加上?,表示属性可选
let h3: {name: string, age?: number};
h3 = {name: '小明', age: 20}
// [propName: string]: any 表示任意类型的属性
let h4: {name: string, [propName: string]: any};
h4 = {name: '小明', age: 20, gender: '男'} // 不会报错

// 数组2种写法
let i1: string[];
let i2: Array<string>;

// 元组,固定长度的数组
let j: [string, string, number];


// 枚举
enum Gender {Male, Female}
let k: {name: string, gender: Gender}
console.log(k.gender === Gender.Male)

// 联合类型
let l1: string | number; // |符号用来判断是否是其中一个类型
let l2: {name: string} & {age: number}; // &符号不会用在基本类型上因为不会存在两种不同类型的值,但是可以用来判断对象中必须有哪些属性

// 类型别名
type myType1 = string
type myType2 = string | number | boolean
let m1: myType1;
let m2: myType2
```



### 5.运算符

算术运算符、赋值运算符、递增/递减运算符、比较运算符、逻辑运算符



### 6.数组

语法: 

```typescript
// 语法一:
let names: string[] = ['js', 'html', 'css']

// 语法二:
let names: string[] = new Array('js', 'html', 'css')
```



### 7.对象

注意: 这样的写法会造成结构复杂,阅读困难,不够简洁,不能复用,**推荐使用接口来作为对象的类型注解**

语法: 

```typescript
let person: {
	name: string
	age: number
  sayHi: () => void
  sing: (name: string) => void
  sum: (num1: number, num2: number) => number
} = {
  name: 'hqs',
  age: 22,
  sayHi: () => {
    console.log(123)
  },
  sing: (name) => {
    console.log(name)
  },
  sum: (num1, num2) => num1 + num2
}
```



### 8.接口

用来定义一个类或对象的结构,其实就是定义了一个标准或规范.

接口可重复定义,可同名,并且同名接口取合集.

语法: 

```typescript
// 定义一个接口
interface userInfo {
	name: string
	age: number
}

// 使用接口
let person: userInfo = {
  name: 'hqs',
  age: 22,
}
```

使用类实现接口

```typescript
// 使用接口
interface userInfo {
	name: string
	fn (): void
}
class MyClass implements userInfo {
  name: string
  constructor (name: string) {
    this.name = name
  }
  fn () {
    console.log('类实现接口')
  }
}
```



### 9.断言

类型断言,用来手动指定更加具体的类型(比如,比Element类型更加具体的)

技巧: 获取dom元素后,通过console.dir()来得到断言类型.

语法: 

```typescript
// as 语法
let img = document.querySelector('#image') as HTMLImageElement

// <> 语法
let img = <HTMLImageElement>document.querySelector('#image')
```



### 10.枚举

使用场景: 当变量的值,只能是几个固定值中的一个,应该使用枚举类型来实现.

枚举: 枚举是一种类型,因此可以将其作为变量的类型注,解.**枚举是一组有名字的常量(只读)的集合.** **JavaScript中是没有枚举的,typescript中加入枚举来弥补js中的缺陷.**

注意: 枚举成员是只读的,只能访问不能赋值.枚举成员是有值的,默认从0开始自增的数值.

**数字枚举**: 枚举成员的值为数值类型

```typescript
enum Gender { Female, Male }
enum Player { X, O }
// 枚举初始化值
enum NewGender { Female = 1, Male = 100 }
// 使用枚举作为类型注解
let userInfo: Gender
```



**字符串枚举**: 枚举成员的值是字符串,**字符串枚举没有自增行为,因此每个成员必须有初始值.**

```typescript
enum Gender { Female = '女', Male = '男' }
```



### 11.范型

使用场景: 在定义函数或是类时,如果遇到类型不明确就可以使用范型.

```typescript
// 一个范型
function fn<T>(a: T): T{
	return a
}
// 多个范型
function fn1<T, K>(a: T, b: K): T{
	return a
}
// 可以直接调用具有范型的函数
fn(10) // 不指定范型,TS可以自动对类型进行推断
fn<string, number>('hello', 123) // 通过断言指定范型
```

范型实现类(子类)

```typescript
interface Init {
	length: number
}
function fn1<T extends Init>(a: T): number{
	return a
}
```



### 12.类

类有两种属性或方法:

实例属性(方法): 直接定义的属性是实例属性,需要通过对象的实例去访问. per.age\per.fn()

静态属性(方法): 使用static开头的属性是静态属性(类属性),可以直接通过类去访问 Person.type\Person.fn1()

```typescript
class Person {
  // 构造函数在对象创建时调用,在new创建对象时constructor立即执行
  constructor () {
    // this表示当前实例
    console.log(this)
  }
	name: string = 'hqs';
	age: number = 18;
  // 静态属性
	static type: string = 'typescript';
  // readonly表示只读属性,不能修改
	readonly about: string = 'typescript学习记录';
  // 可以连写,表示只读的静态属性
  static readonly aouthor: string = 'hqs';
  fn () {}
  static fn1 () {}
  static readonly fn2 () {}
}
const per = new Person()
```

#### 继承

多个子类可以通过extends继承父类(共同的属性和方法),super表示继承的父类.

注意: 如果子类中写了构造函数,在子类构造函数中必须调用父类函数,并且传入父类函数必须要的参数

```typescript
// 父类
class Animal {
  name: string
  age: number
  constructor (name: string, age: number) {
    this.name = name
    this.age = age
  }
  fn () {
    console.log('动物类')
  }
}
class Cat extends Animal{
  fn () {
    console.log('喵喵喵')
  }
}
class Dog extends Animal{
  fn () {
    console.log('汪汪汪')
  }
}
class Dog1 extends Animal{
  type: string
  constructor (name: string, age: number, type: string) {
    this.super(name, age)
    this.type = type
  }
}
const cat = new Cat('小黄', 2)
const dog = new Dog('小黑', 3)
const dog1 = new Dog1('小白', 6, 'dog')
```

#### 抽象类

场景: 以**abstract**开头的类或类方法,当我们创建了父类(超类),我们只希望这个类被继承,不希望这个类被创建.

抽象类: 只能被继承不能创建.

抽象方法: 抽象方法没有方法体,在typescript中返回void类型,抽象方法只能定义在抽象类中且子类必须对抽象方法进行重写

```typescript
// 抽象类
abstract class Animal {
  name: string
  constructor (name: string, age: number) {
    this.name = name
  }
  // 抽象方法
  abstract fn (): void
}
class Cat extends Animal{
  fn () {
    console.log('喵喵喵')
  }
}
class Dog extends Animal{
  fn () {
    console.log('汪汪汪')
  }
}
const cat = new Cat('小黄', 2)
const dog = new Dog('小黑', 3)
```

#### 修饰符

public: 默认修饰符,公共属性,可在任意位置访问(修改)默认值.

private: 私有属性,外部不可访问,可以通过定义类方法来暴露私有属性.

readonly: 只读属性

get/set: 存取器,get读取,set写入.

abstract: 抽象类

static: 静态属性

#### 语法糖

可以直接将属性定义在构造函数中.

```typescript
class Animal {
  constructor (
  	public name: string, 
    public age: number) {}
}
const animal = new Animal('hqs', 22)
```

***

## 三.高级类型

### 交叉类型(&)

交叉类型是将多个类型合并为一个类型。 这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性。

语法：T & U

其返回类型既要符合 T 类型也要符合 U 类型

用法：假设有两个接口：一个是 Ant 蚂蚁接口，一个是 Fly 飞翔接口，现在有一只会飞的蚂蚁：

```typescript
interface Ant {
    name: string;
    weight: number;
}
interface Fly {
    flyHeight: number;
    speed: number;
}
// 少了任何一个属性都会报错
const flyAnt: Ant & Fly = {
    name: '蚂蚁呀嘿',
    weight: 0.2,
    flyHeight: 20,
    speed: 1,
};
```



### 联合类型(|)

联合类型与交叉类型很有关联，但是使用上却完全不同。

语法：T | U

其返回类型为连接的多个类型中的任意一个

用法：假设声明一个数据，既可以是 string 类型，也可以是 

```typescript
number 类型
let stringOrNumber： string | number = 0
stringOrNumber = ''
```

再看下面这个例子，start 函数的参数类型既是 Bird | Fish，那么在 start 函数中，想要直接调用的话，只能调用 Bird 和 Fish 都具备的方法，否则编译会报错

```typescript
class Bird {
    fly() {
        console.log('Bird flying');
    }
    layEggs() {
        console.log('Bird layEggs');
    }
}
class Fish {
    swim() {
        console.log('Fish swimming');
    }
    layEggs() {
        console.log('Fish layEggs');
    }
}
const bird = new Bird();
const fish = new Fish();

function start(pet: Bird | Fish) {
    // 调用 layEggs 没问题，因为 Bird 或者 Fish 都有 layEggs 方法
    pet.layEggs();

    // 会报错：Property 'fly' does not exist on type 'Bird | Fish'
    // pet.fly();

    // 会报错：Property 'swim' does not exist on type 'Bird | Fish'
    // pet.swim();
}

start(bird);
start(fish);
```



## 四.关键字

### 类型约束(extends)

语法：T extends K

这里的 extends 不是类、接口的继承，而是对于类型的判断和约束，意思是判断 T 能否赋值给 K

可以在泛型中对传入的类型进行约束

```typescript
const copy = (value: string | number): string | number => value
// 只能传入 string 或者 number
copy(10)
// 会报错：Argument of type 'boolean' is not assignable to parameter of type 'string | number'
// copy(false)
```

也可以判断 T 是否可以赋值给 U，可以的话返回 T，否则返回 never

```typescript
type Exclude<T, U> = T extends U ? T : never;
```



### 类型映射(in)

会遍历指定接口的 key 或者是遍历联合类型

```typescript
interface Person {
    name: string
    age: number
    gender: number
}

// 将 T 的所有属性转换为只读类型
type ReadOnlyType<T> = {
    readonly [P in keyof T]: T
}

// type ReadOnlyPerson = {
//     readonly name: Person;
//     readonly age: Person;
//     readonly gender: Person;
// }
type ReadOnlyPerson = ReadOnlyType<Person>
```



### 类型谓词(is)

语法：parameterName is Type

parameterName 必须是来自于当前函数签名里的一个参数名，判断 parameterName 是否是 Type 类型。

具体的应用场景可以跟着下面的代码思路进行使用：

看完联合类型的例子后，可能会考虑：如果想要在 start 函数中，根据情况去调用 Bird 的 fly 方法和 Fish 的 swim 方法，该如何操作呢？

首先想到的可能是直接检查成员是否存在，然后进行调用：

```typescript
function start(pet: Bird | Fish) {
    // 调用 layEggs 没问题，因为 Bird 或者 Fish 都有 layEggs 方法
    pet.layEggs();
    if ((pet as Bird).fly) {
        (pet as Bird).fly();
    } else if ((pet as Fish).swim) {
        (pet as Fish).swim();
    }
}
```

但是这样做，判断以及调用的时候都要进行类型转换，未免有些麻烦，可能会想到写个工具函数判断下：

```typescript
function isBird(bird: Bird | Fish): boolean {
    return !!(bird as Bird).fly;
}

function isFish(fish: Bird | Fish): boolean {
    return !!(fish as Fish).swim;
}

function start(pet: Bird | Fish) {
    // 调用 layEggs 没问题，因为 Bird 或者 Fish 都有 layEggs 方法
    pet.layEggs();

    if (isBird(pet)) {
        (pet as Bird).fly();
    } else if (isFish(pet)) {
        (pet as Fish).swim();
    }
}
```

看起来简洁了一点，但是调用方法的时候，还是要进行类型转换才可以，否则还是会报错，那有什么好的办法，能让我们判断完类型之后，就可以直接调用方法，不用再进行类型转换呢？

OK，肯定是有的，类型谓词 is 就派上用场了

用法：

```typescript
function isBird(bird: Bird | Fish): bird is Bird {
    return !!(bird as Bird).fly
}

function start(pet: Bird | Fish) {
    // 调用 layEggs 没问题，因为 Bird 或者 Fish 都有 layEggs 方法
    pet.layEggs();

    if (isBird(pet)) {
        pet.fly();
    } else {
        pet.swim();
    }
};
```

每当使用一些变量调用 isFish 时，TypeScript 会将变量缩减为那个具体的类型，只要这个类型与变量的原始类型是兼容的。

TypeScript 不仅知道在 if 分支里 pet 是 Fish 类型； 它还清楚在 else 分支里，一定不是 Fish 类型，一定是 Bird 类型



### 待推断类型(infer)

可以用 infer P 来标记一个泛型，表示这个泛型是一个待推断的类型，并且可以直接使用

比如下面这个获取函数参数类型的例子：

```typescript
type ParamType<T> = T extends (param: infer P) => any ? P : T;

type FunctionType = (value: number) => boolean

type Param = ParamType<FunctionType>;   // type Param = number

type OtherParam = ParamType<symbol>;   // type Param = symbol
```

判断 T 是否能赋值给 (param: infer P) => any，并且将参数推断为泛型 P，如果可以赋值，则返回参数类型 P，否则返回传入的类型

再来一个获取函数返回类型的例子：

```typescript
type ReturnValueType<T> = T extends (param: any) => infer U ? U : T;

type FunctionType = (value: number) => boolean

type Return = ReturnValueType<FunctionType>;   // type Return = boolean

type OtherReturn = ReturnValueType<number>;   // type OtherReturn = number
```

判断 T 是否能赋值给 (param: any) => infer U，并且将返回值类型推断为泛型 U，如果可以赋值，则返回返回值类型 P，否则返回传入的类型



### 原始类型保护(typeof)

语法：typeof v === "typename" 或 typeof v !== "typename"
用来判断数据的类型是否是某个原始类型（number、string、boolean、symbol）并进行类型保护

"typename"必须是 “number”， “string”， "boolean"或 “symbol”。 但是 TypeScript 并不会阻止你与其它字符串比较，语言不会把那些表达式识别为类型保护。

看下面这个例子， print 函数会根据参数类型打印不同的结果，那如何判断参数是 string 还是 number 呢？

```typescript
function print(value: number | string) {
    // 如果是 string 类型
    // console.log(value.split('').join(', '))

    // 如果是 number 类型
    // console.log(value.toFixed(2))
};
```

有两种常用的判断方式：

根据是否包含 split 属性判断是 string 类型，是否包含 toFixed 方法判断是 number 类型

弊端：不论是判断还是调用都要进行类型转换

使用类型谓词 is

弊端：每次都要去写一个工具函数，太麻烦了

用法：这就到了 typeof 一展身手的时候了

```typescript
function print(value: number | string) {
    if (typeof value === 'string') {
        console.log(value.split('').join(', '))
    } else {
        console.log(value.toFixed(2))
    }
}
```

使用 typeof 进行类型判断后，TypeScript 会将变量缩减为那个具体的类型，只要这个类型与变量的原始类型是兼容的。



### 类型保护(instanceof)

与 typeof 类似，不过作用方式不同，instanceof 类型保护是通过构造函数来细化类型的一种方式。

instanceof 的右侧要求是一个构造函数，TypeScript 将细化为：

此构造函数的 prototype 属性的类型，如果它的类型不为 any 的话
构造签名所返回的类型的联合
还是以 类型谓词 is 示例中的代码做演示：

最初代码：

```typescript
function start(pet: Bird | Fish) {
    // 调用 layEggs 没问题，因为 Bird 或者 Fish 都有 layEggs 方法
    pet.layEggs();

    if ((pet as Bird).fly) {
        (pet as Bird).fly();
    } else if ((pet as Fish).swim) {
        (pet as Fish).swim();
    }
};
```

使用 instanceof 后的代码：

```typescript
function start(pet: Bird | Fish) {
    // 调用 layEggs 没问题，因为 Bird 或者 Fish 都有 layEggs 方法
    pet.layEggs();

    if (pet instanceof Bird) {
        pet.fly();
    } else {
        pet.swim();
    }
};
```

可以达到相同的效果



### 索引类型查询操作符(keyof)

语法：keyof T
对于任何类型 T， keyof T 的结果为 T 上已知的 公共属性名 的 联合

```typescript
interface Person {
    name: string;
    age: number;
}

type PersonProps = keyof Person; // 'name' | 'age'
```

这里，keyof Person 返回的类型和 ‘name’ | ‘age’ 联合类型是一样，完全可以互相替换

用法：keyof 只能返回类型上已知的 公共属性名

```typescript
class Animal {
    type: string;
    weight: number;
    private speed: number;
}

type AnimalProps = keyof Animal; // "type" | "weight"
```

例如我们经常会获取对象的某个属性值，但是不确定是哪个属性，这个时候可以使用 extends 配合 typeof 对属性名进行限制，限制传入的参数只能是对象的属性名

```typescript
const person = {
    name: 'Jack',
    age: 20
}

function getPersonValue<T extends keyof typeof person>(fieldName: keyof typeof person) {
    return person[fieldName]
}

const nameValue = getPersonValue('name')
const ageValue = getPersonValue('age')

// 会报错：Argument of type '"gender"' is not assignable to parameter of type '"name" | "age"'
// getPersonValue('gender')
```



### 索引访问操作符(T[K])

语法：T[K]
类似于 js 中使用对象索引的方式，只不过 js 中是返回对象属性的值，而在 ts 中返回的是 T 对应属性 P 的类型

用法：

```typescript
interface Person {
    name: string
    age: number
    weight: number | string
    gender: 'man' | 'women'
}

type NameType = Person['name']  // string

type WeightType = Person['weight']  // string | number

type GenderType = Person['gender']  // "man" | "women"
```

## 五.映射类型

### 只读类型(Readonly<T>)

定义：

```typescript
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
}
```

用于将 T 类型的所有属性设置为只读状态。

用法：

```typescript
interface Person {
    name: string
    age: number
}

const person: Readonly<Person> = {
    name: 'Lucy',
    age: 22
}

// 会报错：Cannot assign to 'name' because it is a read-only property
person.name = 'Lily'
```

readonly 只读， 被 readonly 标记的属性只能在声明时或类的构造函数中赋值，之后将不可改（即只读属性）



### 只读数组(ReadonlyArray<T>)

定义：

```typescript
interface ReadonlyArray<T> {
    /** Iterator of values in the array. */
    [Symbol.iterator](): IterableIterator<T>;

    /**
     * Returns an iterable of key, value pairs for every entry in the array
     */
    entries(): IterableIterator<[number, T]>;

    /**
     * Returns an iterable of keys in the array
     */
    keys(): IterableIterator<number>;

    /**
     * Returns an iterable of values in the array
     */
    values(): IterableIterator<T>;
};
```

只能在数组初始化时为变量赋值，之后数组无法修改

使用：

```typescript
interface Person {
    name: string
}

const personList: ReadonlyArray<Person> = [{ name: 'Jack' }, { name: 'Rose' }]

// 会报错：Property 'push' does not exist on type 'readonly Person[]'
// personList.push({ name: 'Lucy' })

// 但是内部元素如果是引用类型，元素自身是可以进行修改的
personList[0].name = 'Lily'
```



### 可选类型(Partial<T>)

用于将 T 类型的所有属性设置为可选状态，首先通过 keyof T，取出类型 T 的所有属性，
然后通过 in 操作符进行遍历，最后在属性后加上 ?，将属性变为可选属性。

定义：

```typescript
type Partial<T> = {
    [P in keyof T]?: T[P];
}
```

用法：

```typescript
interface Person {
    name: string
    age: number
}
// 会报错：Type '{}' is missing the following properties from type 'Person': name, age
// let person: Person = {}

// 使用 Partial 映射后返回的新类型，name 和 age 都变成了可选属性
let person: Partial<Person> = {}

person = { name: 'pengzu', age: 800 }
person = { name: 'z' }
person = { age: 18 }
```



### 必选类型(Required<T>)

和 Partial 的作用相反

用于将 T 类型的所有属性设置为必选状态，首先通过 keyof T，取出类型 T 的所有属性，
然后通过 in 操作符进行遍历，最后在属性后的 ? 前加上 -，将属性变为必选属性。

定义：

```typescript
type Required<T> = {
    [P in keyof T]-?: T[P];
}
```

使用：

```typescript
interface Person {
    name?: string
    age?: number
}

// 使用 Required 映射后返回的新类型，name 和 age 都变成了必选属性
// 会报错：Type '{}' is missing the following properties from type 'Required<Person>': name, age
let person: Required<Person> = {}
```



### 提取属性(Pick<T>)

定义：

```typescript
type Pick<T, K extends keyof T> = {
	[P in K]: TP;
}
```

从 T 类型中提取部分属性，作为新的返回类型。

使用：比如我们在发送网络请求时，只需要传递类型中的部分属性，就可以通过 Pick 来实现。

```typescript
interface Goods {
    type: string
    goodsName: string
    price: number
}

// 作为网络请求参数，只需要 goodsName 和 price 就可以
type RequestGoodsParams = Pick<Goods, 'goodsName' | 'price'>
// 返回类型：
// type RequestGoodsParams = {
//     goodsName: string;
//     price: number;
// }
const params: RequestGoodsParams = {
    goodsName: '',
    price: 10
}
```



### 排除属性(Omit<T>)

定义：type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>

和 Pick 作用相反，用于从 T 类型中，排除部分属性

用法：比如长方体有长宽高，而正方体长宽高相等，所以只需要长就可以，那么此时就可以用 Omit 来生成正方体的类型

```typescript
interface Rectangular {
    length: number
    height: number
    width: number
}

type Square = Omit<Rectangular, 'height' | 'width'>
// 返回类型：
// type Square = {
//     length: number;
// }

const temp: Square = { length: 5 }
```



### 摘取类型(Extract<T, U>)

语法：Extract<T, U>

提取 T 中可以 赋值 给 U 的类型

定义：type Extract<T, U> = T extends U ? T : never;

用法：

```typescript
type T01 = Extract<"a" | "b" | "c" | "d", "a" | "c" | "f">;  // "a" | "c"

type T02 = Extract<string | number | (() => void), Function>;  // () => void
```



### 排除类型(Exclude<T, U>)

语法：Exclude<T, U>

与 Extract 用法相反，从 T 中剔除可以赋值给 U的类型

定义：

```typescript
type Exclude<T, U> = T extends U ? never : T
```

用法：

```typescript
type T00 = Exclude<"a" | "b" | "c" | "d", "a" | "c" | "f">;  // "b" | "d"

type T01 = Exclude<string | number | (() => void), Function>;  // string | number
```



### 属性映射(Record<K, T>)

定义：

```typescript
type Record<K extends string | number | symbol, T> = {
	[P in K]: T;
}
```

接收两个泛型，K 必须可以是可以赋值给 string | number | symbol 的类型，通过 in 操作符对 K 进行遍历，每一个属性的类型都必须是 T 类型

用法：比如我们想要将 Person 类型的数组转化成对象映射，可以使用 Record 来指定映射对象的类型

```typescript
interface Person {
    name: string
    age: number
}

const personList = [
    { name: 'Jack', age: 26 },
    { name: 'Lucy', age: 22 },
    { name: 'Rose', age: 18 }
]

const personMap: Record<string, Person> = {}

personList.map((person) => {
    personMap[person.name] = person
})
```

比如在传递参数时，希望参数是一个对象，但是不确定具体的类型，就可以使用 Record 作为参数类型

```typescript
function doSomething(obj: Record<string, any>) {
}
```



### 不可为空类型(NonNullable<T>)

定义：

```typescript
type NonNullable<T> = T extends null | undefined ? never : T
```

从 T 中剔除 null、undefined、never 类型，不会剔除 void、unknow 类型

```typescript
type T01 = NonNullable<string | number | undefined>;  // string | number

type T02 = NonNullable<(() => string) | string[] | null | undefined>;  // (() => string) | string[]

type T03 = NonNullable<{name?: string, age: number} | string[] | null | undefined>;  // {name?: string, age: number} | string[]
```



### 构造函数参数类型(ConstructorParameters<typeof T>)

返回 class 中构造函数参数类型组成的 元组类型

定义：

```typescript
/**
 * Obtain the parameters of a constructor function type in a tuple
 */
type ConstructorParameters<T extends new (...args: any) => any> = T extends new (...args: infer P) => any ? P : never;
```

  使用：

  ```typescript
  class Person {
      name: string
      age: number
      weight: number
      gender: 'man' | 'women'
  
      constructor(name: string, age: number, gender: 'man' | 'women') {
          this.name = name
          this.age = age;
          this.gender = gender
      }
  }
  
  type ConstructorType = ConstructorParameters<typeof Person>  //  [name: string, age: number, gender: "man" | "women"]
  
  const params: ConstructorType = ['Jack', 20, 'man']
  
  ```



### 实例类型(InstanceType<T>)

获取 class 构造函数的返回类型

定义：

```typescript
/**
 * Obtain the return type of a constructor function type
 */
type InstanceType<T extends new (...args: any) => any> = T extends new (...args: any) => infer R ? R : any;
```

使用：

```typescript
class Person {
    name: string
    age: number
    weight: number
    gender: 'man' | 'women'

    constructor(name: string, age: number, gender: 'man' | 'women') {
        this.name = name
        this.age = age;
        this.gender = gender
    }
}

type Instance = InstanceType<typeof Person>  // Person

const params: Instance = {
    name: 'Jack',
    age: 20,
    weight: 120,
    gender: 'man'
}
```



### 函数参数类型(Parameters<T>)

获取函数的参数类型组成的 元组

定义：

```typescript
/**
 * Obtain the parameters of a function type in a tuple
 */
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;
```

用法：

```typescript
type FunctionType = (name: string, age: number) => boolean

type FunctionParamsType = Parameters<FunctionType>  // [name: string, age: number]

const params:  FunctionParamsType = ['Jack', 20]
```



### 函数返回值类型(ReturnType<T>)

获取函数的返回值类型

定义：

```typescript
/**
 * Obtain the return type of a function type
 */
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```

使用：

```typescript
type FunctionType = (name: string, age: number) => boolean | string

type FunctionReturnType = ReturnType<FunctionType>  // boolean | string
```

## 六.总结

- 高级类型

| 用法 | 描述                                                         |
| ---- | :----------------------------------------------------------- |
| &    | 交叉类型，将多个类型合并为一个类型，交集                     |
| \|   | 联合类型，将多个类型组合成一个类型，可以是多个类型的任意一个，并集 |

- 关键字

| 用法                    | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| T extends U             | 类型约束，判断 T 是否可以赋值给 U                            |
| P in T                  | 类型映射，遍历 T 的所有类型                                  |
| parameterName is Type   | 类型谓词，判断函数参数 parameterName 是否是 Type 类型        |
| infer P                 | 待推断类型，使用 infer 标记类型 P，就可以使用待推断的类型 P  |
| typeof v === “typename” | 原始类型保护，判断数据的类型是否是某个原始类型（number、string、boolean、symbol） |
| instanceof v            | 类型保护，判断数据的类型是否是构造函数的 prototype 属性类型  |
| keyof                   | 索引类型查询操作符，返回类型上已知的 公共属性名              |
| T[K]                    | 索引访问操作符，返回 T 对应属性 P 的类型                     |

- 映射类型

| 用法                       | 描述                                |
| -------------------------- | ----------------------------------- |
| Readonly                   | 将 T 中所有属性都变为只读           |
| ReadonlyArray              | 返回一个 T 类型的只读数组           |
| ReadonlyMap<T, U>          | 返回一个 T 和 U 类型组成的只读 Map  |
| Partial                    | 将 T 中所有的属性都变成可选类型     |
| Required                   | 将 T 中所有的属性都变成必选类型     |
| Pick<T, K extends keyof T> | 从 T 中摘取部分属性                 |
| Omit<T, K extends keyof T> | 从 T 中排除部分属性                 |
| Exclude<T, U>              | 从 T 中剔除可以赋值给 U 的类型      |
| Extract<T, U>              | 提取 T 中可以赋值给 U 的类型        |
| Record<K, T>               | 返回属性名为 K，属性值为 T 的类型   |
| NonNullable                | 从 T 中剔除 null 和 undefined       |
| ConstructorParameters      | 获取 T 的构造函数参数类型组成的元组 |
| InstanceType               | 获取 T 的实例类型                   |
| Parameters                 | 获取函数参数类型组成的元组          |
| ReturnType                 | 获取函数返回值类型                  |

***



## 七.其他

### Typescript错误忽略

#### 单行忽略:

```typescript
// @ts-ignore
```

#### 忽略全文:

```typescript
// @ts-nocheck
```

#### 取消忽略全文:

```typescript
// @ts-check
```



### declare 声明文件

#### 声明文件以 **.d.ts** 为后缀，例如：

```typescript
runoob.d.ts
```

#### 声明文件或模块的语法格式如下：

```typescript
declare module Module_Name {
}
```

#### TypeScript 引入声明文件语法格式：

```typescript
/// <reference path = " runoob.d.ts" />
```



### TypeScript 模块

模块导出使用关键字 **export** 关键字，语法格式如下：

```typescript
// 文件名 : SomeInterface.ts 
export interface SomeInterface { 
   // 代码部分
}
```

要在另外一个文件使用该模块就需要使用 **import** 关键字来导入:

```typescript
import someInterfaceRef = require("./SomeInterface");
```



### namespace 命名空间

命名空间一个最明确的目的就是解决重名问题。

命名空间定义了标识符的可见范围，一个标识符可在多个名字空间中定义，它在不同名字空间中的含义是互不相干的。这样，在一个新的名字空间中可定义任何标识符，它们不会与任何已有的标识符发生冲突，因为已有的定义都处于其他名字空间中。

TypeScript 中命名空间使用 **namespace** 来定义，语法格式如下：

```typescript
namespace SomeNameSpaceName { 
   export interface ISomeInterfaceName {}  
   export class SomeClassName {}  
}
```

以上定义了一个命名空间 SomeNameSpaceName，如果我们需要在外部可以调用 SomeNameSpaceName 中的类和接口，则需要在类和接口添加 **export** 关键字。

要在另外一个命名空间调用语法格式为：

```typescript
SomeNameSpaceName.SomeClassName;
```

如果一个命名空间在一个单独的 TypeScript 文件中，则应使用三斜杠 /// 引用它，语法格式如下：

```typescript
/// <reference path = "SomeFileName.ts" />
```

***



## 四.配置文件

tsconfig.json 是ts编译器的配置文件,ts编译器可以根据它的信息来对代码进行编译.

```typescript
{
  /*
  * 文件虽然是json文件,但是是可以在里面写注释的.
  * include 用来指定需要编译的ts文件
  * exclude 用来排除不需要编译的文件目录
  * extends 继承,如果配置很复杂就需要用到extends来继承其他配置文件或公共配置
  * files 指定被编译的文件列表,只有需要编译的文件很少时才会用到.
  * compilerOptions 编译器的选项,ts配置文件中最重要的属性.
  */
  "include": ["./src/**/*"], // **表示任意目录,*表示任意文件
  "exclude": [],
  "extends": "./configs/base",
  "files": ["core.ts", "sys.ts", "main.ts"],
  "compilerOptions": {
    "target": "ES5", // 指定ts被编译的ES版本
    "module": "commonjs", // 指定模块化的规范
    "lib": ["dom", "es6"], // 指定项目中使用的库
    "outDir": "dist", // 指定编译后文件所在目录
    "outFile": "./dist/app.js", // 将代码合并为一个文件,所有的全局作用域中的代码会合并到同一个文件中
    "allowJs": false, // 是否对js文件进行编译,默认false
  	"checkJs": false, // 是否检查js代码
    "removeComments": true, // 是否移除注释
    "noEmit": true, // 不生成编译后的文件
    "noEmitOnError": true, // 当有错误时不生成编译文件
    "strict": true, // 所有严格检查的总开关
  	"alwaysStrict": true, // 用来设置编译后的文件是否使用严格模式,默认false
  	"noImplicitAny": true, // 不允许隐式的any类型
    "noImplicitThis": true, // 不允许不明确类型的this
    "strictNullChecks": true // 严格检查空值
  }
}
```
