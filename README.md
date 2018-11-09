# js 设计模式

## 面向对象的JavaScript


### js是动态类型语言 & 鸭子类型（只要function/action对）

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
### 多态

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

#### 多态之作用

多态背后的思想是将“做什么”和“谁去做以及怎样去做”分离开来

多态最根本的作用就是通过把过程化的条件分支语句转化为对象的多态性，从而消除这些条件分支语句

#### 多态之隔离固定
```javascript
var makeSound = function( animal ){
    animal.sound();
};
```
#### 多态之封装变化
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
#### 多态之外部调用
正是由于js的动态类型，不检查创建类型、参数类型
animal即可以是duck，也可以是chicken，只要有固定的方法sound，即可调用成功
```javascript
makeSound( new Duck() ); // 嘎嘎嘎
makeSound( new Chicken() ); // 咯咯咯
```

### 封装
封装的目的是将信息隐藏。
不仅包括封装数据和封装实现，还包括封装类型和封装变化。

#### 封装数据
们只能依赖变量的作用域来实现封装特性，而且只能模拟出public和private这两种封装性。

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
#### 封装实现
例：each函数，它的作用就是遍历一个聚合对象，使用这个each函数的人不用关心它的内部是怎样实现的，只要它提供的功能正确便可以。即使each函数修改了内部源代码，只要对外的接口或者调用方式没有变化，用户就不用关心它内部实现的改变。

#### 封装类型
是静态类型语言中一种重要的封装方式，JavaScript由于动态类型、模糊类型的特性，没有能力，也没有必要做
#### 封装变化
>找到变化并封装之
>      ——《设计模式》 

通过封装变化的方式，把系统中稳定不变的部分和容易变化的部分隔离开来，在系统的演变过程中，我们只需要替换那些容易变化的部分,如果这些部分是已经封装好的，替换起来也相对容易。这可以最大程度地保证程序的稳定性和可扩展性。 

    `主要目的是提高可复用性`

### 原型模式和基于原型继承的JavaScript对象系统
####  使用克隆的原型模式
```javascript
var clonePlane = Object.create( plane );
//在不支持Object.create方法的浏览器中，则可以使用以下代码：
Object.create = Object.create || function( obj){
    var F = function(){};
    F.prototype = obj;
    return new F();
}
```
#### 克隆是创建对象的手段
原型模式提供了另外一种创建对象的方式，通过
克隆对象，我们就不用再关心对象的具体类型名
字。这就像一个仙女要送给三岁小女孩生日礼
物，虽然小女孩可能还不知道飞机或者船怎么
说，但她可以指着商店橱柜里的飞机模型说“我
要这个”。
当然在JavaScript这种类型模糊的语言中，创建对
象非常容易，也不存在类型耦合的问题。从设计
模式的角度来讲，原型模式的意义并不算大 。但
JavaScript本身是一门基于原型的面向对象语言，
它的对象系统就是使用原型模式来搭建的，在这
里称之为原型编程范型也许更合适。

#### 原型编程范型的一些规则
* __所有的数据都是对象。__  
JavaScript在设计的时候，模仿Java引入了两套类型机制：基本类型和对象类型。  
基本类型包括undefined、number、boolean、string、function  
    `JavaScript中的根对象是Object.prototype对象`  
可以利用ECMAScript 5提供的Object.getPrototypeOf来查看这两个对象的原型：  
```javascript
console.log( Object.getPrototypeOf( obj1 ) ===Object.prototype ); // 输出：true
console.log( Object.getPrototypeOf( obj2 ) ===Object.prototype ); // 输出：true
```
* __要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它。__
* __对象会记住它的原型。__
* __如果对象无法响应某个请求，它会把这个请求委托给它自己的原型。__
