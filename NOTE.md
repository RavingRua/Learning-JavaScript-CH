# JavaScript

---

> 整理自 《Professional JavaScript for Web Developers》，原作者 Matt Frisibie。

## 01 什么是 JavaScript

### JavaScript实现

完整的JavaScript包括：

+ 核心（ECMAScript）
+ 文档对象模型（DOM）
+ 浏览器对象模型（BOM，在 Web 浏览器为 ECMAScript 的宿主环境时）

另外常见的 ECMAScript 宿主环境还有 NodeJS 和 Adobe Flash（已淘汰）。

#### ECMAScript

ECMAScript 已经经历了多个版本标准的迭代，被设计为一种**平台无关**编程语言。

ECMAScript 定义了：

+ 语法
+ 类型
+ 语句
+ 关键字
+ 保留字
+ 操作符
+ 全局对象

##### 关于 ES6

ECMAScript 第六版，俗称 ES6、ES2015，于2015年6月发布，是有史以来最重要的一次 ES 特性增强，包括了类、模块（ES Module）、迭代器、生成器、箭头函数（Lambda表达式）、期约、反射、代理和众多数据类型。

###### ESNEXT

ECMAScript 标准还在不断完善中，截至 2021年6月22日，ECMA 已有12版标准。

#### DOM

文档对象模型（DOM，Document Object Model）是一个应用编程接口（API），用于在 HTML 中使用拓展的 XML。DOM 会创建表示HTML文档的节点树，通过 ECMAScript 控制网页的内容和结构。

DOM 标准由万维网联盟（W3C，World Wide WebConsortium）制定。DOM 包括 DOM Core 和 DOM HTML：

+ DOM Core：提供映射 XML 文档、访问和操作文档任意节点的方式
+ DOM HTML：提供特定于 HTML 的对象和方法

支持 DOM 对于浏览器尝试而言是重中之重。

##### 其他 DOM

除了 DOM Core 和 HTML，其他的一些基于 XML 的语言也有属于自己的 DOM，以下语言同样是 W3C 推荐标准：

+ 可伸缩矢量图 SVG
+ 数学标记语言 MathML
+ 同步多媒体开发语言 SMIL

#### BOM

浏览器会提供浏览器对象模型（BOM） API，用于支持访问和操作浏览器的窗口。与 ECMAScript 和 DOM 不同，BOM 没有相关标准。而 HTML5 的出现解决了这个问题，HTML5 以正式规范的形式涵盖了尽可能多的BOM
特性，目前各大浏览器的 BOM 实现细节正日趋一致。

> ECMAScript 核心
>
> DOM 提供与网页内容交互的方法和接口
>
> BOM 提供与浏览器交互的方法与接口

---

## 02 HTML 与 JavaScript

早期的 Web 标准并不统一，将 JavaScript 引入网页不应造成部分浏览器内容渲染错误。在 HTML 中使用 JavaScript 的方式逐渐形成了一种标准。

### script 标签

script 标签用于向网页中插入 JavaScript ，最早由 NetScape 创造并成为标准。常用属性：

+ src：脚本文件的URL，**可以跨域**
+ async：表示网页加载时立即下载脚本文件，**只对外部脚本有效**
+ crossorigin：配置 CORS（跨域资源共享），默认不使用
+ defer：将脚本的执行延迟到文档完全解析和显示后执行，**只对外部脚本有效**
+ integrity：用于验证 SRI（子资源完整性），可以确保 CDN（资源分发网络）不提供恶意内容
+ type：用于替代淘汰的 language 属性，当值为 type 时内部代码被视作 ES6 Module，可以使用 import 和 export 关键字

> script 标签可以跨域，因此在 CORS 出现前有一种跨域资源解决方案 JSONP，利用 script 标签的跨域特性来引入跨域资源。

#### script 导致的页面渲染阻塞

script 标签中的代码内容会**从上到下**被解释执行，并且在 script 标签中的内容被解释执行完前，浏览器不会渲染页面其余内容，这可能导致**页面内容渲染阻塞**。

因此，在原生开发中一般会将 script 标签放至 body 末尾，或在读取外部代码时启用 defer 属性（立即下载，但推迟执行）。

#### defer

defer 使外部链接的脚本在页面解析完后运行，HTML5 标准规定推迟执行的脚本在 DOMContentLoaded 事件之前运行。

#### async

async 与defer 类型，但是不能保证链接的脚本按其链接顺序执行，顺序和资源加载顺序有关。

#### 动态加载脚本

利用 DOM API 可以动态加载脚本文件：

```js
const script = document.createElement('script');
script.src = 'js/例1.js';
script.async = false;           // 默认为true
document.head.appendChild(script);
```

这种加载方式对于浏览器预加载器来说不可见，因此可能导致性能问题。在文档头部显示声明来告知预加载器动态脚本的存在：

```html
<!-- 告知预加载器存在动态请求文件 -->
<link href="xxx" rel="prefetch">
```

### 内联和外部代码

直接在 script 标签中的代码为**内联代码**，src 属性外部链接引入的称为**外部代码**。对于可重用的代码，使用外部代码引入有几个好处：

+ **可维护性**：分散的 JavaScript 代码会导致维护困难
+ **缓存**：现代浏览器会缓存外部资源文件，外部链接代码只需读取一次，加载速度会更快

随着通信技术的发展，资源请求对 UX（用户体验）的影响显著减小，在 HTTP 2.0 中，将代码文件分开加载或置于一个大文件中加载的延迟近似。

### 文档模式

文档模式最早出现在 IE 浏览器中，用于让浏览器兼容标准。

```html	
<!DOCTYPE html>
```

### noscript

当浏览器不支持 JavaScript 或关闭了 JavaScript 功能时，noscript 标签内的内容将显示。

```html

<noscript>
    <strong>
        We're sorry but this page doesn't work properly without JavaScript enabled. Please enable it to
        continue. </strong>
</noscript>
```

---

## 03 JavaScript 基础

任何语言都基于语法、操作符、数据类型、流程控制语句等内置功能工作。ECMAScript 标准定义了 JavaScript 的基础。JavaScript 很大程度上借鉴了 C 和类 C 语言。

### 语法

+ ECMAScript 区分大小写
+ 标识符必须由字母（解释器/编译器支持的编码类型，如UTF-8）、下划线、美元符开头，包含字母、下划线、美元符和数字
+ 注释采用 C 风格：

```js
// 单行注释
/*
	多行注释
*/
```

+ 严格模式：ES 5 标准提出了“严格模式”，通过在代码块中添加`"use strict";`开启，该语句实际上是解释器预处理语句
+ 一条 ES 语句以分号结尾，但也可以省略，省略时由解释器决定语句结尾位置（不推荐）
+ 代码块由花括号`{}`包裹

> 现代 IDE 支持解析一种新的 JavaScript 注释：JsDoc，这种新的文档注释类型能帮助程序员理解代码，尤其是接口部分。IDE 的代码分析器通常会根据文档注释进行代码提示和补全，并检测潜在错误。

### 关键字和保留字

关键字不能用作标识符和属性名，ES 6 的保留字有：

+ break 终止循环
+ do 和 while 一起使用，在每次循环前执行
+ in 判断对象是否为另一个对象成员，或用于循环语句中
+ typeof 判断类型，返回 String 类型值
+ case 用于 switch 语句中
+ else 用于判断
+ instanceof 判断某个实例对象是否为某个构造函数（类），返回 Boolean
+ var 声明变量（不建议使用）
+ catch 用于错误捕捉处理
+ export 模块导出
+ new 创建一个新的实例对象，并绑定 this 指针
+ void 执行语句，返回 undefined
+ class 用于声明类型（构造函数）
+ extends 继承
+ return 用于函数返回
+ while 循环
+ const 用于声明常量
+ finally 用于错误最终处理
+ super 指向基类
+ with 用于简化对象引用语句（不建议使用）
+ continue 用于进入下一循环
+ for 循环
+ switch 选择
+ yield 用于生成器返回
+ debugger 断点
+ function 声明函数对象
+ this 特殊指针
+ default 用于选择块中，或导出语句中
+ if 判断
+ throw 用于抛出错误
+ delete 用于删除对象成员
+ import 导入语句
+ try 用于异常处理语句

ES 6 同样为未来的标准设置了保留字（一些已实现）：

+ enum 枚举类型
+ implements 实现抽象类/接口
+ package 包
+ interface 接口
+ public 定义公有成员
+ protected 定义保护成员
+ private 定义私有成员
+ static 静态成员
+ let 用于声明变量
+ async 用于声明异步函数
+ await 用于异步函数中，解构 Promise

为了兼容和为未来标准作准备，不应使用保留字和关键字。

### 变量

#### 全局变量

不使用关键字直接声明的变量，可以在全局作用域中访问（不推荐）。

#### 局部变量

局部变量是由 var 或 let 声明的变量，两者在作用域和变量提升特性上不同。

##### var

var 声明一个函数的局部变量，在函数使用后即销毁。在其他代码块中声明的 var 变量可以被外部访问（不推荐）。

由 var 关键字声明的变量会进行声明**提升（hoist）**，所有的变量声明都会被解释器拉到函数作用域顶部（如果有初始化变量，则在语句原位置进行初始化，此前变量值为
undefined，直到初始化语句或变量被赋值），因此可以先使用，后声明（不推荐），这一操作可能会因为作用域不明导致未知错误：

##### let

let 声明一个代码块内的局部变量，不能被外部访问。**let 声明的变量不会被提升**，在 let 变量声明前的作用域区域被称为 “暂时性死区”，在 let 变量声明前调用该变量会抛出 ReferenceError。

此外，在浏览器中，全局作用域声明的 **let 变量不会像 var 一样成为 window 的属性**。

解释器会自动合并重复的 var 声明，而**重复的 let 声明会导致重定义错误**。

##### const

const 和 let 类似，区别是 const 声明的常量必须在声明时被初始化，且不能在之后更改其值。const 声明的变量是**顶层 const**，当其指向一个对象时，不能重新绑定一个对象到 const
对象上，但是可以改变对象的成员。尝试修改 const 变量将导致运行时错误。

### 数据类型

ECMAScript 有6种基本数据类型：

+ Undefined
+ Null
+ Boolean
+ Number
+ String
+ Symbol

和1种复杂数据类型：

+ Object

ES 中不能自定义数据类型（class实际上是构造函数的另一种写法），但是可以用这7种数据类型作多种数据类型使用。

#### typeof 关键字

typeof 关键字接收一个变量作参数，返回其类型的小写字符串。

```js
// 基本数据类型
console.log(typeof undefined);          // undefined
console.log(typeof null);               // object，实际上是Null类型，特殊值null会被操作符认为是空对象的引用
console.log(typeof Boolean());          // boolean
console.log(typeof Number());           // number
console.log(typeof String());           // string
console.log(typeof Symbol());           // symbol

// 复杂数据类型
console.log(typeof Object());           // object
console.log(typeof Function());         // function，严格意义上函数和其他的特殊对象应该属于Object
                                        // 但是为了区分返回各自类型值
```

对于在内部实现了 [[ Call ]] 方法（即`()`函数调用操作符）的对象，typeof 将会返回 `'function'`。

#### Undefined

Undefined 于 ES 3 中提出，目的是与 Null 进行区分。对于没有初始化的变量，其类型和值都为 undefined，因此不必显示声明变量值为 undefined 。undefined 是一个**假值**。

undefined 实际上是 null 的派生类：

```js
// undefined实际上是null的派生类
console.log(undefined == null);             // true
console.log(undefined === null);            // false

// undefined用于标识一个变量尚未初始化
let val = undefined;

// null用于标识一个标识符指向空对象，在将来将会接收一个对象
let obj = null;
```

#### Null

Null 只有一个值，即特殊值 null。语义上，null 表示空对象指针，因此 typeof 将返回 "object"。**定义一个将来需要接收对象的标识符时，应该使用 null 而不是其他类型值**，通过检查其值是否为 null
即可判断该变量是否接收了一个对象。null 也是一个假值。

#### Boolean

布尔类型只有两个值，即 true 和 false，**其他任何值都不是 Boolean 类型**。在使用判断语句和转型函数时，其他数据类型会被转换成对应的布尔值。

+ 被转换为 true 的类型值称为**真值**（truthy），除假值以外的所有值都为真值
+ 被转换为 false 的类型值称为**假值**（falsy）， `false`、`0`、`""`（空字符串）、`null`、`undefined` 和 `NaN`为假值

```js
// 布尔类型只有两个值，即 true 和 false，其他任何值都不是 Boolean 类型
// 真值会被转换为true，假值被转换为false
console.log(true == 1);             // true
console.log(true === 1);            // false

// 以下均为假值
console.log(Boolean(0));
console.log(Boolean(''));
console.log(Boolean(null));
console.log(Boolean(undefined));
console.log(Boolean(NaN));
```

#### Number

ECMAScript 中的 Number 类型用于标识整数和浮点数（双精度），其值的字面量可以是十进制，也可以是八进制（0o开头）和十六进制（0x开头）。

##### 浮点数

ECMAScript 会尽可能的将浮点数转化为整数，最高精度为**17**位小数。小数位后包含至少6个0的浮点数都会以科学记数法标识。

浮点数是不精确的：

```js
// 由于计算机内存中存储浮点数方式的原因，浮点数计算是不精确的
console.log((0.1 + 0.2) === 0.3);               // false
console.log(0.1 + 0.2);                         // 0.30000000000000004

// 比较浮点数
const EPSILON = 1e-17;
if (0.3 - (0.1 + 0.2) < EPSILON) console.log(true);
```

ES 会尽可能地将 Number 类型作为整数存储和操作，因此一些不必要的浮点数字面量会被转换为整数：

```js
console.log(Number.isInteger(1)); 		// true
console.log(Number.isInteger(1.00)); 	// true
console.log(Number.isInteger(1.01)); 	// false
```

