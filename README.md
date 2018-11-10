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
        objName: 'obj1sven',
         getName: function(){
           return this.objName;
        }
    };
     var obj2 = {
        objName: 'obj2anne'
    };
    console.log( obj1.getName() ); // 输出:obj1sven
    console.log( obj1.getName.call( obj2 ) );// 输出：obj2anne

    var lostThis=obj1.getName;
    console.log( lostThis() ); // 输出:undefined  this指向调用函数的winodw（下文的丢失this现象）
    ```
#### 2.1.2 丢失的this
上文的代码的提到此现象，还比如：
```javascript
var getId = function( id ){
return document.getElementById( id );
};
getId( 'div1' );//正确
//////////////////////////////////
var getId = document.getElementById;
getId( 'div1' ); //报错

//这是因为许多引擎的document.getElementById方法的内部实现中需要用到this。这个this本来被期望指向document，
```
修正this

```javascript
    document.getElementById = (function (func) {
        return function () {
            return func.apply(document, arguments);
        }
    })(document.getElementById);
    //以上的 普通写法是
    // var func=document.getElementById;//先保存原来的，防止回调循环
    // document.getElementById = function () {
    //     return func.apply(document, arguments);
    // };
    var getId = document.getElementById;
    var div = getId('div1');
    console.log(div.id); // 输出： div1
```
### 2.2 call和apply
Function.prototype.call和Function.prototype.apply都是非常常用的方法。它们的作用一模一样，区别仅在于传入参数形式的不同。

当使用call或者apply的时候，如果我们传入的第一个参数为null，函数体内的this会指向默认的宿主对象，在浏览器中则是window。  
（是在严格模式下，函数体内的this还是为null）
```javascript
Math.max.apply( null, [ 1, 2, 5, 3, 4 ] )// 输出：5
```
1. 改变this指向

```javascript

document.getElementById( 'div1' ).onclick =function(){
    var func = function(){
        alert ( this.id ); // 输出：div1
    }
    func.call( this );
};

```
2.  Function.prototype.bind （返回对应函数，便于稍后调用）
模拟实现：
```javascript
Function.prototype.bind = function(){
    var self = this, // 保存原函数
    context = [].shift.call( arguments ),// 需要绑定的this上下文
    args = [].slice.call( arguments );// 剩余的参数转成数组
    return function(){ // 返回一个新的函数
        return self.apply( 
            context,
            [].concat.call( args, [].slice.call( arguments) ) 
        );
        // 执行新的函数的时候，会把之前传入的context当作新函数体内的this
        // 并且组合两次分别传入的参数，作为新函数的参数
    }
};

var obj = {
    name: 'sven'
};
var func = function(){
        alert ( this.name ); // 输出：sven
    }.bind( obj);
