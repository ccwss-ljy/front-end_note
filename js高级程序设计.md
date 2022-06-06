# 一、什么是JavaScript（了解）

# 二、HTML中的JavaScript

##  2.1 \<script>元素

### 2.1.1 属性（8个）

1. async：该属性只可以用在外部js文件，表示可以立即下载并执行js文件，并且该属性不会影响页面其他文件的下载解析。
2. charset：和src配合使用，规定src文件的代码字符集
3. defer：该属性只可以用在外部js文件，表示文件可以立即下载，且不会影响页面其他文件下载解析。等文档解析和**显示**后再执行
4. （之后再查一下，不懂）crossorigin：配置相关请求的cors设置，默认不使用cors。crossorigin=“anonymous”配置文件请求不需要设置凭据标志。crossorigin=“use-credentials”设置凭据标志，以为着出站请求会包含凭据
5. integrity：允许比对接收到的资源的签名和指定的加密签名是否一致来判断子资源的完整性。防止内容分发网络（CDN）提供恶意内容
6. src：要执行的外部文件
7. language：废弃
8. type：代替language，表示代码块中脚本语言的内容类型（MIME类型），惯例是“text/javascript”。当使用es6的模块化的时候，需要使用module，才能使用import和export。

### 2.1.2 注意（4个）

- 不可以在代码块中出现< /script>，如果要出现需要使用转义字符<\/script>。因为在从上到下遍历过程中，就算代码中“< /script>”是一个字符串，也会将其认为是< /script>结束标签。并且浏览器也会报错。

  ```html
  <script>
  	console.log("</script>") //报错
  	console.log("<\/script>") //不报错,输出</script>
  </script>
  ```

- script不可以使用自闭合标签，因为在XHTML文档中，会忽略结束标签，有些浏览器不可以使用。

  ```html
  <script></script> //正确
  <script/> //错误
  ```

- 如果使用外联js文件，在该自闭合标签内再写js代码，浏览器只识别外联js文件
- script的src属性支持跨域，进行get请求，取得相应资源。这个请求不受浏览器同源策略的限制，但是返回文件的执行是受限制的。

### 2.1.3 标签位置

​	目前script都放在页面内容的后面。因为放在head里的话，js的下载和解析会阻碍后面页面的渲染，导致页面白屏。

### 2.1.4 推迟执行脚本（defer）

​	defer只可以用在外部js文件，表示该脚本文件在执行的过程中不会修改页面结构，并且遇到该js文件可以直接下载但不执行，不影响html解析。当页面解析到 < /html>时，多个defer文件按页面放置顺序执行，会在DOMContentLoaded事件（DOM树构建完成时）之前执行。但是在实际中，defer有可能不按顺序执行且有可能在DOMContentLoaded后执行，所以最好只有一个defer属性的js外联文件。

### 2.1.5 异步执行脚本（async）

​	async和defer类似只可以使用在外联js文件，区别在于可以立即下载执行。多个拥有该属性的js文件，没有执行顺序，谁先下载完成谁先执行。拥有该属性的js文件也不可以修改dom结构，该属性的js文件保证会在load事件前执行。

### 2.1.6 动态加载脚本

​	下方是动态添加js脚本的方法，在将script添加到DOM且执行到该代码之前都不会发送请求

```js
let script = document.createElement("script");
script.src = 'haha.js';
document.head.appendChild(script);
```

​	该种方法添加js脚本默认为异步方式加载，但不是所有浏览器支持async属性，所以将该代码改写成同步模式

```js
let script = document.createElement("script");
script.src = 'haha.js';
script.async = false;
document.head.appendChild(script);
```

​	而且动态添加的js脚本，浏览器的预加载器是不可见的，这样就可能会影响到整个页面的性能，所以要填在head里添加link标签，显示声明它们，让预加载器知道这些动态添加的文件的存在

```html
<link rel='preload' href="haha.js">
```

### 2.1.7 XHTML中的变化（了解p16）

### 2.1.8 废弃的语法

​	在script标签中最好不要使用type属性，因为type指定的MIME类型可能有些浏览器不支持，那么就会跳过该脚本不执行。除非是XHTML（目前也被淘汰）或< script>要求（比如module）或包含非js代码使用type属性。

## 2.2 行内代码与外部文件

​	推荐使用外部文件，原因如下：

- 可维护性。
- 缓存。因为使用外部文件，首次访问需要加载，后续访问浏览器缓存中已经存在了，所以会加快加载速度。
- 适应未来。

​	在新浏览器支持SPDY/HTTP2下，可以减轻带宽消耗问题，最好把一个大文件拆分成多个轻量、独立的js组件。因为通过缓存机制，B文件可能会用到A文件之前请求过得文件，使用缓存会加快加载速度。

## 2.3 文档模式（后续从网上查查）

## 2.4 \<noscript>元素

​	\<noscript>标签里支持任何\<body>中的html元素，除了\<script>的标签。

​	只有浏览器不支持js脚本或浏览器关闭了对脚本的支持，\<noscript>标签里的内容才会展现出来。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <noscript>
        <p>只有关闭对js脚本的支持，你才能看到我</p>
    </noscript>