IEEE 754 双精度浮点表示法规定在一定范围内的数值可以使用整数表示。这个数值范围从Number.MIN_SAFE_INTEGER（-2^53+1^）到Number.MAX_SAFE_INTEGER（2^53-1^），使用全局
Number 对象的方法`isSafeInteger()`可以判断一个数值是否为安全整数（可以存储为二进制整数）：

```js
console.log(Number.isSafeInteger(-1 * (2 ** 53))); // false
console.log(Number.isSafeInteger(-1 * (2 ** 53) + 1)); // true
console.log(Number.isSafeInteger(2 ** 53)); // false
console.log(Number.isSafeInteger((2 ** 53) - 1)); // true
```

##### 值范围

全局对象 Number 中的两个属性记录了JavaScript 运行环境可以表示的最大与最小数值：

```js
// 全局Number对象有两个属性，记录了 JavaScript 运行环境中的最大和最小值范围
console.log(Number.MAX_VALUE);                  // 1.7976931348623157e+308  (NodeJS,Chromium)
console.log(Number.MIN_VALUE);                  // 5e-324                   (NodeJS,Chromium)
```

超出范围无法表示的值会被转为 Infinity：

```js
// 判断一个值是否无穷大
console.log(isFinite(1 / 0));           // false
console.log(isFinite(1e-17));           // true
```

##### NaN

NaN，Not a Number，任何本应返回数值的操作在失败后都将返回 NaN：

```js
// 特殊值 NaN，Not a Number，表示返回数值的操作失败
console.log(0 / -0);                            // NaN
console.log(1 / 'b');                           // NaN
```

NaN 是假值，不等于任何值，包括自身：

```js
// NaN不等于任何值，包括自身
console.log(NaN === NaN);                       // false
```

`isNaN()` 函数用于判断一个值是否为 NaN（如果值可以转换为数值，视作非 NaN）：

```js
// isNaN函数用于判断一个值是否为NaN
console.log(isNaN(1));                 // false
console.log(isNaN('1'));               // false
console.log(isNaN('a'));               // true
console.log(isNaN(NaN));               // true
console.log(isNaN(true));              // false
console.log(isNaN(false));             // false
```

##### 数值转换

Number()、parseInt() 和 parseFloat() 用于将值转换成 Number。

###### Number

转型函数，规则如下：

```js
// 转型函数Number，将一个值转为Number类型
console.log(Number(true));                  // 1
console.log(Number(false));                 // 0
console.log(Number(2));                     // 2，数值类型直接返回
console.log(Number(null));                  // 0
console.log(Number(undefined));             // NaN

// 字符串规则
// 包含数字的字符串会尝试转换为Number，忽略首位的0，计入正负号，返回十进制
console.log(Number('1'));                   // 1
console.log(Number('+1'));                  // 1
console.log(Number('-1'));                  // -1
console.log(Number('01'));                  // 1
console.log(Number(' 1'));                  // 1
console.log(Number('1.0001'));              // 1.0001
// 十六进制字符串将转换为十进制
console.log(Number('0xdeadbeef'));          // 3735928559
// 空字符串返回0
console.log(Number(''));                    // 0
// 其他情况均返回NaN
console.log(Number('abc'));                 // NaN

// 对象规则
// 首先调用对象的valueOf方法，如果其返回值为NaN，则尝试调用其toString方法，并按照字符串规则转换
console.log(Number({
    valueOf() {
        return 10;
    }
}));                                              // 10
console.log(Number({
    toString() {
        return '20';
    }
}));                                              // 20
console.log(Number({}));                    // NaN
```

###### parseInt

parseInt 用于将一个字符串转换为 Number 整数类型，相比转型函数更注重字符串是否包含数字模式。忽略字符串最前方的空格，**从第一个非空格开始转换**。如果第一个字符不是**数字或正负号**，就返回
NaN，之后一直检测下一个字符，直到结尾或非数字字符。该函数可以接收两个参数，第二个参数为数制。

parseInt 也可以接收非字符串类型（不推荐），并尝试调用 valueOf 和 toString 方法。

```js
console.log(parseInt(1));               // 1
console.log(parseInt(1.00001));         // 1
console.log(parseInt('1'));             // 1
console.log(parseInt('1.00001'));       // 1
console.log(parseInt('     -1'));       // -1
console.log(parseInt('1b'));            // 1
console.log(parseInt('aa', 16));  // 170
console.log(parseInt(''));              // NaN
console.log(parseInt('abc'));           // NaN
```

###### parseFloat

parseFloat 和 parseInt 类似，将字符串转为浮点数。从第一个出现的小数点开始记为小数位，直到字符串末尾或遇到非数字字符结束。如果尝试解析的结果不是浮点而是整数，则返回整数类型。

```js
console.log(parseFloat(1));         // 1
console.log(parseFloat(1.00001));   // 1.00001
console.log(parseFloat('1'));       // 1
console.log(parseFloat('01'));      // 1
console.log(parseFloat('1.00001')); // 1.00001
console.log(parseFloat('0xaa'));    // 0
console.log(parseFloat('1.2.3'));   // 1.2
console.log(parseFloat('1.00'));    // 1
```

#### String

ES 的 String类型用 `''`、`""`、` `` `表示，含义没有区别。

ECMAScript 中的字符串是**不可变的**（immutable），字符串一旦创建则不可更改，如果一个变量字符串，只有为其重新赋值才能改变值内容。

##### 字符字面量

String 类型包含一些字符字面量：

| 字面量 | 含义         |
| ------ | ------------ |
| \n     | 换行         |
| \t     | 制表位       |
| \b     | 退格         |
| \r     | 回车         |
| \f     | 换页         |
| \\     | 反斜杠       |
| \\'    | 引号         |
| \xnn   | 十六进制     |
| \unnnn | Unicode 字符 |

##### toString

大部分类型有 toString 方法，将值类型转换为 String 类型，null 和 undefined 则没有。Number 类型值的 toString 可以接收一个参数，表示转换的数制：

```js
console.log((10).toString());       // '10'
console.log((10).toString(2));      // '1010'
console.log((10).toString(8));      // '12'
console.log((10).toString(10));     // '10'
console.log((10).toString(16));     // 'a'
```

##### 转型函数

转型函数 String() 会调用接收参数的 toString 方法，如果接收 null 或 undefined 则返回字符串 'null' 或 'undefined' ：

```js
console.log(String(10));            // '10'
console.log(String({}));            // '[object Object]'
console.log(String(null));          // 'null'
console.log(String(undefined));     // 'undefined'
```

##### 模板字面量

模板字面量由 ES 6 标准提出，使用``` `表示，支持换行表示，并可以使用`${}`在内部插入 JavaScript 语句。

##### 标签函数

标签函数（tag function）本身是一种常规函数，可以接收一个模板字面量作为参数。模板字面量中的字符部分和插值语句结果会分别作参数传入函数：

```js
let tagFunction = (strings, ...expression) => {
    console.log(strings);                               // [ '', ' + ', ' = ', '' ]
    expression.forEach(value => console.log(value));
    // 1
    // 2
    // 3

    return 'template';
}

const a = 1;
const b = 2;

// 使用标签函数时，直接在函数名后接上模板字面量
console.log(tagFunction`${a} + ${b} = ${a + b}`);       // template
```

让标签函数返回模板字面量原内容：

```js
function zipTag(strings, ...expressions) {
    return strings[0] + expressions.map((e, i) => `${e}${strings[i + 1]}`).join('');
}
```

##### 获取原始的模板字面量

全局对象 String 的 raw 方法是一个标签函数，可以通过 raw 来获取一个模板字面量的原内容（转义字符，不包括执行前的 JavaScript 语句）：

```javascript
const a = 1;
const b = 2;

console.log(String.raw`${a} + ${b} = ${a + b}`);        // 1 + 2 = 3
console.log(`\u00a9`);                                  // ©
console.log(String.raw`\u00a9`);                        // \u00a9
```

标签函数的第一个参数（通常命名为 strings）是一个数组对象，其内部有一个 raw 属性，同样记录了模板字符串的原始内容：

```js
function raw(strings, ...expression) {
    console.log(strings.raw);                           // [ '\\u00aa' ]
}

console.log(`\u00aa`);                                  // ª
raw`\u00aa`;
```

#### Symbol

Symbol 符号类型是 ES 6 新增的基本类型，Symbol 是**唯一且不变**的，用作对象属性的**唯一标识符**。在 ES 6 之前，对象的属性标识符都是 String 类型，通过操作符`[]`或`.`
来访问。如果对象属性键为局部的 Symbol ，在作用域外将无法访问该属性，类似于私有属性。但是可以通过`Object.getOwnPropertySymbols()`来获取所有对象的符号类型键数组，并从中获取符号。

```js
// 创建一个符号
let sym1 = Symbol();
console.log(sym1);                      // Symbol()

// 使用一个字符串来描述一个符号，描述与符号的定义和标识无关
let sym2 = Symbol('sym2');
console.log(sym2);                      // Symbol(sym2)
```

Symbol不是一个构造函数，因此无法像其他类型一样通过new创建一个用对应类型对象包裹的对象。Symbol也没有字面量语法，无法直接使用.操作符和[]操作符访问。

##### 全局符号

使用 Symbol 的 for 方法可以注册一个全局符号，传入一个字符串类型参数作为符号的键，同样作描述使用。非字符串类型的参数会被转换为字符串。如果没有传入参数，键为 undefined（String 类型的 'undefined'）：

```js
// 使用Symbol的for方法注册一个全局符号，接收一个字符串作为符号的键，该键同样是该符号的描述
// 如果不传入参数，则键为"undefined"
// for方法对每个键执行幂等操作，如果该键没有注册，则在全局创建一个符号
let globalSym1 = Symbol.for('global1');
// 如果该键已经注册，则返回该符号
let globalSym2 = Symbol.for('global1');
console.log(globalSym1);                // Symbol(global1)
console.log(globalSym2);                // Symbol(global1)
console.log(globalSym1 === globalSym2); // true
```

使用 Symbol 的 keyFor 方法来查询一个全局符号的键，如果查询的符号非全局符号返回 undefined（值为 Undefined 类型的 undefined），参数必须为符号，否则会抛出 TypeError：

```js
let globalSym1 = Symbol.for('global1');
let globalSym2 = Symbol.for('global1');
let globalSym3 = Symbol.for();
let sym1 = Symbol();

console.log(Symbol.keyFor(globalSym1));     // 'global1'
console.log(Symbol.keyFor(globalSym2));     // 'global1'
console.log(Symbol.keyFor(globalSym3));     // 'undefined'
console.log(Symbol.keyFor(sym1));           // undefined
```

##### 使用符号作对象属性标识符

在可以使用 Number 或 String 作属性标识符的地方都可以用 Symbol 代替，包括字面量属性和 `Object.definedProperty()`，访问该属性只能通过对应的 Symbol：

```js
const name = Symbol('name');
const value = Symbol('value');
const obj = {
    [name]: 'obj'
}
Object.defineProperty(obj, value, {value: 100});

console.log(obj);                               // {Symbol(name): "obj", Symbol(value): 100}
console.log(obj.name);                          // undefined
console.log(obj.value);                         // undefined
console.log(obj[name]);                         // obj
console.log(obj[value]);                        // 100
```

`Object.getOwnPropertyNames()`返回对象实例的常规属性数组，`Object.getOwnPropertySymbols()`返回对象实例的符号属性数组：

```js
console.log(Object.getOwnPropertyNames(obj));       // []
console.log(Object.getOwnPropertySymbols(obj));     // [ Symbol(name), Symbol(value) ]
```

`Object.getOwnPropertyDescriptors()`会返回同时包含常规和符号属性描述符的对象：

```js
console.log(Object.getOwnPropertyDescriptors(obj));
// {
//   [Symbol(name)]: {
//     value: 'obj',
//     writable: true,
//     enumerable: true,
//     configurable: true
//   },
//   [Symbol(value)]: {
//     value: 100,
//     writable: false,
//     enumerable: false,
//     configurable: false
//   }
// }
```

`Reflect.ownKey()`会返回常规和符号类型键的数组：

```js
console.log(Reflect.ownKeys(obj));              // [ Symbol(name), Symbol(value) ]
```

遍历所有符号类型键来获取符号：

```js
let o = {
    [Symbol('foo')]: 'foo val',
    [Symbol('bar')]: 'bar val'
};

console.log(o);
// {Symbol(foo): "foo val", Symbol(bar): "bar val"}

let barSymbol = Object.getOwnPropertySymbols(o)
    .find((symbol) => symbol.toString().match(/bar/));

console.log(barSymbol);
// Symbol(bar)
```

##### 常用内置符号

常用内置符号（well-known symbol）用于暴露 ECMAScript 语言的内部行为，类似 C++ 和一些语言的运算符重载、成员函数重写。通过重写内置符号键的属性或方法，来**改变原生结构的行为**
。使用常用内置符号作为键的成员一般是由 ES 操作符或内置对象的方法来调用的，无法直接调用。

#### Object

在 ECMAScript 中，Object（对象）类型是所有其他对象的基类。使用关键字`new`来创建一个对象：

```js
// 创建一个对象
let obj1 = new Object();

// 简化写法
let obj2 = {};
```

一些 Object 方法：

```js
// 创建一个对象
let obj1 = new Object();

// 定义对象属性
Object.defineProperties(obj1, {
    'name': {
        value: 'obj1',
    },
    'value': {
        value: 10
    }
});

// 简化写法
let obj2 = {
    name: 'obj2',
    value: 20
};

// 判断对象实例（非原型）上是否有给定属性
console.log(obj1.hasOwnProperty('name'));       // true

// 判断一个对象是否为另一个对象的原型
console.log(obj1.isPrototypeOf(obj1));          // false

// 判断对象的属性是否为可枚举的（用于for-in）
console.log(obj1.propertyIsEnumerable('name')); // false

// 返回对象的字符串表示
console.log((new Date()).toString());                       // 国际标准时间格式描述

// 返回对象的字符串表示，本地化显示
console.log((new Date()).toLocaleString());                 // 国家/地区标准时间格式描述

// 返回对象的Number、Boolean或String表示，一般和toString行为一致
console.log(obj1.valueOf());                                // {}
```

