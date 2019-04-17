#JavaScript

##数据类型
####基本类型(有时又被称为直接量)
1.Number
整形，浮点型，NaN(not a number)，Infinity,-Infinity(正负无穷e)
2.String
utf-16字符集
3.Boolean
只有true，false
####特殊类型
1.Null
本质是空指针，只有一个值就是Null,用于检测一个对象是否被引用
2.Undefined
未定义，一切未声明类型的初值均为undefined
```js
var car;//未声明
alert(bus);//未定义，浏览器会抛出异常：bus is not definded
```
3.Object
其不同于其他语言中的对象：无法新建任何对象，所有的对象均是继承自默认对象.所有的对象都有属性和方法，例如
```js
var a={
    Q:1,
    W:2
};
```
此处的a对象有Q属性和W属性，但是对于每一个普通的属性来说，他们还有自己的属性，通过Object.defineProperty()进行配置：
(1)Configurable:可配置，即意思为可以对对象属性进行修改，包括修改固有属性，删除属性，属于最高的权限，相反，configurable: false即为不可配置在第一次define时定好的属性，默认false
```js
var a = { name: [1, 2, 3, 4, 5] };
Object.defineProperty(a, "name", {
    configurable: true,//可配置
    writable: false//不可写
    });
console.log(a.name);//"array(5)"
a.name = "www";//修改无效
console.log(a.name);//"array(5)"
Object.defineProperty(a, "name", {
            writable: true,//可写
        });
a.name = "www";
console.log(a.name);//"www"
var b = { name: [1, 2, 3, 4, 5] };
Object.defineProperty(b, "name", {
    configurable:false,//不可配置
    writable: false//不可写
    });
Object.defineProperty(b, "name", {
            writable: true,//抛出error，can not redefine
    });
```
(2)writable:见上代码，默认false
(3)enumerable:可枚举，在遍历对象时，决定该属性可不可以显示，如果遍历不可枚举属性将不会显示该属性，不会抛出异常(即使use strict在chrome下也不会异常)。直接调用可以正常使用，默认为true
```js
var a = { };
Object.defineProperty(a, "name", {
    configurable: false,
    enumerable: false,
    writable: false,
    value:[1,2,3,4,5]
});
Object.defineProperty(a, "sky", {
    configurable: false,
    enumerable: true,
    writable: false,
    value:123
});
"use strict"
console.log(a.propertyIsEnumerable(name));//false
for(var x in a)
console.log(x);//sky
console.log(a.name);//array(5)
```
(4)vaule:属性值，默认undefined
(5)存取器属性getter，setter
一般的属性对应的属性值是数，字符，对象，甚至是函数，但是一般的函数会被当做字符串，不会执行，只有get,set属性对应属性值的函数才能被当作函数,其中get是在调用属性时执行的函数，set是在写入
####类型转换
##操作符
同C语言
##语句
for循环
(1)for 同C
(2)for in :一个指定的变量来循环一个对象所有可枚举的属性。JavaScript 会为每一个不同的属性执行指定的语句。
(3)for of :对值的每一个独特的属性调用一个将被执行的自定义的和语句挂钩的迭代。
```js
var a=new Array(1,2,3,4,5,6);
for(let i of a)
    console.log(i);//1,2,3,4,5,6
for(let i in a)
    console.log(i);//0,1,2,3,4,5
var b=new String("qwe qrqr");
for(let i of b)
    console.log(i);//q,w,e, ,q,r,q,r
for(let i in b)
    console.log(i);//0,1,2,3,4,5,6,7
```
可以说区别很明显了，没有用对象举例是因为for of不能用于对象，即使所有的属性可枚举浏览器依旧显示不可举
(4)forEach :只用于遍历数组,效果类似与for of
```js
ArrayName.forEach(element => {
            console.log(element);
        });
```
##对象
####创建对象
1.最基本的方式，对于a对象只有W，Q属性，但是依旧使用可以原型的方法
```js
var a={
    W:2,
    Q:3,
    c:function(){
        this.W=3;
    }
};
console.log(a.W);//2
a.c();
console.log(a.W);//3
```
2.工厂模式
上面第一种最明显的就是不可复用，为了解决这种问题，同时由于ECMAscript无法创建类的特性，就有了以下使用函数来代替类：
```js
function create(name,age,job){
    var o=new Object();
    o.name=name;
    o.job=job;
    o.age=age;
    o.sayhello=function(){
        console.log(this.name+" say hello to you");
    };
    return o;
}
var person1=create("wkq",21,"stduent");
```
3.构造函数模式
新的问题出现了，person1是哪个对象的实例，对于每一实例都有一个构造函数属性(constructor),试一下会发现上例是function Object() { [native code] }，很明显没人能看懂这是哪个对象，因此便有了该模式：
```js
function Person(name,age,job){
    this.age=age;
    this.name=name;
    this.job=job;
    this.sayhello=function(){
        console.log(this.name+" say hello to you");
    };
}
var person1=new Person("wkq",21,"stduent");
console.log(person1.constructor==Person);//true
```
这里有必要说一下构造函数，其本质和普通函数一样，也可以直接但普通函数使用，但其会为window对象创建新的属性（即构造函数内部定义的属性）。
这种模式和我们在C++和Java中的类写法已经非常相似。但是问题是不断地，旧问题解决，就有会有新问题出现：Person对象有age，name,如果新建一个Teacher类，这两个一定会重复，而且在不同的对象变量中会占用不同的资源，JavaScript是一个注重效率的语言，因此就会有以下的新方法
4.原型模式
原型是什么，是一个指针，指向一个对象，而原型是属于函数的属性,且只属于函数，虽然我们仍然可以对其他对象使用console.log(Object.prototype),但这本质浏览器在生成代码时给该对象新定义了prototype属性，其值为undefined。原型模式就是通过函数的原型来避免过多实例里属性的重复。
```js
function Person(){};
Person.prototype.name="wkq";
...//各种属性
var person1=new Person();
```
这里的name属性会在所有的由Person()创建的实例中共享，可能有person1，person2...他们都有name="wkq".很明显，又有问题了，
不可能所有人都叫wkq，那当然也是有解决办法的：
```js
//接上代码
person1.name="weiao";
console.log(person1.name);//weiao
```
我想让person1叫weiao，现在他也确实叫weiao，但有没有可能以后别人都叫weiao了呢？不可能，就是因为指针，我想要改变指针指向的对象只能先用指针找到，再更改，很明显这里并没有这么做。那么这里到底做了什么？这里做了name属性的实例化，也就是说，从这一行代码开始，person1终于有了第一个属性（之前的都是原型的）——name。
基本的原型模式结束了，但是有没有感觉很麻烦，反正我觉得很麻烦，以至于我只写了一个属性，好，新问题来了，怎样让他写着更方便？先想一下为什么觉得麻烦？因为对象可以这么写呀：
```js
var a={
    Q:1,
    W:2,
    E:3
};
```
这方便吧？太方便了。好，就按照这么写：
```js
function Person(){};
Person.prototype={
    name:"wkq",
    age:21,
    job:"stduent"
};
```
但是方便就意味着会有一定的代价，猜一下，代价什么呢？
首先，他和最简单的模式类似，只不过左操作数是原型。那我们知道最基础的就是在最基本的Object上完成的，那同理这里亦是通过Object完成的，换句话说，就是由Object构造器完成的。看到构造器是不是很熟悉，construct，构造器属性。
就是他。如果简写，就会让我们第二步，第三步整个白费。这个代价也太大了，真正的“捡了芝麻丢了西瓜”。好了，解决新问题吧。
```js
function Person(){};
Person.prototype={
    construct:Person,
    name:"wkq",
    age:21,
    job:"stduent"
};
```
行了，解决了，重新实现一下construct属性，将他“实例化”。
整理一下，这里一共牵扯了原型，构造器（函数），实例三者，三者到底怎么组织在一起的？
首先构造器：他只有一个属性：prototype指向原型；
原型：有基本属性（age,name,job...）,还有一个重要属性construct，指向构造器；
实例：可能有自由属性（重写属性实现属性实例化），其与原型属于同一作用域链（在编译原理中具体讨论）可以很简单的理解为实例是被嵌套在原型内部，也就是说实例与构造器没有直接联系。
到此，算是比较清晰地分析了原型模式，之前说过，不存在没有问题的模式，而原型模式的缺点就是他的优点——共享，只要我通过某种手段让实例去改变原型，那么这一整个原型下的实例都会被影响。
(5)
