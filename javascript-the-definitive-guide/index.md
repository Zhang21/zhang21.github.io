# JavaScript权威指南


参考:

- [豆瓣](https://book.douban.com/subject/35396470/)

<br/>

---

<!--more-->

<br/>

# 概述

JavaScript是web的编程语言。绝大多数网站都使用JavaScript，所有现代浏览器都包含JavaScript解释器。Node.js使得JavaScript编程可以在web浏览器之外进行。

JavaScript是一种高级的、动态的、解释型的编程语言，它非常适合面向对象和函数式编程风格。JavaScript的变量是无类型的。它的语法不严格地讲基于Java，但是这两种语言在其他方面是无关的。

JavaScript这个名称很容易引起误解。除了表面上的语法相似之外，JavaScript与Java编程语言完全不同。

JavaScript由网景公司创建，从技术上讲，JavaScript是Sun Micorosystems(现在的Oracle)授权的商标，用来描述Netscape公司(现在的Mozilla公司)对该语言的实现。网景公司将这种语言提交给欧洲计算机制造商协会(ECMA)进行标准化，但由于商标问题，这种语言的标准化版本只能使用一个尴尬的名字"ECMAScript"。实际上，每个人都称这种语言为JavaScript。

本书使用 `ECMAScript` 和 `ES` 来表示该语言标准和该标准的版本。

对于2010年代的大多数版本，所有web浏览器都支持ECMAScript标准的第5版。本书将ES5作为兼容性基线，不再讨论该语言的早期版本。自ES6以来，ECMAScript规范已经以每年发布一次为基调，该语言的版本——es2016、es2017...(现在以发布年份来确定)。

JavaScript的原始宿主环境是一个web浏览器，这仍是JavaScript代码最常见的执行环境。web浏览器环境允许JavaScript代码通过发送HTTP请求从用户的鼠标和键盘获取输入。它允许JavaScript代码用HTML和CSS向用户显示输出。

从2010年开始，另一个宿主已经可以用于JavaScript代码。Node没有限制JavaScript使用web浏览器提供的api，而是允许JavaScript访问整个操作系统，允许JavaScript程序读写文件，通过网络发送和接收数据，以及发出和服务HTTP请求。

<br/>
<br/>

## 探索

Exploring JavaScript

学习一门编程语言时，尝试书中的示例代码很重要，然后修改它们，再来一次测试你对这门语言的理解。为此，需要一个JavaScript解释器。

尝试几行JavaScript最简单的方法是在web浏览器中使用开发者工具。另一种方法是安装Node。

```
node

> let x = 2, y = 3;
> x + y
5

node hello.js
```

<br/>
<br/>

## Hello World

```JavaScript
console.log("Hello, World!")
```

执行`node hello.js`，会打印出"Hello, World!"。

<br/>

如果想在web浏览器的JavaScript控制台中看到同样的消息打印出来，那么创建一个名为`hello.html`的文件，并写入下面的代码。

```JavaScript
<script src="hello.js"></script>
```

然后使用`file://`URL加载`hello.html`到浏览器，打开控制台，查看问候语。

<br/>
<br/>

## 旅程

A Tour of JavaScript

一些示例代码:

```JavaScript
// Anything following double slashes is an English-language comment.
// Read the comments carefully: they explain the JavaScript code.

// A variable is a symbolic name for a value.
// Variables are declared with the let keyword:
let x;      // Declare a variable named x.

// Values can be assigned to variables with an = sign
x = 0;      // Now the variable x has the value 0
x           // => 0: A variable evaluates to its value.

// JavaScript supports several types of values
x = 1;                     // Numbers.
x = 0.01;                  // Numbers can be integers or reals.
x = "hello world";         // Strings of text in quotation marks.
x = 'JavaScript';          // Single quote marks also delimit strings.
x = true;                  // A Boolean value.
x = false;                 // The other Boolean value.
x = null;                  // Null is a special value that means "no value."
x = undefined;             // Undefined is another special value like null.
```

还有两种非常重要的类型是对象和数组。

```JavaScript
// JavaScript's most important datatype is the object.
// An object is a collection of name/value pairs, or a string to value map.
let book = {               // Objects are enclosed in curly braces.
    topic: "JavaScript",   // The property "topic" has value "JavaScript."
    edition: 7             // The property "edition" has value 7
};                         // The curly brace marks the end of the object.

// Access the properties of an object with . or []:
book.topic                 // => "JavaScript"
book["edition"]            // => 7: another way to access property values.
book.author = "Flanagan";  // Create new properties by assignment.
book.contents = {};        // {} is an empty object with no properties.

// Conditionally access properties with ?. (ES2020):
book.contents?.ch01?.sect1 // => undefined: book.contents has no ch01 property.

// JavaScript also supports arrays (numerically indexed lists) of values:
let primes = [2, 3, 5, 7]; // An array of 4 values, delimited with [ and ].
primes[0]                  // => 2: the first element (index 0) of the array.
primes.length              // => 4: how many elements in the array.
primes[primes.length-1]    // => 7: the last element of the array.
primes[4] = 9;             // Add a new element by assignment.
primes[4] = 11;            // Or alter an existing element by assignment.
let empty = [];            // [] is an empty array with no elements.
empty.length               // => 0

// Arrays and objects can hold other arrays and objects:
let points = [             // An array with 2 elements.
    {x: 0, y: 0},          // Each element is an object.
    {x: 1, y: 1}
];
let data = {                 // An object with 2 properties
    trial1: [[1,2], [3,4]],  // The value of each property is an array.
    trial2: [[2,3], [4,5]]   // The elements of the arrays are arrays.
};
```

<br/>

你可能已经注意到，在前面的代码中，一些注释以箭头开头(`=>`)。这些代码显示了注释之前代码产生的值。那些`// =>`注释也用作断言。

注释和断言有两种相关的风格。如果你看到`// a == 42`形式的注释，这意味着在注释运行之前的代码之后，变量a的值将是42。如果你看到`// !`形式的注释，这意味着注释前一行的代码抛出一个异常(感叹号之后的注释的其余部分通常解释抛出的是哪种异常)。

<br/>

在JavaScript中形成表达式最常见的方法之一是使用运算符:

```JavaScript
// Operators act on values (the operands) to produce a new value.
// Arithmetic operators are some of the simplest:
3 + 2                      // => 5: addition
3 - 2                      // => 1: subtraction
3 * 2                      // => 6: multiplication
3 / 2                      // => 1.5: division
points[1].x - points[0].x  // => 1: more complicated operands also work
"3" + "2"                  // => "32": + adds numbers, concatenates strings

// JavaScript defines some shorthand arithmetic operators
let count = 0;             // Define a variable
count++;                   // Increment the variable
count--;                   // Decrement the variable
count += 2;                // Add 2: same as count = count + 2;
count *= 3;                // Multiply by 3: same as count = count * 3;
count                      // => 6: variable names are expressions, too.

// Equality and relational operators test whether two values are equal,
// unequal, less than, greater than, and so on. They evaluate to true or false.
let x = 2, y = 3;          // These = signs are assignment, not equality tests
x === y                    // => false: equality
x !== y                    // => true: inequality
x < y                      // => true: less-than
x <= y                     // => true: less-than or equal
x > y                      // => false: greater-than
x >= y                     // => false: greater-than or equal
"two" === "three"          // => false: the two strings are different
"two" > "three"            // => true: "tw" is alphabetically greater than "th"
false === (x > y)          // => true: false is equal to false

// Logical operators combine or invert boolean values
(x === 2) && (y === 3)     // => true: both comparisons are true. && is AND
(x > 3) || (y < 3)         // => false: neither comparison is true. || is OR
!(x === y)                 // => true: ! inverts a boolean value
```

如果JavaScript表达式像短语，那么JavaScript语句就像完整的句子。

<br/>

函数是一个已命名和参数化的JavaScript代码块，你只定义一次，然后可以反复调用它。

```JavaScript
// Functions are parameterized blocks of JavaScript code that we can invoke.
function plus1(x) {        // Define a function named "plus1" with parameter "x"
    return x + 1;          // Return a value one larger than the value passed in
}                          // Functions are enclosed in curly braces

plus1(y)                   // => 4: y is 3, so this invocation returns 3+1

let square = function(x) { // Functions are values and can be assigned to vars
    return x * x;          // Compute the function's value
};                         // Semicolon marks the end of the assignment.

square(plus1(y))           // => 16: invoke two functions in one expression
```

在ES6及以后的版本中，有一种定义函数的快捷语法。这种简洁的语法使用 `=>` 将参数列表与函数体分开，因此以这种方式定义的函数称为箭头函数。当你希望将一个未命名的函数作为参数传递给另一个函数时，最常用的是箭头函数。

```JavaScript
const plus1 = x => x + 1;   // The input x maps to the output x + !
const square = x => x * x;  // The input x maps to the output x * x
plus1(y)                    // => 4: function invocation is the same
square(plus1(y))            // => 16
```

<br/>

当我们使用函数和对象时，我们得方法:

```JavaScript
// When functions are assigned to the properties of an object, we call
// them "methods."  All JavaScript objects (including arrays) have methods:
let a = [];                // Create an empty array
a.push(1,2,3);             // The push() method adds elements to an array
a.reverse();               // Another method: reverse the order of elements

// We can define our own methods, too. The "this" keyword refers to the object
// on which the method is defined: in this case, the points array from earlier.
points.dist = function() { // Define a method to compute distance between points
    let p1 = this[0];      // First element of array we're invoked on
    let p2 = this[1];      // Second element of the "this" object
    let a = p2.x-p1.x;     // Difference in x coordinates
    let b = p2.y-p1.y;     // Difference in y coordinates
    return Math.sqrt(a*a + // The Pythagorean theorem
                     b*b); // Math.sqrt() computes the square root
};
points.dist()              // => Math.sqrt(2): distance between our 2 points
```

<br/>

下面是一些函数，它们的主体演示了常见的控制结构语句:

```JavaScript
// JavaScript statements include conditionals and loops using the syntax
// of C, C++, Java, and other languages.
function abs(x) {          // A function to compute the absolute value.
    if (x >= 0) {          // The if statement...
        return x;          // executes this code if the comparison is true.
    }                      // This is the end of the if clause.
    else {                 // The optional else clause executes its code if
        return -x;         // the comparison is false.
    }                      // Curly braces optional when 1 statement per clause.
}                          // Note return statements nested inside if/else.
abs(-10) === abs(10)       // => true

function sum(array) {      // Compute the sum of the elements of an array
    let sum = 0;           // Start with an initial sum of 0.
    for(let x of array) {  // Loop over array, assigning each element to x.
        sum += x;          // Add the element value to the sum.
    }                      // This is the end of the loop.
    return sum;            // Return the sum.
}
sum(primes)                // => 28: sum of the first 5 primes 2+3+5+7+11

function factorial(n) {    // A function to compute factorials
    let product = 1;       // Start with a product of 1
    while(n > 1) {         // Repeat statements in {} while expr in () is true
        product *= n;      // Shortcut for product = product * n;
        n--;               // Shortcut for n = n - 1
    }                      // End of loop
    return product;        // Return the product
}
factorial(4)               // => 24: 1*4*3*2

function factorial2(n) {   // Another version using a different loop
    let i, product = 1;    // Start with 1
    for(i=2; i <= n; i++)  // Automatically increment i from 2 up to n
        product *= i;      // Do this each time. {} not needed for 1-line loops
    return product;        // Return the factorial
}
factorial2(5)              // => 120: 1*2*3*4*5
```

<br/>
<br/>

# 词法结构

lexical structure

编程语言的词法结构是一组基本规则，用于指定如何用该语言编写程序。它是一种语言的最低层次语法：例如，它指定变量名的样子，注释的分隔符字符，以及一个程序语句如何与下一个程序语句分割。

它涵盖了：

- Case sensitivity, spaces, and line breaks
- Comments
- Literals
- Identifiers and reserved words
- Unicode
- Optional semicolons

<br/>
<br/>

## 文本

The Text of a JavaScript Program

JavaScript是一门区分大小写的语言。JavaScript忽略程序中标记之间出现的空格。在大多数情况下，也会忽略换行符。由于你可以在程序中自由地使用空格和换行符，因此你可以以一种整洁和一致的方式对程序进行格式化和缩进，从而使代码易于阅读和理解。

除了常规的空格字符(`\u0020`)之外，JavaScript还将制表符、各种ASCII控制字符和各种Unicode空格字符识别为空格。JavaScript识别换行符、回车符和回车/换行序列作为行结束符。

<br/>
<br/>

## 注释

Comments

JavaScript支持两种风格的注释。在`//`和行尾之间的任何文本都被JavaScript视为注释并被忽略。字符`/*`和`*/`之间的任何文本也被视为注释，这些注释可能跨越多行，但可能不是嵌套的。

```Javascript
// This is a single-line comment.

/* This is also a comment */  // and here is another comment.

/*
 * This is a multi-line comment. The extra * characters at the start of
 * each line are not a required part of the syntax; they just look cool!
 */
```

<br/>
<br/>

## 字面量

Literals

字面量直接出现在程序中的数据值。

```JavaScript
12               // The number twelve
1.2              // The number one point two
"hello world"    // A string of text
'Hi'             // Another string
true             // A Boolean value
false            // The other Boolean value
null             // Absence of an object
```

<br/>
<br/>

## 标识符和保留字

Identifiers and Reserved Words

标识符只是一个名称。在JavaScript中，标识符用于命名常量、变量、属性、函数和类，并为JavaScript代码中的某些循环提供标签。JavaScript标识符必须以字母、下划线(`_`)或美元符号(`$`)开头，后面的字符可以是字母、数字、下划线和美元符号。（数字不允许作为第一个字符，这样JavaScript可以很容易地将标识符与数字区分开来）。

```JavaScript
// 合法的标识符
i
my_variable_name
v13
_dummy
$str
```

与任何语言一样，JavaScript保留某些标识符供语言本身使用。这些保留字不能用作常规标识符。

<br/>
<br/>

### 保留字

Reserved Words

下面的单词是JavaScript语言的一部分。其中许多是保留的关键字，不能用作常量、变量、函数或类的名称。其他的则在有限的上下文中使用，没有语法歧义。还有一些关键字不能被完全保留，以保持与旧程序的向后兼容性，因此有复杂的规则来管理何时可以将它们用作标识符，何时不能。最简单的方法是避免使用这些词作为标识符。

```txt
as      const      export     get         null     target   void
async   continue   extends    if          of       this     while
await   debugger   false      import      return   throw    with
break   default    finally    in          set      true     yield
case    delete     for        instanceof  static   try
catch   do         from       let         super    typeof
class   else       function   new         switch   var
```

JavaScript 还保留或限制使用某些关键字，目前没有使用的语言，但可能会在未来的版本。

```txt
enum  implements  interface  package  private  protected  public
```

由于历史原因，参数和 eval 在某些情况下不允许作为标识符，最好完全避免使用。

<br/>
<br/>

## Unicode

JavaScript程序是使用Unicode字符集编写的，你可以再字符串和注释中使用任何Unicode字符。为了便于移植和编辑，通常再标识符中只使用ASCII字母和数字，但这只是一种编程约定。

<br/>
<br/>

### Unicode转义字符

Unicode Escape Sequences

一些计算机和软件不能显示、输入或正确处理完整的Unicode字符集。为了支持老技术和系统，JavaScript定义了转义序列，允许我们仅使用ASCII字符编写Unicode字符。这些Unicode转义字符以`\u`开始，后根四个十六进制，或大括号括起来的一到六个十六进制。

```JavaScript
let café = 1; // Define a variable using a Unicode character
caf\u00e9     // => 1; access the variable using an escape sequence
caf\u{E9}     // => 1; another form of the same escape sequence
```

<br/>
<br/>

### Unicode规范

Unicode Normalization

如果在JavaScript程序中使用非ASCII字符，则必须知道Unicode允许对同一字符进行多种编码方式。

Unicode标准定义了所有字符的首选编码，并制定了将文本转换为适合于比较的规范形式的标准化过程。JavaScript假设它要解释的源代码已经被规范化了，它自己不做任何规范化。

<br/>
<br/>

### 可选分号

Optional Semicolons

JavaScript使用分号(`;`)来分隔语句。这对于明确代码的含义非常重要：如果没有分隔符，一条语句的结尾可能会是下一条语句的开始，反之亦然。在JavaScript中，如果两个语句写在不同的行上，通常可以省略这两个语句之间的分号。许多JavaScript程序员使用分号来显式地标记语句的结束，即使在不需要分号的地方也是如此。另一种风格是尽可能省略分号，只在少数需要分号的情况下使用。

```JavaScript
a = 3;
b = 4;

a = 3; b = 4;
```

建议显式地加上分号。

<br/>
<br/>

# 类型、值和变量

<br/>

## 定义

Definitions

JavaScript类型可以分为两类：基本类型和对象类型。基本类型包括数字(number)、文本字符串(string)和布尔值(boolean)。

特殊的JavaScript值`null`和`undefined`是基本值，但不是数字、字符串或布尔值。每个值通常被认为是它自己特殊类型的唯一成员。

任何不是数字、字符串、布尔值、Symbol、null或undefined的JavaScript值都是对象。对象是属性的集合，其中每个属性都有一个名称和一个值。一个非常特殊的对象，全局对象。

普通的JavaScript对象是命名值的无序集合。该语言还定义了一种特殊类型的对象，称为数组，表示编号值的有序集合。

除了基本对象和数组之外，JavaScript还定义了许多其他有用的对象类型。Set对象表示一组值。Map对象表示从键到值的映射。RegExp类型标识文本模式，支持对字符串进行复杂的匹配、搜索和替换操作。Date类型标识日期和时间，并支持基本日期运算。Error及其子类表示在执行JavaScript代码时可能出现的错误。

JavaScript与更静态的语言的不同之处在于，函数和类不仅仅是语言语法的一部分：它本身是可由JavaScript程序操作的值。像任何不是基本值的JavaScript值一样，函数和类是一种特殊的对象。

JavaScript解释器为内存管理执行自动垃圾收集。这意味着JavaScript程序员通常不需要担心对象或其他值的销毁或释放。当一个值不再可获得时、当程序不再有方法引用它时、解释器知道它再也不能被使用并自动回收它所占用的内存。

JavaScript支持面向对象的编程风格。不严格地说，这意味着不是用全局定义的函数来操作各种类型的值，而是由类型本身定义处理值的方法。例如，要对数组a的元素进行排序，我们不需要将a传递给`sort()`函数。相反，我们调用`sort()`方法。

```JavaScript
a.sort();       // The object-oriented version of sort(a).
```

从技术上讲，只有JavaScript对象才有方法。但是数字、字符串、布尔值和符号值的行为就好像它们有方法一样。在JavaScript中，只有null和undefined值不能调用方法。

JavaScript的对象类型是可变的，它的基本类型是不可变的。

JavaScript自由地将值从一种类型转换为另一种类型。例如，程序需要一个字符串，而你给了它一个数字，它会自动将数字转换为字符串。

常量和变量允许你在程序中使用名称来引用值。常量是用`const`声明的，变量是用`let`声明的（在旧的JavaScript代码中用`var`声明）。JavaScript常量和变量都是无类型的：声明不指定要赋给哪种类型的值。

<br/>
<br/>

## 数字

Number

JavaScript的主要数字类型Number用于表示整数和近似实数。

JavaScript数字格式允许你精确地表示 -9,007,199,254,740,992(−253) 和 9,007,199,254,740,992(253) 之间的所有整数。如果使用大于这个值的整数值，则可能会丢失末尾数字的精度。

当一个数字直接出现在JavaScript程序中时，它被称为数字文字。任何数字文字前面都可以加一个减号(`-`)，以使数字为负。

<br/>
<br/>

### 整数值

Integer Literals

在JavaScript程序中，以10为基础的整数被写成数字序列。

除了十进制，还有十六进制(`0x开头`)。在ES6及后续版本中，还可以使用前缀`0b`来表示二进制的整数、`0o`来表示八进制的整数。

```JavaScript
0
3
10000000

0xff       // => 255: (15*16 + 15)
0xBADCAFE  // => 195939070

0b10101  // => 21:  (1*16 + 0*8 + 1*4 + 0*2 + 1*1)
0o377    // => 255: (3*64 + 7*8 + 7*1)
```

<br/>
<br/>

### 浮点值

Floating-Point Literals

浮点字面值可以有小数点，它们使用传统的实数语法。实数表示为数字的整数部分，然后是小数点和小数部分。

浮点字量也可以用指数表示法表示：一个实数后面跟着字母e(E)，后面跟着一个可选的加号或减号，然后是一个整数指数。这种计数方法表示的数值，是由前面的实数乘以10的指数次幂。

```JavaScript
// 栗子
3.14
123.456
.33333
6.02e23    // 6.02 × 10²³
1.47E-32   // 1.47 x 10⁻³²
```

你可以在数字字面值中使用下划线将长字面值分解成更容易阅读的块:

```JavaScript
let billion = 1_000_000_000;   // Underscore as a thousands separator.
let bytes = 0x89_AB_CD_EF;     // As a bytes separator.
let bits = 0b0001_1101_0111;   // As a nibble separator.
let fraction = 0.123_456_789;  // Works in the fractional part, too.
```

<br/>
<br/>

### 算数运算

Arithmetic in JavaScript

JavaScript使用算术运算处理数字，包括加(`+`)、减(`-`)、乘(`*`)、除(`/`)和取模(`%`)。ES2016增加了取幂(`**`)。

除了上述基本运算，JavaScript还支持更复杂的算数运算，这些复杂运算通过作为Math对象的属性定义的函数和常量来实现。

JavaScript中的算术在溢出(overflow)、下溢(underflow)或除以0的情况下不会引发错误。

当数值操作的结果大于最大可表示数(溢出)时，结果是一个特殊的无穷值(`Infinity`)。反之为负无穷(`-Infinity`)。无穷值的任何操作都会产生无穷值。

当数值操作的结果比最小的可表示数字更接近于零时，就会发生下溢。这种情况下，JavaScript返回0。如果下溢发生在负数，将返回一个特殊的值，称为负零。这个值与普通的0几乎没有区别。

在JavaScript中除以0不是错误：它只是返回无穷大或负无穷大。然而，有一个例外：0除以0没有一个定义良好的值，这个操作的结果是一个特殊的非数字值`NaN`。

JavaScript预先定义了全局常量Infinity和NaN来保存正无穷大和非数值，这些值也可以作为Number对象的属性。

```JavaScript
Infinity                    // A positive number too big to represent
Number.POSITIVE_INFINITY    // Same value
1/0                         // => Infinity
Number.MAX_VALUE * 2        // => Infinity; overflow

-Infinity                   // A negative number too big to represent
Number.NEGATIVE_INFINITY    // The same value
-1/0                        // => -Infinity
-Number.MAX_VALUE * 2       // => -Infinity

NaN                         // The not-a-number value
Number.NaN                  // The same value, written another way
0/0                         // => NaN
Infinity/Infinity           // => NaN

Number.MIN_VALUE/2          // => 0: underflow
-Number.MIN_VALUE/2         // => -0: negative zero
-1/Infinity                 // -> -0: also negative 0
-0

// The following Number properties are defined in ES6
Number.parseInt()       // Same as the global parseInt() function
Number.parseFloat()     // Same as the global parseFloat() function
Number.isNaN(x)         // Is x the NaN value?
Number.isFinite(x)      // Is x a number and finite?
Number.isInteger(x)     // Is x an integer?
Number.isSafeInteger(x) // Is x an integer -(2**53) < x < 2**53?
Number.MIN_SAFE_INTEGER // => -(2**53 - 1)
Number.MAX_SAFE_INTEGER // => 2**53 - 1
Number.EPSILON          // => 2**-52: smallest difference between numbers
```

非数值在JavaScript中有一个不同寻常的特性：它不等于任何其它值，包括它自己。这意味这你不能编写`x === NaN`来确定x的值是否为NaN。相反，你必须写`x != x`或`Number.isNaN(x)`。

负零值也有些不同寻常，它和正零值是相等的。这意味着这两个值几乎一模一样，除了作为除数之外：

```js
let zero = 0;         // Regular zero
let negz = -0;        // Negative zero
zero === negz         // => true: zero and negative zero are equal
1/zero === 1/negz     // => false: Infinity and -Infinity are not equal
```

<br/>
<br/>

### 二进制浮点数和舍入误差

Binary Floating-Point and Rounding Errors

有无穷多的实数，但只有有限的数可以用JavaScript浮点格式精确地表示。这意味着在JavaScript中处理实数时，数字的表示通常是实际数字的近似值。

JavaScript使用的IEEE-754浮点表示法是二进制表示法，它可以精确表示2的幂形式的分数(1/2, 1/1024)。不幸的是，我们常用的分数是十进制(1/10, 1/100)。二进制浮点表示法不能精确地表示像0.1这样简单的数字。

JavaScript数字有足够的精度，可以非常接近0.1。但这个数字不能准确地表示，这可能会导致问题。

```js
// example
let x = .3 - .2;    // thirty cents minus 20 cents
let y = .2 - .1;    // twenty cents minus 10 cents
x === y             // => false: the two values are not the same!
x === .1            // => false: .3-.2 is not equal to .1
y === .1            // => true: .2-.1 is equal to .1
```

由于舍入误差，上例两者的近似值之间的差并不完全相同。需要注意，这个问题并不是JavaScript特有的，它会影响任何使用二进制浮点数的编程语言。

如果这些浮点数近似值使程序出现问题，请考虑使用缩放整数。例如，可将货币值整数美分，而不是小数美元。

<br/>
<br/>

### 使用BigInt实现任意精度整数

Arbitrary Precision Integers with BigInt

在ES2020中定义的JavaScript最新特征之一使一种名为BigInt的数字类型，该类型主要是为了支持64位整数的表示。

Bigint字面量以数字字符串编写，后根小写字母n。

```js
1234n                // A not-so-big BigInt literal
0b111111n            // A binary BigInt
0o7777n              // An octal BigInt
0x8000000000000000n  // => 2n**63n: A 64-bit integer
```

可以使用`BigInt()`函数，将普通的数字或字符串转换为BigInt值。

```js
BigInt(Number.MAX_SAFE_INTEGER)     // => 9007199254740991n
let string = "1" + "0".repeat(100); // 1 followed by 100 zeros.
BigInt(string)                      // => 10n**100n: one googol
```

BigInt值的算术运算与常规类似，只不过除法会去掉任何余数(归0).

<br/>
<br/>

### 日期和时间

Dates and Times

JavaScript定义了一个简单的Date类，用于表示和操作日期和时间的数字。JavaScript的日期是对象，但它们也有一个数字表示形式的时间戳，指定了自1970年1月1日以来经过的毫秒数。

<br/>
<br/>

## 文本

Text

表示文本的JavaScript类型是字符串。字符串是由16位值组成的不可变有序序列，每个值通常代表一个Unicode字符。空字符串是长度为0的字符串。

JavaScript使用Unicode字符集的UTF-16编码，它的字符串是无符号的16位值序列。

```js
let euro = "€";
let love = "❤";
euro.length   // => 1: this character has one 16-bit element
love.length   // => 2: UTF-16 encoding of ❤ is "\ud83d\udc99"
```

JavaScript定义的大多数字符串操作方法操作的是16位值，而不是字符。然而，在ES6中，字符串是可迭代的，它迭代的是字符串实际字符，而不是16位值。

<br/>
<br/>

### 字符串值

String Literals

使用单引号或双引号或反引号来包含字符串。

```js
""  // The empty string: it has zero characters
'testing'
"3.14"
'name="myform"'
"Wouldn't you prefer O'Reilly's book?"
"τ is the ratio of a circle's circumference to its radius"
`"She said 'hi'", he said.`
```

用反引号包含的字符串是ES6的一个特性，它允许JavaScript表达式嵌入字符串字面量。

在ES5中，可以在每行末尾使用反斜杠(`\`)来将字符串分隔成多行。ES6反引号语法允许字符串跨多行分割，在这种情况下，行结束符是字符串字面值的一部分。

```js
// A string representing 2 lines written on one line:
'two\nlines'

// A one-line string written on 3 lines:
"one\
 long\
 line"

// A two-line string written on two lines:
`the newline character at the end of this line
is included literally in this string`
```

在组合JavaScript和HTML时，最好使用一种风格的引号用于JavaScript，另一种风格的引号用于HTML。

```js
// 字符串“Thank you”在 JavaScript 表达式中被单引号引用，然后在 HTML 事件处理程序属性中被双引号引用
<button onclick="alert('Thank you')">Click Me</button>
```

<br/>
<br/>

### 字符串中的转义序列

Escape Sequences in String Literals

反斜杠(`\`)在JavaScript是转义字符。

```js
'You\'re right, it can\'t be a quote'
```

<br/>
<br/>

### 使用字符串

Working with Strings

字符串的一些操作。使用加号相加，使用`===`或`!==`进行比较。使用运算符进行比较。字符串比较简单地通过比较 16位值来完成。

```js
let msg = "Hello, " + "world";   // Produces the string "Hello, world"
let greeting = "Welcome to my blog," + " " + name;

// 确定字符串的长度
s.length

// 字符串的一些方法
let s = "Hello, world"; // Start with some text.

// Obtaining portions of a string
s.substring(1,4)        // => "ell": the 2nd, 3rd, and 4th characters.
s.slice(1,4)            // => "ell": same thing
s.slice(-3)             // => "rld": last 3 characters
s.split(", ")           // => ["Hello", "world"]: split at delimiter string

// Searching a string
s.indexOf("l")          // => 2: position of first letter l
s.indexOf("l", 3)       // => 3: position of first "l" at or after 3
s.indexOf("zz")         // => -1: s does not include the substring "zz"
s.lastIndexOf("l")      // => 10: position of last letter l

// Boolean searching functions in ES6 and later
s.startsWith("Hell")    // => true: the string starts with these
s.endsWith("!")         // => false: s does not end with that
s.includes("or")        // => true: s includes substring "or"

// Creating modified versions of a string
s.replace("llo", "ya")  // => "Heya, world"
s.toLowerCase()         // => "hello, world"
s.toUpperCase()         // => "HELLO, WORLD"
s.normalize()           // Unicode NFC normalization: ES6
s.normalize("NFD")      // NFD normalization. Also "NFKC", "NFKD"

// Inspecting individual (16-bit) characters of a string
s.charAt(0)             // => "H": the first character
s.charAt(s.length-1)    // => "d": the last character
s.charCodeAt(0)         // => 72: 16-bit number at the specified position
s.codePointAt(0)        // => 72: ES6, works for codepoints > 16 bits

// String padding functions in ES2017
"x".padStart(3)         // => "  x": add spaces on the left to a length of 3
"x".padEnd(3)           // => "x  ": add spaces on the right to a length of 3
"x".padStart(3, "*")    // => "**x": add stars on the left to a length of 3
"x".padEnd(3, "-")      // => "x--": add dashes on the right to a length of 3

// Space trimming functions. trim() is ES5; others ES2019
" test ".trim()         // => "test": remove spaces at start and end
" test ".trimStart()    // => "test ": remove spaces on left. Also trimLeft
" test ".trimEnd()      // => " test": remove spaces at right. Also trimRight

// Miscellaneous string methods
s.concat("!")           // => "Hello, world!": just use + operator instead
"<>".repeat(5)          // => "<><><><><>": concatenate n copies. ES6

// 字符串也可以像只读数组一样处理
let s = "hello, world";
s[0]                  // => "h"
s[s.length-1]         // => "d"
```

请记住，字符串在JavaScript是不可变的。某些操作字符串的方法返回新的字符串，但它们不会修改调用它们的字符串。

<br/>
<br/>

### 模板值

Template Literals

在ES6中，字符串文本可以用反引号分隔。然而，这不仅仅是另一种字符串字面量语法，因为这些模板字面量可以包含任意的JavaScript表达式。

```js
let name = "Bill";
let greeting = `Hello ${ name }.`;  // greeting == "Hello Bill."

// 下面的模板文字包含四个JavaScript表达式
// 一个Unicode转义序列，以及至少四个换行符
let errorMessage = `\
\u2718 Test failure at ${filename}:${linenumber}:
${exception.message}
Stack trace:
${exception.stack}
`;
```

模板字面量有一个强大但不太常用的特性，如果一个函数名恰好出现在开始的反引号之前，那么模板字面值中的文本和表达式的值就会传递给该函数。例如，这可以用于在将值替换到文本之前对值应用 HTML 或 SQL 转义。

ES6有一个内置的标记函数 `String.raw()` ，它在不处理任何反斜杠转义的情况下返回带有反引号的文本。

```js
`\n`.length            // => 1: the string has a single newline character
String.raw`\n`.length  // => 2: a backslash character and the letter n

// 请注意，尽管标记模板文字的标记部分是一个函数，但在调用它时没有使用圆括号。在这个非常特定的例子中，反勾字符替换了开括号和闭括号。
```

<br/>
<br/>

### 模式匹配

Pattern Matching

JavaScript定义了一种称为正则表达式(RegExp)的数据类型，用于描述和匹配文本字符串中的模式。

一对斜杠之间的文本构成了正则表达式字面量。

```js
/^HTML/;             // Match the letters H T M L at the start of a string
/[1-9][0-9]*/;       // Match a nonzero digit, followed by any # of digits
/\bjavascript\b/i;   // Match "javascript" as a word, case-insensitive

// RegExp对象的一些方法
let text = "testing: 1, 2, 3";   // Sample text
let pattern = /\d+/g;            // Matches all instances of one or more digits
pattern.test(text)               // => true: a match exists
text.search(pattern)             // => 9: position of first match
text.match(pattern)              // => ["1", "2", "3"]: array of all matches
text.replace(pattern, "#")       // => "testing: #, #, #"
text.split(/\D+/)                // => ["","1","2","3"]: split on nondigits
```

<br/>
<br/>

## 布尔值

Boolean Values

布尔值的保留字只有 true 和 false 。

任何JavaScript值都可以转换为布尔值。

```js
// 下面的值会转换为 false
undefined
null
0
-0
NaN
""  // the empty string
```

所有其它值，包括所有对象（和数组）都转换为true。

布尔值有一个 `toString()` 方法，将它们转换为字符串"true"或"false"。

三个重要的布尔运算符：与(`&&`)，或(`||`)，非(`!`)。

```js
if ((x === 0 && y === 0) || !(z === 0)) {
    // x and y are both zero or z is non-zero
}
```

<br/>
<br/>

## null和undefined

null 是一个语言关键字，其计算结果为一个特殊值，该值通常用于指示没有值。

对null使用 `typeof` 运算符返回字符串"object"，表示null可被人为是一个特殊的对象值，表示"没有对象"。然而，在实践中，null通常被视为它自己类型的唯一成员，可用来表示数字、字符串和对象的"无值"。

JavaScript中的null，类似于其他语言中的 NULL, nil或None。

<br/>

JavaScript还有第二个值，表示没有值。undefined值代表一种更深层次的缺席。它是尚未初始化的变量的值，以及在查询不存在的对象属性或数组元素的值时获得的值。undefined是一个预定义的全局变量，它被初始化为undefined值。对它使用 `typeof` 运算符，它将返回"undefined"，表明该值时特殊类型的唯一成员。

尽管存在这些差异，null 和 undefined 都表示没有值，并且经常可以互换使用。相等运算符(`==`)认为它们相等。请使用严格的相等运算符(`===`)来区分它们。两者都是假值。两者都没有任何属性或方法。

我认为 undefined 表示系统级的、意外的或类似错误的值缺失， null 表示程序级的、正常的或预期的值缺失。

<br/>
<br/>

## 符号

Symbols

Symbols 在ES6中被引入，用作非字符串属性名。它的唯一目的是作为对象属性的唯一标识符。要理解Symbols，你需要知道JavaScript的基本对象类型时属性的无序集合，其中每个属性都有一个名称和值。

```js
let strname = "string name";      // A string to use as a property name
let symname = Symbol("propname"); // A Symbol to use as a property name
typeof strname                    // => "string": strname is a string
typeof symname                    // => "symbol": symname is a symbol
let o = {};                       // Create a new object
o[strname] = 1;                   // Define a property with a string name
o[symname] = 2;                   // Define a property with a Symbol name
o[strname]                        // => 1: access the string-named property
o[symname]                        // => 2: access the symbol-named property
```

Symbol类型没有文字语法。要获取符号值，调用 `Symbol()` 函数。这个函数不会两次返回相同的值，即使调用时带有相同的实参。此函数接受一个可选的字符串实参，并返回一个唯一的符号值。

实际上， Symbol是一种语言扩展机制。

<br/>

## 全局对象

The Global Object

全局对象是一个常规的JavaScript对象，它有一个非常重要的用途：该对象的属性是JavaScript程序可用的全局定义的标识符。当JavaScript解释器启动时，它会创建一个新的全局对象，并为其提供一组初始属性。

属性定义：

- 全局常量(global constants)：如undefined, Infinity, NaN
- 全局函数(global functions)：如isNaN(), eval(), parseInt()
- 构造函数(constructor functions)：如Date(), RegExp(), String(), Object(), Array()
- 全局对象(global objects)：如Math和JSON

全局对象的初始属性不是保留字，但是应该被当作保留字来对待。

在Node中，全局对象有一个名为global的属性，该属性的值就是全局对象本身，因此在Node程序中始终可以通过名称global来引用全局对象。

在web浏览器中，Window对象充当它所代表的浏览器窗口中包含的所有JavaScript代码的全局对象。这个全局窗口对象有一个自引用窗口属性，可以用来引用全局对象。Window对象定义了核心的全局属性，但是它也定义了一些其他的全局属性，这些全局属性是特定于web浏览器和客户端JavaScript的。

ES2020最终定义了 globalThis 作为任何上下文中引用全局对象的标准方式。到2020年，所有现代浏览器和Node都实现了该特性。

<br/>
<br/>

## 不可变的原始值和可变的对象引用

Immutable Primitive Values and Mutable Object References

JavaScript中的原始值（undefined, null, 布尔值, 数字和字符串）与对象（数组和函数）有着根本的区别。原始值是不可更改的！对象是可变的——它的值可修改。

如果比较两个单独的字符串，当且仅当它们的长度相等且每个索引的字符都相等时，JavaScript 才认为它们相等。

对象的比较并非值的比较：即使两个对象包含同样的属性及相同的值，它们也是不相等的。各个索引元素完全相等的两个数组也不相等。

我们有时将对象称为 引用类型(reference type)，以此和JavaScript的基本类型区分开。对象的比较均是引用的比较：当且仅当它们引用同一个基对象时，它们才相等。

<br/>
<br/>

## 类型转换

Type Conversions

JavaScript中取值所需类型非常灵活。如果JavaScript期望使用一个字符串，它把给定的值将转换为字符串。如果期望一个数字，它将把给定的值转换为数字（如果转换结果无意义，将返回NaN）。

```js
// 栗子
10 + " objects"     // => "10 objects":  Number 10 converts to a string
"7" * "4"           // => 28: both strings convert to numbers
let n = 1 - "x";    // n == NaN; string "x" can't convert to a number
n + " objects"      // => "NaN objects": NaN converts to string "NaN"
```

<br/>

| Value | to String | to Number | to Boolean |
| - | - | - | - |
| undefined | “undefined” | NaN | false |
| null | “null” | 0 | false |
| true | “true” | 1 |  |
| false | “false” | 0 |  |
| "” (empty string) |  | 0 | false |
| “1.2” (nonempty, numeric) |  | 1.2 | true |
| “one” (nonempty, non-numeric) |  | NaN | true |
| 0 | “0” |  | false |
| -0 | “0” |  | false |
| 1 (finite, non-zero) | “1” |  | true |
| Infinity | “Infinity” |  | true |
| -Infinity | “-Infinity” |  | true |
| NaN | “NaN” |  | false |
| {} (any object) | see §3.9.3 | | true |
| [] (empty array) | "" | 0 | true |
| [9] (one numeric element) | “9” | 9 | true |
| [‘a’] (any other array) | use join() method | NaN | true |
| function(){} (any function) | | NaN | true |

<br/>

### 转换和相等

Conversions and Equality

JavaScript有两个运算符来测试两个值是否相等。

- 严格的相等运算符(`===`)：操作数不是同一类型则不认为相等
- 灵活的相等运算符(`==`)

```js
null == undefined // => true: These two values are treated as equal.
"0" == 0          // => true: String converts to a number before comparing.
0 == false        // => true: Boolean converts to number before comparing.
"0" == false      // => true: Both operands convert to 0 before comparing!
```

需要特别注意的是，一个值转换为另一个值并不意味着两个值相等。比如，你在期望使用布尔值的地方使用了 undefined ，它将转换为 false ，但这并不表明 undefined == false 。

<br/>
<br/>

### 显式转换

Explicit Conversions

尽管JavaScript可以自动做许多类型转换，但有时仍需要做显式转换，或者为了使代码变得清晰易读而做显式转换。

执行显示类型转换的最简单的方法是使用 `Boolean()`, `Number()`和`String()`函数。

```js
Number("3")    // => 3
String(false)  // => "false":  Or use false.toString()
Boolean([])    // => true
```

JavaScript中的某些运算符会做隐式的类型转换，有时用于类型转换。

```js
x + ""   // => String(x)
+x       // => Number(x)
x-0      // => Number(x)
!!x      // => Boolean(x): Note double !
```

<br/>
<br/>

### 对象转原始值

Object to Primitive Conversions

JavaScript 的对象到原始值转换复杂的一个原因是某些类型的对象有不止一个原始表现。例如，日期对象可以表示为字符串或数字时间戳。

JavaScript 规范定义了将对象转换为原始值的三种基本算法：

- 偏好字符串(prefer-string)：如果可以转换成字符串，这个算法会返回一个原始值，提供一个字符串
- 偏好数字(prefer-number)：如果可以转换，这个算法会返回一个原始值，提供一个数字
- 无偏好(no-preference)：表示不对任何类型原始值有偏好，并且可以定义自己的转换

<br/>
<br/>

## 变量声明和赋值

Variable Declaration and Assignment

术语 "变量" 意味着可以分配新值:与变量相关的值可能随着程序运行而变化。如果我们将一个值永久地赋给一个名称，那么我们将该名称称为常量而不是变量。

在JavaScript程序中使用变量或常量之前，必须先声明它。在ES6及以后的版本中，这是通过 `let` 和 `const` 关键字来完成的。在ES6之前，变量是用 `var` 声明的。

<br/>
<br/>

### let和const

Declarations with let and const

在ES6及以后，使用 `let` 关键字来声明变量。

```js
let i;
let sum;

let aa, bb;

let message = "hello";
let i = 0, j = 0, k = 0;
let x = 2, y = x*x; // Initializers can use previously declared variables
```

如果没有为变量指定初始值，则变量的值是未定义的(undefined)。

<br/>

使用 `const` 来声明常量，在声明常量是必须初始化它。

```js
const H0 = 74;         // Hubble constant (km/s/Mpc)
const C = 299792.458;  // Speed of light in a vacuum (km/s)
const AU = 1.496E8;    // Astronomical Unit: distance to the sun (km)
```

顾名思义，常量不能改变它们的值，任何这样做都会引发TypeError。

通常约定（但不是通用的）是使用全大写字母的名称来声明常量，以将它们与变量区分开来。

一种情况下，我们只对不能改变的值使用const。在另一种情况下，对于任何不会发生变化的值都使用const。

<br/>

JavaScript允许我们将循环变量声明为循环语法本身的一部分，这是使用let的另一种常见方式。

```js
for(let i = 0, len = data.length; i < len; i++) console.log(data[i]);
for(let datum of data) console.log(datum);
for(let property in object) console.log(property);
```

<br/>

变量的范围是定义变量的程序源代码的区域。用let和const声明的变量和常量是块作用域的。JavaScript类和函数定义都是块，if/else语句体、while循环体、for循环体等也是块。

当声明出现在顶层，责任和代码块之外时，我们说它是一个全局变量或常量，具有全局作用域。在Node和客户端JavaScript模块中，全局作用域是定义它的文件。然而，在传统的客户端JavaScript中，全局变量的作用域是定义它的HTML文档。

在同一个作用域中对多个let或const声明使用相同的名称是一个语法错。

```js
const x = 1;        // Declare x as a global constant
if (x === 1) {
    let x = 2;      // Inside a block x can refer to a different value
    console.log(x); // Prints 2
}
console.log(x);     // Prints 1: we're back in the global scope now
let x = 3;          // ERROR! Syntax error trying to re-declare x
```

<br/>

如果你习惯了静态类型语言，比如C和Java，那么你可能认为变量声明的主要目的是指定可以赋给变量的值的类型。但是，如你所见，没有与JavaScript的变量声明相关联的类型。一个JavaScript变量可以保存任何类型的值。

例如，在JavaScript中，先给变量赋一个数字，然后再给该变量赋一个字符串，这是完全合法的（但通常是糟糕的编程风格）。

```js
let i = 10;
i = "ten";
```

<br/>
<br/>

### var

Variable Declarations with var

在ES6以前的版本中，声明变量是使用 `var` 关键字，而没有方法声明常量。

```js
var x;
var data = [], count = data.length;
for(var i = 0; i < count; i++) console.log(data[i]);
```

虽然var和let有相同的语法，但它们的工作方式有重要的不同：

- 用var声明的变量没有块作用域。相反，它们的作用域是包含函数的主体，无论它们嵌套在函数内部有多深。
- 如果你在函数体之外使用var，它会声明一个全局变量。但是var声明的全局变量和let声明的全局变量有一个重要的不同。
  - 用var声明的全局变量是作为全局对象的属性实现的。全局对象可以引用为globaThis。
  - 用let和const声明的全局变量和常量不是全局对象的属性。
- 用var多次声明一个变量是合法的。而且因为var变量具有函数作用域而不是块作用域，所以这种类型的重声明实际上很常见。
- var声明最不寻常的特性之一就是吊装。当用var声明一个变量时，这个声明会被提升到外围函数的顶部。因此，用var声明的变量可以在外围函数的任何位置使用，不会出错。

在严格模式，如果你使用一个未声明的变量，会得到一个引用错误。但在严格模式之外，如果你将一个值赋给一个没有使用let、const或var声明的名称，那么你最终将创建一个新的全局变量。

<br/>
<br/>

### 解构赋值

Destructuring Assignment

ES6实现了一种复合声明和赋值语法，称为解构赋值。在解构赋值中，等号右边的值是一个数组或对象（结构化的值），左边使用模仿数组和对象字面量的语法指定一个或多个变量名。当发生解构赋值时，将从右边的值中提取一个或多个值（解构），并存储到左边命名的变量中。

```js
// 数组值的简单解构赋值
let [x,y] = [1,2];  // Same as let x=1, y=2
[x,y] = [x+1,y+1];  // Same as x = x + 1, y = y + 1
[x,y] = [y,x];      // Swap the value of the two variables
[x,y]               // => [3,2]: the incremented and swapped values

let [x,y] = [1];     // x == 1; y == undefined
[x,y] = [1,2,3];     // x == 1; y == 2
[,x,,y] = [1,2,3,4]; // x == 2; y == 4

let [x, ...y] = [1,2,3,4];  // y == [2,3,4]

// 解构赋值使返回数组值的函数变得很容易
// Convert [x,y] coordinates to [r,theta] polar coordinates
function toPolar(x, y) {
    return [Math.sqrt(x*x+y*y), Math.atan2(y,x)];
}

// Convert polar to Cartesian coordinates
function toCartesian(r, theta) {
    return [r*Math.cos(theta), r*Math.sin(theta)];
}

let [r,theta] = toPolar(1.0, 1.0);  // r == Math.sqrt(2); theta == Math.PI/4
let [x,y] = toCartesian(r,theta);   // [x, y] == [1.0, 1,0]

// 嵌套数组
let [a, [b, c]] = [1, [2,2.5], 3]; // a == 1; b == 2; c == 2.5

// 对象
let transparent = {r: 0.0, g: 0.0, b: 0.0, a: 1.0}; // A RGBA color
let {r, g, b} = transparent;  // r == 0.0; g == 0.0; b == 0.0
```

更多详细信息请参考原文。

<br/>
<br/>

## 摘要

本章的一些要点：

- 数字和文本
- 布尔型、符号、空、未定义
- 不可变基元类型和可变一你用类型
- 类型转换：隐式和显示
- 声明和初始化长脸和变量，词法范围
- 解构赋值

<br/>
<br/>

# 表达式和运算符

JavaScript expressions and the operators

本章的一些关键点：

- 表达式是JavaScript程序的短语
- 任何表达式都可以被计算为JavaScript值
- 表达式除了产生一个值外，还有副作用
- 简单的表达式，如文字、变量引用和属性访问，可以与运算符结合使用以生成更大的表达式
- 定义了算术、比较、布尔、赋值、位操作和杂项运算符，包括三元条件运算符
- 加号既可用于数字，也可用于字符串

<br/>

## 基本表达式

Primary Expressions

最简单的表达式被称为基本表达式。JavaScript中的主表达式是常量或文字值、某些语言关键字和变量引用。

当任何标识符在程序中单独出现时，JavaScript假定它是全局对象的变量、常量或属性，并查找其值。如果不存在具有该名称的变量，则尝试对不存在的变量求值会抛出一个ReferenceError。

<br/>
<br/>

## 对象和数组初始化器

Object and Array Initializers

对象和数组初始化器是其值为新创建的对象或数组的表达式。

```js
// array
let sparseArray = [1,,,,5];

// object
let p = { x: 2.3, y: -1.2 };  // An object with 2 properties
let q = {};                   // An empty object with no properties
q.x = 2.3; q.y = -1.2;        // Now q has the same properties as p
```

<br/>
<br/>

## 函数定义表达式

Function Definition Expressions

函数定义表达式定义一个JavaScript函数，这种表达式的值是新定义的函数。

```js
// function
let square = function(x) { return x * x; };
```

<br/>
<br/>

## 属性访问表达式

Property Access Expressions

属性访问表达式的计算结果为对象属性或数组元素的值。

```js
// 属性访问的两种语法
expression . identifier
expression [ expression ]

// example
let o = {x: 1, y: {z: 3}}; // An example object
let a = [o, 4, [5, 6]];    // An example array that contains the object
o.x                        // => 1: property x of expression o
o.y.z                      // => 3: property z of expression o.y
o["x"]                     // => 1: property x of object o
a[1]                       // => 4: element at index 1 of expression a
a[2]["1"]                  // => 6: element at index 1 of expression a[2]
a[0].x                     // => 1: property x of expression a[0]
```

<br/>
<br/>

### 条件属性访问

Conditional Property Access

ES2020增加了两种属性访问方法：

```js
expression ?. identifier
expression ?.[ expression ]

// example
let a = { b: null };
a.b?.c.d   // => undefined

let a = { b: {} };
a.b?.c?.d  // => undefined
```

在JavaScript中，null和undefined是仅有的两个没有属性的值。在正则属性访问表达式中使用。如果表达式左边的计算为null或undefined，则会地到一个TypeError。可以使用上述方法防止此类错误。

<br/>
<br/>

## 调用表达式

Invocation Expressions

调用表达式是JavaScript中用于调用函数或方法的语法。

```js
f(0)            // f is the function expression; 0 is the argument expression.
Math.max(x,y,z) // Math.max is the function; x, y, and z are the arguments.
a.sort()        // a.sort is the function; there are no arguments.
```

如果函数表达式的值不是一个函数，则抛出TypeError。如果函数使用return返回值，则值成为调用表达式的值。否则，调用表达式的值为undefined。

<br/>
<br/>

### 条件调用

Conditional Invocation

在ES2020中，你同样可以在调用函数时使用 `?.` 。通常情况下，当调用函数时，如果左侧的表达式为null、undefined或其他非函数类型，则会抛出TypeError异常。使用这个语法，整个表达式将求值为undefined，并且不会抛出异常。

```js
function square(x, log) { // The second argument is an optional function
    log?.(x);             // Call the function if there is one
    return x * x;         // Return the square of the argument
}
```

<br/>
<br/>

## 对象创建表达式

Object Creation Expressions

对象创建表达式会创建一个新的对象并调用一个函数(称为构造函数)来初始化该对象的属性。

```js
new Object()
new Point(2,3)

// 如果没有传递参数，则可省略括号
new Object
new Date
```

<br/>
<br/>

## 运算符

Operator Overview

JavaScript运算符，大概分为这几类：

- 一元运算符
- 位操作运算符
- 加减乘除运算符
- 布尔运算符
- 比较运算符
- 赋值运算符
- 其他运算符: `...`, `?:`, `in`, `delete`, `instanceof`, `typeof`, `??`, `?.`, `,`, `void`

<br/>

下表按优先级排序。

> 标记A的列，给出了运算符的结合性。可以是从L(从左到右)或R(从右到左)。<br/>
> 标记N的列，指定了运算符的数量。<br/>
> 标记为类型的列，列出了操作数的预期类型以及结果类型。

| 运算符 | 描述 | A | N | 类型 |
| - | - | - | - | - |
| `++` | 前缀或后缀递增 | R | 1 | lval->num |
| `--` | 前缀或后缀递减 | R | 1 | lval->num |
| `-` | 负数 | R | 1 | num->num |
| `+` | 转换为数字 | R | 1 | any->num |
| `~` | 反转位 | R | 1 | int->int |
| `!` | 反转布尔值(非) | R | 1 | bool->bool |
| `delete` | 移除一个属性 | R | 1 | lval->bool |
| `typeof` | 确定操作数的类型 | R | 1 | any->str |
| `void` | 返回未定义的值 | R | 1 | any->undef |
| `**` | 指数化 | R | 2 | num,num->num |
| `*`, `/`, `%` | 乘, 除, 取余 | L | 2 | num,num->num |
| `+`, `-` | 加, 减 | L | 2 | num,num->num |
| `+` | 字符串连接 | L | 2 | str,str->str |
| `<<` | 左移 | L | 2 | int,int->int |
| `>>` | 带符号扩展的右移 | L | 2 | int,int->int |
| `>>>` | 带零扩展的右移 | L | 2 | int,int->int |
| `<`, `<=`, `>`, `>=` | 数字顺序比较 | L | 2 | num,num->bool |
| `<`, `<=`, `>`, `>=` | 字母顺序比较 | L | 2 | str,str->bool |
| `instanceof` | 测试对象类 | L | 2 | obj,func->bool |
| `in` | 测试属性是否存在 | L | 2 | any,obj->bool | 
| `==` | 非严格相等 | L | 2 | any,any->bool |
| `!=` | 非严格不等 | L | 2 | any,any->bool |
| `===` | 严格相等 | L | 2 | any,any->bool |
| `!==` | 严格不等 | L | 2 | any,any->bool |
| `&` | 按位与 | L | 2 | int,int->int |
| `^` | 按位异或 | L | 2 | int,int->int |
| `\|` | 按位或 | L | 2 | int,int->int |
| `&&` | 逻辑与 | L | 2 | any,any->any |
| `\|\|` | 逻辑或 | L | 2 | any,any->any |
| `??` | 选择第一个已定义的操作数 | L | 2 | any,any->any |
| `?:` | 选择第二或第三个操作数 | R | 3 | bool,any,any->any |
| `=` | 赋值 | R | 2 | lval,any->any |
| `**=`, `*=`, `/=`, `%=` | 操作和赋值 | R | 2 | lval,any->any |
| `+=`, `-=`, `&=`, `^=`, `\|=` | | | |
| `<<=`, `>>=`, `>>>=` | | | |
| `,` | 丢弃第一个操作数，返回第二个 | L | 2 | any,any->any |

<br/>
<br/>

### 操作数的数量

Number of Operands

<br/>
<br/>

### 操作数和结果类型

Operand and Result Type

<br/>
<br/>

### 运算符副作用

Operator Side Effects

<br/>
<br/>

### 运算符优先级

Operator Precedence

<br/>
<br/>

### 运算符结合性

Operator Associativity

<br/>
<br/>

### 求值顺序

Order of Evaluation

<br/>
<br/>

## 算术表达式

Arithmetic Expressions

算数表达式包括：

- 一元算数运算符
- 位运算符

<br/>
<br/>

## 关系表达式

Relational Expressions

关系表达式包括：

- 相等和不相等
- 比较
- in
- instanceof

<br/>
<br/>

## 逻辑表达式

Logical Expressions

逻辑表达式包括：

- 与(`&&`)
- 或(`\|\|`)
- 非(`!`)

<br/>
<br/>

## 赋值表达式

Assignment Expressions

<br/>
<br/>

## 评估表达式

Evaluation Expressions

```js
eval("3+2") // => 5
```

<br/>
<br/>

## 杂项运算符

Miscellaneous Operators

<br/>
<br/>

# 语句

Statements

语句就是JavaScript句子或命令，JavaScript句子以分号(`;`)结尾。

程序只不过是一些列顺序执行的语句。默认情况下，JavaScript解释器按照语句的编写顺序依次执行这些语句。另一种方法是改变默认的执行顺序，如：

- 条件(condition)
- 循环(loop)
- 跳转(jump)

<br/>
<br/>

## 表达式语句

Expression Statements

JavaScript中最简单的语句是表达式。

<br/>
<br/>

## 复合语句和空语句

Compound and Empty Statements

一个语句块（括在花括号内的一系列语句）将多个语句组合成一个复合语句。

空语句在期望的位置不包含任何语句。空语句不执行任何操作，当要创建一个包含空主体的循环时，空语句会很有用。在下面的for循环中，所有工作均由表达式完成，并不需要循环体，因此使用一个空语句。

```js
// block statement
{
    x = Math.PI;
    cx = Math.cos(x);
    console.log("cos(π) = " + cx);
}

// null statement
;

// Initialize an array a
for(let i = 0; i < a.length; a[i++] = 0) ;

// 当有意使用空语句时，以下注释代码是一种清楚表明是故意这样做的方式的好主意
for(let i = 0; i < a.length; a[i++] = 0) /* empty */ ;
```

<br/>
<br/>

## 条件语句

Conditionals

条件语句根据指定表达式的值执行或跳过其他语句。

建议使用花括号来括住主体，这样更直观清晰。

<br/>
<br/>

### if语句

if语句是基本的控制语句。

```js
if (expression)
    statement
```

栗子

```js
if (username == null)       // If username is null or undefined,
    username = "John Doe";  // define it

// If username is null, undefined, false, 0, "", or NaN, give it a new value
if (!username) username = "John Doe";

// 可以使用语句块将多个语句组合为一个
if (!address) {
    address = "";
    message = "Please specify a mailing address.";
}
```

<br/>

if else语句:

```js
if (expression)
    statement1
else
    statement2
```

栗子

```js
if (n === 1)
    console.log("You have 1 new message.");
else
    console.log(`You have ${n} new messages.`);

if (i === j) {
    if (j === k) {
        console.log("i equals k");
    }
} else {  // What a difference the location of a curly brace makes!
    console.log("i doesn't equal j");
}
```

<br/>
<br/>

### else if

else if语句并不是真正的JavaScript语句，而只是一个经常使用的编程习惯用法。

```js
if (n === 1) {
    // Execute code block #1
} else if (n === 2) {
    // Execute code block #2
} else if (n === 3) {
    // Execute code block #3
} else {
    // If all else fails, execute block #4
}
```

<br/>
<br/>

### switch语句

当所有分支都依赖同一表达式的值时，使用多个if语句重复评估该表达式是浪费性能的，因此可以使用switch语句。

break语句使解释器跳到switch语句的末尾，并继续其后的语句。case子句仅指定了所需代码的起点，没有指定任何终点。在没有break语句的情况下，switch语句在于其表达式值匹配的case处开始执行其代码块，并继续执行语句，知道到达该块的末尾为止。

在函数内部使用switch时，可以使用return而不是break。两者都可用于终止switch语句并防止执行陷入下一种情况。

如果所有case表达式都不与switch表达式匹配，则switch语句将在标有defalult的语句处开始执行其主体。如果没有default，则switch语句将完全跳过其主体。default一般习惯写在末尾，但实际上它可以出现在switch语句主体内的任何位置。

```js
switch(expression) {
    statements
}

switch(n) {
case 1:                        // Start here if n === 1
    // Execute code block #1.
    break;                     // Stop here
case 2:                        // Start here if n === 2
    // Execute code block #2.
    break;                     // Stop here
case 3:                        // Start here if n === 3
    // Execute code block #3.
    break;                     // Stop here
default:                       // If all else fails...
    // Execute code block #4.
    break;                     // Stop here
default:
    // xxx
}
```

栗子:

```js
function convert(x) {
    switch(typeof x) {
    case "number":            // Convert the number to a hexadecimal integer
        return x.toString(16);
    case "string":            // Return the string enclosed in quotes
        return '"' + x + '"';
    default:                  // Convert any other type in the usual way
        return String(x);
    }
}
```

<br/>
<br/>

## 循环语句

Loops

JavaScript有五个循环语句：

- while
- do/while
- for
- for/of
- for/in

<br/>
<br/>

### while语句

```js
while (expression)
    statement
```

栗子:

```js
let count = 0;
while(count < 10) {
    console.log(count);
    count++;
}
```

<br/>
<br/>

### do/while语句

意味着循环的主体至少执行一次。

do/whild不如while常用——在实践中，确定要至少执行一次循环在某种程度上并不常见。

```js
do
    statement
while (expression);
```

栗子:

```js
function printArray(a) {
    let len = a.length, i = 0;
    if (len === 0) {
        console.log("Empty Array");
    } else {
        do {
            console.log(a[i]);
        } while(++i < len);
    }
}
```

<br/>
<br/>

### for语句

```js
for(initialize ; test ; increment)
    statement
```

栗子：

```js
for(let count = 0; count < 10; count++) {
    console.log(count);
}
```

<br/>
<br/>

### for/of语句

ES6定义了一个新的循环语句: for/of，它适用于可迭代对象。数组、字符串、set和map都是可迭代的。

```js
let data = [1, 2, 3, 4, 5, 6, 7, 8, 9], sum = 0;
for(let element of data) {
    sum += element;
}
sum       // => 45
```

<br/>

对象是不可迭代的（默认情况下），尝试在常规对象上使用for/of会在运行时引发TypeError。 如果想要便利对象的属性，可以使用for/in循环，或通过 `Object.keys()` 、 `Object.values()` 或 `Object.entries()` 方法。

在ES6中，字符串是一个一个字符可迭代对象。

ES6内置的Set和Map类是可迭代的。

ES2018引入了一种新型的迭代器，称为异步迭代器，以及for/of循环的一种变体，即与异步迭代器一起使用的for/await循环。

<br/>
<br/>

### for/in语句

for/in循环在in之后可用于任何对象，它遍历指定对象的属性名称。

```js
for (variable in object)
    statement
```

<br/>
<br/>

## 跳转语句

Jumps

JavaScript中的一些跳转语句：

- 标记语句
- break
- continue
- return
- yield
- throw
- try/catch/finally

<br/>
<br/>

### 标记语句

Labeled Statements

任何语句都可以在其前面加上标识符和冒号来标记。

通过给语句加标签，可以为它指定一个名称，可以使用该名称在程序的其他位置引用它。

```js
identifier: statement
标识符: 语句

// 栗子
mainloop: while(token !== null) {
    // Code omitted...
    continue mainloop;  // Jump to the next iteration of the named loop
    // More code omitted...
}
```

标签的命名空间与变量和函数的命名空间不同，因此可以将相同的标识符用作语句标签以及变量或函数名称。带标签的语句本身可以被标记。实际上，这意味着任何语句都可以具有多个标签。

<br/>
<br/>

### break语句

单独使用 break 语句会使最里面的循环或 switch 语句立即退出。

当循环具有复杂的终止条件时，通常使用 break 语句更容易实现其中一些条件，而不是尝试在单个循环表达式中全部表达它们。

JavaScript 还允许 break 关键字后跟一个语句标签（只是标识符，没有冒号）。当 break 与标签一起使用时，它跳转到具有指定标签的封闭语句的末尾或终止。

```js
for(let i = 0; i < a.length; i++) {
    if (a[i] === target) break;
}

break labelname;
```

<br/>
<br/>

### continue语句

continue 不是退出循环，而是在重新开始循环的下一次迭代。

continue 语句也可以与标签一起使用。

<br/>
<br/>

### return语句

函数内的 return 语句指定该函数的调用值。

在没有 return 语句的情况下，函数调用只是依次依次执行函数体中的每个语句，直到到达函数末尾，然后返回到其调用者。在这种情况下，调用表达式的计算结果为 undefined。

<br/>
<br/>

### yield语句

yield 仅在ES6生成器函数中使用，以在所生成的值序列中生成下一个值，而无需实际返回。

为了了解 yield，必须了解迭代器和生成器。

```js
// A generator function that yields a range of integers
function* range(from, to) {
    for(let i = from; i <= to; i++) {
        yield i;
    }
}

```

<br/>
<br/>

### throw语句

在JavaScript中，只要发生运行时错误，并且只要程序使用 throw 语句显式抛出一个异常，就会引发异常。使用 try/catch/finally 语句捕获异常。

引发异常时，JavaScript 解释器立即停止正常程序执行并跳转到最近的异常处理程序。

```js
throw expression;

function factorial(x) {
    // If the input argument is invalid, throw an exception!
    if (x < 0) throw new Error("x must not be negative");
    // Otherwise, compute a value and return normally
    let f;
    for(f = 1; x > 1; f *= x, x--) /* empty */ ;
    return f;
}
factorial(4)   // => 24
```

<br/>
<br/>

### try/catch/finally语句

try/catch/finally 语句是 JavaScript 的异常处理机制。catch 和 finally 块都是可选的，但是 try 块必须至少与这些块之一相伴。

如果 try 块中发生异常，并且有一个关联的 catch 块来处理该异常，则解释器将首先执行 catch 块，然后执行 finally 块。如果没有本地 catch 块来处理该异常，则解释器将首先执行 finally 块，然后跳转到最近的包含 catch 子句。

```js
try {
    // Normally, this code runs from the top of the block to the bottom
    // without problems. But it can sometimes throw an exception,
    // either directly, with a throw statement, or indirectly, by calling
    // a method that throws an exception.
}
catch(e) {
    // The statements in this block are executed if, and only if, the try
    // block throws an exception. These statements can use the local variable
    // e to refer to the Error object or other value that was thrown.
    // This block may handle the exception somehow, may ignore the
    // exception by doing nothing, or may rethrow the exception with throw.
}
finally {
    // This block contains statements that are always executed, regardless of
    // what happens in the try block. They are executed whether the try
    // block terminates:
    //   1) normally, after reaching the bottom of the block
    //   2) because of a break, continue, or return statement
    //   3) with an exception that is handled by a catch clause above
    //   4) with an uncaught exception that is still propagating
}
```

<br/>
<br/>

## 杂项语句

Miscellaneous Statements

描述其余三个JavaScript语句：

- with
- debugger
- use strict

<br/>
<br/>

### with语句

with 语句运行代码块，就像指定对象的属性是该代码范围内的变量一样。

该语句使用对象的属性作为变量创建一个临时作用域，然后在该作用域内执行语句。

```js
with (object)
    statement
```

在严格模式下禁止 with 语句，并且在非严格模式下应将其视为已弃用，请尽可能避免使用它。与 with 一起使用的 JavaScript 代码难以优化，并且与没有 with 语句编写的等效代码相比，运行速度可能慢得多。

with 语句的常用用法是使使用深度嵌套的对象层次结构更清晰。

<br/>
<br/>

### debugger语句

debugger 用于执行某种调试。实际上，该语句就像一个断点，停止执行 JavaScript 代码，并且可以使用调试器打印变量的值，检查调用堆栈等。

```js
function f(o) {
  if (o === undefined) debugger;  // Temporary line for debugging purposes
  ...                             // The rest of the function goes here.
}
```

<br/>
<br/>

### 严格模式

use strict

"use strict" 的目的式指示后面的代码是严格代码。

严格代码在严格模式下执行。严格模式是该语言的受限子集，可修复重要的语言缺陷并提供更强的错误检查和更高的安全性。

严格模式和非严格模式有以下区别：

- 在严格模式下，不允许使用 with 语句。
- 在严格模式下，必须声明所有变量。如果将值分配给未声明的变量、函数、函数参数、catch 子句参数或全局对象的属性的标识符，则会引发 ReferenceError。
- 在严格模式下，作为函数（而不是方法）调用的函数的 this 值是 undefined。在非严格模式下，作为函数调用的函数总是将全局对象作为其 this 值。
- 在严格模式下，对不可写属性的分配以及在不可扩展对象上尝试创建新属性的尝试将引发 TypeError。在非严格模式下，这些尝试将以静默方式失败。
- 在严格模式下，传递给 `eval()` 的代码无法像在非严格模式下那样在调用者的作用域中声明变量或定义函数。

<br/>
<br/>

## 声明语句

Declarations

从技术上讲，关键字 const, let, var, function, class, import 和 export 并不是语句，但它们看起来很像语句，并且本书将它们非正式地称为语句。

这些关键字被更准确地描述为声明而不是语句。不确切地说，可以将声明视为代码开始运行之前已处理的部分程序。

<br/>
<br/>

### const/let/var关键字

在ES6之前，`var`关键字是唯一可以用来声明变量的方式，并且没有办法去声明常量。在ES6及以后的版本中，`const` 用于声明常量，`let` 用于声明变量。

使用var关键字所声明的变量作用域仅限于包含它们的函数内部，而不是包含它们的块级作用域内部。这可能会导致错误，在现代JavaScript中真正没有理由使用var而不是let。

```js
const TAU = 2*Math.PI;
let radius = 3;
var circumference = TAU * radius;
```

<br/>
<br/>

### function关键字

关键字 `function` 用于定义函数。

JavaScript代码块中的任何函数声明都会在该代码运行之前进行处理，并且函数名绑定到整个块中的函数对象。我们说函数声明被提升了，在程序中调用一个函数的代码可以存在于定义该函数的代码之前。

```js
function area(radius) {
    return Math.PI * radius * radius;
}
```

<br/>
<br/>

### class关键字

关键字 `class` 用于创建一个类。

与函数不同，类声明不会被提升，你不能在出现类声明之前的代码处调用它。

```js
class Circle {
    constructor(radius) { this.r = radius; }
    area() { return Math.PI * this.r * this.r; }
    circumference() { return 2 * Math.PI * this.r; }
}
```

<br/>
<br/>

### import/export关键字

关键字 `import` 和 `export`，以便在JavaScript代码中一个模块中定义的值可以在另一个模块中使用。模块是具有自己全局命名空间的JavaScript代码文件，完全独立于其他模块。

JavaScript模块内的值是私有的，除非它们已经被明确地导出，否则不能被导入到其他模块中。当一个模块只导出单个值时，通常使用特殊形式 `export default`。

```js
import Circle from './geometry/circle.js';
import { PI, TAU } from './geometry/constants.js';
import { magnitude as hypotenuse } from './vectors/utils.js';
```

```js
// geometry/constants.js
const PI = Math.PI;
const TAU = 2 * PI;
export { PI, TAU };

export const TAU = 2 * Math.PI;
export function magnitude(x,y) { return Math.sqrt(x*x + y*y); }
export default class Circle { /* class definition omitted here */ }
```

<br/>
<br/>

# 对象

Objects

对象是JavaScript最基本的数据类型。

<br/>
<br/>

## 对象介绍

Introduction to Objects

对象是一个复合值，聚合了多个值，并允许按名称存储和获取这些值。对象是属性的无序集合，每个属性有一个名称和值。类似于其它语言的字典这种数据结构。

除了维护自己的属性，JavaScript对象还继承了另一个对象的属性，即它的原型。对象的方法通常是继承的属性，而这种 **原型继承** 是JavaScript的一个关键特性。

JavaScript对象是动态的，属性通常可以添加和删除，但它们可以用来模拟静态类型语言的静态对象和结构体。

有时，能够区分直接在对象上定义的属性和从原型对象继承的属性是很重要的。JavaScript使用术语 **自有属性** 来指代非继承属性。

除了名称和值之外，每个属性还有三个属性特性:

- 可写(writable): 是否可以设置属性的值
- 可枚举(enumerable): 是否会在for/in循环中返回属性名称
- 可配置(configurable): 是否可以删除属性，是否可以修改属性

许多JavaScript的内置对象具有只读、不可枚举或不可配置的属性。但是，在默认情况下，创建的对象的所有属性都是可写、可枚举和可配置的。

<br/>
<br/>

## 创建对象

Creating Objects

对象可以用对象字面量创建，也可以用 `new` 关键字和 `Object.create()` 函数来创建。

<br/>
<br/>

### 对象字面量

Object Literals

对象字面量是由若干键/值对组成的映射表。

```js
let empty = {};                          // An object with no properties
let point = { x: 0, y: 0 };              // Two numeric properties
let p2 = { x: point.x, y: point.y+1 };   // More complex values
let book = {
    "main title": "JavaScript",          // These property names include spaces,
    "sub-title": "The Definitive Guide", // and hyphens, so use string literals.
    for: "all audiences",                // for is reserved, but no quotes.
    author: {                            // The value of this property is
        firstname: "David",              // itself an object.
        surname: "Flanagan"
    }
};
```

对象文本中最后一个属性的尾随逗号是合法的，并且某些编程样式鼓励使用这些尾随逗号。因为，如果之后在对象文本的末尾添加新属性，则不太可能导致语法错误。

对象字面量是一个表达式，它每次计算时都会创建和初始化一个新对象。

<br/>
<br/>

### 使用new创建对象

`new` 关键字必须紧跟一个函数调用。这种方式使用函数叫做构造函数调用，其提供初始化一个新创建的对象的服务。

```js
// 在JavaScript中，内置类型都包含相对应的构造函数
let o = new Object();  // Create an empty object: same as {}.
let a = new Array();   // Create an empty array: same as [].
let d = new Date();    // Create a Date object representing the current time
let r = new Map();     // Create a Map object for key/value mapping
```

<br/>
<br/>

### 原型

Prototypes

在讲第三种对象创建技术之前，我们应该了解一下 **原型**。每一个JavaScript对象都和另一个对象(原型)相关联，每一个对象都从原型继承属性。

所有通过对象字面量创建的对象都具有同一个原型对象，并可以通过 `Object.prototype` 获得对原型对象的引用。通过关键字new和构造函数调用创建的对象的原型就是构造函数的prototype属性的值。

请记住，几乎所有对象都有原型，但只有相对较少的对象具有原型属性。正是这些具有原型属性的对象定义了所有其他对象的原型。

没有原型的对象不多，`Object.prototype` 就是其中之一，它不继承任何属性。其他原型都想都是普通对象，普通对象都具有原型。大部分的都早函数都具有一个继承自 `Object.prototype` 的原型。

<br/>
<br/>

### Object.create()

`Object.create()` 创建一个新的对象，用第一个实参作为它的原型。

```js
let o1 = Object.create({x: 1, y: 2});     // o1 inherits properties x and y.
o1.x + o1.y                               // => 3

// 可以使用null来创建一个没有原型的新对象，
// 但这种方式创建的对象不会继承任何东西，甚至不包括基础方法。
let o2 = Object.create(null);             // o2 inherits no props or methods.

// 创建一个普通的空对象
let o3 = Object.create(Object.prototype); // o3 is like {} or new Object().
```

可以通过任意原型创建新对象，这是一个强大的特性。

`Object.create()` 的一个用途是预防对象无意间(非恶意地)被无法支配的库函数篡改。可以创建一个继承它的对象来传递给函数，而不是将其直接传递给函数。当函数读取继承对象的属性时，实际上读取的是继承来的值。如果给继承对象的属性赋值，则这些属性指挥影响这个继承对象自身，而不是原始对象。

```js
let o = { x: "don't change this value" };
library.function(Object.create(o));  // Guard against accidental modifications
```

<br/>
<br/>

## 查询和设置属性

Querying and Setting Properties

可以通过点(`.`)或方括号(`[]`)来获取属性的值。

```js
let author = book.author;       // Get the "author" property of the book.
let name = author.surname;      // Get the "surname" property of the author.
let title = book["main title"]; // Get the "main title" property of the book.

book.edition = 7;                   // Create an "edition" property of book.
book["main title"] = "ECMAScript";  // Change the "main title" property.
```

<br/>
<br/>

### 对象作为关联数组

Objects As Associative Arrays

下面两个表达式具有相同的值。

```js
object.property
object["property"]
```

第一种使用点，这和C和Java中访问一个结构体或对象的静态字段非常类似。第二种使用方括号，看起来更像数组，只是这个数组元素是通过字符串索引而不是数字索引。这种数组就是我们所说的关联数组(associative array)，也称作散列、映射或字典。JavaScript对象都是关联数组。

在C, C++, Java和一些强类型语言中，对象只能拥有固定数目的属性，并且这些属性的名称必须提前定义好。由于JavaScript是一个弱类型语言，因此不适用这条规则：对象在程序中可以创建任意数量的属性。

当使用点运算符访问对象的属性时，属性名用一个标识符来表示。标识符必须直接出现在JavaScript程序中，它们不是数据类型，所以无法在程序中修改。当通过方括号来访问对象的属性时，属性名通过字符串来表示，字符串是JavaScript的数据类型，在程序运行时可以修改和创建它们。

<br/>
<br/>

### 继承

Inheritance

JavaScript对象中有一组自有属性(own properties)，也有一组属性是继承自它的原型对象。想要理解属性继承，必须更深入地了解属性访问的细节。

假设要查询对象o的属性x。如果o中不存在x名称的自由属性，那么将会继续在o的原型对象中查询属性x。如果原型对象中也没有x，但这个原型对象也有原型，那么继续在这个原型对象的原型上执行查询，直到找到x或者查找到一个原型是null的对象为止。可以看到，对象的原型属性构成了一个链，通过这个链可以实现属性的继承。

<br/>
<br/>

### 属性访问错误

Property Access Errors

查询一个不存在的属性并不会报错，如果在对象o自身的属性或继承的属性中均未找到属性x，属性访问表达式 `o.x` 返回undefined。

但是，如果对象不存在，那么试图查询这个不存在的对象的属性就会报错。null和undefined值都没有属性，因此查询这些值的属性会报错。

ES2020支持用 `?.` 条件属性访问，它允许这样重写上面的赋值表达式。

```js
book.subtitle    // => undefined: property doesn't exist

let len = book.subtitle.length; // !TypeError: undefined doesn't have length

let surname = book?.author?.surname;
```

<br/>
<br/>

## 删除属性

Deleting Properties

删除运算符能删除对象中的属性。`delete` 没有操作属性的值，而是操作属性的属性。

`delete` 运算符只删除自有属性，不删除继承属性。（想要删除继承属性，必须从定义这个属性的原型对象上删除它。这回影响所有继承这个原型的对象。）

如果删除成功，或删除没有任何影响时，删除表达式计算结果为 true（如删除不存在的属性）。它作用域非属性访问表达式(无用代码)时也返回 true。

`delete` 不能删除那些可配置为 false 的属性。某些内置对象的属性是不可配置的，比如通过变量声明和函数声明创建的全局对象的属性。在严格模式下，删除一个不可配置属性会类型错误。在非严格模式中，会返回 false。

```js
delete book.author;          // The book object now has no author property.
delete book["main title"];   // Now it doesn't have "main title", either.

let o = {x: 1};    // o has own property x and inherits property toString
delete o.x         // => true: deletes property x
delete o.x         // => true: does nothing (x doesn't exist) but true anyway
delete o.toString  // => true: does nothing (toString isn't an own property)
delete 1           // => true: nonsense, but true anyway

// In strict mode, all these deletions throw TypeError instead of returning false
delete Object.prototype // => false: property is non-configurable
var x = 1;              // Declare a global variable
delete globalThis.x     // => false: can't delete this property
function f() {}         // Declare a global function
delete globalThis.f     // => false: can't delete this property either
```

<br/>
<br/>

## 测试属性

Testing Properties

JavaScript对象可以看作属性的集合，经常会检测集合中成员的所属关系——判断某个属性是否存在于某个对象中。可以用in, hasOwnPreperty(), propertylsEnumerable()等方法。

```js
let o = { x: 1 };
"x" in o         // => true: o has an own property "x"
"y" in o         // => false: o doesn't have a property "y"
"toString" in o  // => true: o inherits a toString property

let o = { x: 1 };
o.hasOwnProperty("x")        // => true: o has an own property x
o.hasOwnProperty("y")        // => false: o doesn't have a property y
o.hasOwnProperty("toString") // => false: toString is an inherited property

let o = { x: 1 };
o.x !== undefined        // => true: o has a property x
o.y !== undefined        // => false: o doesn't have a property y
o.toString !== undefined // => true: o inherits a toString property
```

<br/>
<br/>

## 枚举属性

Enumerating Properties

遍历对象的属性。

对象继承的内置方法不可枚举，但在代码中给对象添加的属性都是可枚举的。

```js
let o = {x: 1, y: 2, z: 3};          // Three enumerable own properties
o.propertyIsEnumerable("toString")   // => false: not enumerable
for(let p in o) {                    // Loop through the properties
    console.log(p);                  // Prints x, y, and z, but not toString
}

// Object.keys() 返回对象的可枚举自有属性名称数组集合
// Object.getOwnPropertyNames() 返回数组中也包含不可迭代的自有属性，只要它们的名称是字符串
// Object.getOwnPropertySymbols() 返回名称是 Symbol 的自有属性，无论它们是否可枚举
// Reflect.ownKeys() 返回所有的自由属性名称，包括可枚举和不可枚举类型，也包括字符串和 Symbol
```

<br/>
<br/>

### 属性枚举顺序

Property Enumeration Order

ES6正式定义元素的自有属性的枚举顺序。上面的一些方法按一些顺序排列：

- 首先列出名称为非负整数的字符串属性，按从小到大的数字列出。此规则意味着数组和数组类对象将按顺序枚举其属性。
- 列举所有看起来像数组索引的属性后，将列出所有具有字符串名称的剩余属性。这些属性按添加到对象的顺序列出。
- 最后，其名称为Symbol对象的属性按添加的对象的顺序列出。

<br/>
<br/>

## 扩展对象

Extending Objects

在JavaScript代码中有一个很常见的操作，需要将一个对象中的属性拷贝到另外一个对象。

```js
// 示例
let target = {x: 1}, source = {y: 2, z: 3};
for(let key of Object.keys(source)) {
    target[key] = source[key];
}
target  // => {x: 1, y: 2, z: 3}
```

但因为这个是常用操作，各种JavaScript框架定义公用函数，经常将其命名为 `extend()` 来执行这个拷贝动作。最后，在ES6中，这个功能以 `Object.assign()` 的形式被添加到JavaScript核心语言中。

```js
// 有一个对象定义许多属性的默认值，希望将这些默认属性中不存在于目标对象中的属性复制到目标对象中
// 使用 Object.assign() 不会得到想要的结果
Object.assign(o, defaults);  // overwrites everything in o with defaults

// 想得到这个效果需要创建一个新的对象，将默认值拷贝到其中，然后用 o 的属性重写默认值中的属性
o = Object.assign({}, defaults, o);

// 可以用 … 展开操作符如下操作这个对象拷贝并重写
o = {...defaults, ...o};
```

<br/>
<br/>

## 序列化对象

Serializing Objects

对象序列化是指将对象的状态转换为字符串，也可将字符串还原为对象。函数 `JSON.stringify()` 和 `JSON.parse()` 用来序列化和还原对象。这些方法都是用JSON(JavaScript Object Notation)作为数据交换格式，JavaScript对象表示法，它的语法和JavaScript对象于数组字面量的语法非常接近。

```js
let o = {x: 1, y: {z: [false, null, ""]}}; // Define a test object
let s = JSON.stringify(o);   // s == '{"x":1,"y":{"z":[false,null,""]}}'
let p = JSON.parse(s);       // p == {x: 1, y: {z: [false, null, ""]}}
```

JSON的语法是JavaScript语法的子集，它并不能表示JavaScript里的所有值。

- 支持对象、数组、字符串、无穷大数字、ture、false和null，并且它们可以序列化和还原。
- NaN、Infinity和 -Infinity序列化结果是null。
- 日期对象序列化的结果是ISO格式的日期字符串，但还原却依然保留它们的字符串形态，而不会将它还原为原始日期对象。
- 函数、RegExp、Error对象和undefined只不能序列化和还原。
- `JSON.stringify()` 只能序列化对象可枚举的自有属性。对于一个不能序列化的属性来说，在序列化后的输出字符串中会将这个属性省略。

<br/>
<br/>

## 对象方法

Object Methods

介绍在 `Object.prototype` 上定义的少数通用的对象方法。

- `toString()`
- `toLocaleString()`
- `valueOf()`
- `toJSON()`

<br/>
<br/>

### toString方法

The toString() Method

此方法没有实参，它将返回一个表明调用这个方法的对象值的字符串。

```js
let s = { x: 1, y: 1 }.toString();  // s == "[object Object]"
```

<br/>
<br/>

### toLocaleString方法

The toLocaleString() Method

此方法返回一个表示这个对象的本地化字符串。

```js
let point = {
    x: 1000,
    y: 2000,
    toString: function() { return `(${this.x}, ${this.y})`; },
    toLocaleString: function() {
        return `(${this.x.toLocaleString()}, ${this.y.toLocaleString()})`;
    }
};
point.toString()        // => "(1000, 2000)"
point.toLocaleString()  // => "(1,000, 2,000)": note thousands separators
```

<br/>
<br/>

### valueOf方法

The valueOf() Method

当需要将对象转换为某种原始值而非字符串的时候才会调用它，尤其是转换为数字的时候。

```js
let point = {
    x: 3,
    y: 4,
    valueOf: function() { return Math.hypot(this.x, this.y); }
};
Number(point)  // => 5: valueOf() is used for conversions to numbers
point > 4      // => true
point > 5      // => false
point < 6      // => true
```

<br/>
<br/>

### toJSON方法

The toJSON() Method

对于需要执行序列化的对象来说，`JSON.stringify()` 方法会调用 `toJSON()` 方法。如果在待序列化的对象中存在这个方法，则调用它，返回值即是序列化的结果，而不是原始的对象。

```js
let point = {
    x: 1,
    y: 2,
    toString: function() { return `(${this.x}, ${this.y})`; },
    toJSON: function() { return this.toString(); }
};
JSON.stringify([point])   // => '["(1, 2)"]'
```

<br/>
<br/>

## 扩展对象字面量语法

Extended Object Literal Syntax

JavaScript的最新版本扩展了许多有用的对象字面量相关的语法。

<br/>
<br/>

### 简写属性

Shorthand Properties

假设值存储在变量x和y中，并且想要创建具有名为x和y的属性的对象，这些属性包含这些值。使用基本字面量语法，最终会重复每个标识两次。

在ES6之后，可以删除标识符的冒号和一个副本，最终使用更简单的代码。

```js
let x = 1, y = 2;
let o = {
    x: x,
    y: y
};

let x = 1, y = 2;
let o = { x, y };
o.x + o.y  // => 3
```

<br/>
<br/>

### 计算属性名称

Computed Property Names

有时需要创建具有特定属性的对象，但该属性的名称不是可以在源代码中键入的编译时常量。相反需要的属性名称存储在变量中，或者是调用的函数的返回值。不能对此类属性使用基本对象字面量。而必须创建一个对象，通过额外的属性，添加所需步骤。

使用称为计算属性的ES6特性设置这样的对象要简单得多，该功能允许从前面的代码写入方括号内并直接移动到对象字面量。

```js
const PROPERTY_NAME = "p1";
function computePropertyName() { return "p" + 2; }

let o = {};
o[PROPERTY_NAME] = 1;
o[computePropertyName()] = 2;

let p = {
    [PROPERTY_NAME]: 1,
    [computePropertyName()]: 2
};

p.p1 + p.p2 // => 3
```

<br/>
<br/>

### 符号作为属性名称

Symbols as Property Names

在ES6之后，属性名称可以是字符串或Symbol。如果将Symbol分配给变量或常量，则可以使用计算属性语法将该Symbol用作属性名称。

Symbol的要点不是安全性，而是为JavaScript对象定义一个安全的扩展机制。如果从第三方代码获取对象，你无法控制该对象，并且需要向该对象添加自己的一些属性，但希望确保属性不会与对象上可能存在的任何属性冲突，可以安全地使用Symbol作为属性名称。

```js
const extension = Symbol("my extension symbol");
let o = {
    [extension]: { /* extension data stored in this object */ }
};
o[extension].x = 0; // This won't conflict with other properties of o

```

<br/>
<br/>

### 展开运算符

Spread Operator

在ES2018之后，可以使用展开运算符 `...` 将现有的对象中的属性复制到新的对象中。但它并不是真正意义上的JavaScript运算符。它是一种特殊情况下语法，仅在对象文本中可用。

```js
let position = { x: 0, y: 0 };
let dimensions = { width: 100, height: 75 };
let rect = { ...position, ...dimensions };
rect.x + rect.y + rect.width + rect.height // => 175
```

如果展开的目标对象和源对象中具有相同的名称，则该属性的值将是位置处于后面的值。

```js
let o = { x: 1 };
let p = { x: 0, ...o };
p.x   // => 1: the value from object o overrides the initial value
let q = { ...o, x: 2 };
q.x   // => 2: the value 2 overrides the previous value from o.
```

展开运算符只展开对象的自有属性，不展开继承属性。

```js
let o = Object.create({x: 1}); // o inherits the property x
let p = { ...o };
p.x                            // => undefined
```

最后，请注意，虽然展开运算符在代码中只是三个小点，但它对JavaScript解释器来说可以代表大量的工作。如果对象具有n个属性，则将这些属性分散到另一个对象的过程很可能是 `O(n)` 操作。如果你发现自己使用 ... 在循环或递归函数中，类似将数据累积到一个大对象中的方法，你可能正在编写一个低效的 `O(n2)` 算法，该算法不会随着n变大而扩展。

<br/>
<br/>

### 速记方法

Shorthand Methods

当函数被定义为对象的属性时，我们称该函数为方法。

```js
let square = {
    area: function() { return this.side * this.side; },
    side: 10
};
square.area() // => 100
```

在ES6中，对象字面量语法已扩展成允许省略函数关键字和冒号的快捷方法。

```js
let square = {
    area() { return this.side * this.side; };
    side: 10
};
square.area() // => 100
```

<br/>
<br/>

### 属性的获取器和设置器

Property Getters and Setters

JavaScript还支持存取器属性，这些属性没有单个值，而是具有存取器方法:

- `getter`方法：返回值是属性存取表达式的值
- `setter`方法：设置一个存储器属性的值
- 如果属性同时有getter和setter方法，则它是一个可读写属性。
- 如果只含有getter方法，它是一个只读属性。
- 如果只含有setter方法，它是一个只可写属性（这对一个数据属性来说是不可能的）。如果尝试去读它，计算结果永远是undefined。

```js
let o = {
    // An ordinary data property
    dataProp: value,

    // An accessor property defined as a pair of functions.
    get accessorProp() { return this.dataProp; },
    set accessorProp(value) { this.dataProp = value; }
};
```

<br/>
<br/>

# 数组

Array

数组是值的有序集合。每个值叫做一个元素，每个元素有一个位置，以数字表示，称为索引。JavaScript数组是无类型的：数组元素可以是任意类型，并且同一个元素也可能有不同的类型。数组的元素甚至也可能是对象或其它数组，这允许创建复杂的数据结构。Javascript数组的索引是基于零的32位值。JavaScript数组是动态的：根据需要它们会增长或缩减，并且在创建数组时无需声明一个固定的大小，或者在数组大小变化时无需重新分配空间。JavaScript数组可能是稀疏的：数组元素的索引不一定要连续，它们之间可以有空缺。每个JavaScript数组都有一个length属性。针对非稀疏数组，该属性就是数组元素的个数。针对稀疏数组，length大于任何元素的最高索引。

数组继承自 `Array.prototype` 中的属性，它定义了一套丰富的数组操作方法。

ES6引入了一组新的数组类，这些类统称为 **类型化数组**，类型化数组有固定的长度和固定的数组元素类型。它们提供高性能和对二进制数据的字节级访问。

<br/>
<br/>

## 创建数组

Creating Arrays

有以下几种方式创建数组：

- 数组字面量(`[]`)
- 展开运算符(`...`)
- `Array()`构造函数
- `Array.of()`和`Array.from()`工厂方法

<br/>
<br/>

### 数组字面量

Array Literals

使用数组字面量是创建数组最简单的方法，在方括号中将数组元素用逗号隔开即可。

```js
let empty = [];                 // An array with no elements
let primes = [2, 3, 5, 7, 11];  // An array with 5 numeric elements
let misc = [ 1.1, true, "a", ]; // 3 elements of various types + trailing comma

let base = 1024;
let table = [base, base+1, base+2, base+3];

let b = [[1, {x: 1, y: 2}], [2, {x: 3, y: 4}]];

let count = [1,,3]; // Elements at indexes 0 and 2. No element at index 1
let undefs = [,,];  // An array with no elements but a length of 2
```

数组字面量语法允许可选的尾部逗号，所以 `[,,]` 的长度是2，不是3。

<br/>
<br/>

### 展开运算符

The Spread Operator

ES6之后，可以使用展开运算符(`...`)将一个数组中的元素展开在数组字面量中。

```js
let a = [1, 2, 3];
let b = [0, ...a, 4];  // b == [0, 1, 2, 3, 4]

// 展开运算符可以方便的创建一个数组的拷贝（浅拷贝）
let original = [1,2,3];
let copy = [...original];
copy[0] = 0;  // Modifying the copy does not change the original
original[0]   // => 1

// 展开运算符可以作用于任何可迭代对象
let digits = [..."0123456789ABCDEF"];
digits // => ["0","1","2","3","4","5","6","7","8","9","A","B","C","D","E","F"]

// Set 对象是可迭代对象，所以数组去重有一种简单的方法是用展开运算符将数组转换成 set 然后再转成数组
let letters = [..."hello world"];
[...new Set(letters)]  // => ["h","e","l","o"," ","w","r","d"]
```

<br/>
<br/>

### Array()构造函数

The Array() Constructor

另一种创建数组的方法是使用 `Array()` 构造函数。

```js
// 这个方法创建了一个没有元素的空数组，它等价于 [] 数组字面量
let a = new Array();

// 指定数组的长度
let a = new Array(10);

// 为数组显式指定多个元素
let a = new Array(5, 4, 3, 2, 1, "testing, testing");
```

<br/>
<br/>

### Array.of()

当 Array() 构造函数调用时有一个数值型实参，它会将实参作为数组的长度。但当调用时不止一个数值型实参时，它会将那些实参作为数组的元素创建。这意味着 Array() 构造函数不能创建只有一个数值型元素的数组。这意味着 Array() 构造函数不能创建只有一个数值型元素的数组。

在ES6中，`Array.of()` 函数修复了这个问题，它是一个将其实参值（无论有多少个实参）作为数组元素创建并返回一个新数组的工厂方法。

```js
Array.of()        // => []; returns empty array with no arguments
Array.of(10)      // => [10]; can create arrays with a single numeric argument
Array.of(1,2,3)   // => [1, 2, 3]
```

<br/>
<br/>

### Array.from()

Array.from 是ES6中另外一个数组工厂方法。它期望一个可迭代或类数组对象作为它的第一个实参，并返回一个包含对象中元素的新数组。

```js
let copy = Array.from(original);

```

<br/>
<br/>

## 读写数组元素

 Reading and Writing Array Elements

 使用方括号(`[]`)来访问数组中的一个元素。

 ```js
let a = ["world"];     // Start with a one-element array
let value = a[0];      // Read element 0
a[1] = 3.14;           // Write element 1
let i = 2;
a[i] = 3;              // Write element 2
a[i + 1] = "hello";    // Write element 3
a[a[i]] = a[0];        // Read elements 0 and 2, write element 3
 ```

 请记住，数组是对象的特殊形式。使用方括号访问数组元素就像用方括号访问对象的属性一样。

 ```js
let o = {};    // Create a plain object
o[1] = "one";  // Index it with an integer
o["1"]         // => "one"; numeric and string property names are the same
 ```

 注意，可以使用负数或非整数来索引数组。这种情况下，数值转换为字符串，字符串作为属性名来用。既然名字不是非负整数，它就只能当做常规的对象属性，而非数组的索引。同样，如果凑巧使用了是非负整数的字符串，它就当做数组索引，而非对象属性。当使用的一个浮点数和一个整数相等时情况也是一样的。

 ```js
a[-1.23] = true;  // This creates a property named "-1.23"
a["1000"] = 0;    // This the 1001st element of the array
a[1.000] = 1;     // Array index 1. Same as a[1] = 1;
 ```

 <br/>
 <br/>

## 稀疏数组

Sparse Arrays

稀疏数组就是包含从0开始的不连续索引的数组。通常，数组的length代表数组中元素的个数。如果数组是稀疏的，length值将大于元素的个数。

```js
let a = new Array(5); // No elements, but a.length is 5.
a = [];               // Create an array with no elements and length = 0.
a[1000] = 0;          // Assignment adds one element but sets length to 1001.
```

足够稀疏的数组通常在实现上比稠密的数组更慢 ，内存利用率更高。在这样的数组中查找元素的时间与常规对象属性的查找时间一样长。

<br/>
<br/>

## 数组长度

Array Length

每个数组都有一个length属性。

```js
[].length             // => 0: the array has no elements
["a","b","c"].length  // => 3: highest index is 2, length is 3

// 设置 length为一个小于当前长度的非负整数n时
// 当前数组中那些索引值大于或等于 n 的元素将从中删除
a = [1,2,3,4,5];     // Start with a 5-element array.
a.length = 3;        // a is now [1,2,3].
a.length = 0;        // Delete all elements.  a is [].
a.length = 5;        // Length is 5, but no elements, like new Array(5)
```

<br/>
<br/>

## 增减数组元素

Adding and Deleting Array Elements

> 注意，删除数组元素与为其赋 undefined 值是类似的。<br/>
> 对一个数组元素使用 delete 不会修改数组的 length 属性，也不会将元素从高索引处移下来填充已删除属性留下的空白。如果从数组中删除一个元素，它就变成稀疏数组。

```js
let a = [];      // Start with an empty array.
a[0] = "zero";   // And add elements to it.

// 使用push()方法在数组末尾压入元素
let a = [];           // Start with an empty array
a.push("zero");       // Add a value at the end.  a = ["zero"]
a.push("one", "two"); // Add two more values.  a = ["zero", "one", "two"]

// 使用delete运算符来删除数组元素
let a = [1,2,3];
delete a[2];   // a now has no element at index 2
2 in a         // => false: no array index 2 is defined
a.length       // => 3: delete does not affect array length
a              // => [1, 2, ]

// splice(index，howmany, itemx,...,itemX)通用方法来插入、删除或替换数组元素
// index: 必需。从何处开始添加/删除元素。
// howmany: 可选。删除多少元素。如果未设置，则删除 index 到数组结尾的所有元素
// item1,...,itemX: 可选。要添加到数组的新元素。
let b = [1, 2, 3, 4];
b.splice(2, 1)
b.length    // => 3
b           // => [1, 2, 4]
```

<br/>
<br/>

## 迭代数组

Iterating Arrays

在ES6中，最容易遍历数组元素的方法是for/of循环，它按照升序返回数组元素。对于稀疏数组它没有特殊的行为，数组中不存在的元素只是单纯的返回 undefined。

```js
let letters = [..."Hello world"];  // An array of letters
let string = "";
for(let letter of letters) {
    string += letter;
}
string  // => "Hello world"; we reassembled the original text
```

如果还需要知道元素索引，可以使用 `entries()` 方法。

```js
let everyother = "";
for(let [index, letter] of letters.entries()) {
    if (index % 2 === 0) everyother += letter;  // letters at even indexes
}
everyother  // => "Hlowrd"
```

在嵌套循环或其他性能至关重要的上下文中，有时可能会看到这样的数组遍历，以便数组长度仅被查一次，而不是在每次循环都去查询。以下两种形式都是符合习惯的 for 循环，虽然不是特别常用，而且对于现代 JavaScript 解释器，它们是否对性能有任何影响尚不清楚。

```js
// Save the array length into a local variable
for(let i = 0, len = letters.length; i < len; i++) {
    // loop body remains the same
}

// Iterate backwards from the end of the array to the start
for(let i = letters.length-1; i >= 0; i--) {
    // loop body remains the same
}
```

<br/>
<br/>

## 多维数组

Multidimensional Arrays

JavaScript不支持真正的多为数组，但可以用数组的数组来近似。访问数组的数组，使用两次方括号(`matrix[x][y]`)即可。

```js
// Create a multidimensional array
let table = new Array(10);               // 10 rows of the table
for(let i = 0; i < table.length; i++) {
    table[i] = new Array(10);            // Each row has 10 columns
}

// Initialize the array
for(let row = 0; row < table.length; row++) {
    for(let col = 0; col < table[row].length; col++) {
        table[row][col] = row*col;
    }
}

// Use the multidimensional array to compute 5*7
table[5][7]  // => 35
```

<br/>
<br/>

## 数组方法

Array Methods

由Array类定义的方法是最强大的。请记住，其中一些方法修改了调用的数组，而其中一些方法使数组保持不变。

- 迭代器方法(iterator methods)：循环遍历数组的元素
- 堆栈和队列方法(stack and queue methods)：在数组的开头和结尾添加和删除数组元素
- 子数组方法(subarray methods)：用于提取、删除、插入、填充、复制一个更大数组中相邻的区域
- 搜索和排序方法(searching and sorting methods)：用于查找和排序数组中的元素

<br/>
<br/>

### 数组迭代器方法

Array Iterator Methods

这些方法将数组按顺序传递到所指定的函数来遍历数组，它们提供了迭代、映射、筛选、测试和减少数组的便捷方法。

- `forEach()`方法
- `map()`方法
- `filter()`方法
- `find()`和`findIndex()`方法
- `every()`和`some()`方法
- `reduce()`和`reduceRight()`方法

<br/>
<br/>

#### forEach方法

`forEach()` 方法遍历数组，调用为每个元素指定的函数。

```js
let data = [1,2,3,4,5], sum = 0;
// Compute the sum of the elements of the array
data.forEach(value => { sum += value; });          // sum == 15

// Now increment each array element
data.forEach(function(v, i, a) { a[i] = v + 1; }); // data == [2,3,4,5,6]
```

请注意，`forEach()` 方法不提供在所有元素传递给函数之前终止迭代的方法。也就是说，没有等效于for循环的break语句可以使用。

<br/>
<br/>

#### map方法

`map()` 方法将调用数组的每个元素传递到指定的函数，并返回一个包含函数返回的值的数组。

它返回一个新数组，它不会修改调用它的数组。如果该数组是稀疏的，则不会为缺失的元素调用函数，但返回的数组将稀疏，其确实元素与原始数组的位置相同：它将具有相同的长度和相同的缺失元素。

```js
let a = [1, 2, 3];
a.map(x => x*x)   // => [1, 4, 9]: the function takes input x and returns x*x
```

<br/>
<br/>

#### filter方法

`filter()` 方法返回一个数组，其中包含调用该数组的数组元素的子集。传递给它的函数应该是断言：返回真或假的函数。

```js
let a = [5, 4, 3, 2, 1];
a.filter(x => x < 3)         // => [2, 1]; values less than 3
a.filter((x,i) => i%2 === 0) // => [5, 3, 1]; every other value
```

它跳过稀疏数组中丢失元素并且返回值也总是稠密的。

```js
// 缩小稀疏数组的间距
let dense = sparse.filter(() => true);

// 缩小间隙并移除 undefined 和 null 元素
a = a.filter(x => x !== undefined && x !== null);
```

<br/>
<br/>

#### find和findIndex方法

`find()` 和 `findIndex()` 方法，在数组中迭代，查找断言函数返回真实值的元素。但是它们在断言首次查找到元素时停止遍历。发生这种情况时，find返回匹配元素，findIndex返回索引。如果未找到，find返回undefined，而findIndex()返回 -1。

```js
let a = [1,2,3,4,5];
a.findIndex(x => x === 3)  // => 2; the value 3 appears at index 2
a.findIndex(x => x < 0)    // => -1; no negative numbers in the array
a.find(x => x % 5 === 0)   // => 5: this is a multiple of 5
a.find(x => x % 7 === 0)   // => undefined: no multiples of 7 in the array
```

<br/>
<br/>

#### every和some方法

`every()` 和 `some()` 方法是数组断言：它们将指定的断言函数应用于数组的元素，然后返回真或假。

`every()` 方法与数学全称量化符号 ∀ 相似：如果数组中所有元素执行断言函数返回值都为 true，则返回 true。

`some()` 方法与数学存在限定符 ∃ 相同：如果数组中存在至少有一个元素调用断言函数返回 true 的返回 true，仅在断言全部返回 false 时返回 false。

```js
let a = [1,2,3,4,5];
a.every(x => x < 10)      // => true: all values are < 10.
a.every(x => x % 2 === 0) // => false: not all values are even.

let a = [1,2,3,4,5];
a.some(x => x%2===0)  // => true; a has some even numbers.
a.some(isNaN)         // => false; a has no non-numbers.
```

请注意，every和some只要知道要返回的值，都停止对数组元素的遍历。

<br/>
<br/>

#### reduce和reduceRight方法

`reduce()` 和 `reduceRight()` 方法使用指定的函数将数组元素进行组合，生成单个值。这在函数式编程中是常见的操作，也可以称为注入(inject)和折叠(fold)。

在空数组上，不带初始值实参调用reduce将导致类型错误异常。

reduceRight 的工作原理和 reduce 一样，不同的是它按照数组索引从高到低(从右到左)处理数组，而不是从低到高。

```js
let a = [1,2,3,4,5];
a.reduce((x,y) => x+y, 0)          // => 15; the sum of the values
a.reduce((x,y) => x*y, 1)          // => 120; the product of the values
a.reduce((x,y) => (x > y) ? x : y) // => 5; the largest of the values

// Compute 2^(3^4).  Exponentiation has right-to-left precedence
let a = [2, 3, 4];
a.reduceRight((acc,val) => Math.pow(val,acc)) // => 2.4178516392292583e+24
```

<br/>
<br/>

### 使用flat和flatMap展平数组

Flattening arrays with flat() and flatMap()

在ES2019中，`flat()` 方法创建并返回一个新的数组，该数组包含与调用的数组相同的元素，只不过作为数组的任何元素都展平(flattened)到返回的数组中。

`flatMap()` 会将返回的数组自动展平。

```js
let a = [1, [2, [3, [4]]]];
a.flat(1)   // => [1, 2, [3, [4]]]
a.flat(2)   // => [1, 2, 3, [4]]
a.flat(3)   // => [1, 2, 3, 4]
a.flat(4)   // => [1, 2, 3, 4]

let phrases = ["hello world", "the definitive guide"];
let words = phrases.flatMap(phrase => phrase.split(" "));
words // => ["hello", "world", "the", "definitive", "guide"];
```

<br/>
<br/>

### 使用concat方法添加数组

Adding arrays with concat()

`concat()` 方法创建并返回一个新数组。如果这些实参中的任何一个自身是数组，则连接的是数组的元素，而非数组本身。请注意，此方法不会递归扁平化数组的数组。它也不会修改原始数组。

请注意，concat创建调用数组的新副本。在许多清情况下，这是正确的做法，但它是一个昂贵的操作。如果你发现自己编写代码像 `a = a.concat(x)`，那么你应该考虑使用 `push()` 或 `splice()` 修改数组，而不是创建新的数组。

```js
let a = [1,2,3];
a.concat(4, 5)          // => [1,2,3,4,5]
a.concat([4,5],[6,7])   // => [1,2,3,4,5,6,7]; arrays are flattened
a.concat(4, [5,[6,7]])  // => [1,2,3,4,5,[6,7]]; but not nested arrays
a                       // => [1,2,3]; the original array is unmodified
```

<br/>
<br/>

### 栈和队列方法

Stacks and Queues with push(), pop(), shift(), and unshift()

- `push()`方法：在数组的尾部添加一个或多个元素 ，并返回数组的新长度
- `pop()`方法：删除数组的最后一个元素，减少数组长度并返回它删除的值
- `shift()`方法：删除数组的第一个元素并将其返回，然后把所有随后的元素下移一个位置来填补数组头部的孔雀。
- `unshift()`方法：在数组头部添加一个或多个元素，并将已存在的元素移动到更高索引的位置来获得足够的空间，最后返回数组新的长度。

<br/>

push 和 pop 方法允许将数组当作栈来使用，两个方法都修改并替换原始数组。

```js
let stack = [];       // stack == []
stack.push(1,2);      // stack == [1,2];
stack.pop();          // stack == [1]; returns 2
stack.push(3);        // stack == [1,3]
stack.pop();          // stack == [1]; returns 3
stack.push([4,5]);    // stack == [1,[4,5]]
stack.pop()           // stack == [1]; returns [4,5]
stack.pop();          // stack == []; returns 1

// push方法不展平传入的数组
// 如果想要将数组元素全部压入另一个数组，可以使用展开运算符
a.push(...values);
```

<br/>

可以使用 unshift 和 shift 实现栈，但它比使用 push 和 pop 的效率低，因为每次在数组头部添加或删除元素时，都需要向上或向下移动数组元素。但是，你可以使用 push 在数组末尾添加元素并 shift 从数组的头部删除它们来实现队列。

```js
let q = [];            // q == []
q.push(1,2);           // q == [1,2]
q.shift();             // q == [2]; returns 1
q.push(3)              // q == [2, 3]
q.shift()              // q == [3]; returns 2
q.shift()              // q == []; returns 3
```

unshift 有一个特性，你可能会觉得令人惊讶。给它传递多个实参时，它们将一次全部插入，这意味着它们最终在数组中的顺序与一次插入一个实参的顺序时不通。

```js
let a = [];            // a == []
a.unshift(1)           // a == [1]
a.unshift(2)           // a == [2, 1]
a = [];                // a == []
a.unshift(1,2)         // a == [1, 2]
```

<br/>
<br/>

### 子数组方法

Subarrays with slice(), splice(), fill(), and copyWithin()

数组定义了许多在连续区域、子数组(subattay)或数组片段(slice)上工作的方法。

- `slice()`方法：返回指定数组的一个片段或子数组。
- `splice()`方法：在数组中插入或删除元素，会修改调用的数组。
- `fill()`方法：将数组或数组片段的元素填充为指定值。
- `copyWithin()`方法：将数组的一个片段复制到数组中的新位置。

<br/>

```js
let a = [1,2,3,4,5];
a.slice(0,3);    // Returns [1,2,3]
a.slice(3);      // Returns [4,5]
a.slice(1,-1);   // Returns [2,3,4]
a.slice(-3,-2);  // Returns [3]

let a = [1,2,3,4,5,6,7,8];
a.splice(4)    // => [5,6,7,8]; a is now [1,2,3,4]
a.splice(1,2)  // => [2,3]; a is now [1,4]
a.splice(1,1)  // => [4]; a is now [1]

let a = [1,2,3,4,5];
a.splice(2,0,"a","b")  // => []; a is now [1,2,"a","b",3,4,5]
a.splice(2,2,[1,2],3)  // => ["a","b"]; a is now [1,2,[1,2],3,3,4,5]

let a = new Array(5);   // Start with no elements and length 5
a.fill(0)               // => [0,0,0,0,0]; fill the array with zeros
a.fill(9, 1)            // => [0,9,9,9,9]; fill with 9 starting at index 1
a.fill(8, 2, -1)        // => [0,9,8,8,9]; fill with 8 at indexes 2, 3

let a = [1,2,3,4,5];
a.copyWithin(1)       // => [1,1,2,3,4]: copy array elements up one
a.copyWithin(2, 3, 5) // => [1,2,3,4,4]: copy last 2 elements to index 2
a.copyWithin(0, -2)   // => [4,4,3,4,4]: negative offsets work, too
```

<br/>
<br/>

### 数组搜索和排序方法

Array Searching and Sorting Methods

方法：

- `indexOf()` 方法：从数组中从头到尾搜索具有指定值的元素，并返回找到的第一个元素的索引，如果未找到，则返回-1。
- `lastIndexOf()` 方法：从数组中从尾到头搜索具有指定值的元素，并返回找到的第一个元素的索引，如果未找到，则返回-1。
- `include()` 方法：如果数组包含该值返回 true 否则 false。
- `sort()` 方法：对数组的元素直接进行排序，并返回排序后的数组。如果数组中包含 undefined 元素，它们会被放在数组的结尾。
- `reverse()` 方法：反转数组中元素的顺序并返回反转后的数组。

<br/>

```js
let a = [0,1,2,1,0];
a.indexOf(1)       // => 1: a[1] is 1
a.lastIndexOf(1)   // => 3: a[3] is 1
a.indexOf(3)       // => -1: no element has value 3

let a = [1,true,3,NaN];
a.includes(true)            // => true
a.includes(2)               // => false
a.includes(NaN)             // => true
a.indexOf(NaN)              // => -1; indexOf can't find NaN

let a = ["banana", "cherry", "apple"];
a.sort(); // a == ["apple", "banana", "cherry"]

let a = [1,2,3];
a.reverse();   // a == [3,2,1]
```

<br/>
<br/>

### 数组到字符串的转换

Array to String Conversions

数组类定义了三个方法将数组转化为字符串：

- `join()` 方法：将数组所有与乃是转换为字符串并连接它们，返回生成的字符串。可指定一个可选的分隔符。
- `toString()` 方法
- `toLocaleString()` 方法

```js
let a = [1, 2, 3];
a.join()               // => "1,2,3"
a.join(" ")            // => "1 2 3"
a.join("")             // => "123"
let b = new Array(10); // An array of length 10 with no elements
b.join("-")            // => "---------": a string of 9 hyphens

[1,2,3].toString()          // => "1,2,3"
["a", "b", "c"].toString()  // => "a,b,c"
[1, [2,"c"]].toString()     // => "1,2,c"
```

<br/>
<br/>

### 静态数组函数

Static Array Functions

数组类还定义了三个静态函数，可以通过数组构造函数而不是数组调用。

- `Array.of()`：创建新数组的工厂方法
- `Array.from()`：创建新数组的工厂方法
- `Array.isArray()`：判断之歌未知值是否是数组

<br/>
<br/>

## 类数组对象

Array-Like Objects

一种常常完全合理的看法是把拥有一个数值型length属性和对应非负整数属性的对象看作数组的同类。

<br/>
<br/>

## 字符串作为数组

Strings as Arrays

JavaScript字符串的行为类似于 UTF-16 Unicode字符的只读数组。可以使用方括号替代 `charAt()` 访问单个字符。

```js
let s = "test";
s.charAt(0)    // => "t"
s[1]           // => "e"
```

当然，字符串使用 `typeof` 运算符仍然返回string，如果将字符串传递给 `Array.isArray()` 方法，则返回 false。

请记住，字符串是不可变值，因此当字符串被视为数组时，它们是只读数组。数组方法push, sort, reverse, splice直接修改数组，它们不能处理字符串。但是，尝试使用数组方法修改字符串不会引发异常：它只是静默失败。

<br/>

---

<br/>

# 函数

Functions

函数是一个JavaScript代码块，只定义一次，但可以执行或调用任意次数。除了参数之外，每次函数调用都有另外一个值——调用上下文——即 `this` 关键字。

如果函数挂载在一个对象上作为其属性，它就被称为方法。当该方法被调用时，该对象就是该方法函数的上下文或this值。

用于初始化新创建的对象的函数称为构造函数。

JavaScript函数可以嵌套在其他函数中定义，并且它们可以访问定义它们所处的作用域内任何变量。这意味着JavaScript函数是闭包，支持闭包非常重要，它是非常强大的编程技巧。

<br/>
<br/>

## 函数定义

Defining Functions

最直接的方法是使用 `function` 关键字，它既可以用作声明也可以用作表达式。

ES6定义了 **箭头函数**，它具有特别简洁语法，并且在将一个函数作为参数传递给另一个函数的场景中非常使用。

函数也可以用 `Function()` 构造函数来定义。此外，JavaScript还定义了一些特殊类型的函数。`function *` 定义函数生成器，`async function` 定义异步函数。

<br/>
<br/>

### 函数声明

Function Declarations

栗子:

```js
// Print the name and value of each property of o.  Return undefined.
function printprops(o) {
    for(let p in o) {
        console.log(`${p}: ${o[p]}\n`);
    }
}

// A recursive function (one that calls itself) that computes factorials
// Recall that x! is the product of x and all positive integers less than it.
function factorial(x) {
    if (x <= 1) return 1;
    return x * factorial(x-1);
}
```

在ES6之前，函数只允许在JavaScript文件顶层或者其他函数中声明。然而一些实现违反规约，在循环体条件体或者其他块中定义函数。在ES6严格模式下，函数运行在块内进行声明。一个定义在块内的函数只存在于该块内，块外是不可见的。

<br/>
<br/>

### 函数表达式

Function Expressions

函数表达式出现在一个它的上层表达式或语句的上下文中，并且函数名称是可选项。

```js
// This function expression defines a function that squares its argument.
// Note that we assign it to a variable
const square = function(x) { return x*x; };

// Function expressions can include names, which is useful for recursion.
const f = function fact(x) { if (x <= 1) return 1; else return x*fact(x-1); };

// Function expressions can also be used as arguments to other functions
[3, 2, 1].sort(function(a, b) { return a-b; });

// Function expressions are sometimes defined and immediately invoked
let tensquared = (function(x) {return x*x;}(10));
```

注意函数名称在函数表达式中式可选项，在大部分函数表达式中我们省略了它。函数声明实际上声明了一个变量，并且将函数对象赋值给它。按照这个角度来看，函数表达式没有声明一个变量：可以根据它是否会多次调用由你自己决定是将新定义的函数对象赋值给一个常量还是变量。

用const定义函数表达式是一个非常好的做法，你不会因为意外赋值而重写了你的函数。

如果一个函数表达式包含一个名称，那这个函数的局部函数作用域内会包含一个属性名为该属性名的对象，其值绑定的是该函数。实际上，函数名变成这个函数的一个局部变量。大多数函数表达式不需要函数名称，这让它们的定义更简洁（但并没有箭头函数简洁）。

在函数声明和函数表达式之间有一个非常重要的不同。当你用函数声明，该函数对象创建于该函数所在的作用域的代码开始执行之前，也就是声明提前，所以可以在函数定义之前调用他们。如果用函数表达式来定义一个函数，这样使用就是不对的：该函数不会存在，直到函数定义表达式真正被计算。因为，想要执行一个函数，必须可以引用它，而一个函数表达式定义的函数一直到该函数赋值给一个变量后才能被引用，所以要使用函数表达式需要在函数被调用之前定义。

<br/>
<br/>

### 箭头函数

Arrow Functions

在ES6之后，可以用一个特别简洁的语法来定义函数，被称为 **箭头函数**。由于箭头函数是表达式，而不是声明语句，也不需要一个函数名称。

箭头函数支持更简洁的语法。如果函数体只有一个简单的 return 语句，则可以省略 return 关键字，分号和花括号一起省略，将函数体写成一个计算返回值的表达式。

而且，如果一个箭头函数只有一个参数，可以省略参数列表的圆括号。

注意，如果箭头函数没有参数，则必须写一对空的圆括号。

```js
// (参数) => { 函数体 }
const sum = (x, y) => { return x + y; };

const sum = (x, y) => x + y;

const polynomial = x => x*x + 2*x + 3;

const constantFunc = () => 42;
```

此外，如果箭头函数是一个单一的 return 语句，而且它返回的是一个对象字面量，那必须将对象字面量用圆括号包起来，避免将对象字面量的大括号误解成函数体的大括号。

```js
// Good
const f = x => { return { value: x }; };  // Good: f() returns an object
const g = x => ({ value: x });            // Good: g() returns an object
// Bad
const h = x => { value: x };              // Bad: h() returns nothing
const i = x => { v: x, w: x };            // Bad: Syntax Error
```

箭头函数可以完美的传递一个函数给另一个函数，比如一些数组的常规操作方法。

```js
// Make a copy of an array with null elements removed.
let filtered = [1,null,2,3].filter(x => x !== null); // filtered == [1,2,3]
// Square some numbers:
let squares = [1,2,3,4].map(x => x*x);               // squares == [1,4,9,16]
```

箭头函数不同于用关键字定义的函数：箭头函数从定义它们的环境继承 this 关键字，而不是像其他定义方式那样定义自己的调用上下文。这是箭头函数一个重要且特别实用的特性。箭头函数也不同于其他函数，它们没有原型属性。这意味着它不能被当作一个构造函数去创建一个类。

<br/>
<br/>

### 嵌套函数

Nested Functions

在JavaScript中，函数可以嵌套在其他函数内。

嵌套函数的有趣之处在于它的变量作用域规则：它们可以访问嵌套它们（或多重嵌套）的函数的参数和变量。

```js
// 内部函数 square()可以读写外部函数 hypotenuse()定义的参数a和b
function hypotenuse(a, b) {
    function square(x) { return x*x; }
    return Math.sqrt(square(a) + square(b));
}
```

<br/>
<br/>

## 调用函数

Invoking Functions

构成函数主体的JavaScript代码在定义之时并不会执行，只有调用该函数时，它们才会执行。JavaScript函数可以以物种方式被调用：

- 作为函数(fcuntion)
- 作为方法(method)
- 作为构造函数(constructor)
- 通过 `call()` 和 `apply()` 方法间接调用
- 隐式调用

<br/>
<br/>

### 函数调用

Function Invocation

函数或方法通过调用表达式被调用。

```js
printprops({x: 1});
let total = distance(0,0,2,1) + distance(2,1,3,5);
let probability = factorial(5)/factorial(13);
```

<br/>

**有条件的调用**(CONDITIONAL INVOCATION)

在ES2020中可通过 `?.` 符号，使函数只有在不为null和undefined的时候再调用。

```js
// 表达式 f?.(x) 等价于
(f !== null && f !== undefined) ? f(x) : undefined
```

函数调用在非严格模式下，调用上下文(this)是全局对象。然而在严格模式下，调用上下文是undefined。注意箭头语法定义的函数行为是不同的：实际上它们总是继承它们定义位置的this值。

以函数形式调用的函数通常不使用this关键字。不过，this关键字可以用来判断当前是否是严格模式。

```js
// Define and invoke a function to determine if we're in strict mode.
const strict = (function() { return !this; }());
```

<br/>

**递归函数和栈**(RECURSIVE FUNCTIONS AND THE STACK)

递归函数只调用自己。在写递归函数时，考虑内存分配是很重要的。当函数A调用函数B，然后函数B调用函数C，JavaScript编译器需要知道在哪里重新执行函数B，当函数B执行完成后它需要知道在哪里执行函数A。你可以将执行上下文想象成一个栈，当一个函数调用另一个函数时，一个新的执行上下文被压入栈中，当被调用函数返回，它的执行上下文对象从栈中弹出。如果一个函数递归调用100次，那么会有100个对象被压入栈中，然后这100个对象再一次从栈中弹出。这种调用非常耗内存。以现代的硬件递归调用100次通常没什么问题。但是如果一个函数递归上千次，它可能会失败并报错"Maximum call-stack size exceeded"。

<br/>
<br/>

### 方法调用

Method Invocation

方法只不过是对象属性函数。

```js
// 对象的方法
object.method = function;
```

方法调用和函数调用有一个重要的区别，即调用上下文。属性访问表达式由两部分组成：一个对象和属性名称。对象成为调用上下文，函数体可以使用关键字this引用该对象。

```js
let calculator = { // An object literal
    operand1: 1,
    operand2: 1,
    add() {        // We're using method shorthand syntax for this function
        // Note the use of the this keyword to refer to the containing object.
        this.result = this.operand1 + this.operand2;
    }
};
calculator.add();  // A method invocation to compute 1+1.
calculator.result  // => 2
```

方法和this关键字是面向对象编程范式的核心。任何函数只要作为方法调用实际上都会传入一个隐式的实参对象，就是调用这个方法对象本身。

<br/>

**方法链**(METHOD CHAINING)

当方法返回一个对象，这个对象还可以再调用它的方法。

当方法并不需要返回值时，最好直接返回this。需要注意，this是一个关键字，不是变量，也不是属性名。JavaScript语法不允许给this赋值。

关键字this没有变量作用域的限制，除了箭头函数，嵌套函数不会从包含它的函数中继承this。如果嵌套函数作为方法调用，其this的值指向调用它的对象。如果嵌套函数作为函数调用（不包含箭头函数），其this值不是全局对象（非严格模式下）就是undefined（严格模式下）。

```js
let o = {                 // An object o.
    m: function() {       // Method m of the object.
        let self = this;  // Save the "this" value in a variable.
        this === o        // => true: "this" is the object o.
        f();              // Now call the helper function f().

        function f() {    // A nested function f
            this === o    // => false: "this" is global or undefined
            self === o    // => true: self is the outer "this" value.
        }
    }
};
o.m();                    // Invoke the method m on the object o.
```

<br/>
<br/>

### 构造函数调用

Constructor Invocation

如果函数或方法调用之前带有关键字new，它就构成构造函数调用。

如果构造函数没有形参，JavaScript的语法允许省略实参列表和圆括号。

```js
// 以下两者等价
o = new Object();
o = new Object;
```

构造函数调用创建一个空的新对象，这个对象继承自构造函数的prototype属性。构造函数试图初始化这个新创建的对象，并将这个对象用作其调用上下文，因此构造函数可以使用 this 关键字来引用这个新创建的对象。

构造函数通常不使用 return 关键字，它们通常初始化新对象，当构造函数的函数体执行完毕时，它会隐式返回。在这种情况下，构造函数调用表达式的计算结果就是这个新对象的值。然而如果构造函数显式地使用 return 语句返回一个对象，那么调用表达式的值就是这个对象。如果 return 语句没有指定返回值，或者返回一个原始值，那么这时将忽略返回值，同时使用这个新对象作为调用结果。

<br/>
<br/>

### 间接调用

Indirect Invocation

JavaScript中的函数也是对象，和其它JavaScript对象没什么两样，函数对象也可以包含方法。其中的两个方法 `apply()` 和 `call()` 可以用来间接地调用函数。两个方法都允许显式指定调用所需的 this 值，也就是说，任何函数都可以作为任何对象的方法来调用，哪怕这个函数不是那个对象的方法。

<br/>
<br/>

### 隐式函数调用

Implicit Function Invocation

有各式各样的JavaScript语言特性，它们看起来不像函数调用但是却能调用函数。

<br/>
<br/>

## 函数参数

Function Arguments and Parameters

函数实际参数(args)和形式参数(pars)。

JavaScript中的函数定义并未指定函数形参的类型，函数调用也未对传入的实际值做任何类型检查。

<br/>

### 可选参数和默认值

Optional Parameters and Defaults

当调用参数的时候传入的实参比函数申明指定的形参个数要少，剩下的形参都将设置为 undefined 值。所以一些参数设置成可选的是非常实用的。

需要注意，当使用可选参数时，需要将可选参数放在实参列表的最后。

在ES6之后，可以直接在函数的参数列表中为每个函数参数定义默认值。

```js
// Append the names of the enumerable properties of object o to the
// array a, and return a.  If a is omitted, create and return a new array.
function getPropertyNames(o, a) {
    // if (a === undefined) a = [];  // If undefined, use a new array
    // 以下习惯用法
    a = a || [];
    for(let property in o) a.push(property);
    return a;
}

// getPropertyNames() can be invoked with one or two arguments:
let o = {x: 1}, p = {y: 2, z: 3};  // Two objects for testing
let a = getPropertyNames(o); // a == ["x"]; get o's properties in a new array
getPropertyNames(p, a);      // a == ["x","y","z"]; add p's properties to it

// ES6之后的语法
function getPropertyNames(o, a = []) {
    for(let property in o) a.push(property);
    return a;
}

// This function returns an object representing a rectangle's dimensions.
// If only width is supplied, make it twice as high as it is wide.
const rectangle = (width, height=width*2) => ({width, height});
rectangle(1)  // => { width: 1, height: 2 }
```

默认参数表达式只有在函数调用时进行计算，而不是在它定义时。

<br/>
<br/>

### 剩余参数和可变长度参数列表

Rest Parameters and Variable-Length Argument Lists

调用函数时允许传入的实参比函数声明时指定的形参个数少。剩余参数允许相反的情况：它允许我们在调用函数时，传入比形参多任意个数的实参。

剩余参数由三个点(`...`)开始，必须是函数声明的最后一个参数。在一个函数体中，剩余参数的值总是一个数组。这个数组可能是空的，但是剩余参数永远不会是 undefined。（因此，从不会给剩余参数设置默认值，这是不合法的。）

类似这种函数可以接收任意个数的实参，这种函数也称为 **不定实参函数** ，这个术语原子古老的C语言。

不要混淆 `...剩余参数` 和 `...` 展开运算符。

```js
function max(first=-Infinity, ...rest) {
    let maxValue = first; // Start by assuming the first arg is biggest
    // Then loop through the rest of the arguments, looking for bigger
    for(let n of rest) {
        if (n > maxValue) {
            maxValue = n;
        }
    }
    // Return the biggest
    return maxValue;
}

max(1, 10, 100, 2, 3, 1000, 4, 5, 6)  // => 1000
```

<br/>
<br/>

### 参数对象

The Arguments Object

剩余参数是在ES6中加入的，在此之前，不定实参函数是用参数对象实现的。在严格模式下，`arguments` 被视为保留字，不能声明具有该名称的局部变量来定义函数的参数。

```js
function max(x) {
    let maxValue = -Infinity;
    // Loop through the arguments, looking for, and remembering, the biggest.
    for(let i = 0; i < arguments.length; i++) {
        if (arguments[i] > maxValue) maxValue = arguments[i];
    }
    // Return the biggest
    return maxValue;
}

max(1, 10, 100, 2, 3, 1000, 4, 5, 6)  // => 1000
```

<br/>
<br/>

### 函数调用的扩展运算符

The Spread Operator for Function Calls

当需要单个值时，展开运算符(`...`)用来拆包，或者说将元素从数组（或其它可迭代对象）中展开到上下文。

```js
let numbers = [5, 2, 10, -1, 9, 100, 1]
Math.min(...numbers) // => -1
```

在函数定义和函数调用中使用相同的 ... 语法时，和展开运算符有着相仿的效果。

```js
// This function takes a function and returns a wrapped version
function timed(f) {
    return function(...args) {  // Collect args into a rest parameter array
        console.log(`Entering function ${f.name}`);
        let startTime = Date.now();
        try {
            // Pass all of our arguments to the wrapped function
            return f(...args);  // Spread the args back out again
        }
        finally {
            // Before we return the wrapped return value, print elapsed time.
            console.log(`Exiting ${f.name} after ${Date.now()-startTime}ms`);
        }
    };
}

// Compute the sum of the numbers between 1 and n by brute force
function benchmark(n) {
    let sum = 0;
    for(let i = 1; i <= n; i++) sum += i;
    return sum;
}

// Now invoke the timed version of that test function
timed(benchmark)(1000000) // => 500000500000; this is the sum of the numbers
```

<br/>
<br/>

### 将函数实参结构为形参

Destructuring Function Arguments into Parameters

用实参列表调用函数时，实参的值最终赋值给函数定义的参数。函数调用初始化阶段非常像变量赋值。

```js
// 如果一个函数的参数带有方括号，就说明要给每一个方括号传一个数组。
// 比如矢量2D
function vectorAdd(v1, v2) {
    return [v1[0] + v2[0], v1[1] + v2[1]];
}
vectorAdd([1,2], [3,4])  // => [4,6]

// 结构这两个矢量实参
function vectorAdd([x1,y1], [x2,y2]) { // Unpack 2 arguments into 4 parameters
    return [x1 + x2, y1 + y2];
}
vectorAdd([1,2], [3,4])  // => [4,6]

// 将一个简单的对象参数解构成两个参数
function vectorAdd(
    {x: x1, y: y1}, // Unpack 1st object into x1 and y1 params
    {x: x2, y: y2}  // Unpack 2nd object into x2 and y2 params
)
{
    return { x: x1 + x2, y: y1 + y2 };
}
vectorAdd({x: 1, y: 2}, {x: 3, y: 4})  // => {x: 4, y: 6}

// 2D或3D矢量的乘法
// Multiply the vector {x,y} or {x,y,z} by a scalar value
function vectorMultiply({x, y, z=0}, scalar) {
    return { x: x*scalar, y: y*scalar, z: z*scalar };
}
vectorMultiply({x: 1, y: 2}, 2)  // => {x: 2, y: 4, z: 0}
```

<br/>

一些语言（如Python），允许函数的调用以 `name=value` 的形式指定实参。JavaScript不允许直接这样做，但可以通过解构对象实参到函数参数中。构思一个函数将指定数量的元素从一个数组复制到另一个数组中，可以随意地为每个数组指定起始偏移量。

```js
function arraycopy({from, to=from, n=from.length, fromIndex=0, toIndex=0}) {
    let valuesToCopy = from.slice(fromIndex, fromIndex + n);
    to.splice(toIndex, 0, ...valuesToCopy);
    return to;
}
let a = [1,2,3,4,5], b = [9,8,7,6,5];
arraycopy({from: a, n: 3, to: b, toIndex: 4}) // => [9,8,7,6,1,2,3,5]
```

当解构一个数组，在其被拆包时，可以定义一个剩余参数将其余值放在数组中。

```js
// This function expects an array argument. The first two elements of that
// array are unpacked into the x and y parameters. Any remaining elements
// are stored in the coords array. And any arguments after the first array
// are packed into the rest array.
function f([x, y, ...coords], ...rest) {
    return [x+y, ...rest, ...coords];  // Note: spread operator here
}
f([1, 2, 3, 4], 5, 6)   // => [3, 5, 6, 3, 4]
```

最后，请记住，除了可以解构实参对象和数组，也可以解构数组对象，对象有数组属性，并且对象还有对象的属性。

<br/>
<br/>

### 参数类型

Argument Types

JavaScript方法的形参并未声明类型，在形参传入函数体之前也未作任何类型检查。

JavaScript在必要时会进行类型转换。

你应当添加类似的实参类型检查逻辑。因为宁愿程序在传入非法值时宝座，也不愿非法值导致程序在执行时报错。相比而言，逻辑执行时的报错消息不甚清晰且更难处理。

```js
// Return the sum of the elements an iterable object a.
// The elements of a must all be numbers.
function sum(a) {
    let total = 0;
    for(let element of a) { // Throws TypeError if a is not iterable
        if (typeof element !== "number") {
            throw new TypeError("sum(): elements must be numbers");
        }
        total += element;
    }
    return total;
}
sum([1,2,3])    // => 6
sum(1, 2, 3);   // !TypeError: 1 is not iterable
```

<br/>
<br/>

## 函数作为值

Functions as Values

可以将函数赋值给变量，存储在对象的属性或数组的元素中，作为参数传入另一个函数。

```js
function square(x) { return x*x; }

let s = square;  // Now s refers to the same function that square does
square(4)        // => 16
s(4)             // => 16

let o = {square: function(x) { return x*x; }}; // An object literal
let y = o.square(16);                          // y == 256

let a = [x => x*x, 20]; // An array literal
a[0](a[1])              // => 400
```

<br/>
<br/>

### 定义自己的函数属性

Defining Your Own Function Properties

JavaScript中的函数并不是原始值，而是一种特殊的对象，函数可以拥有属性。当函数需要一个静态变量来在调用时保持某个值不变，最方便的方式就是给函数定义属性，而不是定义全部变量，显然定义全局变量会让命名空间变得更加杂乱无章。

可以将这些信息放到全局变量中，但这并不是必须的，因为这个信息仅仅是函数本身用到的。最好将这个信息保存到函数对象的一个属性中。

```js
// 此示例，每次调用函数都会返回一个唯一的整数
// Initialize the counter property of the function object.
// Function declarations are hoisted so we really can
// do this assignment before the function declaration.
uniqueInteger.counter = 0;

// This function returns a different integer each time it is called.
// It uses a property of itself to remember the next value to be returned.
function uniqueInteger() {
    return uniqueInteger.counter++;  // Return and increment counter property
}
uniqueInteger()  // => 0
uniqueInteger()  // => 1
```

<br/>
<br/>

## 函数作为命名空间

Functions as Namespaces

变量声明在函数内对于函数体外是不可见的。因此，有时定义函数作为临时命名空间非常有用，可以在其中定义变量而不弄乱全局命名空间。

将代码放入一个函数内，然后调用这个函数，这样全局变量就变成了函数内的局部变量。

```js
function chunkNamespace() {
    // Chunk of code goes here
    // Any variables defined in the chunk are local to this function
    // instead of cluttering up the global namespace.
}
chunkNamespace();  // But don't forget to invoke the function!

// 可以用一个单独的表达式定义一个匿名函数并调用它
(function() {  // chunkNamespace() function rewritten as an unnamed expression.
    // Chunk of code goes here
}());          // End the function literal and invoke it now.
```

定义匿名函数并立即在单个表达式中调用它的写法很常见，并给它起了个名字 **匿名调用函数表达式**。

函数用作命名空间很常用，在命名空间函数中定义一个或多个函数使用其中的变量，然后将他们作为函数命名空间的返回值。这样的函数称为闭包。

<br/>
<br/>

## 闭包

Closures

和其他大多数现代编程语言一样，JavaScript也采用词法作用域。也就是说，函数的执行依赖于变量作用域，这个作用域是在函数定义时决定的，而不是函数调用时决定的。为了实现这种词法作用域，JavaScript函数对象的内部状态不仅包含函数的代码逻辑，还必须包括对函数定义出现的作用域的引用。将函数对象和作用域相互关联起来，函数体内部的变量都可以保存在函数作用域内，这种特性在计算机科学文献中称为闭包。

从技术的角度将，所有的JavaScript函数都是闭包，但是大多数函数调用和定义在同一个作用域内，通常不会注意到这里有涉及到闭包。当调用函数不和其他定义处于同一作用域内时，事情变得非常微妙。当一个函数嵌套了另一个函数，外部函数将嵌套的函数对象作为返回值返回的时候往往会发生这种事情。

理解闭包首先要了解嵌套函数的词法作用域规则。

示例1：checkscope() 函数声明了一个局部变量，然后定义并执行了一个函数f()。函数f() 返回了这个变量的值，最后将函数f()的执行结果返回。

```js
let scope = "global scope";          // A global variable
function checkscope() {
    let scope = "local scope";       // A local variable
    function f() { return scope; }   // Return the value in scope here
    return f();
}
checkscope()                         // => "local scope"
```

示例2：将函数内的一对圆括号移动到了 checkscope() 之后，checkscope() 现在仅仅返回函数内嵌套的一个函数对象，而不是直接返回结果。

```js
let scope = "global scope";          // A global variable
function checkscope() {
    let scope = "local scope";       // A local variable
    function f() { return scope; }   // Return the value in scope here
    return f;
}
let s = checkscope()();              // What does this return?
```

JavaScript函数的执行用到了作用域，这个作用域时函数定义的时候创建的。嵌套的函数 f() 定义在变量 scope 绑定的值("local scope")的作用域里，这个绑定无论f函数在何处调用依然有效。因此上例最后返回"local scope"。简而言之，闭包这个特性强大到让人吃惊：它们可以捕捉到它们的外部函数所绑定的局部变量和参数。

书写闭包的时候，还需要注意一件事情，this 是JavaScript的关键字，而不是变量。箭头函数从包含它们的函数中继承this值，但是用function关键字定义的函数不是。所以，如果写一个闭包需要使用包含它的函数的this值，要在闭包返回之前使用箭头函数或者调用bind()，或将this值赋值给一个变量，这样你的闭包才会继承它。

```js
const self = this;  // Make the this value available to nested functions
```

<br/>
<br/>

## 函数属性，方法和构造器

Function Properties, Methods, and Constructor

JavaScript程序中，函数既是值也是对象，也可以拥有属性和方法。

函数：

- 属性
  - `length`
  - `name`
  - `protorype`
- 方法
  - `call()`
  - `apply()`
  - `bind()`
  - `toString()`
- 构造函数 `Function()`

<br/>

### 属性length

The length Property

函数的只读属性length，指定函数参数个数-声明在其参数列表中的参数个数，大多数函数期望的实参个数。

<br/>
<br/>

### 属性name

The name Property

如果使用名称定义函数，只读属性 name 指定函数定义时用的名称，或未命名函数表达式在首次创建时分配给变量或属性的名称。此属性在编写调试或错误消息时很有用。

<br/>
<br/>

### 属性prototype

The prototype Property

prototype 属性是指向一个对象的引用，这个对象称作原型对象。每一个函数都包含不同的原型对象。当将函数用作构造函数的时候，新创建的对象会从原型对象上继承属性。

<br/>
<br/>

### 方法call和apply

The call() and apply() Methods

可以将 `call()` 和 `apply()` 看做是某个对象的方法，通过调用方法的形式来间接调用函数。

```js
f.call(o);
f.apply(o);

o.m = f;     // Make f a temporary method of o.
o.m();       // Invoke it, passing no arguments.
delete o.m;  // Remove the temporary method.
```

不要忘了，箭头函数从它定义的位置的上下文继承this值。这不能被 call 和 apply 方法重写。如果通过箭头函数调用它两任何一个方法，第一个实参实际上都被忽略。

<br/>
<br/>

### 方法bind

The bind() Method

`bind()` 方法的主要作用是将函数绑定至某个对象。

```js
function f(y) { return this.x + y; } // This function needs to be bound
let o = { x: 1 };                    // An object we'll bind to
let g = f.bind(o);                   // Calling g(x) invokes f() on o
g(2)                                 // => 3
let p = { x: 10, g };                // Invoke g() as a method of this object
p.g(2)                               // => 3: g is still bound to o, not p.
```

箭头函数从它们定义的上下文中继承 this 值，并且其不可被 `bind()` 方法重写，如果上面的代码用箭头函数定义函数f()，这个绑定不会生效。

但是 `bind()` 方法不仅仅是将函数绑定至一个对象。它还附带了一些其他应用：除了第一个实参之外，传入bind的实参也会绑定至 this 值。

```js
let sum = (x,y) => x + y;      // Return the sum of 2 args
let succ = sum.bind(null, 1);  // Bind the first argument to 1
succ(2)  // => 3: x is bound to 1, and we pass 2 for the y argument

function f(y,z) { return this.x + y + z; }
let g = f.bind({x: 1}, 2);     // Bind this and y
g(3)     // => 6: this.x is bound to 1, y is bound to 2 and z is 3
```

bind()返回函数的名称属性是调用bind()的函数的名称属性前面加上前缀为单词bound。

<br/>
<br/>

### 方法toString

The toString() Method

实际上，大多数（非全部）的 `toString()` 方法的实现都返回函数的完整源码。

<br/>
<br/>

### 构造函数Function

The Function() Constructor

因为函数是对象，有一个 `Function()` 构造函数可用来创建新的对象。它并不需要通过传入实参以指定函数名，它创建一个匿名函数。

```js
const f = new Function("x", "y", "return x*y;");

// 等价
const f = function(x, y) { return x*y; };
```

关于 `Function()` 构造函数有几点需要注意：

- `Function()` 构造函数允许JavaScript在运行时动态地创建并编译函数。
- 每次调用 `Function()` 构造函数都会解析函数体，并创建新的函数对象。如果是在一个循环或多次调用的函数中执行这个构造函数，执行效率会受影响。相比之下，循环中的嵌套函数和函数定义表达式则不会每次执行时都重新编译。
- `Function()` 构造函数所创建的函数并不是使用词法作用域，相反，函数体代码的编译类似顶层函数。

```js
let scope = "global";
function constructFunction() {
    let scope = "local";
    return new Function("return scope");  // Doesn't capture local scope!
}
// This line returns "global" because the function returned by the
// Function() constructor does not use the local scope.
constructFunction()()  // => "global"
```

<br/>
<br/>

## 函数式编程

Functional Programming

和Lisp、Haskell不同，JavaScript并非函数式编程语言，但在JavaScript中可以像操作对象一样操控函数，也就是说可以在JavaScript中应用函数式编程技术。数组方法诸如 `map()` 和 `reduce()` 就可以非常适合用于函数式编程风格。

函数式编程旨在扩展对JavaScript函数功能的探索，而不是为了良好的编程风格。

<br/>
<br/>

### 使用函数处理数组

Processing Arrays with Functions

假设有一个数组，数组元素都是数字，我们想要计算这些元素的平均值和标准差。

若使用非函数式编程风格，代码如下：

```js
let data = [1,1,3,5,5];  // This is our array of numbers

// The mean is the sum of the elements divided by the number of elements
let total = 0;
for(let i = 0; i < data.length; i++) total += data[i];
let mean = total/data.length;  // mean == 3; The mean of our data is 3

// To compute the standard deviation, we first sum the squares of
// the deviation of each element from the mean.
total = 0;
for(let i = 0; i < data.length; i++) {
    let deviation = data[i] - mean;
    total += deviation * deviation;
}
let stddev = Math.sqrt(total/(data.length-1));  // stddev == 2
```

可以使用数组方法 `map()` 和 `reduce()` 来实现同样的计算，这种实现很简洁。

```js
// First, define two simple functions
const sum = (x,y) => x+y;
const square = x => x*x;

// Then use those functions with Array methods to compute mean and stddev
let data = [1,1,3,5,5];
let mean = data.reduce(sum)/data.length;  // mean == 3
let deviations = data.map(x => x-mean);
let stddev = Math.sqrt(deviations.map(square).reduce(sum)/(data.length-1));
stddev  // => 2
```

这个新版本的代码看起来跟第一版有很大不同，但是它仍然调用对象的方法，所以它还是面向对象编程。接下来用函数版本的 `map()` 和 `reduce()` 方法：

```js
const map = function(a, ...args) { return a.map(...args); };
const reduce = function(a, ...args) { return a.reduce(...args); };

const sum = (x,y) => x+y;
const square = x => x*x;

let data = [1,1,3,5,5];
let mean = reduce(data, sum)/data.length;
let deviations = map(data, x => x-mean);
let stddev = Math.sqrt(reduce(map(deviations, square), sum)/(data.length-1));
stddev  // => 2
```

<br/>
<br/>

### 高阶函数

Higher-Order Functions

高阶函数就是操作函数的函数，它接收一个或多个函数作为参数，并返回一个新函数。

```js
// This higher-order function returns a new function that passes its
// arguments to f and returns the logical negation of f's return value;
function not(f) {
    return function(...args) {             // Return a new function
        let result = f.apply(this, args);  // that calls f
        return !result;                    // and negates its result.
    };
}

const even = x => x % 2 === 0; // A function to determine if a number is even
const odd = not(even);         // A new function that does the opposite
[1,1,3,5,5].every(odd)         // => true: every element of the array is odd
```

<br/>
<br/>

### 函数的局部应用

Partial Application of Functions

<br/>
<br/>

### 记忆

Memoization

将上次的计算结果缓存起来，在函数式编程当中，这种缓存技巧叫做记忆(memorization)。

```js
// Return a memoized version of f.
// It only works if arguments to f all have distinct string representations.
function memoize(f) {
    const cache = new Map();  // Value cache stored in the closure.

    return function(...args) {
        // Create a string version of the arguments to use as a cache key.
        let key = args.length + args.join("+");
        if (cache.has(key)) {
            return cache.get(key);
        } else {
            let result = f.apply(this, args);
            cache.set(key, result);
            return result;
        }
    };
}
```

<br/>

---

<br/>

# 类

Class

在JavaScript中可以定义对象的类，让每个对象都共享某些属性，这种共享的特性非常有用。类的成员或实例都包含一些属性，用以存放或定义它们的状态，其中有些属性定义了它们的行为（通常称为方法）。这些行为通常是由类定义的，而且为所有实例所共享。

在JavaScript中，类的实现是基于其原型继承机制的。如果两个实例都从同一个原型对象上继承了属性，我们说它们是同一个类的实例。

如果两个对象继承自同一个原型，往往意味着（但不是绝对）它们是由同一个构造函数创建并初始化的。

JavaScript一直允许定义类。ES6引入了全新的语法（包括 class 关键字），使创建类更加容易。

如果你对诸如Java和C++这种强类型的面向对象编程比较熟悉，你会发现JavaScript中的类和Java以及C++中的类有很大不同。尽管在写法上类似，但是最好要理解JavaScript的类和基于原型的继承机制，以及和传统的Java（类Java语言）的类和基于类的继承机制的不同之处。

<br/>
<br/>

## 类和原型

Classes and Prototypes

在JavaScript中，类的所有实例对象都从同一个原型对象上继承属性。因此，原型对象是类的核心。

一个简单的JavaScript类的示例：

```js
// This is a factory function that returns a new range object.
function range(from, to) {
    // Use Object.create() to create an object that inherits from the
    // prototype object defined below.  The prototype object is stored as
    // a property of this function, and defines the shared methods (behavior)
    // for all range objects.
    let r = Object.create(range.methods);

    // Store the start and end points (state) of this new range object.
    // These are noninherited properties that are unique to this object.
    r.from = from;
    r.to = to;

    // Finally return the new object
    return r;
}

// This prototype object defines methods inherited by all range objects.
range.methods = {
    // Return true if x is in the range, false otherwise
    // This method works for textual and Date ranges as well as numeric.
    includes(x) { return this.from <= x && x <= this.to; },

    // A generator function that makes instances of the class iterable.
    // Note that it only works for numeric ranges.
    *[Symbol.iterator]() {
        for(let x = Math.ceil(this.from); x <= this.to; x++) yield x;
    },

    // Return a string representation of the range
    toString() { return "(" + this.from + "..." + this.to + ")"; }
};

// Here are example uses of a range object.
let r = range(1,3);      // Create a range object
r.includes(2)            // => true: 2 is in the range
r.toString()             // => "(1...3)"
[...r]                   // => [1, 2, 3]; convert to an array via iterator
```

上述代码定义了一个工厂函数 `range()` 来创建一个新的Range对象。用range函数的methods属性来存放定义类的原型对象。只是将原型对象随意的放在一个地方，并不是一个规约或者习惯。

range函数在每个Range对象中都定义from和to属性。它们是非共享、非继承属性，是每个独立的Range对象的独特自有状态。

`range.methods` 对象应用了ES6的速记语法来定义方法，这是为什么没有看到function关键字的原因。

原型中的一个方法 `Symbol.iterator` 使用了计算属性名，表明它是为Range对象定义了一个迭代器。方法名称带有一个前缀星号(`*`)，标识它是一个生成器函数而不是普通的函数。

定义在 `range.methods` 中的共享继承方法都使用在 `range()` 工厂函数初始化的from和to属性。在这些方法被调用时，为了引用from和to属性，都使用this关键字来获取对象的引用。this这种用法是任何类中方法的基本特征。

<br/>
<br/>

## 类和构造函数

 Classes and Constructors

上述示例定义一个类，但这种方法并不常用，因为它没有定义一个构造函数。构造函数是用来初始化新建对象的。

下面的示例演示了在不支持ES6 class关键字版本的JavaScript中创建一个类的通用方法。即使是 class 已经很好支持的今天，仍然有很多旧JavaScript代码用这种方式定义类，并且你必须熟悉这种习惯用法，以便于阅读旧代码，也能够明白在使用class关键字时明白在底层发生了什么。

使用构造函数的 Range 类：

```js
// This is a constructor function that initializes new Range objects.
// Note that it does not create or return the object. It just initializes this.
function Range(from, to) {
    // Store the start and end points (state) of this new range object.
    // These are noninherited properties that are unique to this object.
    this.from = from;
    this.to = to;
}

// All Range objects inherit from this object.
// Note that the property name must be "prototype" for this to work.
Range.prototype = {
    // Return true if x is in the range, false otherwise
    // This method works for textual and Date ranges as well as numeric.
    includes: function(x) { return this.from <= x && x <= this.to; },

    // A generator function that makes instances of the class iterable.
    // Note that it only works for numeric ranges.
    [Symbol.iterator]: function*() {
        for(let x = Math.ceil(this.from); x <= this.to; x++) yield x;
    },

    // Return a string representation of the range
    toString: function() { return "(" + this.from + "..." + this.to + ")"; }
};

// Here are example uses of this new Range class
let r = new Range(1,3);   // Create a Range object; note the use of new
r.includes(2)             // => true: 2 is in the range
r.toString()              // => "(1...3)"
[...r]                    // => [1, 2, 3]; convert to an array via iterator
```

注意，当工厂函数 `range()` 转化为构造函数时被重命名为 `Range()`。这里遵循了一个常见的编程约定：从某种意义上讲，定义构造函数即是定义类，并且类名首字母要大写。而普通的函数和方法都是首字母小写。

再者，注意 `Range()` 构造函数是通过 new 关键字调用，而 `range()` 工厂函数则不必使用 new。由于 `Range()` 构造函数是通过 new 关键字调用的，因此不必调用 `Object.create()` 或其他什么逻辑来床架你新对象。在调用构造函数之前就已经创建了新对象，并且通过 this 关键字可以获取这个新对象。Range() 构造函数只不过是初始化 this 而已。构造函数甚至不必返回这个新创建的对象，构造函数会自动创建对象，然后将构造函数作为这个对象的方法来调用一次，最后返回这个新对象。

构造函数的调用与常规函数调用如此不同，实际上，这是我们为构造函数以大写字母命名的另一个原因。构造函数就是用来构造新对象的，它必须通过关键字 new 调用，如果将构造函数用作普通函数的话，往往不会正常工作。开发者可以通过命名规范（构造函数首字母大写，普通方法首字母小写）来判断是否应该在函数之前使用关键字 new。

在函数正文中，可以使用特殊的表达式 `new.target` 来判断函数是否已构造函数的方式调用。如果是 undefined，那么包含它的函数是作为函数调用的，没有使用 new 关键字。

```js
function C() {
    if (!new.target) return new C();
    // initialization code goes here
}
```

这个老式技巧只在构造函数定义时生效。类使用 class 关键字创建，不允许不使用 new 调用它的构造函数。

<br/>
<br/>

### 构造函数，类的标识和实例判断

Constructors, Class Identity, and instanceof

原型对象是类的基本标识：只有两个对象继承同一原型对象时，这两个对象是同一类实例。构造函数的关键点不是初始化新创建对象的状态：两个构造函数可能具有指向同一原型对象的原型属性。那么，两个构造函数都可用于创建同一类的实例。

尽管构造函数不像原型那样重要，但是构造函数充当 class 的大众脸。最明显的是，构造函数的名称通常用作类的名称。

```js
// 测试对象r是不是一个Range对象
r instanceof Range   // => true: r inherits from Range.prototype

// 为特定原型测试对象的原型链，并且不想将构造函数用作媒介
range.methods.isPrototypeOf(r);  // range.methods is the prototype object.
```

<br/>
<br/>

### 属性constructor

任何普通 JavaScript 函数（出箭头函数、生成器函数和异步函数之外）都可以用做构造函数，并且调用构造函数时需要一个 protoype 属性的。因此，每个JavaScript函数都自动拥有一个 prototype 属性。这个属性的值是一个对象，这个对象包含唯一一个不可枚举的属性 constructor（它的属性的值是一个函数对象）。

```js
let F = function() {}; // This is a function object.
let p = F.prototype;   // This is the prototype object associated with F.
let c = p.constructor; // This is the function associated with the prototype.
c === F                // => true: F.prototype.constructor === F for any F
```

可以看到构造函数的原型中存在预先定义好的 constructor 属性，这意味着对象通常继承的 constructor 是它们的构造函数的引用。由于构造函数是类的公共标识，因此这个 constructor 属性为对象提供了类。

```js
let o = new F();      // Create an object o of class F
o.constructor === F   // => true: the constructor property specifies the class
```

<br/>
<br/>

## 使用class关键字定义类

Classes with the class Keyword

类自第一个版本以来一直是JavaScript的一部分，但在ES6中，它们最终引入 `class` 关键字得到了自己的语法。

使用 class 编写 Range 类的实例，用 class 关键字声明类，后面解一个类名，最后是花括号包含类的正文。

```js
class Range {
    constructor(from, to) {
        // Store the start and end points (state) of this new range object.
        // These are noninherited properties that are unique to this object.
        this.from = from;
        this.to = to;
    }

    // Return true if x is in the range, false otherwise
    // This method works for textual and Date ranges as well as numeric.
    includes(x) { return this.from <= x && x <= this.to; }

    // A generator function that makes instances of the class iterable.
    // Note that it only works for numeric ranges.
    *[Symbol.iterator]() {
        for(let x = Math.ceil(this.from); x <= this.to; x++) yield x;
    }

    // Return a string representation of the range
    toString() { return `(${this.from}...${this.to})`; }
}

// Here are example uses of this new Range class
let r = new Range(1,3);   // Create a Range object
r.includes(2)             // => true: 2 is in the range
r.toString()              // => "(1...3)"
[...r]                    // => [1, 2, 3]; convert to an array via iterator
```

新的 class 语法更清洁方便，可以看作是构造函数的基本类定义机制的语法糖。

与对象不同，类不支持具有名称/值对的属性的定义。

关键字 constructor 用于定义类的构造函数。但是，定义的函数实际上并不命名为 constructor。类声明语句定义一个新的变量 Range，并将此特殊构造函数的值分配给该变量。

如果类不需要执行任何初始化，可以省略构造函数关键字及其正文，并将隐式创建一个空构造函数。

如果要定义子类（或继承来自另一个类的类），可以使用 externds 关键字与 class 关键字：

```js
// A Span is like a Range, but instead of initializing it with
// a start and an end, we initialize it with a start and a length
class Span extends Range {
    constructor(start, length) {
        if (length >= 0) {
            super(start, start + length);
        } else {
            super(start + length, start);
        }
    }
}
```

与函数声明一样，类声明同时具有语句和表达式形式。

```js
let square = function(x) { return x * x; };
square(3)  // => 9

// we can also write
let Square = class { constructor(x) { this.area = x * x; } };
new Square(3).area  // => 9
```

<br/>

几个重要但是不易主义的类语法：

- 类声明正文中的所有代码都隐式采用严格模式，即使未出现 `user strict` 指令。
- 与函数声明不同，类声明不是 **声明提前**的，在声明类之前，不能实例化类。

<br/>
<br/>

### 静态方法

Static Methods

可以通过使用 static 关键字作为方法声明前缀，来定义类正文中的静态方法。静态方法定义为构造函数的属性，而不是原型对象的属性。

有时会看到静态方法称为类方法，因为它们是使用类/构造函数的名称调用的。使用此术语时，将类方法与在类实例上调用的常规实例方法进行对比，由于静态方法在构造函数上调用，而不是在任何特定实例上调用，因此在静态方法中使用 this 关键字几乎从来就没有意义。

<br/>
<br/>

### Getter、Setter和其它方法形式

Getters, Setters, and other Method Forms

在类正文中，可以定义 getter 和 setter 方法，就像在对字面量中一样。唯一的区别是，在类正文中，不会将逗号放在 getter 或 getter 之后。

通常，对象字面量中允许的所有速记方法定义语法也允许在类正文中使用。这包括生成器方法和名称为方括号中表达式值的方法。

```js
// 生成器方法
*[Symbol.iterator]() {
    for(let x = Math.ceil(this.from); x <= this.to; x++) yield x;
}
```

<br/>
<br/>

### 公共、私有和静态字段

Public, Private, and Static Fields

ES6标准只允许创建方法（getter、setter 和生成器）和静态方法，它不包括用于定义字段的语法。如果要在类实例上定义字段，则必须在构造函数或其中一种方法中这样做。必须在类正文之外类定义后，才能为类定义静态字段。

但是，对于允许以公有和私有形式定义实例和静态字段的扩展类语法正在进行标准化。

```js
// 假设你正在编写一个这样的类
// 其中一个构造函数初始化了三个字段
class Buffer {
    constructor() {
        this.size = 0;
        this.capacity = 4096;
        this.buffer = new Uint8Array(this.capacity);
    }
}

// 使用可能标准化的新实例字段语法
class Buffer {
    size = 0;
    capacity = 4096;
    buffer = new Uint8Array(this.capacity);
}
```

字段初始化代码已移出构造函数，现在直接显示在类正文中。（当然，该代码仍作为构造函数的一部分运行。如果不定义构造函数，则字段初始化为隐式创建的构造函数的一部分。）赋值左侧的 this 消失，但请注意，即使是在初始化赋值的右侧，仍必须使用 this 引用这些字段）。

在添加字段语法之前，类正文看起来很像使用快捷方法语法的对象字面量，只不过逗号被删除。字段语法清楚地表明类正文与对象字面量不完全相同。

标准化中的实例字段同时也定义了私有实例字段。

```js
// 私有字段
// 类正文之外的任何代码不可见且不可访问
class Buffer {
    #size = 0;
    get size() { return this.#size; }
}
```

请注意，必须先使用新字段语法 声明私有字段，然后才能使用它们。

如果在共有或私有字段声明之前添加静态字段，这些字段将创建为构造函数的属性，而不是实例属性。

<br/>
<br/>

### 一个复数类的示例

Example: A Complex Number Class

一个复数类：

```js
/**
 * Instances of this Complex class represent complex numbers.
 * Recall that a complex number is the sum of a real number and an
 * imaginary number and that the imaginary number i is the square root of -1.
 */
class Complex {
    // Once class field declarations are standardized, we could declare
    // private fields to hold the real and imaginary parts of a complex number
    // here, with code like this:
    //
    // #r = 0;
    // #i = 0;

    // This constructor function defines the instance fields r and i on every
    // instance it creates. These fields hold the real and imaginary parts of
    // the complex number: they are the state of the object.
    constructor(real, imaginary) {
        this.r = real;       // This field holds the real part of the number.
        this.i = imaginary;  // This field holds the imaginary part.
    }

    // Here are two instance methods for addition and multiplication
    // of complex numbers. If c and d are instances of this class, we
    // might write c.plus(d) or d.times(c)
    plus(that) {
        return new Complex(this.r + that.r, this.i + that.i);
    }
    times(that) {
        return new Complex(this.r * that.r - this.i * that.i,
                           this.r * that.i + this.i * that.r);
    }

    // And here are static variants of the complex arithmetic methods.
    // We could write Complex.sum(c,d) and Complex.product(c,d)
    static sum(c, d) { return c.plus(d); }
    static product(c, d) { return c.times(d); }

    // These are some instance methods that are defined as getters
    // so they're used like fields. The real and imaginary getters would
    // be useful if we were using private fields this.#r and this.#i
    get real() { return this.r; }
    get imaginary() { return this.i; }
    get magnitude() { return Math.hypot(this.r, this.i); }

    // Classes should almost always have a toString() method
    toString() { return `{${this.r},${this.i}}`; }

    // It is often useful to define a method for testing whether
    // two instances of your class represent the same value
    equals(that) {
        return that instanceof Complex &&
            this.r === that.r &&
            this.i === that.i;
    }

    // Once static fields are supported inside class bodies, we could
    // define a useful Complex.ZERO constant like this:
    // static ZERO = new Complex(0,0);
}

// Here are some class fields that hold useful predefined complex numbers.
Complex.ZERO = new Complex(0,0);
Complex.ONE = new Complex(1,0);
Complex.I = new Complex(0,1);
```

定义了复数类后，我们可以将构造函数、实例字段、实例方法、类字段和类方法如下使用：

```js
let c = new Complex(2, 3);     // Create a new object with the constructor
let d = new Complex(c.i, c.r); // Use instance fields of c
c.plus(d).toString()           // => "{5,5}"; use instance methods
c.magnitude                    // => Math.hypot(2,3); use a getter function
Complex.product(c, d)          // => new Complex(0, 13); a static method
Complex.ZERO.toString()        // => "{0,0}"; a static property
```

<br/>
<br/>

## 为现有的类添加方法

Adding Methods to Existing Classes

JavaScript 基于原型的继承机制是动态的：对象从其原型继承属性，即使原型的属性在创建对象后发生更改。这意味着我们只需向原型对象添加新方法，即可扩展 JavaScript 类。

```js
// 将计算共轭复数的方法添加到上例
// Return a complex number that is the complex conjugate of this one.
Complex.prototype.conj = function() { return new Complex(this.r, -this.i); };
```

<br/>
<br/>

## 子类

Subclasses

在面向对象的编程中，B 类可以扩展 A 类成为它的子类。我们称 A 是父类，B 是子类。B 的实例继承 A 的方法。B 类可以自定方法，使用相同名称可以重写类 A 中的方法。

本节首先演示 ES6 之前如何定义子类，然后演示使用 class 和 extends 关键字的子类和使用 super 关键字调用父类构造函数。

<br/>
<br/>

### 子类和原型

Subclasses and Prototypes

一个简单的子类：

```js
// This is the constructor function for our subclass
function Span(start, span) {
    if (span >= 0) {
        this.from = start;
        this.to = start + span;
    } else {
        this.to = start;
        this.from = start + span;
    }
}

// Ensure that the Span prototype inherits from the Range prototype
Span.prototype = Object.create(Range.prototype);

// We don't want to inherit Range.prototype.constructor, so we
// define our own constructor property.
Span.prototype.constructor = Span;

// By defining its own toString() method, Span overrides the
// toString() method that it would otherwise inherit from Range.
Span.prototype.toString = function() {
    return `(${this.from}... +${this.to - this.from})`;
};
```

为了使 Span 成为 Range 的子类，需要使 `Span.prototype` 从 `Range.prototype` 继承。

一个健壮的子类机制需要允许类调用其父类的方法和构造函数，但在ES6之前，JavaScript 没有一个简单的方法来执行这些操作。

幸运的是，ES6 用 super 关键字作为类语法的一部分解决了这些问题。

<br/>
<br/>

### 带有extends和super的子类

Subclasses with extends and super

在 ES6 之后，可以简单的在类声明时接一个 extends 从句添加一个父类，即使对于内置类也可以这样做。

下例是一个检测 key 和 value 类型的 Map 子类。

```js
class TypedMap extends Map {
    constructor(keyType, valueType, entries) {
        // If entries are specified, check their types
        if (entries) {
            for(let [k, v] of entries) {
                if (typeof k !== keyType || typeof v !== valueType) {
                    throw new TypeError(`Wrong type for entry [${k}, ${v}]`);
                }
            }
        }

        // Initialize the superclass with the (type-checked) initial entries
        super(entries);

        // And then initialize this subclass by storing the types
        this.keyType = keyType;
        this.valueType = valueType;
    }

    // Now redefine the set() method to add type checking for any
    // new entries added to the map.
    set(key, value) {
        // Throw an error if the key or value are of the wrong type
        if (this.keyType && typeof key !== this.keyType) {
            throw new TypeError(`${key} is not of type ${this.keyType}`);
        }
        if (this.valueType && typeof value !== this.valueType) {
            throw new TypeError(`${value} is not of type ${this.valueType}`);
        }

        // If the types are correct, we invoke the superclass's version of
        // the set() method, to actually add the entry to the map. And we
        // return whatever the superclass method returns.
        return super.set(key, value);
    }
}
```

在构造函数中使用 `super()` 需要了解一些重要规则：

- 如果使用 extends 关键字定义类，则类的构造函数必须使用 `super()` 调用父类构造函数。
- 如果未在子类中定义构造函数，将自动为你定义一个构造函数。此隐式定义的构造函数只将传递给它的值传递给 `super()`。
- 在使用 `super()` 调用父类构造函数之前，不能在构造函数中使用 this 关键字。这个强制规则确保父类先于子类初始化。

在构造函数中，需要先调用父类构造函数，然后才能访问 this 和初始化新对象。但重写方法时没有此类规则。调用重写父类方法时不需要调用父类方法。如果它确实使用 super 来调整父类中的重写方法，它可以在重写方法的开头、中间和末尾调用。

<br/>
<br/>

### 委托而不是继承

Delegation Instead of Inheritance

extends 关键字便于创建子类。但这并不意味着应该创建大量的子类。如果要编写某些其他类共享的行为的类，可以尝试通过创建子类来继承该行为。但是，通常将期望的行为编写在类中比用类创建其他类的实例，并根据需要委托给该实例更简单也更灵活。创建新类不将其作为子类，而是通过包装(wrapping)或组合(composing)其他类。这种委托方法通常称为组合(composition)，它是一种面向对象编程经常被引用的座右铭——倾向于组合而不是继承。

使用委托实现一个类似 JavaScript 的 Set 类的 Histogram 类。

```js
/**
 * A Set-like class that keeps track of how many times a value has
 * been added. Call add() and remove() like you would for a Set, and
 * call count() to find out how many times a given value has been added.
 * The default iterator yields the values that have been added at least
 * once. Use entries() if you want to iterate [value, count] pairs.
 */
class Histogram {
    // To initialize, we just create a Map object to delegate to
    constructor() { this.map = new Map(); }

    // For any given key, the count is the value in the Map, or zero
    // if the key does not appear in the Map.
    count(key) { return this.map.get(key) || 0; }

    // The Set-like method has() returns true if the count is non-zero
    has(key) { return this.count(key) > 0; }

    // The size of the histogram is just the number of entries in the Map.
    get size() { return this.map.size; }

    // To add a key, just increment its count in the Map.
    add(key) { this.map.set(key, this.count(key) + 1); }

    // Deleting a key is a little trickier because we have to delete
    // the key from the Map if the count goes back down to zero.
    delete(key) {
        let count = this.count(key);
        if (count === 1) {
            this.map.delete(key);
        } else if (count > 1) {
            this.map.set(key, count - 1);
        }
    }

    // Iterating a Histogram just returns the keys stored in it
    [Symbol.iterator]() { return this.map.keys(); }

    // These other iterator methods just delegate to the Map object
    keys() { return this.map.keys(); }
    values() { return this.map.values(); }
    entries() { return this.map.entries(); }
}
```

<br/>
<br/>

### 类的层次结构和抽象类

Class Hierarchies and Abstract Classes

使用 JavaScript 类封装数据和模块化代码通常是一种很好的技术，你可能会发现自己经常使用类关键字。但是，你可能会发现，你更喜欢组合而不是继承，而且很少需要使用 extends （除非使用需要扩展的库或框架）。

但是，在某些情况下，多个级别的子类是合适的，我们将选举一个扩展示例来结束本章，通过描述不同种类的集合来演示类的层次结构。

下面的示例定义了大量子类，但它也演示了如何定义抽象类（不包括完整实现的类）作为一组相关子类的通用父类。抽象父类可以定义所有子类继承和共享的部分实现。因此，子类只需要通过实现父类定义的抽象方法来定义它们自己的独特行为。请注意，JavaScript 对抽象方法或抽象类没有任何正式定义，我只是将这个名字用于未实现的方法和不完全实现的类。

抽象类和实体类的层次

```js
/**
 * The AbstractSet class defines a single abstract method, has().
 */
class AbstractSet {
    // Throw an error here so that subclasses are forced
    // to define their own working version of this method.
    has(x) { throw new Error("Abstract method"); }
}

/**
 * NotSet is a concrete subclass of AbstractSet.
 * The members of this set are all values that are not members of some
 * other set. Because it is defined in terms of another set it is not
 * writable, and because it has infinite members, it is not enumerable.
 * All we can do with it is test for membership and convert it to a
 * string using mathematical notation.
 */
class NotSet extends AbstractSet {
    constructor(set) {
        super();
        this.set = set;
    }

    // Our implementation of the abstract method we inherited
    has(x) { return !this.set.has(x); }
    // And we also override this Object method
    toString() { return `{ x| x ∉ ${this.set.toString()} }`; }
}

/**
 * Range set is a concrete subclass of AbstractSet. Its members are
 * all values that are between the from and to bounds, inclusive.
 * Since its members can be floating point numbers, it is not
 * enumerable and does not have a meaningful size.
 */
class RangeSet extends AbstractSet {
    constructor(from, to) {
        super();
        this.from = from;
        this.to = to;
    }

    has(x) { return x >= this.from && x <= this.to; }
    toString() { return `{ x| ${this.from} ≤ x ≤ ${this.to} }`; }
}

/*
 * AbstractEnumerableSet is an abstract subclass of AbstractSet.  It defines
 * an abstract getter that returns the size of the set and also defines an
 * abstract iterator. And it then implements concrete isEmpty(), toString(),
 * and equals() methods on top of those. Subclasses that implement the
 * iterator, the size getter, and the has() method get these concrete
 * methods for free.
 */
class AbstractEnumerableSet extends AbstractSet {
    get size() { throw new Error("Abstract method"); }
    [Symbol.iterator]() { throw new Error("Abstract method"); }

    isEmpty() { return this.size === 0; }
    toString() { return `{${Array.from(this).join(", ")}}`; }
    equals(set) {
        // If the other set is not also Enumerable, it isn't equal to this one
        if (!(set instanceof AbstractEnumerableSet)) return false;

        // If they don't have the same size, they're not equal
        if (this.size !== set.size) return false;

        // Loop through the elements of this set
        for(let element of this) {
            // If an element isn't in the other set, they aren't equal
            if (!set.has(element)) return false;
        }

        // The elements matched, so the sets are equal
        return true;
    }
}

/*
 * SingletonSet is a concrete subclass of AbstractEnumerableSet.
 * A singleton set is a read-only set with a single member.
 */
class SingletonSet extends AbstractEnumerableSet {
    constructor(member) {
        super();
        this.member = member;
    }

    // We implement these three methods, and inherit isEmpty, equals()
    // and toString() implementations based on these methods.
    has(x) { return x === this.member; }
    get size() { return 1; }
    *[Symbol.iterator]() { yield this.member; }
}

/*
 * AbstractWritableSet is an abstract subclass of AbstractEnumerableSet.
 * It defines the abstract methods insert() and remove() that insert and
 * remove individual elements from the set, and then implements concrete
 * add(), subtract(), and intersect() methods on top of those. Note that
 * our API diverges here from the standard JavaScript Set class.
 */
class AbstractWritableSet extends  AbstractEnumerableSet {
    insert(x) { throw new Error("Abstract method"); }
    remove(x) { throw new Error("Abstract method"); }

    add(set) {
        for(let element of set) {
            this.insert(element);
        }
    }

    subtract(set) {
        for(let element of set) {
            this.remove(element);
        }
    }

    intersect(set) {
        for(let element of this) {
            if (!set.has(element)) {
                this.remove(element);
            }
        }
    }
}

/**
 * A BitSet is a concrete subclass of AbstractWritableSet with a
 * very efficient fixed-size set implementation for sets whose
 * elements are non-negative integers less than some maximum size.
 */
class BitSet extends AbstractWritableSet {
    constructor(max) {
        super();
        this.max = max;  // The maximum integer we can store.
        this.n = 0;      // How many integers are in the set
        this.numBytes = Math.floor(max / 8) + 1;   // How many bytes we need
        this.data = new Uint8Array(this.numBytes); // The bytes
    }

    // Internal method to check if a value is a legal member of this set
    _valid(x) { return Number.isInteger(x) && x >= 0 && x <= this.max; }

    // Tests whether the specified bit of the specified byte of our
    // data array is set or not. Returns true or false.
    _has(byte, bit) { return (this.data[byte] & BitSet.bits[bit]) !== 0; }

    // Is the value x in this BitSet?
    has(x) {
        if (this._valid(x)) {
            let byte = Math.floor(x / 8);
            let bit = x % 8;
            return this._has(byte, bit);
        } else {
            return false;
        }
    }

    // Insert the value x into the BitSet
    insert(x) {
        if (this._valid(x)) {               // If the value is valid
            let byte = Math.floor(x / 8);   // convert to byte and bit
            let bit = x % 8;
            if (!this._has(byte, bit)) {    // If that bit is not set yet
                this.data[byte] |= BitSet.bits[bit]; // then set it
                this.n++;                            // and increment set size
            }
        } else {
            throw new TypeError("Invalid set element: " + x );
        }
    }

    remove(x) {
        if (this._valid(x)) {              // If the value is valid
            let byte = Math.floor(x / 8);  // compute the byte and bit
            let bit = x % 8;
            if (this._has(byte, bit)) {    // If that bit is already set
                this.data[byte] &= BitSet.masks[bit];  // then unset it
                this.n--;                              // and decrement size
            }
        } else {
            throw new TypeError("Invalid set element: " + x );
        }
    }

    // A getter to return the size of the set
    get size() { return this.n; }

    // Iterate the set by just checking each bit in turn.
    // (We could be a lot more clever and optimize this substantially)
    *[Symbol.iterator]() {
        for(let i = 0; i <= this.max; i++) {
            if (this.has(i)) {
                yield i;
            }
        }
    }
}

// Some pre-computed values used by the has(), insert() and remove() methods
BitSet.bits = new Uint8Array([1, 2, 4, 8, 16, 32, 64, 128]);
BitSet.masks = new Uint8Array([~1, ~2, ~4, ~8, ~16, ~32, ~64, ~128]);
```

<br/>

---

<br/>

# 模块

Modules

模块化编程的目标是允许使用来自不同作者和来源的代码模块来组装大型程序，即使出现了不同模块作者没有预料到的代码，所有这些代码也能正确运行。作为一个实际问题，模块化主要是关于封装和隐藏私有实现细节，并保持全局名称空间整洁，以便模块不会意外地修改其他模块定义的变量、函数和类。

知道最近，JavaScript 还没有对模块的内置支持，在大型代码库上工作的程序员尽力使用类、对象和闭包的弱模块性。基于闭包的模块化在代码捆绑工具的支持下，实际使用中形成了一种基于 `require()` 函数的模块化形式，Node 采用了这种形式。作为一个实际问题，JavaScript 模块化仍然依赖于代码捆绑工具。

以下各节包括：

- 使用类、对象和闭包来自己做模块
- 使用 `require()` 的 Node 模块
- 使用 export、import 和 `import()` 的 ES6 模块

<br/>
<br/>

## 使用类、对象和闭包的模块

Modules with Classes, Objects, and Closures

类的重要特征之一是它们充当其方法的模块。使用类和对象实现模块化是 JavaScript 编程中常见而有用的技术，但这还不够。特别是，它没有提供任何方法来隐藏模块内部的实现细节。

<br/>
<br/>

## Node中的模块

Modules in Node

在 Node 编程中，将程序分成尽可能多的文件是很正常的。这些 JavaScript 代码文件被认为都生活在一个快速的文件系统中。与 Web 浏览器不同的是，浏览器必须通过相对较慢的网络连接来读取 JavaScript 文件，将 Node 程序捆绑在一个单一的 JavaScript 文件中没有任何必要或好处。

在 Node 中，每个文件都是一个独立的模块，有一个私有的命名空间。在一个文件中定义的常量、变量、函数和类对该文件是私有的，除非该文件将它们导出。一个模块的导出值只有在另一个模块明确导入的情况下才会在该模块中可见。

Node 模块使用 `require()` 函数导入其他模块，并通过设置 Exports 对象的属性或全局替换 `modules.exportsobject` 来导出其公共API。

<br/>
<br/>

### Node Exports

Node 定义了一个全局导出对象，它总是被定义。如果你正在编写一个导出多个值的 Node 模块，你可以简单地将它们分配给这个对象的属性。

```js
const sum = (x, y) => x + y;
const square = x => x * x;

exports.mean = data => data.reduce(sum)/data.length;
exports.stddev = function(d) {
    let m = exports.mean(d);
    return Math.sqrt(d.map(x => x - m).map(square).reduce(sum)/(d.length-1));
};
```

<br/>
<br/>

### Node Imports

一个 Node 模块通过调用 `require()` 函数导入另一个模块。

如果你想要导入 Node 内置的系统模块，或通过包管理器安装在系统上的模块，那么只需要使用模块的非限定名称。

```js
// These modules are built in to Node
const fs = require("fs");           // The built-in filesystem module
const http = require("http");       // The built-in HTTP module

// The Express HTTP server framework is a third-party module.
// It is not part of Node but has been installed locally
const express = require("express");
```

<br/>

如果想要导入自己的代码模块，模块名称应该是包含该代码的文件路径，相对于当前模块的文件。以根路径(`/`)字符开头的绝对路径是合法的，但通常情况下，使用相对路径(`./`)开头。

你可以在导入的文件上省略后缀(`.js`)，Node 仍会找到这些文件，但通常会看到这些文件的扩展名被明确包括在内。

```js
const stats = require('./stats.js');
const BitSet = require('./utils/bitset.js');

```

<br/>
<br/>

### 浏览器上的Node格式的模块

Node-Style Modules on the Web

JavaScript 具有自己的标准模块语法，但是使用捆绑程序的开发人员更喜欢将正式的 JavaScript 模块与 import 和 export 语句一起使用。

<br/>
<br/>

## ES6中的模块

Modules in ES6

ES6 给 JavaScript 添加了 import 和 export 关键字，并且最终支持真正的模块化，将其作为核心语言特性。ES6 模块化概念上和 Node 模块化相同：每个文件是它们自己的模块，定义在文件中的常量、变量、函数和类是模块私有成员，除非它们被显式导出。

请注意，ES6 模块在某些重要方面也与常规 JavaScript 脚本不同。最明显的区别式模块化本身：在常规脚本中，变量、函数和类的顶级声明在所有脚本共享的一个全局上下文中。模块每个文件都有其自己的专用上下文，并且可以使用导入和导出语句。模块中的代码将自动进入严格模式。

由于拥有 JavaScript 模块化的先驱，Node 处于必须支持两个不完全兼容的模块系统的尴尬境地。 Node13 支持 ES6 模块，但绝大多数 Node 程序仍在使用 Node 模块。

<br/>
<br/>

### ES6 Exports

要从 ES6 模块导出常量、变量、函数和类，只需在声明之前添加关键字 export。

```js
export const PI = Math.PI;

export function degreesToRadians(d) { return d * PI / 180; }

export class Circle {
    constructor(r) { this.r = r; }
    area() { return PI * this.r * this.r; }
}
```

<br/>

可以直接在末尾编写一个导出语句，而不是每个代码中都单独编写导出。

```js
export { Circle, degreesToRadians, PI };
```

<br/>

编写只导出一个值的模块也很常见，在这种情况下，通常使用 `export default`。

```js
export default class BitSet {
    // implementation omitted
}
```

<br/>

同时使用 export 和 export default 的模块是合法的，但是不常用。模块的默认导出只能有一个。

请注意， export 关键字只能出现在 JavaScript 代码的顶层。不能从类、函数、循环或条件内导出值。

<br/>
<br/>

### ES6 Imports

使用 import 关键字导入其他模块导出的值。

只能在模块的顶层导入，不能再类、函数、循环或条件中导入。根据近似通用规约，需要在模块的开头进行模块导入。但这不是必须的：就像函数声明。

被导入的模块用单引号或双引号来括上，由字符串字面量常量来指定。

```js
// 导入 默认导出的模块
import BitSet from './bitset.js';

// 导入 导出多个值的模块
import { mean, stddev } from "./stats.js";

// 导入所有内容
// 如 stats.mean() 
import * as stats from "./stats.js";

// 定义一个默认导出或者多个命名导出的模块
import Histogram, { mean, stddev } from "./histogram-stats.js";
```

<br/>

将没有导出的模块包含到程序中。

```js
import "./analytics.js";
```

这样的代码在首次运行时导入，并且随后的导入不执行任何操作。仅定义函数的模块只有在导出至少一个函数时才有用。但是，如果模块运行一些代码，那么即使没有使用符号也可以导入。

请注意，即使对于具有导出的模块，也可以使用不导入任何内容的导入语法。

<br/>
<br/>

### 重命名的导入和导出

Imports and Exports with Renaming

如果两个模块使用相同的名称导出两个不同的值，并且要导入这两个值（意味着这两个值重名了），则在导入时必须重命名值以避免冲突。

```js
import { render as renderImage } from "./imageutils.js";
import { render as renderUI } from "./ui.js";
```

也可以在导出时重命名值，但仅在使用 export 语句的花括号时才可以。

```js
export {
    layout as calculateLayout,
    render as renderLayout
};
```

<br/>
<br/>

### 再导出

Re-Exports

在单个文件中导入多个模块，然后在作为一个模块导出。

```js
import { mean } from "./stats/mean.js";
import { stddev } from "./stats/stddev.js";
export { mean, stdev };
```

<br/>

ES6 模块预见了这种使用场景，并为此提供了一种特殊的语法。可以使用 export 和 from 关键字合并导入和导出到一个单独的再导出语句中。

```js
export { mean } from "./stats/mean.js";
export { stddev } from "./stats/stddev.js";

// 再导出语法允许重命名
export { mean, mean as average } from "./stats/mean.js";
export { stddev } from "./stats/stddev.js";
```

其它一些用法这里就不写了，到时有需要自己再搜。

<br/>
<br/>

### Web上的JavaScript模块

JavaScript Modules on the Web

使用 ES6 模块的生产代码通常仍与 webpack 之类的工具捆绑在一起。代码捆绑往往会提供更好的性能。随着网络速度的增长以及浏览器供应商继续优化其 ES6 模块的实现，将来这种情况可能会发生很大的变化。

<br/>
<br/>

### 动态导入

Dynamic Imports with import()

ES6 的导入和导出指令时完全静态的，解释器和工具能够在模块加载时通过简单的文本分析来确定模块之间的关系，而不需要实际执行模块中的任何代码。有了静态导入的模块，你可以保证在你的模块中的任何代码开始运行之前，你导入到模块中的值就可以使用了。

在 Web 上，代码必须通过网络传输，而不是从文件系统读取。而客户端设备相同较慢，在这种情况下，静态模块导入，要求整个程序在运行前被加载，可能没有什么意义。

对于 Web 应用来说，最初只加载足够的代码来渲染显示给用户的第一个页面是很常见的。然后，一旦用户有了一些初步的互动内容，他们就可以开始加载网络应用的其余部分所需的通常也大得多的代码。Web 浏览器通过使用 DOM API 将一个新的 `<script>` 标签注入到当前的 HTML 文档中，使动态加载代码变得很容易，而且这种做法已经持续了很多了。

虽然动态加载已有了很长一段时间，但它并不是语言本身的一部分。这种情况随着 ES2020 中 `import()` 的引入而改变，所有支持 ES6 模块的浏览器都支持动态导入。

<br/>
<br/>

### import.meta.url

在 ES6 模块中，特殊语法 `import.meta` 指的是一个包含当前执行模块的元数据的对象。这个对象的 url 属性使加载模块的 URL（在 Node中，这是一个本地格式的 `file://url`）。

<br/>

---

<br/>

# 标准库

JavaScript built-in Library

本章的一些内容：

- Set 和 Map 类，用于表示一组值以及从一组值到另一组值的映射。
- 类数组对象，称为 TypedArrays，表示二进制数据的数组，以及从非数组二进制数据中提取值的相关类。
- 正则表达式和 RegExp 类，它们定义文本类型，对文本处理很有用。
- 表示和操作日期和时间的 Date 类。
- Error 类及其各种子类，在 JavaScript 程序中发生错误时抛出它们的实例。
- JSON 对象，其方法支持由对象、数组、字符串、数字和布尔值组成的 JavaScript 数据结构序列化和反序列化。
- Intl 对象和它定义的类可以帮助你本地化你的程序。
- Console 对象，其方法输出字符串的方式对调式程序和记录这些程序的行为特别有用。
- URL 类，简化了解析和操纵 URL的任务。
- `setTimeout()` 相关函数，用于在指定的时间间隔后执行的代码。

<br/>
<br/>

## Set类和Map类

Sets and Maps

Javascript 的对象类型是一种通用的数据结构，可用于将字符串（对象的属性名称）映射到任意值。当被映射的值是固定的（比如 true），那么对象实际上是一组字符串。

在 JavaScript 编程中，对象通常被用作映射和集合，但这收到字符串的限制，而且由于对象通常继承一些属性而变得复杂，这些属性通常不准备成为映射的一部分。

因此，ES6 引入了正则的 Set 和 Map 类。

<br/>
<br/>

### Set类

The Set Class

set 是值的集合，就像数组。但不同于数组，它没有被排序或被索引，并且不允许重复。

使用 `Set()` 创建 Set 对象，`Set()` 构造函数的实参不必是数组：允许任何可迭代的对象（包括其它 Set 对象）。

```js
let s = new Set();       // A new, empty set
let t = new Set([1, s]); // A new set with two members

let t = new Set(s);                  // A new set that copies the elements of s.
let unique = new Set("Mississippi"); // 4 elements: "M", "i", "s", and "p"

// size 属性，集合包含多少值
unique.size  // => 4
```

<br/>

创建集合时无需初始化。可以随时使用 `add()`、`delete()` 和 `clear()` 添加和删除元素。由于集合不能包含重复向，因此向集合添加一个已经存在的值是一个无效操作。

集合成员关系是基于严格的相等性检查。

```js
let s = new Set();  // Start empty
s.size              // => 0
s.add(1);           // Add a number
s.size              // => 1; now the set has one member
s.add(1);           // Add the same number again
s.size              // => 1; the size does not change
s.add(true);        // Add another value; note that it is fine to mix types
s.size              // => 2
s.add([1,2,3]);     // Add an array value
s.size              // => 3; the array was added, not its elements
s.delete(1)         // => true: successfully deleted element 1
s.size              // => 2: the size is back down to 2
s.delete("test")    // => false: "test" was not a member, deletion failed
s.delete(true)      // => true: delete succeeded
s.delete([1,2,3])   // => false: the array in the set is different
s.size              // => 1: there is still that one array in the set
s.clear();          // Remove everything from the set
s.size              // => 0
```

<br/>

JavaScript 和 Python 的集合之间有个很大的差异。Python 的集合比较成员是为了相等，而不是标识，Python 的集合只允许不可变成员（如元组），并且不允许将列表和字典添加到集合中。

实际上，对集合进行的最重要的操作并不是添加和删除元素，而是检查指定的值是否是集合的成员。

集合已针对成员资格进行了优化，无论集合有多少个成员，`has()` 方法都非常快。数组的 `include()` 方法也执行成员资格测试，但是花费的时间与数组的大小成正比，并且将数组作为集合使用要比使用真正的集合对象慢得多。

```js
let oneDigitPrimes = new Set([2,3,5,7]);
oneDigitPrimes.has(2)    // => true: 2 is a one-digit prime number
oneDigitPrimes.has(3)    // => true: so is 3
oneDigitPrimes.has(4)    // => false: 4 is not a prime
oneDigitPrimes.has("5")  // => false: "5" is not even a number
```

<br/>

Set 类是可迭代的，这意味着可以使用 for 循环来枚举集合中的所有元素。并且也可以使用展开运算符将它们转换为数组和实参列表。

```js
let sum = 0;
for(let p of oneDigitPrimes) { // Loop through the one-digit primes
    sum += p;                  // and add them up
}
sum                            // => 17: 2 + 3 + 5 + 7

[...oneDigitPrimes]         // => [2,3,5,7]: the set converted to an Array
Math.max(...oneDigitPrimes) // => 7: set elements passed as function arguments
```

<br/>

Set 通常被称为无序集合。但对于 JavaScript 的 Set 类而言，并非完全如此。JavaScript Set 类始终记住插入元素的顺序，并且在遍历集合时始终使用此顺序。

除了可迭代之外，Set 类还实现了一个 `forEach()` 方法，该方法类似于同名的 array 方法。

```js
let product = 1;
oneDigitPrimes.forEach(n => { product *= n; });
product     // => 210: 2 * 3 * 5 * 7
```

<br/>
<br/>

### Map类

The Map Class

Map 对象代表一组称为键的值。从某种意义上讲，映射就像一个数组，但映射不是使用一组连续的整数作为建，而是允许使用任意值作为索引。

```js
// 用 Map() 构造函数创建一个 map
let m = new Map();  // Create a new, empty map
let n = new Map([   // A new map initialized with string keys mapped to numbers
    ["one", 1],
    ["two", 2]
]);

// 从其他对象复制
let copy = new Map(n); // A new map with the same keys and values as map n
let o = { x: 1, y: 2}; // An object with two properties
let p = new Map(Object.entries(o)); // Same as new map([["x", 1], ["y", 2]])
```

创建 Map 对象后，有几种方法：

- `get()`：查询给定键的值
- `set()`：添加新的键值对，如果键已存在，则会更新值。
- `has()`：判断键是否存在
- `delete()`：删除一个键和值
- `clear()`：移除所有键值对
- `size()`：包含多少键

方法的示例:

```js
let m = new Map();   // Start with an empty map
m.size               // => 0: empty maps have no keys
m.set("one", 1);     // Map the key "one" to the value 1
m.set("two", 2);     // And the key "two" to the value 2.
m.size               // => 2: the map now has two keys
m.get("two")         // => 2: return the value associated with key "two"
m.get("three")       // => undefined: this key is not in the set
m.set("one", true);  // Change the value associated with an existing key
m.size               // => 2: the size doesn't change
m.has("one")         // => true: the map has a key "one"
m.has(true)          // => false: the map does not have a key true
m.delete("one")      // => true: the key existed and deletion succeeded
m.size               // => 1
m.delete("three")    // => false: failed to delete a nonexistent key
m.clear();           // Remove all keys and values from the map
```

任何 JavaScript 的值都可以被用作 Map 的键或值。包括 null, undefined 和 NaN，也包括对象和数组这种引用类型。

Map 是可迭代对象，每一个迭代值是两个元素数组，第一个元素是键，第二个元素是键映射的值。Map 类按照插入顺序进行遍历。

```js
let m = new Map([["x", 1], ["y", 2]]);
[...m]    // => [["x", 1], ["y", 2]]

for(let [key, value] of m) {
    // On the first iteration, key will be "x" and value will be 1
    // On the second iteration, key will be "y" and value will be 2
}

// 只遍历键或值
[...m.keys()]     // => ["x", "y"]: just the keys
[...m.values()]   // => [1, 2]: just the values
[...m.entries()]  // => [["x", 1], ["y", 2]]: same as [...m]

// 也可以使用 forEach() 方法
m.forEach((value, key) => {  // note value, key NOT key, value
    // On the first invocation, value will be 1 and key will be "x"
    // On the second invocation, value will be 2 and key will be "y"
});
```

<br/>
<br/>

### WeakMap和WeakSet

WeakMap 类是 Map 类的变体（但不是真正的子类），它不会阻止其键值被垃圾回收。垃圾回收是 JavaScript 解释器回收不再可访问并且无法由程序使用的对象的内存过程。

`WeakMap()` 和 `Map()` 构造函数有一些不同：

- 它的键必须是对象或数组
- 原始值不受垃圾回收的限制，不能用作键。
- 它仅实现 get, set, hast, delete 方法。
- 它是不可迭代的。
- 它不实现 size 属性，因为随着对象被垃圾回收，它的大小可能随时更改。

WeakMap 的预期用途是允许将值与对象相关联而不会引起内存泄漏。

<br/>

WeakSet 实现了一组对象，这些对象不会阻止垃圾回收这些对象。

WeakSet 对象 与 Set 对象的区别与 WeakMap 与 Map 的区别相同：

- 不允许原始值作为成员。
- 仅实现 add, has, delete 方法。
- 没有 size 属性。

<br/>
<br/>

## 类型化数组和二进制数据

Typed Arrays and Binary Data

常规 JavaScript 数组可以具有任何类型的元素，并且可以动态增长或收缩。但它仍然与如 C 或 Java 的低级语言的数组类型有很大不同。ES6 中新增的类型化数组更接近那些语言的低级数组。从类型上讲，类型化数组不是数组（`Array.isArray()` 返回 false），但是它实现了所有数组方法和一些其他方法。

类型化数组在某些方面和常规数组有所不同：

- 它的元素都是数字。但与常规 JS 数字不同，它允许指定要存储在数组中的数字的类型和大小。
- 创建它时，必须指定其长度，并且该长度永远不会改变。
- 它的元素始终会初始化为 0。

<br/>
<br/>

### 类型化数组的类型

Typed Array Types

JS 没有定义 TypedArray 类。相反，有11种类型化数组，每重类型具有不同的元素类型和构造函数。

- `Int8Array()`：有符号的字节
- `Uint8Array()`：无符号的字节
- `Uint8ClampedArray()`：没有滚动的无符号字节
- `Int16Array()`：有符号的16位短整数
- `Uint16Array()`：无符号的16位短整数
- `Int32Array()`：有符号的32位整数
- `Uint32Array()`：无符号的32位整数
- `BigInt64Array()`：有符号的64位 BigInt 值
- `BigUint64Array()`：无符号的64位 BigInt 值
- `Float32Array()`：32位浮点值
- `Float64Array()`：64位浮点值

<br/>
<br/>

### 创建类型化数组

Creating Typed Arrays

创建一个类型化数组最简单的方法时调用相应的构造函数。

```js
let bytes = new Uint8Array(1024);    // 1024 bytes
let matrix = new Float64Array(9);    // A 3x3 matrix
let point = new Int16Array(3);       // A point in 3D space
let rgba = new Uint8ClampedArray(4); // A 4-byte RGBA pixel value
let sudoku = new Int8Array(81);      // A 9x9 sudoku board
```

<br/>
<br/>

### 使用类型化数组

Using Typed Arrays

读写类型化数组的元素：

```js
// Return the largest prime smaller than n, using the sieve of Eratosthenes
function sieve(n) {
    let a = new Uint8Array(n+1);         // a[x] will be 1 if x is composite
    let max = Math.floor(Math.sqrt(n));  // Don't do factors higher than this
    let p = 2;                           // 2 is the first prime
    while(p <= max) {                    // For primes less than max
        for(let i = 2*p; i <= n; i += p) // Mark multiples of p as composite
            a[i] = 1;
        while(a[++p]) /* empty */;       // The next unmarked index is prime
    }
    while(a[n]) n--;                     // Loop backward to find the last prime
    return n;                            // And return it
}
```

<br/>
<br/>

### 类型化数组的方法和属性

Typed Array Methods and Properties

除了标准的数组方法外，类型化数组还实现了一些自己的方法。

```js
let bytes = new Uint8Array(1024);        // A 1K buffer
let pattern = new Uint8Array([0,1,2,3]); // An array of 4 bytes
bytes.set(pattern);      // Copy them to the start of another byte array
bytes.set(pattern, 4);   // Copy them again at a different offset
bytes.set([0,1,2,3], 8); // Or just copy values direct from a regular array
bytes.slice(0, 12)       // => new Uint8Array([0,1,2,3,0,1,2,3,0,1,2,3])
```

<br/>
<br/>

### 数据视图和内联性

DataView and Endianness

<br/>
<br/>

## 使用正则进行模式匹配

Pattern Matching with Regular Expressions

JS 正则表达式语法和许多其他编程语言所使用的语法非常相似。

<br/>
<br/>

### 定义正则表达式

Defining Regular Expressions

在 JS 中，正则表达式以 RegExp 对象呈现。正则表达式文字被指定为一对斜杠(`/`)字符内的内容。

```js
let pattern = /s$/;

// 使用 RegExp() 构造函数
let pattern = new RegExp("s$");
```

<br/>
<br/>

### 模式匹配的字符串方法

String Methods for Pattern Matching

一些方法：

- `search()`
- `replace()`
- `match()`
- `matchall()`
- `split()`

```js
"JavaScript".search(/script/ui)  // => 4
"Python".search(/script/ui)      // => -1

let s = "15 times 15 is 225";
s.replace(/\d+/gu, n => parseInt(n).toString(16))  // => "f times f is e1"

"7 plus 8 equals 15".match(/\d+/g)  // => ["7", "8", "15"]

// One or more Unicode alphabetic characters between word boundaries
const words = /\b\p{Alphabetic}+\b/gu; // \p is not supported in Firefox yet
const text = "This is a naïve test of the matchAll() method.";
for(let word of text.matchAll(words)) {
    console.log(`Found '${word[0]}' at index ${word.index}.`);
}

"123,456,789".split(",")           // => ["123", "456", "789"]
```

<br/>
<br/>

### RegExp类

本节介绍 `RegExp()` 构造函数，RegExp 实例的属性，以及由 RegExp 类定义的两个重要的模式匹配方法。

示例：

```js
// Find all five-digit numbers in a string. Note the double \\ in this case.
let zipcode = new RegExp("\\d{5}", "g");

let exactMatch = /JavaScript/;
let caseInsensitive = new RegExp(exactMatch, "i");
```

<br/>
<br/>

## 日期和时间类

Dates and Times

Date 类是用于处理日期和时间的 JS API。

Date API 的一个怪癖是一年的第一个月是数字 0，而一月的第一天是数字 1。如果省略了时间字段，则构造函数会将它们全部默认未 0， 并将时间设置到午夜。

请注意，当使用多个数字调用时，`Date()` 构造函数使用本地计算机设置的时区来解释它们。如果要以 UTC 时间，则可以使用 `Date.UTC()`。

如果将字符串传递给它，它将尝试解析该字符串作为日期和时间规范。

有了 Date 对象后，可以使用各种 get 和 set 方法查询和修改日期的年月日时分秒毫秒字段。有两种形式：一种式默认的本地时间，一种是 UTC 时间。

```js
// 使用 Date() 构造函数创建一个 Date 对象
// 没有实参，该对象代表当前日期和时间
let now = new Date();     // The current time

// 如果传递一个实参，则将该参数解释为自 1970 年依赖的毫秒数
let epoch = new Date(0);  // Midnight, January 1st, 1970, GMT

// 如果指定多个整数实参，则它们将被解释为本地时区中的年、月、日、小时、分钟、秒和毫秒
let century = new Date(2100,         // Year 2100
                       0,            // January
                       1,            // 1st
                       2, 3, 4, 5);  // 02:03:04.005, local time

// Midnight in England, January 1, 2100
// 如果打印日志，默认情况将在本地时区打印日期
// 如果要以 UTC 日期，则应使用 toUTCString() 或 toISOString() 将其显式转换为字符串
let century = new Date(Date.UTC(2100, 0, 1));

let century = new Date("2100-01-01T00:00:00Z");  // An ISO format date

let d = new Date();                  // Start with the current date
d.setFullYear(d.getFullYear() + 1);  // Increment the year
```

<br/>
<br/>

### 时间戳

Timestamps

JS 在内部将日期表示为整数——表示自 UTC 时间 1970 年 1 月 1 日 午夜以来的毫秒数。支持的最大整数为 8,640,000,000,000,000，因此 JS 在超过 270,000 年的时间内不会用完毫秒。

这些毫秒值有时被称为时间戳。

对于任何 Date 对象， 可使用 `getTime()` 和 `setTime()` 方法。

```js
// 向日期添加 30 秒
d.setTime(d.getTime() + 30000);

// 测量代码运行多久
let startTime = Date.now();
reticulateSplines(); // Do some time-consuming operation
let endTime = Date.now();
console.log(`Spline reticulation took ${endTime - startTime}ms.`);
```

<br/>

高分辨率时间戳(HIGH-RESOLUTION TIMESTAMPS)

对计算机而言，毫秒实际上是一个相对较长的时间，有时你可能希望以更高的精度测量经过的时间。`performance.now()` 函数允许这样做：它也返回毫秒级的时间戳，但包含毫秒的分数，它仅指示网页加载成功 或 Node 进程开始以来已花费多少时间。

performance 对象是较大的 Performance API 的一部分，该 API 不是由 ECMAScript 标准定义的，而是由 Web 浏览器和 Node 实现的。

```js
// 为了在 Node 中使用 performance 对象，必须使用以下命令导入它
const { performance } = require("perf_hooks");
```

<br/>
<br/>

### 日期算术

Date Arithmetic

日期对象也可以进行算术比较操作。

```js
let d = new Date();
d.setMonth(d.getMonth() + 3, d.getDate() + 14);
```

<br/>
<br/>

### 格式化和解析字符串

Formatting and Parsing Date Strings

Date 类定义了许多用于将 Date 对象转换为字符串的不同方法。

```js
let d = new Date(2020, 0, 1, 17, 10, 30); // 5:10:30pm on New Year's Day 2020
d.toString()  // => "Wed Jan 01 2020 17:10:30 GMT-0800 (Pacific Standard Time)"
d.toUTCString()         // => "Thu, 02 Jan 2020 01:10:30 GMT"
d.toLocaleDateString()  // => "1/1/2020": 'en-US' locale
d.toLocaleTimeString()  // => "5:10:30 PM": 'en-US' locale
d.toISOString()         // => "2020-01-02T01:10:30.000Z"
```

还有一个静态的 `Date.parse()` 方法，该方法将字符串作为实参，尝试将其解析为日期和时间，并返回时间戳描述日期。

<br/>
<br/>

## Error类

Error Classes

JS 的 throw 和 catch 语句可以抛出并捕获任何 JS 值，包括原始值。没有必须用于发出错误的异常类型。JS 确实定义了一个 Error 类，但是传统上是使用 throw 发出错误信号时使用 Error 的实例或子类。使用 Error 对象的一个理由是，当创建一个 Error 时，它会捕获 JS 堆栈的状态，并且如果未捕获到异常，则堆栈跟踪将于错误消息一起显示，这将有助于调试问题。

错误对象有两个属性：message 和 name，和一个 `toString()` 方法。

- `message` 属性的值传递给 `Error()` 构造函数，并在必要时转换为字符串。
- `name` 属性，对于使用 `Error()` 创建的错误对象，它始终为 Error。
- `toString()` 方法仅返回 name 属性的值后跟冒号和空格以及 message 属性的值。

<br/>

尽管它不是 ECMAScript 标准的一部分，但 Node 和所有现代浏览器还在 Error 对象上定义了 stack 属性。此属性的值是多行字符串，其中包含创建 Error 对象时的 JS 调用堆栈的跟踪信息。当捕获到意外错误时，这对于记录日志很有用。

除了 Error 类，JS  还定义了许多子类，这些子类用于表示 ECMAScript 定义的特定类型的错误。这些子类如下：

- EvalError
- RangeError
- ReferenceError
- SyntaxError
- TypeError
- URIError

<br/>

应该随意定义自己的错误子类，以最好地封装自己程序的错误条件。请注意，可以不受限于名称和消息两个属性。如果创建子类，则可以定义新属性以提供错误详细信息。

例如，处理 HTTP 请求，则可能需要定义一个 HTTPError 类，该类具有一个 status 属性，该属性保存失败请求的 HTTP 状态代码。

```js
class HTTPError extends Error {
    constructor(status, statusText, url) {
        super(`${status} ${statusText}: ${url}`);
        this.status = status;
        this.statusText = statusText;
        this.url = url;
    }

    get name() { return "HTTPError"; }
}

let error = new HTTPError(404, "Not Found", "http://example.com/");
error.status        // => 404
error.message       // => "404 Not Found: http://example.com/"
error.name          // => "HTTPError"
```

<br/>
<br/>

## JSON序列化和解析

JSON Serialization and Parsing

当程序需要保存数据或通过网络向其他程序传输数据时，它必须将其内存中的数据结构转换为可以保存或传输的字节或字符，然后再进行解析以恢复原来的内存中的数据结构。这种将数据结构转换为字节(byte)或字符(character)的过程被称为序列化(serialization 或 marshaling)。

在 JS 中序列化数据使用称为 JSON(JavaScript Object Notation) 的序列化格式。顾名思义，这种格式使用 JS 对象和数组字面语法，将由对象和数组组成的数据结构转换为字符串。JSON 支持原始数字和字符串，也支持真值、假值和空值，以及由这些原始值组成的数组和对象。JSON 不支持其他 JS 类型，如 Map、Set、RegExp、Date 和类型化数组。

尽管如此，JSON 已经被证明是一种非常通用的数据格式，甚至在非 JS 的程序中也被普遍使用。

JS 通过 `JSON.stringify()` 和 `JSON.parse()` 这两个函数来支持 JSON 的序列化和反序列化。

```js
let o = {s: "", n: 0, a: [true, false, null]};
let s = JSON.stringify(o);  // s == '{"s":"","n":0,"a":[true,false,null]}'
let copy = JSON.parse(s);   // copy == {s: "", n: 0, a: [true, false, null]}
```

<br/>

当数据被序列化为 JSON 格式时，JSON 是 JS 的子集，其结果是有效的 JS 源代码，其表达式被评估为原始数据结构的一个副本。

如果你在 JSON 字符串前加上 ` var data = `，并将结果传给 `eval()`，你会得到一个分配给变量 data 的原始数据副本。然而，你永远不应该这样做，因为这是一个巨大的安全漏洞——如果一个攻击者可以在 JSON 文件中注入任意的 JS 代码，攻击者就可以使你的程序运行他的代码。直接使用 `JSON.parse()` 来解析 JSON 格式的数据会更快更安全。

JSON 有时被用作人类可读的配置文件格式。请注意，JSON 格式是一个非常严格的 JS 子集。注释是不允许的，属性名称必须使用双引号括起来，即使 JS 也没有这样要求。

<br/>
<br/>

### JSON定制

JSON Customizations

如果 `JSON.stringify()` 被要求序列化一个 JSON 格式不支持的值，它会查看该值是否有一个 `toJSON()` 方法。如果有，它会调用该方法，然后将返回值字符串代替原始值。

Date 对象实现了 `toJSON()`，它返回与 `toISOString()` 方法一样的字符串。这意味着，如果你序列化一个包含 Date 的对象，它将自动转换为字符串。当你解析序列化的字符串时，重新创建的数据结构将与你开始时的数据结构不完全相同，因为它将有一个字符串，而原始对象是一个日期。

如果你需要重新创建 Date 对象，你可以传递一个 reviver 函数作为 `JSON.parse()` 的第二个参数。

```js
let data = JSON.parse(text, function(key, value) {
    // Remove any values whose property name begins with an underscore
    if (key[0] === "_") return undefined;

    // If the value is a string in ISO 8601 date format convert it to a Date.
    if (typeof value === "string" &&
        /^\d\d\d\d-\d\d-\d\dT\d\d:\d\d:\d\d.\d\d\dZ$/.test(value)) {
        return new Date(value);
    }

    // Otherwise, return the value unchanged
    return value;
});
```

`JSON.stringify()` 也允许通过传递一个数组或函数作为可选的第二个参数来定制其输出。

```js
// Specify what fields to serialize, and what order to serialize them in
let text = JSON.stringify(address, ["city","state","country"]);

// Specify a replacer function that omits RegExp-value properties
let json = JSON.stringify(o, (k, v) => v instanceof RegExp ? undefined : v);
```

你应该明白你是在定义一个自定义的数据格式，并牺牲了与 JSON 兼容的工具和语言的大型生态系统的可移植性和兼容性。

<br/>
<br/>

## 国际化API

The Internationalization API

JS 的国际化 API 包括三个类，它们允许我们以适当地方式格式化数字、日期和时间，并以适当地方式比较字符串。这些类不是 ECMAScript 的一部分，但被定义为 ECMA402 标准的一部分，并得到了网络浏览器的良好支持。

- `Intl.NumberFormat`
- `Intl.DateTimeFormat`
- `Intl.Conllator`

国际化最重要的部分之一是显示已被翻译成用户语言的文本。

<br/>
<br/>

### 格式化数字

Formatting Numbers

世界各地的用户希望数字以不同的方式进行格式化。

<br/>
<br/>

### 格式化日期和时间

Formatting Dates and Times

<br/>
<br/>

### 比较字符串

Comparing Strings

<br/>
<br/>

## 控制台API

The Console API

你已经看到过 `console.log()` 函数的使用。在网络浏览器中，它可在开发者工具的控制台中来执行，这在调试的时候非常有用。在 NodeJS 中，`console.log()` 是一个通用的输出函数，并将其参数打印到标准输出流中。

Console API 定义了一些有用的函数，它不是 ECMAScript 标准的一部分，但它被浏览器和 Node 所支持。它定义了如下函数：

- `console.log()`：将参数转换为字符串，并输出到控制台。
- `console.debug()`, `console.info()`, `console.warn()`, `console.error()`：控制消息级别
- `console.assert()`：如果第一个参数是真实的（即断言通过），那么函数什么也不做。如果是假的或其他错误的值，那么其余的参数就会被打印出来。
- `console.clear()`：清除控制台。
- `console.table()`：用于产生表格输出。
- `console.trace()`：输出后有一个堆栈跟踪。
- `console.count()`：记录被调用的次数。
- `console.countReset()`：重置计数器。
- `console.group()`
- `console.groupCollapsed()`
- `console.groupEnd()`
- `console.time()`：记录被调用的时间，并不产生输出。
- `console.timeLog()`
- `console.timeEnd()`

<br/>
<br/>

### 使用控制台格式化输出

Formatted Output with Console

像 `console.log()` 这样的控制台函数，支持格式化字符串。通过使用两位的百分号序列：

- `%s`：字符串
- `%i`, `%d`：数字，然后截断为整数
- `%f`：数字
- `%o`, `%O`：视为对象，并显示属性名称和值
- `%c`：在网络浏览器中视为 CSS 样式的字符串，在 Node 中被简单地忽略。

<br/>
<br/>

## URL APIs

URL 类可以解析 URL，也允许修改现有的 URL。URL 类不是任何 ECMAScript 标准的一部分，但它可以在 Node 和除 IE 之外的浏览器中使用。

```js
// 使用 URL() 构造函数创建 URL 对象
let url = new URL("https://example.com:8000/path/name?q=term#fragment");
url.href        // => "https://example.com:8000/path/name?q=term#fragment"
url.origin      // => "https://example.com:8000"
url.protocol    // => "https:"
url.host        // => "example.com:8000"
url.hostname    // => "example.com"
url.port        // => "8000"
url.pathname    // => "/path/name"
url.search      // => "?q=term"
url.hash        // => "#fragment"
```

不常用的示例，包含用户和密码：

```js
let url = new URL("ftp://admin:1337!@ftp.example.com/");
url.href       // => "ftp://admin:1337!@ftp.example.com/"
url.origin     // => "ftp://ftp.example.com"
url.username   // => "admin"
url.password   // => "1337!"
```

设置某些属性：

```js
let url = new URL("https://example.com");  // Start with our server
url.pathname = "api/search";               // Add a path to an API endpoint
url.search = "q=test";                     // Add a query parameter
url.toString()  // => "https://example.com/api/search?q=test"
```

<br/>
<br/>

### 遗留的URL功能

Legacy URL Functions

有多种尝试来支持核心 JS 语言中的 URL 转义和取消转义。第一次尝试是全局定义的 `escaple()` 和 `unescaple()` 函数，这些函数现在已经被废弃了，但仍然被广泛实施。它们不应该被使用。

上面两个函数被废弃后，ECMAScript 引入了两对替代性的全局函数：

- `encodeURI()`：将一个字符串作为参数，并返回一个新的字符串，其中的非 ASCII 字符和某些 ASCII 字符被转义。
- `decodeURI()`：需要转义的字符首先被转换为 UTF-8 编码，然后该编码的每个字节被替换为 %xx 转义序列，xx 是两个十六进制的数字。
- `encodeURIComponent()`
- `decodeURIComponent()`

所有这些遗留功能的根本问题是，它们试图将一个单一的编码方案应用于 URL 的所有部分。而事实是，URL 的不同部分使用不同的编码。如果你想要一个正确的格式化的编码的 URL，解决方法是简单地使用 URL 类来处理你做的所有 URL 操作。

<br/>
<br/>

## 定时器

Timers

自 JS 诞生之初，网络浏览器就定义了两个函数：`setTimeou()` 和 `setInterval()`，允许程序要求浏览器在指定时间后调用某个函数，或在指定间隔内重复调用该函数。这些函数从未被标准化为核心语言的一部分，但它们可以在所有的浏览器和 Node 中使用，并且是 JS 标准库中事实上的一部分。

```js
setTimeout(() => { console.log("Ready..."); }, 1000);
setTimeout(() => { console.log("set..."); }, 2000);
setTimeout(() => { console.log("go!"); }, 3000);
```

如果省略了第二个参数，它的默认值是 0，但这并不意味着你指定的函数会被立即调用。相反，该函数被注册为尽快被调用。如果一个浏览器忙于处理用户输入或其他事件，可能需要 10 毫秒或更长事件才能被调用。

两个函数都会返回一个值，改值的实际类型并不重要，你应当把它当作一个不透明的值。你可以用这个值做的唯一的事情是把它传递给 `clearTimeout()`，以取消或停止这两个函数的执行。

```js
// Once a second: clear the console and print the current time
let clock = setInterval(() => {
    console.clear();
    console.log(new Date().toLocaleTimeString());
}, 1000);

// After 10 seconds: stop the repeating code above.
setTimeout(() => { clearInterval(clock); }, 10000);
```

<br/>

---

<br/>

# 迭代器和生成器

可迭代对象及其关联的迭代器是 ES6 的一个特性。数组、字符串、Set 和 Map 对象都是可迭代的。这意味着这些数据结构可以被 `for/of` 遍历循环。

<br/>

## 迭代器如何工作

How Iterators Work

`for/of` 循环和展开运算符(`...`)可与可迭代对象无缝配合，但是应该了解迭代工作的实际情况。需要理解三种独立的类型才能理解 JS 中的迭代。

- 可迭代对象：如数组等类型。
- 迭代器本身：它执行迭代。
- 一个迭代结果对象：该对象保存迭代的每个步骤的结果。

<br/>

任何对象具有特殊迭代器方法，并且该方法返回迭代对象，那么该对象为可迭代对象。内置可迭代数据类型的迭代器对象本身是可迭代的。迭代器对象 `next()` 方法，该方法返回迭代结果对象。迭代结果对象是具有 value 和 done 属性的对象。

<br/>
<br/>

## 实现可迭代对象

Implementing Iterable Objects

可迭代对象在 ES6 中非常有用，应该考虑使自己的数据类型在可以表示迭代的任何时候都可迭代。

为了使类可迭代，必须实现一个名为 `Symbol Symbol.iterator` 的方法。该方法必须返回一个具有 `next()` 方法的迭代器对象。并且 `next()` 方法必须返回具有 value 属性的 `and/or` 一个布尔 done 属性。

一个可迭代数组范围类：

```js
/*
 * A Range object represents a range of numbers {x: from <= x <= to}
 * Range defines a has() method for testing whether a given number is a member
 * of the range. Range is iterable and iterates all integers within the range.
 */
class Range {
    constructor (from, to) {
        this.from = from;
        this.to = to;
    }

    // Make a Range act like a Set of numbers
    has(x) { return typeof x === "number" && this.from <= x && x <= this.to; }

    // Return string representation of the range using set notation
    toString() { return `{ x | ${this.from} ≤ x ≤ ${this.to} }`; }

    // Make a Range iterable by returning an iterator object.
    // Note that the name of this method is a special symbol, not a string.
    [Symbol.iterator]() {
        // Each iterator instance must iterate the range independently of
        // others. So we need a state variable to track our location in the
        // iteration. We start at the first integer >= from.
        let next = Math.ceil(this.from);  // This is the next value we return
        let last = this.to;               // We won't return anything > this
        return {                          // This is the iterator object
            // This next() method is what makes this an iterator object.
            // It must return an iterator result object.
            next() {
                return (next <= last)   // If we haven't returned last value yet
                    ? { value: next++ } // return next value and increment it
                    : { done: true };   // otherwise indicate that we're done.
            },

            // As a convenience, we make the iterator itself iterable.
            [Symbol.iterator]() { return this; }
        };
    }
}

for(let x of new Range(1,10)) console.log(x); // Logs numbers 1 to 10
[...new Range(-2,2)]                          // => [-2, -1, 0, 1, 2]
```

<br/>

除了使类可迭代外，定义返回可迭代值的函数也非常有用。

可迭代对象和迭代器的一个关键特性是它们固有的惰性：当需要计算下一个值时，可以将计算推迟到实际需要改值时进行。

<br/>
<br/>

### 关闭一个迭代器

“Closing” an Iterator: The Return Method

在大多数操作系统中，打开文件以从其中读取文件的程序需要记住在完成读取后关闭这些文件。

<br/>
<br/>

## 生成器

Generators

生成器是一种使用强大的新 ES6 语法定义的迭代器，当要迭代的值不是数据结构的元素，而是计算结果时，此功能特别有用。

要创建生成器，必须首先定义一个生成器函数（使用 `function *` 来定义）。调用生成器函数时，它实际上并不执行函数主体，而是返回生成器对象。该生成器对象是一个迭代器。调用其 `next()` 方法会使生成器函数的主体从头开始运行，直到到达 `yield` 语句为止。

`yield` 语句是 ES6 新特性，类似于 `return` 语句。`yield` 语句的值成为迭代器上 `next()` 调用返回的值。

请注意，无法使用箭头函数语法编写生成器函数。

一个示例：

```js
// A generator function that yields the set of one digit (base-10) primes.
function* oneDigitPrimes() { // Invoking this function does not run the code
    yield 2;                 // but just returns a generator object. Calling
    yield 3;                 // the next() method of that generator runs
    yield 5;                 // the code until a yield statement provides
    yield 7;                 // the return value for the next() method.
}

// When we invoke the generator function, we get a generator
let primes = oneDigitPrimes();

// A generator is an iterator object that iterates the yielded values
primes.next().value          // => 2
primes.next().value          // => 3
primes.next().value          // => 5
primes.next().value          // => 7
primes.next().done           // => true

// Generators have a Symbol.iterator method to make them iterable
primes[Symbol.iterator]()    // => primes

// We can use generators like other iterable types
[...oneDigitPrimes()]        // => [2,3,5,7]
let sum = 0;
for(let prime of oneDigitPrimes()) sum += prime;
sum                          // => 17
```

<br/>

像常规函数一样，我们也可以在 from 表达式中定义生成器。

```js
const seq = function*(from,to) {
    for(let i = from; i <= to; i++) yield i;
};
[...seq(3,5)]  // => [3, 4, 5]
```

<br/>
<br/>

### 生成器示例

生成斐波那契数的生成器函数。请注意，下例生成器函数具有无限循环，并且永久产生值而不会返回。如果将它与展开运算符一起使用，它将循环播放知道内存耗尽且程序崩溃。请小心地使用它。

```js
function* fibonacciSequence() {
    let x = 0, y = 1;
    for(;;) {
        yield y;
        [x, y] = [y, x+y];  // Note: destructuring assignment
    }
}
```

<br/>
<br/>

### yield* 和递归生成器

yield* and Recursive Generators

`yield` 和 `yield*` 只能在生成器函数中使用。`yield*` 可用于任何种类的可迭代对象，包括使用生成器实现的可迭代对象。这意味着它允许我们定义递归生成器，例如在递归定义的树结构上进行简单的非递归迭代。

<br/>
<br/>

## 高级的生成器功能

Advanced Generator Features

生成器函数最常见的用途是创建迭代器，但是生成器的基本特性是它们允许我们暂停计算，产生中间结果，然后在以后恢复计算。这意味着生成器具有的功能超出了迭代器的功能。

<br/>
<br/>

### 生成器函数的返回值

The Return Value of a Generator Function

到目前位置，生成器函数还没有 `return` 语句。但是，像任何函数一样，生成器函数可以返回一个值。为了了解在这种情况下会发生什么，请回忆一下迭代时如何工作的。`next()` 函数的返回值是一个具有 value 属性和 done 属性的对象。如果生成器返回一个值，则对 next 的最终调用将返回一个同时具有 value 和 done 定义的对象。

```js
function *oneAndDone() {
    yield 1;
    return "done";
}

// The return value does not appear in normal iteration.
[...oneAndDone()]   // => [1]

// But it is available if you explicitly call next()
let generator = oneAndDone();
generator.next()           // => { value: 1, done: false}
generator.next()           // => { value: "done", done: true }
// If the generator is already done, the return value is not returned again
generator.next()           // => { value: undefined, done: true }
```

<br/>
<br/>

### yield表达式的值

The Value of a yield Expression

实际上，`yield` 是一个表达式，可以有一个值。

调用生成器的 `next()` 方法时，生成器函数将运行直至到达 `yield` 表达式。将评估 yield 关键字之后的表达式，该值将成为 `next()` 调用的返回值。此时，生成器函数在评估 yield 表达式的中间立即停止执行。下次调用生成器的 `next()` 方法时，传递给 `next()` 的参数成为已暂停的 yield 表达式的值。因此，生成器把 yield 的值返回给它的调用者，然后调用者通过 `next()` 将值传递给生成器。生成器和调用者是两个独立的执行流，来回传递值。

```js
function* smallNumbers() {
    console.log("next() invoked the first time; argument discarded");
    let y1 = yield 1;    // y1 == "b"
    console.log("next() invoked a second time with argument", y1);
    let y2 = yield 2;    // y2 == "c"
    console.log("next() invoked a third time with argument", y2);
    let y3 = yield 3;    // y3 == "d"
    console.log("next() invoked a fourth time with argument", y3);
    return 4;
}

let g = smallNumbers();
console.log("generator created; no code runs yet");
let n1 = g.next("a");   // n1.value == 1
console.log("generator yielded", n1.value);
let n2 = g.next("b");   // n2.value == 2
console.log("generator yielded", n2.value);
let n3 = g.next("c");   // n3.value == 3
console.log("generator yielded", n3.value);
let n4 = g.next("d");   // n4 == { value: 4, done: true }
console.log("generator returned", n4.value);
```

<br/>
<br/>

### 生成器的 return() 和 throw() 方法

The return() and throw() Methods of a Generator

除了使用 `next()` 向生成器提供输入之外，还可以通过生成器的 `return()` 和 `throw()` 方法来更改生成器内部的控制流。

<br/>
<br/>

### 关于生成器的最后说明

A Final Note About Generators

生成器是一个非常强大的通用控制结构。它们使我们能够使用 `yield` 暂停计算，并在以后任意时间使用任意输入值重新开始计算。可以使用生成器在单线程 JS 代码中创建一种写作线程系统。而且，即使某些函数调用实际上是易不的并且依赖于网络事件，也可以使用生成器来掩盖程序的异步部分，从而使代码显得顺序和同步。

尝试使用生成器代码执行这些操作会导致代码难以理解。但是，它已经成为过去时，唯一真正实用的用例时管理异步代码。为此，JS 现在具有 `async` 和 `await` 关键字，并且不再有任何理由以这种方式滥用生成器。

<br/>

---

<br/>

# 异步 JavaScript

大多数计算机程序都是异步的，这意味着在等待数据到达或某些事件发生时，它们常常不得不停止计算。web 浏览器中的 JS 程序时典型的事件驱动的，这意味着它们在实际执行任何操作之前等待用户点击。基于 JS 的服务器通常在执行任何操作之前等待客户机请求通过网络到达。

本章将介绍三种重要的语言特性，有助于简化异步代码的使用。

- `Promise` 是 ES6 中的新特性，是表示目前不可用结果的异步操作对象。
- 关键字 `async` 和 `await` 是在 ES2017 中引入的，通过允许将基于 Promise 的代码构造成同步的方式来简化异步编程。
- 在 ES2018 中引入了异步迭代器和 `for/await` 循环，允许使用简单的同步循环处理异步事件流。

具有讽刺意味的是，尽管 JS 为处理异步代码提供了这些强大的特性，但核心语言本身并没有异步的特性。

<br/>
<br/>

## 使用回调的异步编程

Asynchronous Programming with Callbacks

在最基本的层次上，JS 中的异步编程是通过回调来完成的。回调是你编写一个函数，然后传递给其他函数。当满足某些条件或发生某些（异步）事件时，其他函数调用（回调）你的函数。

<br/>
<br/>

### 计时器

Timers

当希望在经过一定时间后运行某些代码是一种最简单的异步类型。

```js
# 在 setTimeout() 调用后 60,000 ms 后，将调用一个假定的 checkForUpdates() 函数
# checkForUpdates() 是程序定义的一个回调函数。
# setTimeout() 是用于注册回调函数，并指定应该在什么异步条件下调用它的函数。
setTimeout(checkForUpdates, 60000);
```

<br/>
<br/>

### 事件

Events

客户端 JS 程序几乎都是由事件驱动的：它们通常不等待用户执行某种预定的计算，而是等待用户执行某些操作，然后响应用户的操作。事件驱动的 JS 程序在指定的上下文中为指定类型的事件注册回调函数，并且只要指定事件发生，浏览器就会调用这些函数。这些回调函数成为事件句柄或事件监听器。

<br/>
<br/>

### 网络事件

Network Events

JS 编程中异步的另一个常见来源是网络请求。

<br/>
<br/>

### Node中的回调和事件

Callbacks and Events in Node

NodeJS 服务端的 JS 环境是深度异步的额，定义了许多使用回调和事件的 API。

<br/>
<br/>

## Promises

Promise 是一种简化异步编程的核心语言特性。

简单的说，promise 只是使用回调的另一种方式。但是，使用它有实际的好处。基于回调的异步编程的一个真正的问题是，通常在回调内部嵌套多层回调，并且代码行缩进程度很高，以至于很难阅读。Promise 允许将这种嵌套的回调作为更线性的 Promise 链重新表达，该链往往更易于阅读和推理。

回调的另一个问题是，它们会使处理异常变得困难。如果异步函数引发异常，则该异常无法传播回异步操作的发起者。Promise 通过标准化处理异常的方式以及为异常通过 Promise 链正确传播的方式提供帮助。

请注意，promise 表示单个异步计算的未来结果。但是，它不能用于表示重复的异步计算。

<br/>
<br/>

## async和await

ES2017 引入了两个新的关键字（`async` 和 `await`）描述异步 JS 编程中的模式转变。这些关键字极大地简化了 Promises 的使用，使我们能够编写基于 Promise 的异步代码看起来像是等待网络响应或其他异步事件而阻塞的同步代码。

<br/>
<br/>

### await表达式

关键字 await 接受一个 Promise，并将其转换为返回值或引发的异常。

使用 await 的任何代码本身都是异步的。

```js
let response = await fetch("/api/user/profile");
let profile = await response.json();
```

<br/>
<br/>

### async函数

只能在使用 async 关键字声明的函数中使用 await 关键字。

```js
async function getHighScore() {
    let response = await fetch("/api/user/profile");
    let profile = await response.json();
    return profile.highScore;
}
```

<br/>
<br/>

### 等待多个Promises

Awaiting Multiple Promises

等待一组并发执行的异步函数。

```js
async function getJSON(url) {
    let response = await fetch(url);
    let body = await response.json();
    return body;
}

let [value1, value2] = await Promise.all([getJSON(url1), getJSON(url2)]);
```

<br/>
<br/>

### 异步执行的细节

Implementation Details

<br/>
<br/>

## 异步迭代

Asynchronous Iteration

因为单个 Promise 不适用于异步事件序列，所以不能对这些事物使用常规的 async 函数和 await 语句。

ES2018 提供了一个解决方案。异步迭代器基于 Promise，并且与 `for/of` 循环一起的新形式：`for/await`。

<br/>
<br/>

### for/await循环

粗略地说，异步迭代器产生一个 Promise，`for/await` 循环等待该 Promise 兑现，将兑现值分配给循环变量，然后运行循环体的主体。

```js
const fs = require("fs");

async function parseFile(filename) {
    let stream = fs.createReadStream(filename, { encoding: "utf-8"});
    for await (let chunk of stream) {
        parseChunk(chunk); // Assume parseChunk() is defined elsewhere
    }
}
```

<br/>
<br/>

### 异步迭代器

Asynchronous Iterators

异步迭代器和常规迭代器有两个重要的区别。

- 一个异步可迭代对象以符号名称 `Symbol.asyncIterator` 而不是 `Symbol.iterator` 实现一个方法。
- 异步迭代器的 `next()` 方法返回解析为迭代器结果对象的 Promise，而不是直接返回迭代器结果对象。

使用异步迭代器，可以异步选择何时结束迭代。

<br/>
<br/>

### 异步生成器

Asynchronous Generators

实现迭代器最简单的方法是使用生成器。异步迭代器也是如此，可以使用声明为异步的生成器函数来实现。异步生成器具有异步和生成器特性：可以像在常规异步函数中使用 await，并且可以像在常规生成器中一样使用 yield。但是，产生的值会自动包装在 Promise 中。

```js
// A Promise-based wrapper around setTimeout() that we can use await with.
// Returns a Promise that fulfills in the specified number of milliseconds
function elapsedTime(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

// An async generator function that increments a counter and yields it
// a specified (or infinite) number of times at a specified interval.
async function* clock(interval, max=Infinity) {
    for(let count = 1; count <= max; count++) { // regular for loop
        await elapsedTime(interval);            // wait for time to pass
        yield count;                            // yield the counter
    }
}

// A test function that uses the async generator with for/await
async function test() {                       // Async so we can use for/await
    for await (let tick of clock(300, 100)) { // Loop 100 times every 300ms
        console.log(tick);
    }
}
```

<br/>
<br/>

### 实现异步迭代器

Implementing Asynchronous Iterators

除了使用异步生成器来实现异步迭代器外，还可以通过使用 `Symbol.asyncIterator()` 方法定义一个对象来直接实现它们。

```js
function clock(interval, max=Infinity) {
    // A Promise-ified version of setTimeout that we can use await with.
    // Note that this takes an absolute time instead of an interval.
    function until(time) {
        return new Promise(resolve => setTimeout(resolve, time - Date.now()));
    }

    // Return an asynchronously iterable object
    return {
        startTime: Date.now(),  // Remember when we started
        count: 1,               // Remember which iteration we're on
        async next() {          // The next() method makes this an iterator
            if (this.count > max) {     // Are we done?
                return { done: true };  // Iteration result indicating done
            }
            // Figure out when the next iteration should begin,
            let targetTime = this.startTime + this.count * interval;
            // wait until that time,
            await until(targetTime);
            // and return the count value in an iteration result object.
            return { value: this.count++ };
        },
        // This method means that this iterator object is also an iterable.
        [Symbol.asyncIterator]() { return this; }
    };
}
```

<br/>

---

<br/>

# 元编程

本章涵盖了一些高级的 JS 特性，这些特性在日常编程中并不常用，但对于编写可重用库的程序员来说可能很有价值，对于那些想要修改 JS 对象行为细节的人来说也很有兴趣。

这里描述的许多特性可以粗略描述为 **元编程**。如果常规编程是编写代码来操作数据，那么元编程就是编写代码来操作其他代码。

本章涉及的元编程主题包括：

- 控制对象属性的可枚举性、可删除性和可配置性。
- 控制对象的可扩展性，并创建密封和冻结的对象。
- 查询和设置对象的原型。
- 用众所周知的符号微调类型的行为。
- 用模板标签函数创建领域特定语言(DSL, domain-specific languages)。
- 用代理控制对象行为。

<br/>

---

<br/>

# 浏览器中的JavaScript

JavaScript 创建于 1994 年，其明确的目的就是为浏览器显示的文档赋予动态行为。随着时间的发展，今天，Web 对于 JavaScript 程序员而言已经是一个完善的应用开发平台。浏览器提供了包括文本、图形、视频、音频、网络、存储和线程。

客户端 JavaScript 指的是在浏览器中运行的 JavaScript 代码。服务器端 JavaScript 指的是运行在服务器上的程序。

本章内容：

- 控制文档内容和样式
- 确定文档元素在屏幕上的位置
- 创建可重用的用户界面组件
- 绘制图形
- 播放和生成声音
- 管理浏览器导航和历史记录
- 通过网络交换数据
- 在用户的计算机上存储数据
- 用线程执行并发计算

<br/>
<br/>

## Web编程基础

本节介绍如何编写 Web 应用中的 JavaScript 程序，如何将这些程序加载到浏览器，以及如何获取输入、产生输出，如何运行响应事件的异步代码。

<br/>
<br/>

### HTML中的script标签

JavaScript in HTML script Tags

浏览器显示了 HTML 文档。如果想让浏览器执行 JS 代码，必须在 HTML 文档中包含（或引用）相应代码，这就需要用到 `<script>` 标签。

```html
<!DOCTYPE html>                 <!-- This is an HTML5 file -->
<html>                          <!-- The root element -->
<head>                          <!-- Title, scripts & styles can go here -->
<title>Digital Clock</title>
<style>                         /* A CSS stylesheet for the clock */
#clock {                        /* Styles apply to element with id="clock" */
  font: bold 24px sans-serif;   /* Use a big bold font */
  background: #ddf;             /* on a light bluish-gray background. */
  padding: 15px;                /* Surround it with some space */
  border: solid black 2px;      /* and a solid black border */
  border-radius: 10px;          /* with rounded corners. */
}
</style>
</head>
<body>                    <!-- The body holds the content of the document. -->
<h1>Digital Clock</h1>    <!-- Display a title. -->
<span id="clock"></span>  <!-- We will insert the time into this element. -->
<script>
// Define a function to display the current time
function displayTime() {
    let clock = document.querySelector("#clock"); // Get element with id="clock"
    let now = new Date();                         // Get current time
    clock.textContent = now.toLocaleTimeString(); // Display time in the clock
}
displayTime()                    // Display the time right away
setInterval(displayTime, 1000);  // And then update it every second.
</script>
</body>
</html>
```

<br/>

虽然 JS 代码可以直接嵌入 `<script>` 标签中，但更常见的方式是使用它的 `src` 属性来指定包含 JS 代码文件的 URL。

```html
<script src="scripts/digital_clock.js"></script>
```

使用 src 属性有很多好处，也建议这样使用。

<br/>

脚本运行时机：同步(async)和延迟(defer)。

在浏览器引入 JS 语言之初，还没有任何 API 可以用来遍历和操作已经渲染好的文档的结构和内容。JS 代码能够影响文档内容的唯一方法，就是在浏览器加载文档的过程中动态生成内容。为此，要使用 `document.write()` 方法在脚本所在的位置向 HTML 中注入文本。

使用 `document.write()` 已经不再被认为是好的风格，但事实上，它的存在意味着当 HTML 解析器遇到 script 元素时，它必须默认运行脚本，只是为了确保在继续解析和渲染文档之前不输出任何 HTML。这可能会极大地减慢网页的解析和渲染速度。

幸运的是，这种默认的同步或阻塞的脚本执行模式并不是唯一的选择。`<script>` 标签可以有 defer 和 async 属性，它们使脚本以不同的方式执行。

```html
<script defer src="deferred.js"></script>
<script async src="async.js"></script>
```

两个属性都明确的告诉浏览器，链接的脚本不使用 `document.write()` 来生成 HTML 输出，因此，浏览器可以在下载脚本的同时继续解析和渲染文档。

注意，延迟的脚本是按照它们在文档中出现的顺序运行的。异步脚本在加载时运行，这意味着它们可能不按顺序执行。

默认情况下，带有 `type="module"` 属性的脚本回在文档加载完毕后执行，就像它们有一个defer属性一样。你可以用 asyncs 属性覆盖这个默认值，这将使代码在模块及其所有依赖项加载完毕后立即执行。

一个替代 async 和 defer 属性的简单方法，特别是对于直接包含在 HTML 中的代码，是简单地把你的脚本放在 HTML 文件的最后。这样，脚本在运行时就可以知道它之前的文档内容已经被解析并准备好被操作了。

按需加载脚本。有时，你可能有一些 JS 代码，在文档第一次加载时不使用，只有在用户采取某些行动时才需要（如点击按钮等）。

如果你不使用模块，你可以按需加载一个 JS 文件，只需要在你的文档中加入一个 script 标签。

```js
// Asynchronously load and execute a script from a specified URL
// Returns a Promise that resolves when the script has loaded.
function importScript(url) {
    return new Promise((resolve, reject) => {
        let s = document.createElement("script"); // Create a <script> element
        s.onload = () => { resolve(); };          // Resolve promise when loaded
        s.onerror = (e) => { reject(e); };        // Reject on failure
        s.src = url;                              // Set the script URL
        document.head.append(s);                  // Add <script> to document
    });
}
```

上面的 `importScript()` 函数使用 DOM APIs 创建一个新的 script 标签，并将其添加到文档的 `head` 中。它使用事件处理程序来确定脚本何时加载成功或加载失败。

<br/>
<br/>

### 文档对象模型

The Document Object Model

客户端 JavaScript 编程中最重要的一个对象就是 **文档对象**，它代表浏览器窗口或标签页中显式的 HTML 文档。用于操作 HTML 文档的 API 被称为 **文档对象模型**（DOM, Document Object Model）。

HTML 文档包含一组相互嵌套的 HTML 元素，构成了一棵树。下面是一个简单的示例。

```html
<html>
  <head>
    <title>Sample Document</title>
  </head>
  <body>
    <h1>An HTML Document</h1>
    <p>This is a <i>simple</i> document.
  </body>
</html>
```

<br/>

DOM API 与 HTML 文档的这种树形结构可谓一一对应。文档中的每个 HTML 标签都有一个对应的 JavaScript Element 对象，而文档中的每一行文本也都有一个与之对应的 Text 对象。

Element 类、Text 类和 Document 类，都是一个更通用的 Node 类的子类。各种 Node 对象组合成一个树形结构，JavaScript 可以使用 DOM API 对其进行查询和遍历。

[](https://raw.githubusercontent.com/zhang21/images/master/cs/javascript/15-1.png)

<br/>

DOM API 包含创建新 Element 和 Text 节点的方法，也包含把它们作为其他 Element 对象的孩子插入文档的方法。还有用来在文档中移动元素和删除的方法。服务器端应用可通过 `console.log()` 产生纯文本输出，而客户端 JavaScript 应用则可以使用 DOM API 通过构建或操作文档树产生格式化的 HTML 输出。

每个 HTML 标签类型都有一个与之对应的 JavaScript 类，而文档中出现的每个标签在 JavaScript 中都有对应类的一个实例表示。例如，`<body>` 标签由 `HTMLBodyElement` 的实例表示。JavaScript 中这些元素对象都有与 HTML 标签的属性对应的属性。

<br/>
<br/>

### 浏览器中的全局对象

The Global Object in Web Browsers

每个浏览器窗口（window）或标签（tab）有一个全局对象。所有在该窗口运行的 JS 代码都共享这一单元的全局对象。一个文档（document）中的所有脚本和模块都共享一个全局对象。

全局对象是定义 JS 标准库的地方，如 `parseInt()` 函数、Math 对象、Set 类等。在浏览器中，全局对象还包含各种网络 API 的主要入口。例如， 文档属性（document property）代表当前显示的文档，`fetch()` 方法发出 HTTP 网络请求，`Audio()` 构造函数允许 JS 程序播放声音。

在浏览器中，全局对象承担着双重职责：除了定义内置类型和函数外，它还代表了当前浏览器的窗口，并定义了历史（history）和 innerWidth 属性，前者代表窗口的浏览历史，后者则是窗口的像素宽度。这个全局对象的一个属性被命名为 window，其值是全局对象本身。这意味着你可以在你的客户端代码中简单地输入 `window` 来引用全局对象。

当使用窗口的特定功能时，使用 `window.innerWidth` 比 `innerWidth` 更清楚。

<br/>
<br/>

### 脚本共享一个命名空间

Scripts Share a Namespace

对于模块，在模块顶层定义的常量、变量、函数和类对模块来说是私有的，除非它们被明确导出，在这种情况下，它们可以被其他模块选择性地导入。

然而，对于非模块脚本，情况则完全不同。如果一个脚本中的顶层代码定义了一个常量、变量、函数或类，那么这个声明对同一文档中的所有其他脚本都是可见的。因此，如果你没有使用模块，那么你文档中的独立脚本就会共享一个命名空间，并且表现得好像它们都是一个大脚本的一部分。这对小程序来说很方便，但对大程序来说，避免命名冲突的需要会成为问题，特别是当一些脚本是第三方库的时候。

总结一下：在模块中，顶层声明的作用域是模块，并且可以明确地导出。然而，在所有非模块脚本中，顶层声明的范围是包含文档的，并且声明被文档中的所有脚本所共享。较早的 var 和 function 是通过全局对象的属性共享的，较新的 const、let 和 class 声明也是共享的，具有相同的文档范围，但它们并不作为 JS 代码可以访问的任何对象的属性存在。

<br/>
<br/>

### JS程序的执行

Execution of JavaScript Programs

在客户端 JS 中，没有正式的程序定义，但我们可以说，一个 JS 程序由文档中所有 JS 代码组成。这些独立的代码位共享一个全局的窗口对象，这使它们能够访问代表 HTML 文档的同一个底层文档对象。不属于模块的脚本还共享一个顶级命名空间。

如果一个网页包含一个嵌入的框架（使用 `<iframe>` 元素），嵌入文档中的 JS 代码与嵌入文档中的代码有不同的的全局对象和文档对象，它可以被视为一个独立的 JS 程序。不过请记住，对于一个 JS 程序的边界是什么，并没有正式的定义。

你可以认为 JS 程序的执行是分两个阶段进行的。

在第一个阶段，文档内容被加载，script 元素的代码被运行。脚本通过按照它们在文档中出现的顺序运行，尽管这个默认顺序可以通过我们描述的 async 和 defer 属性来修改。任何一个脚本中的 JS 代码都是从上到下运行的，当然也会受到 JS 的条件语句、循环语句和其他控制语句的影响。

一旦文档加载完毕，所有的脚本运行完毕，JS 的执行就进入了第二个阶段。这个阶段是异步的，是事件驱动的。如果一个脚本要参与第二个阶段，那么它在第一阶段必须做的一件事就是至少注册一个事件处理程序（event handler）或其他回调函数，这些函数将被异步地调用。在这个事件驱动的第二阶段，浏览器调用事件处理函数和其他回调函数，以响应异步发生的事件。事件处理程序最常被调用，以响应用户输入，但也可能被网络活动、文档和资源加载中的错误所触发。

在事件驱动阶段最先发生的一些事件是 DOMContentLoaded 和 load 事件。DOMContentLoaded 是在 HTML 文档被完全加载和解析后触发的。当文档的所有外部资源（如图片）也被完全加载时，load 事件就触发了。JS 程序经常使用这些事件中的一个作为触发器或启动信号。

经常可以看到这样的程序，它的脚本定义了一些功能，但除了注册一个事件处理函数在事件驱动的执行阶段开始时被 load 事件触发外，没有采取任何行动。

一个 JS 程序的加载阶段相对较短，一旦文档被加载，事件驱动阶段将持续到文档被浏览器显示为止。因为这个阶段时异步和事件驱动的，所以可能会出现长时间的不活动，没有执行任何 JS，而由用户或网络事件引发的突发活动则是其中的亮点。

<br/>

客户端 JS 线程模型。JS 是一种单线程语言，单线程执行使得编程更加简单：你可以在保证两个事件处理程序不会同时运行的情况下编写代码。你可以操作文档内容，知道没有其他线程在同一时间试图修改它，而且你在编写 JS 代码时永远不需要单线锁、死锁和竞赛问题。

单线程执行意味着在脚本和事件处理程序执行时，网络浏览器会停止对用户输入的响应。这给 JS 程序员带来了负担：这意味着 JS 脚本和事件处理程序的运行时间不能太长。如果一个脚本执行一个计算密集型的任务，它就会给文档的加载带来延迟，在脚本完成之前，用户不会看到文档内容。如果一个事件处理程序执行了一个计算密集型的任务，浏览器可能会变得没有反应，可能导致用户认为它已经崩溃了。

网络平台定义了一种叫做 web worker 的受控的并发形式，它是一个后台线程，用于执行计算密集型任务而不冻结用户界面。在 web worker 线程中运行的代码不能访问文档内容，不能与主线程或其他 workers 进行通信，因此主线程无法检测到并发性，web workers 也不会改变 JS 程序的基本单线程执行模型。

<br/>

客户端 JS 时间线。我们已经看到，JS 程序开始于一个脚本执行阶段，然后过渡到一个事件处理阶段。这两个阶段可以分为以下步骤：

1. 浏览器创建一个 Document 对象，并开始解析网页，在解析 HTML 元素及其文本内容时，向文档中添加 Eletement 对象和 Text 节点。在这个阶段， `document.readyState` 属性的值时 loading。
2. 当 HTML 解析器遇到一个没有任何 async、defer 或 `type="module"` 属性的 script 标签时，它会将该脚本添加到文档中，然后执行该脚本。脚本是同步执行的，HTML 解析器在脚本下载和运行时暂停。像这样的脚本可以使用 `document.write()` 在输入流中插入文本，当解析器恢复时，这些文本将成为文档的一部分。
3. 当解析器遇到了一个设置了 async 属性的 script 元素时，它就开始下载脚本文件并继续解析文档。脚本在下载后会尽快执行，但解析器不会停止并等待它下载。异步脚本不能使用 `document.write()` 方法。
4. 当文档被完全解析后，`document.readyState` 属性会变为 interactive。
5. 任何设置了 defer 属性的脚本会按照它们在文档中出现的顺序执行。异步脚本也可以在这个时候被执行。延迟的脚本可以访问完整的文档，它们不饿能使用 `document.write()` 方法。
6. 浏览器在文档对象上触发了一个 DOMContentLoaded 事件。这标志着从同步的脚本执行阶段过渡到异步的、事件驱动的程序执行阶段。但是，请注意，此时可能仍有异步脚本尚未执行。
7. 此时，文档已被完全解析，但浏览器可能仍在等待其他内容的加载（如图片）。当所有这些内容都加载完毕，并且所有的异步脚本都已加载并执行，`document.readyState` 属性将变为 complete，浏览器在窗口对象上触发一个 load 事件。
8. 从这一点出发，事件处理程序被异步调用，以响应用户输入事件、网络事件和定时器到期等。

<br/>
<br/>

### 程序的输入和输出

Program Input and Output

向任何程序一样，客户端的 JS 程序处理输入数据以产生输出数据。有多种输入方式可供选择：

- 文档内容本身，JS 代码可以通过 DOM API 访问。
- 用户输入，以事件的形式出现（如鼠标点击）。
- 被显示的文档的 URL 对客户端的 JS 来说是可用的，即 `document.URL`。如果把这个字符串传递给 `URL()` 构造函数，你可以很容易地访问 URL 的路径、查询和片段部分。
- HTTP Cookie 请求头的内容可以作为 `document.cookie` 提供给客户端代码。Cookie 通常被服务器端代码用于维护用户会话，但客户端代码在必要时也可以读取和写入它们。
- 全局导航器属性提供了对浏览器、它运行的操作系统以及各自能力的信息的访问。例如，`nabigator.userAgent` 是一个标识浏览器的字符串。

客户端 JS 通常回在需要时产生输出，通过 DOM API 操纵 HTML 文档，或者使用 React 等高层框架来操纵文档。客户端代码也可以使用 `console.log()` 和相关方法来产生输出，这在调试时很有用。

<br/>
<br/>

### 程序错误

Program Errors

与直接运行在操作系统之上的应用程序不同，浏览器中的 JS 程序并不能真正崩溃。如果你的 JS 程序在运行时发生了异常，而你又没有一个 `catch` 语句来处理它，那么错误信息将显示在开发者控制台中，但任何已经注册的事件处理程序将继续运行并对事件做出响应。

如果你想定义一个最后的错误处理程序，当这种未捕获的异常发生时被调用，将 Window 对象的 onerror 属性设置为一个错误处理函数。当一个未捕获的异常在调用栈中一路传播，并且错误信息即将显示在开发者控制台时，`window.onerror` 函数将被调用，并带有三个字符串参数。

- 第一个参数时一个描述错误的消息。
- 第二个参数是一个字符串，包含导致错误的 JS 代码的 URL。
- 第三个参数时错误发生在文档中的行号。

<br/>

当一个 Promise 被拒绝，并且没有 `.catch()` 函数来处理它时，这种情况很像一个未处理的异常：一个未预料到的错误或你程序中的逻辑错误。你可以通过定义 `window.onunhandledrejection` 函数或使用 `window.addEventListener()` 为 unhandledrejection 事件注册一个处理程序来检测。传递给这个处理程序的事件对象将有一个 promise 属性，其值是被拒绝的 promise 对象，还有一个 reason 属性，其值是传递给 `.catch()` 函数的东西。

通常没有必要定义 `onerror` 或 `onunhandledrejection` 处理程序，但如果你想向服务器报告客户端的错误，它作为一种遥测机制是相当有用的，这样你就可以获得用户浏览器中发生的错误信息。

<br/>
<br/>

### Web安全模式

The Web Security Model

网页可以在你的个人设备上执行任意的 JS 代码，这一事实具有明显的安全影响，浏览器供应商一直在努力平衡两个相互竞争的目标：

- 定义强大的客户端 API 以实现有用的 web 程序。
- 防止恶意代码读取或修改你的数据，损害你的隐私，诈骗你或浪费你的时间。

<br/>

作为一个 JS 程序员，你应该注意这些问题。

JS 不能做什么。浏览器对恶意代码的第一道防线是，它们根本不支持某些功能。如，客户端的 JS 不提供任何方法来写入或删除任意文件，这意味着一个 JS 程序不能删除数据或植入病毒。

同样，客户端的 JS 也不具备通用的网络功能。HTTP 或 WebSocket 这些 API 都不允许不经中介访问更广泛的网络。

同源策略（same-origin policy）。同源策略是对 JS 代码可以与那些网页内容互动的一种全面的安全限制。当一个网页包括 `<iframe>` 元素时，它通常会发挥作用。在这种情况下，同源策略规范了一个框架中的 JS 代码与其他框架内容的互动。具体来说，一个脚本只能读取与包含该脚本的文档具有相同起源的窗口和文档属性。

一个文档的来源被定义为加载该文件的 URL 的协议、主机和端口。从不同的网络服务器加载的文件有不同的来源。通过同一主机的不同端口加载的文档有不同的来源。http 和 https 协议加载的文档也有不同的来源，即使它们来自于同一个网络服务器。

脚本本身的来源与同源策略无关：重要的是嵌入该脚本的文档的来源。例如，由主机 A 托管的脚本，被包含在由主机 B 提供的网页中（使用 script 元素的 src 属性），该脚本的来源是主机 B，该脚本可以完全访问包含它的文档内容。

同源策略也适用于脚本化的 HTTP 请求。JS 代码可以向加载包含文档的 Web Server 发出任意的 HTTP 请求，但它不允许脚本与其他 Web 服务器进行通信（除非这些 Web 服务器选择加入 CORS）。

同源策略给使用多个子域名的大型网站带来了问题。例如，源为 `orders.example.com` 的脚本可以需要从 `example.com` 的文档中读取属性。为了支持这类多域名网站，脚本可以通过将 `document.domain` 设置为域名的后缀来改变它们的源。因此，脚本可以将 `document.domain` 设置未 `example.com` 来改变其源。

放宽同源策略的第二种技术是 **跨域资源共享**（CORS, Cross-Origin Resource Sharing），它允许服务器决定它们为哪些源提供服务。CORS 使用 `Origin` 请求头和 `Access-Control-Allow-Origin`  响应头扩展了 HTTP。它允许服务器使用同一个头来明确列出可以请求一个文件的来源，除非存在这些头，否则不会放松同源限制。

**跨站点脚本攻击**（XSS, Cross-Site Scripting）。是一个安全的术语，攻击者将 HTML 标签或脚本注入到目标网站中。客户端 JS 程序员必须了解并抵御跨站脚本。

如果一个网页动态地生成文档内容，并以用户提交的数据为基础，而没有首先通过删除任何嵌入的 HTML 标签来净化这些数据，那么它就容易受到跨站脚本攻击。

<br/>

一个栗子，它使用提供的名称来问候用户。

```js
<script>
let name = new URL(document.URL).searchParams.get("name");
document.querySelector('h1').innnerHTML = "Hello" + name;
</script>
```

请求的 URL 示例:

```url
# 要给正常的 url
http://www.example.com/greet.html?name=David
# "Hello David"

# 如果参数是这样呢？
name=%3Cimg%20src=%22x.png%22%20onload=%22alert(%27hacked%27)%22/%3E
# Hello <img src="x.png" onload="alert('hacked')"/>
```

图片加载后，onload 属性中的 JS 字符串被执行。全局的 `alert()` 函数显示一个模态对话框。一个对话框相对来说是良性的，但表明在此网站上执行任意代码是可能的，因为它显示的是没有经过消毒的 HTML。

跨站点脚本攻击之所以被称为跨站攻击，是因为涉及到不止一个网站。如果网站 B 能够说服用户点击该链接，他们将被带到网站 A，但该网站现在正在运行来自网站 B 的代码，该代码可能会玷污页面或导致其故障。更危险的是，恶意代码可能会读取网站 A 存储的 cookies（可能是账号或身份信息），并将这些数据送回网站 B。

一般来说，防止 XSS 攻击的方法是再使用任何不受信任的数据来创建动态文档内容之前，从这些数据中删除 HTML 标签。

```js
name = name
    .replace(/&/g, "&amp;")
    .replace(/</g, "&lt;")
    .replace(/>/g, "&gt;")
    .replace(/"/g, "&quot;")
    .replace(/'/g, "&#x27;")
    .replace(/\//g, "&#x2F;")
```

解决 XSS 的另一种方法是构建你的 Web 应用程序，使不受信任的内容总是在 `<iframe>` 中显示，并设置沙箱属性以禁用脚本和其他功能。

<br/>
<br/>

## Events

客户端 JS 程序使用异步事件驱动的编程模型。在这种编程方式中，只要文档或浏览器与之相关的一些元素或对象发生有趣的事情，网络浏览器就会产生一个事件。例如，用户将鼠标移动到一个超链接上，会产生一个事件。如果一个 JS 应用程序关心要给特定类型的键时，都会产生一个事件。如果一个 JS 应用程序关心一个特定类型的事件，它可以注册一个或多个函数，在该类型的事件发生时被调用。

在客户端 JS 中，事件可以发生在 HTML 文档中的任何元素上，这一事实使得浏览器的事件模型比 Node 的事件模型要复杂得多。

介绍一些重要的定义，这些定义有助于解释该事件模型：

- 事件类型（event type），这个字符串指定发生了什么类型的事件。例如 mousemove，意味着用户移动了鼠标。
- 事件目标（event target），这是事件发生的对象，或与事件相关的对象。当我们谈论一个事件时，必须同时指定类型和目标。
- 事件处理程序（event handler），或监听器（event listener），该函数处理或响应一个事件。
- 事件对象（event object），这个对象与特定的事件相关联，包含关于该事件的细节。
- 事件传播（event propagation），浏览器通过这个过程决定哪些对象要出发事件处理程序。

有些事件有与之相关的默认动作。例如，当点击事件发生在一个超链接上时，默认的动作是让浏览器跟踪该链接并加载一个新的页面。事件处理程序可以通过调用事件对象的一个方法来阻止这个默认动作。

<br/>
<br/>

### 事件类别

Event Categories

客户端 JS 支持如此多的事件类型，以至于本章不可能将它们全部涵盖。不过可以将事件分为一些类别，以说明所支持的事件的种类和范围。

- 依赖于设备的输入事件（device-dependent input events），这些事件直接与特定的输入设备相联系，如鼠标和键盘。
  - mousedown, mouseup, mousemove
  - touchstart, touchmove, touchend
  - keydown, keyup
- 与设备无关的输入事件（device-independent input events），这些事件不直接与特定的输入设备相联系。
- 用户界面事件（user interface events），UI 事件是更高层次的事件，通常发生在 HTML 表单上，为网络应用程序定义一个用户界面。
- 状态变化事件（state-change events），有些事件不是由用户活动直接触发的，而是由网络或浏览器活动触发的，并表示某种生命周期或状态相关的变化。
- 特定 API 事件（API-specific events），一些由 HTML 和相关规范定义的网络 API，包括他们自己的事件类型。

<br/>
<br/>

### 注册事件处理程序

Registering Event Handlers

有两种基本的方法来注册事件处理程序：

- 第一种，从网络的早期开始，是在作为事件目标的对象或文档元素上设置一个属性。
- 第二种，较新较普遍，是将处理程序传递给对象或元素的 `addEventListener()` 方法。

<br/>

设置事件处理程序属性（SETTING EVENT HANDLER PROPERTIES），事件处理程序属性的缺点是，它们是围绕着这样的假设而设计的：事件目标对于每种类型的事件最多只有一个处理程序。使用 `addEventListener()` 注册事件通常更好，因为这不会覆盖任何先前注册的处理程序。

```js
// Set the onload property of the Window object to a function.
// The function is the event handler: it is invoked when the document loads.
window.onload = function() {
    // Look up a <form> element
    let form = document.querySelector("form#shipping");
    // Register an event handler function on the form that will be invoked
    // before the form is submitted. Assume isFormValid() is defined elsewhere.
    form.onsubmit = function(event) { // When the user submits the form
        if (!isFormValid(this)) {     // check whether form inputs are valid
            event.preventDefault();   // and if not, prevent form submission.
        }
    };
};
```

<br/>

设置事件处理程序属性（SETTING EVENT HANDLER ATTRIBUTES），文档元素的事件处理程序属性也可以直接在 HTML 文件中定义相应的 HTML 标签的属性。这种技术在现代网络开发中通常是不受欢迎的，但它是可行的，而且在这里记录下来，因为你可能在现有的代码中仍然可以看到它。

当把事件处理程序定义为一个 HTML 属性时，该属性的值应该是一串 JS 代码。这段代码应该是事件处理函数的主体，而不是一个完整的函数声明。也就是说，你的 HTML 事件处理程序代码不应该被大括号包围并以函数关键字为前缀。

示例：

```html
<button onclick="console.log('Thank you');">Please Click</button>
```

<br/>

`addEventListener()`，任何可以成为事件目标的对象，这包括 Window 和 Document 以及所有的文档元素，都可以定义一个名为 `addEventListener()` 的方法，你可以用它来为该目标注册一个事件处理程序。

`addEventListener()` 需要三个参数。第一个是处理程序被注册的事件类型；第二个是当指定类型事件发生时应该被调用的函数；第三个是可选的布尔值或对象。

```html
<button id="mybutton">Click me</button>
<script>
let b = document.querySelector("#mybutton");
b.onclick = function() { console.log("Thanks for clicking me!"); };
b.addEventListener("click", () => { console.log("Thanks again!"); });
</script>
```

<br/>

`removeEventListener()` 方法从一个对象中删除一个事件处理函数。

```js
document.removeEventListener("mousemove", handleMouseMove);
document.removeEventListener("mouseup", handleMouseUp);
```

<br/>
<br/>

### 事件处理程序调用

Event Handler Invocation

一旦注册了一个事件处理程序，当指定对象上发生指定类型的事件时，浏览器就会自动调用它。

事件处理程序参数（EVENT HANDLER ARGUMENT），事件处理程序用一个事件对象作为其单一参数来调用的。事件对象的属性：

- type：发生事件的类型。
- target：发生该事件的对象。
- currentTarget：对于传播的事件，这个属性是当前事件处理程序所注册的对象。
- timeStamp：毫米级时间戳，代表事件发生的时间，但不代表绝对时间。
- isTrusted：如果事件由浏览器本身派发，这个属性为真；如果事件由 JS 代码派发，则为假。
- 特定种类的事件有额外的属性。如鼠标和指针事件，有 clientX 和 clientY 属性，指定事件发生时的窗口的坐标。

<br/>

事件处理程序上下文（EVENT HANDLER CONTEXT），当你通过设置一个属性来注册一个处理程序时，它看起来就像你在对象目标上定义了一个新方法：

```js
target.onclick = function() { /* handler code */ };
```

处理程序以目标作为其 this 值被调用，即是在使用 `addEventListener()` 注册时。然而，这对定义为箭头函数的处理程序不起作用：箭头函数总是具有与定义它们的范围相同的 this 值。

<br/>

处理程序的返回值（HANDLER RETURN VALUE），在现代 JS 中，事件处理程序不应该返回任何东西。你可能回在旧代码中看到返回值的事件处理程序。

反之浏览器执行默认操作的标准和首选方法是调用事件对象的 `preventDefault()` 方法。

<br/>

调用顺序（INVOCATION ORDER），一个事件目标可以为一个特定类型的事件注册一个以上的事件处理程序。当该类型的事件发生时，浏览器会按照注册的顺序调用所有的处理程序。

<br/>
<br/>

### 事件传播

Event Propagation

当事件的目标是 Window 对象或其它独立的对象时，浏览器对事件的反应仅仅是调用该对象上的适当的处理程序。然而，当事件的目标是一个文档或文档元素时，情况就比较复杂了。

大多数发生在文档元素上的事件都是冒泡的（bubble）。值得注意的例外是焦点（focus）、模糊（blur）和滚动（scroll）事件。文档元素上的加载（load）事件会冒泡，但它在文档对象上停止冒泡，不会传播到窗口对象上。

事件冒泡是事件传播的第三个阶段。目标对象本身的事件处理程序的调用是第二阶段。第一阶段，甚至在目标处理程序被调用之前就发生了，被称为捕获阶段。

事件捕获提供了一个机会，可以在事件被传递到目标之前偷看它们。捕获的事件处理程序可用于调试，也可用于下一节描述的事件取消技术一起使用，以过滤事件，使目标事件处理程序实践上从未被调用。事件捕获的一个常见用途是处理鼠标拖动，鼠标运动事件需要由被拖动的对象而不是文档元素来处理。

<br/>
<br/>

### 事件取消

Event Cancellation

浏览器会对许多用户事件做出反应，即使你的代码没有这样做：当用户在一个超链接上点击鼠标时，浏览器会跟随链接。如果一个 HTML 文本输入元素有键盘焦点，而用户输入了一个键，浏览器就会输入用户的输入。如果用户在触摸屏设备上移动手指，浏览器就会滚动。如果你为类似的事件注册了一个事件处理程序，你可以通过调用事件对象的 `preventDefault()` 方法来防止浏览器执行其默认动作。

取消与事件相关的默认动作只是事件取消的一种方式。我们也可以通过调用事件对象的 `stopPropagation()` 方法来取消事件的传播。如果在同一个对象上定义了其他的处理程序，这些处理程序的其余部分仍然会被调用，但是在调用 `stopPropagation()` 之后，任何其他对象上的事件处理程序都不会被调用。`stopPropagation()` 在捕获阶段，在事件目标本身，以及在冒泡阶段发挥作用。`stopImmediatePropagation()` 的作用与 `stopPropagation()` 类似，但是它也会阻止调用在同一个对象上注册的任何后续处理程序。

<br/>
<br/>

### 分派自定义事件

Dispatching Custom Events

客户端 JS 的事件 API 是一个相对强大的API，你可以用它来定义和调度你自己的事件。

可以用 `CustomEvent()` 构造函数创建你自己的事件对象，并把它传递给 `dispatchEvent()`。

```js
// Dispatch a custom event so the UI knows we are busy
document.dispatchEvent(new CustomEvent("busy", { detail: true }));

// Perform a network operation
fetch(url)
  .then(handleNetworkResponse)
  .catch(handleNetworkError)
  .finally(() => {
      // After the network request has succeeded or failed, dispatch
      // another event to let the UI know that we are no longer busy.
      document.dispatchEvent(new CustomEvent("busy", { detail: false }));
  });

// Elsewhere, in your program you can register a handler for "busy" events
// and use it to show or hide the spinner to let the user know.
document.addEventListener("busy", (e) => {
    if (e.detail) {
        showSpinner();
    } else {
        hideSpinner();
    }
});
```

<br/>
<br/>

## 文档脚本

Scripting Documents

客户端 JS 的存在是为了把静态的 HTML 文档变成互动的网络应用。因此，对网页内容进行脚本处理，确实是 JS 的核心目的。

每个窗口对象都有一个指向文档对象的文档属性。文档对象代码窗口的内容，它是本节的主题。然而，文档对象并不是独立存在的。它是 DOM 中用于表示和操作文档内容的核心对象。

DOM API 包括：

- 如何从一个文档中查询或选择个别元素。
- 如何遍历一个文档，以及如何找到任何文档元素的祖先、兄弟姐妹和后代。
- 如何查询和设置文档元素的属性。
- 如何查询、设置和修改文档的内容。
- 如何通过创建、插入和删除节点来修改文档的结构。

<br/>

### 选择文档元素

Selecting Document Elements

客户端 JS 程序经常需要操作文档的一个或多个元素（element）。全局文档属性指的是文档对象，而文档对象有 head 和 body 属性，分别指的是 `<head>` 和 `<body>` 标签的元素对象。但是，一个想要操作嵌入在文档中更深的元素的程序，必须以某种方式获得或选择指向这些文档元素的元素对象。

**用 CSS 选择器选择元素（SELECTING ELEMENTS WITH CSS SELECTORS）**。CSS 样式表有一个非常强大的语法，被称为选择器，用于描述文档中的元素或元素集。DOM 方法 `querySelector()` 和 `querySelectorAll()` 允许我们在文档中找到符合指定 CSS 选择器的一个或多个元素。

CSS 选择器可以通过 tag 名称，id 属性的值或 class 属性中的词来描述元素。

```js
div                     // Any <div> element
#nav                    // The element with id="nav"
.warning                // Any element with "warning" in its class attribute
```

`#` 字符用于基于 id 属性的匹配，`.` 字符用于基于 class 属性的匹配。也可以根据更一般的属性值来选择元素：

```js
p[lang="fr"]            // A paragraph written in French: <p lang="fr">
*[name="x"]             // Any element with a name="x" attribute

span.fatal.error        // Any <span> with "fatal" and "error" in its class
span[lang="fr"].warning // Any <span> in French with class "warning"
```

选择器也可以指定文档结构：

```js
#log span               // Any <span> descendant of the element with id="log"
#log>span               // Any <span> child of the element with id="log"
body>h1:first-child     // The first <h1> child of the <body>
img + p.caption         // A <p> with class "caption" immediately after an <img>
h2 ~ p                  // Any <p> that follows an <h2> and is a sibling of it
```

如果两个选择器用逗号隔开，这意味着我们已经选择了其中任何一个选择器相匹配的元素：

```js
button, input[type="button"] // All <button> and <input type="button"> elements
```

<br/>

如你所见，CSS 选择器允许我们通过 type, id, class, attributes 和在文档中的位置来引用文档中的元素。

`querySelector()` 方法接受一个 CSS 选择器字符串作为参数，并返回它找到的文档中第一个匹配的元素，如果没有匹配，则返回空。

```js
// Find the document element for the HTML tag with attribute id="apinner"
let spinner = document.querySelector("#spinner");
```

`querySelectorAll()` 类似，但是它返回文档中所有匹配的元素，而不是只返回第一个元素。它的返回值不是一个元素对象的数组，而是一个类似数组的对象，称为 NodeList。如果没有匹配，它将返回一个长度为 0 的 NodeList 对象。

`NodeList` 对象有一个长度属性，并且可以像数组一样进行索引，可以使用循环来迭代。通过使用 `Array.from()` 把它转换成一个真正的数组。

```js
// Find all Element objexts for <h1>, <h2>, and <h3> tags
let titles = document.querySelectorAll("h1, h2, h3");
```

`querySelector()` 和 `querySelectorAll()` 是由 Element class 以及 Document class 实现的。当在一个元素上被调用时，这些方法将只返回该元素的后代元素。

<br/>

注意，CSS 定义了 `::first-line` 和 `::first-letter` 的伪元素。在 CSS 中，这些匹配文本节点的一部分，而不是实际的元素。如果与 `querySelectorAll()` 或 `querySelector()` 一起使用，它们将无法匹配。另外，许多浏览器会拒绝返回 `:link` 和 `:visited` 伪类的匹配，因为这可能会暴露用户的浏览历史信息。

另一个基于 CSS 的元素选择方法是 `closest()`。这个方法由 Element class 定义，它接受一个选择器作为它的唯一参数。如果选择器与它所调用的元素相匹配，它就返回该元素。否则，它返回选择器匹配的最接近的祖先元素。如果没有匹配，则返回 null。

在某种意义上，`closest()` 与 `querySelector()` 相反。`closest()` 从一个元素开始，在树中寻找它上面的匹配，而 `querySelector()` 从一个元素开始，在树中寻找它下面的匹配。当你在文档树的高层注册一个事件处理程序时，`closest()` 就很有用。例如，如果你正在处理一个点击事件，你可能想知道它是否是一个点击超链接。事件对象会告诉你目标是什么，但这个目标可能是 一个链接里面的文本，而不是超链接 `<a>` 标签本身。

```js
// Find the closest enclosing <a> tag that has an href attribute.
let hyperlink = event.target.closest("a[href]");

// Return true if the element e is inside of an HTML list element
function insideList(e) {
    return e.closest("ul,ol,dl") !== null;
}
```

<br/>

相关的 `match()` 方法并不返回祖先或后代，它指示测试一个元素是否被一个 CSS 选择器所匹配，如果是则返回 true，否则返回 false。

```js
// Return true if e is an HTML heading element
function isHeading(e) {
    return e.matches("h1,h2,h3,h4,h5,h6");
}
```

<br/>

**其他元素选择方法（OTHER ELEMENT SELECTION METHODS）**。除了 `querySelector()` 和 `querySelectorAll()` 之外，DOM 还定义了一些旧的元素选择方法，这些方法现在或多或少已经过时了。

```js
// Look up an element by id. The argument is just the id, without
// the CSS selector prefix #. Similar to document.querySelector("#sect1")
let sect1 = document.getElementById("sect1");

// Look up all elements (such as form checkboxes) that have a name="color"
// attribute. Similar to document.querySelectorAll('*[name="color"]');
let colors = document.getElementsByName("color");

// Look up all <h1> elements in the document.
// Similar to document.querySelectorAll("h1")
let headings = document.getElementsByTagName("h1");

// getElementsByTagName() is also defined on elements.
// Get all <h2> elements within the sect1 element.
let subheads = sect1.getElementsByTagName("h2");

// Look up all elements that have class "tooltip."
// Similar to document.querySelectorAll(".tooltip")
let tooltips = document.getElementsByClassName("tooltip");

// Look up all descendants of sect1 that have class "sidebar"
// Similar to sect1.querySelectorAll(".sidebar")
let sidebars = sect1.getElementsByClassName("sidebar");
```

<br/>

**预选的元素(PRESELECTED ELEMENTS)** 由于历史原因，Document 类定义了快捷属性来访问某些类型的节点。

```js
// 通过 document.forms 属性，你可以访问 <form id="address"> 标签
document.forms.address;
```

<br/>
<br/>

### 文档结构和遍历

Document Structure and Traversal

从文档中选择了一个元素，你有时需要找到它在文档结构中相关的部分（父、兄弟姐妹、子）。当我们主要对文档中的元素而不是其中的文本感兴趣时，有一个遍历 API，允许我们把文档当作一颗由元素对象组成的树，忽略那些也是文档一部分的文本节点。

这个遍历 API 不涉及任何方法，它只是元素对象上的一组属性，允许我们引用一个给定元素的父、子和兄弟姐妹：

- 父节点(parentNode)
- 孩子(children)
- 孩子元素计数(childElementCount)
- 第一个子元素(firstElementChild)，最后一个子元素(lastElementChild)
- 下一个兄弟姊妹元素(nextElementSibling)，前一个兄弟姊妹元素(previousElementSibling)

使用这些元素属性，就可以很容易的引用需要的元素：

```js
document.children[0].children[1]
document.firstElementChild.firstElementChild.nextElementSibling
```

<br/>

**作为节点树的文档(DOCUMENTS AS TREES OF NODES)** 如果你想遍历一个文档或文档的某些部分，你可以使用一组定义在所有节点对象上的不同属性。这样你可以看到元素、文本节点，甚至是注释节点。

所有的节点对象都定义了以下属性：

- 父节点
- 子节点
- 第一个孩子，最后一个孩子
- 下一个兄弟姐妹，前一个兄弟姐妹
- 节点类型
- 节点值
- 节点名称

使用这些节点属性，可以很容易地引用。

<br/>
<br/>

### 属性

Attributes

HTML 元素由一个 tag 名称和一组称为属性的 name/value 对组成（例如 `<a href="xxx">`）。

元素类定义了一般的 `getAttribute()`, `setAttribute()`, `hasAttribute` 和 `removeAttribute()` 方法，用于查询、设置、测试和删除元素。但是 HTML 元素的属性值都可以作为代表这些元素的 HTMLElement 对象的属性来使用，而且将它们作为 JavaScript 属性来处理通常比调用前面的一般方法要容易得多。

<br/>

**作为元素属性的 HTML 属性**（HTML ATTRIBUTES AS ELEMENT PROPERTIES）

表示 HTML 文档元素的元素对象通常定义了反映元素 HTML 属性的读/写属性。元素定义了 id, title, lang 和 dir 等通用 HTML 属性以及 `onclick` 等事件处理程序属性。特定元素子类型定义了这些元素的特定属性。

```js
// 例如，要查询图片的 URL，可以使用代表 <img> 元素的
// HTMLElement 的 src 属性。
let image = document.querySelector("#main_image");
let url = image.src;       // The src attribute is the URL of the image
image.id === "main_image"  // => true; we looked up the image by id

// 类似地，你可以使用以下代码设置表单提交
let f = document.querySelector("form");      // First <form> in the document
f.action = "https://www.example.com/submit"; // Set the URL to submit it to.
f.method = "POST";                           // Set the HTTP request type.
```

一些注意事项：

- 对于某些元素，某些 HTML 属性名称会映射到不同名称的属性。
- HTML 属性不区分大小写，但 JS 属性名称区分大小写，请注意转换。
- 有些 HTML 属性名称在 JS 中是保留字。对于这些属性，一般规则是在属性名称前面加上 "html"。
- 表示 HTML 属性的属性通常是字符串值。但当属性是布尔值或数值时，属性就是布尔值或数字。事件处理程序属性的值总是函数（或空）。
- 这个基于属性的 API 并没有定义删除属性的方法。删除属性请使用 `remoreAttribure()` 方法。

<br/>

**类属性**（THE CLASS ATTRIBUTE）

HTML 元素的 class 属性尤为重要。该属性的值是一个以空格分隔的 CSS 类列表，这些 CSS 类适用于改元素并影响其 CSS 样式。由于 class 在 JS 中是一个保留字，该属性的值可通过元素对象上的 `className` 属性获取。在客户端 JS 编程中，通常需要从列表中添加和删除单个类名，而不是将列表作为单个字符串来处理。

因此，元素对象定义了一个 `classList` 属性，允许将类属性视为一个列表，它是一个可迭代的类似数组的对象，并定义了 `add()`, `remove()`, `contains()` 和 `toggle()` 方法。

```js
// When we want to let the user know that we are busy, we display
// a spinner. To do this we have to remove the "hidden" class and add the
// "animated" class (assuming the stylesheets are configured correctly).
let spinner = document.querySelector("#spinner");
spinner.classList.remove("hidden");
spinner.classList.add("animated");
```

<br/>

**数据集属性**（DATASET ATTRIBUTES）

在 HTML 元素上附加额外信息有时很有用，通常是在 JS 代码将选择这些元素并以某种方式对其进行操作时。在 HTML 中，任何名称以小写字母和前缀 `data-` 开头的属性都是有效的，你可以将它们用于任何目的。这些数据集属性不会影响其所在元素的显示效果，它们定义了一种附加额外数据的标准方法，而不会影响文档的有效性。

在 DOM 中，元素对象有一个数据集属性，它指向一个对象，该对象的属性与去掉前缀的 `data-` 属性相对应。因此，`dataset.x` 将保存 `data-x` 属性的值。

假设一个 HTML 文档包含如下文本：

```html
<h2 id="title" data-section-number="16.1">Attributes</h2>
```

那么你可以编写这样的 JS 来访问该部分编号：

```js
let number = document.querySelector("#title").dataset.sectionNumber;
```

<br/>
<br/>

### 元素内容

Element Content

再看看文档树的结构，问问自己 `<p>` 元素的内容是什么。本节将解释如何使用 HTML 表示法和元素内容的纯文本表示法。

<br/>

**元素内容为 HTML**（ELEMENT CONTENT AS HTML）

读取元素的 innerHTML 属性后，该元素的内容会以标记字符串的形式返回。在元素上设置该属性会调用网络浏览器的解析器，并用新字符串的解析表示替换元素的当前内容，你可以通过控制台进行测试。

```js
document.body.innerHTML = "<h1>Oops</h1>";
```

你会看到这个页面消失了，取而代之的是一个标题 "Oops"。网页浏览器非常擅长解析 HTML，设置 innerHTML 通常相当有效。

警告！在使用这些 HTML API 时，切勿在文档中插入用户输入内容，这一点非常重要。如果这样做，就会允许用户在应用程序中注入自己的脚本，详情请参考跨站脚本。

元素的 outerHTML 属性其值包括元素本身。查询 outerHTML 时，其值包括元素的开头和结尾标记。在元素上设置 outerHTML 时，新内容将取代元素本身。

插入相邻 HTML 标记（`insertAdjacentHTML()`）是一个相关的元素方法，通过它可以在指定元素的相邻位置插入任意 HTML 标记字符串。

<br/>

**元素内容为纯文本**（ELEMENT CONTENT AS PLAIN TEXT）

有时，你想以纯文本的形式查询元素和内容，或在文档中插入纯文本（而无需转义 HTML 标记中使用的角括号和括弧）。标准的方法是使用 `textContent` 属性。

```js
let para = document.querySelector("p"); // First <p> in the document
let text = para.textContent;            // Get the text of the paragraph
para.textContent = "Hello World!";      // Alter the text of the paragraph
```

textContent 属性由 Node 类定义，因此它既适用于文本节点，也适用于元素节点。对于元素节点，它会查找并返回元素所有后代中的所有文本。

`<SCRIPT>` 元素中的文本内联 `<script>` 元素有一个文本属性，可以用来获取文本。浏览器永远不会显示 `<script>` 元素的内容，HTML 解析器也会忽略脚本中的角括号和符号。因此，`<script>` 元素是嵌入任何文本数据供应用程序使用的理想位置。只需将元素的 type 属性设置为某个值（如 "text/x-custom-data"），这样就能清楚的表明脚本不是可执行的 JS 代码。这样 JS 解释器就会忽略脚本，但该元素会存在于文档树中，其中 text 属性会将数据返回给你。

<br/>
<br/>

### 创建插入和删除节点

Creating, Inserting, and Deleting Nodes

我们可以在单个节点的级别上更改文档。Document 类定义了创建元素对象的方法，而 Element 和 Text 对象则拥有在树中插入、删除和替换节点的方法。

```js
// 使用 Document 类的 createElement() 方法创建新元素
// 并使用 append() 和 prepend() 方法将文本字符串或其他元素添加到新元素中
let paragraph = document.createElement("p"); // Create an empty <p> element
let emphasis = document.createElement("em"); // Create an empty <em> element
emphasis.append("World");                    // Add text to the <em> element
paragraph.append("Hello ", emphasis, "!");   // Add text and <em> to <p>
paragraph.prepend("¡");                      // Add more text at start of <p>
paragraph.innerHTML                          // => "¡Hello <em>World</em>!"
```

如果要在包含元素的子节点列表中插入一个元素或文本节点，你应该获取一个同级节点的引用，然后调用 `before()` 或 `after()` 将数据插入到同级节点前/后。

```js
// Find the heading element with class="greetings"
let greetings = document.querySelector("h2.greetings");

// Now insert the new paragraph and a horizontal rule after that heading
greetings.after(paragraph, document.createElement("hr"));
```

请注意，元素只能插入文档中的一个位置。如果一个元素已经在文档中，而你将它插入到其他地方，它将移动到新的位置，而不会被复制。

如果你确想复制一个元素，请使用 `cloneNode()` 方法，通过传递 `true` 来复制其所有内容。

```js
// We inserted the paragraph after this element, but now we
// move it so it appears before the element instead
greetings.before(paragraph);

// Make a copy of the paragraph and insert it after the greetings element
greetings.after(paragraph.cloneNode(true));
```

你可以调用 `remove()` 方法从文档中删除元素或文本节点，也可以调用 `replaceWith()` 方法将其替换。

```js
// Remove the greetings element from the document and replace it with
// the paragraph element (moving the paragraph from its current location
// if it is already inserted into the document).
greetings.replaceWith(paragraph);

// And now remove the paragraph.
paragraph.remove();
```

<br/>
<br/>

### 示例：生成目录

Generating a Table of Contents

使用 DOM API 生成目录。

```js
/**
 * TOC.js: create a table of contents for a document.
 *
 * This script runs when the DOMContentLoaded event is fired and
 * automatically generates a table of contents for the document.
 * It does not define any global symbols so it should not conflict
 * with other scripts.
 *
 * When this script runs, it first looks for a document element with
 * an id of "TOC". If there is no such element it creates one at the
 * start of the document. Next, the function finds all <h2> through
 * <h6> tags, treats them as section titles, and creates a table of
 * contents within the TOC element. The function adds section numbers
 * to each section heading and wraps the headings in named anchors so
 * that the TOC can link to them. The generated anchors have names
 * that begin with "TOC", so you should avoid this prefix in your own
 * HTML.
 *
 * The entries in the generated TOC can be styled with CSS. All
 * entries have a class "TOCEntry". Entries also have a class that
 * corresponds to the level of the section heading. <h1> tags generate
 * entries of class "TOCLevel1", <h2> tags generate entries of class
 * "TOCLevel2", and so on. Section numbers inserted into headings have
 * class "TOCSectNum".
 *
 * You might use this script with a stylesheet like this:
 *
 *   #TOC { border: solid black 1px; margin: 10px; padding: 10px; }
 *   .TOCEntry { margin: 5px 0px; }
 *   .TOCEntry a { text-decoration: none; }
 *   .TOCLevel1 { font-size: 16pt; font-weight: bold; }
 *   .TOCLevel2 { font-size: 14pt; margin-left: .25in; }
 *   .TOCLevel3 { font-size: 12pt; margin-left: .5in; }
 *   .TOCSectNum:after { content: ": "; }
 *
 * To hide the section numbers, use this:
 *
 *   .TOCSectNum { display: none }
 **/
document.addEventListener("DOMContentLoaded", () => {
    // Find the TOC container element.
    // If there isn't one, create one at the start of the document.
    let toc = document.querySelector("#TOC");
    if (!toc) {
        toc = document.createElement("div");
        toc.id = "TOC";
        document.body.prepend(toc);
    }

    // Find all section heading elements. We're assuming here that the
    // document title uses <h1> and that sections within the document are
    // marked with <h2> through <h6>.
    let headings = document.querySelectorAll("h2,h3,h4,h5,h6");

    // Initialize an array that keeps track of section numbers.
    let sectionNumbers = [0,0,0,0,0];

    // Now loop through the section header elements we found.
    for(let heading of headings) {
        // Skip the heading if it is inside the TOC container.
        if (heading.parentNode === toc) {
            continue;
        }

        // Figure out what level heading it is.
        // Subtract 1 because <h2> is a level-1 heading.
        let level = parseInt(heading.tagName.charAt(1)) - 1;

        // Increment the section number for this heading level
        // and reset all lower heading level numbers to zero.
        sectionNumbers[level-1]++;
        for(let i = level; i < sectionNumbers.length; i++) {
            sectionNumbers[i] = 0;
        }

        // Now combine section numbers for all heading levels
        // to produce a section number like 2.3.1.
        let sectionNumber = sectionNumbers.slice(0, level).join(".");

        // Add the section number to the section header title.
        // We place the number in a <span> to make it styleable.
        let span = document.createElement("span");
        span.className = "TOCSectNum";
        span.textContent = sectionNumber;
        heading.prepend(span);

        // Wrap the heading in a named anchor so we can link to it.
        let anchor = document.createElement("a");
        let fragmentName = `TOC${sectionNumber}`;
        anchor.name = fragmentName;
        heading.before(anchor);    // Insert anchor before heading
        anchor.append(heading);    // and move heading inside anchor

        // Now create a link to this section.
        let link = document.createElement("a");
        link.href = `#${fragmentName}`;     // Link destination

        // Copy the heading text into the link. This is a safe use of
        // innerHTML because we are not inserting any untrusted strings.
        link.innerHTML = heading.innerHTML;

        // Place the link in a div that is styleable based on the level.
        let entry = document.createElement("div");
        entry.classList.add("TOCEntry", `TOCLevel${level}`);
        entry.append(link);

        // And add the div to the TOC container.
        toc.append(entry);
    }
});
```

<br/>
<br/>

## CSS脚本

Scripting CSS

我们已经看到，JS 可以控制 HTML 文档的逻辑结构和内容。它还可以通过编写 CSS 脚本来控制这些文档的视觉外观和布局。

值得一提的是，有些 CSS 样式通常是从 JS 脚本中提取的：

- 将显示样式（display style）设置为 "none" 可以隐藏元素。之后，你可以通过将显示设置为其他值来显示该元素。
- 通过将位置样式（position style）设置为 "absolute", "relative" 或 "fixed"，然后将顶部（top）和左侧（left）样式设置未所需坐标，就可以动态定位元素。
- 你可以使用变换样式来 移动（shift）, 缩放（scale） 和 旋转（rotate）元素。
- 你可以使用过渡（transition）样式将其他 CSS 样式的变化制作成动画。这些动画由浏览器自动处理，不需要 JS，但你可以使用 JS 来启动动画。

<br/>
<br/>

### CSS类

CSS Classes

使用 JS 影响文档内容样式最简单的方法，就是从 HTML 标记的类属性中添加或删除 CSS 类名。使用元素对象的 `classList` 属性很容易做到这一点。

假设文档的样式表包含一个隐藏类的定义：

```css
.hidden {
  display:none;
}
```

定义了上述样式后，就可以用如下代码隐藏/显示一个元素：

```js
// Assume that this "tooltip" element has class="hidden" in the HTML file.
// We can make it visible like this:
document.querySelector("#tooltip").classList.remove("hidden");

// And we can hide it again like this:
document.querySelector("#tooltip").classList.add("hidden");
```

<br/>
<br/>

### 行内样式

Inline Styles

假设文档的结构中只包含一个提示条元素，我们想在显示它之前先动态把它定位好。一般来说，我们不可能针对提示条的所有可能位置都创建一个类。

这种情况下，我们需要用程序修改提示条在 HTML 中的 style 属性，设置只针对它的行内样式。

<br/>
<br/>

## 文档几何与滚动


















<br/>

---

<br/>

# 服务器端的JavaScript















<br/>

---

<br/>

# JavaScript的工具和扩展

本章要介绍的工具和语言扩展如下：

- ESLint：辅助发现代码中潜藏的缺陷和风格问题。
- Prettier：用于标准方式格式化 JavaScript 代码。
- Jest：一种编写 JavaScript 单元测试的一站式解决方案。
- npm：用于管理和安装程序依赖的软件库。
- webpack、Rollup 和 Parcel等代码打包工具：用于把 JavaScript 代码模块转换为在网页中使用的单个代码文件。
- Babel：用于把使用最前沿特性（或语言扩展）的 JavaScript 代码转译为可以在当前浏览器中运行的 JavaScript 代码。
- JSX 语言扩展（React 框架使用）：可以让我们用类似 HTML 标记的 JavaScript 表达式描述用户界面。
- Flow 语言扩展（或类似于 TypeScript 扩展）：可以让我们为 JavaScript 代码添加类型注解，通过类型检查保证类型安全。

<br/>
<br/>

## 使用ESLint检查代码

Linting with ESLint

在编程领域，lint 是指代码虽然技术上正确，但书写却不够规范，甚至有 bug，或者没有达到最优。

目前最常用的 JavaScript linter 是 ESLinter。如果运行它并花实际时间解决它指出的问题，你的代码会更清晰，更不容易出错。

ESLint 定义了很多 linting 规则，而且有一堆插件生态，可以增加新规则。但 ESLint 也是完全可以配置的，可以定义一个配置文件，让 ESLint 只执行你想让它执行的规则。

示例代码：

```js
var x = 'unused';

export function factorial(x) {
    if (x == 1) {
      return 1;
    } else {
        return x * factorial(x-1)
    }
}
```

如果对这段代码运行 ESLint，可能会看到如下输出：

```sh
eslint code/ch17/linty.js

code/ch17/linty.js
  1:1   error    Unexpected var, use let or const instead      no-var
  1:5   error    'x' is assigned a value but never used        no-unused-vars
  1:9   warning  Strings must use doublequote                  quotes
  4:11  error    Expected '===' and instead saw '=='           eqeqeq
  5:1   error    Expected indentation of 8 spaces but found 6  indent
  7:28  error    Missing semicolon                             semi

✖ 6 problems (5 errors, 1 warning)
  3 errors and 1 warning potentially fixable with the `--fix` option.
```

<br/>
<br/>

## 使用Prettier格式化代码

JavaScript Formatting with Prettier

有些项目使用 linter 的目的是强制编码风格一致，以便团队成员在修改共享代码时，可以保持相同的编码习惯。

对于这种通过 linter 强制代码格式的需求，更流行的做法是使用类似 Prettier 的工具来自动解析和重新格式化代码。

示例代码：

```js
// 这个函数，逻辑没有问题，但格式不符合管理
function factorial(x)
{
         if(x===1){return 1}
           else{return x*factorial(x-1)}
}
```

对这段代码运行 Prettier 可以修复缩进，添加省略的分号、空格等。

```sh
prettier factorial.js
function factorial(x) {
  if (x === 1) {
    return 1;
  } else {
    return x * factorial(x - 1);
  }
}
```

Prettier 接受配置，但选项不多。比如可以选择最大行长度、缩进数、是否使用分号、字符串使用单引号还是双引号，等等。一般来说，Prettier 的默认选项还是比较合适的。只要在项目中采用 Prettier，就永远不用再担心代码格式化了。

<br/>
<br/>

## 使用Jest做单元测试

对于任何项目而言，编写测试都是重要的一环。JavaScript 这样的动态语言支持测试框架，可以大幅减少编写测试的工作量，甚至能让写测试变得很好玩。

本节介绍 Jest，它是一个囊括了所有测试功能的流行框架。

示例代码：

```js
const getJSON = require("./getJSON.js");

/**
 * getTemperature() takes the name of a city as its input, and returns
 * a Promise that will resolve to the current temperature of that city,
 * in degrees Fahrenheit. It relies on a (fake) web service that returns
 * world temperatures in degrees Celsius.
 */
module.exports = async function getTemperature(city) {
    // Get the temperature in Celsius from the web service
    let c = await getJSON(
        `https://globaltemps.example.com/api/city/${city.toLowerCase()}`
    );
    // Convert to Fahrenheit and return that value.
    return (c * 5 / 9) + 32;  // TODO: double-check this formula
};
```

测试代码：

```js
// Import the function we are going to test
const getTemperature = require("./getTemperature.js");

// And mock the getJSON() module that getTemperature() depends on
jest.mock("./getJSON");
const getJSON = require("./getJSON.js");

// Tell the mock getJSON() function to return an already resolved Promise
// with fulfillment value 0.
getJSON.mockResolvedValue(0);

// Our set of tests for getTemperature() begins here
describe("getTemperature()", () => {
    // This is the first test. We're ensuring that getTemperature() calls
    // getJSON() with the URL that we expect
    test("Invokes the correct API", async () => {
        let expectedURL = "https://globaltemps.example.com/api/city/vancouver";
        let t = await(getTemperature("Vancouver"));
        // Jest mocks remember how they were called, and we can check that.
        expect(getJSON).toHaveBeenCalledWith(expectedURL);
    });

    // This second test verifies that getTemperature() converts
    // Celsius to Fahrenheit correctly
    test("Converts C to F correctly", async () => {
        getJSON.mockResolvedValue(0);                // If getJSON returns 0C
        expect(await getTemperature("x")).toBe(32);  // We expect 32F

        // 100C should convert to 212F
        getJSON.mockResolvedValue(100);              // If getJSON returns 100C
        expect(await getTemperature("x")).toBe(212); // We expect 212F
    });
});
```

写完测试后，可以使用 `jest` 命令运行它。

<br/>
<br/>

## 使用npm管理依赖包

Package Management with npm

npm 是一个使用 Node 打包而成的包管理器。

如果要尝试别人写的 JavaScript 项目，在下载其代码后，通常第一件事就是 `npm install`。这个命令会读取 `package.json` 文件中的依赖列表，并下载项目依赖的第三方包并保存到 node_modules 目录中。

示例命令：

```sh
# 安装特定包
# 除了使用包名来安装依赖之外，它也会在项目的 package.json 文件中添加一条记录。
npm install express

# 还有一种依赖址对项目的开发者有用，项目运行的时候并不需要。
# 比如，项目中使用 Prettier 来保证代码格式统一，但它属于开发依赖，安装它时使用 --save-dev
npm install --save-dev prettier

# 有时候，可能需要全局安装某个开发者工具，从而即便在没有 package.json 文件和 node_modules 目录的地方也可以使用。
# 可以在安装依赖时添加 -g (global) 全局选项
$ npm install -g eslint jest
/usr/local/bin/eslint -> /usr/local/lib/node_modules/eslint/bin/eslint.js
/usr/local/bin/jest -> /usr/local/lib/node_modules/jest/bin/jest.js
+ jest@24.9.0
+ eslint@6.7.2
added 653 packages from 414 contributors in 25.596s

# npm 还支持 uninstall、update 和其它命令。
# audit 子命令可以找到并修复依赖中的安全漏洞。
npm audit --fix
```

在项目本地安装 ESLint 等工具时，eslint 脚本会保存在 `./node_modules/.bin/eslint` 目录中，因此运行命令比较麻烦。好在 `npm` 也附带了一个 `npx` 命令，这样就可以用 `npx eslint/jest` 命令来运行本地安装的工具（如果工具未安装，npx 会为你自动安装）。

维护 npm 的公司也在维护 <http://npmjs.com> 包仓库，其中托管这成千上万的开源包。使用 yarn 或 pnpm 也可以访问这个仓库。

<br/>
<br/>

## 代码打包

Code Bundling

如果要写一个在浏览器中运行的大型 JavaScript 项目，可能会用到代码打包工具，特别是在使用的外部库是以模块形式提供的时候。Web 开发者已经使用 ES6 模块很多年了，最初浏览器尚未支持 `import` 和 `export` 关键字。为了使用 ES6 模块，程序员使用代码打包工具从程序的主入口开始，跟着 import 指令树，从而找到程序依赖的所有模块。然后把所有独立的模块文件组合成一个 JavaScript 代码包，并重写 import 和 export 指令让代码可以在这种新形式下运行。结果是一个代码文件，让不支持模块的浏览器可以加载运行。

Web 性能是一个棘手的问题，需要考虑很多变数，包括浏览器厂商的持续改进。因此要确保浏览器最快地加载代码，唯一的方式是全面测量和仔细度量。有一个变数是始终可控的，那就是代码大小。更小的 JavaScript 肯定加载和运行的更快。

市面上有很多有些的打包工具，其中常用的有 webpack、Rollup和Parcel。功能基本大同小异，区别在于配置方式和使用门槛。webpack 出现的时间比较早，拥有庞大的插件生态，高度可配置，而且支持比较旧的非模块库。但 webpack 也比较复杂，很难配置。相对地，Parcel 的目标则是以零配置方式做正确的事。

<br/>
<br/>

## 使用Bable转译

Transpilation with Babel

Babel 是一个编译工具，可以把使用现代语言特性编写的 JavaScript 代码编译为不使用那些现代语言特性的 JavaScript 代码。因为是把 JavaScript 代码编译成 JavaScript 代码，Babel 有时候也被称为转译器。Babel 的目的就是让开发者可以使用 ES6 及之后的新语言特性，同时仍然可以兼容那些只支持 ES5 的浏览器。

类似乘方操作符(`**`)和箭头函数这样的语言特性，比较容易转换为 `Math.pow()` 和 `function` 表达式。

对于想使用最前沿技术的人来说，Babel 仍然是仍然是有用的。

你可以使用 npm 安装 Babel，使用 npx 运行它。它读取 `.babelrc` 配置文件，获取你对如何转换 JavaScript 的要求。

<br/>
<br/>

## JSX：JavaScript中的标记表达式

JSX: Markup Expressions in JavaScript

JSX 是对核心 JavaScript 的扩展，它使用 HTML 风格的语法定义元素树。JSX 与构建用户界面的 React 框架紧密联系。在 React 中，这个使用 JSX 定义的元素树最终会被渲染为 HTML 而进入浏览器。

本节介绍 JSX 语言扩展，而不是 React。

可以把 JSX 元素想象为一种新的 JavaScript 表达式语法。

```jsx
// 一个简单的 JSX 赋值表达式
let line = <h1/>;
```

如果使用 JSX，那么需要使用 Babel（或类似工具）把 JSX 表达式编译为常规的 JavaScript。这个转换很简单，为此有些开发者选择使用 React 而不使用 JSX。

Babel 会把上面赋值语句中的 JSX 表达式转换为下面这个简单的函数调用：

```js
let line = React.createElement("hr", null);
```

<br/>
<br/>

## 使用Flow检查类型

Type Checking with Flow

Flow 也是一种语言扩展，让我们可以为 JavaScript 代码添加类型注解，同时它也是一个检查 JavaScript 代码中类型错误（包括有注解和无注解）的工具。

要使用 Flow，需要一开始就使用 Flow 语言扩展给代码添加类型注解。然后运行 Flow 工具分析代码、报告错误类型。等修复错误并准备好运行代码后，可以使用 Babel 从代码中剥离 Flow 类型注解。

<br/>
<br/>

### TypeScript与Flow

TypeScript 是 Flow 的一个非常流行的替代平。TypeScript 也是一种 JavaScript 扩展，但它除了类型还添加了其他语言特性。TypeScript 编辑器 tsc 负责把 TypeScript 程序编译为 JavaScript 程序，在此期间会像 Flow 那样分析并报告类型错误。tsc 不是 Babel 插件，而是一个独立的编译器。

TypeScript 是 2012 年发布的，早于 ES6，当时 JavaScript 还没有 class 关键字、for/of 循环、模块和期约等。Flow 是一个相对窄的语言扩展，只给 JavaScript 增加了类型注解。TypeScript 则是一个经过良好设计的新语言。顾名思义，为 JavaScript 添加类型是 TypeScript 的主要目的，也是人们今天使用它的原因。但也还有其它特性。2020 年，TypeScript IDE和代码编辑器的集成度都要高于 Flow。

不管怎么说，本书讲的都是 JavaScript，本节介绍 Flow 而不介绍 TypeScript 是不想把焦点从 JavaScript 身上转移开。但这里介绍给 JavaScript 添加类型的所有内容，对你在项目中使用 TypeScript 同样也会有帮助。

Flow 需要一定的投入，但相应地，Flow 会让人养成良好的编程习惯，杜绝因为走捷径而导致错误。