</body>
</html>
```

# 三、语言基础

## 3.1 语法

### 3.1.1 区分大小写

​	Test和test不是同一个变量

### 3.1.2 标识符

​	第一个字符只能是字母，_（下划线），$（美元符号）

​	剩余的字符可一个是字母，下划线，美元符号，数字

## 3.2 关键字与保留字（了解）

## 3.3 变量

### 3.3.1 var

1. var声明作用域

   没有使用var操作符定义一个变量称为自由变量，通过作用域链寻找声明该变量的地方，直到全局。

```js
function test(){
    var message = 'hi'
}
test();
console.log(message); //报错，因为message是在函数作用域里声明的，属于局部变量，在函数外部获取不到。
--------------------------------
function test(){
    message = 'hi'
}
test();
console.log(message); //可以获取到，输出hi。因为在函数作用域内没有用var操作符，message变量默认变为全局变量。只要调用了test，就会在全局定义message。
```

```js
var a = 1, b = 2, c = 3 //var、let、const支持一次性多定义
```

2. var声明提升

```js
console.log(age); //不会报错，输出undefined
var age = 12;
--------------
//因为变量提升的原因，该代码实际为
var age;
console.log(age);//所以输出undefined
age = 12;
----------------------
console.log(age);
age = 12;
// 报错，只有用var操作符定义的变量才会变量提升，所以这里的age不会提升。
```

```js
var age = 12;
var age = 13;
var age = 14;
console.log(age) //14
//var支持重复定义
```

### 3.1.2 let

1. let不支持重复声明

```html
<script>
	let age = 1;
</script>
<script>
	let age = 2;//会报错
</script>
-----------------
let age = 12;
let age = 13;
let age = 14;
console.log(age) //报错，语法错误
```

2. 不会变量提升，在let声明之前的执行瞬间被称为“暂时性死区”

3. 全局声明
   - 下面代码，var和let声明的变量虽然都是全局变量，但是var声明的变量默认成为window的属性，而let的则不会

```js
var name = "haha"
console.log(window.name) //haha
let age = 13;
console.log(window.age) //undefined
```

4. 条件声明

​	let不支持条件声明，因为let在块级作用域里声明以后，外部获取不到。

### 3.1.3 const

​	与let很相似，但是必须初始化，并且不能对其修改。let与const也不能声明相同变量

```js
const name; //报错
-------------
//对于对象和数组可以修改地址指向位置里的值，但是不可以修改地址
const person = {};
person.name = 'zs' //可以
person = {name:'zs'} //不可以，因为相当于把一个新的地址赋值给person，报错
```

## 3.4 数据类型

### 3.4.1 typeof操作符

​	对一个值使用typeof操作符，会返回一个字符串，如下结果：

- string：表示是一个字符串
- number：表示是一个数字
- undefined：表示该值还未定义
- boolean：表示该值是一个布尔值
- function：表示该值是一个函数
- object：表示该值是一个对象或是null
- symbol：表示该值是一个符号

### 3.4.2 Undefined类型

​	对于一个声明但是没有初始化的值来说为undefined

```js
let name;
console.log(typeof name); //undefined
```

​	对于一个未声明的变量，直接使用它，会报错。对于未声明的变量只能做一个有效的操作，就是使用typeof操作符，并且会返回undefined

```js
console.log(age); //报错，因为未声明。
console.log(typeof age); //undefined
```

​	从逻辑上讲，虽然它两完全不同，但是都是干不了任何实际的操作，所以使用typeof操作符结果都为undefined。

​	**注意：建议在声明每一个变量时，都要初始化。这样typeof出现undefined的情况，就可以认为是没有声明**

### 3.4.3 Null类型

​	null从逻辑上讲，表示为一个空对象指针。为了代码规范化，在初始化变量时，把之后可能保存对象值的变量定义为null。

### 3.4.4 Boolean类型

| 数据类型         | 转换为true值               | 转换为false值  |
| ---------------- | -------------------------- | -------------- |
| String           | 字符串有值（包括“ ”）      | “”（空字符串） |
| Number           | 除0、NaN以外（包括无穷值） | 0、NaN         |
| Boolean          | true                       | false          |
| Undefined        |                            | undefined      |
| Object           | 除null                     | null           |
| Symbol（后续看） |                            |                |

### 3.4.5 Number类型

​	八进制用0或0o表示（0表示的八进制在严格模式下会报错），十六进制用0x表示。

​	**注意：+0和-0任何情况下都全等**

1. 浮点数
   - 小数如果类似为0.1这种小数点前面为0的小数，可以直接不写整数，如.1（但是不推荐）。
   - 因为存储浮点数使用的内存空间是整数的两倍，所以js能将小数换成整数就换成整数。于是1.0或1.数值都会变成整数。
   - 科学计数法：31250000 = 3.125e7、0.000003 = 3e-6
   - 最好不要用小数相加来进行判断，比如0.1 + 0.2  != 0.3
2. 值的范围
   - js能表示的最大值保存在Number.MAX_VALUE，最小值为Number.MIN_VALUE。
   - 超出了最大值为Number.POSITIVE_INFINITY或Infinity（正无穷），超出了最小值为Number.NEGATIVE_INFINITY或-Infinity（负无穷）
   - 正负无穷无法参加计算
   - 判断一个数是否可以表示用isFinite()，true表示在正负无穷内，false表示为该数为无穷，无法表示。

3. NaN

   - 0、+0、-0它们相互除返回的结果是NaN

   - 非0除以0、+0、-0分别为Infinity、Infinity、-Infinity

   - isNaN()，可以判断一个数据类型是否不是数值。可以转换为数值的结果为false，不可以转换为数值的为true

     ```js
     console.log(isNaN("10")) //false
     console.log(isNaN(10)) //false
     console.log(isNaN(true)) //false
     console.log(isNaN(NaN)) //true
     console.log(isNaN("blue")) //true
     ```

4. 数值转换

   数值转换有三种方式，Number()、parseInt()、parseFloat()

   - Number()

     | 数据                                                 | 转换后的值                                                   |
     | ---------------------------------------------------- | ------------------------------------------------------------ |
     | object                                               | 通过调用Object.valueOf()方法返回的结果，然后再按其他数据类型操作来操作。如果结果为NaN转为字符串，再按字符串操作来操作 |
     | null                                                 | 0                                                            |
     | undefined                                            | NaN                                                          |
     | 纯数字字符串（“123”、“1.1”、“0x10”，“”（或只有空）） | 123、1.1、16、0                                              |
     | 其余字符串                                           | NaN                                                          |
     | true/false                                           | 1/0                                                          |

   - parseInt（）

     只适用于字符串、数字、创建的字符串对象，第二个参数可选来决定是几进制，而且只认识0x。

     规则：忽略前面的空格，只要遇到第一个字符不是数字字符返回NaN（“”返回NaN），为数字字符开始记录直到不是数字字符或者结束为止，返回整数。

   - parseFloat()

     和parseInt基本一样，但是只认识十进制，而且没有第二个参数。如果字符串为整数返回整数。

### 3.4.6 String类型(记得回来看标签函数)

1. 字符字面量（p38）

2. 字符串的特点

   - 字符串定义后就是不可变的，如果修改只可以销毁原字符串然后重新保存新值。

3. 转换为字符串

   有两种方法：

   - toString

     可以将除了null、undefined的数据类型转为字符串等价物

     ```js
     true.toString(); //"true"
     10.toString(); //"10"
     10.toString(16); //"a"
     "ha".toString(); //"ha" 创建一个该字符串的副本
     {a:1}.toString(); //"[object Object]"
     [1,2,3].toString(); //"1,2,3"
     ```

   - String()

     可以将null、undefined转为“null”、“undefined”。其余数据类型和toString()一样。

4. 模板字面量

   使用\``符号使用模板字面量，会保留``内的所有字符格式，包括空格。所以在使用时要注意。