func();
```
3. 借用其他对象的方法
    1. 是“借用构造函数”，通过这种技术，可以实现一些类似继承的效果 
    ```javascript
    var A = function( name ){
    this.name = name;
    };
    var B = function(){
    A.apply( this, arguments );  //类似继承
    };
    B.prototype.getName = function(){
    return this.name;
    };
    var b = new B( 'sven' );
    console.log( b.getName() ); // 输出： 'sven'
    ```
    2. 类数组对象arguments借用Array.prototype对象上的方法
    ```javascript
    (function(){
        Array.prototype.push.call( arguments, 3 );
        console.log ( arguments ); // 输出[1,2,3]
    })( 1, 2 );
    ```
## 三、闭包和高阶函数
### 3.1 闭包
闭包（closure）的形成与变量的作用域以及变量的生存周期密切相关。
#### 3.1.1 变量的作用域
是用var关键字在函数中声明变量，这时候的变量即是局部变量
#### 3.1.2 变量的生存周期
```javascript
var func = function(){
    var a = 1; // 退出函数后局部变量a将被销毁
    alert ( a );
};
func();
func();
```
```javascript
var func = function(){
    var a = 1;
    return function(){
        a++;
        alert ( a );
    }
};
var f = func();
f(); // 输出：2
f(); // 输出：3
f(); // 输出：4
f(); // 输出：5
```
f返回了一个匿名函数的引用，它可以访问到func()被调用时产生的环境，而局部变量a一直处在这个环境里。

既然局部变量所在的环境还能被外界访问，这个局部变量就有了不被销毁的理由。在这里产生了一个闭包结构，局部变量的生命看起来被延续了。
```javascript
//批量注册点击事件，用闭包保存每一个i
var nodes =document.getElementsByTagName( 'div' );
for ( var i = 0, len = nodes.length; i< len; i++ ){

    (function( i ){
        nodes[ i ].onclick = function(){
            console.log(i);
        }
    })( i )

};
```
#### 3.1.3 闭包的更多作用
1. 封装变量  
    计算乘积
    ```javascript
    var mult = function(){
        var a = 1;
        for ( var i = 0, l = arguments.length; i <l; i++ ){
            a = a * arguments[i];
        }
        return a;
    };
    ```
    计算乘积，并缓存结果
    ```javascript
    var cache = {};
    var mult = function(){
        var args = Array.prototype.join.call(arguments, ',' );//join 将数组拼接成字符串，以便缓存
        if ( cache[ args ] ){ //有缓存结果
            return cache[ args ];
        }
        var a = 1;
        for ( var i = 0, l = arguments.length; i <l; i++ ){
            a = a * arguments[i];
        }
        return cache[ args ] = a; //缓存结果
    };
    alert ( mult( 1,2,3 ) ); // 输出：6
    alert ( mult( 1,2,3 ) ); // 输出：6
    ```
    计算乘积，并缓存结果，将cache缓存封闭进计算函数中，避免被修改
    ```javascript
    var mult = (function(){
        var cache = {};
        return function(){
            var args = Array.prototype.join.call(arguments, ',' );
            if ( args in cache ){
                return cache[ args ];
            }
            var a = 1;
            for ( var i = 0, l = arguments.length;i < l; i++ ){
                a = a * arguments[i];
            }
            return cache[ args ] = a;
        }
    })();
    ```
    计算乘积，并缓存结果，将cache缓存封闭进计算函数中，避免被修改  
    封闭独立的代码块=>小函数  
    如果这些小函数不需要在程序的其他地方使用，最好是把它们用闭包封闭起来
    ```javascript
    var mult = (function(){
        var cache = {};
        var calculate = function(){ // 封闭calculate函数
            var a = 1;
            for ( var i = 0, l = arguments.length;
            i < l; i++ ){
            a = a * arguments[i];
            }
            return a;
        };
        return function(){
            var args = Array.prototype.join.call(arguments, ',' );
            if ( args in cache ){
                return cache[ args ];
            }
            return cache[ args ] =calculate.apply( null, arguments );
        }
    })();

    ```
2. 延续局部变量的寿命
    以下report函数并不是每一次都成功发起了HTTP请求。
    ```javascript
    var report = function( src ){
        var img = new Image();
        img.src = src;
    };
    report( 'http://baidu.com/getUserInfo' );
    ```
    丢失数据的原因是img是report函数中的局部变量，当report函数的调用结束后，img局部变量随即被销毁，而此时或许还没来得及发出HTTP请求，所以此次请求就会丢失掉。在不使用全局变量的情况下，用闭包保存变量：
    ```javascript
    var report = (function(){
        var imgs = [];
        return function( src ){
            var img = new Image();
            imgs.push( img );
            img.src = src;
        }
    })();
    ```
#### 3.1.4 闭包和面向对象设计
1. 闭包写法  
    ```javascript
    var extent = function(){
        var value = 0;
        return {
            call: function(){
                value++;
                console.log( value );
            }
        }
    };
    var extent = extent();
    extent.call(); // 输出：1
    extent.call(); // 输出：2
    extent.call(); // 输出：3
    ```
2. 面向对象——字面量
    ```javascript
    var extent = {
        value: 0,
        call: function(){
            this.value++;
            console.log( this.value );
        }
    };
    extent.call(); // 输出：1
    extent.call(); // 输出：2
    extent.call(); // 输出：3

    ```
3. 面向对象——构造函数
    ```javascript
    var Extent = function(){
        this.value = 0;
    };
    Extent.prototype.call = function(){
        this.value++;
        console.log( this.value );
    };
    var extent = new Extent();
    extent.call(); // 输出：1
    extent.call(); // 输出：2
    extent.call(); // 输出：3
    ```
#### 3.1.5 用闭包实现命令模式
命令模式的意图是把请求封装为对象，从而分离请求的发起者和请求的接收者（执行者）之间的耦合关系。在命令被执行之前，可以预先往命令对象中植入命令的接收者。但在JavaScript中，函数作为一等对象，本身就可以四处传递，用函数对象而不是普通对象来封装请求显得更加简单和自然。如果需要往函数对象中预先植入命令的接收者，那么闭包可以完成这个工作。在面向对象版本的命令模式中，预先植入的命令接收者被当成对象的属性保存起来；而在闭包版本的命令模式中，命令接收者会被封闭在闭包形成的环境中，代码如下：
```javascript
var Tv = {
    open: function () {
        console.log('打开电视机');
    },
    close: function () {
        console.log('关上电视机');
    }
};
//////////////// 面向对象的方式 ////////////////////////////
var OpenTvCommand = function (receiver) {                //
    this.receiver = receiver;                            //
};                                                       //
OpenTvCommand.prototype.execute = function () {          //
    this.receiver.open(); // 执行命令，打开电视机          //
};                                                       //
OpenTvCommand.prototype.undo = function () {             //
    this.receiver.close(); // 撤销命令，关闭电视机         //
};                                                       //
/////////////////// 闭包的方式 /////////////////////////////
var createCommand = function (receiver) {                //
    var execute = function () {                          //
        return receiver.open(); // 执行命令，打开电视机    //
    }                                                    //
    var undo = function () {                             //
        return receiver.close(); // 执行命令，关闭电视机   //
    }                                                    //
    return {                                             //
        execute: execute,                                //
        undo: undo                                       //
    }                                                    //
};                                                       //
///////////////////////////////////////////////////////////

