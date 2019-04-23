# JavaScript

## 数据类型
#### 基本类型(有时又被称为直接量)
1. Number
整形，浮点型，NaN(not a number)，Infinity,-Infinity(正负无穷e)
2. String
utf-16字符集
3. Boolean
只有true，false
#### 特殊类型
1. Null
本质是空指针，只有一个值就是Null,用于检测一个对象是否被引用
2. Undefined
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
#### 类型转换
## 操作符
同C语言
## 语句
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
## 对象
#### 创建对象
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
实例：可能有自由属性（重写属性实现属性实例化），其与原型属于同一原型链（在继承中具体讨论）可以很简单的理解为实例是被嵌套在原型内部，也就是说实例与构造器没有直接联系。
到此，算是比较清晰地分析了原型模式，之前说过，不存在没有问题的模式，而原型模式的缺点就是他的优点——共享，只要我通过某种手段让实例去改变原型，那么这一整个原型下的实例都会被影响。
(5)最少在我看来上述问题已经属于强行找问题了，换句话说，一眼就能看出的问题已经基本没有了。但是还存在一些习惯问题，就例如上个问题：
###### 组合使用构造函数和原型：
```js
function Person(name,age,job){
    this.name=name;
    this.age=age;
    this.job=job;
}
Person.prototype={
    constructor:Person,
    sayHello:function(){
        console.log(this.name);
    }
}
```
就很直接地把共享属性和自有属性分开。但是有个问题，你的代码给别人看的时候很有可能别人看不懂：
```C++
class Student{
public:
    //成员变量
    char *name;
    int age;
    float score;
    //成员函数
    void say(){
        cout<<name<<"的年龄是"<<age<<"，成"<<score<<endl;
    }
};
```
```Java
public class Student {

    private int id;

    public Student(Integer id) {
        this.id = id;
    }

    public static void main(String[] args) throws Exception {

        Constructor<Student> constructor = Student.class.getConstructor(Integer.class);
        Student stu3 = constructor.newInstance(123);
    }
}
```
这分别是C++和Java的定义对象，是不是觉得很不一样？
###### 动态原型模式
```js
function Person(name,job,age){
    this.name=name;
    this.job=job;
    this.age=age;
    if(typeof this.sayHello!="function"){
        Person.prototype.sayName=function(){
            console.log("hello");
        };
    }
}
```
这种模式就是针对原型模式的缺点进而优化原型模式，使用判断来避免二次定义
###### 稳妥构造函数模式
我们的最初目的是什么？新建对象。那怎样是最方便且问题最少的做法？直接拿别人现成的。
```js
function Person(name,job,age){
    let o=new Object();
    //定义私有变量
    let _name=name;
    let _age=age;
    let _job=job;
    //共有变量
    o.type="school";
    //定义特权函数用于访问私有变量
    o.sayHello=function(){
        return "hello I'm"+name;
    }
    return o;
}
var person1=new Person("wkq","stduent",21);
console.log(person1.sayHello());//hello I'mwkq
console.log(person1._name);//undefined
console.log("_name" in person1);//false
console.log(person1.type);//school
person1.getName = function () {
    if(this._name=="wkq")//如果可以获取私有变量则进行修改
        this._name="weiao";
    return this._name;
};
console.log(person1.getName());//undefined
```
自此，算是得到了一种与其他语言最为类似的模式，成员变量，方法，私有，公有都做到了，其语法格式也相对类似。
#### 继承
子类获得父类的方法与属性，很明显有很多投机取巧的办法做到类似的效果，于是就有instanceof方法用于检测是不是投机取巧：
```js
 function foo() {
            this.s=2;
            this.e=3;
        }
        var a=new foo();
        var i=Object.create(a,{
            b:{
                value:1
            }
        });
        console.log(i.s);//2
        console.log(i instanceof foo);//true
```
其作用是检测一个**对象或其父类**是否由一个**构造函数**所创建的。简单地，我可以直接将父类构造函数在子类里再调用一遍，其效果是很相似的。继承是为了避免重复定义以造成资源浪费，但这种实现和继承的本意背道而驰。
值得说明的是instanceof的左右操作数，一定是对象和构造函数。
###### 原型链
这个简单，就是从顶：Object向下将所有的对象变成一个双向链表，头指针是Object，所有的对象都是链表上的节点，其前驱是他的原型，其后继是子类，所有的对象都可以沿着链表向前遍历方法和变量，虽然是双向链表，但是**禁止**向后遍历。
###### 继承的实现
根据上面的分析，继承的实现就转化为了新建链表节点。好，我们首先根据我们的习惯，有头插法和尾插法，然后再排除头插法（原型链的特性），就只有尾插法一种,也就是上面的示例，当然还有一种：
```js
function foo(){
    S:2
}
function fun(){
    Q:2
}
fun.prototype=new foo();
var s=new fun();
```
这里创建的s就是foo构造函数对应对象的子类实例，是不是和链表操作很像：
```c
List *H,p;
p=H=(List*)malloc(sizeof(List));
H->next=p;
//H.data=foo()=fun().prototype;
//p.data=fun();
```
当然也有其他实现的方法，但是在我看来其他方法就属于投机取巧的方式，其就是直接在子类直接调用超类的构造函数的call方法。
## 函数
首先一点，也是最重要的一点：**函数是对象**。
```js
function foo() {
    let a=0;
    a++;
}
Object.defineProperty(foo,"length",{
    value:3
});
console.log(foo.length);//3,不实例的输出是0
console.log(typeof(foo));//funtion
```
下来正式对函数进行介绍,即将涉及到内容有：
1. 函数声明与函数表达式
2. 函数作用域与声明提升
3. 函数闭包
4. 函数式编程
### 函数声明与函数表达式
什么是声明？在我看来就是告诉引擎和编译器在这一段内是一个函数，至于具体干啥，先不用管，而表达式与其有本质上的区别：以下面的代码为例：
```js
var foo=function bar(){
    let a=0;
    a++;
};
```
当这一段代码出现在全局范围内，编译器是会直接将该段代码直接放到主进程中，即为立即执行，但是值得注意的是声明仅限于var foo这一段，也就是说，这一部分代码会被分割成两部分：
```js
var foo;
foo=function bar(){
    let a=0;
    a++;
}
```
声明很简单，只需要知道声明和定义会分开实现，被声明的变量在定义前值为undefined，还有一个很重要的：函数名只是函数对象的指针，也就是说，在具体操作中和对象是非常类似的。
### 作用域
此处非常重要！这里由于牵扯的东西过多，故直接将作用域整个拿过来分析。
在我们熟悉的编译过程中，一般会涉及：词法分析、语法分析、代码生成，但由于JavaScript的特殊性，会对其性能进行尽可能地优化，但由于我们只是讨论作用域，因此可以将JavaScript编译执行过程抽象为边编译边执行。
首先有几个重要的角色：