5. 字符串插值

   模板字面量从技术上讲属于js的表达式，而不是字符串，只是最后会返回一个字符串。

   使用${}来进行插值，${}里面的内容会使用String()转为字符串。

6. 模板字面量标签函数（p42看不懂之后看）

7. 原始字符串

   使用String.raw，可以通过模板字面量获取到转移前的字符串。

   ```js
   console.log(`first line\nsecound line`) //first line
   										//secound line
   console.log(String.raw`first line\nsecound line`) //first line\nsecound line
   -----------------------------
   console.log(`\u00A9`) //©
   console.log(String.raw`\u00A9`) //\u00A9
   ```

### 3.4.7 Symbol类型(后面的属性放一放，之后看)

​	概念：Symbol（符号）是唯一的，不可变的。主要的用途是确保对象属性的唯一性，不会发生对象属性冲突的危险。

1. 符号的基本用法

   个人理解：符号就像每个人，每个人都是不同的。初始化里的字符串就像每个人的名字，名字可能相同，但是相同名字对应的人是不同的。

   ```js
   let s1 = Symbol(); //初始化
   let s2 = Symbol("foo"); //初始化，传入foo来描述这个符号，相当于给符号起了个名字
   let s3 = Symbol("foo");
   console.log(s2 === s3); //false
   ```

   符号不可以使用new关键字，来创建符号包装对象。如果是在想让对象包装符号，可以使用Object()

2. 使用全局符号注册表

   - Symbol.for()

   如果不同部分想共享和重用同一个符号，就可以在全局注册表中注册一个Symbol。使用Symbol.for()方法。该方法是幂等方法，同一个字符串注册在全局注册表的符号是相等的。第一个使用Symbol.for()时，看全局注册表上有没有，如果没有进行注册，如果有直接返回。

   ```js
   let s1 = Symbol.for("foo");
   let s2 = Symbol.for("foo");
   console.log(s1 === s2); //true
   ```

   在全局注册表上注册的符号和用Symbol()定义的符号是不等的。可以这样理解，如果相等的话，说明用Symbol()定义的符号也是注册在全局注册表的，这显然是不对的。所以它两必然不等。

   ```js
   let s = Symbol("foo");
   let s1 = Symbol.for("foo");
   console.log(s === s1) //false
   ```

   用Symbol.for()传的值默认都会通过String()转为字符串，如果传空，默认符号为Symbol(undefined)

   ```js
   let s1 = Symbol.for();
   let s2 = Symbol.for(undefined);
   console.log(s1 === s2); //true,因为undefined转为字符串为“undefined”，传空值也转为“undefined”。所以相等
   ```

   - Symbol.keyFor()

     使用Symbol.keyFor()，可以查全局注册表上符号的描述，不是全局注册表上的符号返回undefined。参数如果不为符号，则报错。

     ```js
     let s1 = Symbol.for("foo");
     let s2 = Symbol('foo');
     console.log(Symbol.keyFor(s1)); //foo
     console.log(Symbol.keyFor(s2)); //undefined
     ```

