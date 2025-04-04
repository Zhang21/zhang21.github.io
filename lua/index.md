# Lua语言


Lua 文档笔记。

<!--more-->

---

参考：

- [Lua 官方文档](http://www.lua.org/docs.html)

<br/>
<br/>

# 介绍

Lua 是一种强大、高效、轻量级的可嵌入脚本语言。它支持面向过程编程、面向对象编程、函数式编程、数据驱动编程和数据描述。

Lua 结合了简单的面向过程式语法和基于关联数组和可扩展语义的强大数据结构描述构造的编程语言。Lua 是动态类型的，通过解释字节码（bytecode）并使用基于寄存器的虚拟机运行，并具有自动内存管理和分代垃圾回收功能，使其非常适用于配置、脚本和快速原型开发。

Lua 是一个以干净的 C 语言编写的库，实现了标准的 C 和 C++ 的共同子集。Lua 发行版包括一个名为 `lua` 的主机程序，该程序使用 Lua 库提供完整、独立的 Lua 解释器，可用于交互式或批处理使用。

作为一个扩展语言，Lua 没有主程序（main）的概念。它嵌入在宿主客户端中，宿主程序可以调用函数来执行一段 Lua 代码，可以读写 Lua 变量，并且可以注册 C 函数供 Lua 代码调用。通过使用 C 函数，Lua 可以增强处理各种不通领域的能力，从而创建共享语法框架的定制化编程语言。

<br/>
<br/>

# 基础概念









































