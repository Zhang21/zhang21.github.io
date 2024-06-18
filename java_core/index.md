# Java核心技术


参考：

- Java核心技术-卷一
- [JUnit](https://junit.org/)

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

介绍 `static` 修饰符。

<br/>
<br/>

### 静态字段

如果将一个字段定义为 static，那么此字段并不出现在每个类的对象中。每个静态字段只有一个副本。可以认为静态字段属于类，不属于单个对象。

```java
// 假设需要为每一个员工分配唯一的标识码
// 每个 Employee 对象都有自己的 id 字段，但这个类的所有实例将共享一个 nextId 字段。
// 即使没有 Employee 对象，静态字段 nextId 也存在。
class Employee {
    private static int nextId = 1;
    private int id;
}
```

<br/>

> 注释：在一些面向对象程序设计的语言中，静态字段被称为类字段。术语静态只是沿用了 C++ 的叫法，并无实际意义。

<br/>
<br/>

### 静态常量

静态变量使用得比较少，但静态常量却很常用。

```java
// Math 类中定义了一个静态常量
// 可用 Math.PI 来访问这个常量
public class Math {
    public static final double PI = 3.14159265358979323846;
}
```

如果省略关键字 static，那么 PI 就变成了 Math 类得一个实例字段。需要通过类的一个对象来访问 PI，并且每一个对象都有它自己得一个 PI 副本。

<br/>
<br/>

### 静态方法

静态方法是不操作对象的方法。

```java
// Math 类的 pow 方法就是一个静态方法
Math.pow(x, a) // 计算幂，但并不使用对象来完成任务。
```

可以认为静态方法是没有 this 参数的方法。

Employee 类的静态方法不能访问 id 实例字段，因为它并不操作对象。但静态方法可以访问静态字段。

建议使用类名而不是对象来调用静态方法。

```java
public static int advanceId() {
    int r = nextId;
    nextId++;
    return r;
}

// 调用这个方法，需要提供类名
// int n = Employee.advanceId();
```

<br/>

以下情况可以使用静态方法：

- 方法不需要访问对象状态，因为它需要的所有参数都通过显式参数提供（如 `Math.pow`）
- 方法只需要访问类的静态字段（如 `Employee.advanceId`）

<br/>
<br/>

### 工厂方法

静态方法还有一种常见的用途。类似 LocalDate 和 NumberFormat 的类使用静态工厂方法来构造对象。如工厂方法 `LocalDate.now`。

```java
NumberFormat currencyFormatter = NumberFormat.getCurrencyInstance();
double x = 0.1;
System.out.Println(currencyFormatter.format(x));
```

为什么 NumberFormat 类不使用构造器来创建对象呢？有两个原因：

- 无法为构造器命名。构造器的命令总是要与类名相同。
- 使用构造器时，无法改变所构造对象的类型。

<br/>
<br/>

### main方法

需要指出，可以调用静态方法而不需要任何对象。例如，不需要构造 Math 类的任何对象就可以调用 `Math.pow`。

同理，main 方法也是一个静态方法。main 方法不对任何对象进行操作。事实上，启动程序时还没有任何对象。将执行静态 main 方法，并构造程序所需要的对象。

<br/>

> 提示：每一个类都可以有一个 main 方法。这是为类增加演示代码的一个技巧。

<br/>
<br/>

## 方法参数

按值调用（call by value）表示方法接收的是调用者提供的值。而按引用调用（call by reference）表示方法接收的是调用者提供的变量位置。

Java 总是采用按值调用。也就是说，方法会得到所有参数值的一个副本。具体来讲，方法不能修改传递给它的任何参数变量的内容。

Java 中对方法参数能做什么和不能作什么：

- 方法不能修改基本数据类型的参数。
- 方法可以改变对象参数的状态。
- 方法不能让一个对象参数引用一个新对象。

<br/>
<br/>

## 对象构造

Java 提供了多种编写构造器的机制。

<br/>
<br/>

### 重载

如果多个方法有相同的方法名但有不同的参数，便出现了重载（overloading）。编译器必须挑选出具体调用哪个方法，如果编译器无法匹配参数，就会产生编译错误。

```java
// 构造一个空的对象
var message = new StringBuilder();
// 或指定一个初始化字符串
var todoList = new StringBuilder("To do:\n");
```

<br/>

> 注释：Java 允许重载任何方法，而不只是构造器方法。要完整地描述一个方法，需要指定方法名以及参数类型，这叫作方法的签名。<br/>
> 如 String 类有 4 个名为 indexOf 的公共方法。它们的签名是 indexOf(int), indexOf(int, int), indexOf(String), indexOf(String, int)。

<br/>
<br/>

### 默认字段初始化

如果在构造器中没有显式地为一个字段设置初始值，就会将它自动设置为默认值。

<br/>
<br/>

### 无参数的构造器

很多类都包含无参数的构造器，由无参数构造器创建对象时，对象的状态会设置为适当的默认值。

如果类中没有构造器，就会为你提供一个无参数构造器，它讲所有的实例字段设置为相应的默认值。

如果类中提供了至少一个构造器，但是没有提供无参数构造器，那么构造对象是就必须提供参数，否则就是不合法的。

<br/>
<br/>

### 显式字段初始化

不论调用哪种构造器，每个实例字段都要设置为一个有意义的初始值，确保这一点总是一个好主意。

可以在类中直接为任何字段赋值。初始值不一定是常量值。

<br/>
<br/>

### 参数名

在编写很小的构造器时，在为参数命名时可能有些困惑。

```java
public Employee(String n, double s) {
    name = n;
    salary = s;
}
```

通常喜欢用单个字母作为参数名。但这样有一个缺点：只有阅读代码才能够了解参数 n 和 s 的含义。

<br/>

有些程序员在每个参数前加一个前缀。这样读者一样就能看懂参数的含义。

```java
public Employee(String aName, double aSalary) {
    name = aName;
    salary = aSalary;
}
```

<br/>

还有一种常用的技巧，它基于参数变量会屏蔽同名的实例字段。

```java
public Employee(String name, double salary) {
    this.name = name;
    this.salary = salary;
}
```

<br/>
<br/>

### 调用另一个构造器

关键字 this 指示一个方法的隐式参数。不过，这个关键字还有另外一个含义。

如果构造器的第一个语句形如 `this(...)`，此构造器将调用同一个类的另一个构造器。

```java
// 当调用 new Employee(600) 时，Employee(double) 构造器将调用 Employee(String, double) 构造器。
public Employee(double s) {
  // call Employee(String, double)
  this("Employee #" + NextId, s);
  nextId++;
}
```

<br/>
<br/>

### 初始化块

初始化实例字段的方法：

- 在构造器中设置值；
- 在声明中赋值；
- 初始化块（initialization block）。

```java
class Employee {
    ...
    // object initialization block
    {
        id = nextId;
        nextId++;
    }
}
```

无论使用哪个构造器构造对象，id 字段都会在对象初始化块中初始化。首先运行初始化块，然后才运行构造器的主体部分。

<br/>
<br/>

### 对象析构

有些面向对象的程序设计语言有显式的析构器方法，其中放置一些清理代码，当对象不再使用可能需要执行清理代码。在析构器中，最常见的操作是回收分配给对象的内存空间。由于 Java 会完成自动的垃圾回收，不需要人工回收内存，所以 Java 不支持析构器。

当然，某些对象使用了内存之外的其他资源。在这种情况下，当资源不再需要时，将其回收和再利用就十分重要。

如果一个资源一旦使用完就需要立即关闭，那么应该提供一个 close 方法来完成必要的清理工作。

如果可以等到虚拟机退出，那么可以增加一个关闭勾子。在 Java 9 中，可使用 Cleaner 类注册一个动作来清理。

> 警告：不要使用 finalize 方法来完成清理。该方法已被废弃。

<br/>
<br/>

## 记录

有时，数据就只是数据，而面向对象提供的数据隐藏有些碍事。

如平面上的一个点就用 x 和 y 坐标来描述。

为了更简洁的定义这些类，JDK 14 引入了一个预览特性：记录。最终版本在 JDK 16 中发布。

<br/>
<br/>

### 记录概念

记录（record）是一种特殊形式的类，其状态不可变，而且公共可读。

一个记录的实例字段称为组件。

不能为记录增加实例字段。

```java
// 如将一个平面坐标定义为一个记录
record Point(double x, double y) { }

// 其结果是有以下实例字段的类
private final double x;
private final double y;

// 这个类有一个构造器
Point(double x, double y)

// 和以下访问器方法
public double x()
public double y()
```

除了字段访问器方法，每个记录有 3 个自动定义的方法：`toString`, `equals` 和 `hashCode`。

<br/>
<br/>

## 包

Java 允许使用包（package）将类组织到一个集合中。借助包可以方便地组织你的代码，并将你自己的代码与其他人提供的代码库分开。

<br/>
<br/>

### 包名

使用包的主要原因是确保类名的唯一性。如多个包中都有一个 Employee 类，就不会冲突。事实上，为了包名的绝对唯一性，可以使用因特网域名以逆序的形式作为包名，然后对于不同的项目使用不同的子包。

如 `hosrstmann.com`，如果逆序就得到包名 `com.hosrtmann`。然后可以追加一个项目命，如 `com.hosrtmann.corejava`。如果再把类加上，那么这个类的完全限定名是 `com.horstmann.corejava.Employee`。

<br/>

> 从编译器的角度来看，嵌套的包之间没有任何关系。如 java.util 包与 java.util.jar 包毫无关系。每一个包都是独立的类集合。

<br/>
<br/>

### 类的导入

一个类可以使用所属包（这个类所在的包）中的所有类，以及其他包中的公共类（public class）。

可以采用两种方式访问另一个包中的公共类：

- 第一种方式是使用完全限定名。
- 更简单和更常用的方式是使用 import 语句。

```java
// 完全限定名
java.time.LocalDate today = java.time.LocalDate.now();


// import 语句导入
// import java.time.*;
import java.time.LocalDate;
LocalDate today = LocalDate.now();
```

在包中定位类是编译器的工作。类文件中的字节码总是使用完整的包名来索引其他类。

<br/>
<br/>

### 静态导入

有一种 import 语句允许导入静态方法和静态字段，而不只是类。

```java
// import static java.lang.System.*;
import static java.lang.System.out;

out.println("xxx"); // System.out
```

实际上很多程序员想要写成 `System.out`，这样写出来的代码更直观。

<br/>
<br/>

### 在包中增加类

要想将类放入包中，就必须将包名放在源文件的开头。

如果没有在源文件中防止 package 语句，那么这个源文件中的类就属于无名包（unnamed package）。无名包没有包名。

```java
package com.horstmann.corejava
// 包中的源文件目录 com/horstmann/corejava

import java.time.*;

public class Employee {
    ...
}
```

<br/>
<br/>

### 包访问

前面已经见过访问修饰符 public 和 private。标记为 public 的部分可以由任意类使用；标记为 privte 的部分只能由定义它们的类使用。如果没有指定 public 或 private，这个部分（类、方法、变量）可以由同一个包中的所有方法访问。

<br/>
<br/>

### 类路径

类存储在文件系统的子目录中，类的路径必须与包名匹配。

另外，类文件也可以存储在 JAR 文件中在一个 JAR 文件中，可以包含多个压缩格式的文件和子目录，这样既可以节省空间又可以改善性能。在程序中用到第三方的库时，你通常会得到一个或多个需要包含的 JAR 文件。

<br/>

> 提示：JAR 文件使用 ZIP 格式组织文件和子目录。可使用任何 ZIP 工具查看 JAR 文件。

<br/>

为了使类能够被多个程序共享，需要做到一下几点：

- 把类文件放到一个目录中。
- 将 JAR 文件放在一个目录中。
- 设置类路径。类路径是包含类文件的路径的集合。

```sh
# 在 UNIX 环境下，类路径各项之间用冒号 : 分割
/home/user/classdir:.:/home/user/archives/archive.jar

# 在 Windows 环境下，则以分号 ; 分隔
c:\classdir;.;c:\archives\archive.jar
```

<br/>
<br/>

### 设置类路径

最好使用 `-classpath`（或 -cp, 或 Java 9 中的 `--class-path`）选项指定类路径。

另一种方式是通过设置 CLASSPATH 环境变量来指定类路径。

```sh
java -classpath /home/user/classdir:.:/host/user/archives/archive.jar MyProg

export CLASSPATH=/home/user/classdir:.:/host/user/archives/archive.jar
```

<br/>

> 警告：有人建议永久地设置 CLASSPATH 环境变量。一般来说这是一个糟糕的想法。人们可能会忘记全局设置。

<br/>
<br/>

## JAR文件

在将应用程序打包时，你希望只向用户提供一个单独的文件，而不是一个包含大量类文件的目录结构。Java 归档（JAR）文件就是为此而设计的。JAR 文件即可以包含类文件，也可以包含注入图像和声音等其他类型的文件。

此外，JAR 文件是压缩的，它使用了我们熟悉的 ZIP 压缩格式。

<br/>
<br/>

### 创建JAR文件

可使用 jar 工具制作 JAR 文件。

```sh
# 类似于 tar 命令的选项
jar cvf jarFileName file1 file2 ...
```

<br/>
<br/>

### 清单文件

除了类文件、图像和其他资源外，每个 JAR 文件还包含一个清单文件（manifest），用于描述归档文件的特殊特性。

清单文件被命名为 `MANIFEST.MF`，它位于 JAR 文件的一个特殊的 `META-INF` 子目录中。

<br/>
<br/>

### 可执行JAR文件

通过 jar 命令的 e 选项指定程序的入口点，即通常调用 java 执行程序时指定的类。

```sh
jar cvfe MyProgram.jar com.mycompany.mypkg.MainAppClass files...

jar -jar MyProgram.jar
```

<br/>
<br/>

### 多版本JAR文件

随着模块和包强封装的引入，之前可访问的一些内部 API 不再可用。这可能要求库提供商为不同 Java 版本发布不同的代码。为此，Java 9 引入了多版本 JAR。

<br/>
<br/>

## 文档注释

JDK 包含一个很有用的工具，叫作 javadoc，它可以由源文件生成一个 HTML 文档。

想想 godoc 和 pydoc 就明白了。

<br/>
<br/>

### 注释的插入

javadoc 工具从以下几项中抽取信息：

- 模块
- 包
- 公共类与接口
- 公共的和受保护的字段
- 公共的和受保护的构造器及方法

每个 `/** ... */` 文档注释包含标记以及之后紧跟着的自由格式文本。标记如 `@author`。自由格式文本的第一个句子应该是一个概要陈述。javadoc 工具自动地将这些句子抽取出来生成概要页。

在自由格式文本中，可以使用 HTML 修饰符。

> 注释：如果文档中有到其他文件的链接，如图像文件等，就应该将这些文件放到包含源文件的目录下的一个子目录 doc-files 中。javadoc 工具将从源目录将 doc-files 目录及其内容复制到文档目录中。

<br/>
<br/>

### 类注释

类注释必须放在 import 语句之后，class 定义之前。

```java
/**
 * doc1
 * doc2
 * doc3
 */ 
public class Card {
    ...
}
```

<br/>
<br/>

### 方法注释

方法注释必须放在所描述的方法之前。除了通用标记外，还可以使用以下标记。

```java
/**
 * raiseSalary raises the salary of an employee
 * @param variable description
 * @return description
 * @throws class description
 */
public double raiseSalary(double byPercent) {
    ...
}
```

<br/>
<br/>

### 字段注释

只需要对公共字段（通常值静态常量）增加文档注释。

```java
/**
 * The "Hearts" card suit
 */
public static final int HEARTS = 1;
```

<br/>
<br/>

### 包注释

要想产生包注释，就需要在每一个包目录中添加一个单独的文件。可以有如下两个选择：

- 提供一个名为 `package-info.java` 的文件。这个文件必须包含一个初始的 javadoc 注释（以 `/** ... */` 界定），后面是一个 package 语句。它不能包含一更多的代码或注释。
- 提供一个名为 `package.html` 的 HTML 文件，抽取 `<body> ... </body>` 之间的所有文本。

<br/>
<br/>

## 类设计技巧

介绍几点技巧，应用这些技巧可以使你设计的类更能得到专业 OOP 圈子的认可。

- 一定要保证数据私有。绝对不要破坏封装性。
- 一定要初始化数据。
- 不要在类中使用过多的基本类型。其想法是要用其他的类，而不是使用多个相关的基本类型。这样会使类更易于理解，也易于修改。
- 不是所有的字段都需要单独的字段访问器和更改器。
- 分解有过多职责的类。
- 类名和方法名要能够体现它们的职责。
- 优先使用不可变的类。

<br/>

---

<br/>

# 继承

继承（inheritance）的基本思想是，可以基于已有的类创建新的类，这是 Java 程序设计的一项核心技术。

反射（reflection）是指在程序运行期间更多地了解类及其属性的能力。

<br/>
<br/>

## 类、超类和子类

比如 Manager 类，增加一些新功能，但可以重用 Employee 类中已经编写的部分代码，并保留 Employee 类中的所有字段。

从理论上讲，在 Manager 与 Employee 之间存在着明显的 "is-a" 关系，这个关系是继承的一个明显特征。

<br/>
<br/>

### 定义子类

使用关键字 `extends` 表示继承，只是正在构造的新类派生于一个已存在的类。这个已存在的类称为超类（superclass）、基类（base class）或父类（parent class）；新类称为子类（subclass/child class）或派生类（derived class）。

```java
public class Manager extends Employee {
    private double bonus;
    ...
    public void setBonus(double bonus) {
        this.bonus = bonus;
    }
}
```

<br/>

> 注释：Java 语言规范指出：声明为私有的类成员不会被这个类的子类继承。

<br/>
<br/>

### 覆盖方法

超类中有些方法对子类并不一定适用。

比如 Manager 类中的 getSalary 方法应该返回薪水和奖金的总和。为此，需要提供一个新的方法来覆盖（override）超类中的这个方法。

只有 Employee 方法能直接访问 Employee 类的私有字段，这意味着 Manager 类的 getSalary 方法不能直接访问 salary 字段。如果 Manager 类的方法想访问那些私有字段，就必须像所有其他方法一样使用公共接口，这里就是要使用 Employee 类中的公共方法 getSalary。

我们希望调用超类 Employee 中的 getSalary 方法，而不是当前类的这个方法。为此，可以使用特殊的关键字 `super` 来解决这个问题。 super 只是一个指示编译器调用超类方法的特殊关键字。

```java
// 在 Manager 子类中
public double getSalary() {
    double baseSalary = super.getSalary();
    return baseSalary + bonus;
}
```

<br/>
<br/>

### 子类构造器

由于 Manager 类的构造器不能访问 Employee 类的私有字段，所以必须通过一个构造器来初始化这些字段。利用特殊的 super 语法调用这个构造器。使用 super 调用构造器的语句必须是子类构造器的第一条语句。

```java
public Manager(String name, double salary, int year, int month, int day) {
    // 调用超类 Employee 中 n, s, year, month 和 day 参数的构造器的简写
    super(name, salary, year, month, day);
    bonus = 0;
}
```

<br/>
<br/>

### 继承层次结构

继承并不仅限于一个层次。例如，可以由 Manger 类派生初 Executive 类。有一个公共超类派生出来的所有类的集合称为继承层次结构。

在继承层次结构中，从某个特定的类到其祖先的路劲称为该类的继承链（inheritance chain）。

通常，一个祖先类可以有多个子孙链。

![继承层次结构](https://raw.githubusercontent.com/zhang21/images/master/cs/java/5-1.png)

<br/>
<br/>

### 多态

在 Java 程序设计中，对象变量时多态的（polymorphic）。一个 Employee 类型的变量既可以引用一个 Employee 类型的对象，也可以引用它的任何一个子类的对象。

```java
Manager boss = new Manager(...);
Employee[] staff = new Employee[3];
staff[0] = boss;
```

<br/>
<br/>

### 理解方法调用

<br/>
<br/>

### 阻止继承

有时候，可能希望阻止定义某个类的子类。不允许扩展的类被称为 final 类。final 类中的所有方法自动成为 final 方法。

```java
// 假设希望组织人们派生 Executive 类的子类
public final class Executive extends Manager {
    ...
}
```

也可以将类中的某个特定方法声明为 final，那么所有子类都不能覆盖这个方法。

```java
public class Employee {
    ...
    public final String getName() {
        return name;
    }
}
```

<br/>

> 注释：枚举和记录总是 final，它们不允许扩展。

<br/>
<br/>

### 对象引用的强制类型转换

正像有时候需要将浮点数转换成整数一样，可能还需要将某个类的对象引用转换成另外一个类的对象引用。

进行强制类型转换的唯一原因：要在暂时忘记对象的实际类型之后使用对象的全部功能。

只能在继承层次结构内进行强制类型转换。在进行强制类型转换之前，先查看是否能够成功地转换。使用 `instanceof` 操作符。

```java
// int nx = (int) 3.405;
Manager boss = (Manager) staff[0];

if (staff[i] instanceof Manager) {
    boss = (Manager) staff[i];
    ...
} 
```

<br/>
<br/>

### 受保护的访问

大家都知道，最好将类中的字段标记为 private，而方法标记为 public。任何声明为 private 的特性都不允许其他类访问。子类也不能访问超类的私有字段。

有时，可能希望限制超类中某个方法只允许子类访问，或允许子类的方法访问超类的某个字段。在这种情况下，可以将一个类特性（方法或字段）声明为受保护（protected）。

例如，将超类 Employee 中的 hireDay 字段声明为 protected，这样 Manger 方法就可以直接访问这个字段。

在 Java 中，受保护字段只能由同一个包中的类访问，避免了滥用此机制随意地派生子类来访问受保护的字段。

在实际使用中，要谨慎使用受保护字段。假设你的类要提供给其他人使用，而你在设计这个类时设置了一些受保护字段。其他人可能会由这个类派生新类，并访问你的受保护的字段。在这种情况下，如果你想修改你的类实现，就势必会影响到其他人。这违背了 OOP 提倡数据封装的精神。

受保护的方法更有意义。如果一个类的某个方法使用很棘手，就可以将它声明为 protected。这表明可以相信这个子类能正确地使用这个方法，而其他类则不行。

<br/>

Java 中的 4 个访问控制修饰符：

- 仅本类可以访问——private。
- 可由外部访问——public。
- 本包和所有子类可以访问——protected。
- 本包中可以访问——默认，不需要修饰符。

<br/>
<br/>

## Object类

Object 类是 Java 中所有类的始祖，每一个类都扩展了 Object，它是所有类的超类。

<br/>
<br/>

### Object类型的变量

可使用 Object 类型的变量引用任何类型的对象。

当然，Object 类型的变量只能用于作为任意值的一个泛型容器。要相对其中的类型进行具体的操作，还需要清楚对象的原始类型，并进行相应的强制类型转换。

```java
Object obj = new Employee("AA", 11111);

Employee e = (Employee) obj;
```

<br/>

在 Java 中，只有基本类型（如数值、字符和布尔等）不是对象。

所有的数组类型都扩展了 Object 类的类类型。

<br/>
<br/>

### equals方法

Object 类中的 equals 方法用于检测一个对象是否等于另外一个对象。

<br/>
<br/>

### 相等测试与继承

<br/>
<br/>

### hashCode方法

散列码（hash code）是由对象导出的一个整型值。散列码没有规律。如果 x 和 y 是两个不同的对象，那么 `x.hashCode()` 与 `y.hashCode()` 基本上不会相同。

<br/>
<br/>

### toString方法

Object 类中的 toString 方法，会返回一个字符串，表示这个对象的值。

<br/>
<br/>

## 泛型数组列表

有一些程序设计语言，必须在编译时就确定所有数组的大小。在 Java 中，它允许在运行时确定数组的大小。

一旦确定了数组的大小，就无法再轻松地改变了。在 Java 中，要处理这个常见的情况，可使用 Java 中的 `ArrayList` 类。ArrayList 类与数组类似，但在添加或删除元素时，它能够自动地调整容量，而不需要为此额外编写代码。

ArrayList 是一个有类型参数的泛型类（generic class）。

<br/>
<br/>

### 声明数组列表

声明和构造一个保存 Employee 对象的数组列表：

```java
ArrayList<Employee> staff = new ArrayList<Employee>();
```

数组列表管理着一个内部的对象引用数组。如果数组的空间用尽，数组列表就会自动地创建一个更大的数组，并将队友对象从较小的数组拷贝到较大的数组。

<br/>
<br/>

### 访问数组列表元素

为了提供数组列表自动扩容的遍历，这要求使用一种更复杂的语法来访问元素。其原因是 ArrayList 类并不是 Java 程序设计语言的一部分，它只是标准库中的一个实用工具类。

不能使用 `[]` 语法格式来访问或改变，而要使用 get 和 set 方法。

<br/>
<br/>

### 类型化与原始数组列表的兼容性

在你的代码中，可能总是想用类型参数来增加安全性。

<br/>
<br/>

## 对象包装器与自动装箱

有时，需要将 int 这样的基本类型转换为对象。所有的基本类型都有一个与之对应的类。如 Integer 类对应基本类型 int。通常，这些类称为包装器（wrapper）。

这些包装器类的名字：Ingeter, Long, Float, Double, Short, Byte（前 6 个派生于公共超类 Number）, Character 和 Boolean。包装器类是不可变的，即一旦构造了包装器，就不语寻更改包装在其中的值。同时，包装器类还是 final，不能派生它们的子类。

```java
// 定义一个整型数组列表
var list = new ArrayList<Integer>();
```

<br/>

> 警告：由于每个值分别包装在一个对象中，所以 `ArrayList<Integer>` 的效率远低于 `int[]` 数组。因此，只有当程序员操作的方便性比执行效率更重要时，才会考虑对较小的集合使用这种构造。

<br/>

幸运的是，有一个很有用的特性，可以容易地向 `ArrayList<Integer>` 添加 int 类型的元素。

```java
// 自动装箱
list.add(3); // 将自动转换为 list.add(Integer.valueOf(3));

// 自动拆箱
int n = list.get(i); // 将转换为 int n = list.get(i).intValue();
```

这种转换称为自动装箱（autoboxing）。

反过来，当将一个 Integer 对象赋值给一个 int 值是，将会自动拆箱。

<br/>
<br/>

## 参数个数可变的方法

变参（varargs），参数个数可变的方法。

Java 代码中的省略号（...），表示这个方法可以接收任意数量的对象。

```java
public class PrintStream {
    public PrintStream printf(String fmt, Object... args) {
        return format(fmt, args);
    }
}
```

<br/>
<br/>

## 抽象类

从某种角度看，祖先类更有一般性，人们只将它作为派生其他类的基类，而不是用来构造你想使用的特定实例。如员工和学生都属于人类。

在 Java 程序设计语言中，抽象方法是一个重要的概念。

为了提高程序的清晰性，包含一个或多个抽象方法的类本身必须被声明为抽象的。

```java
public abstract class Person {
    ...
    public abstract String getDescription();
}
```

Student 类定义了 `getDescription` 方法。因此，在 Student 类中的全部方法都是具体的，这个类不再是抽象类。

```java
public class Student extends Person {
    ...
    public String getDescription() {
        return "a student majoring in " + major;
    }
}
```

<br/>
<br/>

## 枚举类

如果需要的话，可以为枚举类型增加构造器、方法和字段。当然，构造器只是在构造枚举常量的时候调用。

枚举的构造器总是私有的。可以省略 private 修饰符。如果声明一个 enum 构造器为 public 或 protected，则会出现语法错误。

所有枚举类型都是抽象类 Enum 的子类。

```java
public enum Size {
    SMALL("S"), MEDIUM("M"), LARGE("L"), EXTRA_LARGE("XL");

    private String abbreviation;

    Size(String abbreviation) {
        this.abbreviation = abbreviation;
    }

    public String getAbbreviation() {
        return abbreviation;
    }
}
```

<br/>
<br/>

## 密封类

除非一个类声明为 final，否则任何人都可以派生这个类的子类。如果相对它有更多控制权呢？

在 Java 中，密封类（sealed class）会控制哪些类可以继承它。Java 15 中作为一个预览特定增加了密封类，并在 Java 17 中最终确定了这个特性。

```java
public abstract sealed class JSONValue {
    permits JSONArray, JSONNumber, JSONString, JSONBoolean, JSONObject, JSONNull {
        ...
    }
}
```

如果试图定义一个未经允许的子类，将是一个错误。

<br/>
<br/>

## 反射

反射库（reflection library）提供了一个丰富且精巧的工具集，可用来编写动态操纵 Java 代码的程序。使用反射，Java 可以支持用户界面生成器、对象关系映射以及很多其他需要动态查询类能力的开发工具。

能够分析类能力的程序称为可反射（reflective）。反射机制的功能：

- 在运行时分析类的能力。
- 在运行时检查对象。
- 实现泛型数组操作代码。
- 利用 Method 对象，这个对象很像 C++ 中的函数指针。

<br/>
<br/>

## 继承的设计技巧

使用继承时的一些技巧：

- 将公共操作和字段放在超类中。
- 不要使用受保护的字段。
- 使用继承实现 "is-a" 关系。
- 除非所有继承的方法都有意义，否则不要使用继承。
- 覆盖方法时，不要改变预期的行为。
- 使用多态，而不要使用类型信息。
- 不要滥用反射。

<br/>

---

<br/>

# 接口、lambda表达式于内部类

接口（interface）用来描述类应该做什么，而不指定它们具体应该如何做。一个类可以实现（implement）一个或多个接口。

lambda 表达式，是一种见解的方法，用来创建可以在将来某个时间点执行的代码块。通过 lambda 表达式，可以用一种精巧而简洁的方式表示使用回调或可变行为的代码。

内部类（inner class）定义在另外一个类的内部，它们的方法可以访问其外部类的字段。内部类技术在设计合作类集合时很有用。

代理（proxy）是实现任意接口的对象。

<br/>
<br/>

## 接口

<br/>
<br/>

### 接口的概念

在 Java 中，接口不是类，而是对希望符合这个接口的类的一组需求。

接口中的所有方法都自动是 public 方法。因此可以不必提供关键字 public。

```java
public interface Comparable {
    int compareTo(Object other);
}
```

在 Comparable 接口中，compareTo 方法是抽象的，它没有具体实现。任何实现此接口的类都需要包含一个 compareTo 方法。

<br/>

要让类实现一个接口，需要完成两个步骤：

- 将类声明为实现给定的接口
- 对接口中的所有方法提供定义

```java
class Employee implements Comparable
```

<br/>
<br/>

### 接口的属性

接口不是类，不能使用 `new` 操作符实例化一个接口。但可以声明接口变量。

可以使用 instanceof 检查一个对象是否实现了某个接口。

与建立类的继承层次结构一样，也可以扩展接口。

接口中的方法都自动为 public，接口中的字段总是 public static final。

<br/>

> 注释：可以将接口方法显式标记为 public，将字段标记为 public static final，这是合法的。但 Java 语言规范建议不要提供冗余的关键字。

<br/>
<br/>

### 接口与抽象类

使用抽象基类表示通用属性存在一个严重的问题。每个类只能扩展一个类。但每个类可以实现任意多个接口。

其他语言允许一个类有多个超类。Java 的设计者选择不支持多重继承，主要原因是多重继承会让语言变得非常复杂。

因此为什么要引入接口，而不是只使用抽象类。

实际上，接口可以提供多重继承的大多数好处，同时还能避免多重继承的复杂性和低效性。

<br/>
<br/>

### 静态和私有方法

在 Java 8 中，允许在接口中增加静态方法。只是这似乎有违将接口作为抽象规范的初衷。

目前为止，通常的做法都是将静态方法放在伴随类中。在标准库中，你会看到成对出现的接口和实用工具类（如 `Path/Paths`）。

类似地，实现你自己的接口时，没有理由再为实用工具方法另外提供一个伴随类。

```java
public interface Path {
    public static Path of(URI uri) {...}
    public static Path of(String first, String... more) {...}
    ...
}
// 这样一来，Path 类就不再是必要的了。
```

在 Java 9 中，接口中的方法可以是 private 方法。

<br/>
<br/>

### 默认方法

可以为任何接口方法提供一个默认实现，必须用 default 装饰符标记。

默认方法的一个重要用法是接口演化。

<br/>
<br/>

### 解决默认方法冲突

如果先在一个接口中将一个方法定义为默认方法，然后又在超类或其他接口中定义了同样的方法，回发生什么情况？

规则如下：

1. 超类优先。
2. 接口冲突。必须覆盖这个方法来解决冲突。

<br/>
<br/>

### 接口与回调

回调（callback）是一种常见的程序设计模式。在此模式中，可以指定某个特定事件发生时应该采取的动作。

<br/>
<br/>

### Comparator接口

<br/>
<br/>

### 对象克隆

本节讨论 Cloneable 接口，此接口表示一个类提供了一个安全的 clone 方法。

<br/>
<br/>

## lambda表达式

lambda 表达式采用一种简洁的语法定义代码块。

<br/>
<br/>

### 为什么引入lambda表达式

lambda 表达式是一个可传递的代码块，可以在以后执行以此或多次。

在 Java 中传递一段代码并不容易，不能直接传递代码段，你必须先构造一个对象，这个对象的类要有一个方法包含所需的代码。

<br/>
<br/>

### lambda表达式的语法

lambda 表达式形式：参数、箭头以及一个表达式。

```java
# 希腊字母 lambda（λ）
(String first, String second) -> {
    first.length() - second.length();
}

// 即便 lambda 表达式没有参数，仍然要提供括号。
() -> { for (int 1 = 10; i >=0; i--) System.out.println(i); }

// 如果可以推导出参数类型，则可以忽略其类型。
Comparator<String> comp = 
    (first, second) // same as (String first, String second) ->
        first.length() - second.length();
```

<br/>

> 注释：如果一个 lambda 表达式只在某些分支返回值，而另外一些分支不返回值，这是不合法的。例如 `(int x) -> { if (x >= 0) return 1; }` 就不合法。

<br/>
<br/>

### 函数式接口

对于只有一个抽象方法的接口，需要这种接口的对象时，就可以提供一个 lambda 表达式。这种接口称为函数式接口（functional interface）。

```java
// Arrays.sort 方法的第二个参数需要一个 Comparator 实例，Comparator 就是只有一个方法的接口，
// 所以可以提供一个 lambda 表达式
Arrays.sort(words,
    (first, second) -> first.length() - second.length());
```

<br/>
<br/>

### 方法引用

有时，lambda 涉及一个方法。

```java
var timer = new Timer(1000, event -> System.out.println(event));

// 如果直接把 println 方法传递到 Timer 构造器就更好了
var timer = new Timer(1000, System.out::println);
```

表达式 `System.out::println` 是一个方法引用，它指示编译器生成一个函数式接口的实例，覆盖这个接口的抽象方法来调用给定的方法。

<br/>

方法引用示例：

| 方法引用 | 等价的 lambda 表达式 | 说明 |
| - | - | - |
| separator::equals | x -> separator.equals(x) | 这时一个包含一个对象和一个实例方法的方法表达式 |
| String::trim | x -> x.strip() | 这是包含一个类和一个实例方法的方法表达式 |
| String::concat | (x, y) -> x.concat(y) | - |
| Interger.valueOf | x -> Integer.valueOf(x) | 这是包含一个静态方法的方法表达式 |
| Inter.sum | (x, y) -> Integer.sum(x, y) | - |
| String::new | x -> new String(x) | 这是一个构造器引用 |
| String[]::new | n -> new String[n] | 数组构造器引用 |

<br/>

> 注释：包含对象的方法引用与等价的 lambda 表达式还有一个细微的差别。考虑一个方法引用，如 separator::equals。如果 separator 为 null，构造 separator::equals 时就会立即抛出一个 NullPointerException 异常。而 x ->separator.equals(x) 只在调用时才会抛出 NullPointerExecption。

<br/>
<br/>

### 构造器引用

构造器引用与方法引用很类似，只不过方法名为 new。如 `Person::new` 是 Person 构造器的一个引用。

<br/>
<br/>

### 变量作用域

通常，你可能希望在 lambda 表达式中访问外围方法或类中的变量。

lambda 表达式有 3 个部分：代码块、参数和自由变量的值。外围方法或类中的变量就属于自由变量。

lambda 表达式可以捕获外围作用域中变量的值。在 Java 中，为了确保所捕获的值是明确定义的，这里有一个重要的限制。在 lambda 表达式中，只能引用值不会改变的变量。lambda 表达式中捕获的变量必须是事实最终变量（变量初始化后就不会再为它赋新值）。

这个限制是有原因的。如果在 lambda 表达式中更改变量，并发执行多个动作时就会不安全。

<br/>

> 注释：关于代码块连同自由变量值有一个术语：闭包（closure）。在 Java 中，lambda 表达式就是闭包。

<br/>
<br/>

### 处理lambda表达式

使用 lambda 表达式的重点是延迟执行（deferred execution）。毕竟，如果想要立即执行代码，完全可以直接执行，而无需把它包装在一个 lambda 表达式中。以后在执行的原因：

- 在一个单独的线程中运行代码；
- 多次运行代码；
- 在算法的适当位置运行代码；
- 发生某种情况时运行代码；
- 只在必要时才运行代码。

<br/>
<br/>

### 再谈Comparator

Comparator 接口包含很多方便的静态方法来创建比较器。这些方法可以用于 lambda 表达式或方法引用。

<br/>
<br/>

## 内部类

内部类（inner class）是定义在另一个类中的类。

- 内部类可以对同一个包中的其他类隐藏。
- 内部类方法可以访问定义这些方法的作用域中的数据，包括原本私有的数据。

内部类原先对于简洁地实现回调非常重要，不过如今 lambda 表达式在这方面可以做得更好。但内部类对于构建代码还是很有用的。

内部类的对象会有一个隐式引用，指向实例化这个对象的外部类对象。通过这个指针，它可以访问外部对象的全部状态。静内部类没有这个附加的指针。

<br/>
<br/>

### 使用内部类访问对象状态

<br/>
<br/>

### 内部类的特殊语法规则

<br/>
<br/>

### 内部类是否有用、必要和安全

内部类的语法很复杂。内部类与 Java 语言的其他特性之间如何交互不是很明确。

内部类将转换为常规的类文件，用美元符号（`$`）分隔外部类名和内部类名。

<br/>
<br/>

### 局部内部类

声明局部类时不能有访问说明符（即 public 或 private）。局部类的作用域总是限定在声明这个局部类的块中。

<br/>
<br/>

### 由外部方法访问变量

局部类不仅能够访问外部类的字段，还可以访问局部变量（这些局部变量必须是事实最终变量）。

<br/>
<br/>

### 匿名内部类

使用局部内部类时，如果只想创建这个类的一个对象，甚至不需要为类指定名字。这被称为匿名内部类（anonymous inner class）。

尽管匿名类不能有构造器，但可以提供一个对象初始化块。

```java
var count = new Person("Dracula") {
    { initialization }
    ...
};
```

<br/>
<br/>

### 静态内部类

有时，使用内部类只是为了把一个类隐藏在另外一个类的内部，并不需要内部类有外部类对象的一个引用。为此，可以将内部类声明为 static，这样就不会生成那个引用。

只要内部类不需要访问外部类对象，就应该使用静态内部类。

静态内部类可以有静态字段和方法。

在接口中声明的内部类自动是 static 和 public。

类中声明的接口、记录和枚举都自动为 static。

<br/>
<br/>

## 服务加载器

利用服务加载器（ServiceLoader）类可以很容易地加载符合公共接口的服务。

<br/>
<br/>

## 代理

利用代理（proxy）可以在运行时创建实现了一组特定接口的新类。只有在编译时无法确定需要实现哪个接口时才有必要使用代理。

<br/>

---

<br/>

# 异常、断言和日志

为了避免错误这类事情的发生，至少应该做到以下几点：

- 向用户通知错误；
- 保存所有工作；
- 允许用户妥善地退出程序。

<br/>
<br/>

## 处理错误

为了在程序中处理异常情况，必须考虑程序中可能出现的错误和问题。

- 用户输入错误；
- 设备错误；
- 物理限制；
- 代码错误。

异常处理器（exception handler）处理异常，异常有自己的语法和一个特殊的继承层次结构。

<br/>
<br/>

### 异常分类

在 Java 语言中，异常对象都是派生于 Throwable 类的一个类的实例。

<br/>

![异常层次结构](https://raw.githubusercontent.com/zhang21/images/master/cs/java/7-1.png)

<br/>

Error 类层次结构描述了 Java 运行时系统的内部错误和资源耗尽问题。你不应该抛出这种类型的对象。

编写 Java 程序时，要重点关注 Exception 层次结构。一般规则是：由编程错误导致的异常属于 RuntimeException；如果程序本身没有问题，但由于 I/O 错误之类的问题导致的异常属于其他异常。

<br/>

继承自 RuntimeException 的异常包括以下问题：

- 错误的强制类型转换；
- 越界的数组访问；
- 访问 null 指针。

不继承自 RuntimeException 的异常包括：

- 试图越过文件末尾继续读取数据；
- 试图打开一个不存在的文件。
- 试图根据给定的字符串查找 Class 对象，而这个字符表示的类并不存在。

<br/>

Java 语言将派生于 Error 类或 RuntimeException 类的所有异常称为非检查型异常，所以其他异常称为检查型异常。编译器将检查你是否为所有的检查型异常提供异常处理。

<br/>
<br/>

### 声明检查型异常

如果遇到了无法处理的情况，Java 方法可以抛出一个异常。

```java
// 标准类库中的一个例子
public FileInputStream(String name) throws FileNotFoundException
```

<br/>

编写自己的方法时，不必声明你的方法可能抛出的所有 throwable 对象。至于如何使用 `throws` 子句，需要记住遇到下面几种情况时会抛出异常：

- 调用了一个抛出检查型异常的方法。
- 检测到一个错误，并利用 throw 语句抛出一个检查型异常。
- 程序出现错误。
- Java 虚拟机或运行时库出现内部错误。

<br/>

总之，一个方法必须声明所有可能抛出的检查型异常。而非检查型异常要么在你的控制之外，要么是由从一开始就应该避免的情况导致的。

<br/>
<br/>

### 如何抛出异常

首先应该决定抛出什么类型的异常。

如果已有的异常类能够满足需求：

- 找到一个合适的异常类；
- 创建这个类的一个对象；
- 将对象抛出。

<br/>
<br/>

### 创建异常类

如果任何标准异常类无法描述清楚你的问题，你需要创建自己的异常类。定义一个派生于 Exception 的类，或派生于 Exception 的某个子类。习惯的做法是，自定义的这个类应该包含两个构造器，一个默认的构造器，一个是包含详细描述信息的构造器。

```java
class FileFormatException extends IOException {
    public FileFormatException() {}
    public FileFormatException(String grip) {
        super(gripe);
    }
}

// throw new FileFormatException();
```

<br/>
<br/>

## 捕获异常

<br/>
<br/>

### 捕获异常概述

有些时候需要捕获异常（`try/catch` 语句）。

```java
try {
    ...
}
catch (ExceptionType e) {
    handler for this type
}
```

<br/>
<br/>

### 捕获多个异常

捕获多个异常。

```java
try {
    ...
}
catch (FileNotFoundException e) {
    ...
}
catch (UnknownHostException e) {
    ...
}
catch (IOException e) {
    ...
}

// e.getMessage() 获取更多异常信息
// e.getClass().getName() 获取异常对象的实际类型
```

<br/>
<br/>

### 再次抛出异常与异常链

可以在 `catch` 子句中抛出一个异常。

```java
try {
    access the db
}
catch (SQLException original) {
    var e = new SerletException("db error");
    e.initCause(original);
    throw e;
}
```

强烈建议使用这总包装技术。这样可在子系统中抛出高层异常，而不会丢失原始异常的细节信息。

<br/>
<br/>

### finally子句

不管是否捕获到异常，`finally` 子句的代码都会执行。

```java
var in = new FileInputStream(...);
try {
    ...
}
catch (IOException e) {
    ...
}
finally {
    in.close();
}
```

<br/>
<br/>

### try-with-Resource语句

Java 7 为自动关闭资源提供了一个很有用的 `try-with-Resource` 语句。类似于 python 中的 `with open` 语句。

```java
// try 模块退出时，会自动调用 res.close()。
try (Resource res = ...) {
    work with res
}
```

<br/>
<br/>

### 分析栈轨迹元素

栈轨迹（stack trace）是程序执行过程中某个特定点上所有挂起的方法调用的一个列表。当 Java 程序因为一个未捕获的异常而终止时，就会显示栈轨迹。

<br/>
<br/>

## 使用异常的技巧

使用异常的一些技巧：

- 异常处理不能代替简单的测试
- 不要过分地细化异常
- 合理利用异常层次结构
- 不要压制异常
- 在检测错误时，苛刻要比放任更好
- 不要羞于传递异常
- 使用标准方法报告 null 指针和越界异常
- 不要想最终用户显示栈轨迹

<br/>
<br/>

## 使用断言

在一个具有自我保护能力的程序中，断言很常用。

<br/>
<br/>

### 断言的概念

断言（assertion）机制允许你在测试期间在代码中插入一些检查，而在生产环境代码中自动删除这些检查。

```java
// assert condition;
assert x >= 0;

// 表达式部分的唯一目的是生产一个消息字符串
// assert condition : expression;
assert x >= 0 : x;
```

如果结果为假，则抛出一个 AssertionError 异常。

<br/>
<br/>

### 启用和禁用断言

默认情况下，断言是禁用的。可在运行程序时用 `-enableassertions` 或 `-ea` 选项启用断言。

需要注意的是，不必重新编译程序来启用或禁用断言。启用或禁用断言是类加载器的功能。禁用断言时，类加载器会去除断言代码，因此，不会降低程序运行的速度。

```sh
java -enablessertions MyAPP

# 在特定类或包中启用断言
java -ea:MyClass -ea:cot.myconipany*mylib MyAPP

# 使用 -disableassertions 或 -da 在特定的类和包中禁用断言
java -ea:... -da:MyClass MyAPP
```

<br/>

如果担心断言会占据类文件的空间，可以有选择的包含断言。如下：

```java
if (asserts) assert x >= 0;
```

<br/>
<br/>

### 使用断言完成参数检查

在 Java 语言中，提供了 3 中处理系统错误的机制：

- 抛出一个异常
- 记录日志
- 使用断言

什么时候应该选择使用断言呢？

- 断言失败是致命的、不可恢复的错误。
- 断言检查只在开发和测试阶段打开，用于确定程序内部错误的位置。

<br/>
<br/>

### 使用断言提供假设文档

通常，很多程序员使用注释来提供底层假设的文档。

<br/>
<br/>

## 日志

将应用的一些输出信息写入日志。

<br/>

> 注释：很多应用会使用其他日志框架，如 Log4J 2 和 Logback，它们能提供比标准 Java 日志框架更高的性能。

<br/>
<br/>

### 基本日志

对于简单的日志记录，可以使用全局日志记录（global logger）。

```java
Logger.getGlobal().info("Log message example");

// 如在 main 抑制所有日志
Logger.getGlocal().setLevel(Level.OFF);
```

<br/>
<br/>

### 高级日志

在一个专业的应用程序中，你肯定不想将所有的日志都记录到一个全局日志记录器中。

```java
// 未被任何变量引用的日志记录器可能会被垃圾回收，为了防止这种情况，要用静态变量存储日志记录器的一个引用。
private static final Logger myLogger = Logger.getLogger("com.mycompany.myapp");
```

日志记录器也有层次。例如，如果对日志记录器 "com.mycompany" 设置了日志级别，它的日志记录器也会继承这个级别。通常，有以下 7 个日志级别：

- SEVERE
- WARNING
- INFO
- CONFIG
- FINE
- FINER
- FINEST

在默认情况下，实际上只记录前 3 个级别（INFO 及其上）。可以设置不同的级别。

```java
logger.setLevel(Level.FINE);

// Level.ALL 或 Level.OFF
// logger.log(Level.FINE, message);
logger.fine(message);
```

<br/>

默认的日志记录会显示包含日志调用的类和方法的名字（根据调用栈得出）。不过，如果虚拟机对执行过程进行了优化，就可能得不到准确的调用信息。

```java
// 可使用 logp 方法获得调用类和方法的确切位置。
void logp(Level l, String className, String methodName, String message)
```

<br/>
<br/>

### 修改日志管理器配置

可通过编辑配置文件来修改日志系统的各个属性。默认的配置文件位于 `jdk/conf/logging.properties` （Java 9 之前是 `jre/lib/logging.properties`）。

<br/>
<br/>

### 本地化

你可能希望将日志消息本地化，以便全球用户都可以阅读。

<br/>
<br/>

### 日志处理器

默认情况下，日志记录器将记录发送到 ConsoleHandler，它会将记录输出到 System.err 流。

与日志记录器一样，处理器也有日志级别。对于一个要记录的日志消息，它的日志级别必须高于记录器和处理器二者的阈值。

要想记录 FINE 级别的日志，你就必须修改配置文件中的日志级别和处理器级别。

```properties
// 日志管理起配置文件中的默认日志级别
java.util.logging.ConsoleHandler.level = INFO
```

<br/>

默认情况下，日志记录器将记录发送到自己的处理器和父日志记录器的处理器。这个祖先会把记录发送到控制台。不过，我们并不像两次看到这些记录，因此应该将 useParenthandlers 属性设置为 false。

要将日志发送到其他地方，就要添加其他的处理器。日志 API 为此提供了两个很有用的处理器：

- FileHandler：将记录收集到一个文件中。
- SocketHandler：将记录发送到指定的主机和端口。

```java
var handler = new FileHandler();
logger.addHandler(handler);
```

这些记录被发送到用户主目录的 `javan.log` 文件中，n 是文件的唯一编号。默认情况下，记录会格式化为 XML。

<br/>

你可以自定义日志文件名。

| 变量 | 描述 |
| - | - |
| %h | 系统用户目录 |
| %t | 系统临时目录 |
| %u | 用于解决冲突的唯一标号 |
| %g | 循环日志的生成编号 |
| %% | % 字符 |

文件循环功能，日志文件以循环序列的形式保存（如 app.log.0, app.log.1 等）。只要文件超出了大小限制，最老的文件就会被删除，其他的文件将重新命名。

<br/>

可以通过扩展 Handler 类或 StreamHandler 类自定义处理器。

<br/>
<br/>

### 过滤器

在默认情况下，会根据日志记录的级别进行过滤。

每个日志记录器和处理器都可以有一个可选的过滤器来完成额外的过滤。

```java
// 实现 Filter 接口定义过滤器
boolean isLoggable(LogRecord record)
```

要将一个过滤器安装到一个日志记录器或处理器中，只需要调用 `setFileter` 方法。注意，一次最多只能有一个过滤器。

<br/>
<br/>

### 格式化器

ConsoleHandler 类和 FileHandler 类可以生成文本和 XML 格式的日志记录。

你可以自定义格式。这需要扩展 Formatter 类并覆盖 format 方法。你可以用你喜欢的任何方式对记录中的信息进行格式化，并返回结果字符串。

<br/>
<br/>

### 日志技巧

日志的一些最常用的操作：

- 对一个简单的应用，现则一个日志记录器。
- 默认的日志配置只会记录 INFO 及其之上的日志消息到控制台。
- 默认的级别要记录对程序用户有意义的消息。对于程序员想要的日志消息，可以使用 FINE 级别。

```java
// 如想要调用 println 时，可换成 logger.fine
logger.fine("File open dialog canceled");

// 记录那些意料之外的异常也是一个不错的想法
try {
    ...
}
catch (SomeException e) {
    logger.log(Level.FINE, "explanation", e);
}
```

<br/>
<br/>

## 调试技巧

1, 可以用下面的代码打印或记录任意变量的值。

```java
System.out.println("x=" + x);
Logger.getGlobal().info("x=", + x);
```

<br/>

2, 可以在每一个类放置一个单独的 `main` 方法。这样就可以提供一个单元测试桩（stub），允许你独立地测试类。

```java
public class MyClass {
    methods and fields
    ...
    public static void main(String[] args) {
        test code
    {
}
```

<br/>

3, JUnit 是一个非常流行的单元测试框架，利用它可以很容易地组织测试用例套件。

<br/>

4, 日志代理是一个子类的对象，它可以截获方法调用，将这些调用记入日志，然后调用超类中的方法。

<br/>

5, 利用 Throwable 类的 `printStackTrace` 方法，可以从任意的异常对象获得栈轨迹。

```java
try {
    ...
}
catch (Throwable t) {
    t.printStackTrace();
    throw t;
}

// 获得栈轨迹
Thread.dumpStack();

// 一般来说，栈轨迹显示在 `System.err` 上，可以将它捕获到字符串中。
var out = new StringWriter();
new Throwable().printStackTrace(new PrintWriter(out));
String description = out.toString();
```

<br/>

6, 通常，将程序错误记入一个文件会很有用。错误是发送到 `System.err`，请注意。

```sh
java MyProgram 1> errors.txt 2>&1
```

<br/>

7, 在 `System.err` 中显示未捕获的异常的栈轨迹并不是一个理想的方法，这些消息很乱，会让人慌乱。更好的方法是将这些消息记录到一个文件中。

<br/>

8, 想要观察类的加载过程，启动 Java 虚拟机时使用 `-verbose` 标志。

<br/>

9, `-Xlint` 选项告诉编译器找出常见的代码问题。

<br/>

10, Java 虚拟机提供了对 Java 应用的监控和管理支持，允许在虚拟机中安装代理来跟踪内存消耗、线程使用、类加载等情况。

这个特性对于规模很大且长时间运行的 Java 程序尤其重要。

<br/>

11, Java 任务控制器（JMC）是一个专业级性能分析和诊断工具。

<br/>

---

<br/>

# 泛型程序设计

泛型类和泛型方法有类型参数，这使得它们可以准确地描述用特定类型实例化时会发生什么。在泛型类之前，必须使用 Object 编写适用于多种类型的代码。这很繁琐，也不安全。

随着泛型的引入，Java 有了一个表述能力很强的类型系统，允许设计者详细地描述变量和方法的类型要如何变化。

<br/>
<br/>

## 为什么要使用泛型

泛型程序设计（generic programming）意味着编写的代码可以对多种不同类型的对象重用。例如，你并不希望为收集 String 和 File 对象分别编写不同的类。

<br/>
<br/>

### 类型参数的好处

在 Java 中增加泛型类之前，泛型程序设计是用继承实现的。

这种方法有两个问题：获取一个值时必须进行强制类型转换；没有错误检查。

泛型提供了一个更好的解决方案：类型参数（type parameter）。它会让你的程序更易读，更安全。

```java
// ArrayList 类现在由一个类型参数用来指示元素的类型
// 这使得代码具有更好的可读性。
var files = new ArrayList<String>();
```

<br/>
<br/>

### 谁想成为泛型程序员

作为一个泛型程序员，你的任务就是要预计到你的泛型类将来所有可能地用法。

Java 语言的设计者发明了通配符类型（wildcard type），构建类库的程序员可以编写出尽可能灵活的方法。

<br/>
<br/>

## 定义简单泛型类

泛型类（generic class）就是有一个或多个类型变量的类。换句话说，泛型类相当于普通类的工厂。

一个简单的 Pair 泛型类

```java
// Pair 类引入了一个类型变量 T。泛型可以有多个类型变量。
// 可以用具体的类型变量来实例化泛型类型，如 Pair<String>
public class Pair<T> {
    private T first;
    private T second;

    public Pair() { first = null; second = null; }
    public Pair(T first, T second) { this.first = first; this.second = second; }

    public T getFirst() { return first; }
    public T getSecond() { return second; }

    public void setFirst(T newValue) { first = newValue; }
    public void setSecond(T newValue) { second = newValue; }
}
```

<br/>
<br/>

## 泛型方法

可以在普通类/泛型类中定义泛型方法。

```java
class ArrayAlg {
    public static <T> T getMiddle(T... a) {
        return a[a.length / 2];
    }
}

// 调用泛型方法
String middle = ArrayAlg.<String>getMiddle("John", "Q.", "Public");
// 在大多数情况下，可以省略 <String> 类型参数
String middle = ArrayAlg.getMiddle("John", "Q.", "Public");
```

<br/>
<br/>

## 类型变量的限定

有时，类或方法需要对类型变量加以约束。

```java
// 比如要计算数组中最小元素
class ArrayAlg {
    public static <T> T min(T[] a) {
        if (a == null || a.length == 0) return null;
        T smallest = a[0];
        for (int i = 1; i < a.length; i++} {
            if (smallest.compareTo(a[i]) > 0) smallest = a[i];
        }
        return smallest;
    }
}
```

解决的方法是限制 T 只能是实现了 Comparable 接口的一个类。可以通过对类型变量 T 设置一个限定（bound）来实现。

```java
public static <T extends Comparable> T min(T[] a) ...

// 一个类型变量或通配符可以有多个限定。
// T extends Comparable & Serializable
```

为什么使用关键字 extends 而不是 implements？

记法 `<T extends BoundingType>`，表示 T 应该是限定类型（bound type）的子类型（sub）。T 和限定类型可以是类，也可以是接口。选择 extends 的原因是它更接近于子类型的概念，并且 Java 的设计者也不打算再新增关键字。

按照 Java 继承机制，可以根据需要拥有多个接口超类型，但最多有一个限定可以是类。如果有一个类作为限定，它必须是列表中的第一个限定。

<br/>
<br/>

## 泛型代码和虚拟机

虚拟机没有泛型类型对象——所有对象都属于普通类。在泛型实现的早期版本中，甚至能够将使用泛型的程序编译为在 1.0 虚拟机上运行的类文件。

下面的小节中，你会看到编译器如何擦除类型参数，这个过程对 Java 程序员有什么影响。

<br/>
<br/>

### 类型擦除

无论何时定义一个泛型类型，都会自动提供一个相应的原始类型（raw type）。这个原始类型的名字就是去掉类型参数后的泛型类型名。类型变量会被擦除（erased），并替换为其限定类型（对于无限定的变量则替换为 Object）。

<br/>
<br/>

### 转换泛型表达式





























<br/>
<br/>

## 限制与局限性

使用 Java 泛型时需要考虑的一些限制。

<br/>
<br/>

### 不能用基本类型实例化类型参数

不能用基本类型代替类型参数。因此，没有 `Pair<double>`，只有 `Pair<Double>`。

<br/>
<br/>

### 运行时类型查询只适用于原始类型

虚拟机中的对象总是有一个特定的非泛型类型。因此，所有的类型查询只生成原始类型。

<br/>
<br/>

### 不能创建参数化类型的数组

不能实例化参数化类型的数组。

<br/>
<br/>

### Varargs警告





























<br/>

---

<br/>

# 集合

关注性能时，选择不同的数据结构会带来很大差异。

本章介绍如何利用 Java 类库帮助我们实现程序涉及所需的传统数据结构。详细的有一门数据结构课程，这里仅简单介绍如何使用标准库中的集合类。

<br/>
<br/>

## Java集合框架

Java 最初的版本只为最常用的数据结构提供了很少的一组类：Vector、Stack、Hashtable、BitSet 和 Enumeration 接口。其中 Enumeration 接口提供了一种抽象机制，用于访问任意容器中的元素。

这一节将介绍 Java 集合框架的基本设计，展示如何具体使用，并解释一些颇具争议的特性背后的考虑。

<br/>
<br/>

### 集合接口与实现分离

与现代的数据结构类库的常见做法一样，Java 集合类库也将接口（interface）与实现（implementation）分离。

下面利用我们熟悉的队列（queue）来说明接口与实现如何分离。

队列接口指出可以在队尾添加元素，在队头删除元素，并且可以查找队列中元素的个数。当需要收集对象并按照先进先出方式获取对象时，就应该使用队列。





















<br/>

---

<br/>

# 图形界面程序设计

<br/>

---

<br/>

# Swing界面组件

<br/>

---

<br/>

# 并发

多任务（multitasking）是操作系统的一种能力，看起来可以在同一时刻运行多个程序。操作系统回味每个进程分配时间片，给人并行处理的感觉。

多线程程序在更低一层扩展了多任务的概念：单个程序看起来好像在同时完成多个任务。每个任务在一个线程（thread）中执行，线程是控制线程的简称。如果一个程序可以同时运行多个线程，这个程序就是多线程程序。

多进程与多线程有什么区别？本质的区别在于每个进程都拥有自己的一整套变量，线程则共享数据。这听起来似乎有些风险，的确如此。不过，共享变量使线程之间的通信比进程之间的通信更高效、更容易。此外，在操作系统中，与进程比较，线程更轻量，创建和撤销单个线程比启动新进程的开销要小得多。

在实际应用中，多线程非常有用。如一个服务器能够同时服务并发的请求。

<br/>
<br/>

## 什么是线程

一个使用了两个线程的简单程序。

<br/>
<br/>

## 线程状态

线程可以有以下 6 种状态，调用 `getState()` 方法确定线程的当前状态。

- New
- Runnable
- Blocked
- Waiting
- Timed waiting
- Terminated

<br/>

![线程状态](https://raw.githubusercontent.com/zhang21/images/master/cs/java/12-1.png)

<br/>
<br/>

### 新建线程

新建线程（如 `new Thread(r)`）时，程序开没有开始运行线程种的代码。线程可运行前还有一些基础工作要做。

<br/>
<br/>

### 可运行线程

一个可运行的线程可能正在运行也可能没运行。要由操作系统为线程提供具体的运行时间。

一旦一个线程开始运行，它不一定始终保持运行。事实上，运行中的线程有时需要暂停，让其他线程有机会运行。具体细节由操作系统提供。抢占式调度系统给每一个可运行线程一个时间片来执行任务。当时间片用完时，操作系统就会剥夺该线程的运行全，并给另一个线程一个机会来运行。当选择下一个线程时，操作系统会考虑线程的优先级。

所有现代桌面和服务器操作系统都使用抢占式调度。在有多个处理器的机器上，每个处理器可以运行一个线程，因此可以有有多个线程并行运行。

<br/>
<br/>

### 阻塞和等待线程

当线程出于阻塞或登台状态时，它暂时是不活动的。它不执行任何代码，并且消耗最少的资源。要由线程调度器重新激活这个线程。

<br/>
<br/>

### 终止线程

线程终止的两个原因：

- 由于 run 方法正常退出，线程自然终止。
- 因为一个没有捕获的异常终止了 run 方法，是线程意外终止。

<br/>
<br/>

## 线程属性

线程的各种属性，包括中断的状态、守护进程、未捕获异常的处理器以及不应使用的一些遗留特性。

<br/>
<br/>

### 中断线程

除了已经废弃的 stop 方法，没有办法强制一个线程终止。不过，interrupt 方法可以用来请求终止一个线程。

当对线程调用 interrupt 方法时，就会设置线程的中断状态（interrupted status）。这是每个线程都有的一个布尔标志。各个线程应该不时地检查这个标志，以判断线程是否被中断。

<br/>
<br/>

### 守护线程

守护线程的唯一用途是为其他线程提供服务。计时器线程就是一个例子，它定时地向其他线程发送计时器嘀嗒信号。

只剩下守护线程时，虚拟机就会退出。因为只剩下守护线程，就没必要继续运行程序了。

```java
// 将一个线程转换为守护线程
t.setDaemon(true);
```

<br/>
<br/>

### 线程名

```java
// 为线程设置名字
var t = new Threa(runnable);
t.setName("Thread01");
```

<br/>
<br/>

### 未捕获异常的处理器

线程的 run 方法不能抛出任何检查型异常，但是，非检查型异常可能会导致线程终止。在此情况下，线程会死亡。

实际上，在线程死亡之前，异常会传递到一个用于处理未捕获异常的处理器。这个处理器必须属于一个实现了 `Thread.UncaughtExceptionHandler` 接口的类。

<br/>
<br/>

### 线程优先级

在 Java 程序设计语言中，每一个线程有一个优先级。默认情况下，一个线程会继承构造它的那个线程的优先级。

可用 `setPriority` 方法提高或降低线程的优先级。可将优先级设置为 `MIN_PRIORITY`（在 Thread 类中定义为 1） 与 `MAX_PRIORITY`（定义为 10）之间。`NORM_PRIORITY` 定义为 5。

线程调度器优先选择优先级高的线程。但是，线程优先级高度依赖于系统。当 Java 虚拟机依赖于主机平台的线程实现时，Java 线程的优先级会映射到主机平台的优先级。

在没有使用操作系统的 Java 早期版本中，线程优先级可能很有用。不过现在不要使用线程优先级了。

<br/>
<br/>

## 同步

在大多数多线程应用中，多个线程可能需要共享存取相同的数据。如果两个线程同时操作同一个对象，这会导致对象被破坏。这种情况通常称为静态条件（race condition）。

为了避免多线程破坏共享数据，必须学习如何同步存取（synchronize the access）。

<br/>
<br/>

### 竟态条件

比如，当两个线程试图同时更新同一个银行账户时，就会出现此问题。

问题在于这不是原子操作。

<br/>
<br/>

### 锁对象

有两种机制可防止并发访问一个代码块。

- 一个 `synchronized` 关键字。
- 一个 Java 5 引入的 `ReentrantLock` 类。

<br/>

要把 unlock 操作包在 finally 子句中，这一点至关重要。如果临界区中的代码抛出一个异常，必须释放锁。否则，其他线程将永远阻塞。

使用锁时，就不能使用 try-with-resources 语句。

要注意不能由于抛出异常而绕过临界区中的代码。如果在临界区代码结束前抛出了异常，finally 子句将释放锁，但是对象可能出于被破坏的状态。

<br/>

```java
// 用 ReentrantLock 保护代码块
myLock.lock();
try {
    critical section
}
finally {
    myLock.unlock();
}
```

<br/>
<br/>

### 条件对象

通常，线程进入临界区后却发现只有满足了某个条件之后它才能执行。可以使用一个条件对象来管理那些已经获得了一个锁却不能有效工作的线程。由于历史原因，条件对象（condition object）经常被称为条件变量（condition variable）。

只有当 线程 拥有一个条件的锁时，它才能在这个条件上调用 await（将线程放在这个条件的等待集中）, signalAll（接触该条件等待集中所有线程的阻塞状态） 或 signal（从该条件的等待集中随机选择一个线程，解除其阻塞状态）方法。

<br/>
<br/>

### synchronized关键字

前面介绍了 Lock 和 Condition 对象。再进一步深入之前，需要对锁和条件的要点做一个总结：

- 锁用来保护代码段，一次只允许一个线程执行被保护的代码。
- 锁可以管理试图进入被保护代码段的线程。
- 一个锁可以有一个或多个关联的条件对象。
- 每个条件对象管理那些已经进入被保护代码段但还不能运行的线程。

<br/>

Lock 和 Condition 接口允许程序员充分控制锁定。不过，大多数情况下你并不需要那样控制，可以使用 Java 语言内置的一种机制。Java 中每个对象都有一个内部锁（intrinsic lock）。如果一个方法声明使用 synchronized 关键字，那么对象的锁将保护整个方法。也就是说，要调用这个方法，编程必须获得内部对象锁。

使用 synchronized 可以得到更简洁的代码。

```java
public synchronized void method() {
    ...
}

// 等价于
public void method() {
    this.intrinsicLock.lock();
    try {
        ...
    }
    finally {
        this.instrinsicLock.unlock();
    }
}
```

<br/>

> 注释：wait, notifyAll 和 notify 方法是 Object 类的 final 方法。Condition 方法必须命名为 await, signalAll 和 signal，从而不会与那些方法发生冲突。

<br/>

将静态方法声明为同步也是合法的。如果调用这样一个方法，它会获得关联类对象的内部锁。

内部锁和条件也存在一些限制。包括：

- 不能中断一个正在尝试获得锁的线程。
- 不能指定尝试获得锁的超时时间。
- 每个锁只能有一个条件，这很低效。

应该使用 锁对象和条件对象，还是同步方法呢？下面是一些建议：

- 最好既不使用 Lock/Condition 也不使用 synchronized 关键字。在许多情况下，可以使用 `java.util.concurrent` 包中的某种机制，它会为你处理所有的锁定。
- 如果 synchronized 使用你的程序，那么尽量使用它。这样可以减少代码量，减少出错。
- 如果特别需要 Lock/Condition  结构提供的额外能力，则使用它们。

<br/>
<br/>

### 同步块

线程可以通过调用同步方法获得对象的锁。还有一种机制可以获得这个锁：进入一个同步块（synchronized lock）。













<br/>

---

<br/>

# 单元测试

介绍 Java 平台最常用的测试框架 JUnit（目前是 JUnit 5），以及如何编写单元测试。

<br/>
<br/>

## JUnit概述

JUnit 5 要求在运行时使用 Java 8（或更高版本）。

`JUnit5 = JUnit Platform + JUnit Jupiter + JUnit Vintage`，三个子项目：

- JUnit Platform：是在 JVM 上启动测试框架的基础。
- JUnit Jupiter：是编程模型和扩展模型的组合。
- JUnit Vintage：为运行基于 JUnit 3 和 Junit 4 的测试提供测试引擎。

<br/>
<br/>

## JUnit安装

依赖元数据：

```yml
groupId: org.junit.platform
version: 1.10.2
artifactId: junit-platform-xxx

groupId: org.junit.jupiter
version: 5.10.2
artifactId: junit-jupiter-xxx

groupId: org.junit.vintage
version: 5.10.2
artifactId: junit-vintage-engine

# ...
```

<br/>
<br/>

## 编写测试

第一个简单的测试样例：

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import example.util.Calculator;
import org.junit.jupiter.api.Test;

class MyFirstJUnitJupiterTest {
    private final Calculator calculator = new Calculator();

    @Test
    void addition() {
        assrterEquals(2, calculator.add(1, 1));
    }
}
```

<br/>
<br/>

### 支持的注解

JUnit Jupiter 支持以下注解，用于配置测试和扩展框架。所有核心注解都位于 `junit-jupiter-api` 模块的 `org.junit.jupiter.api` 包中。

| 注解 | 描述 |
| - | - |
| `@Test` | 表示一个方法是测试方法 |
| `@ParameterizedTest` | 表示方法是参数化测试 |
| `@RepeatedTest` | 表示方法是用于重复测试的测试模板 |
| `@TestFactory` | 表示方法是用于动态测试的测试工厂 |
| `@TestTemplate` | 表示方法是测试用例的模板，设计为被调用多次，调用次数取决于自注册的提供者返回的调用上下文。|
| `@TestClassOrder` | 用于配置注解测试类中 `@Nested` 测试类的测试类执行顺序 |
| `@TestMethodOrder` | 用于配置注解测试类的测试方法执行顺序 |
| `@TestInstance` | 用于配置注解测试类的测试实例生命周期 |
| `@DisplayName` | 声明测试类或测试方法的自定义显示名称。 |
| `@DisplayNameGeneration` | 声明测试类的自定义显示名称生成器。 |
| `@BeforeEach` | 表示被注解方法应该在当前类的每个方法之前执行。 <br> 方法：`@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory`|
| `@AfterEach` | 表示被注解的方法应在当前类的每个方法之后执行。|
| `@BeforeAll` | 表示被注解的方法应该在当前类的所有方法之前执行。|
| `@AfterAll` | 表示被注解的方法应该在当前类的所有方法之后执行。|
| `@Nested` | 表示注解类是一个非静态嵌套测试类。|
| `@Tag` | 在类或方法级别声明标记，用于过滤测试。|
| `@Disabled` | 用于禁用测试类或测试方法。|
| `@Timeout` | 设定超时时间。 |
| `@ExtendWith` | 用于注册自定义扩展。 |
| `@RegisterExtension` | - |
| `@TempDir` | 提供临时目录。 |

<br/>
<br/>

#### 元注解和组合注解

JUnit Jupiter注解可以用作元注解。这意味着您可以定义自己的组合注释，它将自动继承其元注释的语义。

<br/>
<br/>

### 定义测试

测试方法和生命周期可在当前测试类中本地声明、从超类继承或从结构继承。此外，测试方法和声明周期不是抽象的，也不得返回值（`@TestFactory` 除外，它必须返回值）。

测试类、方法和生命周期不需要是 `public`，但不能是 `priate`。

一般建议测试类、方法和生命周期省略 `public` 修饰符，除非有技术上的原因（测试类由另一个包中的测试类扩展）。另一个原因是在使用 java 模块系统时简化模块路径上的测试。

一个标准的测试类：

```java
import static org.junit.jupiter.api.Assertions.fail;
import static org.junit.jupiter.api.Assumptions.assumeTrue;

import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class StandardTests {

    @BeforeAll
    static void initAll() {
    }

    @BeforeEach
    void init() {
    }

    @Test
    void succeedingTest() {
    }

    @Test
    void failingTest() {
        fail("a failing test");
    }

    @Test
    @Disabled("for demonstration purposes")
    void skippedTest() {
        // not executed
    }

    @Test
    void abortedTest() {
        assumeTrue("abc".contains("Z"));
        fail("test should have been aborted");
    }

    @AfterEach
    void tearDown() {
    }

    @AfterAll
    static void tearDownAll() {
    }

}
```

<br/>
<br/>

### 测试的显示名称

测试类和方法可以声明自定义显示名称。

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

@DisplayName("A special test case")
class DisplayNameDemo {

    @Test
    @DisplayName("Custom test name containing spaces")
    void testWithDisplayNameContainingSpaces() {
    }

    @Test
    @DisplayName("╯°□°）╯")
    void testWithDisplayNameContainingSpecialCharacters() {
    }

    @Test
    @DisplayName("😱")
    void testWithDisplayNameContainingEmoji() {
    }

}
```

<br/>
<br/>

### 测试的断言

JUnit Jupiter 提供了许多断言方法（assertions）。所有断言都是 `org.junit.jupiter.api.Assertions` 类中的静态方法。

```java
import static java.time.Duration.ofMillis;
import static java.time.Duration.ofMinutes;
import static org.junit.jupiter.api.Assertions.assertAll;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.junit.jupiter.api.Assertions.assertTimeout;
import static org.junit.jupiter.api.Assertions.assertTimeoutPreemptively;
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.util.concurrent.CountDownLatch;

import example.domain.Person;
import example.util.Calculator;

import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

class AssertionsDemo {

    private final Calculator calculator = new Calculator();

    private final Person person = new Person("Jane", "Doe");

    @Test
    void standardAssertions() {
        assertEquals(2, calculator.add(1, 1));
        assertEquals(4, calculator.multiply(2, 2),
                "The optional failure message is now the last parameter");
        assertTrue('a' < 'b', () -> "Assertion messages can be lazily evaluated -- "
                + "to avoid constructing complex messages unnecessarily.");
    }

    @Test
    void groupedAssertions() {
        // In a grouped assertion all assertions are executed, and all
        // failures will be reported together.
        assertAll("person",
            () -> assertEquals("Jane", person.getFirstName()),
            () -> assertEquals("Doe", person.getLastName())
        );
    }

    @Test
    void dependentAssertions() {
        // Within a code block, if an assertion fails the
        // subsequent code in the same block will be skipped.
        assertAll("properties",
            () -> {
                String firstName = person.getFirstName();
                assertNotNull(firstName);

                // Executed only if the previous assertion is valid.
                assertAll("first name",
                    () -> assertTrue(firstName.startsWith("J")),
                    () -> assertTrue(firstName.endsWith("e"))
                );
            },
            () -> {
                // Grouped assertion, so processed independently
                // of results of first name assertions.
                String lastName = person.getLastName();
                assertNotNull(lastName);

                // Executed only if the previous assertion is valid.
                assertAll("last name",
                    () -> assertTrue(lastName.startsWith("D")),
                    () -> assertTrue(lastName.endsWith("e"))
                );
            }
        );
    }

    @Test
    void exceptionTesting() {
        Exception exception = assertThrows(ArithmeticException.class, () ->
            calculator.divide(1, 0));
        assertEquals("/ by zero", exception.getMessage());
    }

    @Test
    void timeoutNotExceeded() {
        // The following assertion succeeds.
        assertTimeout(ofMinutes(2), () -> {
            // Perform task that takes less than 2 minutes.
        });
    }

    @Test
    void timeoutNotExceededWithResult() {
        // The following assertion succeeds, and returns the supplied object.
        String actualResult = assertTimeout(ofMinutes(2), () -> {
            return "a result";
        });
        assertEquals("a result", actualResult);
    }

    @Test
    void timeoutNotExceededWithMethod() {
        // The following assertion invokes a method reference and returns an object.
        String actualGreeting = assertTimeout(ofMinutes(2), AssertionsDemo::greeting);
        assertEquals("Hello, World!", actualGreeting);
    }

    @Test
    void timeoutExceeded() {
        // The following assertion fails with an error message similar to:
        // execution exceeded timeout of 10 ms by 91 ms
        assertTimeout(ofMillis(10), () -> {
            // Simulate task that takes more than 10 ms.
            Thread.sleep(100);
        });
    }

    @Test
    void timeoutExceededWithPreemptiveTermination() {
        // The following assertion fails with an error message similar to:
        // execution timed out after 10 ms
        assertTimeoutPreemptively(ofMillis(10), () -> {
            // Simulate task that takes more than 10 ms.
            new CountDownLatch(1).await();
        });
    }

    private static String greeting() {
        return "Hello, World!";
    }

}
```

<br/>
<br/>

### 测试的假设

JUnit Jupiter 提供了许多假设方法（assumptions），所有假设都是 `org.junit.jupiter.api.Assumptions` 类中的静态方法。

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assumptions.assumeTrue;
import static org.junit.jupiter.api.Assumptions.assumingThat;

import example.util.Calculator;

import org.junit.jupiter.api.Test;

class AssumptionsDemo {

    private final Calculator calculator = new Calculator();

    @Test
    void testOnlyOnCiServer() {
        assumeTrue("CI".equals(System.getenv("ENV")));
        // remainder of test
    }

    @Test
    void testOnlyOnDeveloperWorkstation() {
        assumeTrue("DEV".equals(System.getenv("ENV")),
            () -> "Aborting test: not on developer workstation");
        // remainder of test
    }

    @Test
    void testInAllEnvironments() {
        assumingThat("CI".equals(System.getenv("ENV")),
            () -> {
                // perform these assertions only on the CI server
                assertEquals(2, calculator.divide(4, 2));
            });

        // perform these assertions in all environments
        assertEquals(42, calculator.multiply(6, 7));
    }

}
```

<br/>
<br/>

### 禁用测试

测试类或方法可禁用。

```java
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

@Disabled("Disabled until bug #99 has been fixed")
class DisabledClassDemo {

    @Test
    void testWillBeSkipped() {
    }

}
```

<br/>

```java
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class DisabledTestsDemo {

    @Disabled("Disabled until bug #42 has been resolved")
    @Test
    void testWillBeSkipped() {
    }

    @Test
    void testWillBeExecuted() {
    }

}
```

<br/>
<br/>

### 条件测试执行

<br/>
<br/>

### 标记和过滤

测试类和方法可以打标记，这些标记可用于过滤测试发现和执行。

```java
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

@Tag("fast")
@Tag("model")
class TaggingDemo {

    @Test
    @Tag("taxes")
    void testingTaxCalculation() {
    }

}
```

<br/>
<br/>

### 测试执行顺序

默认情况下，测试类和方法将使用一种确定性算法排序，但故意不明显。这可确保测试套件的后续运行以相同的顺序执行测试类和方法，从而实现可重复的构建。

<br/>
<br/>

### 测试实例生命周期

为了允许隔离执行单个的测试方法，并避免由于可变测试实例状态而产生的意外副作用，JUnit 在执行每个测试方法之前创建每个测试类的新实例。

如果你希望 Jupiter 在同一个测试实例上执行所有测试方法，只需使用 `@TestInstance` 对你的测试类进行注解。当使用这种模式时，每个测试将创建一个新的测试实例。因此，如果你的测试方法依赖于存储在实例变量中的状态，则可能需要在 `@BeforeEach` 或 `@AfterEach` 方法中重置该状态。

<br/>

可以修改默认测试实例生命周期，要更改它，只需将 `junit.jupiter.testinstance.lifecycle.default` 配置参数设置为 `TestInstance.Lifecycle` 中定义的枚举常量的名称，忽略大小写。

更改默认的测试实例生命周期模式可能会导致不可预测的结果和脆弱的构建，如果应用的不一致。因此，建议更改 JUnit Platform 配置文件中的默认值，而不是通过 JVM 系统属性。

<br/>
<br/>

### 嵌套测试

嵌套测试给测试编写者更多的能力，来表达几组测试之间的关系。

用于测试 stack 的嵌套测试套件：

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.util.EmptyStackException;
import java.util.Stack;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

@DisplayName("A stack")
class TestingAStackDemo {

    Stack<Object> stack;

    @Test
    @DisplayName("is instantiated with new Stack()")
    void isInstantiatedWithNew() {
        new Stack<>();
    }

    @Nested
    @DisplayName("when new")
    class WhenNew {

        @BeforeEach
        void createNewStack() {
            stack = new Stack<>();
        }

        @Test
        @DisplayName("is empty")
        void isEmpty() {
            assertTrue(stack.isEmpty());
        }

        @Test
        @DisplayName("throws EmptyStackException when popped")
        void throwsExceptionWhenPopped() {
            assertThrows(EmptyStackException.class, stack::pop);
        }

        @Test
        @DisplayName("throws EmptyStackException when peeked")
        void throwsExceptionWhenPeeked() {
            assertThrows(EmptyStackException.class, stack::peek);
        }

        @Nested
        @DisplayName("after pushing an element")
        class AfterPushing {

            String anElement = "an element";

            @BeforeEach
            void pushAnElement() {
                stack.push(anElement);
            }

            @Test
            @DisplayName("it is no longer empty")
            void isNotEmpty() {
                assertFalse(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when popped and is empty")
            void returnElementWhenPopped() {
                assertEquals(anElement, stack.pop());
                assertTrue(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when peeked but remains not empty")
            void returnElementWhenPeeked() {
                assertEquals(anElement, stack.peek());
                assertFalse(stack.isEmpty());
            }
        }
    }
}
```

在本例中，通过为设置代码定义分层生命周期方法，外部测试的先决条件被用于内部测试。

外层测试的设置代码会在内部测试执行前运行，这让你能够独立运行所有测试。你甚至可以不运行外部测试，而单独运行内部测试，因为外部测试的设置代码始终会被执行。

<br/>
<br/>

### 构造函数和方法和依赖注入

新的 Jupiter 支持测试构造函数和方法有参数，这带来了更大的灵活性，并为构造函数和方法启用依赖注入。

`ParameterResolver` 为希望在运行时动态解析参数的测试扩展定义了 API。如果测试类构造函数、测试方法或生命周期方法接受一个参数，则该参数必须在运行时由已注册的 `ParameterResolver` 解析。

目前有三个自动注册的内置解析器：

- TestInfoParameterResolver
- RepetitionInfoParameterResolver
- TestReporterParameterResolver

<br/>
<br/>

### 测试接口和默认方法

Jupiter 允许在接口 `default` 方法上声明 `@Test`、`@RepeatedTest`、`@ParameterizedTest`、`@TestFactory`、`@TestTemplate`、`@BeforeEach` 和 `@AfterEach`。

如果测试接口或测试类用 `@TestInstance(Lifecycle.PER_CLASS)` 注解，则可以载测试接口中的 `static` 方法或接口 `default` 方法上声明 `@BeforeAll` 和 `@AfterAll`。

示例如下：

```java
@TestInstance(Lifecycle.PER_CLASS)
interface TestLifecycleLogger {

    static final Logger logger = Logger.getLogger(TestLifecycleLogger.class.getName());

    @BeforeAll
    default void beforeAllTests() {
        logger.info("Before all tests");
    }

    @AfterAll
    default void afterAllTests() {
        logger.info("After all tests");
    }

    @BeforeEach
    default void beforeEachTest(TestInfo testInfo) {
        logger.info(() -> String.format("About to execute [%s]",
            testInfo.getDisplayName()));
    }

    @AfterEach
    default void afterEachTest(TestInfo testInfo) {
        logger.info(() -> String.format("Finished executing [%s]",
            testInfo.getDisplayName()));
    }

}
```

```java
interface TestInterfaceDynamicTestsDemo {

    @TestFactory
    default Stream<DynamicTest> dynamicTestsForPalindromes() {
        return Stream.of("racecar", "radar", "mom", "dad")
            .map(text -> dynamicTest(text, () -> assertTrue(isPalindrome(text))));
    }

}
```

<br/>
<br/>

### 重复测试

Jupiter 通过使用 `@RepeatedTest` 注解并指定所需的重复次数，提供了重复测试指定次数的功能。每次重复测试的调用都像执行常规的 `@Test` 方法一样，完全支持相同的生命周期回调和扩展。

以下示例演示了如何声明名为 `repeatedTest()` 的测试，该测试将自动重复 10 次。

```java
@RepeatedTest(10)
void repeatedTest() {
    // ...
}
```

<br/>
<br/>

### 参数化测试

参数化测试可以使用不同的参数多次运行测试，它们的声明方式与普通的 `@Test` 方法相同，但使用 `@ParameterizedTest` 注解。此外，你必须声明一个源，为每次调用提供参数，然后在测试方法中消费参数。

```java
@ParameterizedTest
@ValueSource(strings = { "racecar", "radar", "able was I ere I saw elba" })
void palindromes(String candidate) {
    assertTrue(StringUtils.isPalindrome(candidate));
}
```

执行上述参数化测试方法时，每次调用都将分别报告。

<br/>

为了使用参数化测试，你需要添加对 `junit-jupiter-params` 构建的依赖。

<br/>

参数化测试方法通常直接从配置源中消费参数。不过，它也可以选择将来自源的参数聚合到传递给方法的单个对象中。具体来说，参数化测试方法必须按照以下规则声明形式参数：

- 必须先声明零个或多个索引参数。
- 接下来必须声明零个或多个聚合器。
- ParameterResolver 提供的零个或多个参数必须最后声明。

<br/>

Jupiter 开箱即提供了大量 source 注解，具体的注解详情参考官方文档。

<br/>
<br/>

### 测试模板

`TestTemplate` 方法不是常规的测试用例，而是测试用例的模板。因此，它可以根据注册提供程序返回的调用上下文数量被多次调用。因此，它必须与已注册的 `TestTemplateInvocationContextProvider` 扩展结合使用。每次调用测试模板方法的行为都与执行常规 `@Test` 方法一样，并完全支持相同的生命周期回调和扩展。

重复测试和参数化测试是测试模板的内置特性。

<br/>
<br/>

### 动态测试

标准的 `@Test` 注解描述了实现了测试用例的方法，这些测试用例是静态的。因为它们在编译时已被完全指定，其行为不会因运行时发生的任何事情而改变。`Assumptions` 提供了动态行为的一种基本形式，但其表达能力却有意受到限制。

Jupiter 引入了一种全新的测试编程模型，这种新测试是一种动态测试，它在运行时由注解为 `@TestFactory` 的工厂方法生成。

与 `@Test` 方法相比，`@TestFactory` 方法本身不是测试用例，而是测试用例的工厂。因此，动态测试是工厂的产物。

<br/>
<br/>

### 测试超时

`Timeout` 注解允许声明测试、测试工厂、测试模板和生命周期方法的超时时间，默认单位是秒，但可以配置。

```java
class TimeoutDemo {

    @BeforeEach
    @Timeout(5)
    void setUp() {
        // fails if execution time exceeds 5 seconds
    }

    @Test
    @Timeout(value = 500, unit = TimeUnit.MILLISECONDS)
    void failsIfExecutionTimeExceeds500Milliseconds() {
        // fails if execution time exceeds 500 milliseconds
    }

    @Test
    @Timeout(value = 500, unit = TimeUnit.MILLISECONDS, threadMode = ThreadMode.SEPARATE_THREAD)
    void failsIfExecutionTimeExceeds500MillisecondsInSeparateThread() {
        // fails if execution time exceeds 500 milliseconds, the test code is executed in a separate thread
    }

}
```

<br/>
<br/>

### 并行执行

默认情况下，Jupiter 测试在单线程中顺序运行。要启用并行测试，请将 `junit.jupiter.execution.parallel.enabled` 配置参数设置为 `true`。

请注意，启用此属性只是并行执行测试的第一步。如果启用，默认情况下测试类和方法仍将按顺序执行。测试树中的节点是否并行执行由其执行模式控制。有以下两种模式可选：

- `SAME_THREAD`，默认执行模式。强制在父进程使用的同一线程种执行。
- `CONCURRENT`，并发执行，除非资源锁强制在同一线程中执行。

```
junit.jupiter.execution.parallel.enabled = true
junit.jupiter.execution.parallel.mode.default = concurrent
```

<br/>
<br/>

## 运行测试

基本上使用 IDE 点击运行测试。

<br/>
<br/>

## AssertJ断言

AssertJ: <https://joel-costigliola.github.io/assertj/index.html>

<br/>

虽然 JUnit Jupiter 提供的断言功能足以满足许多测试场景的需要，但有时需要更强大和附加功能。JUnit 小组推荐使用 AssertJ, Hamcrest 等第三方断言库。

JUnit Assert 不是很好，因此推荐使用 AssertJ 断言。

AssertJ 是一个流畅的断言库，可以帮助开发者编写简洁、可读性强的断言代码。

<br/>
<br/>

### 配置和导入AssertJ

配置依赖项：

```xml
<dependency>
  <groupId>org.assertj</groupId>
  <artifactId>assertj-core</artifactId>
  <!-- use 2.9.1 for Java 7 projects -->
  <version>3.11.1</version>
  <scope>test</scope>
</dependency>
```

<br/>

导入它：

```java
import static org.assertj.core.api.Assertions.*;
```

<br/>
<br/>

### JUnit断言与AssertJ断言

```java
// JUnit assert
assertEquals(expected, actual);

// AssertJ
assertThat(actual).isEqualTo(expected);
```

<br/>
<br/>

### 使用AssertJ断言

对布尔、对象、数组、字符、类、文件和数字等进行断言。详细使用信息请参考官方文档。

<br/>

#### 布尔断言

```java
assertThat(xxx).isTrue();
assertThat(xxx).isFalse();
```

<br/>
<br/>

#### 字符串断言

```java
String str = null;  

// 断言null或为空字符串  
assertThat(str).isNullOrEmpty();  

// 断言空字符串  
assertThat("").isEmpty();  

// 断言字符串相等 断言忽略大小写判断字符串相等  
assertThat("Frodo")
    .isEqualTo("Frodo")
    .isEqualToIgnoringCase("frodo");  

// 断言开始字符串 结束字符穿 字符串长度  
assertThat("Frodo")
    .startsWith("Fro")
    .endsWith("do")
    .hasSize(5);  

// 断言包含字符串 不包含字符串  
assertThat("Frodo")
    .contains("rod")
    .doesNotContain("fro");  

// 断言字符串只出现过一次  
assertThat("Frodo").containsOnlyOnce("do");  

// 判断正则匹配  
assertThat("Frodo")
    .matches("..o.o")
    .doesNotMatch(".*d"); 
```

<br/>
<br/>

#### 数字断言

```java
Integer num = null;  

// 断言空  
assertThat(num).isNull();  

// 断言相等  
assertThat(42).isEqualTo(42);  

// 断言大于 大于等于  
assertThat(42)
    .isGreaterThan(38)
    .isGreaterThanOrEqualTo(38);  

// 断言小于 小于等于  
assertThat(42)
    .isLessThan(58)
    .isLessThanOrEqualTo(58);  

// 断言0  
assertThat(0).isZero();  

// 断言正数 非负数  
assertThat(1)
    .isPositive()
    .isNotNegative();  

// 断言负数 非正数  
assertThat(-1)
    .isNegative()
    .isNotPositive();  
```

<br/>
<br/>

#### 文件断言

```java
assertThat(someFile)
  .exists()
  .isFile()
  .canRead()
  .canWrite();
```

<br/>

---

<br/>

# 常用工具

<br/>
<br/>

## Maven

Maven 是一个项目管理和构建工具。

假设我们有一个 java 项目，我们通常有一些琐碎工作：

- 依赖项
- 目录结构
- 配置环境
- 编译、测试和部署等

所以我们需要一个标准化的 Java 项目管理和构建工具。

<br/>
<br/>

### Maven介绍

一个使用 Maven 管理的普通 Java 项目结构。

```
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   └── resources
│   └── test
│       ├── java
│       └── resources
└── target
```

<br/>

Maven 工程使用以下几个要素作为唯一标识。

- groupId：通常是公司或组织的名称
- artifactId：通常是项目名称
- version：项目的版本
- packaging：项目类型，默认是 jar。

<br/>

Maven 在版本管理时候的几个特殊字符串：

- SNAPSHOT：一般用于开发过程中，表示不稳定版本。
- LATEST：指某个特定构件的最新发布
- RELEASE：指最后一个发布版

<br/>
<br/>

### Maven依赖管理

Maven 会自动处理依赖的依赖。

Maven 维护了一个中央仓库，所有第三方库将自身的 jar 以及相关信息上传到中央仓库，Maven 就可以从中央仓库把所需依赖下载到本地。

Maven 并不会每次都从仓库下载 jar 包。一旦下载，就会自动缓存在本地目录。但 `-SNAPSHOT` 的开发版本每次都会重复下载。这种版本只能用于内部私有仓库，不允许出现在公开发布。

除了可以从中央仓库下载外，还可以从 Maven 的镜像仓库下载。比如修改配置中的镜像为阿里云。

<br/>

官方推荐的查找依赖的网站：

- <https://search.maven.org/>
- <https://repository.apache.org/>
- <https://mvnrepository.com/>

<br/>

Maven 定义了几种依赖关系。

| scope | 说明 | 示例 |
| - | - | - |
| compile | 编译时需要用到 | commons-logging |
| test | 编译 Test 时需要用到 | junit |
| runtime | 编译时不需要，运行时需要 | mysql |
| provided | 编译时需要，但运行时由 JDK 或某个服务器提供 | servlet-api |

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.48</version>
    <scope>runtime</scope>
</dependency>
```

<br/>
<br/>

### Maven构建流程

Maven 把项目的构建划分为不同的生命周期（lifecycle）。它的过程（phase）包括：编译、测试、打包、集成测试、验证、部署等。

所以我们使用 mvn 命令时，后面的参数是过程，Maven 自动根据声明周期运行到指定的过程。

执行一个过程又会触发一个或多个目标（goal）。

- lifecycle：相当于 package，包含一个或多个 phase。
- phase：相当于 class，包含一个或多个 goal。
- goal：相当于 method，真正干活的。

```sh
# maven 先执行 clean 生命周期并运行到 clean 过程，然后执行 default 声明周期并运行到 package 这个过程。
mvn clean package
```

<br/>
<br/>

### Maven使用插件

使用 Maven 构建项目，就是执行声明周期，执行到指定的过程。每个过程执行自己的目标。

实际上，执行每个过程，都是通过插件（plugin）来执行的。所以，Maven 实际上就是配置好需要使用的插件，然后通过过程调用它们。

```sh
# maven 执行 compile 这个过程，
# compile 过程会调用 compiler 插件执行关联的 compiler:compile 这个目标。
mvn compile
```

<br/>

Maven 内置的一些常用的标准插件。

| 插件名称 | 对应的执行过程 |
| - | - |
| clean | clean |
| compiler | compile |
| surefire | test |
| jar | package |

<br/>

如果标准插件无法满足需求，我们可以声明自定义插件。

```xml
<project>
  ...
  <build>
    <plugins>
      <plugin>
        ...
</project>
```

<br/>
<br/>

### Maven模块管理

把一个大项目拆分为多个模块。Maven 可以有效地管理多个模块，只需把每个模块当作一个独立的 Maven 项目。

如果各个模块的 pom 中有重复部分，可以把重复部分提取出来写到 parent 里，然后继承它。

```
mutiple-project
├── pom.xml
├── module-a
│   ├── pom.xml
│   └── src
├── module-b
│   ├── pom.xml
│   └── src
└── module-c
    ├── pom.xml
    └── src
```

<br/>
<br/>

### mvnw

mvnw 是 Maven Wrapper 的缩写。对某些项目，它可能必须使用某个特定版本的 Maven，这时就可以使用 mvnw。mvnw 可以负责给特定的项目安装特定版本的 Maven，而不影响全局和其他项目。

<br/>
<br/>

### 发布工件

发布自己的 jar 包到 Maven 仓库中，别人通过 `groupId:artifactId:version` 引用即可使用。

有三种常用的方法：

- 以静态文件发布
- 通过 Nexus 发布到中央仓库
- 发布到私有仓库

<br/>
<br/>

### SETTINGS配置文件

- 全局配置：`maven目录/conf/settings.xml`。
- 用户配置：`$home/.m2/settings.xml`。用户配置优先于全局配置。

<br/>

配置详解：

- LocalRepository: 本地仓库路径。
- interactiveMode：是否需要和用户交互以获得输入。
- usePluginRegistry：是否需要使用 plugin-registry.xml 文件来管理插件版本。
- offline：是否需要在离线模式下运行。
- pluginGroups：当插件的组 groupId 没有显式提供时，供搜寻插件 groupId 的列表。
- servers：服务端的一些设置。
- mirrors：配置镜像。
- proxies： 配置代理。
- profiles：根据环境参数来调整构建配置的列表。
- activation：自动触发 profile 的条件逻辑。
- properties：对应profile的扩展属性列表。
- repositories：远程仓库列表。

<br/>

### POM工程文件

项目对象模型（pom, project object model），`pom.xml` 是 Maven 项目的配置文件，用以描述项目的各种信息。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- The Basics -->
  <groupId>...</groupId>
  <artifactId>...</artifactId>
  <version>...</version>
  <packaging>...</packaging>
  <dependencies>...</dependencies>
  <parent>...</parent>
  <dependencyManagement>...</dependencyManagement>
  <modules>...</modules>
  <properties>...</properties>

  <!-- Build Settings -->
  <build>...</build>
  <reporting>...</reporting>

  <!-- More Project Information -->
  <name>...</name>
  <description>...</description>
  <url>...</url>
  <inceptionYear>...</inceptionYear>
  <licenses>...</licenses>
  <organization>...</organization>
  <developers>...</developers>
  <contributors>...</contributors>

  <!-- Environment Settings -->
  <issueManagement>...</issueManagement>
  <ciManagement>...</ciManagement>
  <mailingLists>...</mailingLists>
  <scm>...</scm>
  <prerequisites>...</prerequisites>
  <repositories>...</repositories>
  <pluginRepositories>...</pluginRepositories>
  <distributionManagement>...</distributionManagement>
  <profiles>...</profiles>
</project>
```

<br/>
<br/>

### Maven常用命令

常用的过程：

- clean：清理
- compile： 编译
- test：运行测试
- package：打包
- install：安装包到本地仓库
- deploy：发布到远程仓库

<br/>

```sh
mvn [plugin-name]:[goal-name]

# 创建项目
# -DgroupId=packageName, -DartifactId=projectName
mvn archetype:generate

# 验证项目
mvn validate

# 打包
mvn package
mvn clean package
# 跳过单元测试 -Dmaven.test.skip=true

# 只要 jar 包
mvn jar:jar

# 编译
mvn compile

# 测试
mvn test

# 检查
mvn verify

# 清理
mvn clean

# 下载依赖
mvn install

# 发布项目
mvn deploy

# 显示依赖
mvn dependency:tree
mvn dependency:list
```

<br/>
<br/>

## Gradle











