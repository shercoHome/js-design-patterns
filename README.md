# js 设计模式

## 一、面向对象的JavaScript


### 1.1 js是动态类型语言 & 鸭子类型（只要function/action对）

```javascript
//会嘎嘎叫，就可以加入合唱团，不管是什么类型
var duck = {
    duckSinging: function(){
    console.log( '嘎嘎嘎' );
}
};
var chicken = {
    duckSinging: function(){
    console.log( '嘎嘎嘎' );
}
};
var choir = []; // 合唱团
var joinChoir = function( animal ){
    if ( animal && typeof animal.duckSinging=== 'function' ){
        choir.push( animal );
        console.log( '恭喜加入合唱团' );
        console.log( '合唱团已有成员数量:' +
        choir.length );
    }
};
joinChoir( duck ); // 恭喜加入合唱团
joinChoir( chicken ); // 恭喜加入合唱团
 ```
### 1.2 多态

“多态”一词源于希腊文polymorphism，拆开来看是poly（复数）+ morph（形态）+ ism，从字面上我们可以理解为复数形态。

同一操作作用于不同的对象上面，可以产生不同的解释和不同的执行结果。

```javascript
var makeSound = function( animal ){
    if ( animal instanceof Duck ){
        console.log( '嘎嘎嘎' );
    }else if ( animal instanceof Chicken ){
        console.log( '咯咯咯' );
    }
};
var Duck = function(){};
var Chicken = function(){};
makeSound( new Duck() ); // 嘎嘎嘎
makeSound( new Chicken() ); // 咯咯咯

```

#### 1.2.1 多态之作用

多态背后的思想是将“做什么”和“谁去做以及怎样去做”分离开来

多态最根本的作用就是通过把过程化的条件分支语句转化为对象的多态性，从而消除这些条件分支语句

#### 1.2.2 多态之隔离固定

```javascript
var makeSound = function( animal ){
    animal.sound();
};
```
#### 1.2.3 多态之封装变化

```javascript
var Duck = function(){}
Duck.prototype.sound = function(){
console.log( '嘎嘎嘎' );
};
var Chicken = function(){}
Chicken.prototype.sound = function(){
console.log( '咯咯咯' );
};
```
#### 1.2.4 多态之外部调用

正是由于js的动态类型，不检查创建类型、参数类型
animal即可以是duck，也可以是chicken，只要有固定的方法sound，即可调用成功
```javascript
makeSound( new Duck() ); // 嘎嘎嘎
makeSound( new Chicken() ); // 咯咯咯
 ```

### 1.3 封装

封装的目的是将信息隐藏。
不仅包括封装数据和封装实现，还包括封装类型和封装变化。

#### 1.3.1 封装数据
我们只能依赖变量的作用域来实现封装特性，而且只能模拟出public和private这两种封装性。  
通过函数来创建作用域(ECMAScript 6中提供let)  

```javascript
var myObject = (function(){

    var __name = 'sven'; // 私有（private）变量

    return {
        getName: function(){ // 公开（public）方法
            return __name;
        }
    }
})();

console.log( myObject.getName() ); // 输出：sven
console.log( myObject.__name ) // 输出：undefined

```
#### 1.3.2 封装实现
例：each函数，它的作用就是遍历一个聚合对象，使用这个each函数的人不用关心它的内部是怎样实现的，只要它提供的功能正确便可以。即使each函数修改了内部源代码，只要对外的接口或者调用方式没有变化，用户就不用关心它内部实现的改变。

#### 1.3.3 封装类型
是静态类型语言中一种重要的封装方式，JavaScript由于动态类型、模糊类型的特性，没有能力，也没有必要做

#### 1.3.4 封装变化
>找到变化并封装之
>      ——《设计模式》 

通过封装变化的方式，把系统中稳定不变的部分和容易变化的部分隔离开来，在系统的演变过程中，我们只需要替换那些容易变化的部分,如果这些部分是已经封装好的，替换起来也相对容易。这可以最大程度地保证程序的稳定性和可扩展性。 

    `主要目的是提高可复用性`

### 1.4 原型模式和基于原型继承的JavaScript对象系统
#### 1.4.1 使用克隆的原型模式

 ```javascript
var clonePlane = Object.create( plane );
//在不支持Object.create方法的浏览器中，则可以使用以下代码：
Object.create = Object.create || function( obj){
    var F = function(){};
    F.prototype = obj;
    return new F();
}
 ```
#### 1.4.2 克隆是创建对象的手段
原型模式提供了另外一种创建对象的方式，通过克隆对象，我们就不用再关心对象的具体类型名字。这就像一个仙女要送给三岁小女孩生日礼物，虽然小女孩可能还不知道飞机或者船怎么说，但她可以指着商店橱柜里的飞机模型说“我要这个”。
当然在JavaScript这种类型模糊的语言中，创建对象非常容易，也不存在类型耦合的问题。从设计模式的角度来讲，原型模式的意义并不算大 。但JavaScript本身是一门基于原型的面向对象语言，它的对象系统就是使用原型模式来搭建的，在这里称之为原型编程范型也许更合适。