3. 使用符号作为属性

   - 以下几种方式都可以在对象中定义符号属性

   ```js
   let s1 = Symbol('foo');
   let s2 = Symbol('bar');
   let s3 = Symbol('baz');
   let s4 = Symbol('qux');
   let o = {
       [s1]:'foo value' //第一种方法
   }
   o[s2] = 'bar value'; //第二种方法
   Object.defineProperty(o, s3, {value:"baz value"}) //第三种方法
   Object.defineProperties(o, {
       [s3]:{value:"baz value"},
       [s4]:{value:"qux value"}
   }) //第四种方法
   ```

   - 获取对象属性的几种方法

   ```js
   let s1 = Symbol("foo"),
       s2 = Symbol("bar");
   let o = {
       [s1]: 'foo value',
       [s2]: 'bar value',
       baz: 'baz value',
       qux: 'qux value'
   }
   console.log(Object.getOwnPropertyNames(o)); //['baz','qux'] 返回常规属性的数组
   console.log(Object.getOwnPropertySymbols(o)); //[Symbol('foo'),Symbol('bar')] 返回符号属性的数组
   console.log(Object.getOwnPropertyDescriptors(o)); //返回所有属性的属性描述符对象
   console.log(Reflect.ownKeys(o)); //['baz','qux',Symbol('foo'),Symbol('bar')] 返回所有属性的数组
   ```

   - 符号定义以后就会存在，所以也可以直接用做对象的属性，但是如果没有显示地存这个符号的话，获取这个属性将会很困难

   ```js
   let s1 = Symbol("foo");
   let o = {
       [s1]: 'foo value',
       [Symbol("bar")]: 'bar value',
       baz: 'baz value',
       qux: 'qux value'
   } //o的第二个属性就是直接将符号作为了属性，而第一个属性是将符号显式存储到s1，然后将s1作为属性。
   console.log(o[s1]) //'foo value'
   console.log(o[Symbol("bar")]); //'undefined'，因为符号是唯一的，所以这样操作是有问题的，所以需要通过Object.getOwnPropertySymbols这个api往出取，所以比较复杂
   ```

4. 常用内置符号
5. Symbol.asyncIterator

### 3.4.8 Object类型

- constructor：用于创建对象的函数，比如实例对象、原型对象的constructor都是这个构造函数。
- hasOwnProperty(propertyName)：判断当前对象实例上有没有该属性
- isPrototypeOf(Object)：用于判断当前对象是不是另一个对象的原型
- propertyIsEnumerable(propertyName)：用于判断给定的属性是否可以使用for-in遍历
- toLocaleString()：返回对象的字符串表示，该字符串反映对象所在的本地化执行环境
- toString()：返回对象的字符串表示
- valueOf()：返回对象对应的字符串、数值或布尔值表示。

## 3.5 操作符

### 3.5.1 一元操作符

1. 递增/递减操作符

   给其他数据类型使用该操作符，其他数据类型会先使用Number()转为数字类型，再进行操作。

2. 一元加和减

   一元加放到数据类型前面默认使用Number()方法，一元减放到数据类型前面默认使用Number()方法，将可以变为数字的数据类型变为负数。

### 3.5.2 位操作符

​	js的数字，整数是用原码存，负数是用补码存。如果能用位运算实现的计算，最好用位运算，因为位运算是在底层实现的，速度要快。

1. 按位非（~）

   ```js
   let num1 = 25; //0...11001
   let num2 = ~num1; //1...00110
   console.log(num2); //-26。因为num2是负数，当它输出时，需要将当前值取补码输出，所以最终结果是-26。
   ```

2. 按位与（&）

3. 按位或（|）

4. 按位异或（^）

5. 左移（<<）

6. 有符号右移（>>）

   右移后的空位，由符号位的值来填充。

7. 无符号右移（>>>）

   不管符号位，整个向右移，正负数左边的空位都补充0

### 3.5.3 布尔操作符

1. 逻辑非（!）

   使用!，首先会把其他数据类型的值转为布尔类型（转换规则符合Boolean()）然后取反。

   如果使用!!，把其他数据类型转为布尔类型，相当于使用了Boolean()。

2. 逻辑与（&&）

   不光返回布尔值，如果操作数中有不是布尔值，有可能返回其他数据类型，遵循短路原则。

   - 第一个操作数转为布尔类型为true，会返回第二个操作数的结果
   - 第一个操作数转为布尔类型为false，返回第一个操作数

3. 逻辑或（||）

   不光返回布尔值，如果操作数中有不是布尔值，有可能返回其他数据类型，遵循短路原则。

   - 第一个操作数转为布尔类型为true，返回第一个操作数的结果
   - 第一个操作数转为布尔类型为false，返回第二个操作数的结果

### 3.5.4 乘性操作符

