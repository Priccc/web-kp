JavaScript 三大特性： 封装、继承和多态

[JS继承题-封装篇](https://juejin.cn/post/6844904094948130824)

[彻底弄懂JS继承](https://juejin.cn/post/6844904098941108232)

先来了解一些基本概念

```
// 1. 构造函数
function Cat(name){
  this.name = name
}
// 2. 构造函数原型对象
Cat.prototype
// 3. 构造函数的实例
var gaogao = new Cat('糕糕');
// 4. 构造函数的静态方法， 名为fn
Cat.fn = function() {}
// 5. 原型方法上的方法，名为fn
Cat.prototype.fn = function() {}
```

![实例原型链](https://user-gold-cdn.xitu.io/2020/3/17/170e717b74663dea?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 封装

### 1. es6 之前的封装

都知道 ES6 的 class 实际就是一个语法糖，那么在 ES6 之前，是没有类这个概念的，因此是借助于原型对象和构造函数来实现。

- 私有属性及方法：只能在构造函数内访问不能被外部所访问(在构造函数内使用 var 声明的属性)
- 公有属性和方法(或实例方法): 对象外可以访问到对象内的属性和方法(在构造函数内使用 this 设置，或者设置在构造函数原型对象上比如 Cat.prototype.xxx)
- 静态属性和方法: 定义在构造函数上的方法(比如 Cat.xxx)，不需要实例就可以调用(例如 Object.assign())

#### 私有属性方法和共有属性方法

- 在函数内用 var 定义的就是私有的
- 在函数内用 this 承接的就是公有

#### 静态属性方法和公有属性方法

- 在构造函数上定义的是静态属性方法 如 promise.all(), Array.from()等
- 在实例上定义的事公寓属性方法 如 Array.prototype.push()等

#### 实例自身的属性和定义在构造函数原型对象中的属性

```
function Cat (name) {
  this.name = name
}
Cat.prototype.prototypeProp = '我是构造函数原型对象上的属性'
Cat.prototype.cleanTheBody = function (){
  console.log('我会用唾液清洁身体')
}
var guaiguai = new Cat('guaiguai')
console.log(guaiguai)
console.log(guaiguai.name)
console.log(guaiguai.prototypeProp)
guaiguai.cleanTheBody()
```

输出的结果：
```
Cat {name: "guaiguai"}
'guaiguai'
'我是构造函数原型对象上的属性'
'我会用唾液清洁身体'
```
prototypeProp 和 cleanTheBody 并不出现在实例guaiguai上，但是guaiguai可以访问他们

所以结论：
<b>定义在构造函数原型对象上的属性和方法虽然不能直接表现在实例对象上，但是实例对象却可以访问或者调用它们</b>

#### 区分自身的属性和定义在构造函数原型对象中的属性

```
function Cat (name) {
  this.name = name
}
Cat.prototype.prototypeProp = '我是构造函数原型对象上的属性'
Cat.prototype.cleanTheBody = function () {
  console.log('我会用唾液清洁身体')
}
var guaiguai = new Cat('guaiguai')
for (key in guaiguai) {
  if (guaiguai.hasOwnProperty(key)) {
    console.log('我是自身属性', key)
  } else {
    console.log('我不是自身属性', key)
  }
}
console.log('-分隔符-')
console.log(Object.keys(guaiguai))
console.log(Object.getOwnPropertyNames(guaiguai))

```

结果
```
'我是自身属性 name'
'我不是自身属性 prototypeProp'
'我不是自身属性 cleanTheBody'
'-分隔符-'
["name"]
["name"]
```
结论：

 - 使用for...in...能获取到实例对象自身的属性和原型链上的属性
 - 使用Object.keys()和Object.getOwnPropertyNames()只能获取实例对象自身的属性
 - 可以通过.hasOwnProperty()方法传入属性名来判断一个属性是不是实例自身的属性


### 2. class 封装

(一) class的基本概念：

当你使用class的时候，它会默认调用constructor这个函数，来接收一些参数，并构造出一个新的实例对象(this)并将它返回。如果你的class没有定义constructor，也会隐式生成一个constructor方法

(二) class中几种定义属性的区别：：

在constructor中var一个变量，它只存在于constructor这个构造函数中
在constructor中使用this定义的属性和方法会被定义到实例上
在class中使用=来定义一个属性和方法，效果与第二点相同，会被定义到实例上
在class中直接定义一个方法，会被添加到原型对象prototype上
在class中使用了static修饰符定义的属性和方法被认为是静态的，被添加到类本身，不会添加到实例上

(三) other:

class本质虽然是个函数，但是并不会像函数一样提升至作用域最顶层
如遇class中箭头函数等题目请参照构造函数来处理
使用class生成的实例对象，也会有沿着原型链查找的功能

## 继承

### 1. 原型链继承

将子类的原型对象指向父类的实例

![原型链继承](https://user-gold-cdn.xitu.io/2020/3/17/170e718601592273?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```
function Parent (name) {
  this.name = name
  this.sex = 'boy'
  this.colors = ['white', 'black']
}
function Child () {
  this.feature = ['cute']
}
var parent = new Parent('parent')
Child.prototype = parent // 子类的原型对象指向父类的实例

var child1 = new Child('child1')
child1.sex = 'girl'
child1.colors.push('yellow')
child1.feature.push('sunshine')

var child2 = new Child('child2')

console.log(child1)
console.log(child2)

console.log(child1.name)
console.log(child2.colors)

console.log(parent)

```

结果：
```
Child { feature: ['cute', 'sunshine'], sex: 'girl' }
Child { feature: ['cute'] }

'parent'
['white', 'black', 'yellow']

Parent { name: 'parent', sex: 'boy', colors: ['white', 'black', 'yellow'] }
```

优点：
 - 继承了父类的模板，又继承了父类的原型对象

缺点：
 - 如果要给子类的原型上新增属性和方法，就必须放在Child.prototype = new Parent()这样的语句后面
 - 无法实现多继承(因为已经指定了原型对象了)
 - 来自原型对象的所有属性都被共享了，这样如果不小心修改了原型对象中的引用类型属性，那么所有子类创建的实例对象都会受到影响(这点从修改child1.colors可以看出来)
- 创建子类时，无法向父类构造函数传参数(这点从child1.name可以看出来)


#### instanceof

instanceof 运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上。a instanceof B 即a的_proto_上是否有B的prototype

```
function Parent () {
  this.name = 'parent'
}
function Child () {
  this.sex = 'boy'
}
Child.prototype = new Parent()
var child1 = new Child()

console.log(child1 instanceof Child) // true
console.log(child1 instanceof Parent) // true
console.log(child1 instanceof Object) // true

```

#### isPrototypeOf

isPrototypeOf()的用法和instanceof相反。

它是用来判断指定对象object1是否存在于另一个对象object2的原型链中，是则返回true，否则返回false。

### 2. 构造继承

在子类构造函数内部使用call或apply来调用父类构造函数

```
function Parent (name) {
  this.name = name
}
function Child () {
  this.sex = 'boy'
  Parent.call(this, 'child')
}
var child1 = new Child()
console.log(child1)

```

优点：

 - 解决了原型链继承中子类实例共享父类引用对象的问题，实现多继承，创建子类实例时，可以向父类传递参数

缺点：

  - 构造继承只能继承父类的实例属性和方法，不能继承父类原型的属性和方法
  - 实例并不是父类的实例，只是子类的实例

### 3. 组合继承

使用原型链继承来保证子类能继承到父类原型中的属性和方法
使用构造继承来保证子类能继承到父类的实例属性和方法

```
function Parent(name) {
  this.name = name;
}
Parent.prototype.getName = function() {
  console.log(this.name);
}
function Child(name, sex) {
  Parent.call(this, name);
  this.sex = sex;
}
Child.prototype = new Parent();
Child.prototype.getSex = function() {
  console.log(this.sex);
}
```

#### constructor

用来标示实例对象是由哪个构造函数创建的。

```
guaiguai.__proto__ = Cat.prototype
Cat.prototype.constructor = Cat
guaiguai.__proto__.constructor = Cat
```


```
function Parent (name) {
  this.name = name
}
Parent.prototype.getName = function () {
  console.log(this.name)
}
function Child (name) {
  this.sex = 'boy'
  Parent.call(this, name)
}
Child.prototype = new Parent()
Child.prototype.getSex = function () {
  console.log(this.sex)
}

var child1 = new Child('child1')
var parent1 = new Parent('parent1')
console.log(child1.constructor) // f Parent()
console.log(parent1.constructor) // f Parent()

```

![](https://user-gold-cdn.xitu.io/2020/3/21/170fc8039b9967c0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

parent1是Parent的一个实例， 实例本身没有constructor, 所以在原型（_proto_）上查找， 指向构造函数Parent, 于是第二个打印出Parent

child 因为切断了与原来的联系，所以向上查找， 然后在匿名实例的_proto_上找到了构造函数Parent， 所以第一个也打印出Parent

所以在使用组合继承时， 需要修正constructor的指向

```
function Parent(name) {
  this.name = name;
}
Parent.prototype.getName = function() {
  console.log(this.name);
}
function Child(name, sex) {
  Parent.call(this, name);
  this.sex = sex;
}
Child.prototype = new Parent();
Child.prototype.constructor = Child;
Child.prototype.getSex = function() {
  console.log(this.sex);
}
```

![](https://user-gold-cdn.xitu.io/2020/3/21/170fc80609af65f2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

这样的原型链就完整了


```
function Parent(name) {
  this.name = name;
  this.color = ['white']
}
Parent.prototype.feature = ['cute'];

function Child(name, color) {
  Parent.call(this, [name, color]);
}
Child.prototype = new Parent();
Child.prototype.constructor = Child;

const child1 = new Child('child1', ['white']);
child1.color.push('red');
child1.feature.push('sunshine');
const child2 = new Child('child2', ['white']);

console.log(child1.colors) // ['white', 'red']
console.log(child2.colors) // ['white']
console.log(child1.features) // ['cute', 'sunshine']
console.log(child2.features) // ['cute', 'sunshine']
```

优点：

- 可以继承父类实例属性和方法，也能够继承父类原型属性和方法
 - 弥补了原型链继承中引用属性共享的问题
 - 可传参，可复用

缺点：
 - 使用组合继承时，父类构造函数会被调用两次
 - 并且生成了两个实例，子类实例中的属性和方法会覆盖子类原型(父类实例)上的属性和方法，所以增加了不必要的内存。

### 4. 寄生组合继承

组合继承的缺点就是 父类的构造函数走了两次， 生成了两个实例

那么 我们需要跳过父类属性上的实例， 直接继承父类原型链上的属性

#### Object.create

Object.create(proto, propertiesObject)

 - 参数一，需要指定的原型对象
 - 参数二，可选参数，给新对象自身添加新属性以及描述器

 第一个参数proto，它的作用就是能指定你要新建的这个对象它的原型对象是谁。

 我们使用var parent1 = new Parent()创建了一个对象parent1，那parent1.__proto__就是Parent.prototype。

使用var obj = new Object()创建了一个对象obj，那obj.__proto__就是Object.prototype。

而这个Object.create() ，它现在能指定你新建对象的__proto__。


```
function Parent(name) {
  this.name = name;
}
Parent.prototype.getName = function() {
  console.log(this.name);
}
function Child(name, sex) {
  Parent.call(this, name);
  this.sex = sex;
}
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
Child.prototype.getSex = function() {
  console.log(this.sex);
}
```


总结：解决了组合继承的缺点

 - 父级的构造函数只调用了一次， 只创建了一次实例
 - 子类能访问父级的方法和原型上的方法
 - 能正常使用instanceof方法


### 5. 原型式继承

原型式继承原理与Object.create()相同， 创建一个构造函数， 构造函数的原型指向对象，然后通过new操作符创造一个实例，并且返回一个实例。

ES5之后可以使用Object.create()实现， 之前就需要手动实现了

```
 function create(obj) {
   function F() {}
   F.prototype = obj;
   F.prototype.constructor = F;
   return new F();
 }
```

优点：
 再不用创建构造函数的情况下，实现了原型链继承，代码量减少一部分。

缺点：
 - 无法直接给父级构造函数使用参数
 - 谨慎定义方法，以免定义方法也继承对象原型的方法重名
 - 一些引用数据操作的时候会出问题，两个实例会公用继承实例的引用数据类

### 6. 寄生式继承

他就是在原型式继承的基础上在封装一层， 增强对象

```
function createAnother (original) {
    var clone = Object.create(original);; // 通过调用 Object.create() 函数创建一个新对象
    clone.fn = function () {}; // 以某种方式来增强对象
    return clone; // 返回这个对象
}

```

```
var cat = {
  heart: '❤️',
  colors: ['white', 'black']
}
function createAnother (original) {
    var clone = Object.create(original);
    clone.actingCute = function () {
      console.log('我是一只会卖萌的猫咪')
    }
    return clone;
}
var guaiguai = createAnother(cat)
var huaihuai = Object.create(cat)

guaiguai.actingCute() // '我是一只会卖萌的猫咪'
console.log(guaiguai.heart) // '❤️'
console.log(huaihuai.colors) //  ['white', 'black']
console.log(guaiguai) // {actingCute: f}
console.log(huaihuai) // {}

```

优缺点与原型式继承相同

### 7. 混入方式继承多个对象

如何一个子类继承多个父类

```
function Child () {
    Parent.call(this)
    OtherParent.call(this)
}
Child.prototype = Object.create(Parent.prototype)
Object.assign(Child.prototype, OtherParent.prototype)
Child.prototype.constructor = Child

```

### 8. class中的继承

class 中的继承依靠 `extends`和`super`， 他们的继承效果与寄生组合继承一样

super相关：
 - 在实现继承时，如果子类中有constructor函数，必须得在constructor中调用一下super函数，因为它就是用来产生实例this的。
 - super有两种调用方式：当成函数调用和当成对象来调用。
 - super当成函数调用时，代表父类的构造函数，且返回的是子类的实例，也就是此时super内部的this指向子类。在子类的constructor中super()就相当于是Parent.constructor.call(this)。
 - super当成对象调用时，普通函数中super对象指向父类的原型对象，静态函数中指向父类。且通过super调用父类的方法时，super会绑定子类的this，就相当于是Parent.prototype.fn.call(this)。