#### 1.4.3 JavaScript中的原型继承（原型编程范型的一些规则）

* 所有的数据都是对象。
* 要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它。
* 对象会记住它的原型。
* 如果对象无法响应某个请求，它会把这个请求委托给它自己的原型。

    1. __所有的数据都是对象。__  

    JavaScript在设计的时候，模仿Java引入了两套类型机制：基本类型和对象类型。  
    基本类型包括undefined、number、boolean、string、function  

        `JavaScript中的根对象是Object.prototype对象`  

    可以利用ECMAScript 5提供的Object.getPrototypeOf来查看这两个对象的原型：  
    ```javascript
    console.log( Object.getPrototypeOf( obj1 ) ===Object.prototype ); // 输出：true
    console.log( Object.getPrototypeOf( obj2 ) ===Object.prototype ); // 输出：true
    ```
    2. __要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它。__  

    是显式地调用var obj1 = new Object()或者var obj2 = {}。
    此时，引擎内部会从Object.prototype上面克隆一个对象出来，
    我们最终得到的就是这个对象。  
    例：  
    ```javascript
    function Person( name ){
        this.name = name;
    };
    Person.prototype.getName = function(){
        return this.name;
    };
    var a = new Person( 'sven' )
    console.log( a.name ); // 输出：sven
    console.log( a.getName() ); // 输出：sven
    console.log( Object.getPrototypeOf( a ) ===Person.prototype ); // 输出：true
    ```

    `在JavaScript中没有类的概念，这句话我们已经重
    复过很多次了。但刚才不是明明调用了new
    Person()吗？`  

    在这里Person并不是类，而是函数构造器，JavaScript的函数既可以作为普通函数被调用，也可以作为构造器被调用。**当使用new运算符来调用函数时，此时的函数就是一个构造器。** 用new 运算符来创建对象的过程，实际上也只是先克隆Object.prototype对象，再进行一些其他额外操作的过程。

    js 的new，过程如下：
    ```javascript
    function Person(name) {
            this.name = name;
    ;
    Person.prototype.getName = function () {
            return this.name;
    };
    var objectFactory = function () {
            var obj = new Object(); // 从 Object.prototype上克隆一个空的对象
            //shift  返回数组原来的第一个元素的值。
            //call() 它的第一个参数用作 this 的对象。其他参数都直接传递给函数自身
            //原本是对[]进行shift操作，由于call存在，shift的操作对象this指向了 arguments
            //arguments.shift(), 直接操作报错，因为arguments不是一个真正的数组
            //[],空数组，只是为了调用shift方法, 可以是任意数组，如[666,777,888]
            //其实相当于 Constructor = arguments[0]，区别在于，使用shift后，原对象arguments改变了
            var Constructor = [].shift.call(arguments); // 取得外部传入的构造器，此例是Person

            obj.__proto__ = Constructor.prototype;
            // 指向正确的原型 通过这句代码，我们让obj.__proto__ 指向Person.prototype，而不是原来的Object.prototype。
            //__proto__就是对象跟“对象构造器的原型”联系起来的纽带

            //apply() 方法有两个参数，用作 this 的对象和要传递给函数的参数的数组
            //arguments 是被shift改变后的参数，去除了Person，只剩下参数
            var ret = Constructor.apply(obj, arguments); // 借用外部传入的构造器给obj设置属 性
            return typeof ret === 'object' ? ret : obj; // 确保构造器总是会返回一个对象
    };
    var a = objectFactory(Person, 'sven');
    console.log(Object.getPrototypeOf(a) === Person.prototype); // 输出：true

    //此时，以下两行代码的效果是一致的
    var a = objectFactory( A, 'sven' );
    var a = new A( 'sven' );

    ```
    3. __对象会记住它的原型。__  

    “对象的原型”，就JavaScript的真正实现来说，其实并不能说对象有原型，而只能说对象的构造器有原型。

    JavaScript给对象提供了一个名为__proto__的隐藏属性，某个对象的__proto__属性默认会指向它的构造器的原型对象

    ```javascript
    var a = new Object();
    console.log ( a.__proto__=== Object.prototype); // 输出：true
    ```
    实际上，__proto__就是对象跟“对象构造器的原型”联系起来的纽带。正因为对象要通过__proto__属性来记住它的构造器的原型，所以我们用上一节的objectFactory函数来模拟用new创建对象时， 需要手动给obj对象设置正确的__proto__指向。

     4. __如果对象无法响应某个请求，它会把这个请求委托给它自己的原型。__  

    “对象把请求委托给它自己的原型”这句话，更好的说法是对象把请求委托给它的构造器的原型。

    `这条规则即是原型继承的精髓所在。`

    在JavaScript中，每个对象都是从Object.prototype对象克隆而来的，如果是这样的话，我们只能得到单一的继承关系，即每个对象都继承自Object.prototype对象，这样的对象系统显然是非常受限的。  
    实际上，虽然JavaScript的对象最初都是由Object.prototype对象克隆而来的，但对象构造器的原型并不仅限于Object.prototype上，而是可以动态指向其他对象。  
    这样一来，**当对象a需要借用对象b的能力时，可以有选择性地把对象a的构造器的原型指向对象b，从而达到继承的效果。**下面的代码是我们最常用的原型继承方式：
    ```javascript
    var obj = { name: 'sven' };
    var A = function(){};
    A.prototype = obj;
    var a = new A();
    console.log( a.name ); // 输出：sven
    ```
    浏览器引擎工作步骤  

    * 首先，尝试遍历对象a中的所有属性，但没有找到name这个属性。
    * 查找name属性的这个请求被委托给对象a的构造器的原型，它被a.__proto__ 记录着并且指向A.prototype，而A.prototype被设置为对象obj。
    * 在对象obj中找到了name属性，并返回它的值。

    ```javascript
    var A = function(){};
    A.prototype = { name: 'sven' };
    var B = function(){};
    B.prototype = new A();
    var b = new B();
    console.log( b.name ); // 输出：sven
    ```
    浏览器引擎工作步骤
    * 首先，尝试遍历对象b中的所有属性，但没有找到name这个属性。
    * 查找name属性的请求被委托给对象b的构造器的原型，它被b.__proto__ 记录着并且指向B.prototype，而B.prototype被设置为一个通过new A()创建出来的对象。
    * 在该对象中依然没有找到name属性，于是请求被继续委托给这个对象构造器的原型A.prototype。
    * 在A.prototype中找到了name属性，并返回它的值。

    `给A.prototype的构造器原型Object.prototype，Object.prototype的原型是null，`

