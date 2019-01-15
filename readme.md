<!-- TOC -->

  - [0.1. 写在前面](#01-写在前面)
- [1. 创建对象](#1-创建对象)
  - [1.1. Object构造函数创建](#11-object构造函数创建)
  - [1.2. 对象字面量语法创建](#12-对象字面量语法创建)
  - [1.3. 工厂模式](#13-工厂模式)
  - [1.4. 构造函数模式](#14-构造函数模式)
  - [1.5. 原型模式](#15-原型模式)
    - [1.5.1. 另一种原型语法](#151-另一种原型语法)
  - [1.6. ❤❤❤组合使用构造函数模式和原型模式❤❤❤](#16-❤❤❤组合使用构造函数模式和原型模式❤❤❤)
  - [1.7. 动态原型模式](#17-动态原型模式)
- [2. 继承](#2-继承)
  - [2.1. 原型链+原型链继承](#21-原型链原型链继承)
  - [2.2. 借用构造函数继承](#22-借用构造函数继承)
  - [2.3. ❤❤组合继承❤❤](#23-❤❤组合继承❤❤)
  - [2.4. 原型式继承](#24-原型式继承)
  - [2.5. ❤❤❤寄生组合式继承❤❤❤](#25-❤❤❤寄生组合式继承❤❤❤)
  - [ES6的继承](#es6的继承)

<!-- /TOC -->
## 0.1. 写在前面

 1. 对象就是一个无序属性的集合，也就是一组组的名值对，其中的值可以是数据或函数
 2. 每个对象都是基于一个引用类型（变量分为基础类型和引用类型）创建的，其中最基础的就是Object类型 。
 3. 构造函数本身也就是个函数而已，只不过这个函数用来构造对象，任何函数，只要通过new来调用，就是个构造函数
 4. 函数中的this指针：this就是当下运行环境，当在全局中调用定义的函数时，this也就指向全局，即window对象。
 5. 万物皆对象，构造函数是对象，实例是对象，构造函数的原型也是对象
 6. 原型链最顶端的原型是Object对象，也就是所有函数的默认原型都是Object对象的实例

# 1. 创建对象
## 1.1. Object构造函数创建
```
var person = new Object();
person.name = 'xiaohong';
person.myName = function(){
    alert(this.name)
};
```
缺点：代码不能复用，创造多个对象时，有很多重复代码
## 1.2. 对象字面量语法创建
```
var person = {
    name: 'xiaohong';
    myName: function(){
        alert(this.name)
    };
};
```
缺点：和使用Object构造函数一样，只是简便些，代码不能复用，创造多个对象时，有很多重复代码
## 1.3. 工厂模式
将Object构造函数创建的方法的过程封装，使变成一个能接受参数来构建不同对象的方法
```
function createPerson(name){
    var o = new Object();
    o.name = name;
    o.myName = function(){
        alert(this.name)
    };
}
```
缺点：多次调用时，每次都会为对象创建一个nem属性和一个myName方法，代码仍旧不能复用。
## 1.4. 构造函数模式
和Object类型创建新对象类似，但用构造函数模式，是创建自定义的构造函数
```
function Person(name){
    this.name = name;
    this.family = ['father', 'mother']
    this.myName = function(){
        alert(this.name);
    };
};
var xiaoming = new Person('xiaoming')
var xiaohong = new Person('xiaohong')
```
通过`new Person()`创建的xiaoming就是Person对象的*实例*，实例也是个对象，要创建这个xiaoming实例，步骤如下：

 1. 创建一个对象xiaoming
 2. 将作用域赋给xiaoming，也就是本来的this指向window对象，现在指向xiaoming对象
 3. 给xiaoming对象添加Person中定义的属性
<br>

通过`new Person()`创建的两个实例xiaoming和xiaohong内的属性和方法是不能复用的。对于两个实例来说：

 1. name属性需要两个实例分别拥有一个指针指向自己的属性值无可厚非
 2. 例如family属性和myName方法，都是应该两个实例分别有一个指针指向同一个属性定义位置的，可通过这样创建的实例，他们是没有复用的，也就是`xiaoming.myName !== xiaohong.myName`
 
## 1.5. 原型模式
为了能够使这些共享属性能够复用，可以把这些属性放在函数的原型对象上，我们假设Person对象的原型对象为O，那么`Person.prototype = O`
 
 1. 只要创建了一个新函数A，那么A就定会有一个`prototype`属性指针指向它的原型对象B，默认情况下，原型对象B也会自动有一个`constructor`属性指针指向A
 2. 由构造函数创建的实例对象a也会自动创建一个指针叫做`[[prototype]]`（或者`__proto__`）来指向原型对象B，从而保证了实例也能访问到在构造函数原型上定义的属性和方法。

![prototype基本规则](https://github.com/KiiiKi/js-prototype-and-inherit/blob/master/prototype.png)
```
Cat.prototype = Animal                    //true
Animal.constructor = Cat                  //true
Cat.prototype.constructor = Cat           //true
var kitty = new Cat()
kitty.__proto__ = Animal                  //true
Animal.isPrototypeOf(kitty)               //true
Object.getPropertyOf(kitty) == Animal     //true
```
<br>

对象实例更改各种类型的属性：
```
//这个写法属于下节要介绍到的 “组合使用构造函数和原型模式”
function Person(name){
    this.name = name;  //基本数据类型
    this.color = ['red', 'blue']；  //引用类型
};
Person.prototype.age = 18;  //基本数据类型
Person.prototype.family = ['father', 'mother'];   //引用类型
Person.prototype.myName = function(){
    alert(this.name);
};
var xiaoming = new Person('xiaoming')
var xiaohong = new Person('xiaohong')

xiaoming.family.push('sister')   //更改原型上的引用类型
console.log(xiaoming.family)    //["father", "mother", "sister"]
console.log(xiaohong.family)    //["father", "mother", "sister"]
console.log(xiaoming.hasOwnProperty('family'))    //false，属性来自原型
console.log(xiaohong.hasOwnProperty('family'))    //false，属性来自原型

xiaoming.age = 20    //更改原型上的基本数据类型
console.log(xiaoming.age)    //20
console.log(xiaohong.age)    //18
console.log(xiaoming.hasOwnProperty('age'))   //true，属性来自实例
console.log(xiaohong.hasOwnProperty('age'))   //false，属性来自原型

xiaoming.color.push('yellow')   //更改构造函数上的引用类型
console.log(xiaoming.color)    //["red", "blue", "yellow"]
console.log(xiaohong.color)    //["red", "blue"]
console.log(xiaoming.hasOwnProperty('color'))    //true，属性来自实例
console.log(xiaohong.hasOwnProperty('color'))    //true，属性来自实例
```

1. 更改原型上的引用类型family

 - 基本类型值被保存在栈内存中，复制基本类型值得到的是值的副本
 - 引用类型的值是对象，保存在堆内存中，所以引用类型的变量family实际上包含的不是对象本身，而只是一个指向对象的指针，所以复制时，只是创建了一个新的指针，指向还是不变的。
 - 当xiaoming更改原型上的引用类型family时，就把对象内容本体改变为`["father", "mother", "sister"]`，于是`xiaohong.family`指针的内容一样改变。
2. 更改原型上的基本数据类型age
对象实例不能更改原型上的基本数据类型，只能做到屏蔽，也就是`xiaoming`有两个age属性，一个`age = 20`在实例上，一个`age = 18`在原型上，而同名属性中，实例上的优先级更高，所以只能看到实例上的age，但原型上的age仍然是18，所以`xiaohong`仍然是18
<br>
3. 更改构造函数上的引用类型
在  '*函数构造模式* ' 那节已经提到过，在构造函数中定义的属性全部都不能得到复用，也就是`xiaoming.color`和`xiaohong.color`虽然内容一样，但是指针和指针指向都是不同的，也就当作两个毫无关联的属性看待，所以xiaoming更改自己的color不会影响到`xiaohong.color` 
<br>

**简单来说就是原型属性上的引用类型值会被全部实例共享，谨慎改变！**
<br>

### 1.5.1. 另一种原型语法
前面的添加方法：
```
Person.prototype.age = 18; 
Person.prototype.family = ['father', 'mother'];
Person.prototype.myName = function(){
    alert(this.name);
};
```
为了减少不必要的`Person.prototype`输入，使用一个对象自变量（这个对象是`Person.prototype`）来包含所有的属性和方法，然后再用此对象 **重写** 整个原型对象。
```
Person.prototype = {
    constructor: Person,   //必须要写！！
    age: 18,
    family: ['father', 'mother'],
    myName: function(){
        alert(this.name);
    }
}
```
这种写法切断了`person`和最初原型对象之间的联系，是把`Person`的原型对象整个重置改写为了上面设置的这个'`Person.prototype`'对象。但是这个新建的'`Person.prototype`'对象的`constructor`指针并没有指向`Person`，而是指向默认的`Object`对象，所以要自己添加`Person.prototype.constructor: Person;`来让该属性访问回正确的值。
<br>


## 1.6. ❤❤❤组合使用构造函数模式和原型模式❤❤❤
上面的xiaoming和xiaohong的例子其实已经说明了组合使用的道理。也就是：

 - 构造函数模式：定义 **实例属性** 
 
    > 每个实例都有，但值不同。也就是重复构造多个，每个实例的指针不同，指向也不同。

 - 原型模式：定义实例所 **共享** 的属性以及方法
    
    > 方法定义在原型上，也就是重复构造的多个实例，其指针虽然不同，但指向相同（实例中定义同名属性可以屏蔽原型属性，但不能改变原型属性）

```
//构造函数模式:
function Person(name, age){
    this.name = name;  //基本数据类型
    this.age = age; 
};
//原型模式：
Person.prototype = {
    constructor: Person,
    family: ['father', 'mother'],
    myName: function(){
        alert(this.name);
    }
}
var xiaoming = new Person('xiaoming', 20)
var xiaohong = new Person('xiaohong', 18)
```
## 1.7. 动态原型模式
将所有的配置都封装在构造函数中，在构造函数里面写初始化原型.第一次调用构造函数时，通过判断是否有该方法，没有则添加到原型属性中去。
```
function Person(name, age){
    this.name = name;  //基本数据类型
    this.age = age; 
    if(typpeof this.myName != 'function'){
        Person.prototype.myName = function(){
            alert(this.name);
        };
    }
};
```
# 2. 继承

## 2.1. 原型链+原型链继承

**原型链就是让原型对象对应另一个类型的实例**
![原型链基本规则](https://github.com/KiiiKi/js-prototype-and-inherit/blob/master/yuanxinglian.png)
```
function SuperType(){
    this.value = true;
}
SuperType.prototype.getSuperValue = function(){
  return this.value;
}
function SubType(){
    this.subvalue = false;
}
SubType.prototype = new SuperType()
SubType.prototype.constructor = SubType
SubType.prototype.getSubValue = function(){
  return this.subvalue;
}
var instance = new SubType()

console.log(SuperType.prototype)
/*
    {
    getSuperValue: ƒ,
    constructor: ƒ SuperType()
    }
*/
console.log(SuperType)
/* 
    ƒ SuperType(){
        this.value = true;
    }
*/
console.log(SubType.prototype)
/*
    {
    constructor: ƒ SubType(),
    getSubValue: ƒ (),
    value: true
    }
*/
console.log(SubType)
/*
    ƒ SubType(){
        this.subvalue = false;
    }
*/
```
继承实现本质：
SuperType创建了一个实例，并且把这个实例当作SubType的prototype属性指向。也就是把SubType的‘旧原型对象’取代了，重置改写为这个`new SuperType()`对象。所以同理我们要补充`SubType.prototype.constructor = SuperType`来让该属性访问回正确的值。
<br>

原型链的问题：
```
function SuperType(){
    this.value = true;
    this.color = ['red', 'yellow']
}
function SubType(){
}
var a = new SuperType()
SubType.prototype = a
SubType.prototype.constructor = SubType

var instance = new SubType()
var instance2 = new SubType()
instance.value = false;
instance.color.push('green')

console.log(instance.value)     //false
console.log(instance2.value)    //true
console.log(instance.color)     //["red", "yellow", "green"]
console.log(instance2.color)    //["red", "yellow", "green"]
```
 ‘创建对象--原型模式’那节不是说：构造函数中定义的属性都不可复用，相当于虽然内容一样，但是指针和指针指向都是不同的，也就当作两个毫无关联的属性看待吗？为什么此处color定义在SuperType的构造函数里，还是会被`instance.color.push('green')`重写呢？？？？？？
 > - a作为`SubType.prototype`，实际上是`new SuperType()`，也就是SuperType对象的一个实例罢了。
 > - 每一个`new SuperType()`上的属性的确是指针和指针指向都是不同的，也就当作几个毫无关联的属性看待。所以可以当作此处的a有着一个不会被影响的value和color
 > - 作为一个实例，a（也就是`SubType.prototype`）现在的属性有`a.value`和`a.color`，这种属性对于SubType来说，和定义`SubType.prototype.value`、`SubType.prototype.color`没有区别。所以对于SubType的所有实例来说，他们的color是多个指针指向同一个color对象。
 > - 对于instance来说，`instance.color.push('green')`实际上是在改变原型上的引用变量，也就是改变了指针指向的对象本身，所以就造成了`instance2.color`也发生变化。
<br>

## 2.2. 借用构造函数继承
为了继承父类构造函数内定义的属性，并且不出现同一属性的多指针指向一个对象的问题，使用‘借助构造函数’的方法。
其本质就是不通过将原型指针指向父类的实例对象的方法，而是**子类构造函数直接调用父类构造函数**，来达到获取父类构造函数内定义的属性的效果。
```
function SuperType(){
  this.name = 'xiaoming',
  this.color = ['red', 'yellow']
}
SuperType.prototype.sex = 'male';
function SubType(){
  console.log(this)      //subtype {age}
  SuperType.call(this)   //将subtype运行时的环境改成SuperType的环境，所以能调用SuperType的属性
  console.log(this)      //subtype {name, color, age}
  this.age = 18
}

var child_1 = new SubType()
var child_2 = new SubType()
child_1.color.push('green')
var father_1 = new SuperType()

console.log(child_1.color)     //["red", "yellow", "green"]
console.log(child_2.color)     //["red", "yellow"]
console.log(child_1.name)      //xiaoming
console.log(father_1.name)     //xiaoming
console.log(child_1.age)       //18
console.log(father_1.age)      //undefined
console.log(child_1.sex)       //undefined
console.log(father_1.sex)      //male
```
通过`SuperType.call(this)`  将child_1和child_2运行时的环境改成SuperType的环境，也就是原本child调用的this时SubType，现在this变成了SuperType，所以能调用SuperType的属性
但是由`console.log(child_1.sex)`可以看出，因为用`SuperType.call(this)`只是将指针改变，并没有实质上将SuperType与SubType进行原型上的连接，所以结果为undefined。

## 2.3. ❤❤组合继承❤❤
为了达到原型上的链接，又不让父类构造函数上的属性在子类实例上出现问题，于是组合使用“原型链继承”和“借用构造函数继承”，其意义就是通过“原型链继承”来获得原型属性及方法的继承；通过“借用构造函数继承”来获得构造函数属性的继承。
```
function SuperType(){
  this.color = ['red', 'yellow']
}
SuperType.prototype.sex = 'male';
function SubType(){
  SuperType.call(this) 
}
SubType.prototype = new SuperType()
SubType.prototype.constructor = SubType

var child_1 = new SubType()
var child_2 = new SubType()
child_1.color.push('green')

console.log(child_1.color)     //["red", "yellow", "green"]
console.log(child_2.color)    //["red", "yellow"]
console.log(child_1.sex)       //male
```
通过这样的组合继承，就能让不同的子实例拥有自己的属性的同时，也能使用相同的方法增加复用性。它是js最常用的继承模式。

## 2.4. 原型式继承
以一个对象A对基础，将这个对象作为一个临时构造函数B的原型，再返回这个临时构造函数的一个实例B()，最后销毁临时构造函数。
```
function Object(A){
  function B(){};
  B.prototype = A;
  return new B();
```
用此方式，就不需要通过创建父类的实例也能达到原型链继承效果
```
function SuperType(name){
  this.name = name,
  this.color = ['red', 'yellow']
}
SuperType.prototype.age = 18;

function Object(a){
  function b(){}
  b.prototype = a;
  return new b();
}

var father_1 = new SuperType('xiaoming')
var child_1 = Object(father_1)
child_1.color.push('green')
child_1.name = 'xiaohong'

console.log(child_1.color)     //["red", "yellow", "green"]
console.log(father_1.color)
console.log(child_1.name)     //xiaohong
console.log(father_1.name)     //xiaohong
console.log(child_1.age)      //18
console.log(child_1.__proto__ == father_1)    //supertype
```
上例的原型继承关系为：临时构造函数b.prototype = a，其中a为SuperType的实例father_1；所以临时构造函数b的实例child_1的__proto__ == father_1。
实际跟使用原型链继承效果一样（缺点也一样：子实例能改变父类构造函数内的引用变量），只是创建构造函数变成了函数内的临时构造函数。
> ESMAscript5使用Object.create()方法规范了原型式继承，和Object()效果一样

## 2.5. ❤❤❤寄生组合式继承❤❤❤
常用的‘组合式继承’也存在着不足，就是每次都会调用两次父类构造函数：
```
//第二次调用
SuperType.call(this)
//第一次调用
SubTpe.prototype = new SuperType()
```
寄生组合式继承，就是：
1. 继续使用SuperType.call(this)来继承父类构造函数中的属性和方法
2. 代替SubTpe.prototype = new SuperType()，采用类似原型式继承，创建SuperType.prototype的副本，并将SubType.prototype指向它。
```
function inheritPrototype(subtype, supertype){
  var fatherInstance = Object.create(supertype.prototype)
  subtype.prototype = fatherInstance
  fatherInstance.constructor = subtype
}

function supertype(name){
  this.name = name;
  this.color = ['red', 'blue', 'green']
}
supertype.prototype.sayName = function(){
  alert(this.name)
}
supertype.prototype.size = [1,2,3]

function subtype1(name, age){
  supertype.call(this, name)
  this.color = ['orange']
  this.age = age

}
function subtype2(name, year){
  supertype.call(this, name)
  this.year = year
}

inheritPrototype(subtype1, supertype)
inheritPrototype(subtype2, supertype)

subtype1.prototype.sayAge = function(){
  alert(this.age)
}
subtype2.prototype.sayYear = function(){
  alert(this.year)
}

var child_1 = new subtype1('xiaoming', 29)
var child_2 = new subtype2('xiaohua', 2019)
child_1.color.push('yellow')
child_1.size.push(4)
var father1 = new supertype('xiaohong')

console.log(supertype.prototype)    //{sayName, size}
console.log(subtype1.prototype)     // supertype {sayAge}
console.log(subtype2.prototype)     // supertype {sayYear}
console.log(subtype1.prototype == subtype2.prototype)   //false
console.log(child_1.color)     //["orange", "yellow"]
console.log(father1.color)     //["red", "blue", "green"]
console.log(child_1.size)      //[1, 2, 3, 4]
console.log(father1.size)      //[1, 2, 3, 4]
```
从上面的结果，可以得出这样的寄生组合式继承原理：
![parasitic基本规则](https://github.com/KiiiKi/js-prototype-and-inherit/blob/master/parasitic.png)
为什么subtype1.prototype == subtype2.prototype为false呢？？？？？
> `subtype1.prototype`和`subtype2.prototype`是分别通过调用`inheritPrototype`函数而得来的。而`inheritPrototype`内的`object.create`之前已经说过原理，是通过创建临时构造函数得到的，所以两次创建的临时构造函数肯定是不同的，从而获得的是两个不同的`fatherInstance`对象。  

因为寄生组合式只调用了一次SuperType的构造函数，所以在保持原型链不变的同时，还能避免创造重复的属性，体现了其高效率，因此，这是普遍认为的最理想的继承方式。

## ES6的继承
按照上面用过的例子，转化为ES6就是下面的写法：
```
class SuperType {
  constructor(name){
    this.name = name;
    this.color = ['red', 'yellow']
  }
  //ES6明确规定，Class内部只有静态方法，没有静态属性,所以ES6在类中定义静态属性都是错误的。
  //size = [1,2,3]
  sayName(){console.log(this.name)}
}

class SubType extends SuperType {
  constructor(name, age){
    super(name)
    this.age = age;
  }
  sayAge(){console.log(this.age)}
}

var xiaohua = new SubType('xiaohua', 18)
var xiaohong = new SuperType('xiaohong')
xiaohua.sayName()
xiaohua.sayAge()
xiaohua.color.push('green')
console.log(xiaohua.color)
console.log(xiaohong.color)
```
区别：

 1. 在构建构造函数时，不再使用创建`function`的方式，取而代之的是`class`。
 2. ES6在构造函数中的`constructor`中定义的属性和内容，与ES5在构造函数中定义的属性和方法一样，都是私有的指针指向不重复的。
 3. 直接在构造函数中定义的方法，等同于ES5在原型上定义的方法，这些方法对于下级是公用的，指针指向重复的。
 4. 需要注意的是，ES6暂不支持在`class`内部定义属性，所以前面例子中的`size = [1,2,3]`会报错。
 5. 使用`extends`代替之前自己编写的`inheritPrototype`函数，从而实现原型上的方法继承。
 6. 在`extends`中使用`super`来实现构造函数中定义的属性和方法继承，`super()`中只需传入需要传递给父class的变量名称，实现子实例向超类型的构造函数传递参数的功能。