### 操作符

ECMAScript 定义了一组操作符，可用于各种类型的值。在对象上使用时，操作符一般会调用`valueOf`或`toString`来获取对象的值表示。

#### 递增/递减操作符

一元操作符`++`、`--`操作符可以用于任何类型，包括且不限于 Number，还可用于 String、Boolean、Object。

对于 Number，执行一般操作；

对于非 Number，如果可以被直接转换成 Number（调用 `toString()`或`valueOf()`），则先转换再运算，否则返回 NaN：

```js
// String
let str1 = '123';
let str2 = '123a';

console.log(++str1);        // 124
console.log(++str2);        // NaN

// Boolean
let flag1 = true;
let flag2 = false;

console.log(++flag1);       // 2
console.log(++flag2);       // 1

// Object
let obj1 = {};
let obj2 = {
    valueOf() {
        return 20;
    }
};

console.log(++obj1);        // NaN
console.log(++obj2);        // 21
```

#### 加减操作符

加减操作符和数学中的加减操作符作用相同，对于 Number 类型是计算数值，对于其他类型将调用相应规则。

对于 String，`+`操作符将连接字符串：

+ 如果两个值都为字符串，则连接
+ 如果有一个值不是字符串，将另一个值转为字符串并连接

```js
let result1 = 5 + 5; // 两个数值
console.log(result1); // 10
let result2 = 5 + "5"; // 一个数值和一个字符串
console.log(result2); // "55"
```

`-`操作符则是进行数学减运算，会尝试将 String 转换成 Number，

```js
console.log('1' + '2' + '3');       // '123'
console.log('1' + '2' - '3');       // 9
console.log('a' - '3');             // NaN
```

#### 位操作符

ECMAScript 中的 Number 类型按照 IEEE 754 浮点数算术标准中的双精度64位方式存储。

在实际运算时，先将64位浮点数值转换为32位数值进行运算，再转换为64位浮点数存储，使用时64位存储方式是不可见的，对于程序员而言只需考虑32位数值。

正整数按前31位存放数值，32位存放符号的规则在内存中存储。负数使用补码，由绝对值取反加一获得。

##### 按位非

一元操作符波浪符`~`按数值的二进制位进行取反操作：

```js
let num1 = 25; 		// 二进制 00000000000000000000000000011001
let num2 = ~num1; 	// 二进制 11111111111111111111111111100110
console.log(num2); 	// -26
```

##### 按位与

二元操作符和符`&`进行按二进制位的与操作，接收两个参数：

```js
let result = 25 & 3;
console.log(result); // 1
```

##### 按位非

二元操作符管道符`|`进行按二进制位的非操作：

```js
let result = 25 | 3;
console.log(result); // 27
```

##### 按位异或

二元操作符脱字符`^`进行按二进制位的异或（即或非）操作：

```js
let result = 25 ^ 3;
console.log(result); // 26
```

##### 左移

二元操作符双小于号`<<`进行按位左移操作，接收值和位移位数作参数，并保留值的符号，缺位补0：

```js
console.log(2 << 5);        // 64
console.log(-2 << 5);       // -64
```

##### 有符号右移

二元操作符双大于号`>>`进行按位右移操作，接收值和位移位数作参数，并保留值的符号，缺位补0：

```js
console.log(64 >> 5);       // 2
console.log(-64 >> 5);      // -2
```

##### 无符号右移

二元操作符三大于号`>>>`进行按位右移操作，接收值和位移位数作参数，符号位也进行右移，缺位补0：

```js
console.log(64 >>> 5);      // 2
console.log(-64 >>> 5);     // 134217726
```

#### 布尔操作符

+ 逻辑非`!`：一元逻辑非操作符感叹号符，尝试将参数转换成 Boolean 类型后取反，双感叹号符`!!`相当于调用转型函数`Boolean()`，在布尔运算中拥有最高优先级
+ 逻辑与`&&`：二元逻辑与操作符双和符，短路特性，优先级高于逻辑或
+ 逻辑或`||`：二元逻辑或操作符双管道符，短路特性

#### 乘性操作符

+ 乘法`*`：二元操作符乘号，会尝试使用`Number()`转型函数将非数值类型转换
+ 除法`/`：二元操作符斜杠，会尝试使用`Number()`转型函数将非数值类型转换，`0 / 0`返回特殊值`NaN`
+ 取模`%`：二元操作符百分号，会尝试使用`Number()`转型函数将非数值类型转换

#### 指数操作符

ES 7 标准提出了指数二元操作符双星号`**`，代替原来的`Math.pow()`方法。

```js
console.log(Math.pow(3, 2)); 		// 9
console.log(3 ** 2); 				// 9
console.log(Math.pow(16, 0.5)); 		// 4
console.log(16 ** 0.5); 				// 4
```

#### 关系操作符

二元关系操作符`<`、`>`、`>=`、`<=`比较两值关系，并返回 Boolean。关系操作符会尝试将值转换为数值后比较。**对于 NaN，任何值与其比较都将返回 false。**

+ 对于数值，直接比较大小
+ 对于字符串，先将字符转为编码数字，再比较数值：

```js
let result = "23" < "3"; 	// true
```

+ 如果任意一值是数值，则转换另一值为数值后比较：

```js
let result1 = "23" < 3;		// false
let result2 = "a" < 3;		// false，'a'会转换为NaN
```

+ 如果任意一值是布尔值，则布尔值会转换为数值再比较
+ 如果任意一值是对象，调用其`valueOf()`或`toString()`方法

#### 相等操作符

在 ES 6 标准前，原本的相等操作符在比较前会进行类型转换。在 ES 6 标准出台后，添加了**全等操作符**，**不进行类型转换**。

##### 等于和不等于

二元操作符`==`和`!=`，会在比较前进行强制类型转换并比较值。（不推荐使用）

+ 比较前转换类型，存在数值时，将其他值转换为数值再比较
+ null 和 undefined 相等
+ null 和 undefined 无法转换类型
+ NaN 不等于任何值，包括自身

| 表达式            | 结果 |
| ----------------- | ---- |
| null == undefined | T    |
| "NaN" == NaN      | F    |
| 5 == NaN          | F    |
| NaN == NaN        | F    |
| NaN != NaN        | T    |
| false == 0        | T    |
| true == 1         | T    |
| true == 2         | F    |
| undefined == 0    | F    |
| null == 0         | F    |
| "5" == 5          | T    |

##### 全等于和不全等于

二元操作符`===`=和`!==`，在比较前不进行类型转换，只有类型和值都相等的情况下`===`返回 true。

#### 条件操作符

三元条件操作符问号符和冒号符组合`expr ? true_value : false_value`，相当于 if-else 语句。

#### 赋值操作符

二元赋值操作符等号符`=`，用于赋值，左侧称左值（left-hand），右侧称右值（right-hand）。

其他一元复合赋值操作符：

+ 乘后赋值`*=`
+ 除后赋值/=`
+ 取模后赋值%=`
+ 加后赋值+=`
+ 减后赋值-=`
+ 左移后赋值`<<=`
+ 右移后赋值>>=`
+ 无符号右移后赋值>>>=`

#### 逗号操作符

逗号操作符`,`用于在一句语句中执行多个操作，如复合赋值语句：

```js
let num1 = 1, num2 = 2, num3 = 3;
```

在赋值时使用逗号操作符分隔值，最终会返回表达式中最后一个值：

```js
let num = (5, 1, 4, 8, 0); // 0
```

### 流程控制语句

+ if：常用的判断语句
+ do-while：至少会执行一次的循环语句
+ while：循环语句
+ for：循环语句
+ for-in：严格迭代语句，用于遍历**可枚举**对象中的**非符号键**属性，获取**键的值**，由于对象属性排列是无序的，在不同的运行环境中不一定能保证返回的键的顺序是一致的，一般用于遍历**对象**类型

```js
let obj = {
    name: 'obj',
    type: 'Object',
    [Symbol('value')]: 10
};

for (const key in obj) {
    console.log(key);
    // name
    // type
    console.log(obj[key]);
    // 'obj'
    // 'Object'
}
```

+ for-of：严格迭代语句，用于遍历**可迭代**对象的元素，获取的是**元素值**，一般用于数组等：

```js
let arr = ['foo', 'bar', 'baz'];

for (const value of arr) {
    console.log(value);
    // foo
    // bar
    // baz
}
```

+ for-await-of：于 ES 7 提出，用于遍历生成期约的可迭代对象
+ 标签语句：用于给语句加上标签，可以用于嵌套循环等复杂的大段代码块中，用于标识代码块作用：

```js
loop1: for (let i = 0; i < 10; i++) {
    console.log(i);
    loop2: for (let j = 0; j < 10; j++) {
        console.log(j);
        loop3: for (let k = 0; k < 10; k++) {
            console.log(k);
        }
    }
}

// 另一种用法是类似于 C 中 goto 语句的目的地，在 ECMAScript 中配合 continue 和 break 同样可以实现类似效果，不建议使用。
```

+ with：用于设置一块代码区域为特点的对象作用域，**不建议使用**，严格模式下使用 with 会抛出错误：

```js
let qs = location.search.substring(1);
let hostName = location.hostname;
let url = location.href;

with (location) {
    let qs = search.substring(1);
    let hostName = hostname;
    let url = href;
}
```

+ switch：选择判断语句，和其他的一些语言不同，ES 中的 case 可以使用**任何类型的值**

> ECMAScript 包含7种数据类型：Undefined、Null、Boolean、Number、String、Symbol 和 Object
>
> Null 类型的特殊值 null 表示指向一个空对象
>
> Number 类型不区分浮点数和整数，以64位双精度方式在内存中存储，以32位方式运算
>
> Object 是复杂数据类型，是所有对象的基类
>
> 没有指定返回值的函数将返回 undefined

## 04 变量、作用域与内存

ECMAScript 的变量是松散类型的，变量的值和类型在生命期内可以改变。ES 不允许直接访问内存空间。

### 原始和引用值

ES 变量可以包含两种类型：原始值和引用值。

**原始值（primitive value，或称基元值）**是最简单的数据，包含6种基础类型的值。原始值是**按值访问（by value）**的，程序员操作的是存储在变量中的实际值。原始值存储在**栈内存**中。

**引用值（by reference）**是由多个值构成的对象，包含复杂类型 Object 的值。引用值是**按引用（by reference）**
访问的，不能直接操作对象所以在内存空间，而是按引用访问。包含引用值的变量实际在栈内存中存储了一个**指针**，其指向的对象则在**堆内存**中。

#### 动态属性

引用值可以在定义后随时动态地添加属性，而原始值不能：

```js
// 引用值可以动态地添加属性
let man = {};
man.name = 'Steven';
console.log(man.name);                  // 'Steven'

// 原始值不能动态添加属性，即使不会报错
let num = 10;
num.name = 'number';
console.log(num.name);                  // undefined
```

除了`Symbol()`外，**其他类型的转型函数可以当做构造函数使用**，通过 new 关键字实例化一个被初始值包裹的同类型对象，称为**基础类型包装器**
。这和直接使用字面量初始一个值不同，实例化的结果将是一个引用值对象，其行为类似原始值，因此可以动态添加属性：

```js
// 使用new关键字实例化一个原始类型的引用值，拥有和原始值相似的行为，称为基础类型包装器
let str1 = 'string';
let str2 = new String('string');

str1.name = 'str1';
str2.name = 'str2';

console.log(str1);              // 'string'
console.log(str2);              // [String: 'string'] { name: 'str2' }
console.log(str2.toString());   // 'string'
console.log(str2.valueOf());    // 'string'

console.log(str1.name);         // undefined
console.log(str2.name);         // str2

console.log(typeof str1);       // 'string'
console.log(typeof str2);       // 'object'

// 字符串类型是不变的，使用包装器也一样无法改变值，操作包装器对象时可能会发生类型转换
str2 += ' str2';
console.log(str2);              // 'string str2'
console.log(typeof str2);       // 'string'
```

#### 复制值

复制原始类型值时，本质是将一个原始类型值存放的内存中的数据**复制到另一内存区域中**，两者互不干扰，相互独立：

```js
let num1 = 10;
let num2 = num1;

num2 = 20;

console.log(num1);      // 10
console.log(num2);      // 20
```

复制引用类型值时，本质是**新建指针存储到栈中**，该指针指向原本在堆中的引用类型对象。同时指向同一对象的指针通过引用方式访问时修改的是同一个实例：

```js
let obj1 = {name: 'obj1'};
let obj2 = obj1;

obj2.name = 'obj2';
console.log(obj1.name); // 'obj2'
```

#### 值的参数传递

ES 中参数均**按值传递**，即复制值。对于原始类型是复制新值，对于引用类型是复制新指针。和 C++ 不同，ES 中没有引用传递，因此即使是引用类型的参数**也将占用一块新的内存来存放临时指针**。

```js
// 值的参数传递始终是值传递
let fun = (arg) => {
    if (typeof arg === 'number') arg **= 2;
    else arg.value **= 2;
};

let num = 10;
let obj = {value: 20};

fun(num);               	// 复制值
console.log(num);           // 10

fun(obj);               	// 复制指针
console.log(obj.value);     // 400

fun(obj.value);         	// 复制值
console.log(obj.value);     // 400
```

#### instanceof

