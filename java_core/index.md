# Java核心技术


参考：

- Java核心技术-卷一

<br/>

---

<!--more-->

<br/>

# 概述

<br/>
<br/>

## 程序设计平台

Java 并不只是一种语言，它是一个完整的平台，有一个庞大的库，其中包含了大量可重用的代码。还有如安全性、跨操作系统的可以执行以及自动垃圾收集等服务的运行环境。

<br/>
<br/>

## 关键术语

Java 的一些关键术语：

- Java 开发包（JDK, Java Development Kit）
- Java 运行环境（JRE, Java Runtime Environment）
- OpenJDK：Java 标准版本的一个免费开源实现
- 长期支持版本（LTS, Long Term Support）
- 简单性：Java 语法是 C++ 语言的一个纯净版本。
- 面向对象
- 分布式：Java 有一个丰富的例程库，用于处理 TCP/IP 协议。
- 健壮性：Java 编译器能够检测许多其他语言中仅在运行时才能够检测出来的问题。Java 采用的指针模型可以消除重写内存和损坏数据的可能性。
- 安全性：不可信代码在沙箱环境中执行，在这里它不会影响主系统。
- 体系结构中立：使用虚拟机
- 可移植性
- 解释性：Java 解释器可在任何移植了解释器的机器上直接执行 Java 字节码。
- 高性能
- 多线程
- 动态性

<br/>
<br/>

## 发展简史

发展历史：

- 1991 年，Sun 公司的工程师小组开发设计了 Java 语言。
- 1996 年，Sun 公司发布了 Java 的第 1 个版本。
- 1998 年，发布了 Java 1.2 版 和 Java 2 版。
- 2005 年，Java 5.0 （1.5）版，自 1.1 版以来做出重大改进的版本。
- 2006 年，Java 6 版。
- 2011 年，Java 7 版。
- 2014 年，Java 8 版，改变最大。包含了函数式编程方式。
- 2017 年，Java 9 版。
- 从 2018 年开始，每 6 个月就会发布一个 Java 版本，以支持更快地引入新特性。每过一段时间，会把某个版本（如 Java 11 和 Java 17）指定为长期支持版本。

<br/>

---

<br/>

# 编程环境

<br/>
<br/>

## 安装开发工具包

Oracle 公司会提供 Java 开发工具包（JDK）版本。

```sh
# 下载 jdk
# https://adoptium.net/
# https://www.oracle.com/java/technologies/downloads/

# 解压并设置 jdk
# /opt/jdk1.8.0_392

# 编辑 PATH
# /etc/profile
JAVA_HOME=/opt/jdk1.8.0_392
JRE_HOME=$JAVA_HOME/jre
CLASSPATH=:$JAVA_HOME/lib:$JRE_HOME/lib
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME JRE_HOME CLASSPATH PATH
```

<br/>

---

<br/>

# 基本程序设计结构

<br/>
<br/>

## 一个简单的程序

```java
public class FirstSample
{
    public static void main(String[] args)
    {
        System.out.Println("We will not use 'Hello, World!'");
    }
}
```

分析这个程序：

- Java 区分大小写。
- 关键字 `public` 称为访问修饰符（access modifier），这些修饰符用于控制程序的其他部分对这段代码的访问级别。
- 关键字 `class` 表明程序中的全部内容都包含在类中。类是 Java 应用的构建模块，所有内容都必须放在类中。
- 大括号 `{}` 划分程序的各个部分（通常称为块）。
- 关键字 `void` 表示这个方法不返回值。
- Java 中的每个语句必须用分号（`;`）结束。回车不是语句的结束标志。
- Java 中的所有函数都是某个类的方法。
- Java 中类名的规则很宽松。必须以字母开头，后面可跟字母和数字的任意组合。长度基本没有限制。不能使用保留字。
- 标准命名规定：类名以大写字母开头。如果由多个单词组成，每个单词的首字母都应该大写（驼峰命名法）。
- 源代码文件名必须与公共类的类名相同（如 `FirstSample.java`，不能写成 `firstsample.java`）。
- 如果没有错误，Java 编译器编译后会得到一个名为 `FirstSample.class` 的类字节码文件。
- 运行程序。运行一个已编译的程序时，Java 虚拟机总是从指定类中 `main` 方法的代码开始执行。