>引擎：负责整个编译和执行过程；
编译器：负责和操作编译相关的东西；
作用域：维护所有的声明标识符，并按照相应的规则进行相应的操作；
执行栈：所有直接执行的代码段依次存放在其中等待主进程操作；
等待队列：所有异步事件在webapi执行完相应的事件后，会依次存入队列，而每一个事件对应一个回调函数，从而在从队列中出去之后，进入主进程执行相应的回调函数。
>
在举例之前有一个关于引擎和作用域之间的操作：LHS查询和RHS查询，这是在引擎在执行操作时在作用域对变量标识符进行查找的两种方式：
>LHS，又称左查询，例如a=0;引擎在执行时会先在作用域內部查找a，我们这部是赋值操作，因此会在找到a后直接进行修改；
RHS，又称右查询，例如console.log(a);我们要把a作为参数传入console的log函数内，要知道，我们希望传入的是a内部的值，而不是a这个标识符，因此RHS又被称为retrieve his source value，取得他的源值。
>
现在正式开始：
```js
var a=0;
setTimeout(function(){console.log(a);},1000);
```
>首先代码字符串传入引擎
引擎调用编译器进行编译
编译第一句var a=0;
编译器首先询问作用域是否存在a
存在则跳过
否则进行声明a标识符是一个变量，将其放入作用域
然后编译器生成a=2的执行代码交给引擎
引擎执行，放入执行栈，再在执行栈调用该代码段，对a进行LHS查找，并进行赋值
再此调用编译器对下一句进行编译
由于setTimeout存在于内置对象内部，便向下编译
遇到function会声明函数，并将后续生成执行代码
引擎执行setTimeout函数，将其放入执行栈，执行，遇到事件需要调用webapi等待1000ms
1000ms后放入等待队列，此时主进程空闲，调用队列的事件对应的回调函数，即执行function
引擎对console进行LHS查找，并调用log函数，再对a进行RHS，执行a=0操作
执行log函数，调用webapi，结束后放入队列，再进入主进程，结束整个程序
>