操作符`instanceof`用于检测一个变量是否为给定引用类型的实例，由对象原型链决定，是则返回 true。如果用 `instanceof`检测原始值，始终返回 false。

### 执行上下文和作用域

每个**执行上下文（context）**都与一个**变量对象（variable object）**关联，变量对象的作用域决定了内部成员能访问哪些数据。变量对象无法通过代码访问，但是解释器后台将利用变量对象作处理。

上下文对应的变量对象会在生命期结束时被销毁，包括内部所有变量和函数。

上下文类型：

+ 全局上下文
+ 函数上下文
+ 块上下文

#### 全局上下文

**全局上下文**即最外层的执行上下文。在浏览器中，最外层的执行上下文即 window 窗口对象：

```js
var val1 = 10;
let val2 = 20;

console.log(this);                  // 浏览器：window对象，NodeJS：空对象
console.log(this.val1);             // 浏览器：10，NodeJS：undefined
console.log(this.val2);             // 浏览器：undefined，NodeJS：undefined
```

浏览器全局上下文会在关闭窗口（选项卡）或关闭浏览器时销毁。

#### 上下文栈

**上下文栈**中存储每个上下文对应的变量对象，栈底为全局上下文。当流程进入函数时，会将函数上下文推入上下文栈，如果下个进入的上下文也是函数则此函数将**中断**。当前函数流程结束返回时则弹出上下文栈顶元素，返回中断的上下文中继续执行流程。

#### 作用域链

上下文中代码执行时会创建变量对象的一个**作用域链（scope chain）**，决定各级上下文访问变量和函数的顺序。作用域链中的下一个变量对象可以访问链之前所有变量对象内部成员（变量、函数、函数参数）。

**标识符查找**开始于作用域的链尾，逐级查找标识符对应的内容，如果找到则终止查找并返回值，如果没有找到则说明标识符对应的变量未声明。作用域链中的对象有属于自己的**原型链**。

```js
// 作用域链
{	// 变量对象1
    // 链首
    let val1 = 1;
    let name = '1';
    {	// 变量对象2
        let val2 = 2;
        let name = '2';
        {	// 变量对象3
            // 链尾
            let val3 = 3;
            let name = '3';

            // 作用域链决定能访问的变量和函数以及访问顺序
            // 链尾变量对象可以访问之前所有变量对象内的函数和变量
            console.log(val1);      // 1
            console.log(val2);      // 2
            console.log(val3);      // 3
            // 如果遇到重名变量或函数，则按访问顺序，访问最近的重名变量或函数
            console.log(name);      // '3'
        }
    }
}
```

对于函数上下文，则其**活动对象（activation object）**作为其变量对象，活动对象最初只有一个成员，即可迭代对象`arguments`，全局上下文没有这个成员。

#### 作用域链增强

执行上下文主要有全局上下文和函数上下文两种。此外`eval()`函数执行时会使用第三种上下文。

而在使用`with`和`try/catch`语句时，当前作用域链会被增强，链首会被添加一个新作用域链，当语句结束时消除。

+ with：进入 with 语句内部时，在链首追加指定对象的上下文
+ catch：进入 catch 内部时，在链首追加一个包含错误对象的变量对象

### 变量声明

#### var

+ var 声明的变量会被自动添加到最近的上下文（函数和全局）
+ var 声明的变量会被提升，并忽略重复声明
+ var 作用域为函数上下文，在一些循环语句（if、while）中声明变量会泄漏到循环外部

#### let 和 const

+ let 作用域为块级（花括号`{}`内部范围）
+ let 在作用域内不能重复声明
+ let 不会提升，不会添加到上下文，具有暂时性死区（temporal dead zone），在声明前无法访问
+ const 必须在声明时被初始化
+ const 变量是顶层 const，不能重新绑定对象，可以修改绑定的对象
+ const 声明的变量初始化的引用值（对象）在运行时会被替换为实际的值，不会通过查询表进行查找，这是一种解释器优化

> 尽量使用 const 来声明变量，避免因为不必要的重新赋值产生问题代码。不再使用 var 。

### 垃圾回收

JavaScript 是使用垃圾回收技术的语言，无需程序员手动管理堆内存。垃圾回收的基本思路是：**确定不会再使用的变量并释放**。该过程是周期性的，每隔一定时间进行一次。确定无用变量的过程称为**标记**，ES
标准在浏览器环境实现的过程中，主要有两种标记策略。

#### 标记清理

**标记清理（mark-and-sweep）**是最常用的垃圾回收策略，大部分浏览器都主要使用该策略。

1. 标记内存中所有的变量
2. 将上下文中的变量、被上下文中变量引用的变量的标记去除
3. 回收带标记的变量

#### 引用计数

**引用计数（reference counting）**策略不常用，是最早的垃圾回收策略。该策略的基本思想是：当一个变量值被创建或引用时，计数+1，当值的引用减少时，计数-1，如果计数为0则清除。

这种策略在一些情况下将导致内存泄漏问题，如**循环引用**：

```js
function problem() {
    let objectA = new Object();
    let objectB = new Object();
    objectA.someOtherObject = objectB;
    objectB.anotherObject = objectA;
}

// 函数结束时，objectA和objectB的计数为2，两者并不会被清除
```

> 在 IE8 中，DOM 和 BOM 并非 JavaScript 对象，而是属于浏览器中使用 C++ 编写的组件对象模型（COM，omponent Object Model）对象，这将造成跨语言的循环引用问题。IE9 将 DOM 和 BOM 都修改为了原生 JavaScript 对象，但是 IE 的问题远不止这些。
>
> IE7 发布后，JavaScript 引擎的垃圾回收程序被调优为动态改变分配变量、字面量或数组槽位等会触发垃圾回收的阈值。IE7 的起始阈值都与 IE6 的相同。如果垃圾回收程序回收的内存不到已分配的15%，这些变量、字面量或数组槽位的阈值就会翻倍。如果有一次回收的内存达到已分配的85%，则阈值重置为默认值。这么一个简单的修改，极大地提升了重度依赖 JavaScript 的网页在浏览器中的性能。

### 内存管理策略

目前，浏览器中的 JavaScript 运行在一个较为特殊的环境下，和桌面以及其他运行环境不同，为浏览器分配的内存往往更少。因此让页面保持较少的运行内存十分重要。对于其他环境中的 NodeJS
服务器与桌面应用一样，尽可能提高程序运行效率是非常重要的。

#### 释放内存

尽可能不要申请不必要的内存空间，对于不再使用的变量，可以直接手动释放。**解除引用**是常用的内存释放手段，方法是将一个变量赋值为`null`，原先的变量值会被自动释放。

#### 弃用 var

var 可能导致不必要的变量泄漏，而块级作用域的 let 和 const 在块作用域结束后会自动释放。

#### 隐藏类和删除操作

JavaScript V8 是目前最流行的 JavaScript 解释器引擎，在众多浏览器和 NodeJS 中都使用 V8。V8 在编辑 JavaScript 为机器码时会利用**隐藏类**特性。

V8 在运行时会将对象与一个隐藏类关联，跟踪他们的属性特征。这和其他高级编程语言中的自定义数据类型相似，只是 ES 中，自定义数据类型相当于一个构造器。用同一个构造器声明的多个对象将共用一个隐藏类，**
但是为对象动态添加或删除属性或方法会导致生成一个新的隐藏类**，这将降低运行效率。因此，最好的方式是**将不再需要的属性绑定到空对象上**，并减少动态赋值，使用构造器或新的`class`语法：

```js
function Article() {
    this.title = 'Inauguration Ceremony Features Kazoo Band';
    this.author = 'Jake';
}

let a1 = new Article();
let a2 = new Article();

// delete a1.author;		删除操作会导致新的隐藏类出现
a1.author = null;
```

#### 常见的内存泄漏问题

##### 意外声明的全局变量

没有使用关键字声明的变量会被挂载到全局上下文中，使用 var 声明的变量可能在无疑中泄漏（如在循环条件中的变量）。使用新的 let 和 const 来避免这些问题。

##### 闭包泄漏

**闭包**很容易在不经意间泄漏变量，通常发生在回调函数和函数上下文的函数声明中：

```js
let outer = function () {
    let name = 'Jake';
    return function () {
        return name;
    };
};
```

函数 outer 返回一个内部声明的函数，该函数内部引用了局部变量 name 。此变量本该在 outer 执行完毕后被销毁，但是由于返回的函数进行了引用。如果有一个变量接收了这个闭包，只有在该闭包被销毁时 name 才会一同销毁。因此，**
尽可能不要再被返回的闭包中引用局部变量**。

##### 定时器泄漏

定时器的回调函数导致的内存泄漏属于**闭包**泄漏的一种，如果回调函数内部引用了一个外部上下文的变量，在定时器结束前该变量值会一直存在：

```js
let name = 'Jake';
setInterval(() => {
    // name直到100ms后被释放
    console.log(name);
}, 100);
```

##### 静态分配和对象池

一般来说，对于浏览器前端的业务场景并不需要特别注意垃圾回收问题。但是随着浏览器技术的发展和 JavaScript 运用环境的拓展，在前端的 canvas 接口和服务器端应用上可能需要特别的软件优化。

在一些场景下，可能会有大量复杂数据类型被不断创建和销毁（如生成计算机图形接口所需要的多维向量对象）。**如果解释器发现一些对象经常被创建和销毁，就会提高垃圾回收频率**，这将导致总体性能下降。此时，可以使用**对象池**技术。

假如有一个一维向量增加函数，用于计算并返回一个一维向量：

```js
function addVector(a, b) {
    let resultant = new Vector();
    resultant.x = a.x + b.x;
    resultant.y = a.y + b.y;
    return resultant;
}
```

每次调用该函数，其内部将实例化一个对象，如果频繁调用，解释器会提高局部垃圾回收频率。

然而这个对象的实例化是不必要的，可以让函数接收一个外部对象并修改：

```js
function addVector(a, b, resultant) {
    resultant.x = a.x + b.x;
    resultant.y = a.y + b.y;
    return resultant;
}
```

这个外部对象不应直接定义在全局上下文中，这会污染全局作用域。其中的一种解决方案是使用对象池，创建一个全局上下文中的对象来存储不同的向量类型实例对象：

```js
// vectorPool 是已有的对象池
let v1 = vectorPool.allocate();
let v2 = vectorPool.allocate();
let v3 = vectorPool.allocate();

v1.x = 10;
v1.y = 5;
v2.x = -3;
v2.y = -6;

addVector(v1, v2, v3);
console.log([v3.x, v3.y]); // [7, -1]
vectorPool.free(v1);
vectorPool.free(v2);
vectorPool.free(v3);

// 如果对象有属性引用了其他对象
// 则这里也需要把这些属性设置为null
v1 = null;
v2 = null;
v3 = null;
```

对象池只需分配向量，当池中对象数量不够时分配新对象，当池中有足够对象时分配对象，其他变量在不需要时解除对这些变量的引用即可。本质上是一种**贪婪算法**，分配**静态**但**增长**
的内存。对象池的存储结构适合用内置数组实现，但是要注意不必要的垃圾回收（JavaScript 在某个数组预分配空间不足时会删除并增加一个新数组，尽可能在对象池存储数组中手动分配足够多的空间）。

```js
let vectorList = new Array(100);		// 100个向量对象是否足够？
let vector = new Vector();
vectorList.push(vector);
```

> 原始值保存在栈上，引用值保存在堆中
>
> 原始值复制创建新值，引用值复制创建新指针
>
> typeof 用于确定原始值类型，instanceof 用于确定引用值类型
>
> 执行上下文分全局上下文、函数上下文和块级上下文
>
> 标记清理策略是目前主流的垃圾回收策略
>
> 静态操作可以提高运行效率，减少垃圾回收次数，使用 null 值代替 delete 操作

---

## 05 基本引用类型

**引用值**是某个特定引用类型的**实例**。ECMAScript 中的引用类型是把数据和功能组织在一起的结构，这与面向对象方法中的**类**很相似，但 ECMAScript 中实际上并没有类的存在。引用类型有时也被称为**对象定义**。

在 ES 中，一个实例通过 new 关键字和一个**构造函数（constructor）**来实例化。

### Date

Date 类型将日期保存为**自协调世界时（UTC，Universal Time Coordinated）**，为1970年1月1日0时至今的**毫秒数**，最多表示1970年之后285616年的时间。

构造函数`Date()`可以接收一个参数，单位为毫秒的 Number 类型。在不传入参数的情况下默认存储当前时间。

#### 常用方法

+ `Date.parse()`：接收一个表示时间的字符串，支持`月/日/年`、ISO 8601时间格式等形式的字符串。如果解析失败返回 NaN
+ `Date.UTC()`：接收一组参数，接收年、月（从0开始为1）、日（从1开始）、时、分、秒和毫秒。年和月是必选参数，解析失败返回 NaN

构造函数在接收非 Number 类型参数时会尝试调用 parse 和 UTC 方法解析。

#### 继承的方法

Date 类型重写了以下方法：

+ `toLocalString()`：返回与浏览器运行的本地环境一致的日期和时间
+ `toString()`：返回格林尼治时间字符串
+ `valueOf()`：返回1970.1.1至今毫秒数

#### 日期格式化方法

+ `toDateString()`：显示日期中的周几、月、日、年（格式特定于实现）
+ `toTimeString()`：显示日期中的时、分、秒和时区（格式特定于实现）
+ `toLocaleDateString()`：显示日期中的周几、月、日、年（格式特定于实现和地区）
+ `toLocaleTimeString()`：显示日期中的时、分、秒（格式特定于实现和地区）
+ `toUTCString()`：显示完整的UTC 日期（格式特定于实现）

### RegExp

ES 支持正则表达式，可以通过字面量创建一个 RegExp 对象。

#### 标记

正则对象可以带 flag（标记），设置的标记可以使用 flags 方法查询。