```sh
# 运行 java 程序
# java ClassName，不要加 .class 扩展名
java FirstSample
```

<br/>
<br/>

## 注释

```java
// java 单行注释

/**
 * FirstSample
 * @version 1.01 1997-03-22
 * @author Javaer
 */
```

<br/>
<br/>

## 数据类型

Java 是一种强类型语言，必须为每一个变量声明一个类型。

有 8 种基本类型：

- 4 种整型
- 2 种浮点类型
- 1 种字符类型 `char`（用于表示 Unicode 编码的代码单元）
- 1 种布尔类型

<br/>

{{< admonition >}}

Java 有一个能够表示任意精度的算术包，所谓的大数（big number）是对象，不是基本类型。

{{< /admonition >}}

<br/>
<br/>

### 整型

在 Java 中，整型的范围于运行代码的机器无关。这解决了平台之间的移植性问题。

| 类型 | 存储 | 范围 |
| - | - | - |
| byte | 1 字节 | -128 ~ 127 | 
| short | 2 字节 | -32 768 ~ 32767 |
| int | 4 字节 | -2 147 483 648 ~ 2 147 483 647 |
| long | 8 字节 | -9 223 372 036 854 775 808 9 223 372 036 854 775 807 |

<br/>
<br/>

### 浮点类型

| 类型 | 存储 | 范围 |
| - | - | - |
| float | 4 字节 | $\pm3.40282347\times10^{38}$ （6-7 位有效数字） |
| double | 8 字节 | $\pm1.79769313486231570\times10^{308}$ （15 位有效数字） |

float 类型的数值有一个后缀（F 或 f，如 `3.14F`）。没有后缀的浮点数总是默认为 double 类型。也可以可选地在 double 数值后添加后缀（D 或 d，如 3.14D）。

double 类型的数值精度是 flout 类型的两倍。

所有的浮点计算都遵循 IEEE 754 规范。有 3 个特殊的浮点数值表示溢出和错误：

- 正无穷大
- 负无穷大
- NaN（不是一个数）

<br/>

{{< admonition warning "警告">}}

浮点数不适用于无法接受舍入误差的金融计算。

{{< /admonition >}}

<br/>
<br/>

### char 类型

char 类型的字面量值要用单引号括起来。如，'A' 是编码值为 65 的字符常量。而 "A" 是包含一个字符的字符串。

char 类型的值可以表示为十六进制值，范围从 `\u0000 ~ \uFFFF`。如 `\u03C0` 表示希腊字母 Π。

要想弄清 chat 类型，就必须了解 Unicode 编码机制，它打破了传统字符编码机制的限制。

在 Java 中，chat 类型描述了采用 UTF-16 编码的一个代码单元。

强烈建议不要在程序中使用 char 类型，除非确实需要处理 UTF-16 代码单元。最好将字符串作为抽象数据类型来处理。

<br/>
<br/>

### 布尔类型

布尔类型有两个值：true 和 false，用来判定逻辑条件。

<br/>
<br/>

## 变量与常量

<br/>
<br/>

### 声明变量

声明一个变量时，先指定变量的类型，然后是变量名。

变量名由字母、数字、货币符号等组成，变量名区分大小写，不能使用保留字作为变量名。

```java
double salary;

int vacationDays;

long earthPopulation;

boolean done;

// 可在一行中声明多个变量，但不提倡使用这种风格。分别声明每一个变量可以提高程序的可读性。
int i, j, k;
```

<br/>
<br/>

### 初始化变量

声明变量后，必须用赋值语句显示地初始化变量。千万不要使用未初始化的变量的值。

```java
// ERROR-var not init
int vacationDays;
System.out.println(vacationDays);

int aDays;
aDays = 12;

int bDays =12;
```

<br/>
{{< admonition note "注意" >}}

从 Java 10 开始，对于局部变量，如果可以从变量的初始值推断出它的类型，就不再需要声明类型。而只需使用关键字 `var`。

```java
var cDays = 12;
var greeting = "Hello";
```

{{< /admonition >}}

<br/>

{{< admonition tip "提示">}}

在 Java 中，变量的声明要尽可能靠近第一次使用这个变量的地方，这是一种很好的编程风格。

{{< /admonition >}}

<br/>
<br/>

### 常量

在 Java 中，用关键字 `final` 指示常量，表示这个变量只能被赋值一次。一旦赋值，就不能再更改了。