1. 乘法操作符（*）
   - NaN和任何数相乘都是NaN
   - Infinity*0结果是NaN
   - 如果有操作数不为数值类型，那使用Number()转为数值类型，然后计算
2. 除法操作符（/）
   - NaN和任何数相除都是NaN
   - Infinity/infinity结果为NaN
   - 有限值除以Infinity结果为0，符号由两个操作数来决定
   - 非0除以0，结果为Infinity，符号由两个操作数来决定
   - 如果有操作数不为数值类型，那使用Number()转为数值类型，然后计算
3. 取模操作符（%）（p67）

### 3.5.5 指数操作符

​	** 和Math.pow()都可以

### 3.5.6 加性操作符

1. 加法操作符

   遇到字符串，会把其他数据类型转为字符串类型

2. 减法操作符

   遇到其他数据类型，都会把其他数据类型转为Number数据类型来进行计算

### 3.5.7 关系操作符

- 如果都为字符串会根据编码顺序来进行大小比较
- 如果一个操作数为其他数据类型，就会先转为数值类型或字符串类型进行比较
- NaN不管怎么比较都是false

### 3.5.8 相等操作符

​	（==和!=）在进行比较时，需要先进行数据类型转换。（===和!==）在进行比较时，不进行数据类型的转换

### 3.5.9 条件操作符（三目操作符）

### 3.5.10 赋值操作符

### 3.5.11 逗号操作符

```js
let num = 1, num = 2, num = 3;
let num = (5, 1, 4, 6, 0); //最终num的值为0，这种用法很少见
```

## 3.6 语句

### 3.6.1 if语句

### 3.6.2 do-while语句

### 3.6.3 while语句

### 3.6.4 for语句

### 3.6.5 for-in语句

​	for-in是一个严格的迭代语句，枚举对象中所有的非符号键属性

### 3.6.6 for-of语句（看完第七章再回来理解）

​	for-of会根据可迭代对象的next()方法产生值的迭代顺序来进行迭代

### 3.6.7 标签语句

​	用于给语句加标签

```js
start:for(let i = 0;i < count;i++){
    console.log(i)
} //这个start会在break或是continue上有用，用于嵌套循环
```

### 3.6.8 break和continue语句

​	break和continue只能跳出一层循环。但是加上标签的话，可以跳出标签层。

### 3.6.9 with语句（p78，了解，基本用不到）

### 3.6.10 switch语句

​	switch语句中的每个条件的值的比较使用的是全等，所以不会进行数据类型转换。

## 3.7 函数

# 四、变量、作用域与内存

## 4.1 原始值与引用值

​	概念：原始值是最简单的数据（基本数据类型的值），引用值是由多个值构成的对象

### 4.1.1 动态属性

​	对象可以添加属性，基本数据类型不可以

### 4.1.2 复制值

```js
let str = 'haha';
let str1 = str; //在栈内存新复制了一个值。这两个值互相独立
let obj = {a:1};
let obj1 = obj; //浅拷贝，obj和Obj1都指向同一个对象
```

### 4.1.3 传递参数

​	js中所有函数的参数都是按值传递的。而不是按引用传递。也就是对象的浅拷贝。

### 4.1.4 确定类型

​	typeof来判断原始值很有用，但是对于引用值来说，意义不大。使用instanceof，来判断检测对象属于哪个给定引用类型。

## 4.2 执行上下文与作用域

​	每个上下文都会有一个对应的变量对象。变量对象里存着上下文中所有的变量和函数。全局上下文的变量对象就是window对象。上下文和它的变量、函数都会在其执行完毕后被销毁。全局上下文会在程序退出前销毁。

​	按照程序流进行，到达某个函数时，将该函数上下文推到上下文栈中，该函数执行完毕后，将该上下文推出栈，执行上一层上下文。

​	上下文在执行过程中，会创建变量对象的一个作用域链，当前执行的上下文处于作用域链的最前端，找不到的标识符会通过作用域链找到包含上下文的变量对象，一层一层直到全局上下文。如果全局上下文也没有就报错。

### 4.2.1 作用域链增强

​	在任意上下文内，如果使用with语句或者是try/catch语句。with语句会把参数内的对象变为变量对象成为作用域链的最前端，try/catch语句中的catch语句会生成一个新的变量对象，其中包含抛出错误的对象

### 4.2.2 变量声明

1. 使用var的函数作用域声明

   在函数作用域里声明的变量，在函数外部无法访问。使用var声明的变量，可以变量提升。

2. 使用let的块级作用域声明

   在块级作用域里声明的变量，在块外部无法访问。

3. 使用const的常量声明

   与let一样，但是声明以后要初始化，并且不可以修改原始值和引用值。但是对象中的键值可以被修改，如果键值也不想被修改的话，在定义时可以使用freeze。

   ```js
   let o = Object.freeze({});
   o.name = "zs";
   console.log(o.name); //undefined
   ```

​		能使用const最好使用const，因为使用const，js运行时编译器可以把所有的const变量变为实际的值，不需要从查询表上查找变量。并且在开发中const可以防止一些不变的值，因为个人失误改变。

## 4.3 垃圾回收

### 4.3.1 标记清理（没理解）

​	总之就是垃圾回收程序会把内存中有标记的变量做一次内存清理。不在作用域内的变量会添加标记