+ g：全局模式，表示查找字符串的全部内容，而不是找到第一个匹配的内容就结束
+ i：不区分大小写，表示在查找匹配时忽略pattern 和字符串的大小写
+ m：多行模式，表示查找到一行文本末尾时会继续查找。
+ y：粘附模式，表示只查找从lastIndex 开始及之后的字符串
+ u：Unicode 模式，启用Unicode 匹配
+ s：dotAll 模式，表示元字符.匹配任何字符（包括\n 或\r）

#### 实例属性

+ global：布尔值，表示是否设置了g 标记。
+ ignoreCase：布尔值，表示是否设置了i 标记。
+ unicode：布尔值，表示是否设置了u 标记。
+ sticky：布尔值，表示是否设置了y 标记。
+ lastIndex：整数，表示在源字符串中下一次搜索的开始位置，始终从0 开始。非全局模式始终不变
+ multiline：布尔值，表示是否设置了m 标记。
+ dotAll：布尔值，表示是否设置了s 标记。
+ source：正则表达式的字面量字符串（不是传给构造函数的模式字符串），没有开头和结尾的斜杠
+ flags：正则表达式的标记字符串。始终以字面量而非传入构造函数的字符串模式形式返回（没有前后斜杠）

#### 常用实例方法

+ exec：应用正则，接收一个参数作为要应用正则的字符串，返回包含第一个匹配信息的数组，该数组包含所有查询到的匹配模式的字符串，以及额外的 index（查询到的第一个字符串的起始下标） 与 input（输入的字符串）
  属性，如果是全局查找只返回第一个字符串的 index
+ test：应用正则，接收一个字符串，如果字符串包含模式则返回 true

### 原始值包装类型

ES 中提供三种特殊的引用类型 Boolean、Number 和 String 来包装原始值。在使用字面量初始化包含原始值变量时，ES 会为这些原始值自动用对应类型的对象包装，从而实现在原始值上调用方法。

原始值包装类型对象的声明期仅在使用它的语句范围内。当语句结束后，对象就被销毁了，因此在原始值上添加属性是无效的：

```js
let str1 = 'str1';
console.log(typeof str1);               // 'string'
console.log(str1.substring(0, 3));      // 'str'
str1.name = '1';                        // str1是新创建的一个原始值包装类型对象，语句结束后销毁
console.log(str1.name);                 // undefined，调用str1再次创建一个对象，该对象没有默认的name属性
```

可以显式使用 Boolean、Number 和 String 构造函数创建原始值包装对象，生成的实例具有引用类型的特性：

```js
let str2 = new String('str2');		    // 不使用new则是调用转型函数
console.log(typeof str2);               // 'object'
str2.name = '2';
console.log(str2.name);                 // '2'
```

一般不会显式调用这些构造函数，如果必须要使用，需要注意：

+ 生成的实例是引用类型值，typeof 将返回 `'object'`
+ 在判断语句中，如果引用类型值要被转为 Boolean 类型值，将返回 true，对于一个值为 false 的引用类型 Boolean 对象，判断时依旧返回 true ，这在语句中可能导致阅读歧义。不建议生成一个引用类型为 Boolean
  的实例

#### 字符串方法

String 类型拥有几个常用的方法。

##### concat 连接

`concat()`用于拼接多个字符串，接收任意数量字符串类型参数，返回拼接的结果：

```js
let stringValue = "hello ";
let result = stringValue.concat("world", "!");
console.log(result); 		// "hello world!"
console.log(stringValue); 	// "hello"
```

一般使用更方便的`+`操作符替代。

##### slice 截取范围

截取字符串部分。接收一个或两个参数，第一个参数为起始位置下标，第二个为结束位置下标（不包括下标位置的字符串，截取该位置前的内容，默认截取至结尾）。**参数为负数时**
，参数与字符串长度相加后输入，即对于第一个参数而言，负数代表从倒数第几位开始截取。

##### substring 截取范围

截取字符串部分。接收一个或两个参数，第一个参数为起始位置下标，第二个为结束位置下标（不包括下标位置的字符串，截取该位置前的内容，默认截取至结尾）。**参数为负数时**，转化为0。

##### substr 截取数量

截取字符串部分。接收一个或两个参数，第一个参数为起始位置下标，第二个参数为截取后字符串长度（默认截取全部剩余）。**参数为负数时**
，第一个参数与字符串长度相加后输入，即对于第一个参数而言，负数代表从倒数第几位开始截取，第二个参数会转换为0，截取0长度的字符串。

```js
let stringValue = "hello world";
console.log(stringValue.slice(3)); 			// "lo world"
console.log(stringValue.substring(3)); 		// "lo world"
console.log(stringValue.substr(3)); 		// "lo world"
console.log(stringValue.slice(3, 7)); 		// "lo w"
console.log(stringValue.substring(3, 7)); 	// "lo w"
console.log(stringValue.substr(3, 7)); 		// "lo worl"
console.log(stringValue.slice(-3)); 		// "rld"
console.log(stringValue.substring(-3)); 	// "hello world"
console.log(stringValue.substr(-3)); 		// "rld"
console.log(stringValue.slice(3, -4)); 		// "lo w"
console.log(stringValue.substring(3, -4)); 	// "hel"
console.log(stringValue.substr(3, -4)); 	// "" (empty string)
```

##### indexOf 和 lastIndexOf 查找子串位置

`indexOf()`获取第一个给定子字符串出现的位置，找到则返回第一个出现的子字符串第一个字符的下标，没有找到返回 -1。`lastIndexOf()`为反向查找：

```js
let stringValue = "hello world";
console.log(stringValue.indexOf("o")); 		// 4
console.log(stringValue.lastIndexOf("o")); 	// 7
```

可以接收第二个参数，用于指定开始查找的位置：

```js
let stringValue = "hello world";
console.log(stringValue.indexOf("o", 6)); 		// 7
console.log(stringValue.lastIndexOf("o", 6)); 	// 4
```

查找所有子字符串位置算法：

```js
function findAllIndex(string, target) {
    let positions = [];
    let pos = string.indexOf(target);
    while (pos > -1) {
        positions.push(pos);
        pos = stringValue.indexOf(target, pos + 1);
    }

    return positions;
}
```

##### includes 确认是否存在子串

确认字符串中是否存在给定子字符串。第一个参数接收给定的字符串，第二个可选参数接收开始查找的位置。如果存在返回 true：

```js
let message = "foobarbaz";
console.log(message.includes("bar")); // true
console.log(message.includes("qux")); // false
```

##### startsWith

确认从给定索引位置开始的子字符串是否匹配给定字符串。第一个参数接收给定的字符串，第二个可选参数接收开始查找的位置，默认为0：

```js
let message = "foobarbaz";
console.log(message.startsWith("foo")); // true
console.log(message.startsWith("foo", 1)); // false
```

##### endsWith

确认从索引位置（string.length - substring.length）开始的子字符串是否匹配给定字符串。第一个参数接收给定字符串，第二个可选参数接收指定的末尾位置（即将字符串当作在该位置结尾），默认为字符串长度：

```js
let message = "foobarbaz";
console.log(message.endsWith("bar")); // false
console.log(message.endsWith("bar", 6)); // true
```

##### trim 删除首尾空格

用于去除字符串首尾全部空格，不包括内部空格。`trimStart()`和`trimEnd()`用于去除首或尾空格。

##### repeat 重复字符串内容

重复字符串内容并拼接，返回拼接结果：

```js
let stringValue = "na ";
console.log(stringValue.repeat(16) + "batman");
// na na na na na na na na na na na na na na na na batman
```

##### padStart 和 padEnd

`padStart()` 在字符串起始位置添加给定数量的给定字符串，`padEnd()`从尾部开始重复。第一个参数为重复次数，第二个参数为重复的字符串，默认为空格。

##### replace 替换内容

去除字符串中与第一个给定字符串相同字符串，并用指定的内容替换。第一个参数为要删除的字符串，第二个参数为要替代的字符串：

```js
const p = 'The quick brown fox jumps over the lazy dog. If the dog reacted, was it really lazy?';
console.log(p.replace('dog', 'monkey'));
// "The quick brown fox jumps over the lazy monkey. If the dog reacted, was it really lazy?"
```

ES 2021 提出的`replaceAll()`方法可以替代目标字符串中的所有子字符串。

##### toLowerCase 和 toUpperCase

分别用于大小写转换。

##### split 分隔子串

用一个给定的字符串作分隔标准，返回字符串分隔后的结果数组。第一个参数为分隔标准，第二个可选参数为最大分隔数量：

```js
const str = 'The quick brown fox jumps over the lazy dog.';

const words = str.split(' ');
console.log(words);
// Array ["The", "quick", "brown", "fox", "jumps", "over", "the", "lazy", "dog."]

const chars = str.split('');
console.log(chars);
// Array ["T", "h", "e", " ", "q", "u", "i", "c", "k", " ", "b", "r", "o", "w", "n", " ", "f", "o", "x", " ", "j", "u", "m", "p", "s", " ", "o", "v", "e", "r", " ", "t", "h", "e", " ", "l", "a", "z", "y", " ", "d", "o", "g", "."]

const strCopy = str.split();
console.log(strCopy);
// Array ["The quick brown fox jumps over the lazy dog."]
```

##### match 匹配正则

接收一个正则对象，返回正则匹配的内容数组：

```js
const paragraph = 'The quick brown fox jumps over the lazy dog. It barked.';
const regex = /[A-Z]/g;
const found = paragraph.match(regex);

console.log(found);
// expected output: Array ["T", "I"]
```

##### search 匹配正则

接收一个正则对象，返回第一个符合模式的字符下标，没有找到时返回-1：

```js
const paragraph = 'The quick brown fox jumps over the lazy dog. If the dog barked, was it really lazy?';

// 非英文字符和空格
const regex = /[^\w\s]/g;
console.log(paragraph.search(regex));
// 43
console.log(paragraph[paragraph.search(regex)]);
// "."
```

##### [[ iterator ]]

字符串原型上有一个内置方法 [[ iterator ]]，可以使用常用内置符号访问，并手动使用迭代器：

```js
let message = "abc";
let stringIterator = message[Symbol.iterator]();
console.log(stringIterator.next()); // {value: "a", done: false}
console.log(stringIterator.next()); // {value: "b", done: false}
console.log(stringIterator.next()); // {value: "c", done: false}
console.log(stringIterator.next()); // {value: undefined, done: true}
```

由于具有迭代器，字符串可以使用解构操作符进行解构：

```js
let message = "abcde";
console.log([...message]); // ["a", "b", "c", "d", "e"]
```

##### localeCompare 比较单词表顺序

用于在本地语言单词表中比较两个字符串中字符出现的顺序，出现位置小于给定字符串时返回-1，相同返回0，大于返回1：

```js
let stringValue = "yellow";
console.log(stringValue.localeCompare("brick")); 	// 1
console.log(stringValue.localeCompare("yellow")); 	// 0
console.log(stringValue.localeCompare("zoo")); 		// -1
```

### 标准内置对象

**标准内置对象**是任何由 ECMAScript 实现提供、**与宿主环境无关**，并在ECMAScript程序**开始执行时就存在**的对象。

#### Global

**Global** 对象是 ES 中最特殊的对象，代码不会显示访问这个对象（包括全局下的 this 指针）。事实上 ES 中并**不存在全局函数和全局变量**，在全局下定义的函数和变量都会**成为 Global 对象的属性**。

那些看起来像是全局函数或变量的函数与变量实际上是 Global 对象的成员，`isNaN()`、`isFinite()`、`parseInt()`和`parseFloat()`，实际上都是 Global 对象的方法。

##### URL 编码方法

全局对象的 URL 编码方法用于将字符串编码为浏览器能识别的**统一资源标识符（URI）**。

`encodeURI()`用于对整个字符串进行编码，转码所有**不属于 URL 组件中的非标准字符**，如冒号、斜杠、问号、井号，返回编码结果：

```js
let uri = "http://www.wrox.com/illegal value.js#start";

console.log(encodeURI(uri));	// "http://www.wrox.com/illegal%20value.js#start"，空格被替换
```

`encodeURIComponent()`会将所有非标准字符转码，包括 URL 中内容：

```js
console.log(encodeURIComponent(uri));	// "http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.js%23start"
```

一般使用`encodeURI()`来编码 URL 部分，而`encodeURIComponent()`则用来编码 URI 中剩余部分，如查询字符串。

对应的，`decodeURI()`和`decodeURIComponent()`用于解码：

```js
let uri = "http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.js%23start";
// http%3A%2F%2Fwww.wrox.com%2Fillegal value.js%23start
console.log(decodeURI(uri));
// http:// www.wrox.com/illegal value.js#start
console.log(decodeURIComponent(uri));
```

##### eval

`eval()`就是一个完整的 ECMAScript 解释器。该方法接收一个 ECMAScript 字符串作为参数并执行。eval 中执行的代码拥有和调用 eval
时所在代码块相同的上下文与作用域链，因此可以访问相同上下文中定义的变量和函数。

```js
let msg = "hello world";
eval("console.log(msg)"); 	// "hello world"
```

在 eval 内部定义的函数可以在外部被调用，而变量不行：

```js
eval("function sayHi() { console.log('hi'); }");
sayHi();			// hi
eval("let msg = 'hello world';");
console.log(msg); 	// Reference Error: msg is not defined
```

在严格模式下，外部将无法访问 eval 内部创建的函数和变量。

> 使用 eval 时应该注意页面安全，注意防范 XXS，以及谨慎使用用户输入内容作为 eval 参数。

##### window

在浏览器中，**window 对象**实际上是 Global 对象的代理，是对 ES 的 Global 对象的实现，可以通过 this 指针访问。

#### Math

全局对象属性 Math 用于进行数学相关操作。和其他内置原生方法一样，Math 中的方法由解释器实现，拥有比同样的 JavaScript 代码实现更高的效率。