习惯上，常量名使用全大写。

在 Java 中，可能经常需要创建一个常量以便在一个类的多个方法中使用。通常将其称为类常量。可以使用关键字 `static final` 设置一个类常量。

如果一个常量被声明为 public，那么其他类的方法也可以使用这个常量（如 `Constant2.BB_INCH`）。

```java
public class Constants2 {
    // 类常量
    public static final double BB_INCH = 2.54;

    public static void main(String[] args) {
        // 常量
        final double CM_PER_INCH = 2.54;
    }
}
```

<br/>

{{< admonition note "注意" >}}

`const` 是 Java 保留的关键字，但目前并没有使用。必须使用 `final` 声明常量。

{{< /admonition >}}

<br/>
<br/>

### 枚举类型

枚举类型（enumerated type）包括有限个命名值。

枚举类型的变量只能存储这个类型声明中所列的某个值，或者特殊值 null（表示没有设置任何值）。

```java
// 枚举
enum Size {SMALL, MEDIUM, LARGE, EXTRA_LARGE };

// 现在，可以声明这种类型的变量
Size s = Size.MEDIUM;
```

<br/>
<br/>

## 运算符

<br/>
<br/>

### 算术运算符

在 Java 中通常的算术运算符。

| 类型 | 符号 | 备注 |
| - | - | - |
| 加 | `+` | |
| 减 | `-` | |
| 乘 | `*` | |
| 除 | `/` | 当参与运算的两个操作数都是整数时，表示整除法。<br> 如 15/2 等于7，而 15.0/2 等于 7.5。 |
| 求余 | `%` | |

<br/>

可在赋值中使用二元运算符。

```java
// x = x + 4
x += 4;
```

<br/>

{{< admonition warning 注意>}}

整数被 0 除将产生一个异常，而浮点数被 0 除将得到一个无穷大或 NaN 结果。

{{< /admonition >}}

<br/>
<br/>

### 数学函数与常量

Math 类中包含了各种数学函数。

{{< admonition tip 提示>}}

double y = Math.sqrt(4);

不必在数学方法名和常量名前添加前缀 "Math"，只要在源文件最上面引用即可。

import static java.lang.Math.*;

System.out.println("The square root of \u03c0 is " + sqrt(PI));

{{< /admonition >}}

<br/>
<br/>

### 数值类型之间的转换

数值类型之间的合法转换如下图，实线箭头表示无信息丢失；虚线箭头表示可能有精度损失。