### 4.3.2 引用计数

​	每个值被引用会将它的引用数加1，首先默认声明以后引用数为1。当引用这个值的值引用了其他值，这个值的引用数减1，当为0时，垃圾回收程序就会释放这块内存。

​	但是这种方式，会有一个bug，出现下方这种情况，a和b的引用数都为2，所以不会释放内存。

```js
let a = new Object();
let b = new Object();
a.name = b;
b.name = a;
```

### 4.3.3 性能

### 4.3.4 内存管理（之后看对象池的实现）

​	为了防止内存的大量占用，导致移动端或pc端性能下降。我们可以手动的解除引用，在下一次垃圾回收程序运行时，将不用的变量（全局变量或是循环引用的变量）释放内存。方式为变量 = null。

1. 通过const和let声明提升性能

   因为const和let都以块为作用域，块使用的频率远远大于函数作用域，所以可能会更早的释放内存。

2. 隐藏类和删除操作

   利用相同的构造函数来创建实例时，最好在创建时就定义好属性，这样多个实例因为有同样的构造函数和原型，它们会使用相同的隐藏类，这样会提升性能。如果在定义好实例以后，对该实例进行动态的增加或删除属性，就会添加隐藏类，降低性能。如果想删除属性可以将属性值等于null，而不是使用delete，这样也不会添加新的隐藏类。

3. 内存泄漏

   内存泄漏发生在三个方面：

   - 意外声明了全局变量，比如在函数作用域内没有用var、let、const来声明变量，默认变为全局变量。
   - 在定时器内使用了外部变量，那这个变量就不会被回收
   - 在使用闭包时，变量也不会被清除

4. 静态分配与对象池

   如下方代码所示，频繁的创建新对象，再修改它，再返回给回调者。这样的对象生命周期很短，再加上如果频繁调用该函数，垃圾回收程序会发现该对象需要频繁的被回收，就会提高垃圾回收程序的执行频率影响性能。

   ```js
   function addVector(a, b){
       let result = new Vector();
       result.x = a.x + b.x;
       result.y = a.y + b.y;
       return result
   }
   ```

   正确的做法是在外部已经定义好该对象，这样做结果一样，但是不会频繁的创建新对象。垃圾回收程序也就不会频繁执行。result对象可以在外部用对象池来定义，这样在外部也不会频繁的创建新对象。

   ```js
   function addVector(a, b, result){
       result.x = a.x + b.x;
       result.y = a.y + b.y;
       return result
   }
   ```

# 五、基本引用类型

​	概念：函数是引用类型的一种，对象是引用类型的实例（可以把引用类型类比成“类”，但是它两不完全一样）

## 5.1 Date

```js
let now = new Date(); //返回当前日期和时间
//如果想知道其他时间和日期，就将其他时间的毫秒数传入。也可以直接传入时间字符串，默认调用Date.parse()方法
//返回的时间都是电脑设置的时间
let time = Date.parse(); //传入日期字符串，返回相应的毫秒数，如果不是日期字符串返回NaN
let time = Date.UTC(); //该方法是传入年、月、日的数字来返回对应的毫秒数。不过月份是从0开始
let start = Date.now(); //执行该方法时的时间。一般在一段程序前后分别使用，然后作差来记录程序运行时间
```

### 5.1.1 继承的方法

```js
Date重写了toString、toLocalString、valueOf方法。
toString、toLocalString返回时间字符串
valueOf返回毫秒数
```

### 5.1.2 日期格式化方法（p106）

### 5.1.3 日期/时间组件方法（p106）

## 5.2  RegExp

标记

- g：全局模式，找到所有符合的
- i：不区分大小写
- m：多行模式，表示查找到最后一行会接着查找
- y：粘附模式，表示只查找从lastIndex开始及以后的字符串
- u：Unicode模式，启用Unicode匹配
- s：dotAll模式，表示元字符.可以匹配所有包括\n、\t

正则表达式的创建有两种方式

```js
let reg1 = /at/i;
let reg2 = new RegExp('at', 'i')
```

### 5.2.1 RegExp实例属性

- global：判断是否开启g
- ignoreCase：判断是否开启i
- unicode：判断是否开启u
- sticky：判断是否开启y
- lastIndex：表示在源字符串下一次开启搜索的位置，始终从0开始
- multline：表示是否开启m
- dotAll：表示是否开启s
- source：正则表达式的字面量字符串
- flags：正则表达式的标记字符串

### 5.2.2 RegExp实例方法

- exec：返回一个数组，这个数组会有两个新增属性。input是返回要匹配的字符串，index是返回匹配时的开始位置。数组的第一个元素是匹配的内容，如果有捕获组，之后的元素就是捕获组里的内容。
  - 不使用全局永远只匹配第一个符合的，使用全局可以匹配后面的

```js
let text = '1mom and dad and baby';
let reg = /mom( and dad( and baby)?)?/gi;
let matches = reg.exec(text);
console.log(matches.index); //1
console.log(matches.input); //1mom and dad and baby
console.log(matches[0]) //mom and dad and baby
console.log(matches[1]) // and dad and baby
console.log(matches[2]) // and baby
```

不使用全局