##### 对象属性

+ `Math.E`：自然对数的基数e 的值
+ `Math.LN10`： 10为底的自然对数
+ `Math.LN2`： 2为底的自然对数
+ `Math.LOG2E`： 以2 为底e 的对数
+ `Math.LOG10E`： 以10 为底e 的对数
+ `Math.PI`： π 的值
+ `Math.SQRT1_2`： 1/2的平方根
+ `Math.SQRT2`： 2的平方根

##### min 和 max

`min()`和`max()`方法用于确定一组数值中的最小值和最大值，接收任意多个参数，返回结果：

```js
let max = Math.max(3, 54, 32, 16);
console.log(max); // 54
let min = Math.min(3, 54, 32, 16);
console.log(min); // 3
let values = [1, 2, 3, 4, 5, 6, 7, 8];
console.log(Math.max(...values));	// 8
```

##### ceil

用于向上取整。

```js
console.log(Math.ceil(25.9));   // 26
console.log(Math.ceil(25.5));   // 26
console.log(Math.ceil(-25.1));  // -25
```

##### floor

用于向下取整。

```js
console.log(Math.floor(25.9));  // 25
console.log(Math.floor(25.5));  // 25
console.log(Math.floor(-25.1)); // -25
```

##### round

用于四舍五入。

```js
console.log(Math.round(25.9));  // 26
console.log(Math.round(25.5));  // 26
console.log(Math.round(-25.1)); // -25
```

##### fround

返回最接近的单精度浮点值。

```js
console.log(Math.fround(0.4));  // 0.4000000059604645
console.log(Math.fround(0.5));  // 0.5
console.log(Math.fround(25.9)); // 25.899999618530273
```

##### random

返回 [0,1) 区间内的随机数。以下是常用的随机数获取函数：

```js
let selectFrom = (lowerValue, upperValue) => {
    return Math.floor(Math.random() * (upperValue - lowerValue + 1) + lowerValue);
}
```

##### 其他方法

+ `Math.abs(x)` ：返回 x 的绝对值
+ `Math.exp(x)`：返回 Math.E 的x 次幂
+ `Math.expm1(x)`： 等于 Math.exp(x) - 1
+ `Math.log(x)`： 返回x 的自然对数
+ `Math.log1p(x)`： 等于1 + Math.log(x)
+ `Math.pow(x, power)`： 返回x 的 power 次幂
+ `Math.hypot(...nums)` ：返回 nums 中每个数平方和的平方根
+ `Math.clz32(x)`： 返回32 位整数 x 的前置零的数量
+ `Math.sign(x)` ：返回表示 x 符号的1、0、-0 或-1
+ `Math.trunc(x)`： 返回 x 的整数部分，删除所有小数
+ `Math.sqrt(x)` ：返回 x 的平方根
+ `Math.cbrt(x)`： 返回 x 的立方根
+ `Math.acos(x)` ：返回 x 的反余弦
+ `Math.acosh(x)` ：返回 x 的反双曲余弦
+ `Math.asin(x)` ：返回 x 的反正弦
+ `Math.asinh(x)` ：返回 x 的反双曲正弦
+ `Math.atan(x)` ：返回 x 的反正切
+ `Math.atanh(x)` ：返回 x 的反双曲正切
+ `Math.atan2(y, x)`： 返回 y/x 的反正切
+ `Math.cos(x)` ：返回 x 的余弦
+ `Math.sin(x)` ：返回 x 的正弦
+ `Math.tan(x)` ：返回 x 的正切

#### 所有标准内置对象

以下是所有 ECMAScript 标准内置对象。

##### [值属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#值属性)

这些全局属性返回一个简单值，这些值没有自己的属性和方法。

+ [`Infinity`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Infinity)
+ [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN)
+ [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)
+ [`globalThis`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/globalThis)

##### [函数属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#函数属性)

全局函数可以直接调用，不需要在调用时指定所属对象，执行结束后会将结果直接返回给调用者。

+ [`eval()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/eval)
+ [`uneval()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/uneval)
+ [`isFinite()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/isFinite)
+ [`isNaN()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/isNaN)
+ [`parseFloat()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseFloat)
+ [`parseInt()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt)
+ [`decodeURI()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/decodeURI)
+ [`decodeURIComponent()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/decodeURIComponent)
+ [`encodeURI()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURI)
+ [`encodeURIComponent()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)
+ 已废弃
    + [`escape()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/escape)
    + [`unescape()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/unescape)

##### [基本对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#基本对象)

顾名思义，基本对象是定义或使用其他对象的基础。基本对象包括一般对象、函数对象和错误对象。