![数值类型间的合法转换](https://raw.githubusercontent.com/zhang21/images/master/cs/java/3-1.png)

<br/>

```java
int n = 123456789;
// f is 1.23456792E8，精度损失
float f = n;
```

<br/>

当二元运算符连接的两个值的类型不一致时，先要将两个草奏疏转换为同一种类型，然后再进行计算。

- 如果有一个数为 double，另一个数就会转换为 double。
- 否则，如果一个数为 float，另一个数将会转换为 float。
- 否则，如果一个数为 long，另一个数将会转换为 long 类型。
- 否则，两个操作数都将被转换为 int 类型。

<br/>
<br/>

### 强制类型转换

在 Java 中，允许进行强制类型转换，不过可能会丢失一些信息。

```java
double x = 9.997;

// nx is 9
int nx = (int) x;

// 使用 Math 四舍五入，mx is 10
int mx = (int) Math.round(x);
```

<br/>

{{< admonition warning "警告">}}

强制转换超出了目标类型的表示范围，结果就会截断成一个完全不同的值。如 (byte) 300 实际上会得到 44。

{{< /admonition >}}

<br/>
<br/>

### 自增和自减运算符

由于运算符是改变变量的值，所以不能对数值本身应用运算符。

```java
int n = 12;
n++;
n--;

// Error
4++;

// 前缀形式先完成加 1
++n;
// 后缀形式会使用变量原来的值
n++
```

<br/>
<br/>

### 关系和布尔运算符

| 类型 | 符号 |
| - | - |
| 相等 | `==` |
| 不等 | `!=` |
| 小于 | `<` |
| 大于 | `>` |
| 小于等于 | `<=` |
| 大于等于 | `>=` |
| 与 | `&&` |
| 或 | `\|\|` |
| 非 | `!` |

<br/>
<br/>

### 条件运算符

Java 提供了 `conditional ?:` 运算符，可以根据一个布尔表达式选择一个值。

```java
// condtion ? expression1 : expression2
x < y ? x : y;
```

<br/>
<br/>

### switch 表达式

需要在两个以上的值中做出选择时，可使用 switch 表达式（在 Java 14 中引入）。

```java
String seasonName = switch (seaconCode) {
    case 0 -> "Spring";
    case 1 -> "Summer";
    case 2 -> "Fall";
    case 3 -> "Winter";
    default -> "???";
};

int numLetters = switch (seasonName) {
    case "Spring", "Summer", "Winter" -> 6;
    case "Fall" -> 4;
    default -> -1;
};

enum Size { SMALL, MEDIUM, LARGE, EXTRA_LARGE };
Size itemSize = xxx;
String label = switch (itemSize) {
    case SMALL -> "S";
    case MEDIUM -> "M";
    case LARGE -> "L";
    case EXTRA_LARGE -> "XL";
};
```

<br/>

{{< admonition warning "警告" >}}

如果操作数为 null，会抛出一个 NullPointerException。

{{< /admonition >}}

<br/>
<br/>

### 位运算符

`>>>` 运算符会用 0 填充高位，而 `>>` 会用符号位填充高位。不存在 `<<<` 运算符。

| 类型 | 符号 |
| - | - |
| and | `&` |
| or  | `\|` |
| xor | `^` |
| not | `~` |
| 左移 | `>>` |
| 右移 | `<<` |
| 填充 0 | `>>>` |

<br/>
<br/>

### 运算符优先级

| 运算符 | 综合性 |
| - | - |
| `[] . ()` | 从左向右 |
| `! ~ ++ -- + - ()` | 从右向左 |
| `* / %` | 从左向右 |
| `+ -` | 从左向右 |
| `<< >> >>>` | 从左向右 |
| `< <= > >= instanceof` | 从左向右 |
| `== !=` | 从左向右 |
| `&` | 从左向右 |
| `^` | 从左向右 |
| `\|` | 从左向右 |
| `&&` | 从左向右 |
| `\|\|` | 从左向右 |
| `?:` | 从右向左 |
| `= += -= *= /= %= &= \|= ^= <<= >>= >>>=` | 从右向左 |

<br/>
<br/>

## 字符串

从概念上将，Java 字符串就是 char 值（Unicode 字符）序列。Java 没有内置的字符串类型，标准类库中提供了一个预定义的 String 类。每个用双引号括起来的字符串都是 String 类的一个实例。

```java
String e = "";
String greeting = "Hello";
```

<br/>
<br/>

### 子字符串

String 类的 substring 方法可从字符串中取出子串。

```java
String greeting = "Hello";
String s = greeting.sustring(0,3);
```

<br/>
<br/>

### 字符串拼接

Java 语言允许使用加号（`+`）拼接字符串。当一个字符串与一个非字符串的值进行拼接后，后者会转换成字符串。

```java
String expletive = "Expletive";
int age = 13;
String message = expletive + age;
```

<br/>
<br/>

### 字符串不可变

String 类没有提供任何方法来修改字符串中的某个字段，因此它是不可变的（immutable）。但可以通过字串和和拼接来重写。

```java
String greeting = "Hello";
greeting = greeting.substring(0, 3) + "p!";
```

<br/>
<br/>

### 检测字符串是否相等

使用 `equals` 方法检测字符串是否相等，不要使用 `==` 符号判断字符串是否相等。

```java
s.equals(t);

"hello".equalsIgnoreCase(greeting);
```

<br/>
<br/>

### 空串与 Null 串

空字符串（""）是长度为 0 的字符串。null 是一个特殊的值，表示目前没有任何对象与该变量盖帘。

```java
// 检查字符串是否为空
if (str.length() == 0)
if (str.equals(""))

// 检查字符串是否为 null
if (str == null)
```

<br/>
<br/>

### 码点与代码单元

Java 字符串是一个 char 值序列。char 类型是采用 UTF-16 编码表示的 Unicode 码点的一个代码单元。

```java
// 将码点放入数组
int[] codePoints = str.codePoints().toArray();

// 将码点数组转换为字符串
String str = new String(codePoints, 0, codePoints.length);
// 把单个码点转换为字符串
str = Character.toString(codePoint);
```

<br/>
<br/>

### String API

String 类最常用的一些方法，具体请参考官方文档。

<br/>

### 构建字符串

如果需要用许多小字符串来构建一个字符串，可以使用字符串构建器。

<br/>
<br/>

### 文本块

利用 Java 15 新增的文本块特性，可以很容易地提供跨多行的字符串字面量。文本块特别适合包含用其他语言编写的代码，如 SQL 或 HTML。

Java 没有规定制表符的宽度。要注意制表符和空格混用的情况。

```java
String html = """
<div class="Warning">
  Beware of those 
</div>
""";
```

<br/>
<br/>

## 输入与输出

<br/>
<br/>

### 读取输入

更过详情和实例，请参考官方文档。

```java
import java.util.*;

// 读取标准输入流
Scanner in = new Scanner(System.in);
System.out.print("What's your name? ");
String name = in.nextLine();
```

<br/>
<br/>

### 格式化输出

`System.out` 支持有多个输出方法。可以使用 `%` 字符开头的格式说明符替换为相应的参数。

用于 `printf` 个转换字符。

| 转换字符 | 类型 | 示例 |
| - | - | - |
| d | 十进制整数 | 150 |
| x 或 X | 十六进制整数 | 9f |
| 0 | 八进制整数 | 137 |
| f 或 F | 定点浮点数 | 15.9 |
| e 或 E | 指数浮点数 | 1.59e+01 |
| g 或 G | 通用浮点数 | - |
| a 或 A | 十六进制浮点数 | 0x1.fccdp3 |
| s 或 S | 字符串 | Hello |
| c 或 C | 字符 | H |
| b 或 B | 布尔 | true |
| h 或 H | 散列码 | 42628b2 |
| tx 或 Tx | 遗留的日期时间格式化 | - |
| % | 百分号 | % |
| n | 与平台有关的行分隔符 | - |

<br/>

用于 `printf` 的标志。

| 标志 | 作用 | 示例 |
| - | - | - |
| + | 打印正数或负数的符号 | +3.3 |
| 空格 | 在正数前增加一个空格 | ` 3.3` |
| 0 | 增加前导 0 |  03.3 |
| - | 字段左对齐 | 3.3 |
| `(` | 将负数包围在括号内 | (3.3) |
| , | 增加分组分割符 | 3,333.3 |
| `#`（对于 f 格式）| 总是包含一个小数点 | 33. |
| `#`（对于 x 或 0 格式）| 添加前缀 0x 或 0 | 0xcafe |
| `$` | 指定要格式化的参数索引 | 1599F |
| `<` | 格式化前面指定的同一个值 | 1599F |

<br/>

```java
System.out.printf("%,.2f", 10000.0 / 3.0 ); // 3333.33

// 创建一个格式化的字符串
String message = String.format("Hello, %s, Next year, you'll be %d, name age + 1");
```

<br/>
<br/>

### 文件输入与输出

要读取一个文件，需要构造一个 Scanner 对象。

```java
Scanner in = new Scanner(Path.of("1.txt"), StandardCharsets.UTF_8);
```

{{< admonition warning "警告">}}

可以提供一个字符串参数来构造一个 Scanner，但它会把字符串解释为数据，而不是文件名。

Scanner in = new Scanner("1.txt") // 会将参数看作包含 4 个字符的数据

{{< /admonition >}}

<br/>

要写入文件，需要构造一个 PrintWriter 对象。

```java
PrintWriter out = new PrintWriter("/tmp/1.txt", StandartCharsets.UTF_8);
```

<br/>
<br/>

## 控制流程

与任何程序设计语言一样，Java 支持使用条件语句和循环结构来确定控制流程。

<br/>
<br/>

### 块作用域

块（即复合语句），由若干条 Java 语句组成，并用大括号括起来。块确定了变量的作用域。

开可以嵌套，不能在嵌套的两个块中声明同名的变量。

```java
public static void main(String[] args) {
    int n;
    ...
    {
        int k;
    }
}
```

<br/>
<br/>

### 条件语句

```java
// if
if (condition) {
    statement1;
    statement2;
}

// if else
if (condition) {
    satement1;
} else {
    statement2;
}

// if...else...if
if (condition) {
    xxx;
} else if (condition2) {
    xxx;
} else if (condition3) {
    xxx;
} else {
    xxx;
}
```

<br/>
<br/>

### 循环

`while` 和 `do/while` 都是不定循环。

```java
// while
while (condition) {
    xxx;
}

// do...while
do statement while (condition);
```

<br/>
<br/>

### 确定循环

`for` 循环是支持迭代的一种通用结构。有一条不成文的规则：for 语句的 3 个部分应该对同一个计数器变量进行初始化、检测和更新。若不遵守这一规则，所写的循环很可能晦涩难懂。

可以在不同的 for 循环中定义同名的变量。

```java
for (int i = 1; i <= 10; i++) {
    ...
}
```

<br/>
<br/>

### 多重选择 switch 语句

如果忘记在一个分支末尾增加 breake 语句，有可能会触发多个分支。这种情况相当危险，常会引发错误。

注意  switch 表达式中的 yield 关键字。yield 会终止执行，但与 breake 不同，它还会生成一个值，这就是表达式的值。

```java
// Java 14 case
switch (xxx) {
    case xx1 ->
        ...
    case xx2 ->
        ...
    case xx3 ->
        ...
    default ->
        System.out.println("Error!")
}

// Java 1.0 case
switch (xxx) {
    case x1:
        ...
        break;
    case x2:
        ...
        break;
    default:
        ...
        break;
}
```

<br/>

switch 表达式的每个分支必须生成一个值。如果无法做到，则使用 yield 语句。不允许跳出（也就是 return）。

```java
// 无直通行为
case "Spring" -> {
    System.out.println("spring time");
    yield 6;
}

// 直通行为
case "Summer" -> 6;
```

<br/>
<br/>

### 中断控制流

尽管 Java 的设计者将 `goto` 仍作为一个保留字，但实际上并不打算在语言中包含 goto（带标签的 break 支持了这种程序设计风格）。

`break` 语句，`continer` 语句。

```java
// 不带标签的 break
while (years <= 100) {
    balance += payment;
    double interest = balance * interestRate / 100;
    balance += interest;
    if (condition) break;
    years++;
}

// 带标签的 break，允许跳出多重嵌套的循环。
label: {
    ...
    if (condition) break label;
    ...
}

// continue 语句，将中断正常的控制流程。
```

<br/>
<br/>

## 大数

如果基本的整数和浮点数精度无法满足要求，可使用 `java.math` 包中两个类：BigInteger 和 BigDecimal。这两个类可处理任意长度数字序列的数值。

不能使用算符运算符（如加减乘除等）来组合大数，而需要使用大数类中的 `add` 等方法。

<br/>
<br/>

## 数组

数组存储相同类型值的序列。

<br/>
<br/>

### 声明数组

一旦创建数组，就不能再改变它的长度。允许长度为 0 的数组。

创建一个数字数组是，所有元素都初始化为 0。布尔数组的元素会初始化为 false。对象数组（如 String）的元素则初始化为 null，表示为存放任何对象。

```java
// 类型[] 数组变量名
int[] a;

int[] b = new int[10];

int[] c = {1, 2, 3, 4, 5};

// 注意最后一个值后面的逗号
String[] authors = {
    "A",
    "B",
    "C",
    // add more names here
}
```

<br/>
<br/>

### 访问数组元素

数组元素的索引从 0 开始。访问超限索引会出现索引越界异常。

```java
int[] a = new int[10];
for (int i; i < a.length; i++) {
    a[i] = i;
}
```

<br/>
<br/>

### for each 循环

Java 有一种功能很强的循环结构，可用来依次处理数组中的每个元素，而不必考虑索引值。

Java 的设计者也曾考虑过使用如 `foreach` 和 `in` 这样的关键字，但这种循环并不是最初就包含在 Java 语言中，而是后来添加的。没有人希望破坏已经包含同名方法或变量的来到吗。

`for each` 将会遍历数组中的每个元素，而不是索引值。

```java
// collection 必须是一个数组，或实现了 Iterable 接口的类对象
for (variable : collection) statement

for (int element : a) {
    System.out.println(element);
}
```

<br/>
<br/>

### 数组拷贝

在 Java 中，允许将一个数组变量拷贝至另一个数组变量。这时，两个变量将引用同一个数组。也就是修改任何一个变量都会修改到原数组。

```java
int[] aa = a;
// a[9] is also 99
aa[9] = 99;
```

![数组拷贝](https://raw.githubusercontent.com/zhang21/images/master/cs/java/3-14.png)

<br/>

如果希望拷贝到一个新的数组中，需要使用 Arrays 类的 `copyOf` 方法。

```java
// 第二个参数是新数组的长度
// 如果长度大于原来，新增的元素按照类型填充默认值
// 如果长度小于原来，则只拷贝前面的值
int[] copyA = Arrays.copyOf(a, a.length);
```

<br/>
<br/>

### 命令行参数

请注意，Java 程序中程序名并不存储在 args 数组中。也就是 `args[0]` 是第一个参数（如 "-h"），而不是程序本身。

```java
public class Message {
    public static void main(String[] args) {
        if (args.length == 0 || args[0].equals("-h")) {
            System.out.print("Hello,");
        } else if (args[0].equals("-g")) {
            System.out.print("Goodbye,");
        }
        for (int i = 1; i < args.length; i++) {
            System.out.print(" " + args[i]);
        }
        System.out.println("!");
    }
}
```

<br/>
<br/>

### 数组排序

<br/>
<br/>

### 多维数组

`for each` 循环语句不会自动循环处理二维数组的所有元素。它会循环处理行，而这些行本身就是一维数组。如需访问多维数组，需要使用多个嵌套循环。

```java
// 二维数组（矩阵）
// aa[i][j]
int[][] aa = {
    {1, 2, 3},
    {11, 22, 33},
    {111, 222, 333}
};

for (int[] row : a) {
  for (int value: row) {
    do something with value
  }
}
```

<br/>
<br/>

### 不规则数组

Java 实际上没有多维数组，只有一维数组。多维数组被解释为数组的数组。

![多维数组](https://raw.githubusercontent.com/zhang21/images/master/cs/java/3-15.png)

<br/>

---

<br/>

# 对象与类

<br/>
<br/>

## 面向对象程序设计概述

面向对象程序设计（OOP, Object-Oriented Programming）是当今的主流程序设计范型。

![过程式程序设计与面向对象程序设计](https://raw.githubusercontent.com/zhang21/images/master/cs/java/4-1.png)

<br/>
<br/>

### 类

类（class）指定了如何构造对象。可以将类想象成制作小甜饼的模具，将对象想象为小甜饼。由一个类构造（construct）对象的过程称为创建这个类的一个实例（instance）。

封装（encapsulation）是处理对象的一个重要概念。从形式上看，封装就是将数据和行为组合在一个包中，并对对象的使用者隐藏具体的实现细节。对象中的数据称为实例字段（instance field），操作数据的过程称为方法（method）。作为一个类的实例，一个特定对象有一组特定的实例字段值，这些值的集合就是这个对象的当前状态（state）。

面向对象编程的另一个原则（可通过扩展其他类来构建新类）会让用户自定义 Java 类变得更为容易。事实上，Java 提供了一个神通广大的超类（名为 Object），所有其他类都扩展自这个 Object 类。

通过扩展一个类来得到另外一个类的概念称为继承（inheritance）。

<br/>
<br/>

### 对象

对象的三个主要特征：

- 对象的行为（behavior）：可以对对象做哪些操作，应用哪些方法？
- 对象的状态（state）：调用那些方法时，对象会如何响应？
- 对象的标识（identity）：如何区分可能有相同行为和状态的不同对象？

<br/>
<br/>

### 识别类

编写程序，首先从识别类开始，然后再为各个类添加方法。

<br/>
<br/>

### 类之间的关系

类之间的常见关系：

- 依赖
- 聚合
- 继承

应当尽可能地减少相互依赖的类。用软件工程的术语来说，就是要尽可能减少类之间的耦合。

<br/>
<br/>

## 使用预定义类

在 Java 中，没有类就无法做任何事情。

<br/>
<br/>

### 对象与对象变量

要想使用对象，首先必须构造对象，并指定其初始状态，然后对对象应用方法。

在 Java 中，要使用构造器（构造函数）构造新实例。构造器是一种特殊的方法，其作用是构造并初始化对象。

构造器总是与类同名。因此，Date 类的构造器就名为 Date。

对象变量并不实际包含对象，它只是引用一个对象。

```java
// 构造一个 Date 对象
new Date();
// 将对象放在变量中
Date rightNow = new Date();
```

![对象变量引用同一个对象](https://raw.githubusercontent.com/zhang21/images/master/cs/java/4-4.png)

<br/>
<br/>

### LocalDate 类

表示时间点的 Date 类；日历表示法表示日期的 LocalDate 类。

不要使用构造器来构造 LocalDate 类的对象。实际上，应当使用静态工厂方法（factory method）。

```java
LocalDate.now()
```

<br/>
<br/>

### 更改器方法与访问器方法

更改器方法（mutator method），会修改对象的状态。

只访问对象而不修改对象的方法有时称为访问器方法（accessor method）。如 `LocalDate.getYear` 就是访问器方法。

<br/>
<br/>

## 自定义类

编写更复杂的应用所需的主力类。

<br/>
<br/>

### 定义一个简单的类

在 Java 中，最简单的类定义形式如下。

```java
class ClassName {
    field1
    ...
    constructor1
    ...
    method1
    ...
}
```

<br/>

一个非常简单的 Employee 类示例。一个源文件只能由一个公共类，但可以有任意数目的非公共类。

下面的示例中，一个源文件包含了两个类。编译下面的示例代码时，编译器将在目录中创建两个类文件：`EmployeeTest.class` 和 `Employee.class`。

```java
import java.time.*;

/*
 * Tests the Employee class
 * @version 4-2
 * @author
 */
public class EmplyeeTest {
    public static void main(String[] args) {
        // fill the staff array with three Employee objects
        Employee[] staff = new Employee[3];

        staff[0] = new Employee("A", 100, 2000, 01, 1);
        staff[1] = new Employee("B", 200, 2001, 02, 20);
        staff[2] = new Employee("C", 30, 2000, 10, 15);

        // raise everyone's dalary by 10%
        for (Employee e : staff) {
            e.raiseSalary(10);
        }

        // print out info about all Employee objects
        for (Employee e : staff) {
            System.out.println("name=" + e.getName() + ",salary=" + e.getSalary() + ",hireDay=" + e.getHireDay());
        }
    }
}

class Employee {
    // instance fields
    private String name;
    private double salary;
    private LocalDate hireDay;

    // constructor
    public Employee(String n, double s, int year, int month, int day) {
        name = n;
        salary = s;
        hireDay = LocalDate.of(year, month, day);
    }

    // methods
    public String getName() {
        return name;
    }

    public double getSalary() {
        return salary;
    }

    public LocalDate getHireDay() {
        return hireDay;
    }
    public void raiseSalary(double byPercent) {
        double raise = salary * byPercent / 100;
        salary += raise;
    }
}
```

<br/>
<br/>

### 使用多个源文件

许多程序员习惯将各个类放在一个单独的源文件中。如，将 Employee 类放在 `Employee.java` 文件中，而将 EmployeeTest 类放在 `EmployeeTest.java` 中。

```sh
# 使用通配符编译，所有相匹配的源文件都将被编译成类文件
javac Employee*.java

# 显式编译
# Java 编译器会自动发现和搜索需要的类文件并编译。
javac EmployeeTest.java
```

<br/>
<br/>

### 一些注意事项

上面实例的一些注意事项：

- 关键字 public 意味着任何类的任何方法都可以调用这些方法。
- 关键字 private 确保只有 Employee 类本身的方法能够访问这些实例字段，任何其他类的方法都不能读写这些字段。
- 构造器
  - 构造器与类同名。
  - 每个类可以有一个以上的构造器。
  - 构造器可以有 大于等于 0 个参数。
  - 构造器没有返回值。
  - 构造器总是结合 new 操作符来调用。
  - 不能对一个已经存在的对象调用构造器来重新设置实例字段。
- 在 Java 10中，可以用 var 声明局部变量，而无须指定类型。
- 关键字 var 只能用于方法中的局部变量。参数和字段的类型必须声明。
- 使用 null 值时请小心。如果对 null 值应用方法，会产生 NullPointerException 异常。
- 定义一个类时，最好清楚地知道哪些字段可能为 null。
- 隐式参数和显式参数。
- 封装的优点。
- 基于类的访问权限。方法可以访问所属类任何对象的私有特性。
- 实现一个类时，我们会将所有实例字段都设置为私有字段，因为公共数据很危险。
- 在 Java 中，要实现一个私有方法，只需将关键字 public 改为 private 即可。
- 可以将实例字段定义为 final。

<br/>
<br/>

## 静态字段与静态方法