#### 1.4.4 ECMAScript 6 实现的继承（实际也是通过原型继承）

```javascript
class Animal {
    constructor(name) {
        this.name = name;
    }
    getName() {
        return this.name;
    }
}
class Dog extends Animal {
    constructor(name) {
        super(name);//super关键字用于访问和调用一个对象的父对象上的函数。
    }
    speak() {
        return "woof";
    }
}
var dog = new Dog("Scamp");
console.log(dog.getName() + ' says ' +dog.speak());
```

## 二、this call 和 apply

### 2.1 this

跟别的语言大相径庭的是，JavaScript的this总是指向一个对象，而具体指向哪个对象是在运行时基于函数的执行环境动态绑定的，而非函数被声明时的环境。

#### 2.1.1 this的指向  

`除去不常用的with和eval的情况，具体到实际应用中，this的指向大致可以分为以下4种。`
    
* 作为对象的方法调用。指向该对象
    ``` javascript
    var obj = {
        a: 1,
        getA: function(){
            console.log ( this === obj ); // 输出：true
            console.log ( this.a ); // 输出: 1
        }
    };
    obj.getA();

    ```
* 作为普通函数调用。相当于是window的方法,指向window对象
    ``` javascript
    window.name = 'globalName';
    var myObject = {
        name: 'sven',
        getName: function(){
            console.log(  this.name ); 
        }
    };
    myObject.getName();//sven

    var getName = myObject.getName;
    getName();//globalName
    //相当于 window.getName=myObject.getName;
    //相当于 window.getName();
    ```
* 构造器调用。
    * 当用new运算符调用函数时，该函数总会返回一个对象，通常情况下，构造器里的this就指向返回的这个对象
    ``` javascript
    //构造器的外表跟普通函数一模一样，它们的区别在于被调用的方式
    function MyClassHello(){
        this.name2 = 'Hello';
    };
    var obj = new MyClassHello();
    console.log ( MyClassHello.name ); // 输出：MyClassHello
    console.log ( MyClassHello.name2 ); // 输出：undefined
    console.log ( obj.name ); // 输出：undefined
    console.log ( obj.name2 ); // 输出：Hello
    ```
    * 如果构造器显式地返回了一个object类型的对象，那么此次运算结果最终会返回这个对象
    ``` javascript
    var MyClass = function(){
    this.name = 'sven';
    return { // 显式地返回一个对象
    name: 'anne'
    }
    };
    var obj = new MyClass();
    alert ( obj.name ); // 输出：anne
    ```
* Function.prototype.call或Function.prototype.apply调用，动态地改变传入函数的this：。
    ```javascript
    var obj1 = {
        name: 'sven',
         getName: function(){
           return this.name;
        }
    };
     var obj2 = {
        name: 'anne'
    };
    console.log( obj1.getName() ); // 输出:sven
    console.log( obj1.getName.call( obj2 ) );// 输出：anne
    ```