```js
let text = 'cat bat sat fat';
let reg = /.at/;
let matches = reg.exec(text);
console.log(matches[0]) //cat
console.log(matches.index) //0
console.log(reg.lastIndex) //0

matches = reg.exec(text);
console.log(matches[0]) //cat
console.log(matches.index) //0
console.log(reg.lastIndex) //0
```

使用全局

```js
let text = 'cat bat sat fat';
let reg = /.at/g;
let matches = reg.exec(text);
console.log(matches[0]) //cat
console.log(matches.index) //0
console.log(reg.lastIndex) //3，因为匹配从0-2，所以下次开始匹配的位置是从3开始

matches = reg.exec(text);
console.log(matches[0]) //cat
console.log(matches.index) //4
console.log(reg.lastIndex) //7
```

如果开启粘附模式

```js
let text = 'cat bat sat fat';
let reg = /.at/y;
let matches = reg.exec(text);
console.log(matches[0]) //cat
console.log(matches.index) //0
console.log(reg.lastIndex) //3，因为匹配从0-2，所以下次开始匹配的位置是从3开始

matches = reg.exec(text);
console.log(matches) //null，因为开启粘附，下次从3开始，而且从3开始必须匹配，因为3是空格所以不匹配返回null
console.log(reg.lastIndex) //0

reg.lastIndex = 4; //因为开启了粘附，所以可以提前设置lastIndex位置，使其匹配
matches = reg.exec(text);
console.log(matches[0]) //bat
console.log(matches.index) //4
console.log(reg.lastIndex) //7
```

- test：test来检验是否匹配字符串，返回布尔值

```js
let text = '000-00-0000';
let reg = /\d{3}-\d{2}-\d{4}/
if(reg.test(text)){
    console.log("匹配")
}
```

### 5.2.3 RegExp构造函数属性

- input：最后搜索的字符串
- lastMatch：最后匹配的文本
- lastParen：最后匹配的捕获组
- leftContext：input字符串中出现在lastMatch左边的内容
- rightContext：input字符串中出现在lastMatch右边的内容
- $1-$9：捕获组中按顺序对应的值

RegExp的构造函数属性，当执行完exec或test时，就会填充这些属性

```js
let text = 'this has been a short summer';

let reg = /(.)hort/g;

if(reg.test(text)){
    console.log(RegExp.input); //this has been a short summer
    console.log(RegExp.lastMatch); //short
    console.log(RegExp.leftContext); //this has been a 
    console.log(RegExp.rightContext); // summer
    console.log(RegExp.lastParen); //s
    console.log(RegExp.$1) //s
}
```

```js
let text = 'this has been a short summer';

let reg = /(.)hort/g;

let text1 = 'this has been a long summer';

let reg1 = /(.)ong/g;

reg.test(text)
reg1.test(text1)
console.log(RegExp.input); //this has been a long summer
console.log(RegExp.lastMatch); //long
console.log(RegExp.leftContext); //this has been a 
console.log(RegExp.rightContext); // summer
console.log(RegExp.lastParen); //l
console.log(RegExp.$1) //l
//因为最后匹配的是text1这个字符串，所以RegExp构造函数属性就被替换了
```

## 5.3 原始值包装类型

原始值包装类型有三个分别为Boolean、String、Number，使用Object也可以实现原始值包装类型，和传入的参数有关，比如传入字符串，那么和使用String构造的实例就一样了

```js
let b = new Boolean(true);
let s = new String("heihei");
let n = new Number(1);
let obj = new Object("heihei"); //obj结果为String{"heihei"}
```

### 5.3.1 Boolean

重写了valueOf，返回true、false，重写了toString，返回“true”、“false”。但是最好不要用该原始值包装类型，因为对象的布尔值永远都是true，会影响布尔值的判断

### 5.3.2 Number

- toFixed()：根据参数返回相应位数的字符串表示的数字

  ```js
  let a = 1.006;
  let s = a.toFixed(2); //s=1.01
  ```

- toExponential()：将一个数字返回字符串，用科学计数法表示。参数表示小数位数

  ```js
  let a = 100;
  let s = a.toExponential(1); //s=1.0e+2
  ```

- toPrecision()：会根据情况返回一个最后合理的结果，参数决定显示的数字位数，为科学计数法还是小数

  ```js
  let a = 99;
  let s1 = a.toPrecision(1); //s1=1e+2，因为99没法表示成一个数字，所以变成100再转成科学计数法
  let s2 = a.toPrecision(2); //s2=99
  let s3 = a.toPrecision(3); //s3=99.0
  ```

- isInteger()：判断一个数是不是整数

### 5.3.3 String

1. Js字符

   - charAt()：返回给定索引的字符

   - charCodeAt()：返回给定索引的字符编码

   - fromCharCode()：根据给定字符编码，返回字符串

     ```js
     let s = String.fromCharCode(97,98,99) //abc
     ```

   - codePointAt()：如果是多个编码组成的字符，使用这个就很合适，作用和charCodeAt()作用一样
   - fromCodePoint()：根据给定字符编码，返回字符串，可以写入码点。码点就是一个字符对应的编码。

2. normalize()方法

   让字符串规范化