var setCommand = function (command) {
    document.getElementById('execute').onclick = function () {
        command.execute(); // 输出：打开电视机
    }
    document.getElementById('undo').onclick = function () {
        command.undo(); // 输出：关闭电视机
    }
};
setCommand(new OpenTvCommand(Tv));
setCommand(createCommand(Tv));
```

#### 3.1.6 闭包与内存管理

闭包是一个非常强大的特性，但人们对其也有诸多误解。一种耸人听闻的说法是闭包会造成内存泄露，所以要尽量减少闭包的使用。

局部变量本来应该在函数退出的时候被解除引用，但如果局部变量被封闭在闭包形成的环境中，那么这个局部变量就能一直生存下去。从这个意义上看，闭包的确会使一些数据无法被及时销毁。

使用闭包的一部分原因是我们选择主动把一些变量封闭在闭包中，因为可能在以后还需要使用这些变量，把这些变量放在闭包中和放在全局作用域，对内存方面的影响是一致的，这里并不能说成是内存泄露。

`如果在将来需要回收这些变量，我们可以手动把这些变量设为null。`

跟闭包和内存泄露有关系的地方是，使用闭包的同时比较容易形成`循环引用`，如果闭包的作用域链中保存着一些DOM节点，这时候就有可能造成`内存泄露`。

`但这本身并非闭包的问题，也并非JavaScript的问题。`

在IE浏览器中，由于BOM和DOM中的对象是使用C++以COM对象的方式实现的，而COM对象的垃圾收集机制采用的是引用计数策略。在基于引用计数策略的垃圾回收机制中，如果两个对象之间形成了循环引用，那么这两个对象都无法被回收，但`循环引用造成的内存泄露在本质上也不是闭包造成的`。同样，如果要解决循环引用带来的内存泄露问题，我们只需要把循环引用中的变量设为null即可。将变量设置为null意味着切断变量与它此前引用的值之间的连接。当垃圾收集器下次运行时，就会删除这些值并回收它们占用的内存。

### 3.2 高阶函数
高阶函数是指至少满足下列条件之一的函数。
* 函数可以作为参数被传递；
* 函数可以作为返回值输出。

JavaScript语言中的函数显然满足高阶函数的条件，在实际开发中，无论是将函数当作参数传递，还是让函数的执行结果返回另外一个函数，这两种情形都有很多应用场景，下面就列举一些高阶函数的应用场景。

#### 3.2.1 函数可以作为参数被传递
1. 回调函数
    * 如ajax异步请求
    * 事件委托
    ```javascript
    var appendDiv = function(){
    for ( var i = 0; i < 100; i++ ){
        var div = document.createElement('div' );
        div.innerHTML = i;
        document.body.appendChild( div );
        div.style.display = 'none'; // 事件不具通用性
        }
    };
    appendDiv();
    ```
    因为div.style.display = 'none'，函数难以复用，封装委托成回调
    ```javascript
    var appendDiv = function( callback ){
        for ( var i = 0; i < 100; i++ ){
            var div = document.createElement('div' );
            div.innerHTML = i;
            document.body.appendChild( div );
            if ( typeof callback === 'function' ){
                callback( div );
            }
        }
    };
    appendDiv(function( node ){node.style.display = 'none';});

    ```
2. Array.prototype.sort
    Array.prototype.sort接受一个函数当作参数，这个函数里面封装了数组元素的排序规则。
    * 不变的部分：目的是对数组进行排序；
    * 可变的部分：使用什么规则去排序。  
    把可变的部分封装在函数参数里，动态传入Array.prototype.sort，使Array.prototype.sort方法成为了一个非常灵活的方法，
    ```javascript
    //从小到大排列
    [ 1, 4, 3 ].sort( function( a, b ){
        return a - b;
    });
    // 输出: [ 1, 3, 4 ]
    ```
#### 3.2.2 函数可以作为返回值输出
函数当作返回值输出的应用场景也许更多，也更能体现函数式编程的巧妙。让函数继续返回一个可执行的函数，意味着运算过程是可延续的。
1. 判断数据的类型
    ```javascript
    var isString = function( obj ){
        return Object.prototype.toString.call( obj) === '[object String]';
    };
    var isArray = function( obj ){
        return Object.prototype.toString.call( obj) === '[object Array]';
    };
    var isNumber = function( obj ){
        return Object.prototype.toString.call( obj) ==='[object Number]';
    };
    ```
    三个函数有相同（不变）的地方。可以用一个函数返回这三个函数
    ```javascript
    var isType = function( type ){
        return function( obj ){
            return Object.prototype.toString.call(obj ) === '[object '+ type +']';
        }
    };
    var isString = isType( 'String' );
    var isArray = isType( 'Array' );
    var isNumber = isType( 'Number' );
    console.log( isArray( [ 1, 2, 3 ] ) ); //输出：true

    ```
    再用一个循环，批量注册
    ```javascript
    var Type = {};
    for ( var i = 0, type; type = [ 'String','Array', 'Number' ][ i++ ]; ){
        (function( type ){
            Type[ 'is' + type ] = function( obj ){
                return Object.prototype.toString.call( obj ) ==='[object '+ type +']';
            }
        })( type )
    };
    Type.isArray( [] ); // 输出：true
    Type.isString( "str" ); // 输出：true
    ```
2. getSingle
    下面是一个单例模式的例子,也是一个高阶函数的例子，既把函数当作参数传递，又让函数执行后返回了另外一个函数。
    ```javascript
    var getSingle = function (fn) {
        var ret;
        return function () {
            return ret || (ret = fn.apply(this,arguments));
        };
    };
    var getScript = getSingle(function () {
        return document.createElement('script');
    });
    var script1 = getScript();
    var script2 = getScript();
    console.log(script1 === script2); // 输出：true    
    ////////////////  不使用闭包时，是不同的 //////////////////
    var getScript2 = function () {
        return document.createElement('script');
    };
    var script11 = getScript2();
    var script22 = getScript2();
    console.log(script11 === script22); // 输出：false 
    ```
#### 3.2.3 高阶函数实现AOP
AOP（面向切面编程）的主要作用是把一些跟核心业务逻辑模块无关的功能抽离出来，这些跟业务逻辑无关的功能通常包括日志统计、安全控制、异常处理等。
把这些功能抽离出来之后，再通过“动态织入”的方式掺入业务逻辑模块中。
这样做的好处首先是可以保持业务逻辑模块的纯净和高内聚性，其次是可以很方便地复用日志统计等功能模块。

在Java语言中，可以通过反射和动态代理机制来实现AOP技术。  
而在JavaScript这种动态语言中，AOP的实现更加简单，这是JavaScript与生俱来的能力。  
通常，在JavaScript中实现AOP，都是指把一个函数“动态织入”到另外一个函数之中，具体的实现技术有很多，本节我们通过扩展Function.prototype来做到这一点。代码如下：
# `看不太懂 啊啊啊`
```javascript
function myLog(w) {
    console.log("myLog:" + w);
}
Function.prototype.before = function (beforefn) {
    var __self = this; // 保存原函数的引用
    return function () { // 返回包含了原函数和新函数的"代理"函数
        beforefn.apply(this, arguments);// 执行新函数，修正this
        return __self.apply(this, arguments); // 执行原函数

    }
};
Function.prototype.after = function (afterfn) {
    var __self = this;
    return function () {
        var ret = __self.apply(this, arguments);
        afterfn.apply(this, arguments);
        return ret;
    }
};
var func = function () {
    console.log("running");
};

