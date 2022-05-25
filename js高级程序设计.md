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

​	与let很相似，但是必须初始化，并且不能对其修改。

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