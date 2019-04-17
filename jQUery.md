#jQuery
jQuery 实质上是一个 JavaScript 的库，其目的是方便在为 HTML 加入 js 脚本时精简其代码量，并提供统一的 jQuery 对象，使得操作变得统一规范。 
##选择器

> 由于 jQuery 本质是对 HTML 操作，因此选择器是 jQuery 中最基础的东西
> 1.jQuery 支持 CSS1~3 的所有选择器,例如：
>
> > ```sass
> > $("div");//选取所有div元素，并将其打包成一个jQuery对象
> > $("#idname");//选取id=idname的所有元素
> > jQuery(".classname");//选取class=classname的元素
> > $("a:active");//选取a元素的激活状态
> > ```
>
> 2.支持多种 css 选择器一起使用，中间使用空格分隔
>
> 3.过滤选择
>
> > a.:eq()：根据索引过滤，括号内为索引
> > b.:even（odd）：选择索引为偶数(奇数)的元素，从 0 开始
> > c.:first（last）：选择所有元素中第一个(最后一个)元素
> > d.:gt()（lt()）：选取大于（小于）参数索引的元素
> > e.:root：根元素
> > f.:focus：获得焦点的元素
> > g.:animated：有动画的元素
>
> 4.层级选择
>
> > a.element>child：子元素选择，选择所有 element 元素下 child 子元素，但只能选取直接子元素
> > b.element child：同上，但不限于直接子元素
> > c.element~sibling：选择 element 之后的所有兄弟元素中的 sibling 元素
> >
> > ```sass
> > $(".name~p");//选择所有的class=name的元素的兄弟元素中的p元素
> > ```
>
> 5.jQuery 扩展选择器
>
> > a.:has(element)：元素的后代中有 element 元素的所有元素，不一定是直接子元素
> > b.:header：选取所有 h 元素
> > c.:hidden：选取不可见元素
> > d.:visible：选取可见元素
> > e.:form：选取 form 表单中包含属性值为 form 的元素
> > g.:image：选取图片元素
>
通过选择器我们已经可以做到精确的选取每一个元素，下来就是为各个元素添加各种属性
##DOM属性

>1.addClass(classname)：为元素添加css中.classname的属性
>2.attr()：获取元素属性值，或为元素添加属性
>```sass
>$("input").attr("type");//获取input的type属性值
>$("input").attr("type","text");//将type改为text
>$("input").attr({
>    type:"text",
>    value:"123"
>});//将type改为text，value改为123
>```
>3.hasClass(class)：元素是否有class="class"
>4.html():获取第一个元素的HTML文本，或将参数（HTML文本）加入元素内部，作为html运行