必须清楚，此处的过程简化了引擎的优化过程，仅仅是为了了解作用域在编译执行时的作用。
#### 词法作用域
顾名思义，词法的作用域，也就是标识符的作用域，例如：
```js
function foo(a){
    var b=a*2;
    function bar(c){
        console.log(a,b,c);
    }
}
foo();
```
在全局范围内只有foo标识符，在foo作用域内（函数作用域，下面会介绍）有a，b，bar三个标识符，在bar作用域内有c标识符，也就是说这些标识符都是其作用域内的变量。
对于嵌套的作用域我们可以向上依次查找对应的标识符，直到找到的第一个标识符作为操作数，例如：
```js
var a=1;
(function (){
    a=10;
    console.log(a);//10
})();
```
对于a的RHS查找会在其作用域内查找，其属于全局作用域下的匿名函数作用域，因此他从函数内开始查找，找到a=10后就会执行log方法。
>还有一点，在上面我们模拟了编译执行，知道**作用域的操作**只会发生在编译阶段，也就是**声明阶段**。

那总有人不老实，想有一些骚操作，不想老老实实跟着规则走，想在执行过程中改变变量的作用域，怎么办？也不是不可以，只是非常不推荐，非必须情况下必须避免。
1. eval()
这个东西很厉害，它可以直接改你的JavaScript代码，其会接受一段字符串，在执行时，该函数将字符串的引号去掉，作为一段JavaScript代码存在原有代码中，也就是说无法编译，直接通过引擎执行。
2. with
这个东西就更厉害了,这个玩意可以直接让with内部变量的作用域变得非常混乱，如果不小心写一个未定义的变量，那恭喜，这个变量谁都能用。

总结一下，对于词法作用域，其能将变量分别存在声明的作用域内，不受外部影响，当然也可以改变作用域，这是非常不推荐的做法。
#### 函数作用域与块作用域

函数作用域跟我们熟知的其他语言中的函数一样，只能通过参数进入函数作用域内部，其具体的操作就是词法作用域的操作，看起来这个概念没有任何作用，但函数作用域是后面闭包和函数式编程的一个基础。

块作用域，也就是一个被分开的代码段，像中for循环就是一个代码段，但是对于JavaScript来说其内部标识符是与外部共享的，也就是全局范围内都可以随意访问，C++就不一样，内部的自有变量是不会随意被外部使用。但是我们既然提出了，就说明创建一个代码段有意义。

1. with：由他创造的块是有私有作用域的，但是这个with牵扯的东西过多，之后会具体分析，反正是极不推荐使用
2. try/catch：这个同其他语言的操作，但是他会在内部创建相应的类似函数的作用域，同样也实现了私有作用域。
3. let：es6觉得上面两个做法实在太傻，就直接创建了新关键字：let。正儿八经的私有变量声明，本质是创建一个新的作用域，效果和函数类似，但是有一点特别地，它不会提升。完美，它现在终于和看到的一样了，不再像以前的var声明提升，定义分离。在引入它的同时，又一个新关键字const也来了，此后数值也可以是常量了。