3. 字符串操作方法

   - concat()：拼接字符串

   - slice()、substring()、substr()区别:

     - 当只有一个正参数时，三个一样

       ```js
       let s = "hello world";
       let s1 = s.slice(3) //lo world
       let s1 = s.substring(3) //lo world
       let s1 = s.substr(3) //lo world
       ```

     - 当第一个参数为正参数，第二个参数也为正参数时。slice和substring一样，都是返回第一个参数到第二个参数前之间的子串。而substr返回从第一个参数开始的字符串，第二个参数为该子串的字符个数

       ```js
       let s = "hello world";
       let s1 = s.slice(3, 7) //lo w
       let s1 = s.substring(3, 7) //lo w
       let s1 = s.substr(3, 7) //lo worl
       ```

     - 当第一个参数为负数时，slice会从倒数第n个开始，substring将负数默认变为0，substr会从倒数第n个开始

       ```js
       let s = "hello world";
       let s1 = s.slice(-3) //rld
       let s1 = s.substring(-3) //hello world
       let s1 = s.substr(-3) //rld
       ```

     - 当第二个参数为负数时，slice依旧，substring还是将负数变为0，substr的第一个参数为负数会从倒数第n个开始，第二个参数为负数默认也变为0

       ```js
       let s = "hello world";
       let s1 = s.slice(3, -4) //lo w
       let s1 = s.substring(3, -4) //hel
       let s1 = s.substr(3, -4) //""，因为第二个参数为0，所以返回空串
       ```

4. 字符串位置方法

   - indexof(substr, start):

     从start位置到字符串末尾查找子串，如果不写第二个参数，就是从字符串头部开始查找，返回子串位置

   - lastIndexOf(substr, start):

     从start位置到字符串开头查找子串，如果不写第二个参数，就是从字符串末尾开始查找，返回子串位置

5. 字符串包含方法

   - startsWith(substr, start):

     从start位置开始匹配子串，如果包含返回true，如果不加第二个参数默认为从字符串头部开始

   - endsWith(substr, end):

     假设字符串就到end结束，判断子串是否和默认部分相同，如果包含返回true，如果不加第二个参数默认从字符串末尾开始匹配

   - includes(substr, start):

     从start开始，从整个字符串寻找，不像startsWith和endsWiths只从开始和末尾寻找，找到包含子串返回true

6. trim()方法

   - 有trim()、trimLeft()、trimRight()。分别为将字符串左右空格去除、字符串左边空格去除、字符串右边空格去除

7. repeat(n)方法

   将一个字符串重复n次

8. padStart(n, str)和padEnd(n, str)方法

   将一个字符串填充到n位，如果有第二个参数那么用第二个参数来填充，如果没有就用空格填充。padStart是从开始填充，padEnd是从末尾填充。如果n小于等于源字符串，那么返回源字符串

9. 字符串迭代与解构

   默认暴露了@@iterator，所以可以迭代。也可以使用[...str]，直接变为字符数组

10. 字符串大小写转换

    - toLowerCase()、toUpperCase()、toLocaleLowerCase()、toLocaleUpperCase()

      前两个是以前就有的，变为小写和变为大写。后面两个是新加的，因为不同地区的大小写转换可能不一样，是地区特定的方法。如果确定不了地区，最好使用后两个

11. 字符串模式匹配方法

    - match()：返回匹配的子串，如果开启g，会找出所有匹配子串

    - search()：返回第一次匹配子串的位置。开启g没用

    - replace()：第二个参数不光可以放字符串，也可以放函数

      ```js
      function htmlEscape(text) {
          return text.replace(/[<>&"]/g, function (match, pos, originalText) {
              switch (match) {
                  case "<":
                      return "&lt;";
                  case ">":
                      return "&gt;";
                  case "&":
                      return "&amp;";
                  case "\"":
                      return "&quot;";
              }
          })
      }
      
      console.log(htmlEscape("<div class=\"box\">'hello world'</div>"))
      //&lt;div class=&quot;box&quot;&gt;'hello world'&lt;/div&gt;
      ```

    - split()：第一个参数为以什么分割，第二个参数为返回数组的最大元素个数

12. localeCompare()方法

    字符串比字符串参数大返回1，相等0，小返回-1

    ```js
    const stringValue = 'yellow';
    console.log(stringValue.localeCompare('brick')) //1
    console.log(stringValue.localeCompare('yellow')) //0
    console.log(stringValue.localeCompare('zoo')) //-1
    ```

## 5.4 单例内置对象

### 5.4.1 Global

1. URL编码方法（目前不知道用处）
2. eval()方法：解释字符串中的内容，执行字符串中的js语句。不会变量提升。在eval里的字符串可以看成在块作用域里，所以使用let和const定义的变量，在外部使用会报错。
3. Global属性（p130）
4. window对象：可以看成是Global的代理，因为没有直接访问Global的方式。

### 5.4.2 Math

1. Math对象属性(p132)
2. min()和max()方法：可以放多个参数进行比较
3. 舍入方法
   - Math.floor()：向下取整
   - Math.ceil()：向上取整
   - Math.round()：四舍五入
   - Math.fround()：返回数值最接近单精度的浮点数表示
4. random()方法：随机产生0~1的小数，但是可以到0到不了1。如果为了加密和更大的不确定性使用window.crypto.getRandomValues()
5. 其他方法（p134）