+ [`Object`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object)
+ [`Function`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function)
+ [`Boolean`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Boolean)
+ [`Symbol`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

###### 错误对象

错误对象是一种特殊的基本对象。它们拥有基本的 [`Error`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Error)
类型，同时也有多种具体的错误类型。

+ [`Error`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Error)
+ [`AggregateError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/AggregateError)
+ [`EvalError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/EvalError)
+ [`InternalError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/InternalError)
+ [`RangeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RangeError)
+ [`ReferenceError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError)
+ [`SyntaxError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/SyntaxError)
+ [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError)
+ [`URIError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/URIError)

##### [数字和日期对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#数字和日期对象)

用来表示数字、日期和执行数学计算的对象。

+ [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)
+ [`BigInt`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt)
+ [`Math`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math)
+ [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)

##### [字符串](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#字符串)

用来表示和操作字符串的对象。

+ [`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String)
+ [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp)

##### [可索引的集合对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#可索引的集合对象)

这些对象表示按照索引值来排序的数据集合，包括数组和类型数组，以及类数组结构的对象。

+ [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)
+ [`Int8Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int8Array)
+ [`Uint8Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
+ [`Uint8ClampedArray`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8ClampedArray)
+ [`Int16Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int16Array)
+ [`Uint16Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint16Array)
+ [`Int32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int32Array)
+ [`Uint32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint32Array)
+ [`Float32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Float32Array)
+ [`Float64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Float64Array)
+ [`BigInt64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt64Array)
+ [`BigUint64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigUint64Array)

##### [使用键的集合对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#使用键的集合对象)

这些集合对象在存储数据时会使用到键，包括可迭代的[`Map`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map)
和 [`Set`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)，支持按照插入顺序来迭代元素。

+ [`Map`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map)
+ [`Set`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)
+ [`WeakMap`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)
+ [`WeakSet`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)

##### [结构化数据](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#结构化数据)

这些对象用来表示和操作结构化的缓冲区数据，或使用 JSON （JavaScript Object Notation）编码的数据。

+ [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
+ [`SharedArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer)
+ [`Atomics`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Atomics)
+ [`DataView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/DataView)
+ [`JSON`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON)

##### [控制抽象对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#控制抽象对象)

控件抽象可以帮助构造代码，尤其是异步代码（例如，不使用深度嵌套的回调）。

+ [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
+ [`Generator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Generator)
+ [`GeneratorFunction`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/GeneratorFunction)
+ [`AsyncFunction`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction)

##### [反射](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#反射)

+ [`Reflect`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect)
+ [`Proxy`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

##### [国际化](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#国际化)

ECMAScript核心的附加功能，用于支持多语言处理。

+ [`Intl`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl)
+ [`Intl.Collator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/Collator)
+ [`Intl.DateTimeFormat`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat)
+ [`Intl.ListFormat`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/ListFormat)
+ [`Intl.NumberFormat`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat)
+ [`Intl.PluralRules`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/PluralRules)
+ [`Intl.RelativeTimeFormat`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/RelativeTimeFormat)
+ [`Intl.Locale`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/Locale)

##### [WebAssembly](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#webassembly)

+ [`WebAssembly`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly)
+ [`WebAssembly.Module`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Module)
+ [`WebAssembly.Instance`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Instance)
+ [`WebAssembly.Memory`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Memory)
+ [`WebAssembly.Table`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Table)
+ [`WebAssembly.CompileError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/CompileError)
+ [`WebAssembly.LinkError` (en-US)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/LinkError)
+ [`WebAssembly.RuntimeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/RuntimeError)

##### [其他](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#其他)

+ [`arguments`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/arguments)

> ECMAScript 的引用类型和传统面向对象语言中的类类似，但实现不同
>
> Date 是时间类型
>
> RegExp 是正则表达式接口
>
> 函数也是对象，即 Function 的实例
>
> 原始值在访问时会生成一个临时的原始值包装类型对象，因此可以在原始值上调用方法，语句结束即销毁包装对象

---

## 06 集合引用类型

### Object

Object 是 ECMAScript 中所有引用类型的基类，其实例没有多少功能，但是适合存储数据。

#### 创建实例

Object 实例有两种创建方式，一种是使用 new 关键字，另一种是使用**对象字面量（object literal）**：

```js
let man = {	// 表达式上下文开始
    name: "Steven",
    age: 20
};	// 表达式上下文结束
```

对象字面量从`{`开始到`}`结束，其中内容称为**表达式上下文（expression context）**，表达式上下文期待返回值，在对象字面量中返回值即是一个对象。类似的还有**语句上下文（statement context）**
，内容是一段要执行的语句块。

**在字面量中声明的 Number 类型的键会被自动转换为 String 类型**：

```js
let obj = {
    name: 'Steven',
    age: 20,
    1: true         // Number类型的键会自动转为字符串类型
};

console.log(obj);   // { '1': true, name: 'Steven', age: 20 }
```

使用字面量定义对象的好处是并**不会调用 Object 的构造函数**，可以提高程序效率。

#### 访问属性

ES 中访问对象属性有两种方式，一种是和其他面向对象语言一样的**点语法**，`.`后的字面量实际是 String 类型的键：

```js
console.log(obj.name);
```

另一种是使用中括号，实际上是通过键的值来访问属性，键值可以是 String、Number 或 Symbol：

```js
console.log(obj['name']);
console.log(arr[1]);
console.log(arr[Symbol.iterator]);
```

### Array

ES 中的 Array 引用类型是一组有序的数据，和其他强类型语言不同，每个槽位**可以存放任意类型的值**。Array 的长度时动态的，可以随着数据添加自动增长。数组最多可以包含 4 294 967 295 个元素。

#### 创建数组 of

数组可以通过构造函数和字面量创建，在使用构造函数创建时，可以接收一个 Number 类型参数，代表初始化数组大小，关键字 new 可以省略：

```js
let arr = new Array(10);
console.log(arr);       // [ <10 empty items> ]
```

也可以接收多个任意类型值，作为数组的元素：

```js
let arr = new Array(true, '2', 3);
console.log(arr);       // [ true, '2', 3 ]
```

但是有一个问题，如果要初始化一个只有一个 Number 类型元素的数组时，会出现歧义，此时可以使用 `of()`方法，接收一组参数，并将参数作为返回数组的元素：

```js
arr = Array(3);
console.log(arr);           // [ <3 empty items> ]

arr = Array.of(3);
console.log(arr);           // [ 3 ]
```

#### 转化类数组结构 from

**类数组结构**是类似于数组的数据结构类型，即任何**可迭代结构**，或是一个**拥有 length 属性并可索引**的结构，如 String、Map、Set 以及常用的函数内标准对象 arguments：

```js
console.log(Array.from('string'));      // [ 's', 't', 'r', 'i', 'n', 'g' ]

console.log(Array.from(new Map().set('1', 1).set('2', 2)));
// [ [ '1', 1 ], [ '2', 2 ] ]

console.log(Array.from({
    * [Symbol.iterator]() {
        yield 1;
        yield 2;
        yield 3;
        yield 4;
    }
}));        // [ 1, 2, 3, 4 ]

function getArgsArray() {
    console.log(arguments);            // [Arguments] { '0': 1, '1': 2, '2': 3, '3': 4 }
    return Array.from(arguments);
}

console.log(getArgsArray(1, 2, 3, 4)); // [1, 2, 3, 4]
```

`Array.from()`实际上是一种**浅拷贝**，返回的是一个新数组对象，但是内部元素是浅拷贝：

```js
let a = [[1], [2], [3]];
let b = Array.from(a);
console.log(a === b);
a[0][0] = 10;
console.log(b);			// [[10], [2], [3]]
```

`Array.from()`实际上可以接收三个参数，第二个参数为一个回调，接收类数组元素值作为参数，第三个参数用于提供回调中的 this 指向：

```js
console.log(Array.from([1, 2, 3, 4], v => v **= 2));
// [ 1, 4, 9, 16 ]
console.log(Array.from([1, 2, 3, 4], function (v) {
    return v **= this.exponent;
}, {exponent: 2}));
// [ 1, 4, 9, 16 ]
```

#### 数组空位

使用字面量创建数组时，可以留下空位，其中的值为 undefined：

```js
console.log([, , , , ,]);   // [ <5 empty items> ]
console.log([1, , , 4]);    // [ 1, <2 empty items>, 4 ]
console.log([1, , 4][1]);   // undefined
```

#### 数组索引

数组对象的可迭代索引是类型为 Number 的键，通过`[]`访问，和对象属性一样可以动态添加：

```js
let arr = [1, 2, 3];
console.log(arr[0]);        // 1
arr[5] = 10;
console.log(arr);           // [ 1, 2, 3, <2 empty items>, 10 ]
```

#### 数组长度 length

数组元素的长度保存在`length`属性中，长度包括空位：

```js
console.log([1, , , 4].length);   // 4
```

`length`**不是只读的**，更改其值会改变元素个数，应该避免误操作该属性：

```js
let arr = [1, 2, 3, 4, 5];
arr.length = 2;
console.log(arr);                 // [1, 2]
```

#### 检测数组 isArray

通常情况下，一个全局上下文中只有一个 Array 构造函数，因此要检测一个对象是否为数值类型，只需使用`instanceof`关键字。在一些场景下，一个网页中可能有多个全局上下文（如包含多个框架），如果**跨全局上下文传递数组对象**
，可能导致传递的数组对象在另一全局上下文中被视作非 Array 类型。`Array.isArray()`方法可以避免这个问题：

```js
console.log(Array.isArray([]));    // true
```

#### 迭代器方法

`keys()`用于返回数组对象的索引迭代器：

```js
let arr = ["foo", "bar", "baz", "qux"];
console.log(arr.keys());                // Object [Array Iterator] {}
console.log(Array.from(arr.keys()));    // [ 0, 1, 2, 3 ]
```

`values()`返回元素迭代器：

```js
console.log(Array.from(arr.values()));  // [ 'foo', 'bar', 'baz', 'qux' ]
```

`entires()`返回索引和值的数组迭代器：

```js
console.log(Array.from(arr.entries())); 	// [ [ 0, 'foo' ], [ 1, 'bar' ], [ 2, 'baz' ], [ 3, 'qux' ] ]
for (const [index, value] of arr.entries()) {
    console.log(index);         			// 0,1,2,3
    console.log(value);         			// foo,bar,baz,qux
}
```

#### 填充方法 fill

`fill()`方法用于使用指定值填充数组的一部分，不会改变数组原长度。第一个参数为用于填充的值，第二个可选参数为开始位置，默认0，第三个可选参数为结束位置（不填充该位置），默认为数组长度-1。参数为负数时从尾部计算位置。

```js
arr = [1, 2, 3, 4, 5];
console.log(arr.fill(6));                       // [ 6, 6, 6, 6, 6 ]
console.log(arr.fill(7, 1));            		// [ 6, 7, 7, 7, 7 ]
console.log(arr.fill(8, 2, 4));     			// [ 6, 7, 8, 8, 7 ]
```

#### 内部复制 copyWithin

`copyWithin()`方法用于在复制数组内部的一段元素并插入到内部指定位置，内部元素是**浅拷贝**：

```js
arr = [[1], 2, 3, 4, 5, 6, 7, 8, 9];
console.log(arr.copyWithin(3));                 		// [[1], 2, 3, [1], 2, 3, 4, 5, 6]
arr[0][0] = 10;
console.log(arr[0] === arr[3]);						  // true
console.log(arr);                                       // [[ 10 ], 2, 3, [ 10 ], 2, 3, 4, 5, 6]
```

只有一个参数时，从0开始复制，到参数指定位置（不包括该位置元素）结束，插入到该参数指定位置：

```js
let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
console.log(arr.copyWithin(3));                 // [1, 2, 3, 1, 2, 3, 4, 5, 6]
```

两个参数时，第一个参数为插入位置，第二个为开始复制的位置，一直复制到底：

```js
console.log(arr.copyWithin(0, 5));         // [6, 7, 8, 9, 5, 6, 7, 8, 9]
```

三个参数时，第一个参数为插入位置，第二个参数为复制开始位置，第三个参数为复制结束位置（不包括该位置）：

```js
console.log(arr.copyWithin(4, 0, 3)); 	// [1, 2, 3, 4, 1, 2, 3, 8, 9]
```

#### 转换方法

转换方法继承自 Object，`valueOf()`返回数组本身，`toString()`返回一个字符串，该字符串由每个元素的等效字符串（toString 返回值）和逗号连接，`toLocalString()`
返回本地化结果。如果某项元素的转换值为 null 或 undefined，则以空字符串表示。

#### 栈方法

`pop()`和`push()`用于像栈一样操作数组元素。`push()`接收任意个数参数，按照顺序压入栈（从数组尾），返回压入个数。**数组尾相当于栈顶**，`pop()`返回并删除数组尾元素。

#### 队列方法

`shift()`用于出队操作，入队使用`push()`。**数组头相当于队头**，`shift()`返回数组第一个元素。`unshift()`执行和`shift()`相反操作，从队头依次压入元素：

```js
arr = [1];
arr.push(2, 3);
console.log(arr);               // [ 1, 2, 3 ]
arr = [1];
arr.unshift(2, 3);
console.log(arr);               // [ 2, 3, 1 ]
arr.shift();
console.log(arr);               // [ 3, 1 ]
```

#### 反向排列 reverse

`reverse()`方法用于倒转数组元素，返回数组本身。该方法会改变数组内容。

#### 排序 sort

`sort()`方法默认按升序重新排列数组，比较规则是调用转型函数 `String()`。可以传入一个比较函数作回调，回调接收两个参数代表比较双方，如果第一个参数应该排在第二个参数前面，就返回**负值**；如果两个参数相等，就**返回0**
；如果第一个参数应该排在第二个参数后面，就返回正值：

```js
// 升序
(value1, value2) => {
    if (value1 < value2) {
        return -1;
    } else if (value1 > value2) {
        return 1;
    } else {
        return 0;
    }
}

// 等价于该表达式
(a, b) => a < b ? 1 : a > b ? -1 : 0;

// 数值类型可以更加简化
(a, b) => a - b;
```

该方法返回排序后的数组自身。

#### 数组连接 concat

`concat()`用于连接数组并返回连接结果，不改变数组自身。参数是数组是会被打平，每个元素连接到目标数组上，非数组类型参数直接连接：

```js
arr = [1, 2, 3];
console.log(arr.concat([4, 5, 6], [7, 8]));     // [ 1, 2, 3, 4, 5, 6, 7, 8 ]
console.log(arr);                               // [ 1, 2, 3 ]
```

打平数组的行为在创建数组对象时可以改写，该属性用布尔值控制，使用内置符号访问：

```js
let colors = ["red", "green", "blue"];
let newColors = ["black", "brown"];
let moreNewColors = {
    [Symbol.isConcatSpreadable]: true,
    length: 2,
    0: "pink",
    1: "cyan"
};
newColors[Symbol.isConcatSpreadable] = false;
// 强制不打平数组
let colors2 = colors.concat("yellow", newColors);
// 强制打平类数组对象
let colors3 = colors.concat(moreNewColors);
console.log(colors); // ["red", "green", "blue"]
console.log(colors2); // ["red", "green", "blue", "yellow", ["black", "brown"]]
console.log(colors3); // ["red", "green", "blue", "pink", "cyan"]
```

#### 截取部分 slice

`slice()`方法返回截取的部分数组，不修改元素组，第一个参数为开始截取位置，第二个为结束位置（不包括该元素），默认数组长度：

```js
console.log([1, 2, 3, 4].slice());              // [ 1, 2, 3, 4 ]
console.log([1, 2, 3, 4].slice(1));             // [ 1, 2, 3 ]
console.log([1, 2, 3, 4].slice(1, 2));           // [ 2 ]
```

#### 插入、替换和删除 splice

`splice()`方法本身用于插入数组，也可用于替换与删除数组。接收三个或更多参数：开始插入位置、要删除的元素个数、插入内容（默认为空），插入内容不会被打平。该方法会修改原数组，返回从数组中删除的元素数组。

插入内容：

```js
arr = [1, 2, 3];
console.log(arr.splice(0, 2, 1, 2, 4, 5, [6, 7]));       // [ 1, 2 ]
console.log(arr);                   // [ 1, 2, 4, 5, [ 6, 7 ], 3 ]
```

替换内容，控制删除个数和插入个数一致即可：

```js
arr = [1, 2, 3];
arr.splice(0, 2, 9, 10);
console.log(arr);                   // [ 9, 10, 3 ]
```

删除内容，将第三个参数闲置即可：

```js
arr = [1, 2, 3];
arr.splice(1, 1);
console.log(arr);                   // [ 1, 3 ]
```

#### 严格相等搜索 indexOf includes

`includes()`、`indexOf()`和`lastIndexOf()`用于在数组内搜索指定元素，使用全等`===`比较。接收两个参数：需要查找的元素，可选的起始位置。`indexOf()`
从头开始查找，`lastIndexOf()`从末尾开始查找，如果存在元素则返回下标，不存在返回-1。`includes()`返回布尔值：

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
alert(numbers.indexOf(4)); // 3
alert(numbers.lastIndexOf(4)); // 5
alert(numbers.includes(4)); // true
alert(numbers.indexOf(4, 4)); // 5
alert(numbers.lastIndexOf(4, 4)); // 3
alert(numbers.includes(4, 7)); // false
```

#### 断言函数搜索 find

`find()`方法使用一个断言函数进行搜索，该函数需要返回一个布尔值。回调接收三个参数：元素、下标、数组本身，当返回值为 true 时中断搜索。`find()`在找到目标时返回目标值，没有找到是返回
undefined。可以接收第二个参数，指定回调内部的 this。`findIndex()`方法返回下标，没有找到返回-1：

```js
console.log([1, 2, 3].find(value => value === 3));          // 3
console.log([1, 2, 3].find(value => value === 4));          // undefined
console.log([1, 2, 3].findIndex(value => value === 3));     // 2
console.log([1, 2, 3].findIndex(value => value === 4));     // -1
```

#### 迭代方法 every filter forEach map some

数组有五个迭代方法，这些方法接收一个回调，回调接收三个参数：元素、下标、数组本身。

+ `every()`：与连接，对数组每一项都运行传入的函数，如果对每一项函数都返回 true，则这个方法返回 true。
+ `filter()`：过滤元素，对数组每一项都运行传入的函数，函数返回 true 的项会组成数组之后返回。
+ `forEach()`：遍历并执行，对数组每一项都运行传入的函数，没有返回值。
+ `map()`：遍历支线并保存结果，对数组每一项都运行传入的函数，返回由每次函数调用的结果构成的数组。
+ `some()`：或连接，对数组每一项都运行传入的函数，如果有一项函数返回 true，则这个方法返回 true。

#### 归并方法 reduce

`reduce()`执行归并处理，参数为一个回调和初始归并值。回调接收四个参数：上一归并值、元素、下标、数组本身，并返回下一次调用时作为上一归并值的结果。如果没有指定初始归并值，则从数组第二个元素开始迭代.可以用累加来解释归并行为：

```js
let values = [1, 2, 3, 4, 5];
let sum = values.reduce((prev, cur, index, array) => prev + cur);
alert(sum); // 15
```

`reduceRight()`则是从尾部开始归并。

---

## 20 JavaScript API

### Web Components

**Web Components** 即 **Web 组件**，是一套用于增强 DOM 行为的工具，包括 **Shadow DOM（影子 DOM）**、**Custom Element（自定义元素）**和 **HTML
Template（HTML 模板）**。

Web 组件在各浏览器中的具体实现并不相同，且目前在页面组件数较多时 Shadow DOM 的执行效率并没有一些框架使用的虚拟 DOM效率高。但由于是原生内容，开发者可以跨非原生框架使用这些可复用内容。

#### HTML Template

在 Web 组件之前，浏览器没有基于 HTML 解析构建子 DOM 的功能。直接使用`innerHTML`存在巨大安全隐患（恶意插入代码、信息丢失等），`document.createElement()`的使用过于复杂，且与标签无关。

**HTML 模板**的核心思想是，提前在 HTML 写好特殊标记，让浏览器将其作为子 DOM 解析，但不进行渲染，在需要使用的时候将模板内容**转移**到 DOM 上。使用`<template>`标签创建一个模板：

```html

<template id="foo">
    <p>I'm inside a template!</p>
</template>
```

##### Document Fragment

模板内的内容并不会被渲染，DOM 查询方法也无法获取其中节点。模板的子节点被包含在其`DocumentFragment`节点内，可见于浏览器开发者工具：

```html

<template id="foo">
    #document-fragment
    <p>I'm inside a template!</p>
</template>
```

通过 JavaScript DOM API 获取节点引用后，可以从节点的`content`属性中获取`DocumentFragment`的引用：

```js
console.log(document.getElementById('temp').content);   // document-fragment
```

利用`DocumentFragment`可以向 DOM 中一次添加多个平级节点。如果多次调用`appendChild`会导致**每次添加都重新排列一次 DOM**，`DocumentFragment`由于本身不是一个 DOM
节点对象，不会影响节点优化。在将`DocumentFragment`对象挂载到 DOM 上后，该对象的子节点会直接转移到 DOM 中，`DocumentFragment`成为一个**没有子节点的空节点**：

```js
    const frag = new DocumentFragment();

frag.appendChild(document.createElement('p'));
frag.appendChild(document.createElement('p'));
frag.appendChild(document.createElement('p'));

console.log(frag.children.length);          // 3

// 只进行一次DOM插入，刷新一次DOM，插入三个平级节点
document.querySelector('body').appendChild(frag);

console.log(frag.children.length);          // 0
```

##### \<template>

`<template>`与`DocumentFragment`一样，将其挂载到 DOM 中后其子节点会**直接转移**：

```js
const fooElement = document.querySelector('#foo');
const barTemplate = document.querySelector('#bar');
const barFragment = barTemplate.content;
console.log(document.body.innerHTML);
// <div id="foo">
// </div>
// <template id="bar">
// <p></p>
// <p></p>
// <p></p>
// </template>
fooElement.appendChild(barFragment);
console.log(document.body.innerHTML);
// <div id="foo">
// <p></p>
// <p></p>
// <p></p>
// </div>
// <tempate id="bar"></template>
```

使用`document.importNode`方法可以**克隆**一个已经在 DOM 中的`DocumentFragment`对象，返回一个新的对象而不是引用，因此原模板中的内容不会消失。也可以使用`DocumentFragment`
的`cloneNode()`方法进行克隆，该方法接收一个布尔值来决定深度还是浅度克隆：

```js
const fooElement = document.querySelector('#foo');
const barTemplate = document.querySelector('#bar');
const barFragment = barTemplate.content;
console.log(document.body.innerHTML);
// <div id="foo">
// </div>
// <template id="bar">
// <p></p>
// <p></p>
// <p></p>
// </template>
fooElement.appendChild(document.importNode(barFragment, true));
console.log(document.body.innerHTML);
// <div id="foo">
// <p></p>
// <p></p>
// <p></p>
// </div>
// <template id="bar">
// <p></p>
// <p></p>
// <p></p>
// </template>
```

##### 模板中的 script

模板中的`<script>`标签内容并不会执行，直到被添加到 DOM 中。可以利用这个特性来初始化内容：

```js
// 页面HTML：
//
// <div id="foo"></div>
// <template id="bar">
// <script>console.log('Template script executed');</script>
// </template>
const fooElement = document.querySelector('#foo');
const barTemplate = document.querySelector('#bar');
const barFragment = barTemplate.content;
console.log('About to add template');
fooElement.appendChild(barFragment);
console.log('Added template');
// About to add template
// Template script executed
// Added template
```

#### Shadow DOM

**Shadow DOM（影子 DOM）**可以将一个完整的 DOM 树添加到父 DOM 中，并且可以实现封装，因此 **CSS 样式可以限制在 shadow DOM 中**而不是整个父 DOM 中。

为了安全和节点间的兼容，**并非所有**类型的 HTML 节点都可以包含 shadow DOM，如`<input>`，尝试向这些节点添加 shadow DOM 会抛出错误。

##### 创建影子 DOM 和可访问性

一个 shadow DOM 通过 HTML 对象的`attachShadow()`方法创建，容纳 shadow DOM 的节点称为 **shadow host（影子宿主）**，shadow DOM 的根节点称为 **shadow
root（影子根）**。

`attachShadow()`接收一个`shadowRootInit`对象作参数，返回一个 shadow DOM 实例。`shadowRootInit`包含一个`mode`属性，指定为`'open'`时，可以通过 HTML
元素的`shadowRoot`属性获取影子 DOM 的引用，为`'closed'`则不可获得。

```js
document.body.innerHTML = `
    <div id="foo"></div>
    <div id="bar"></div>
`;
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');
const openShadowDOM = foo.attachShadow({mode: 'open'});
const closedShadowDOM = bar.attachShadow({mode: 'closed'});
console.log(openShadowDOM); // #shadow-root (open)
console.log(closedShadowDOM); // #shadow-root (closed)
console.log(foo.shadowRoot); // #shadow-root (open)
console.log(bar.shadowRoot); // null
```

即使可以隐藏影子 DOM 内容，恶意代码扔有许多方式可以获取其内容，因此封闭 shadow DOM **并不能实现内容保密**，并且其 HTML 在调试工具中是可视的。使用`<iframe>`来跨域引用资源会更加可靠。

##### 使用影子 DOM

影子 DOM 实例的使用和其他 HTML 节点元素相同：

```js
for (let color of ['red', 'green', 'blue']) {
    const div = document.createElement('div');
    const shadowDOM = div.attachShadow({mode: 'open'});
    document.body.appendChild(div);
// 为shadow dom添加内容，css仅在影子DOM内部有效
    shadowDOM.innerHTML = `
    <p>Make me ${color}</p>
    <style>
    	p {
    		color: ${color};
    	}
    </style>
`;
}
```

##### slot 插槽

在默认情况下，如果为一个影子宿主创建影子 DOM，其原先内部元素会被**转移**到影子 DOM 中，但是原先的内部节点会被**隐藏**：

```js
// 1秒后p标签内容会被隐藏
document.body.innerHTML = `
    <div>
    <p>Foo</p>
    </div>
`;
setTimeout(() => document.querySelector('div').attachShadow({mode: 'open'}), 1000);    
```

影子 DOM 的优先级高于节点原来的内容，浏览器会优先渲染影子 DOM ，因为添加的影子 DOM 是空的，所以不会显示任何内容。

如果需要让一些不属于影子 DOM 中的元素在影子 DOM 中渲染，需要使用`<slot>`，原先位于节点中的元素会被**投射（projection）**到影子 DOM 中，但实际上**还是位于外部影子宿主中**
。从浏览器控制台中可以看到这种映射关系：

```js
document.querySelector('div')
    .attachShadow({mode: 'open'})
    .innerHTML = `
    <div id="bar">
        <slot></slot>
    <div>
`;
console.log(document.querySelector('p').parentElement);	// <div id="foo"></div>
```

如果只有一个插槽，所有原来的元素都会被添加到该插槽中。

##### 命名插槽

**命名插槽（named slot）**用于实现多个投射，通过外部节点的 **slot** 属性和影子 DOM 的 **name** 属性对关联，内容渲染顺序和插槽位置相关：

```js
document.body.innerHTML = `
    <div>
    <p slot="foo">Foo</p>
    <p slot="bar">Bar</p>
    </div>
`;
document.querySelector('div')
    .attachShadow({mode: 'open'})
    .innerHTML = `
        <slot name="bar"></slot>
        <slot name="foo"></slot>
`;
// Renders:
// Bar
// Foo
```

##### 事件重定向

**事件重定向（event retarget）**指在影子 DOM 发生的事件会逃出影子 DOM 内部，该事件的`target`属性会**指向外部节点**。事件重定向只会发生在影子 DOM 内部元素上，插槽中从外部投射进来的元素并**不会**进行事件重定向：

```js
    // <div id="host">
    //     <button>outer</button>
    // </div>
const host = document.getElementById('host');
const shadowRoot = host.attachShadow({mode: 'open'});
shadowRoot.innerHTML = `
        <button>click</button>
        <slot></slot>
    `;

shadowRoot.querySelector('button').addEventListener('click', ev => console.log(ev.target));
host.addEventListener('click', (ev) => console.log(ev.target));
host.querySelector('button').addEventListener('click', ev => console.log(ev.target));

// 点击outer，两次打印均为<button>outer</button>，但第一次来源为button，第二次来源为div
// 点击click，第一次打印为<button>click</button>，来源于button
// 第二次打印为<div id="host">...</div>，事件被重定向到了外部，来源于div
```

#### Custom Element

浏览器会尝试将无法识别的标签整合进 DOM，以行内元素的方式显示其内容，这些无法识别的元素**和原生 HTML 元素是同一类型**，具有相同的属性：

```js
document.body.innerHTML = `
	<x-foo >I'm inside a nonsense element.</x-foo >
`;
console.log(document.querySelector('x-foo') instanceof HTMLElement); // true
```

通过使用 **Custom Element（自定义元素）**，可以定义一非 HTML 标准的标签，并为其添加更复杂的行为。

##### 创建自定义元素

自定义元素通过调用全局对象下的 **customElements** 属性（CustomElements 类型的对象）的`define`方法创建，该方法接收一个字符串和一个派生自`HTMLElement`
构造函数（类）的对象（也可以不派生，会失去原生元素的属性）。字符串作为自定义元素名使用，要求必须**包含至少一个不在开头或结尾的连字符**，否则会抛出错误：

```js
class FooElement extends HTMLElement {
}

customElements.define('x-foo', FooElement);
document.body.innerHTML = `
	<x-foo>I'm inside a nonsense element.</x-foo>
`;
console.log(document.querySelector('x-foo') instanceof FooElement); // true
```

通过调用自定义元素的**构造函数**来控制该类在每个 DOM 实例中的行为，该构造函数会覆盖基类构造函数，因此**必须调用基类的构造函数**：

```js
class FooElement extends HTMLElement {
    constructor() {
        super();
        console.log('x-foo')
    }
}

customElements.define('x-foo', FooElement);
document.body.innerHTML = `
    <x-foo></x-foo>
    <x-foo></x-foo>
    <x-foo></x-foo>
`;
// x-foo
// x-foo
// x-foo
```

##### 向自定义元素添加 Web 组件

要向自定义组件中添加 Web 组件，需要使用影子 DOM，如果尝试直接添加子节点将会抛出错误：

```js
class CustomElement extends HTMLElement {
    constructor() {
        super();
        console.log('create custom element');
        // this.innerHTML = '<p>custom</p>'
        this.attachShadow({mode: 'open'});
        this.shadowRoot.innerHTML = `
            #shadow-root
            <p>This is a custom element</p>
            <slot></slot>
        `;
    }
}

customElements.define('custom-el', CustomElement);
```

让视图部分与 JavaScript 代码分离会更加清晰：

```html
    <!-- 模板 -->
<template id="custom-element">
    #shadow-root
    <p>This is a custom element.</p>
    <slot></slot>
    <style>

    </style>
</template>

<!-- 创建自定义元素 -->
<script>
    class CustomElement extends HTMLElement {
        template = document.getElementById('custom-element') ?? document.createElement('template');

        constructor() {
            super();
            this.attachShadow({mode: 'open'});
            this.shadowRoot.appendChild(this.template.content.cloneNode(true));
        }
    }

    customElements.define('custom-el', CustomElement);
</script>

<custom-el>
    some text here.
</custom-el>
```

`<template>`标签可以位于`<head>`中，这样将不影响`<body>`内部的节点优化。

单 js 文件，在 HTML 中使用`<script type="module" src="xxx.js"></script>`引入：

```js
const template = `
    #shadow-root
    <p>This is a custom element.</p>
    <slot></slot>
`;

const style = `
    <style>

    </style>
`;

class CustomElement extends HTMLElement {
    constructor() {
        super();
        this.attachShadow({mode: 'open'});
        this.shadowRoot.innerHTML = template + style;
    }
}

customElements.define('custom-el', CustomElement);

export default CustomElement;
```

##### 组件生命周期

自定义元素组件有5个**生命周期方法**：

+ `constructor()`：在创建元素实例或将已有DOM 元素升级为自定义元素时调用。
+ `connectedCallback()`：在每次将这个自定义元素实例添加到DOM 中时调用。
+ `disconnectedCallback()`：在每次将这个自定义元素实例从DOM中移除时调用。
+ `attributeChangedCallback(name, oldValue, newValue)`：在每次**可观察属性**的值发生变化时调用。在元素实例初始化时，初始值的定义也算一次变化。
+ `adoptedCallback()`：在通过document.adoptNode()将这个自定义元素实例移动到新文档对象时调用。

```js
class FooElement extends HTMLElement {
    constructor() {
        super();
        console.log('ctor');
    }

    connectedCallback() {
        console.log('connected');
    }

    disconnectedCallback() {
        console.log('disconnected');
    }
}

customElements.define('x-foo', FooElement);
const fooElement = document.createElement('x-foo');
// ctor
document.body.appendChild(fooElement);
// connected
document.body.removeChild(fooElement);
```

如果需要在元素属性变化后，触发 `attributeChangedCallback()`回调函数，必须监听该属性。这可以通过定义`observedAttributes()` get 函数来实现，`observedAttributes()`
函数体内包含一个 return语句，返回一个数组，包含了需要监听的属性名称：

```js
class CustomElement extends HTMLElement {
    static get observedAttributes() {
        return ['foo'];
    }
}
```

##### 反射自定义元素属性

自定义元素应该是**响应式**的，对 DOM 的修改应该反映到 JavaScript 对象上。要进行 **JavaScript 至 DOM 间的反射**，常见的方法是使用 getter 和 setter；**DOM 至
JavaScript** 间的反射则需要使用`observedAttributes()`添加**可观察属性**，可观察属性的值变化时会调用`attributeChangedCallback()`：

```js
class Custom extends HTMLElement {
    template = document.getElementById('template') ?? document.createElement('template');

    constructor() {
        super();
        this.attachShadow({mode: 'open'});
        this.shadowRoot.appendChild(this.template.content.cloneNode(true));

        // 构造时自定义元素不能被初始化，会抛出错误
        this.foo;
    }

    // 设置观察属性
    static get observedAttributes() {
        return ['foo'];
    }

    get foo() {
        return this.getAttribute('foo');
    }

    set foo(value) {
        this.setAttribute('foo', value);
    }

    // 挂载时初始化
    connectedCallback() {
        this.foo = true;
    }

    // 观察属性变化时的回调
    attributeChangedCallback(name, oldValue, newValue) {
        if (oldValue === newValue) return;
        console.log(`${oldValue} -> ${newValue}`);
    }
}

customElements.define('custom-el', Custom);
```

##### 升级自定义元素

并非总是要先定义自定义元素，再在 DOM 中使用它们。`CustomElementRegistry`类型暴露了一些方法来检测是否存在对应自定义元素，并进行相关操作。已经连接到 DOM 上的元素会在自定义元素被定义时**自动升级**
，而没有连接的元素对象需要手动强制升级。

+ `CustomElementRegistry.get()`方法接收一个字符串，如果该元素已经定义，则返回元素对象引用，否则返回空。
+ `CustomElementRegistry.whenDefined()`接收一个字符串，返回一个期约，当对应自定义元素创建时期约将履行。
+ `CustomElementRegistry.upgrade()`接收一个 HTMLElement 对象，尝试强制升级该自定义元素。

```js
const cus = document.getElementById('cus');             // 已连接在DOM中的元素，在有自定义元素时会自动升级
const unmounted = document.createElement('custom-el');  // 未连接的元素，不会自动升级
class Custom {
}

customElements.whenDefined('custom-el').then(() => console.log('custom-el defined'));

console.log('Is cus a instance of Custom before define?', cus instanceof Custom); // false
console.log('Is unmounted a instance of Custom before define?', unmounted instanceof Custom); // false

console.log('Is there a custom element called custom-el?', Boolean(customElements.get('custom-el'))); // false

// 定义自定义元素
customElements.define('custom-el', Custom);
console.log('Is there a custom element called custom-el?', Boolean(customElements.get('custom-el'))); // true

console.log('Is cus a instance of Custom after define?', cus instanceof Custom); // true
console.log('Is unmounted a instance of Custom after define?', unmounted instanceof Custom); // false

customElements.upgrade(unmounted); // 强制升级
console.log('Is unmounted a instance of Custom after upgrade?', unmounted instanceof Custom); // true

// 期约 onResolved 异步打印 'custom-el defined'
```