func = func.before(function () {
    myLog("before function")
}).after(function () {
    myLog("after function")
});

func();
```
我们把负责打印数字1和打印数字3的两个函数通过AOP的方式动态植入func函数。通过执行上面的代码，我们看到控制台顺利地返回了执行结果1、2、3。
这种使用AOP的方式来给函数添加职责，也是JavaScript语言中一种非常特别和巧妙的装饰者模式实现。这种装饰者模式在实际开发中非常有用

#### 3.2.4 高阶函数的其他应用
1. currying

    currying又称部分求值。一个currying的函数首先会接受一些参数，接受了这些参数之后，该函数并不会立即求值，而是继续返回另外一个函数，刚才传入的参数在函数形成的闭包中被保存起来。待到函数被真正需要求值的时候，之前传入的所有参数都会被一次性用于求值。
    假设我们要编写一个计算每月开销的函数。在每天结束之前，我们都要记录今天花掉了多少钱。代码如下：
    ```javascript
    //通用的functioncurrying(){}，function currying(){}接受一个参数，即将要被currying的函数。
    var currying = function (fn) {
        var args = [];
        console.log(this);//调用currying的是window
        return function () {
            console.log(this);//调用fn的是xiaoMing
            if (arguments.length === 0) {
                // 当没有参数传入时，计算之前保存的所有参数
                // switch (args.length) {
                //     case 0:
                //         return fn()
                //         break;
                //     case 1:
                //         return fn(args[0])
                //         break;
                //     case 2:
                //         return fn(args[0], args[1])
                //         break;
                //     case 3:
                //         return fn(args[0], args[1], args[2])
                //         break;
                //     case 4:
                //         return fn(args[0], args[1], args[2], args[3])
                //         break;
                //      //   ……
                // }
                //因为参数是保存在数组里，且不确定是几个参数，所以用apply将整个数组传入
                return fn.apply(this, args);//修改fn的this指向为xiaoMing，并运行fn（即xiaoMing.cost）
            } else {
                //将类数组arguments里的参数传入args保存，用apply调用数组的push方法
                [].push.apply(args, arguments);
                //返回本函数
                return arguments.callee;
            }
        }
    };
    ```
    使用例子如下
    ```javascript
    var xiaoMing = {
        name: "小明",
        cost: (function () {
            var money = 0;
            return function () {
                for (var i = 0, l = arguments.length;
                    i < l; i++) {
                    money += arguments[i];
                }
                return money;
            }
        })()
    };

    xiaoMing.cost = currying(xiaoMing.cost);// 转化成currying函数
    xiaoMing.cost(2); // 未真正求值
    xiaoMing.cost(4); // 未真正求值
    xiaoMing.cost(6); // 未真正求值
    console.log(xiaoMing.cost()); // 求值并输出：12
    ```
2. uncurrying

    在JavaScript中，当我们调用对象的某个方法时，其实不用去关心该对象原本是否被设计为拥有这个方法，这是动态类型语言的特点，也是常说的鸭子类型思想。
    同理，一个对象也未必只能使用它自身的方法，那么有什么办法可以让对象去借用一个原本不属于它的方法呢？
    答案对于我们来说很简单，call和apply都可以完成这个需求：
    ```javascript
    var obj1 = {
        name: 'sven'
    };
    var obj2 = {
        getName: function(){
            return this.name;
        }
    };
    console.log( obj2.getName.call( obj1 ) );// 输出：sven

    ```
    我们常常让类数组对象去借用Array.prototype的方法，这是call和apply最常见的应用场景之一
    ```javascript
    (function(){
        Array.prototype.push.call( arguments, 4 );// arguments借用Array.prototype.push方法
        console.log( arguments ); // 输出：[1,2, 3, 4]
    })( 1, 2, 3 );
    ```
    `方法中用到this的地方就不再局限于原来规定的对象，而是加以泛化并得到更广的适用性。`

    `把泛化this的过程提取出来，就是uncurrying`
    ```javascript
    Function.prototype.uncurrying = function () {
        var self = this; //保留一下原生的方法（数组的push/shift foreach）
        return function () {
            //获取第一个参数（这应该是一个对象）
            var obj = Array.prototype.shift.call(arguments);//返回值数组原来的第一个元素的值。
            return self.apply(obj, arguments);
        };
    };
    ```
    uncurrying的另一种实现方式
    ```javascript
    Function.prototype.uncurrying = function(){
        var self = this;
        return function(){
            return Function.prototype.call.apply(self, arguments );
            //0、将参数代进来理解
            //Function.prototype.call.apply(Array.prototype.push, [obj, 2] );
            //1、apply替换的执行函数的对象并扁平化了数组内容，
            //Array.prototype.push.call(obj, 2);
            //2、call函数将数组内容的第一个参数替换执行函数对象
            //obj.push(2);
        }
    };
    ```
    使用如下：(通过uncurrying的方式，Array.prototype.push.call变成了一个通用的push函数)
    ```javascript
    var push = Array.prototype.push.uncurrying();
    (function(){
        push( arguments, 4 );
        console.log( arguments ); // 输出：[1,2, 3, 4]
    })( 1, 2, 3 );
    ```
    一次性地把Array.prototype上的方法“复制”到array对象上
    ```javascript
    //赋值语句会返回那个值，当值不存在时返回undefined，转成boolean就是false，跳出循环。
    for ( var i = 0, fn, ary = [ 'push', 'shift','forEach' ]; fn = ary[ i++ ]; ){
        Array[fn] = Array.prototype[fn].uncurrying();
    };
    var obj = {
        "length": 3,
        "0": 1,
        "1": 2,
        "2": 3
    };
    Array.push( obj, 4 ); // 向对象中添加一个元素
    console.log( obj.length ); // 输出：4
    var first = Array.shift( obj ); // 截取第一个元素
    console.log( first ); // 输出：1
    console.log( obj ); // 输出：{0: 2, 1: 3, 2:4, length: 3}
    Array.forEach( obj, function( i, n ){
    console.log( n ); // 分别输出：0, 1, 2
    });
    ```