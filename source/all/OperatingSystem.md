---
title: 操作系统
date: 2019-04-28 08:59:12
mathjax: true
categories: DevOps
tags:
  - OperatingSystem
  - 操作系统
  - Linux
  - Optimization
  - 性能优化
---


参考:

- OS Three Easy Pieces: <https://book.douban.com/subject/19973015/>
- OSTEP: <http://pages.cs.wisc.edu/~remzi/OSTEP/>

<br/>

环境:

- ELRH7x86_64









<br/>
<br/>

---

<!--more-->

<br/>
<br/>

















# 操作系统介绍

Introduction to Operating Systems


如果你正在攻读本科操作系统课程，你应该已经知道计算机程序运行时的想法。如果没有，这本书将很难。

那么，程序运行时会发生什么呢？
好吧，正在运行的程序做了一件非常简单的事情: **它执行指令(it executes instructions)**。
每秒都有成千上万次，处理器从内存(mem)中取出(fetch)指令，对其进行解码(decode)——即确定这是哪条指令，并执行它。完成此指令后，进程将继续执行下一条指令，以此类推，直到程序最终完成。

刚刚描述了**冯-诺依曼计算模型(Von Neumann model of computing)**的基础知识。在本书中，我们将学习在程序运行的同时，还有许多其它的东西在运行，其主要目标是使系统易于使用。

事实上，有一大堆软件负责使应用程序运行变得容易，允许程序共享内存(share mem)，使程序与设备交互...该软件主体称为**操作系统(Operating System)**，它负责确保系统操作以易于使用的方式正确有效地运行。

操作系统执行此操作的主要方式是通过我们称为**虚拟化(Virtualization)**的通用技术。也就是说，操作系统采用**物理资源(Physical Resource)**(如处理器，内存，磁盘)并将其转换为更通用，功能强大且易于使用的虚拟形式。因此，我们有时将操作系统称为**虚拟机(Virtual Machine)**。

> **问题的关键: 如何虚拟化资源**
> 本书的一个核心问题：操作系统如何虚拟化资源？操作系统为什么要虚拟化资源——它使得系统更易于使用。因此，我们关注：操作系统使用什么机制和策略来实现虚拟化？操作系统如何有效地进行操作？需要什么硬件支持？

<br>

当然，为了告诉用户操作系统做什么，从而利用虚拟机的功能(如运行程序、分配内存、访问文件...)，操作系统还提供了一些可调用的接口(API)。事实上，典型的操作系统会**导出(Export)**数百个可供应用程序访问的**系统调用(System Call)**。
由于操作系统提供这些系统调用来运行程序、访问内存和设备、以及其它相关操作，有时也会说操作系统为应用程序提供了一个**标准库(Standard Library)**。

最后，因为虚拟化允许多个程序运行(共享CPU)，并且许多程序同时(Concurrently)访问它们自己的指令和数据(共享内存)，以及许多程序访问设备(共享磁盘等)，操作系统有时被称为**资源管理器(Resource Manager)**。每个CPU、MEM、DISK都是系统的资源。因此，操作系统的角色是管理这些资源，有效或公平地执行。



<br/>
<br/>


## 虚拟化CPU

Virtualizing the CPU


```c
// Code That Loops and Prints (cpu.c)

#include <stdio.h>
#include <stdlib.h>
#include <sys/time.h>
#include <assert.h>
#include "common.h"

int
main(int argc, char *argv[])
{
    if (argc != 2) {
        fprintf(stderr, "usage: cpu <string>\n");
        exit(1);
    }
    char *str = argv[1];
    while (1) {
        Spin(1);
        printf("%s\n", str);
    }
    return 0;
}
```

<br>

上图的程序，它所做的只是调用`Spin()`——这是一个重复检查时间的函数。
接下来我们具有单个处理器的系统上编译和运行它:

```shell
# 编译
# 此处遇到两个错误
# 1. cpu.c:(.text+0xe0)：对‘pthread_create’未定义的引用
# 2. cpu.c:(.text+0x127)：对‘pthread_join’未定义的引用
# 网上方法: 在编译时需要添加 -lpthread 参数来使用 libpthread.a 库进行编译
gcc -o cpu cpu.c -Wall -lpthread


# 运行
./cpu "ABC"
ABC
ABC
...
# 需手动终止
Ctrl+C
```

<br>

现在让程序复杂一点:

```shell
./cpu A &  ./cpu B &  ./cpu C &  ./cpu D &

[1] 7353
[2] 7354
[3] 7355
[4] 7356
A
B
D
C
A
B
D
C
A
C
B
D
...
```

现在事情变得更有趣了。即使只有一个处理器，但不知何故，所有四个程序都在同时运行！这种魔力是如何发生的呢？

事实证明，操作系统在硬件的帮助下负责这种**错觉(illusion)**——即系统具有大量虚拟CPU的错觉。将单个CPU转换为看似无限数量的CPU，从而允许许多程序看起来像是一次运行，这就是我们所说的虚拟化CPU，这是本书第一部分的重点。

当然，要运行程序并停止它，以及告诉操作系统运行哪些程序，需要使用一些接口(API)来将你的需求传递给操作系统。实际上，它们是大多数用于与操作系统交互的主要方式。

你可能还注意到，一次运行多个程序会引发各种新问题。如，如果两个程序需要在特定时间运行，哪个应该运行。这些问题由操作系统的策略来回答，策略在操作系统中的许多不同位置用户回答这些类型的问题。





<br/>
<br/>
<br/>




## 虚拟内存

Virtualizing Memory


```c
// A Program that Accesses Memory (mem.c)

#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include "common.h"

int
main(int argc, char *argv[])
{
    int *p = malloc(sizeof(int)); // a1
    assert(p != NULL);
    printf("(%d) memory address of p: %08x\n",
        getpid(), (unsigned) p); // a2
    *p = 0; // a3
    while (1) {
        Spin(1);
        *p = *p + 1;
        printf("(%d) p: %d\n", getpid(), *p); // a4
    }
    return 0;
}
```

<br>

现在让我们考虑一下**内存(memory)**。现代机器提供的物理内存的模型非常简单。内存只是一个**字节数组(a array of bytes)**。要读取内存，必须指定一个地址才能访问存储在那里的数据。要写入或更新(write/update)内存，还必须指定要写入数据的给定地址。

程序运行时始终访问内存。程序的所有数据结构保存在内存中，并通过各种指令访问它们，如`loads`, `stores`或其它在执行工作时访问内存的显式指令。不要忘记程序的每条指令也在内存中，每次**取(fetch)指令**时访问内存。

让我们来看下通过调用`malloc()`来分配一些内存的上面那个程序:

```shell
./mem
(6941) memory address of p: 01f13010
(6941) p: 1
(6941) p: 2
^C

# 该程序做了几件事。首先，它分配一些内存(a1)。然后，它打印出内存的地址(a2)，然后将数字零放入新分配的存储器的第一个插槽(a3)。最后，它循环，延迟1秒并递增存储在p中保存的地址的值。它还打出程序的PID。


# Running The Memory Program Multiple Times
./mem & ./mem &
[1] 7544
[2] 7545
(7544) memory address of p: 012f3010
(7545) memory address of p: 006cf010
(7544) p: 1
(7545) p: 1
(7544) p: 2
(7545) p: 2
(7544) p: 3
(7545) p: 3
```

实际上，这正是这里发生的事情，因为操作系统正在虚拟化内存。每个进程都访问自己的**私有虚拟地址空间(private virtual address space)**(有时也称为地址空间)，操作系统以某种方式将其映射到计算机的物理内存中。一个正在运行的程序中的内存引用不会影响其它进程的地址空间。就运行程序而言，它具有物理内存。然而，现实是物理内存是由操作系统管理的共享资源。




<br/>
<br/>
<br/>
<br/>




## 并发

Concurrency


本书另一个主题是**并发性(concurrency)**。使用这个术语来指代同一程序中同时处理多个事件(即并发)。并发问题首先出现在操作系统自身，如前面的虚拟化程序，操作系统同时处理多个事情。

```c
// AMulti-threaded Program (threads.c)

 #include <stdio.h>
 #include <stdlib.h>
 #include "common.h"

 volatile int counter = 0;
 int loops;

 void *worker(void *arg) {
     int i;
     for (i = 0; i < loops; i++) {
         counter++;
     }
     return NULL;
}

 int
 main(int argc, char *argv[])
 {
     if (argc != 2) {
     fprintf(stderr, "usage: threads <value>\n");
     exit(1);
     }
     loops = atoi(argv[1]);
     pthread_t p1, p2;
     printf("Initial value : %d\n", counter);

     Pthread_create(&p1, NULL, worker, NULL);
     Pthread_create(&p2, NULL, worker, NULL);
     Pthread_join(p1, NULL);
     Pthread_join(p2, NULL);
     printf("Final value : %d\n", counter);
     return 0;
}


// >本程序使用Pthread create()创建两个线程
// 你可将线程视为与其它函数相同的内存空间中运行的函数，一次使用多个函数
```

<br>

> **如何构建正确的并发程序?**
> 当同一个内存空间中有许多并发执行的线程时，我们如何构建一个正常工作的程序？操作系统需要哪些原语？硬件提供哪些机制？如何使用它们来解决并发问题？

<br>

运行:

```shell
./threads
usage: threads <value>

./threads 1000
Initial value : 0
Final value : 2000


# 看看更高的值
./threads 10000
Initial value : 0
Final value : 17726

./threads 10000
Initial value : 0
Final value : 18741

./threads 10000
Initial value : 0
Final value : 20000
```

上面出现了既正常又奇怪的结果。这些结果与指令的执行方式有关。不幸的是，上面程序的一个关键部分，共享计数器递增，需要三个指令：

- 一个用于将计数器的值从内存加载到寄存器；
- 一个用于递增；
- 一个用于将其存储回内存。

因为这三个指令不是原子地执行(一次全部执行)，所以会发生奇怪的事。




<br/>
<br/>
<br/>
<br/>




## 持久化

Persistence


```c
// Program That Does I/O (io.c)

#include <stdio.h>
#include <unistd.h>
#include <assert.h>
#include <fcntl.h>
#include <sys/types.h>

int
main(int argc, char *argv[])
{
    int fd = open("/tmp/file", O_WRONLY | O_CREAT | O_TRUNC, S_IRWXU);
    assert(fd > -1);
    int rc = write(fd, "hello world\n", 13);
    assert(rc == 13);
    close(fd);
    return 0;
}
```

<br>

第三个主题是**持久化(persistence)**。在系统内存中，数据很容易丢失，因为如DRAM的设备是以易失的方式存储值。当断电或系统奔溃时，内存中的任何数据都会丢失。因此，我们需要硬件和软件能够持久存储数据。硬件以某种I/O设备的形式出现。

通常，操作系统中管理磁盘的软件被称为**文件系统(file system)**。它负责将用户创建的任何文件以可靠和有效的方式存储在磁盘上。

与操作系统为CPU何MEM提供的抽象不同，操作系统不会为每个应用程序创建专用的虚拟化磁盘。相反，它假设用户经常想要共享文件中的信息。

来看下上面的代码，它打开`/tmp/file`文件，并将`hello world`写入文件。
```shell
./io

cat /tmp/file
hello world
```

<br>

> **如何持久存储数据？**
> 文件系统是负责管理持久化数据的操作系统的一部分。正确地处理需要哪些技术呢？面对硬件和软件故障，如何实现可靠性？

要完成此任务，程序会对操作系统进行三次调用。这些系统调用被路由到称为文件系统的操作系统部分，然后处理请求并向用户返回某种错误代码。

- 第一，调用`open()`打开文件；
- 第二，调用`write()`将数据写入文件；
- 第三，调用`close()`关闭文件。

你可能想知道操作系统为了写入磁盘而执行的操作。文件系统必须完成相当多的工作，首先确定这些新数据将驻留在磁盘上的哪个位置，然后在文件系统维护的各种结构中跟踪它。这样做需要向底层存储设备发出I/O请求，以读取现有结构或更新它们。
任何编写设备驱动程序的人都知道，让设备代表你做某事是一个复杂和详细的过程。它需要深入了解低级设备接口及其确切语义。幸运的是，操作系统提供了一个的标准和简单的方式——通过系统调用(system call)访问设备。因此，操作系统有时被视为**标准库(standard library)**。

为了处理写入期间系统崩溃的问题，大多数文件系统都包含某种复杂的写入协议。(如journaling或copy-on-write)。仔细写入磁盘以确保在写入序列期间发生故障时，系统之后可以恢复到合理的状态。为了使不同额公共操作高效，文件系统采用许多不同的数据结构和访问方法，从简单的列表到复杂的BTREE。




<br/>
<br/>
<br/>
<br/>




## 设计目标

Design Goals


现在我们知道了操作系统实际上做了什么：它使用物理资源(CPU, MEM, DISK...)，并虚拟化它们。它处理与并发相关的棘手的问题。它可以持久存储文件，从而使它们长期安全。最基本的目标是建立一些抽象，以使系统方便和易于使用。抽象是我们计算机科学中所做的一切的基础。

设计和实现操作系统的一个目标是提供**高性能(High Performance)**；另一个目标是尽量减少操作系统的**开销(overhead)**。虚拟化和使操作系统易于使用是值得的，但不是不惜任何代价。因此，我们必须提供虚拟化和其它操作系统功能，而无需过多开销。这些开销以多种形式出现：额外时间、额外空间。

另一个目标是在应用程序之间，以及操作系统和应用程序之间提供**保护(Protection)**。由于我们系统多个程序同时运行，所有希望确保每一个程序的恶意或偶然的不良行为不会伤害到其它程序或操作系统。保护操作系统的主要核心原则始操作系统的**隔离(Isolation)**。将进程彼此隔离是保护的关键，因此是操作系统必须做的大部分工作的基础。

操作系统必须不间断地运行，当它失败时，系统上运行的所有应用程序也会失败。由于这种依赖性，操作系统通常努力提供**高度可靠性(high degree of reliability)**。随着操作系统越来越复杂，构建可靠的操作系统是一个相当大的挑战，这也是一个确切的研究性问题。

其它目标也是有意义的:

- **能源效率**：在绿色世界中越发重要；
- **安全**：对恶意应用程序的安全性至关重要；
- **可移植性**：随着操作系统在越来越小的设备上运行，可移植性也变得很重要。





<br/>
<br/>
<br/>
<br/>




## 一些历史

Some History


让我们简单介绍下操作系统的发展过程。与人类构建的任何系统一样，随着时间的推移，操作系统中积累了许多好的想法。


<br/>


### Early Operating Systems: Just Libraries

一开始，操作系统并没有做太多。基本上，它只是一组常用函数库(function library)。

通常，在这些旧的主机系统上，一个程序由人工控制运行一次。许多你认为的现代操作系统将执行的大部分操作是由人工执行的。如果你和操作员很好，则他可以将你的工作移动到队列的前面。
这种计算方式称为**批处理(batch processing)**，因为设置了很多作业，然后由人工以批处理的方式运行。到目前为止，计算机并没有以**交互式(interactive)**方式使用。因为成本：让用户坐在电脑面前使用它太昂贵了，因为大多数时候它只是闲置，而每小时需要花费数十万美元。



<br/>
<br/>



### Beyond Libraries: Protection

作为一个简单的常用服务库，操作系统在管理机器方面发挥了中心角色的作用。其中一个重要的方面是认识到运行操作系统自身的代码是特殊的。它控制了设备，因此应该与正常的应用程序代码区别对待。
为什么这样？设想一下，如果你允许任何应用程序可以从磁盘的任何地方读取，隐私的概念就会消失，因为任何程序都可以读取任何文件。因此，实现文件系统作为一个库是没有任何意义的。

因此，**系统调用(system call)**的想法产生了。这里的想法是添加一对特殊的硬件指令和硬件状态，以便将操作系统转换为更正式、受控制的流程，而不是将操作系统例程(routine)作为库提供(你只需要进行过程调用以访问它们)。

**系统调用(system call)**和**过程调用(procedure call)**之间的关键区别在于，系统调用将控制转移到中，同时提高**硬件权限级别(hardware privilege level)**。用户应用程序在所谓的**用户模式(user mode)**下运行，这意味着硬件限制应用程序可以执行的操作。例如，以用户模式运行的应用程序通常不能发起对磁盘的I/O请求，但可以访问物理内存页面或在网络上发送数据包。
当启动系统调用时，硬件将控制转移到预先指定的陷阱处理程序(trap handler)，并同时将特权级别提升到**内核模式(kernel mode)**。在内核模式下，操作系统可以完全访问系统的硬件，因此可以执行如启动I/O请求等操作。当操作系统完成对服务的请求时，它通过特殊返回陷阱指令(return-from-trap instrction)将控制权传递给用户，该指令恢复到用户模式，同事将控制权传递回应用程序停止的位置。



<br/>
<br/>



### The Era of Multiprogramming

操作系统真正起飞的时代是超大型计算时代，即minicomputer时代。成本的下降影响了使用者和开发者，从而使计算机系统更加有趣和美好。

特别是，由于希望更好地利用机器资源，**多程序设计(multiprogramming)**变得司空见惯。操作系统不是一次只运行一个作业，而是将大量作业加载到内存中并在它们之间快速切换，从而提高CPU利用率。这种切换特别重要，因为I/O设备很慢，而CPU很快。在I/O正在服务时让CPU等待程序是在浪费CPU时间。相反，为什么不切换到另一个工作运行呢？

在存在I/O和中断的情况下支持多程序设计和重叠的愿望迫使操作系统的概念开发沿着多个方向进行创新。内存保存等问题变得很重要，我们不希望一个程序能够访问另一个程序的内存。了解如何处理多程序设计引入的并发问题也很关键，尽管存在中断，确保操作系统正常运行是一项巨大的挑战。

当时一个主要的进步是Unix操作系统的引入。Unix从不同的操作系统获得了很多好主意，但使它们更简单易用。很快，这个团队向世界各地的人们发送了包含Unix源代码的磁带，随后有许多人加入到了这个项目中来。



<br/>
<br/>



### The Modern Era

除了minicomputer之外，还出现了一种更便宜、速度更快的新机器，我们今天称之为PC(personal computer)。

不幸的是，对于操作系统而言，PC最初代表了一个巨大的飞跃，因为早期的系统忘记了在minicomputer时代学到的经验教训。例如，早期的操作系统，如DOS(the Disk Operating System, from Microsoft)，并不认为内存保护很重要。因此，恶意(或编程不佳)的应用程序可能会乱写内存。第一代Mac OS采用合作方式进行作业调度。因此，一个意外陷入无限循环的线程可以接管整个系统，迫使重启。这一代系统中缺少的操作系统功能的痛苦太多了...

幸运的是，经过几年的苦难，微机操作系统的旧功能开始找到它们的方式进入桌面系统。例如，Mac OS X/Mac OS的核心是Unix，包含了人们对这种成熟系统所期望的所有功能。Windows同样采用了计算历史中的许多好主意，特别是从Windows NT开始，这是Microsoft OS技术的一次重大飞跃。即便是今天的手机也运行这操作系统(如Linux)，这些操作系统更像是1970s年代的微型机，而不是1980s年代的PC。

<br>

> 旁白：**Unix的重要性**
> 在操作系统的历史中，很难夸大Unix的重要性。受其它早期系统的影响，Unix汇集了许多伟大的想法，并使得系统既简单又强大。
> 贝尔实验室的基础Unix是构建小型且强大程序的统一原则，这些程序可以连接在一起形成更大的工作流。shell提供了mete-level programing，当你输入命令，它将程序串联起来以完成更大的任务变得很容易。
> Unix环境对编程人员和开发人员都很友好，同时也为C编程语言提供了编译器。编程人员可以轻松编写自己的程序并共享它们，这使得Unix非常受欢迎。它还是免费的。
> 同样重要的是代码的可读性和可访问性。拥有一个用C编写的漂亮的小内核(kernel)并邀请别人试玩、添加新的酷的功能。
> 不幸的是，随着公司试图主张版权并从中获利，Unix的传播速度便有所放缓。许多公司都有自己的变体，如SunOS、HPUX...贝尔实验室和其它玩家之间的法律纠纷在Unix上投下了一片乌云，许多人想知道它是否能够活下来，特别是在Windows被引入并占据了PC市场的大部分时...

<br>

> 旁白：**然后来了Linux**(ASIDE: AND THEN CAME LINUX)
> 对于Unix，幸运的是，一位名叫**Linus Torvalds**的年轻芬兰Hacker决定编写它自己的Unix版本，该版本大量借用原始系统背后的原则和思想，但不是来自代码库，因此避免了合法性问题。他获得了世界各地许多人的帮助，利用了已经存在的复杂的GNU工具，很快Linux就诞生了(以及现代开源软件运动)。
> 随着互联网时代的带来，大多数公司(如Google、Amazon、Facebook..)选择运行Linux，因为它是免费的，可以随时修改以满足自己的实际需求。随着智能手机成为一个占主导地位的面向用户的平台，由于许多相同的原因，Linux也在那里找到了一个据点(Android)。



<br/>
<br/>



### 摘要

Summary


因此，我们介绍了操作系统。今天的操作系统相对易于使用，而你今天使用的几乎所有操作系统都受到将在本书中讨论的发展的影响。
不幸的是，书中不会介绍的很详细。例如，网络代码、图形设备、安全性。

但是，我们将介绍许多重要的主题，包括CPU和MEM的虚拟化知识，并发性以及通过设备和文件系统的持久性。别担心，虽然有很多方面可以覆盖，但大部分内容都非常酷，而且在路的尽头，你将对计算机系统的真正工作方式有了新的认识。现在开始吧！














<br/>
<br/>

---

<br/>
<br/>






















# 虚拟化

Virtualization


CPU虚拟化：

- A Dialogue on Virtualization
- The Abstraction: The Process
- Interlude: Process API
- Mechanism: Limited Direct Execution
- CPU Scheduling
- Scheduling: The Multi-Level Feedback Queue
- Scheduling: Proportional Share
- Multi-CPU Scheduling
- Summary Dialogue on CPU Virtualization

<br>

MEM虚拟化：

- A Dialogue on Memory Virtualization
- The Abstraction: Address Spaces
- Interlude: Memory API
- Mechanism: Address Translation
- Segmentation
- Free-Space Management
- Paging
- Paging: Faster Translations
- Paging: Smaller Tables
- Beyond Physical Memory: Swapping Mechanisms
- Beyond Physical Memory: Swapping Policies
- Complete Virtual Memory Systems
- Summary Dialogue on Memory Virtualization


<br/>
<br/>


## 进程

[The Abstraction: The Process](http://pages.cs.wisc.edu/~remzi/OSTEP/cpu-intro.pdf)


本章，我们将讨论操作系统为用户提供的最基本的抽象之一：**进程(process)**。进程的定义非常简单：它是一个**正在运行的程序(running program)**。程序本身是一个没有生命的东西，它位于磁盘上，一对指令(可能是一些静态数据)，等待开始行动。操作系统采用这些字节并使它们运行，将程序转换为有用的东西。

事实证明，人们经常想要同时运行多个程序。如运行浏览器、音乐播放器、邮件程序...实际上，典型的操作系统似乎可能同时运行数百个进程。这样做使系统易于使用，因为不需要关心CPU是否可用。

<br>

> TIP: USE TIME SHARING (AND SPACE SHARING)
> **时间共享(time sharing)**是操作系统用于共享资源的基本技术。通过允许资源被一个实体使用一段时间，然后另一个实体使用一段时间...资源可以被许多实体共享。时间共享对应于**空间共享(space sharing)**，其中资源在希望使用它的人之间被划分。例如，磁盘空间是一个空间共享资源，一旦将块分配给文件，在用户删除原始文件之前，通常不会将其分配给另一个文件。

<br>

我们的挑战是:

> 如何提供许多CPU的错觉？
> 虽然只有少数物理CPU可用，但操作系统如何提供几乎无穷无尽的CPU供应的错觉？

操作系统通过虚拟化(virtualizating)CPU来提供这种错觉。通过运行一个进程，然后停止它并运行另一个进程，等等。操作系统可以促使存在许多虚拟CPU存在的错觉，而实际上只有一个(几个)物理CPU。这种基本技术称为CPU的**时间共享/分时(time sharing)**，允许用户运行任意数量的并发进程(concurrent process)。潜在的成本是性能(Performance)，因为如果必须共享CPU，每个进程都会运行得更慢。

要实现CPU的虚拟化，操作系统需要一些低级机制(low-level machinery)和一些高级智能(high-level intelligence):

- **低级机制(low-level machinery mechanisms)**，此机制是实现所需功能的低级方法或协议。例如，我们稍后将学习如何实现**上下文切换(context switch)**，这使操作系统能够停止运行一个程序并在给定的CPU上开始运行另一个程序。所有现代操作系统都采用这个**分时机制(time-sharing mechanism)**。
- 操作系统中还存在一些智能的**策略(policy)**，策略是在操作系统中做出某种决定的算法。例如，给定一些可能在CPU上运行的程序，操作系统运行哪个程序？操作系统中的调度策略将做出此决定，可能使用历史信息，工作负载信息，性能指标...来做出决定。


<br/>
<br/>


### 一个进程

The Abstraction: A Process


为了理解进程的构成，我们必须了解其**机器状态(machine state)**：程序在运行时可以读取(read)或更新(update)的内容。在任何给定的时间，机器的哪些部分对于执行该程序很重要？包含进程的机器状态的一个明显组件是其内存(memory)。指令行在内存中，运行程序读写的数据也在内存中。因此，进程可以寻址的内存(称为其地址空间(address space))是进程的一部分。

**寄存器(registers)**也是进程机器状态的一部分。许多指令明确地读取或更新寄存器，因此它们对于执行过程很重要。

请注意，有一些特殊的寄存器构成了这种机器状态的一部分。如，**program counter(instruction pointer)**告诉我们当前正在运行哪个程序指令；**Stack Pointer**和相关的**frame pointer**用于管理函数参数、局部变量和返回地址的堆栈。

最后，程序通常也访问持久存储设备。此类I/O信息可能包括进程当前打开的文件列表。

<br>

> TIP: SEPARATE POLICY AND MECHANISM
> 在许多操作系统中，常见的设计范例是将高级策略与其低级机制分开。如，操作系统如何执行上下文切换？操作系统现在应该运行哪个进程？将两者分开可以很容易地改变策略，而不必重新考虑该机制，因此是一种模块化形式，一般的软件设计原则。



<br/>
<br/>



### Process API


先了解操作系统的任何接口中必须包含的内容，这些API以某种形式可用于任何现代操作系统。

- **Create**：操作系统必须包含一些创建新进程的方法；
- **Destroy**：由于存在创建进程的接口，因此系统还提供了强制销毁进程的接口。当然，许多进程都会运行并在完成后自动退出。然而，当它们不这样做时，用户可能希望杀死它们；
- **Wait**：有时，等待进程停止运行时有用的；
- **Miscellaneous Control**：除了杀死或等待进程之外，有时还有其它可能的控制措施。如暂停进程，然后恢复它；
- **Status**：通常还有接口来获取有关进程的一些状态信息，如运行了多久...

![](/images/OSTEP/processToProcess.png)



<br/>
<br/>



### 进程创建

Process Creation: A Little More Detail

我们应揭开的一个谜团是如何将**程序(program)**转换为**进程(process)**。具体来说，操作系统如何启动并运行程序？进程创建实际上如何运作？

操作系统运行程序必须做的第一件事是将其代码和任何静态文件数据(如变量...)加载(load)到程序的地址空间中(address space of process)。程序最初以某种可执行格式驻留在磁盘(disk)上。因此，将程序和静态数据加载到内存中的过程需要操作系统从磁盘读取这些字节，并将它们放在内存中。
在早期操作系统中，加载过程是**热切地(eagerly)**完成，即在运行程序之前一次完成；现代操作系统**懒惰地(lazily)**执行该过程，即仅在程序执行期间需要加载代码或数据。要真正了解代码和数据的延迟加载是如何工作的，你必须更多地了解**分页(paging)**和**交换(swapping)**的机制，这将在内存虚拟化里讨论。现在只需记住，在运行任何操作之前，操作系统显然必须要做一些工作才能将重要的程序从磁盘放入内存。

一旦将代码和静态数据加载到内存中，操作系统在运行该进程之前还需要执行一些其它操作。必须为程序的**运行时栈(runtime stack)**分配一些内存。如C程序将堆栈用于局部变量、函数参数和返回地址。操作系统分配此内存并将其提供给进程。操作系统也可能使用参数初始化堆栈，具体来说，它将填充`main()`函数的参数(`argc, argv`数组)。

操作系统还可以为程序的**堆(heap)**分配一些内存。在C程序中，堆用于显式请求的动态分配数据调用`malloc()`来请求这样的空间，并通过调用`free()`显式释放它。数据结构需要堆，如链表(linked list)、哈希表(hash table)、树(tree)和其它又去的数据结构。堆最初会很小，当程序运行并通过`malloc()`库API请求更多内存时，操作系统可能会参与并为进程分配更多内存以满足此类调用。

操作系统还将执行一些其它初始化任务，尤其是与I/O相关的任务。例如，在Unix系统中，默认情况下每个进程都有三个打开的**文件描述符(file descriptors)**，用于stdin, stdout, stderr。这些描述符使程序可以轻松地从终端读取输入并将输出打印到屏幕。将在持久化中详细介绍I/O和文件描述符。

通过将代码和静态数据加载到内存中，通过创建和初始化堆，通过执行与I/O设置相关的其它工作，操作系统最终为程序执行设置了阶段。它还有最后一个任务：启动在入口点运行的程序，即`main()`。通过跳转到`main()`例程，操作系统将CPU的控制权转移到新创建的进程，从而程序开始执行。



<br/>
<br/>



### 进程状态

Process States

现在我们已经知道一个进程是什么以及如何创建它。现在来看看一个进程在给定事件可以处于的不同状态。进程可处以以下三种状态：

- **Running**：进程正在处理器上运行，这意味着它正在执行指令；
- **Ready**：进程已准备好，但由于某种原因，操作系统已选择不在此刻运行它；
- **Blocked**：进程执行某种操作，使其在其它事件发生之前不准备运行。例如，当进程向磁盘发起I/O请求时，它会被阻塞，因此其它一些进程可以使用该处理器。

如下图所示，可以根据系统的判断在准备和运行之间移动进程。从准备到运行意味着该进程已**调度(scheduled)**好；从运行转移到准备意味着该进程被**取消调度(discheduled)**。一旦进程被**阻塞(blocked)**，操作系统将保持这样知道某些事件完成，此时进程再次进入就绪状态。

![](/images/OSTEP/processStateTransitions.png)

<br>

来看一个栗子，两个进程如果通过其中一些状态转换的示例。

| Time | Process0 | Process1 | Notes |
| - | - | - | - |
| 1 | Running | Ready | |
| 2 | Running | Ready | |
| 3 | Running | Ready | |
| 4 | Running | Ready | Process0 now done |
| 5 | – | Running | |
| 6 | – | Running | |
| 7 | – | Running | |
| 8 | – | Running | Process1 now done |

<br>

这个栗子中，process0在运行一段时间后发出I/O请求。此时，该进程被阻塞，使另一个进程有机会运行。
更具体地说，process0启动I/O并被阻塞等待它完成。例如，从磁盘读取或等待来自网络的数据包时，进程会被阻止。操作系统识别process0未使用CPU并开始运行process1。当process1运行时，process0的I/O完成，将process0移回准备状态。最后，process1完成，process0运行然后完成。

| Time | Process0 | Process1 | Notes |
| - | - | - | - |
| 1 | Running | Ready | |
| 2 | Running | Ready | |
| 3 | Running | Ready | Process0 initiates I/O |
| 4 | Blocked | Running | Process0 is blocked |
| 5 | Blocked | Running | so Process1 runs |
| 6 | Blocked | Running | |
| 7 | Ready | Running | I/O done |
| 8 | Ready | Running | Process1 now done |
| 9 | Running | – | |
| 10 | Running | – | Process0 now done |

请注意，即使在这个简单的示例中，操作系统也必须做出许多决定。首先，系统必须在process0发出I/O时运行process1；这样做可以通过保持CPU忙碌来提高资源利用率。其次，系统决定在其I/O完成时不切换会process0。目前尚不清楚这是否是一个好的决定。这些类型的决策是由操作系统调度程序做出的。



<br/>
<br/>



### 数据结构

Data Structures

操作系统是一个程序，与任何程序一样，它有一些追踪各种相关信息的关键**数据结构(data structure)**。例如，为了追踪每个进程的状态，操作系统可能会为所有准备好的进程保留某种进程列表，并提供一些其它信息来追踪当前正在运行的进程。操作系统还必须以某种方式追踪被阻塞的进程；当I/O事件完成时，操作系统应确保唤醒正确的进程并准备好再次运行。

下面显示了操作系统需要跟踪内核中每个进程的信息类型。类似的过程结构存在于真实操作系统中，如Linux、Mac OSX、Windows...看看它们有多复杂。你可以看到操作系统追踪进程的几个重要信息。
对于已停止的进程，**寄存器上下文(register context)**将保持其寄存器的内容。当进程停止时，其寄存器将保持到该内存位置(memory location)。通过恢复这些寄存器，操作系统可以恢复运行该进程。这在以后上下文切换中详细介绍。

```c
// the registers xv6 will save and restore
// to stop and subsequently restart a process
struct context {
int eip;
int esp;
int ebx;
int ecx;
int edx;
int esi;
int edi;
int ebp;
};

// the different states a process can be in
enum proc_state { UNUSED, EMBRYO, SLEEPING,
                  RUNNABLE, RUNNING, ZOMBIE };

// the information xv6 tracks about each process
// including its register context and state
struct proc {
  char *mem; // Start of process memory
  uint sz; // Size of process memory
  char *kstack; // Bottom of kernel stack
        // for this process
  enum proc_state state; // Process state
  int pid; // Process ID
  struct proc *parent; // Parent process
  void *chan; // If non-zero, sleeping on chan
  int killed; // If non-zero, have been killed
  struct file *ofile[NOFILE]; // Open files
  struct inode *cwd; // Current directory
  struct context context; // Switch here to run process
  struct trapframe *tf; // Trap frame for the
        // current interrupt
};
```

还可从图中看出，除了running, ready, blocked之外，还有一些进程可以处于其它状态。
有时，系统将具有该进程在创建是所处的**初始状态(initial state)**。此外，可将进程置于已退出但尚未清除的**最终状态(final state)**(在基于Unix的系统中，这称为**僵尸状态(zombie state)**)。这个最终状态非常有用，因为它允许其它进程(通常是创建此进程的父进程)检查进程的返回代码并查看刚刚完成的进程是否成功运行(通常，程序在基于Unix系统中的返回码为0时，表示已成功完成任务，否则返回非0)。完成后，父进程将进行最后一次调用以等待孩子进程的完成，并且还向操作系统指示它可以清理任何涉及现在已经灭绝的进程的相关数据结构。

<br>

> ASIDE: DATA STRUCTURE — THE PROCESS LIST
> 操作系统充满了各种重要的数据结构。进程列表(process list)，也称为任务列表(task list)。它是比较简单的一个，都是现在能够同时运行多个程序的操作系统都会有类似于这种结构的东西，以便追踪系统中所有正在运行的程序。有时，人们会将存储过程信息的单个结构称为**进程控制块(PCB, process control block)**。

<br>

> ASIDE: KEY PROCESS TERMS(关键进程术语)
> **进程(process)**是正在运行的程序的主要操作系统抽象。在任何时间点，该进程都可以通过其状态来描述：其地址空间中的内存内容、CPU寄存器的内容、有关I/O的信息。
> **进程API**由可使进程相关联的调用程序组成。通常，这包括创建、销毁、其它有用的调用。
> 进程存在许多不同的**进程状态(process state)**，包括running、ready、blocking。不同的时间将进程从这些状态之一转换到另一个状态。
> **进程列表(process list)**包含有关系统中所有进程的信息。每个条目有时称为进程控制块(PCB)，它实际上只是一个包含特定进程信息的结构。




<br/>
<br/>
<br/>
<br/>




## 进程API

[Process API](http://pages.cs.wisc.edu/~remzi/OSTEP/cpu-api.pdf)


> ASIDE: INTERLUDES
> **插曲(interludes)**将涵盖系统的多个实际方面，包括特别关注系统API以及如何使用它们。如果你不喜欢实际的事物(practical things)，你可跳过它。但你应该了解它，因为它们通常在现实生活中很有用。

<br>

> CRUX: HOW TO CREATE AND CONTROL PROCESSES
> 操作系统应该为进程创建和控制提供哪些接口？如何设计这些接口以实现强大的功能、易用性和高性能？

<br>

在此插曲中，将讨论Unix系统中的进程创建。Unix提供了一种使用一对**系统调用(system call)**创建新进程的最有趣的方法:

- `fork()`
- `exec()`

第三个例程，可以由希望等待进程创建完成的进程使用：

- `wait()`


<br/>
<br/>


### fork系统调用

The fork() System Call

`fork()`系统调用用于创建新进程。但是，要预先警告：这是你将要调用的最奇怪的例行程序。更具体的说，你有一个正在运行的程序，代码如下所示。输入并运行它。

```c
// Calling fork() (p1.c)

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    printf("hello world (pid:%d)\n", (int) getpid());
    int rc = fork();
    if (rc < 0) { // >>>fork failed; exit
        fprintf(stderr, "fork failed\n");
        exit(1);
    } else if (rc == 0) { // child (new process)
        printf("hello, I am child (pid:%d)\n", (int) getpid());
    } else { // parent goes down this path (main)
       printf("hello, I am parent of %d (pid:%d)\n",
            rc, (int) getpid());
    }
    return 0;
}
```

运行:

```bash
./p1
hello world (pid:14506)
hello, I am parent of 14507 (pid:14506)
hello, I am child (pid:14507)
```

当它第一次运行时，该进程打印出`hello world`消息，包含在该消息中的**进程标识符(PID, process identifier)**。在Unix系统中，如果想要对进程执行某些操作(如停止它)，则使用PID来命名进程。

现在有趣的部分开始了：该进程调用`fork()`系统调用，操作系统提供该此方法来创建新进程。奇怪的部分：创建的进程是调用进程的精确副本。这意味着对于操作系统来说，现在看起来的两个进程都是p1程序运行的副本，并且两个进程都从`fork()`系统调用返回。新创建的进程(child)不会像`main()`那样开始在`main()`上运行(hello world只打印了一次)。相反，它刚出现时，好像它已经调用了`fork()`本身。

你可能注意到，子进程不是一个精确的副本。尽管它有自己的地址空间、寄存器、PC...，它返回给`fork()`调用者的值是不同的。具体来说，当父进程接收新创建的子进程PID时，子进程接收返回码0.

你可能还注意到：p1的输出不确定。系统中有两个活动的父进程和子进程。假设在单个CPU的系统上运行，可能会发生相反的情况。

```bash
./p1
hello world (pid:29146)
hello, I am child (pid:29147)
hello, I am parent of 29147 (pid:29146)
```

CPU调度器，确定哪个进程在给定时刻运行。因为调度程序很复杂，我们通常不能对它将选择做什么做出强有力的假设，如最先运行哪个进程。这些不确定性导致了一些有趣的问题，特别是在多线程程序中，这将在并发中讨论。



<br/>
<br/>



### wait系统调用

The wait() System Call

到目前为止，我们还没有做太多工作：只创建了一个打印消息并退出的子进程。有时，事实证明，父进程等待子进程完成它一直在做的事情时非常有用的。这个任务是通过`wait()`系统调用完成的。

在下面的栗子中，父进程调用`wait()`以延迟执行，直到子进程执行完毕。子进程完成后，`wait()`返回父进程。添加了`wait()`调用使得数据具有稳定性，你们明白为什么吗？

```c
// Calling fork() And wait() (p2.c)

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main(int argc, char *argv[]) {
    printf("hello world (pid:%d)\n", (int) getpid());
    int rc = fork();
    if (rc < 0) { // >>>fork failed; exit
        fprintf(stderr, "fork failed\n");
        exit(1);
    } else if (rc == 0) { // child (new process)
        printf("hello, I am child (pid:%d)\n", (int) getpid());
    } else { // parent goes down this path (main)
        int rc_wait = wait(NULL);
        printf("hello, I am parent of %d (rc_wait:%d) (pid:%d)\n",
            rc, rc_wait, (int) getpid());
    }
    return 0;
}
```

```bash
 ./p2
hello world (pid:29266)
hello, I am child (pid:29267)
hello, I am parent of 29267 (rc_wait:29267) (pid:29266)
```

使用此代码，我们现在知道子进程将首先打印。但是，如果父进程碰巧先运行，它会立即调用`wait()`，这个系统调用在子进程运行并退出之前不会返回。因此，即使父进程先运行，它礼貌地等待子进程完成运行，然后`wait()`返回，然后父进程打印它的消息。



<br/>
<br/>



### exec系统调用

The exec() System Call

进程创建API的最后一个重要部分是`exec()`系统调用。当你想要运行与调用程序不同的程序时，此系统调用很有用。例如，在p2中调用`fork()`仅在你希望继续运行同一程序的副本时才有用。但是，通常你想运行一个不同的程序，`exec()`就是这么做的。

在下面的栗子中，子进程调用`execvp()`以运行程序wc(word count)。实际上，它从p3上运行wc，返回行数、词数和字节数。

```c
// Calling fork(), wait(), And exec() (p3.c)

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/wait.h>

int main(int argc, char *argv[]) {
    printf("hello world (pid:%d)\n", (int) getpid());
    int rc = fork();
    if (rc < 0) { // >>>fork failed; exit
        fprintf(stderr, "fork failed\n");
        exit(1);
    } else if (rc == 0) { // child (new process)
        printf("hello, I am child (pid:%d)\n", (int) getpid());
        char *myargs[3];
        myargs[0] = strdup("wc"); // program: "wc" (word count)
        myargs[1] = strdup("p3.c"); // argument: file to count
        myargs[2] = NULL; // marks end of array
        execvp(myargs[0], myargs); // runs word count
        printf("this shouldn’t print out");
    } else { // parent goes down this path (main)
    int rc_wait = wait(NULL);
    printf("hello, I am parent of %d (rc_wait:%d) (pid:%d)\n",
         rc, rc_wait, (int) getpid());
    }
    return 0;
}
```

```bash
./p3
hello world (pid:29383)
hello, I am child (pid:29384)
29 107 1030 p3.c
hello, I am parent of 29384 (rc_wait:29384) (pid:29383)
```

`fork()`系统调用很奇怪，它的伙伴`exec()`也不是那么正常。它的作用：给定可执行文件的名称和一些参数，从该可执行文件加载代码和静态数据并覆盖其当前代码段，重新初始化堆和栈以及程序内存空间的其它部分。然后操作系统运行该程序，传入任意参数为该进程的argv。因此，它不会创建新的进程。相反，它将当前运行的程序(p3)转换为不同的运行程序(wc)。在子进程的`exec()`之后，几乎就好像p3从未运行过，成功调用`exec()`永远不会有返回。



<br/>
<br/>



### Motivating The API

Why? Motivating The API

当然，可能会遇到一个大问题：为什么要建立一个奇怪的接口来创建一个新进程？事实证明，`fork()`和`exec()`的分离对于构建Unix shell至关重要，因为它允许shell在调用`fork()`之后，在调用`exec()`之前运行代码。此代码可以改变即将运行的程序的环境，从而可以轻松构建各种有趣的功能。

<br>

> TIP: GETTING IT RIGHT
> 简单和抽象都不能代替正确。有很多方法可以为进程创建设计API，但是，`fork()`和`exec()`的组合非常简单和强大。在这里，Unix设计师做对了。

<br>

shell只是一个用户程序。它会向你显示提示，然后等待你输入内容。你输入一个命令，在大多数情况下，shell确定文件系统中可执行文件所在的位置，调用`fork()`创建一个新的子进程来运行命令，调用`exec()`的某个变体来运行命令，然后通过调用`wati()`命令来等待命令的完成。当子进程完成时，shell从`wait()`返回并再次打印出一个提示，为下一个命令做好准备。

`fork()`和`exec()`的分离允许shell很容易地完成一堆有用的东西。例如: `wc p3.c > 1.txt`
shell完成此任务的方式非常简单，在创建子进程时，在调用`exec()`之前，shell关闭stdout并打开文件`1.txt`。通过这样做，即将运行的此程序的任何输出都被发送到文件而不是屏幕。

下面的程序便完成这样的操作:

```c
// All Of The Above With Redirection (p4.c)

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <fcntl.h>
#include <sys/wait.h>

int main(int argc, char *argv[]) {
    int rc = fork();
    if (rc < 0) { // >>>fork failed; exit
        fprintf(stderr, "fork failed\n");
        exit(1);
    } else if (rc == 0) { // child: redirect standard output to a file
        close(STDOUT_FILENO);
        open("./p4.output", O_CREAT|O_WRONLY|O_TRUNC, S_IRWXU);
        // now exec "wc"...
        char *myargs[3];
        myargs[0] = strdup("wc"); // program: "wc" (word count)
        myargs[1] = strdup("p4.c"); // argument: file to count
        myargs[2] = NULL; // marks end of array
        execvp(myargs[0], myargs); // runs word count
    } else { // parent goes down this path (main)
        int rc_wait = wait(NULL);
    }
    return 0;
}
```

执行:

```bash
./p4

cat p4.output
 29 116 874 p4.c
```

首先，当运行p4时，看起来好像什么也没有发生过。shell只是打印命令提示符，并立即为下一个命令做准备。但事实并非如此，p4确实调用`fork()`来创建一个新子节点，然后通过调用`execvp()`来运行wc程序。你没有看到任何输出打印到屏幕是因为它已被重定向到文件中。

Unix **管道(pipe)** 以类似的方式实现，但使用`pipe()`系统调用。这种情况下，一个进程的输出连接到一个内核管道(即队列)，另一个进程的输入连接到同一个管道。因此，一个进程的输出无缝地用作下一个进程的输入，并且长而有用的命令链可以串在一起。如这个栗子: ` grep -o foo file | wc -l`。

现在，只需说`fork()`和`exec()`组合是一种创建和操作进程的强大方法就足够了。

<br>

> ASIDE: **READ THE MAN PAGES**
> 本书中，当提到特定的系统调用或库调用时，会让你阅读**手册(man/manual pages)**。花时间阅读手册石喜彤程序员成长的关键一步，这些手册页中隐藏了大量有用的花絮。
> 最后，阅读手册可让你避免一些尴尬。当你向别人询问问题是，别人可能会叫你阅读文档。



<br/>
<br/>



### 进程控制和用户

Process Control And Users

在Unix系统中，除了`fork()`, `exec()`, `wait()`之外，还有许多其它接口可与进程进行交互。例如，`kill()`系统调用用于向进程发送**信号(signal)**，包括暂停(pause)、死亡(die)和其它有用的指令。为了方便起见，在大多数Unix Shell中，某些键组合被配置为向当前运行的进程传递特定信号。栗子如下:

| 键组合 | 信号 | 描述 |
| - | - | - |
| `ctrl+c` | SIGINT(2) | 中断 |
| `ctrl+z` | SIGTSTOP(19) | 停止(暂停) |

整个信号子系统提供了丰富的基础设施，可为进程提供外部事件，包括在各个进程中接收和处理这些信号的方法，以及向各个进程以及整个进程组(process groups)发送信号的方法。要使用这种通信形式，进程应使用`signal()`系统调用来捕获(catch)各种信号。这样做可确保当特定信号传递到进程时，它将暂停正常执行并运行特定代码以响应该信号。

这自然会提出一个问题：**谁可以向进程发送信号，谁不能发送？**
通常，系统可以让多个用户同时使用。如果其中一个人可以随意发送信号(如`SIGINT`)，则系统的可用性和安全性将受到影响。因此，现代系统包含用户的强烈概念。用户在输入密码建立凭据后，登录以获取对系统资源的访问权限。然后，用户可以启动一个或多个进程，并对它们进行完全控制(pause, kill...)，用户通常只能控制自己的进程。操作系统的工作是将资源(cpu, mem, disk...)分配给每个用户(及其进程)以满足整体系统目标。



<br/>
<br/>



### 有用的工具

Useful Tools

有许多命令行工具也很有用。如下:

- `ps`
- `top`
- `sar`
- `kill`
- `killall`



<br/>
<br/>



### 摘要

Summary

我们介绍了一些处理Unix进程创建的API: `fork(), exec(), wait()`。但是，我们刚刚撇去了表面。

**ASIDE: KEY PROCESS API TERMS**

- 每个进程都有一个名字，在大多数系统中，该名称为**PID**；
- `fork()`系统调用在Unix系统中用于创建新进程。创建者被称为**父进程（parent）**，被创建的新进程被称为**子进程（child）**；
- `wait()`系统调用允许父进程等待其子进程完成执行；
- `exec()`系统调用允许子进程摆脱与父进程的相似性并执行一个全新的程序；
- Unix Shell通常使用`fork(), exec(), wait()`来启动用户命令。`fork()`和`exec()`的分离支持I/O重定向、管道...；
- 进程控制以信号的方式提供，这可能导致作业停止、继续或终止；
- 可由特定用户控制哪些进程被封装在用户的概念中。操作系统允许多个用户同时登录，并确保用户只能控制自己的进程；
- **超级用户(superuser)**可以控制所有进程。出于安全考虑，请不要使用此用户进行直接操作。




<br/>
<br/>
<br/>




## 直接执行

[Mechanism: Limited Direct Execution](http://pages.cs.wisc.edu/~remzi/OSTEP/cpu-mechanisms.pdf)


为了虚拟化CPU，操作系统需要以某种方式在同时运行的许多作业中共享物理CPU。基本思路很简单：运行一个进程一段时间，然后运行另一个进程，以此类推。通过**分时共享(time sharing)**CPU，实现了虚拟化。

然而，构建这样的虚拟化也存在一些挑战：

- 首先是**性能（Performance）**：如何在不增加系统过多开销的情况下实现虚拟化？
- 第二是**控制（Control）**：如何保持在对CPU的控制的同时有效地运行进程？控制对操作系统尤其重要，，因为它控制资源。如果没有控制权，进程就可以永远运行并接管机器，或者访问不应该被它访问的信息。

因此，要在保持控制的同时获得高性能是构建操作系统的核心挑战之一。

> 关键：**如何通过控制有效地虚拟化CPU**
> 操作系统必须以有效地方式虚拟化CPU，同时保持对系统的控制。为此，需要硬件和操作系统的支持。操作系统通常会使用明智的硬件支持来完成其工作。



<br/>
<br/>



### 基本技术：有限的直接执行

Basic Technique: Limited Direct Execution

为了使程序以预期的速度运行，操作系统开发人员提出了一种技术——**有限的直接执行（limited direct execution）**。**直接执行（direct execution）**的想法很简单：只需在CPU上直接运行程序即可。因此，当操作系统启动一个程序运行时，它会在进程列表中为它创建一个进程条目，为它分配一些内存，将程序代码加载到内存中，找到它的入口点(`main()`例程或类似的东西)，跳转到它，并开始运行用户的代码。

<br>

**Direct Execution Protocol (Without Limits)**

| OS | Program |
| - | - |
| 创建进程列表条目<br>为程序分配内存<br>将程序加载到内存<br>使用`argc/argv`设置堆栈<br>清除寄存器<br>执行调用`main()` | |
| | 运行`main()`<br>从main执行return |
| 释放进程内存<br>从进程列表中删除条目 |

听起来很简单，但这种方法在我们尝试虚拟化CPU的过程中会产生一些问题：

- 如果我们只运行一个程序，操作系统如何确保程序不执行我们不希望它执行的操作，同时仍然有效地运行它？
- 当运行一个程序时，操作系统如何阻止它运行并切换到另一个进程，从而实现我们虚拟化CPU所需的分时共享？



<br/>
<br/>



### 问题1：受限制的操作

Problem1: Restricted Operations


直接执行具有快速的明显优势，程序直接在原生CPU硬件上运行，因此可按照预期的速度执行。但是在CPU上运行会引发一个问题：如果进程希望执行某种受限制的操作（如向磁盘发出I/O请求，访问更多系统资源(cpu, kernel)...），该怎么办？

> **如何执行受限制的操作**
> 进程必须能够执行I/O或其它一些受限制的操作，但不能让进程完全控制系统。操作系统和硬件该如何协同工作？

<br>

> **为什么系统调用看起来像程序调用**
> 你可能想知道为什么对系统调用（如`open(), read()...`）的调用看起来与C中的典型过程调用（procedure call）完全相同。也就是说，它看起来就像一个过程调用，系统如何知道它是一个系统调用，并做了所有正确的事情？原因很简单：它是一个过程调用，但隐藏在过程调用内部的是著名的**陷阱指令（trap instruction）**。举个栗子，当调用`open()`时，你正在执行对C library的过程调用。其中，无论是对于`open()`还是其它的系统调用，库都使用与内核达成一致的调用约定，将参数放在众所周知的位置（stack或register），也将系统调用号放入一个众所周知的位置(stack或register)，然后执行上述陷阱指令。陷阱解压后库中的代码将返回值，并将控制权返回给发出系统调用的程序。因此，进行系统调用的C库部分是在汇编中手工编码的，因为它们需要仔细准遵循约定，以便正确处理参数和返回值，以及执行特定于硬件的陷阱指令。这个汇编代码已经有人替你做了。

<br>

一种方法是让任何进程在I/O和其它相关操作方面做任何它们想做的事情。然而，这样做会妨碍构建所需的多种操作系统。例如，如果我们希望构建一个在授予文件访问权限之前检查权限的文件系统，我们不能简单地让任何用户进程向磁盘发出I/O。如果这样做了，一个进程可以简单地读写整个磁盘，因此所有的保护都将丢失。

因此，我们采用一种新的处理器模式——**用户模式（user mode）**。在用户模式下运行的代码受限于它们可以执行的操作。例如，在用户模式下运行时，进程无法发出I/O请求，这样做了会导致处理器引发异常，操作系统可能会杀死这个进程。

与用户模式相反，**内核模式（kernel mode）**是操作系统运行的模式。在此模式下，运行的代码可以执行其喜欢的操作，包括发出I/O请求、执行所有类型的受限制的指令。

但是，当**用户进程（user process）**希望执行某种特权操作（如I/O）时应该做什么？为了实现这一点，几乎所有的现代硬件都为用户程序提供了执行系统调用的能力。系统调用允许内核小心地将某些关键功能部件暴露给用户程序。如访问文件系统、创建和销毁进程、与其它进程通信以及分配更多内存...大多数操作系统提供了几百个调用（详情请参考POSIX标准）。

<br>

> **使用受保护的控制转移**
> 硬件通过提供不同的执行模式来协助操作系统。
> 在用户模式下，应用程序无法完全访问硬件资源。
> 在内核模式下，操作系统可以访问机器的全部资源。
> 还提供了从陷阱（trap）到内核（kernel）并**从陷阱返回（return-from-trap）**到用户模式的特殊指令，以及允许操作系统告知硬件陷阱表（trap table）驻留在内存中的指令。

<br>

要执行系统调用，程序必须执行特殊的陷阱指令。该指令同时跳转到内核并将权限级别提升为内核模式。一旦进入内核，系统现在可以执行所需的任何特权操作（如果允许），从而为调用进程执行所需的工作。完成后，操作系统会调用一个特殊的**从陷阱返回（return-from-trap）**指令，该指令将返回到调用用户程序，同时将权限级别降低到用户模式。

执行陷阱(trap)时硬件需要小心，它必须确保保存足够的调用程序寄存器（caller's register），以便在操作系统发出`return-from-trap`指令时能够正确返回。例如，在x86上，处理器会将程序计数器（counter）、标志（flag）和一些其它寄存器（register）推送（push）到每个进程的**内核栈（kernel stack）**。`return-from-trap`会将这些值从栈中弹出（pop）并继续执行用户模式的程序。其它硬件系统可能有所不同，但基本概念在不同平台上是相同的。

还有一个重要细节：陷阱如何知道在操作系统中运行哪些代码？显然，调用进程无法指定要跳转的地址。这样做会让程序跳转到内核，这显然是一个非常糟糕的想法。因此内核必须消息控制在陷阱（trap）上执行的代码。

内核通过在启动时设置**陷阱表（trap table）**来实现。当机器启动时，它在特权（内核）模式下，因此可以根据需要自由配置机器硬件。操作系统首先要做的事情之一就是告诉硬件在发生某些异常事件时要运行什么代码。
例如，当发生硬盘中断、发生键盘中断或程序进行系统调用时，应该运行什么代码？操作系统通常通过某种特殊指令通知硬件这些**陷阱处理程序（trap handler）**的位置。一旦通知硬件，它会记住这些处理程序的位置，直到机器下次重启，因此当系统调用或其它异常事件发生时，硬件知道该做什么。

<br>

**Limited Direct Execution Protocol**

| os@boot | hardware |
| - | - |
| initialize trap table | |
| | remember address of... <br> syscall handler |

| os@run | hardware | program(user mode) |
| - | - | - |
| Create entry for process list<br>Allocate memory for program<br>Load program into memory<br>Setup user stack with argv<br>Fill kernel stack with reg/PC<br>return-from-trap | | |
| | restore regs(from kernel stack)<br>move to user mode<br>jump to main | |
| | | Run main()<br>...<br>Call system call<br>trap into OS |
| | save regs(to kernel stack)<br>move to kernel mode<br>jump to trap handler | |
| Handle trap<br>Do work of syscall<br>return-from-trap | | |
| | restore regs(from kernel stack)<br>move to user mode<br>jump to PC after trap | |
| | | ...<br>return from main<br>trap (via exit()) |
| Free memory of process<br>Remove from process list | | |

此时间线总结了协议。假设每个进程都有一个内核寄存器，其中寄存器在进入和退出内核时保存到硬件，并从硬件恢复。

<br>

> **警惕安全系统中的用户输入**
> 即使我们在系统调用期间都非常努力地保护操作系统（通过添加硬件陷阱机制），但实现安全操作系统还有许多其它方面必须考虑。其中之一是在系统调用边界梳理参数，操作系统必须检查用户传入的内容并确保正确指定了参数，否则拒绝该调用。例如，通过`write()`调用，用户将缓冲区的地址指定为write调用的源。如果用户(意外或恶意)传入坏地址（如，内核地址空间的一部分），操作系统必须检测到这一点并拒绝该调用。否则，用户可以读取所有内核内存。鉴于kernel（virtual）内存通常还包括其它的所有物理内存，这个小的滑动将使程序能够读取系统中的任何其它进程的内存，这是非常危险的。
> 通常，安全系统必须非常怀疑地（great suspicion）处理用户输入。不这样做容易导致软件被黑，世界是一个不安全和可怕的地方。

<br>

要指定确切地系统调用，通常会为每个系统调用分配**系统调用号（system call number）**。因此，用户代码负责将所有所需的系统调用号放在寄存器或栈上的特定位置。操作系统在陷阱处理程序内部处理系统调用时，检查此号码，确保它有效。如果有效，则执行相应的代码。这种间接性是一种保护形式，用户代码无法指定要跳转确切地址，而是必须通过号码请求特定服务。

能够执行指令告诉硬件陷阱表所在的位置是一个非常强大的功能。因此，如你所猜，它也是一种特权操作（ privileged operation）。如果你在用户模式下执行此指令，硬件不会鸟你。
如果你可以安装自己的陷阱表，你可以对系统做些什么可怕的事情？你能接管机器吗？

<br>

有限的直接执行（LDE）有两个阶段：

- 启动时，内核初始化陷阱表，CPU会记住它的位置以供后续使用；
- 内核通过特权指令执行此操作。
内核在使用`return-from-trap`指令开始执行进程之前设置了一些东西（分配进程列表、内存...）。这会将CPU切换到用户模式并开始运行此进程。当进程希望发出系统调用时，操作系统处理进程并再次通过`return-from-trap`将控制权返回给进程。然后进程完成其工作，并从`main()`返回。它通常会返回存根代码，它将正确地退出程序。此时操作系统清理完毕，就完成了。



<br/>
<br/>



### 问题2：在进程间切换

Problem2: Switching Between Processes


直接执行的下一个问题是实现**进程之间的切换（switch between process）**。进程之间的切换很简单吗？操作系统应该决定停止一个进程并启动另一个进程。这看起来简单，但实际上有点棘手。具体来说，如果一个进程在CPU上运行，这意味着操作系统没有运行。如果操作系统没有运行，它怎么能做任何事情？吐过操作系统没有在CPU上运行，它显然没有办法采取行动。

> **如何恢复控制CPU**
> 操作系统如何重新获得对CPU的控制，以便它可在进程间切换？


<br/>
<br/>


#### 合作方法：等待系统调用

A Cooperative Approach: Wait For System Calls

一些系统过去采用一种**合作方法（cooperative approach）**。在这种风格中，操作系统信任系统的进程以合理地运行。假定运行时间过长的进程会定期放弃CPU，以便操作系统可以决定运行其它任务。

因此，你可能会问，友好的进程如何在这个乌托邦世界中放弃CPU？事实证明，大多数进程通过进行系统调用来非常频繁地将CPU的控制权转移到操作系统。像这样的系统通常包括一个显式的`yield`系统调用，除了将控制权转移到操作系统（以便操作系统可以运行其进程）之外什么都不做。

应用程序在执行非法操作时也会将控制权转移到操作系统。举个栗子，如果应用程序除以零，或者尝试访问它无法访问的内存，则会为操作系统生成陷阱（trap）。然后操作系统再次获得CPU控制权（并可能终止非法进程）。

因此，在协同调度（cooperative scheduling）系统中，操作系统通过等待系统调用或某种非法操作来重新获得CPU的控制权。你可能回想，这种被动方法也不理想呀！如果一个进程（恶意或错误）最终在无限循环中结束，并且从不进行系统调用，会发生什么？操作系统可以做什么？


<br/>
<br/>


#### 非合作方法：操作系统取得控制权

A Non-Cooperative Approach: The OS Takes Control

如果没有硬件的额外帮助，当一个进程拒绝进行系统调用并因此将控制权返回给操作系统时，操作系统根本无法做很多事情。事实上，在合作方法中，当一个进程陷入无限循环时，你唯一的办法就是采用古老的办法解决计算机系统中的所有问题：重启（Reboot）。因此，我们再次提出了获得CPU控制权的一个子问题。

<br>

> **如何在没有合作的情况下获得控制权（HOW TO GAIN CONTROL WITHOUT COOPERATION）**
> 即使进程没有合作，操作系统如何才能获得对CPU的控制？操作系统可以做些什么确保流氓进程不会接管机器？

<br>

答案很简单，许多人在许多年前构建操作系统时已经发现了：**定时器终端（timer interrupt）**。可以对定时器设备进行编程，以便每隔几毫秒(ms)产生一次中断。当中断被引发时，当前正在运行的进程停止（halted），并且操作系统中预配置的中断处理程序运行。此时，操作系统重新获得CPU的控制权。因此可以随心所欲：停止当前进程并启动另一个进程。

如前面讨论的那样，系统调用时，操作系统必须通知硬件当中断定时器发生时执行什么代码。因此，在启动时，操作系统就是这样做的。其次，在引导序列期间，操作系统必须启动定时器（这当然是特权操作）。一旦计时器开始，操作系统就可以感觉安全，因为控制权最终将返回给它，因此操作系统可以自由运行用户程序。

<br>

> **处理应用程序的坏事（DEALING WITH APPLICATION MISBEHAVIOR）**
> 操作系统通常必须处理行为不当的进程，这些进程（恶意或错误）尝试做它们不应该做的事情。在现代操作系统中，操作系统处理此类不当行为的方式是简单地终止（terminate）违法者。但当你试图非法访问内存或执行非法指令时，操作系统应该做什么呢？

<br>

请注意，当发生中断时硬件有一定的责任，特别是为了保存中断发生时运行的程序的足够的状态，以便后续的`return-from-trap`指令能够正确地恢复正在运行的程序。这组操作非常类似与在显式系统调用陷阱到内核期间硬件的行为，因此各种寄存器被保存，因此可通过`return-from-trap`轻松恢复。


<br/>
<br/>


#### 保存和恢复上下文

Saving and Restoring Context

现在操作系统已经重新获得了控制权，无论是通过系统调用，还是通过定时器中断，都必须做出决定——是继续运行当前进程，还是切换到另一个进程。该决定由称为**调度程序（scheduler）**的操作系统的一部分做出，这将在后面学习。

如果决定做切换，则操作系统执行低级代码。我们称之为**上下文切换（context switch）**。上下文切换的概念很简单：所有操作系统必须做的是为当前正在执行的进程保存一些寄存器值（如，在其内核栈上），并为即将执行的进程恢复（如，来自其内核栈）。通过这样做，操作系统因此确保当最终执行`return-from-trap`指令时，系统继续执行另一个进程，而不是返回到正在运行的进程。

为了保存当前正在运行的进程的上下文，操作系统将执行一些低级汇编代码（low-level assembly code），以保存和运行当前正在运行的进程的通用寄存器、PC、内核栈指针，然后恢复所述寄存器、PC，并切换到内核堆栈，以便于即将执行的进程。通过切换栈，内核在一个进程（被中断的进程）的上下文中进行切换代码的调用，并在另一个进程（将被执行的进程）的上下文中返回。操作系统最终执行`return-from-trap`指令，即将执行的进程将成为当前正在运行的进程。因此上下文切换完成。

<br>

> **使用定时器中断来重新获得控制权**
> 定时器中断使操作系统能够在CPU上再次运行，即使进程以非协作方式运行。因此，此硬件功能对于帮助操作系统维护机器的控制至关重要。

<br>

> **重启是有用的**
> 早些时候，我们注意到在协作下抢占无限循环（infinite loops）的唯一解决办法是重启机器。虽然你可能会嘲笑，但研究人员已经证明重启可以成为构建健壮系统的一个非常有用的工具。
> 具体来时，重启是有用的。因为它将软件移回到已知且更加可测试的状态。重启还会回收陈旧或泄露的资源（如，memory），否则这些资源可能难以处理。最后，重启很容易实现自动化。

<br>

整个进程的时间线如下所示。在此示例中，进程A正在运行，然后被定时器中断所中断。硬件保存其寄存器（在其内核栈上）并进入内核（切换到内核模式）。在定时器中断处理程序中，操作系统决定从正在运行的进程A切换到进程B。此时，它调用`switch()`例程，该例程小心地保存当前寄存器值（进入A的进程结构），恢复进程B的寄存器（来自其进程结构条目），然后切换上下文，特别是通过更改栈指针来使用B的内核栈（而不是A的）。最后，操作系统执行`return-from-trap`，它恢复B的寄存器并开始运行它。


**: Limited Direct Execution Protocol (Timer Interrupt)**

| os@boot<br>kernel mode | hardware |
| - | - |
| initialize trap table | |
| | remember addresses of...<br>syscall handler<br>timer handler |
| start interrupt timer | |
| | start timer<br>interrupt CPU in X ms |

| os@boot<br>kernel mode | hardware | program<br> user mode |
| - | - | - |
| | | Process A<br>... |
| | timer interrupt<br>save regs(A) → k-stack(A)<br>move to kernel mode<br>jump to trap handler | |
| Handle the trap<br>Call `switch()` routine<br>save regs(A) → proc t(A)<br>restore regs(B) ← proc t(B)<br>switch to k-stack(B) | | |
| return-from-trap (into B) | | |
| | restore regs(B) ← k-stack(B)<br>move to user mode<br>jump to B’s PC | |
| | | Process B<br>... |



<br/>
<br/>



### 担心并发？

Worried About Concurrency?


细心的读者可能会想到：在系统调用期间发生定时器中断，会发生什么？或，当你在处理一个中断时而另一个中断发生会发生什么？在内核中难处理吗？......

操作系统确实需要关注在中断或陷阱处理期间发生其它中断会发生什么。事实上，这是本书后面关于**并发（concurrency）**的内容。为了满足读者的胃口，这里介绍操作系统如何处理这些棘手情况的一些基础知识。

操作系统可能做的一件简单的事情，在中断处理期间**禁用中断(disable interrupts)**。这样做可确保在处理一个中断时，不会将其它任何中断传递到CPU。当然，操作系统必须小心这样做，长时间禁用可能会导致中断丢失，这是不好的。

操作系统还开发了许多复杂的**锁定（locking）**方案，以保护对内部数据结构的并发访问。这使得许多活动可以同时在内核中进行，特别适用于多处理器（multiprocessors）。这种锁定可能很复杂，并导致各种有趣且难以发现的错误(bugs)。



<br/>
<br/>



### 摘要

我们描述了一些实现CPU虚拟化的关键低级机制，这是一组我们统称为**有限直接执行（limited direct execution）**的技术。基本思路很简单：只需运行你想在CPU上运行的程序，但首先要确保设置硬件以便在没有操作系统辅助的情况下限制进程可以执行的操作。

我们具有虚拟化CPU的基本机制。但是一个主要问题没有回答：我们应该在给定时间运行哪个进程？**调度器**必须回答这个问题，这是后面讨论的问题。

<br>

> **上下文切换会花费多长时间**
> 你可能回想：上下文切换需要多长时间？或系统调用？有一些工作可以准确测量这些东西，以及一些其它可能的指标。
> 这当然也和硬件配置有关系。应当注意，并非所有操作系统都追踪CPU性能。许多操作系统是内存密集型的，并且内存带宽并没有像处理器速度那样显著提高。所以，购买强大的硬件配置能加速你的操作系统。

<br>

**CPU虚拟化术语**

- CPU至少支持两种执行模式：**受限的用户模式**和**特权内核模式（非受限）**
- 典型的用户应用程序以用户模式运行，并使用系统调用来陷阱(trap)到内核中以请求操作系统服务
- 陷阱指令小心保存寄存器状态，将硬件状态更改为内核模式，并跳转到操作系统到预先指定的目标：**陷阱表（trap table）**
- 当操作系统完成对系统调用的服务时，它会通过另一个特殊的`return-from-trap`指令返回到用户程序，这会降低权限并在跳转到操作系统的陷阱后将控制权返回给指令
- 操作系统必须在引导（boot）时设置陷阱表，并确保用户程序无法轻松修改它们。所有这些都是有限直接执行协议的一部分，改写以有效地运行程序但不会丢失操作系统控制
- 程序运行后，操作系统必须使用**硬件机制（定时器中断）**来确保用户程序不会永远运行。这种方法是CPU调度的非协作方法
- 有时，在定时器中断或系统调用期间，操作系统可能希望从运行当前进程切换到另一个进程，这是一种被称为**上下文切换（context switch）**的低级技术




<br/>
<br/>
<br/>




## 调度

[CPU Scheduling](http://pages.cs.wisc.edu/~remzi/OSTEP/cpu-sched.pdf)


到现在为止，运行进程的低级机制（上下文切换）应该是清楚的。但是，我们尚未了解操作系统调度程序使用的高级策略。
事实上，调度的起源早于计算机系统。早期的方法来自运营管理领域并应用于计算机。

<br>

> **如何制定调度策略（SCHEDULING POLICY）**
> 如何开发一个思考型调度策略的基本框架？关键假设是什么？哪些指标很重要？在最早的计算机系统中使用了哪些方法？


<br/>
<br/>


### 工作负载假设

Workload Assumptions


在介绍可能的策略范围之前，让我们首先对系统中运行的进程做一些简化的假设，有时统称为**工作负载（workload）**。确定工作负载是构建策略的关键部分，对工作负载了解的越多，你的策略就越精细。

我们将对系统中的进程（有时称为作业(jobs)）做出以下假设：

- 每个作业运行相同的时间
- 所有作业都在同一时间完成
- 一旦启动，每个作业都会运行完成
- 所有作业仅使用CPU
- 每个作业的运行时间都是已知的

这些假设很多是不现实的，正如乔治奥威尔《动物农场》中的一些动物比其它动物更平等，本章的一些假设比其它假设更不切实际。特别是，每个作业的运行时间都是已知的。这样做使调度程序无所不知。



<br/>
<br/>



### 调度指标

Scheduling Metrics


除了进行工作负载假设之外，还需要一件事来使我们能够比较不同的调度策略：**调度指标（Scheduling Metrics）**。指标用来衡量某些事物，不同的指标在调度中也有不同的意义。
但是，就目前而言。我们来看一个简单的指标：**周转时间（turnaround time）**。作业的周转时间定义为作业完成时间减去作业到达系统的时间：

$$T_{turnaround} = T_{completion} − T_{arrival}$$

应该注意，周转时间是一个性能指标。这将是本章的主要关注点。另一个有趣的指标是**公平性（fairness）**。在调度方面，性能和公平性往往不一致，这也告诉我们生活并不总是完美的。



<br/>
<br/>



### 先进先出

First In, First Out (FIFO)


一个最基本的算法为**先进先出（First In First Out (FIFO)）**调度。它具有许多积极的属性，简单且易于实现。并且根据假设，它运作良好。

让我们做一个快速的栗子。想象一下，有三个作业A, B, C在大致相同的时间到达系统（$$T_{arrival}=0$$）。由于先进先出必须放置一些工作，让我们假设A-B-C的顺序，假设每个作业运行10s。这些作业的平均周转时间是多少？

![](/images/OSTEP/fifo71.png)

如图，A在10时完成，B在20时完成，C在30时完成。因此，三个作业的平均周转时间仅为$$\frac{10+20+30}{3}=30$$。

<br>

让我们举个栗子来说明不同长度的作业如何导致先进先出调度出现问题。特别是，假设A运行100s,B和C还是10s。

![](/images/OSTEP/fifo72.png)

如图所示，在B或C有机会运行之前，作业A首先整整运行100s。因此，系统的平均周转时间很长：$$\frac{100+110+120}{3}=110$$，痛苦的110s。
这个问题通常被称为**车队效应（convoy effect）**，其中资源的一些小型消费者排在重量级消费者后面。那该怎么办？我们如何开发一种更好的算法来处理？



<br/>
<br/>



### 最短作业优先

Shortest Job First (SJF)


> **最短作业优先的原则**
> 最短作业优先表示可用于任何系统的一般调度原则，其中每个作业的感知周转时间很重要。如果有关机构关心客户满意度的话，很可能他们已经考虑使用最短作业优先。

<br>

**最短作业优先（Shortest Job First(SJF)）**，它首先运行最短的作业，然后是下一个最短的作业，依此类推。

![](/images/OSTEP/sjf73.png)

如图，该图表明了最短作业优先在平均周转时间方面的表现要好得多。它将之前的平均周转时间从110s减少到50s（$$\frac{10+20+120}{3}=50$$），这是极大的改善。
事实上，鉴于我们对所有作业的假设都是同时到达，所以证明它是最优调度算法。但我们的假设相当不切实际。

<br>

这里再举个例子。假设A在`t=0`时到达并且需要运行100s，而B和C在`t=10`时到达并且每个需要运行10s。

![](/images/OSTEP/sjf74.png)

如图，即使B和C在A之后不久到达，他们仍然被迫等到A完成，因此遭遇了同样的车队问题。平均周转时间为103.33s（$$\frac{100+(110-10)+(120-10)}{3}$$）。调度程序能做什么？

<br>

> **预备调度器**
> 事实上，所有现代调度程序都是先发制人，并且非常愿意停止一个运行的进程以运行其它进程。这意味着调度程序采用我们之前学习的机制。特别是，调度程序可以进行上下文切换，暂时停止一个正在运行的进程并恢复另一个进程。



<br/>
<br/>



### 最短完成时间优先

Shortest Time-to-Completion First (STCF)


我们还需要调度程序本身内的一些机制。鉴于前面关于计时器中断和上下文切换的讨论，调度程序当然可以在B和C到达时执行其它操作：它可以抢占作业A并决定运行另一个作业，可能会在执行继续作业A。最短作业优先是**非抢先式（non-preemptive）**调度程序，因此会遇到上述问题。
幸运的是，有一个调度程序正是这样做：向最短作业优先添加抢占，称为**最短完成时间优先（Shortest Time-to-Completion First (STCF)）**，或**抢先最短作业优先（Preemptive Shortest Job First (PSJF)）**调度程序。每当新作业进入系统时，最短完成时间优先调度程序就会确定剩余作业（包括新作业）中的哪一个剩余时间最少，并安排该作业。

![](/images/OSTEP/stcf75.png)

如图，最短完成时间优先将抢占作业A并运行作业B和作业C以完成。只有当它们完成时才会安排作业A的剩余时间。
这会大大改善平均周转时间：$$\frac{(120-0)+(20-10)+(30-10)}{3}=50$$。根据假设，可证明最短完成时间优先是最优。但假设相当不切实际。



<br/>
<br/>



### 响应时间指标

A New Metric: Response Time

如果我们知道工作长度，并且工作只使用了CPU，并且我们唯一的指标是周转时间。那个STCF将是一个很好的策略。实际上，对于早期的批处理计算系统，这些类型的算法有一定意义。然而，分时（shared time）机器的引入改变了这一切。现在，用户将坐在终端上并要求系统提供交互式性能。因此，一个新的指标诞生了：**响应时间（response time）**。

响应时间：$$T_{response=T_{firstrun}-T_{arrival}}$$

例如，作业A在0时到达，作业B和作业C在10时到达。则每个作业的相应时间如下：A(0-0)，B(10-10)，C(20-10)，平均值(3.33)。

正如你可能认为那样，STCF和相关方法对响应时间并不是特别好。如果三个作业同时到达，则第三个作业必须等待前两个作业完全运行才能安排一次。虽然周转时间很好，但这种方法对于响应时间和交互性来说非常糟糕。事实上，想象一下坐在终端前，打字输入，并且不得不等待10s才能看到系统的响应，因为其它工作已安排在你前面：非常不爽。

因此，我们还有另外一个问题：如果构建一个对响应时间敏感的调度程序？



<br/>
<br/>



### 轮询

Round Robin


为了解决这个问题，将引入一种新的调度算法，通常称为**轮询调度（RR, Round Robin）**。基本思路很简单：轮询不是运行作业完成，而是运行**时间切片（time slice）**作业，然后切换到运行队列中的下一个作业。它重复这样做，知道工作完成。因此，轮询有时被称为时间切片（ time-slicing）。注意，时间片的长度必须是定时器中断周期的倍数。例如，如果定时器中断每10ms中断一次，则时间片可以是10ms, 20ms, 10Nms。

<br>

为了更详细的了解轮询，让我们来看一个栗子。假设有三个作业A, B, C在系统中同时到达，并且每个作业都希望运行5s。最短作业优先在运行另一个作业之前运行每个作业（图7.6），相比之下，时间切片为1s的轮询将快速循环作业（图7.7）。

![](/images/OSTEP/sjf76.png)

![](/images/OSTEP/rr77.png)

平均响应时间：

- 轮询（RR）：$$\frac{0+1+2}{3}=1$$
- 最短作业优先（SJF）：$$\frac{0+5+10}{3}=5$$

如你所见，时间片的长度对轮询至关重要。它越短，响应时间的指标度量下轮询的性能越好。然而，使时间片太短是有问题的：上下文切换的成本将主导整体的性能。因此，决定时间片的长度给系统设计者带来了折中，使其足够长以**分摊（amortize）**切换成本不会使系统不再响应。

<br>

> **分摊可以降低成本（ AMORTIZATION CAN REDUCE COSTS）**
> 当某些操作存在固定成本时，一般的分摊技术通常用于系统中。通过较少地产生该成本，降低了系统的总成本。例如，如果时间片设置为10ms，并且上下文切换成本为1ms，则大约10%的时间用于上下文切换，浪费了。如果我们想分摊此成本，我们可以增加时间片（如100ms）。在这种情况下，上下文切换花费的时间少于1%，因此时间切片的成本已经分摊。

<br>

请注意，上下文切换的成本不仅仅来自于操作系统保存和恢复一些寄存器的操作。程序运行时，它们在CPU Cache、TLBs、Branch Predictors和其它分片上构建了大量状态(state)。切换到另一个作业会导致刷新(flush)此状态，并且将引入与当前正在运行的作业相关的新状态，这可能导致显著的性能成本。

因此，如果响应时间是我们的唯一指标，那么具有合理时间片的轮询将是一个出色的调度程序。但我们的老朋友周转时间呢？来看个栗子。A、B、C各自需要5s运行时间，它们同时到达，并且轮询的时间片为1s。从上面轮序的运行图可看出，A在13完成，B在14完成，C在15完成，平均时间为14。

如果周转时间使我们的指标，则轮序是最糟的策略之一。轮序正在做的是延长每个作业，只要它可以，只需在移动到下一个作业之前运行每个作业一小段。由于周转时间紧关注作业何时完成，因此在很多情况下，轮询几乎是悲观的，甚至比简单的先进先出更差。

任何公平的策略（如RR），即在小时间范围内在活跃进程之间均匀划分CPU，将在如周转时间的指标上表现不佳。实际上，这是一种固有的权衡：如果你愿意不公平，你可以完成更短的工作，按时以响应时间为代价；如果你更重视公平，那么响应时间会降低，但会以周转时间为代价。这种权衡(trade-off)在系统中很常见。you can’t have your cake and eat it too.

我们介绍了两种类型的调度程序，当然这些都是基于假设下：

- SJF, STCF优化了周转时间，但对响应时间不利；
- RR优化了响应时间，但对周转时间不利；



<br/>
<br/>



### 合并I/O

Incorporating I/O


> **重叠使得更高的使用率(OVERLAP ENABLES HIGHER UTILIZATION)**
> 如果可能，重叠(overlap)操作以最大化系统的利用率。重叠在许多不同的域中都很有用，包括执行磁盘I/O或向远程计算机发送消息时。在任何一种情况下，启动操作然和切换到其它作业是一个好主意，并提高系统的整体利用率和效率。

<br>

放松假设4，假设所有程序都执行I/O。想象一个没有任何输入的程序，它每次会产生相同的输出。

当作业启动I/O请求时，调度程序明会做一个明确地决定。因为当前正在运行的作业在I/O期间不会使用CPU，它被阻止(blocked)以等待I/O完成。如果将I/O发送到磁盘驱动器，则该进程可能会被阻塞几毫秒或更长时间，具体取决于驱动器当前的I/O负载。因此，调度程序应该可能在那时在CPU上安排另一个作业。

调度程序还必须在I/O完成时做出决定。发生这种情况时，会引发中断，并且操作系统会运行并将发出I/O请求的进程从阻塞状态(blocked back)移回就绪状态(ready state)。当然，它甚至可以决定在那时开展工作。操作系统应如何处理每个工作？

为了更好地理解这个问题，让我们假设有两个作业A和B，每个作业需要50ms的CPU时间。但有一个明显的区别：A运行10ms然后发出I/O请求（假设也许10ms），而B只使用CPU 50ms并且不执行I/O。调度程序首先运行A，然后运行B。

![](/images/OSTEP/incorporatingIO78.png)

<br>

假设正在尝试构建STCF调度程序。显然，只运行一个工作然后运行另一个工作而不考虑I/O是没有意义的。

一种常见的方法是将A的每个10ms子作业视为独立工作。因此，当系统启动时，它的选择是是否安排10ms A或50ms B。使用STCF是明确的。当A的第一个子作业完成时，只剩下B，它开始运行。接着提交一个A的新子作业，它会抢占B并运行10ms。这样做允许重叠，一个进程在等待另一个进程的I/O完成时使用CPU，这样可以更好地利用该系统。

![](/images/OSTEP/incorporatingIO79.png)

<br>

因此，我们看到调度程序如何合并I/O。通过将每个CPU突发视为作业，调度程序可确保**交互(interactive)**的进程进程运行。当这些交互式作业执行I/O时，其它CPU密集型作业会运行，从而更好地利用处理器。



<br/>
<br/>



### 摘要


我们前面假设知道每个作业的长度，这可能是最糟糕的假设。实际上，在通用操作系统中，操作系统对每项工作的长度知之甚少。

我们介绍了调度背后的基本思想，并开发了两类方法。第一个运行剩余的最短作业，从而优化周转时间；第二个在所有作业之间交替运行，从而优化响应时间。两者都有好有坏，在系统中需要一个权衡。我们还看到了如何将I/O合并到调度中，但仍然没有解决操作系统基本无法看到未来的问题。
不久我们将通过构建一个使用最近过去预测未来的调度程序，来了解如何克服这个问题。此调度程序称为**多级反馈队列(multi-level feedback queue)**，它是下一章的主题。



<br/>
<br/>
<br/>



## 多级反馈队列

[Scheduling: The Multi-Level Feedback Queue](http://pages.cs.wisc.edu/~remzi/OSTEP/cpu-sched-mlfq.pdf)


在本章中，我们将解决一种最著名的调度算法问题，称为**多级反馈队列(MLFQ, Multi-level Feedback Queue)**。多级反馈队列调度程序获得了图灵奖(Turing Award)。随后，调度程序经过多年的改进，完成了现代操作系统中的一些实现。

多级反馈队列试图解决的根本问题是双重的：

- 首先，它希望优化周转时间(turnaround time)。这是通过先运行较短的作业来完成。不幸的是，操作系统通常不知道作业运行的时间长短，这这是SJF, STCF等算法所需要的；
- 其次，它希望系统能够对交互式用户的敏感响应，从而最大限度地缩短响应时间。不幸的是，像RR这样的算法会缩短响应时间，但对于周转时间来说却很糟糕。

因此，问题是：鉴于我们通过对进行一无所知，如何构建调度程序来实现这些目标？在系统运行时，调度程序如何了解正在运行的作业的特征，从而做出更好的调度决策？

<br>

> **如何在没有完美知识的情况下安排调度？**
> 如何设计一个调度程序，既可以最大限度地缩短交互式作业的响应时间，又可以在不事先了解作业长度的情况下做大限度地缩短周转时间？
> <br>
> 多级反馈队列是从学习过去预测未来的系统的一个很好的栗子。这些方法在操作系统中很常见。当工具具有行为阶段并且因此可预测时，这种方法起作用。当然，必须小心使用这些技术，因为它们很容易出错，并且驱使系统做出比没有任何知识的情况更糟糕的决策。



<br/>
<br/>



### 基本规则

Basic Rule


为了构建这样的调度程序(scheduler)，在本章中我们将描述多级反馈队列背后的基本算法。虽然许多实施的MLFQ的细节不同，但大多数方法都是相似的。

在我们的处理中，MLFQ有许多不同的队列(queue)，每个队列分配不同的优先级(priority level)。在任何给定时间，准备运行的作业都在单个队列中。MLFQ使用优先级来决定在给定时间应该运行哪个作业：选择具有较高优先级的作业来运行。

当然，在给定队列上可能有多个作业，因此具有相同的优先级。在这种情况下，我们将在这些作业中使用轮询调度(round-robin scheduling)。
因此，我们得出了MLFQ的前两个基本规则：

- Rule 1: If `Priority(A) > Priority(B)`, A runs (B doesn’t)；
- Rule 2: If `Priority(A) = Priority(B`), A and B run in RR。

<br>

因此，MLFQ调度的关键在于调度程序如何设置优先级。MLFQ不是为每个作业提供固定的优先级，而是根据其观察到的行为(observed behavior)改变(varies)作业的优先级。
例如，如果作业在等待键盘输入是反复放弃CPU，MLFQ将保持其高优先级，因为这是交互式进程的行为方式。相反，如果作业长时间集中使用CPU，MLFQ将降低其优先级。通过这种方式，MLFQ将尝试在进程运行时了解进程，从而使用作业历史(history)来预测其未来(future)行为。

一个特定时刻可能的队列可能如下图这样。在此图中，作业(A和B)处于最高优先级，作业C处于中间，作业D处于最低优先级。
当然，只显示某些队列的静态快照并不能真正让你了解MLFQ的工作原理。我们需要的是了解作业优先级如何随时间变化。

![](/images/OSTEP/mlfq81.png)



<br/>
<br/>



### 如何改变优先级

How To Change Priority


我们现在必须决定MLFQ如何在作业的生命周期内更改作业的优先级。要做到这一点，我们必须牢记我们的工作负载：**short-running**（可能经常放弃CPU）的交互式作业的混合，以及一些需要大量CPU时间的**longer-running**的**CPU-bound**作业，但响应时间不重要。这将是我们首次尝试优先级调整算法：

- Rule 3： When a job enters the system, it is placed at the highest priority (the topmost queue).
- Rule 4a： If a job uses up an entire time slice while running, its priority is reduced (i.e., it moves down one queue).
- Rule 4b： If a job gives up the CPU before the time slice is up, it stays at the same priority level.


<br/>


#### 长作业栗子

Example 1: A Single Long-Running Job

让我们看一个简单的栗子。首先我们来看一下当系统中存在长时间运行的作业时会发生什么。

![](/images/OSTEP/mlfq-82.png)

如图所示，作业以最高优先级(Q2)进入。在10ms的单个时间片后，调度程序将作业的优先级降低1，因此作业位于Q1上。在Q1运行一段时间后，作业最终降低到系统中最低优先级(Q0)，并保留。


<br/>
<br/>


#### 短作业栗子

Example 2: Along Came A Short Job

让我们看一个复杂的栗子，看看MLFQ如何尝试接近近似SJF。在这个栗子中，有两个作业： A（长时间运行的CPU密集型作业）和B（短时间运行的交互式作业）。假设A已运行一段时间，然后B到达。会发生什么？MLFQ是否会接近B的SJF？

A（黑色）在最低优先级队列中运行；B（灰色）在`T=100`到达，因此被插入最高队列；因为它的运行时间很短(20ms)，所以B在到达底部队列之前完成，在两个时间片中；然后A恢复运行(低优先级)。

从这个栗子中，你可了解该算法的主要目标：因为它不知道工作是短期还是长期，所以它首先假设它可能是一项短期作业，从而使工作成为高优先级。如果它确实是一个短期作业，它将快速完成；如果它不是，它将慢慢向下排队，因此很快证明自己是一个长期运行的批处理进程。以这种方式，MLFQ近似于SJF。

![](/images/OSTEP/mlfq-83.png)


<br/>
<br/>


#### I/O的栗子

Example 3: What About I/O?

让我们看一些I/O的栗子。如果交互式作业正在执行大量I/O，它将在时间片完成前放弃CPU。在这种情况下，我们不希望惩罚工作，因此只是将其保持在同一水平。

图示显示了一个示例，其中交互式作业B在执行与长时间运行的批处理作业A的CPU的I/O竞争之前仅需要CPU 1ms。MLFQ方法将B保持在最高优先级，因此B不断释放CPU；如果B是一个交互式作业，MLFQ进一步实现快速运行交互式工作的目标。

![](/images/OSTEP/mlfq-84.png)



<br/>
<br/>



### 优先级提升

The Priority Boost

我们可以做些什么来保证CPU绑定的工作会取得一些进展。
这里的简单想法是定期提高系统中所有作业的优先级。有很多方法可以实现这一点，但我们做一些简单的事情：将它们全部放在最顶层的队列中。因此，一条新规则：

- Rule 5： After some time period S, move all the jobs in the system to the topmost queue.

新规则同时解决了两个问题：保证进程不会挨饿(starve)，通过在最顶层队列中，作业将以循环方式与其它高优先级作业共享CPU，从而获得服务；其次，如果CPU绑定的作业已成为交互式，则调度程序在收到优先级提升后会对其进行正确处理。

![](/images/OSTEP/mlfq-85.png)

来看个栗子，在这种情况下，我们只是在与两个短时间运行的交互式作业竞争CPU时显示长时间运行作业的行为。在左边没有优先级提升，因此一旦两个短作业到来，长作业就会挨饿。在右边，每50ms有一个优先级提升，因此我们至少保证长作业取得一些进展，得到提升到每50ms最高优先级，因此定期运行。



<br/>
<br/>



### 更好的计算

Better Accounting










<br/>
<br/>
<br/>



## 彩票调度

[Lottery Scheduling](http://pages.cs.wisc.edu/~remzi/OSTEP/cpu-sched-lottery.pdf)


在本章中，我们将研究一种称为**proportional-share**调度器，有时也称为**fair share**调度器。比例共享基于一个简单的概念：调度程序可能会尝试保证每个作业获得一定比例的CPU时间，而不是优化周转时间或响应时间。

**彩票调度(lottery scheduler)**是共享比例调度一个很好的早期栗子。基本想法非常简单：经常抽奖，以确定下一步应该运行哪一个进程。应该更频繁地运行的进程应该有更多机会赢得彩票，不是吗？

<br>

> 如何按比例共享CPU？
> 如何设计调度程序以按比例的方式共享CPU？这样的关键机制是什么？它们的效果如何？


<br/>
<br/>


### 票代表你的份额

Tickets Represent Your Share


抽奖调度是一个非常基本的概念：**tickets**，用于表示进程应该接收的资源的份额。进程所拥有的票百分比代表其所涉系统资源的份额。

来看个栗子。两个进程A（75票）和B（25票）。因此，我们想要的是A接收`75%`的CPU而B接收剩余的`25%`。

彩票调度每隔一段时间持有彩票来概率地实现这一点。持有彩票很简单：调度程序必须知道有多少总票数。调度程序从这里面选择一张中奖票。

<br>

> 使用票代表份额
> 彩票调度设计中最强大的机制之一就是票。在这些示例中，票用于表示进程的CPU份额，但可更广泛地应用。例如，在最近关于虚拟机程序的虚拟内存管理的作业中，展示如何使用票来表示操作系统的内存份额。因此，如果你需要一种机制来代表一定比例的所有权，这个概念可能就是票。



<br/>
<br/>



### 票机制

Ticket Mechanisms

彩票调度还提供了许多以不同且有时有用的方式操作票的机制。一种方式就是**票货币(ticket currency)**的概念。货币允许拥有一组票的用户在他们自己的工作中以他们想要的任何货币分配票，然后系统自动将所述货币转换为正确的全球价值。

例如，用户A和B每人都获得100张票。A正在运行两个作业A1和A2，并以A的货币向他们提供各500张票(共1000张)。用户B只运行一个作业并给它10张票(共10张)。

```
User A -> 500 (A’s currency) to A1 -> 50 (global currency)
-> 500 (A’s currency) to A2 -> 50 (global currency)
User B -> 10 (B’s currency) to B1 -> 100 (global currency)
```

其它拥有的机制是**票务转移(ticket transfer)**。通过转移，一个进程可暂时将其票交给另一个进程。







<br/>
<br/>
<br/>




## 多CPU调度

Multi-CPU Scheduling


本章将会介绍**多处理器调度(multiprocessor scheduling)**的基础知识。由于此主题相对较高级，因此在你详细研究并发主题后，最好先介绍它。 多核处理器(multicore processor)（多个CPU核心被打包到单个芯片上）。

当然，随着多个CPU的到来，会出现很多困难。主要的一个典型是应用程序只使用一个CPU，添加更多CPU并不能使单个应用程序运行得更快。要解决此问题，你必须重新编写应用程序以**并行(parallel)**运行或使用**线程(threads)**。多线程应用程序可以在多个CPU之间传播工作，因此在获得更多CPU资源时运行速度更快。

除了应用程序之外，操作系统出现的新问题是**多处理器调度(multiprocessor scheduling)**的问题。到目前为止，我们已经讨论了单处理器背后的一些原则。如何将这些想法扩展到多CPU上？我们必须克服哪些问题？


<br/>
<br/>


### 多处理器架构

Multiprocessor Architecture


我们需要了解单CPU硬件和多CPU硬件之间的区别。这种差异主要围绕硬件缓存的使用，以及如何跨越多个处理器共享数据。这就涉及到了复杂的计算机架构问题。

在单CPU的系统中，存在**硬件高速缓存(hardware
caches )**的层次结构，其通常帮助处理器更快地运行程序。高速缓存是小而快速的存储器，通常保存在系统主存储器中找到的数据的副本。相反，主存储器保存所有数据，但访问较大的存储器的速度较慢。通常将频繁访问的数据保存在高速缓存中，系统可以使大而慢的存储器看起来很快。

例如，考虑一个发出显式加载指令以从内存中获取值的程序，以及一个单CPU的简单系统。CPU有一个小缓存（64KB）和一个大的主内存。程序第一次发出负载时，数据驻留在主存储器中，因此需要很长时间才能获取。与其数据可以被重用的处理器将加载的数据的副本放入CPU的高速缓存中。如果程序稍后再次获取该相同的数据项，则CPU首先在高速缓存中检查它。如果在那里找到它，数据的获取速度要快的多，因此程序会运行得更快。

因此，高速缓存基于局部性的概念：**时间局部性(temporal locality)**和**空间局部性(spatial locality)**。时间局部性背后的想法是，当访问一条数据时，很可能在不就的将来再次访问它，想象变量设置指令本身在循环中反复访问。空间局部性的想法是，如果程序访问地址x处的数据项，则它也可以访问x附件的数据项。想想通过数组流式传输的程序，或者一个接一个地执行指令。由于这些类型的位置存在于许多程序中，因此硬件系统可以很好地擦侧哪些数据要放入缓存中，从而可以很好地工作。

![](/images/OSTEP/multi-10-1.png)

![](/images/OSTEP/multi-10-2.png)

<br>

事实证明，使用多个CPU进行缓存要复杂得多。想象一下，在CPU 1上运行的程序读取地址A的数据项（值D）；因为数据不在CPU 1的缓存中，系统从主存储器中取出数据，并获得值D。程序然后修改地址A的值，用新值D更新其缓存，将数据一直写入主存的速度很慢，因此系统稍后执行此操作。然后假设操作系统决定停止运行程序并将其移至CPU 2。然后程序重新读取地址A的值，CPU 2的缓存没有这样的数据，因此系统从主存中取值，并获得旧的值D而不是正确的值D。 这个一般性问题被称为**缓存一致性(cache coherence)**。

基本解决方案有硬件提供：通过监视内存访问，硬件可以确保基本上发生正确的事情并保留单个共享内存的视图。在基于总线的系统上执行此操作的每一种方法是使用称为**总线侦听(bus snooping)**，每个缓存通过观察它们连接到主存储器的总线来关注内存更新。当CPU然后看到它在缓存中保存的数据项的更新时，它将注意到该更改并使其副本无效，或更新它。如上所述，会写缓存会使这更加复杂，但你可以想象基本方案可能如何工作。



<br/>
<br/>


### 同步

Don’t Forget Synchronization


鉴于缓存完成所有这些工作以提供一致性，程序在访问共享数据时是否必须担心什么？
当跨CPU访问共享数据项或结构时，应该使用互斥来保证正确性。例如，假设我们在多个CPU上访问共享队列。没有锁，即使使用底层的一致性协议，同时在队列中添加或删除元素也不会按预期工作；一个需要锁定以原子方式将数据结构更新为新状态。

为了使其更具体，想象一下这个代码序列，它用于从共享链表中删除一个元素。如下图所示，如果两个CPU上的线程同时进入此例程。

Simple List Delete Code:

```c
typedef struct __Node_t {
 int value;
 struct __Node_t *next;
} Node_t;

int List_Pop() {
 Node_t *tmp = head; // remember old head ...
 int value = head->value; // ... and its value
 head = head->next; // advance head to next pointer
 free(tmp); // free old head
 return value; // return value at head
}
```



<br/>
<br/>



### 缓存关联

Cache Affinity


最后一个问题出现在构建多处理器缓存调度程序，称为**缓存关联(cache affinity)**。这个概念很简单：一个进程，当在特定的CPU上运行时，在CPU的高速缓存中建立一个相当大的状态。下一次进程运行时，在同一个CPU上运行它通常是有利的，因为如果它的某些状态已经存在于该CPU的高速缓存中，它将运行得更快。相反，如果每次在不同的CPU上运行进程，则进程的性能会更差，因为每次运行都必须重新加载状态。因此，多处理器调度程序在进行调度决策时应该考虑缓存关联，如果可能的话，可能更愿意将进程保留在同一CPU上。



<br/>
<br/>



### 单队列调度

Single-Queue Scheduling


现在讨论为多处理器系统构建调度程序。最简单的方法是通过将需要调度的所有作业放入单个队列中，从而简单地将基本框架重用与单处理器调度。简称为**单队列多处理器调度(singel queue multi processor scheduling)SQMS**。这种方法的优点就是简单；采取现有策略选择下一个要运行的最优作业并使其适应与一个以上CPU的工作并不需要太多工作（例如，如果有两个CPU，它可以选择两个最优作业来运行。）

然而，SQMS有明显的缺点。

第一个问题是缺乏可伸缩性(scalability)。为了确保调度程序可以在多个CPU上正常工作，开发人员将在代码中插入某种形式的锁(locking)，如前所述。锁确保当SQMS代码访问单队列时（如，找到要运行的下一个作业），产生正确的结果。

不幸的是，锁会极大地降低性能，尤其是随着系统中CPU数量的增长。随着对单个锁的争夺增加，系统在锁开销(overhead)上花费的时间越来越多，而系统本应该执行的工作所花费的时间却越来越少。

第二个主要问题是缓存关联(cache affinity)。例如，假设我们有五个作业要运行(A, B, C, D, E)和四个处理器。调度队列如下所示：

![](/images/OSTEP/sqms-1.png)

因为每个CPU只是从全局共享队列中选择要运行的下一个作业，所以每个作业最终都会在各个CPU之间跳动，因此，与从缓存关联的角度来看完全相反。为了解决此问题，大多数SQMS调度程序都包含某种关联机制，以尽可能使进程仅能在在同一CPU上继续运行。具体来说，一个CPU可能为某些作业提供了亲和力，单四处移动以负载均衡。如，假设按照以下方式调度相同的五个作业：

![](/images/OSTEP/sqms-2.png)

在这种安排中，作业A到D不会在处理器之间移动，只有作业E从一个CPU迁移到另一个CPU，伊尼茨保留了大多数的亲和力。然后，你可以决定下一次迁移另一个作业，从而也实现某种亲和力公平性。但是，实施这种方案可能很复杂。

因此，我们可以看到SQMS方法有其有点和缺点。在给定现有的单CPU调度程序的情况下，实现起来很简单，根据定义，该调度程序只有一个队列。但是，它的伸缩性不好（由于同步开销），并且不容易保留缓存关联。


<br/>
<br/>



### 多队列调度

Multi-Queue Scheduling


由于单队列调度程序中的问题，某些系统选择多队列。我们称这种方法为**多队列多处理器调度(multi queue multiprocessor scheduling)MQMS**。

在MQMS中，我们的基本调度框架由多个调度队列组成。尽管当然可以使用任何算法，但每个队列都可能遵循特定的调度规则，例如RR。
当一个作业进入系统时，根据某种启发式方法（如，随机、选择一项作业比其他作业少的作业...），该作业将恰好放在一个调度队列中。它基本上是独立调度的，从而避免了在单队列方法中发现的信息共享和同步问题。

例如，假设我们有一个只有两个CPU（cpu0、cpu1）的系统，并有一些作业(A、B、C、D)进入系统。假设每个CPU现在都有一个调度队列，则操作系统必须决定将每个作业放入哪个队列。它可能会执行以下操作：

![](/images/OSTEP/mqms-1.png)

现在，根据队列调度策略，每个CPU在确定应该运行什么时都可以选择两个作业。例如，使用RR，系统可能会生产一个如下所示的时间表：

![](/images/OSTEP/mqms-2.png)

它具有更大的可伸缩性。随着CPU数量的增加，队列数量也随之增加，因此锁和缓存争用不应该成为中心问题。此外，MQMS本质上提供了缓存亲和力，作业保持在同一CPU上，从而获得了在其中重用缓存内容的优势。

但是，如果你注意到，可能会发现一个心问题，这是基于多队列方法的根本问题：负载不均衡(load imbalance)。假设作业C完成了，现在，我们有以下调度队列：

![](/images/OSTEP/mqms-3.png)

那么多队列多处理器调度程序一你改改怎么做？才能克服负载不均衡的隐患...

<br>

> CRUX: HOW TO DEAL WITH LOAD IMBALANCE
> How should a multi-queue multiprocessor scheduler handle load imbalance, so as to better achieve its desired scheduling goals?

明显的答案是移动作业，我们再次将其称为迁移(migration)。通过将作业从一个CPU迁移到另一个CPU，可以实现真正的负载均衡。

![](/images/OSTEP/mqms-4.png)

<br>

当然，存在许多其它可能的迁移模式。但现在棘手的是：系统应该如何决定实施这种迁移？

一种基本方法是使用一种成为**work stealing**的技术。使用工作窃取方法，工作量低的(source)队列有时会偷窥另一个(target)队列，以查看其填充(full)程度。如果目标队列比源队列更满，则源将从目标窃取一个或多个作业以帮助负载均衡。

当然，这种方法具有自然的张力。如果经常查看其它队列，那么将会遭受高昂的开销并难以扩展。另一方面，如果不经常查看其它队列，则有遭受负载不均衡的危险。在系统策略设计中很常见，找到正确的阈值是一件黑手艺。



<br/>
<br/>



### Linux多处理器调度程序

Linux Multiprocessor Schedulers

有趣的是，在Linux社区中，没有通用的解决方案来构建多处理器调度程序。随着时间的推移，出现了三种不同的调度程序：

- O(1) scheduler
- the Completely Fair scheduler(CFS)
- the BF scheduler

O(1)和CFS都是用多队列，二BFS使用单队列，这表明两种方法都可以成功。例如，O(1)调度程序是基于优先级的（类似于前面讨论的MLFQ），随着时间的推移更改进程的优先级，然后调度优先级最高的进程，以满足各种给调度目标。交互性是另一个特别的重点。相比之下，CFS是确定性的比例共享(proportional-share)方法（更像前面讨论的Stride schedulig）。BFS是这三种方法中唯一的但队列方法，也是按比例共享，但它基于更复杂的方案，即Earliest Eligible Virtual Deadline First(EEVDF)。



<br/>
<br/>



### 总结

Summary

我们已经看到了多处理器调度的各种方法。单队列方法(SQMS)构建起来很简单，并且可以很好地负载均衡，但是固有地很难扩展到多处理器并具有缓存关联。多队列方法(MQMS)可以更好地处理缓存亲和关系，但是在负载不均衡方面存在麻烦，并且更加复杂。无论采用哪种方法，都没有简单的答案：构建通用调度程序仍然是一项艰巨的任务，因为小的代码的更改可能会导致巨大的行为差异。仅当您确切地知道自己在做什么或者至少为此而获得了很多钱时，才进行这样的练习。




<br/>
<br/>
<br/>



## CPU虚拟化总结

Summary Dialogue on CPU Virtualization: <http://pages.cs.wisc.edu/~remzi/OSTEP/cpu-dialogue.pdf>




<br/>
<br/>
<br/>




## 内存虚拟化对话

A dialogue on Memory Virtualization: <http://pages.cs.wisc.edu/~remzi/OSTEP/dialogue-vm.pdf>



<br/>
<br/>
<br/>




## 内存地址

Address Spaces: <http://pages.cs.wisc.edu/~remzi/OSTEP/vm-intro.pdf>


在早期，构建计算机系统很容易。因为那时用户期望不高。正是那些对易用性(ease of use)，高性能(high performance)，可靠性(reliability)...这些寄予厚望的用户确实导致了所有这些麻烦。



<br/>
<br/>



### 早期系统

Early System


从内存的角度来看，早期的机器并没有为用户提供太多抽象(abstraction)。基本上，计算机的物理内存类似于下图:

![](/images/OSTEP/13-1.png)

操作系统是一组位语内存中的例程(routines)(实际上是一个库)（图中从物理地址0开始），并且将有一个正在运行的程序(process)当前位于内存中(从物理地址开始，图中为64k)，并使用了其余的内存。



<br/>
<br/>



### 多编程和时间共享

Multiprogramming and Time Sharing


一段时间之后，由于机器价格昂贵，人们开始更有效地共享机器。因此，多重编程诞生了，在该时代，多进程准备在给定的时间运行，并且操作系统将在它们之间切换。例如，当一个人决定执行一个I/O时。这样做可以提高CPU的有效利用率(effective utilization)。在每台机器高达数十万百万的那些日子里，这种效率的提高尤其重要。

很快，人们开始需求更多的机器，时间共享(time share)诞生了。具体来说，许多人意识到批处理(batch computing)的局限性，特别是对程序员本身，因为他们厌倦了漫长的程序调试周期。交互性(interacttivity)的概念变得很重要，因为许多用户可能正在同时使用一台计算机，每个用户都在等待（或希望）他们当前正在执行的任务的及时响应。

实现时间共享的一种方法是短暂运行一个进程，使其完全访问所有的内存，然后停止它。将其所有状态保存到某种磁盘中（包括所有物理内存）。加载其它进程的状态，运行一段时间，从而实现对机器的某种粗略共享。

不幸的是，这种方法有一个大问题：它太慢了，特别是随着内存的增长。虽然保存和恢复寄存器级别状态(PC, general-purpose registers...)相对较快，但将整个内存内容保存到磁盘上却无法执行。因此，我们宁愿将进程留在内存中，同时在它们之间进行切换，从而使操作系统能够有效地实现时间共享。如下图所示:

![](/images/OSTEP/13-2.png)

该图有三个进程(A, B, C)，每个进程都为它们分配了512KB物理内存的一部分。假设一个单CPU，操作系统选择其中一个进程(如A)，而其它进程(B, C)在准备队列中等待运行。

随着时间共享变得越来越流行，您可能会猜到对操作系统提出了新的要求。尤其是，允许多个程序同时驻留(reside)在内存中使保护成为一个重要问题。您不希望某个进程能够读取(read)，更糟的是，写入其它进程的内存。



<br/>
<br/>



### 地址空间

The Address Space


但是，我们必须牢记那些讨厌的用户，并且这样做需要操作系统创建易于使用(easy to use)的物理内存抽象(abstraction)。我们称这种抽象为**地址空间**(address space)，它是运行在系统中的程序的内存视图。了解内存的这种基本操作系统抽象是了解如何虚拟化内存的关键。

进程的地址空间包含正在运行的程序的所有内存状态。例如，程序代码(指令)必须驻留在内存中的某个位置，因此它们位于地址空间中。该程序在运行时会使用**栈**(stack)来追踪(track)它在函数调用链中的位置，并分配局部变量，并在例程之间传递参数以及返回值。最后，**堆**(heap)用于动态分配(dynamically allocated)、用户管理的内存。例如，您可能会从C中的`malloc()`调用或以面向对象语言(C++, Java)通过`new`调用中接收到。当然，其中也有其他内容（如，statically-initialized variables)，但是现在让我们假设这三个组件:

- code
- stack
- heap

在下图中，有一个小地址空间(16KB)。程序代码位于地址空间的顶部（此例中，从0开始，并打包到地址空间的前1K）。代码是静态的，因此很容易放置在内存中，因此我们可以将其放在地址空间的顶部，并知道在程序运行时不需要任何空间。

![](/images/OSTEP/13-3.png)

接下来，我们在程序运行时拥有地址空间的两个区域，它们可能增长(grow)或收缩(shrink)。这些是堆(heap)(在顶部)和栈(stack)(在底部)。之所以这样放置它们，是因为每个人都希望能够增长，并且通过将它们放在地址空间的相对两端(opposite ends)，我们可以允许这种增长：它们只需要朝相反的方向增长。这样，堆就在代码之后(1KB)开始向下增长（如，用户通过`malloc()`请求更多内存时），栈从底部(16KB)开始并向上增长（如，用户进行过程调用时）。但是，栈和堆的这种放置只是一个约定(convention)。您可以根据需要以不同的方式安排地址空间（将在后面看到，当多线程(multi threads)共存(co-exist)于一个地址空间时，再也没有一种很好的方法来划分地址空间了）。

当然，当我们描述地址空间时，我们所描述的操作系统为正在运行的程序提供的抽象(abstraction)。该程序实际上不在物理地址0到16KB的内存中，而是将其加载到某个任意物理地址。查看图13-2中的进程A, B, C，您可以看到每个进程如何以不同的地址加载到内存中。因此，问题来了。

> THE CRUX: HOW TO VIRTUALIZE MEMORY
> How can the OS build this abstraction of a private, potentially large address space for multiple running processes (all sharing memory) on top of a single, physical memory?

当操作系统执行此操作时，我们说操作系统正在虚拟化内存(virtualizing memory)，因为正在运行的程序认为它已加载到特定地址的内存中，并且具有很大的地址空间（如32-bits或64-bits）。现实是完全不同的。

例如，当13-2图中的进程A尝试在地址0（我们称其为虚拟地址）上执行加载时，以某种方式，操作系统与某些硬件支持相结合。将必须确保加载实际上并没有转到物理地址0，而是物理地址320KB（将A加载到内存中的地址）。这是内存虚拟化的关键，而内存虚拟化是世界上每个现代计算机系统的基础。



<br/>
<br/>



### 目标

Goals


因此，我们在注释中完成了操作系统的工作：虚拟化内存。但是，操作系统不仅会虚拟化内存，还会以某种风格来做。为了确保操作系统能够做到这一点，我们需要一些目标来指导我们。我们之前已经看过这些目标，我们将再次看到它们，但肯定是值得重复的。

虚拟化内存(VM)系统的一个主要目标是**透明度**(transparency)。操作系统应以对运行的程序不可见的方式实现虚拟内存。因此，程序不应意识到内存已虚拟化。而是，程序行为就像具有自己的专用物理内存一样。在后台，操作系统（和硬件）完成了所有工作，以在许多不同的作业之间多路复用内存，从而实现了这种错觉。

虚拟化内存的另一个目标是**效率**(efficiency)。操作系统应努力在时间(不使程序运行速度更慢)和空间(不为支持虚拟化所需的结构使用过多内存)方面使虚拟化尽可能高效。在实现高效的虚拟化时，操作系统将不得不依赖硬件支持，包括诸如TLB的硬件功能。

最后，虚拟化内存的第三个目标是**保护**(protection)。操作系统应确保保护进程彼此之间以及操作系统本身不受进程影响。当一个进程执行加载、存储、指令获取时，它不应以任何方式访问或影响任何其它进程或操作系统本身（即，其地址空间之外的任何内容）的内存内容。因此，保护使我们能够在流程之间提供隔离(isolation)的特性。每个进程都以你敢再自己的隔离茧中运行，以免遭受其它故障甚至恶意进程的破坏。

在下一章中，我们将重点研究虚拟化内存所需的基本机制，包括硬件和操作系统支持。我们还将研究您在操作系统中会遇到的一些更相关的策略，包括如何管理可用空间(free space)以及当空间不足时要从内存中踢出(kick out of)哪些页面。这样，我们将加深您对现代虚拟内存系统实际工作方式的了解。

> TIP: THE PRINCIPLE OF ISOLATION
> Isolation is a key principle in building reliable systems. If two entities are properly isolated from one another, this implies that one can fail without affecting the other. Operating systems strive to isolate processes from each other and in this way prevent one from harming the other. By using memory isolation, the OS further ensures that running programs cannot affect the operation of the underlying OS. Some modern OS’s take isolation even further, by walling off pieces of the OS from other pieces of the OS. Such microkernels thus may provide greater reliability than typical monolithic kernel designs.



<br/>
<br/>
<br/>



### 总结

Summary


我们已经看到了一个主要的操作系统子系统的引入：虚拟化内存。虚拟化内存系统分则为程序提供庞大、稀疏的专用地址空间的错觉，这些程序将所有指令和数据保存在其中。操作系统将在一些严重的硬件帮助下获取这些虚拟化内存引用中的每一个，并将其转换为物理地址，可以将其提供给物理内存，以获取所需的信息。操作系统将一次对多个进程执行此操作，请确保相互保护程序并保护操作系统。整个方法需要大量的机制以及一些关键的策略才能起作用。我们将从头开始，首先描述关键机制。因此，继续。

```
ASIDE: EVERY ADDRESS YOU SEE IS VIRTUAL

Ever write a C program that prints out a pointer? The value you see
(some large number, often printed in hexadecimal), is a virtual address.
Ever wonder where the code of your program is found? You can print
that out too, and yes, if you can print it, it also is a virtual address. In
fact, any address you can see as a programmer of a user-level program
is a virtual address. It’s only the OS, through its tricky techniques of
virtualizing memory, that knows where in the physical memory of the
machine these instructions and data values lie. So never forget: if you
print out an address in a program, it’s a virtual one, an illusion of how
things are laid out in memory; only the OS (and the hardware) knows the
real truth.

Here’s a little program (va.c) that prints out the locations of the main()
routine (where code lives), the value of a heap-allocated value returned
from malloc(), and the location of an integer on the stack:

#include <stdio.h>
#include <stdlib.h>
int main(int argc, char *argv[]) {
  printf("location of code : %p\n", main);
  printf("location of heap : %p\n", malloc(100e6));
  int x = 3;
  printf("location of stack: %p\n", &x);
  return x;
}


When run on a 64-bit Mac, we get the following output:

location of code : 0x1095afe50
location of heap : 0x1096008c0
location of stack: 0x7fff691aea64

From this, you can see that code comes first in the address space, then
the heap, and the stack is all the way at the other end of this large virtual
space. All of these addresses are virtual, and will be translated by the OS
and hardware in order to fetch values from their true physical locations.
```




<br/>
<br/>
<br/>




## 内存API

Memory API: <http://pages.cs.wisc.edu/~remzi/OSTEP/vm-api.pdf>


在这个插曲中，我们讨论Unix系统中内存分配接口。所提供的接口非常简单，因此本章简短而切合实际。主要问题如下:

> CRUX: HOW TO ALLOCATE AND MANAGE MEMORY
> In UNIX/C programs, understanding how to allocate and manage memory is critical in building robust and reliable software. What interfaces are commonly used? What mistakes should be avoided?



<br/>
<br/>



### 内存类型

Types of Memory


在运行C程序时，分配了两种类型的内存。第一种称为**stack memory**，由编译器为您隐式管理其分配(allocation)和释放(deallocation)。因此，有时称为**automatic memory**。

在C中的stack上声明内存很容易。例如，假设您在`func()`函数中需要一些空间来存放一个名为`x`的整数。要声明这样的一片内存，只需执行以下操作:

```c
void func() {
  int x; //  declares an integer on the stack
  ...
}
```

其余部分由编译器完成，请确保在调用`func()`时在stack上留出空间。当您从函数返回时，编译器会为你释放内存。因此，如果你希望某些信息活到调用之后，你最好不要将该信息保留在stack中。

正是这种对long-lived memory的需求将我们带到了第二种类型的内存，称为**heap memory**，其中所有分配和释放都由您明确处理。毫无疑问，这是一个沉重的责任！无疑是许多bug的原因。但是，如果您小心翼翼并注意，您将可以正确使用此类接口，而不会遇到太多麻烦。如下是一个如何在堆(heap)上分配整数的示例:

```c
void func() {
  int *x = (int *) malloc(sizeof(int));
  ...
}
```

关于此小代码段的一些注意事项。首先，您可能会注意到stack和heap分配都发生在这一行：首先，编译器在看到您的指针声明(`int *x`)时就知道为整数指针腾出空间。随后，当程序调用`malloc()`时，它将在heap上为整数请求空间。例程返回这样一个整数的地址（成功时返回，失败时返回NULL），然后将其存储在stack中以供程序使用。

由于其显式的性质以及更广泛的用途，**heap memory**对内存和系统都提出了更多挑战。因此，这是我们其余讨论的重点。



<br/>
<br/>



### malloc调用

The malloc() Call


`malloc()`调用十分简单：向其传递一个大小，以要求在heap上留出一定的空间。它要么成功并返回给您指向新分配的空间的指针，要么失败并返回NULL。在命令行中输入`man malloc`以获取帮助。

```c
#include <stdlib.h>
...
void *malloc(size_t size);
```

从此信息中，您可以看到你需要做的就是包含头文件`stdlib.h`来使用`malloc`。实际上，您根本不需要这样做。因为默认情况下，所有C程序都链接到C library，之中包含了`malloc()`的代码。添加header只是让编译器检查你是否正确调用了`malloc()`（例如，向其传递了正确数量或类型的参数）。

`malloc()`的单个参数的大小为t，它简单地描述了您需要缩少字节。但是，大多数编程人员不会在此处直接输入数字。（实际上，这样做会被认为是糟糕的形式。）而是使用各种例程(routines)和宏(macros)。例如，要为双精度浮点值分配空间，只需执行以下操作:

```c
double *b = (double *) malloc(sizeof(double));
```

这种对`malloc()`的调用使用`sizeof()`运算符来请求正确的空间量。在C语言中，通常将其视为编译时(compile-time)运算符，这意味着实际大小在编译时已知，因此将一个数字替换为`malloc()`的参数。因此，将`sizeof()`正确地视为运算符，而不是函数调用（函数调用将在运行时发生）。

您还可以将变量名（而不只是类型）传递给`sizeof()`，但是在某些情况下，您可能无法获得所需的结果，因此请小心。例如，让我们看下面的代码片段:

```c
int *x = malloc(10 * sizeof(int));
printf("%d\n", sizeof(x));
```

在第一行中，我们声明了一个由10个整数组成的数组的空间，即好又花哨。但是，当我们在下一行中使用`sizeof()`时，它将返回一个较小的值，如4(32-bit机器)或8(64-bit机器)。原因是在这种情况下，`sizeof()`认为我们只是在问整数的指针有多大，而不是动态分配了多少内存。然而，有时`sizeof()`确实可以按你于其的那样工作:

```c
int x[10];
printf("%d\n", sizeof(x));
```

在这种情况下，有足够的静态信息供编译器知道已经分配了40Bytes。

另一个要注意的地方是字符串(string)。在声明字符串的空间时，请使用以下管用法：`malloc(strlen(s) + 1)`，该字符串使用`strlen()`函数旱区字符串的长度，并向其加1为末尾字符串字符留出空间。在这里使用`sizeof()`可能会导致麻烦。

你可能还会注意到，`malloc()`返回了一个指向void类型的指针。这样做只是C语言中传回地址并让编程人员决定如何处理该地址的方法。编程人员通过使用强制转换来进一步提供帮助。

<br>

> TIP: WHEN IN DOUBT, TRY IT OUT
> If you aren’t sure how some routine or operator you are using behaves, there is no substitute for simply trying it out and making sure it behaves as you expect. While reading the manual pages or other documentation is useful, how it works in practice is what matters. Write some code and test it! That is no doubt the best way to make sure your code behaves as you desire. Indeed, that is what we did to double-check the things we were saying about sizeof() were actually true!



<br/>
<br/>


### free调用

The free() Call


事实证明，分配内存时方程式最简单的部分。知道何时、如何释放内存是最困难的部分。要释放不再使用的heap memory，编程人员只需调用`free()`。

```c
int *x = malloc(10 * sizeof(int));
...
free(x);
```

该例程采用一个参数，即`malloc()`返回的指针。因此，您可能会注意到，分配的区域的大小不是由用户传递的，必须由内存分配本身进行跟踪。



<br/>
<br/>



### 常见错误

Common Errors


使用`malloc()`和`free()`时会出现许多常见错误。这是我们在本科操作系统中反复看到的一些内容。所有这些示例都可以编译，并且无需编译器即可窥视。

正确的内存管理一直是一个问题，事实上，许多更新的语言都支持自动内存管理(automatic memory management)。在这样的语言中，当您调用类似于`malloc()`的东西来分配内存时，您不必调用任何东西来释放空间。相反，垃圾收集器(grabage collector)将运行并找出您不在引用的内存，并为您释放它。

<br>

> TIP: IT COMPILED OR IT RAN 6= IT IS CORRECT
> Just because a program compiled(!) or even ran once or many times correctly does not mean the program is correct. Many events may have conspired to get you to a point where you believe it works, but then something changes and it stops. A common student reaction is to say (or yell) “But it worked before!” and then blame the compiler, operating system, hardware, or even (dare we say it) the professor. But the problem is usually right where you think it would be, in your code. Get to work and debug it before you blame those other components.

<br>

**忘记分配内存**
Forgetting To Allocate Memory

许多例程(rutines)希望在调用它们之前先分配内存。例如，例程`strcpy(dst, src)`将字符串从源指针复制到目标指针。但是，如果您不小心，可能会这样做：

```c
char *src = "hello";
char *dst; // oops! unallocated
strcpy(dst, src); // segfault and die
```

当您运行此代码时，可能导致分段错误，这是您对内存有错误的幻想，因为您对程序感到迷惑，并且很生气。
在这种情况下，正确的代码可能看起来像这样：

```c
char *src = "hello";
char *dst = (char *) malloc(strlen(src) + 1);
strcpy(dst, src); // work properly
```

或者您可以使用`strdup()`使您的生活更加轻松。

<br>

**没有分配足够的内存**
Not Allocating Enough Memory

一个相关的错误是没有分配足够的内存(allocate enough memory)，有时称为**缓存区溢出**(buffer overflow)。在上面的示例中，常见的错误是为目标缓存区腾出几乎足够的空间：

```c
char *src = "hello";
char *dst = (char *) malloc(strlen(src)); // too small!
strcpy(dst, src); // work properly
```

奇怪的是，取决于malloc的实现方式和许多其它细节，该程序通常看似正确运行。在某些情况下，执行字符串复制时，它会在已分配空间的末尾写入一个字节，但这在某些情况下是无害的，可能会覆盖一个不再使用的变量。在某些情况下，这些溢出可能非常有害，并且实际上是许多安全漏洞(security vulnerabilities)的根源。在其它情况下，无论如何，malloc库都会分配一些额外的空间，因此，您的程序实际上不会在其它变量的值上乱写，并且可以正常工作。在其它情况下，该程序的确会出错并崩溃。因此，我们吸取了另一个宝贵的教训：即使正确运行了一次，也并不意味着它是正确的。

<br>

**忘记初始化分配的内存**
Forgetting to Initialize Allocated Memory

遇到此错误，您可以正确地调用`malloc()`，但是忘记将某些值填写到新分配的数据类型中。不要这样做！如果您忘记了，您的程序最终将遇到未初始化的读取(uninitialized read)，即从heap中读取一些未知值的数据。谁知道里面会有什么？如果幸运的话，您可以通过一些值使程序仍然可以运行（如0）。如果不走运，那是随机和有害的事情。

<br>

**忘记释放内存**
Forgetting To Free Memory

另一个常见的错误称为**内存泄漏**(memory leak)，当您忘记释放内存时就会发生。在长时间运行的应用程序或系统中，这是一个巨大的问题，因为缓慢泄露内存最终会导致内存用完(OOM)，此时需要重新启动。因此，通常，当您完成了一块内存后，应该确保释放它。请注意，使用垃圾回收的语言在这里无济于事。如果您仍然引用某些内存块，则没有垃圾回收器会释放它，因此即使在更现代的语言中，内存泄漏仍然时一个问题。

在某些情况下，似乎不用调用`free()`是合理的。例如，您的程序寿命很短，将很快推出。在这种情况下，当进程终止时，操作系统将清除其所有分配的页面，因此本身不会发生内存泄漏。尽管这肯定是有效的，但养成这种习惯可能是个坏习惯，因此请谨慎选择这种策略。从长远来看，作为程序员的目标之一是养成良好的习惯。这些习惯一是了解如何管理内存，以及释放已分配的内存块。即使您可以避免这样做，也应该养成释放显式分配的每个字节的习惯。

<br>

**完成之前释放内存**
Freeing Memory Before You Are Done With It

有时，程序会在使用完成内存之前释放内存。这样的错误称为**悬空指针**(dangling pointer)，如您所猜测的那样，这也是一件坏事。后续使用可能会导致程序崩溃，或覆盖有效内存。

<br>

**反复释放内存**
Freeing Memory Repeatedly

程序有时还不止一次释放内存，这就是所谓的**double free**。这样做的结果是不确定的。可以想象，内容分配库可能会感到困惑，并且会做各种奇怪的事情，崩溃是常见的结果。

<br>

> ASIDE: WHY NO MEMORY IS LEAKED ONCE YOUR PROCESS EXITS
> When you write a short-lived program, you might allocate some space using `malloc()`. The program runs and is about to complete: is there need to call `free()` a bunch of times just before exiting? While it seems wrong not to, no memory will be “lost” in any real sense. The reason is simple: there are really two levels of memory management in the system.
> The first level of memory management is performed by the OS, which hands out memory to processes when they run, and takes it back when processes exit (or otherwise die). The second level of management is within each process, for example within the heap when you call `malloc()` and `free()`. Even if you fail to call `free()` (and thus leak memory in the heap), the operating system will reclaim all the memory of the process (including those pages for code, stack, and, as relevant here, heap) when the program is finished running. No matter what the state of your heap in your address space, the OS takes back all of those pages when the process dies, thus ensuring that no memory is lost despite the fact that you didn’t free it.
> Thus, for short-lived programs, leaking memory often does not cause any operational problems (though it may be considered poor form). When you write a long-running server (such as a web server or database management system, which never exit), leaked memory is a much bigger issue, and will eventually lead to a crash when the application runs out of memory. And of course, leaking memory is an even larger issue inside one particular program: the operating system itself. Showing us once again: those who write the kernel code have the toughest job of all...

<br>

**错误地调用free**
Calling free() Incorrectly

我们讨论的最后一个问题是对`free()`的错误调用。`free()`希望您仅将您从`mallo()`收到的指针之一传递给它。当您传递其它一些值时，可能会发生糟糕的事。因此，这种无效的释放是危险的，当然也应该避免。

<br>

**总结**
Summary

如您所见，有很多滥用内存的方式。用于经常发生内存错误，因此开发了整个工具生态圈来帮助您在代码中查找此类问题。检出purify和valgrind，两者都很擅长帮助您找到与内存相关的问题的根源。一旦您习惯了使用这些强大的工具，您会想知道如果没有这些工具，您将如何生存。



<br/>
<br/>



### 底层操作系统支持

Underlying OS Support


您可能已经注意到，在讨论`malloc()`和`free()`时，我们并未谈论系统调用。原因很简单，它们不是系统调用，而是库调用。因此，malloc库管理您的虚拟地址空间内的空间，但它本身是建立在某些系统调用之上，这些系统调用会向操作系统发出请求以请求更多内存或将一些内存释放回系统。

一个这样的系统调用称为`brk`，用于更改程序的中断(break)的位置：heap末尾的位置。它采用一个参数(新中断的地址)，因此根据新中断是大于还是小于当前中断来增加或减少heap的大小。一个附加的`sbrk`调用传递一个增量，起类似的作用。

请注意，永远不要直接调用`brk`或`sbrk`。它们由内存分配库使用。如果尝试使用它们，则可能会导致某些错误（非常严重）。坚持使用`malloc()`和`free()`。

最后，您还可以通过`mmap`调用从操作系统获取内存。通过传入正确的参数，`mmap()`可以在程序内创建一个匿名(anonymous)内存区域，该区域与任何特定文件都没有关联，而是与交换空间(swap space)关联，将在稍后详细讨论。



<br/>
<br/>



### 其它调用

Other Calls


内存分配库还支持其它一些调用。例如，`calloc()`分配内存，并在返回之前将其清零。这样可以防止某些错误（假设您认为内存已清零）而忘记自己进行初始化。当您为某物分配了空间，然后需要向其中添加一些东西时，例程`realloc()`也会很有用。`realloc()`会创建一个更大的新内存区域，将旧区域复制到并返回指向新区域的指针。




<br/>
<br/>
<br/>




## 地址转换

Address Translation


在开发CPU的虚拟化过程中，我们集中于一种称为受限的直接执行(limited direct execution, LDE)。它背后的想法很简单：在大多数情况下，让程序直接在硬件上运行。但是，在某些关键的时间点（如，当进程发出系统调用或发生计时器中断时），请安排好操作系统，以确保正确的事情发生。因此，在几乎没有硬件支持的情况下，操作系统将尽最大努力拜托正在运行的程序，从而提供有效的虚拟化。但是，通过在这些关键时刻进行干预，操作系统可确保对硬件进行控制。效率(efficiency)和控制(control)是任何现代操作系统的两个主要目标。

在虚拟化内存中，我们将采取类似的策略，在提供所需的虚拟化的同时实现效率和控制力。效率要求我们利用硬件支持，起初会很基本（如，只有几个寄存器），但会变得相当复杂(如，TLB，page-table支持)。控制意味着操作系统确保除了自身的内存外，不允许使用其它任何应用程序访问任何内存。因此，为了保护应用程序彼此之间以及操作系统与应用程序之间的相互帮助，我们在这里也需要硬件的帮助。最后，就灵活性而言，我们将需要从VM系统中获取更多信息。具体来说，我们希望程序能够以任意方式使用其地址空间，从而使系统更易于编程。因此，我们找出了症结所在:

> HOW TO EFFICIENTLY AND FLEXIBLY VIRTUALIZE MEMORY
> How can we build an efficient virtualization of memory? How do we provide the flexibility needed by applications? How do we maintain control over which memory locations an application can access, and thus ensure that application memory accesses are properly restricted? How do we do all of this efficiently?

我们将使用的通用技术可以被视为**基于硬件的地址转换**(hardware-based address translation)，简称为**地址转换**(address translation)，它是对有限直接执行(lde)的一般方法的补充。借助地址转换，硬件转换每个内存访问（如，fetch指令、load、store），将指令提供的虚拟地址更改为所需信息实际所在的物理地址。因此，在每个内存引用上，硬件都会执行地址转换，以将应用程序内存引用重定向到它们在内存中的实际位置。

当然，仅硬件本身不能虚拟化内存，因为它只是提供了低级机制来有效地进行虚拟化。操作系统必须在关键点接入以设置硬件，以便进行正确的转换。因此，它必须管理内存，跟踪那些位置空闲以及正在使用那些位置，并明智地进行干预以保持对内存使用方式的控制。

所有这些工作的目标是再次营造一种美好的幻想：该程序具有自己的私有内存，其自身代码和数据驻留在其中。虚拟现实的背后隐藏这丑陋的物理事实：当一个或多个CPU在运行一个程序与另一个程序之间切换时，许多程序实际上在同时共享内存。通过虚拟化，操作系统（在硬件的帮助下）将丑陋的机器现实转变为有用的，强大且易于使用的抽象。



<br/>
<br/>



### 假设

Assumptions


我们首次关于虚拟化内存的尝试将非常简单，几乎时可笑的。当您视图了解TLB，multi-level page tables和其它技术的来龙去脉时，这就是操作系统的运行方式。

具体来说，我们现在假设用户的地址空间必须连续(contiguously)放置在物理内容中。为了简单起见，我们还将假定地址空间的大小不是太大（小于物理内存）。最后，我们还将假定每个地址空间的大小完全相同。如果这些假设听起来不切实际，请不要担心，我们将在使用过程中放松它，从而实现逼真的内存虚拟化。



<br/>
<br/>



### 一个栗子

An Example


为了更好地了解实现地址转换所需做的事情以及为什么需要这种机制，我们来看一个简单的栗子。想象有一个进程的地址空间如图15-1所示。我们将在这里检查的是一个简短的代码序列，该序列从内存中加载值，将其递增3，然后将值存储回内存中。你可以想象此C语言的代码可能如下所示。编译器将这一行代码转换为汇编，可能看起来像着这样（在x86汇编中）。在Linux上使用`objdmp`进行反汇编。

```c
void func() {
  int x = 3000; // thanks, Perry.
  x = x + 3; // line of code we are interested in
...
```

```
128: movl 0x0(%ebx), %eax ;load 0+ebx into eax
132: addl $0x03, %eax ;add 3 to eax register
135: movl %eax, 0x0(%ebx) ;store eax back to mem
```

该段代码相对简单。假定x的地址已放置在寄存器ebx中，然后使用movl指令将该地址处的值加载到通用寄存器eax中。下一条指令将eax加3，最后一条指令将eax中的值存储在同一位置的内存中。

在图15-1中，观察代码和数据在进程的地址空间中的布局方式。三指令代码序列位于地址128（在顶部附近的代码部分）中，变量x的值位于地址15KB(在底部附近的stack中)。在图中，x的初始值为300，如其在栈中的位置所示。

![](/images/OSTEP/15-1.png)

当这些指令运行时，从进程的角度来看，将进行以下内存访问:
- Fetch instruction at address 128
- Execute this instruction (load from address 15 KB)
- Fetch instruction at address 132
- Execute this instruction (no memory reference)
- Fetch the instruction at address 135
- Execute this instruction (store to address 15 KB)

从程序的角度来看，其地址空间从地址0开始，最大增加到16KB。它生成的所有内存引用都应该在这些范围内。但是，为了虚拟化内存，操作系统希望将进程放置在物理内存中的其它位置，而不必放在地址0。因此，我们遇到了一个问题：如何以一种对进程透明的方式将进程重新放置在内存中？当实际上地址空间位于其它物理地址时，我们如何提供从0开始的虚拟地址空间的错觉？

在图15-2中找到了此进程的地址空间放置在内存中后，物理内存样子的栗子。在改图中，您可以看到操作系统本身使用了物理内存的第一个插槽，并且它已将上述示例中的过程重新定位到从32KB物理内存地址开始的插槽中。其它两个插槽时空闲的（16-32KB和48-64KB）。

![](/images/OSTEP/15-2.png)



<br/>
<br/>



### 动态(基于硬件)重定位

Dynamic (Hardware-based) Relocation


为了对基于硬件的地址转换有一定的了解，我们将首先讨论第一代产品。在1950年代后期的第一台分时(time-sharing)机器中引入了一个简单的概念——基准(base)和边界(bounds)。该技术也称为动态重定位(dylamic relocation)。

具体来说，我们在每个CPU中需要两个硬件寄存器：

- base register
- bounds register（有时称为limitregister）

这个base-and-bounds对将使我们能够将地址空间放置在物理内粗中所需的任何位置，并在确保进程只能访问自己的地址空间的同时这样做。在这种设置下，每个程序都被编写和编译，就好像加载到地址0一样。但是，当程序开始运行时，操作系统会决定将其加载到物理内存中的哪个位置，并将base register设置为该值。在上面的栗子中，操作系统决定将进程加载到32KB的物理地址，因此将base register设置为此值。

进程运行时，有趣的事情开始发生。现在，当进程生成任何内存引用时，处理器将按以下方式对其进行转换: `physical address = virtual address + base`

进程生成的每个内存引用都是一个虚拟地址。硬件又将base register的内容添加到该地址，结果是可以发布到内存系统的物理地址。

为了更好地理解这一点，让我们追溯一条指令执行时发生的情况。具体来说，让我们看一下先前序列中的一条指令：`128: movl 0x0(%ebx), %eax`

程序计数器(program counter)设置为128，当硬件需要fetch(提取)该指令时，它首先将该值增加到32KB(32768)的base register值中，以得到32896的物理地址。然后，硬件从该物理地址获取指令。接下来，处理器开始执行指令。然后，在某个时候，该进程从虚拟地址15KB发出负载，处理器将其加载，然后再次添加到base register(32KB)，获得最终物理地址47KB，从而获得所需的内容。

将虚拟地址转换为物理地址正是我们称为地址转换的技术。也就是说，硬件获取进程认为正在引用的虚拟地址，并将其转换为数据实际驻留的物理地址。因为地址的这种重定位(relocation)发生在运行时，并且因为即使进程开始运行后我们也可以移动地址空间，所以该技术通常称为动态重定位(dynamic relocation)。

现在你那可能会问： bounds(limit) register发生了什么？您可能已经猜到了，bounds register在那里可以提供保护。具体来说，处理器将首先检查内存引用是否在范围之内，以确保它是否合法。在上面的简单示例中，bounds register始终设置为16KB。如果某个进程生成的虚拟地址大于边界(bound)，或为负数，则CPU将引发异常，并且该进程可能会终止。因此，边界点要确保进程生成的所有地址都是合法的，并且在进程的边界内。

我们应该注意，base和bounds寄存器是芯片上保留的硬件结构（每个CPU一对）。有时人们将处理器中有助于地址转换的部分称为**内存管理单元**(memory management unit, MMU)。随着我们开发更复杂的内存管理技术，我们将向MMU添加更多电路。

关于bound register的一些说明，可以在以下两种方式的其中一项定义：一种方式(如上所述)，它保持地址空间的大小，因此硬件在添加base之前首先针对它检查虚拟地址。第二种方式，它确保地址空间末端的物理地址，因此硬件首先添加base，然后确保该地址在范围之内。两种方法在逻辑上是等效的，为简单起见，我们通常采用前一种方法。

<br>

> ASIDE: SOFTWARE-BASED RELOCATION
> In the early days, before hardware support arose, some systems performed a crude form of relocation purely via software methods. The basic technique is referred to as static relocation, in which a piece of software known as the loader takes an executable that is about to be run and rewrites its addresses to the desired offset in physical memory.
> For example, if an instruction was a load from address 1000 into a register (e.g., movl 1000, %eax), and the address space of the program was loaded starting at address 3000 (and not 0, as the program thinks), the loader would rewrite the instruction to offset each address by 3000 (e.g., movl 4000, %eax). In this way, a simple static relocation of the process’s address space is achieved.
> However, static relocation has numerous problems. First and most importantly, it does not provide protection, as processes can generate bad addresses and thus illegally access other process’s or even OS memory; in general, hardware support is likely needed for true protection [WL+93]. Another negative is that once placed, it is difficult to later relocate an address space to another location

<br>

> TIP: HARDWARE-BASED DYNAMIC RELOCATION
> With dynamic relocation, a little hardware goes a long way. Namely, a base register is used to transform virtual addresses (generated by the program) into physical addresses. A bounds (or limit) register ensures that such addresses are within the confines of the address space. Together they provide a simple and efficient virtualization of memory.



<br/>
<br/>



### 栗子

Example Translations


要更详细地了解基于base-and-bound的地址转换，我们来看个栗子。想象一下，地址空间大小为4KB(假设)的进程已在16KB的物理地址处加载。以下是许多地址转换的结果：

```
Virtual Address    Physical Address
0 → 16 KB
1 KB → 17 KB
3000 → 19384
4400 → Fault (out of bounds)
```

从示例中可以看到，您很容易将base address简单地添加到虚拟地址（可以正确地视为地址空间的偏移量）来获得最终的物理地址。仅当虚拟地址太大或负数时，结果才是错误，从而引发异常。



<br/>
<br/>



### 硬件支持：摘要

Hardware Support： A Summary


现在让我们总结一下我们需要硬件的支持(图15-3)。首先，正如有关CPU虚拟化的章节所讨论的，我们需要两种不同的CPU模式。操作系统以特权模式(privileged mode)(内核模式 kernel mode)，在该模式下，它可以访问整个机器。应用程序在用户模式(user mode)模式下运行，在此模式下它们只能有限制的做些什么。A single bit指示CPU当前正在运行的模式。在某些特殊场合（如系统调用或其它类型的异常或中断），CPU会切换模式。

硬件还必须自己提供base and bounds registers。因此，每个CPU都有一对额外的寄存器，它们是CPU的内存管理单元(MMU)的一部分。当用户程序运行时，硬件将通过将base value添加到用户程序生成的虚拟地址中来转换每个地址。硬件还必须能够检查地址是否有效，这可以通过使用bounds register的CPU中的某些电路来实现。

硬件应提供特殊的指令来修改base and bounds registers，从而允许操作系统在运行不同进程时对其进行改变。这些指令是特权的(privilegegd)，仅在内核模式下才能修改寄存器。想象一下，如果用户进程在运行时可以任意修改base register，则会给用户造成严重破坏，则会给用户造成严重破坏。想象一下，多可怕呀！

最后，CPU必须能够在某些情况下生成异常(exceptions)，如一个用户程序试图非法访问内存（地址超出边界）。在这种情况下，CPU应该停止执行用户程序，并安排操作系统运行 out of bounds exception handler。操作系统处理程序(OS Handler)可以弄清楚如何做出反应，在这种情况下，可能会终止进程。同样，如果用户程序尝试更改(privileged) base and bounds registers，则CPU应引发异常并运行在用户模式下的处理程序来执行一个特权操作。CPU还必须提供一种方法来通知这些处理程序(handler)的位置。因此，需要更多特权指令。

![](/images/OSTEP/15-3.png)



<br/>
<br/>



### 操作系统问题

Operating System Issues


正如硬件提供了支持动态重定位的新功能一样，该操作系统现在也必须解决新问题。硬件支持和操作系统管理的结合导致实现简单的虚拟内存。具体而言，在一些关键时刻，操作系统必须介入以实现我们的虚拟内存的base-and-bounds。

首先，操作系统必须在创建进程时采取措施，在内存中为其地址空间找到空间。幸运的是，假设每个地址空间小于物理内存的大小，并且每个空间大小相同，这对操作系统而言很容易。它可以简单地讲武里内u你视为一组插槽，并跟踪每个插槽是空闲的还是正在使用的。创建新进程时，操作系统必须搜索数据结构（通常称为空闲列表），以找到用于新地址空间的空间，然后将其标记为已使用。对于可变大小的地址空间，会更复杂，在后面的章节中讲解。

看个栗子。在图15-2中，您可以看到操作系统本身使用了物理内存的第一个插槽，并且它已将上述示例中的进程重新定位到从32KB物理内存地址开始的插槽中。其它两个插槽是空闲的（16-32KB, 48-94KB）。因此，空闲列表(free list)应该包含这两个条目。

其次，操作系统必须在进程终止时（即，它正常退出或由于行为不当而被强制杀死）做一些工作，回收其所有内存以供其它进程或操作系统使用。进程终止后，操作系统将其内存存放回空闲列表，并根据需要清楚所有相关联的数据结构。

第三，当发生上下文切换时，操作系统还必须执行一些其它步骤。毕竟，每个CPU上只有一对base and bounds register，并且它们的值对于每个正在运行的程序都是不同的，因为每个程序都加载到内存中不同的物理地址。因此，操作系统必须在进程之间进行切换时恢复 base-and-bounds register 对。具体来说，当操作系统决定停止运行某个进程时，它必须以某些按进程的结构将 base-and-bounds registers 的值保存到内存中。如process structure or process control block(PCB)。同样，当操作系统恢复正在运行的进程时，它必须将CPU的 base and bounds 的值设置为该进程的正确值。

我们应该注意，当进程停止时(即未运行)时，操作系统可能很容易地将地址空间从内存中一个位置移动到另一个位置。要移动进程的地址空间，操作系统首先要对进程进行调度。然后，操作系统将地址空间从当前位置复制到新位置。最后，操作系统会更新已保存的base register（在进程结构中）以指向新位置。恢复该过程后，将恢复其(新的) base registers，并再次开始运行，而不必担心其指令和数据现在位于内存中的全新位置。

第四，操作系统必须提供异常处理(exception handler)，或要调用的功能，如上所述，操作系统会在引导时(boot time)（via privileged instructions）安装这些处理程序(handler)。例如，如果某个进程视图访问边界之外的内存，则CPU将引发异常。当一个异常抛出时，操作系统必须准备好采取措施。操作系统的普遍反应将会有敌意：它可能会终止恶意程序。草最系统应高度保护正在运行的计算机，因此，它对于尝试访问内存或执行不应执行的指令的进程没有帮助。再见，行为异常。

图15-5和图15-6说明了许多硬件/操作系统时间轴上的互动。第一个显示了操作系统在引导时为准备使用机器所做的工作，第二个显示了进程(A)开始运行时发生的情况。请注意，在没有操作系统干预的情况下，硬件如何处理其内存转换。咋某个时间点(第二图的中间)，发生了计时器中断，并且操作系统切换到进程B，该进程执行错误的加载(到非法内存地址)。到那时，操作系统必须参与进来，终止进程并通过释放B的内存并将其条目从进程表中删除来进行清理。

从图中可看出，我们仍然遵循有限直接执行(limited direct execution)的基本方法。在大多数情况下，操作系统只是适当地设置硬件，然后让进程直接在CPU上运行。仅当进程异常时，才必须介入操作系统。

![](/images/OSTEP/15-4.png)

![](/images/OSTEP/15-5.png)

![](/images/OSTEP/15-6.png)



<br/>
<br/>



### 总结

Summary


在本章中，我们用虚拟内存中使用的一种称为地址转换的特定机制扩展了LDE(limited direct execution)的概念。通过地址转换，操作系统可以控制进程中的每个内存访问，从而确保访问保持在地址空间的范围内。这项技术效率的关键是硬件支持，它可以为每次访问执行转换，将虚拟地址（进程的内存视图）转换为物理地址（实际视图）。

所有这些都以对已重定位的进程以透明的方式执行。该进程不知道其内存引用正在被转换，从而产生一种奇妙的幻想。

我们还看到了一种特殊的虚拟化形式，称为 base and bounds 或 dynamic relocation。 它非常有效，因为只需要一点硬件逻辑就可以将 base register 添加到虚拟地址并检查进程生成的地址是否在在边界内。base and bounds同样提供保护，操作系统和硬件相结合，以确保没有进程可以在其自身的地址空间之外生成内存引用。保护无疑是操作系统最重要的目标之一。没有它，操作系统将无法控制机器（如果进程可以自由覆盖内存，则它们可以轻松地执行令人讨厌的事情，例如覆盖陷阱表并接管系统）。

不幸的是，这种简单的动态重定位技术确实没有效率。如，图15-2中所看到的，重定位的过程正在使用32-48KB的物理内存。但是，由于进程stack和heap不是太大，因此两者之间的所有空间都被浪费掉了。这种类型的浪费通常被称为内部碎片(internal fragmentation)，因为分配的单元的内部的空间并未全部用完（即碎片化, fragmented），因此被浪费了。在我们目前的方法中，尽管可能有足够的物理内存用于更多进程，但是我们目前仅限于将地址孔家放置在固定大小的插槽中，因此可能产生内部碎片。因此，我们将需要更复杂的机制，以尝试更好地利用物理内存并避免内部碎片。第一个尝试是对 base and bounds 进行细微的概括，即分段(segmentaion)，接下来将进行讨论。



<br/>
<br/>
<br/>



## 分段

Segmentation


到目前为止，我们已经将每个进程的整个地址空间都放在了内存中。借助 base and bounds registers，操作系统可以轻松地将进程重定位到物理内存的不同部分。但是，您可能已经注意到关于我们的这些地址空间的一些有趣之处：在 stack 和 heap 之间的中间有一大块空闲(free)空间。

从图16-1可以想象，虽然进程没有使用 stack 和 heap 之间的空间，但是当我们将整个地址空间重新放置在物理内存中的某个位置时，它仍在占用物理内存。因此，使用 base and bounds registers pair 来虚拟化内存的简单方法很浪费。当整个地址空间都无法容纳到内存中时，这也使得运行程序变得非常困难。因此， base and bounds 并不像我们所希望的那样灵活。

<br>

> THE CRUX: HOW TO SUPPORT A LARGE ADDRESS SPACE
> How do we support a large address space with (potentially) a lot of free space between the stack and the heap? Note that in our examples, with tiny (pretend) address spaces, the waste doesn’t seem too bad. Imagine, however, a 32-bit address space (4 GB in size); a typical program will only use megabytes of memory, but still would demand that the entire address space be resident in memory.

<br>

![16-1](/images/OSTEP/16-1.png)



<br/>
<br/>



### Generalized Base/Bounds


为了解决这个问题，一个想法诞生了。它被称为**分段**(segmentation)。这是一个很老的想法，至少可以追溯到1960年代。这个想法很简单：为什么在我们的MMU中不仅有一对 base and bounds pair，而且为什么地址空间的每个逻辑段(logical segment)都没有 base and bounds pair？段只是特定长度的地址空间的连续部分，在我们的规范地址空间中，我们具有三个逻辑上的不同的段： code, stack, heap。分段允许操作系统执行的操作是将这些分段中的每个分段放置在物理内存的不同部分中，从而避免用未使用的虚拟地址空间填充物理内存。

让我们看一个栗子。假设我们想将图16-1中的地址空间放入物理内存中。通过每个段的 base and bounds pair，我们可以将每个段独立地放置在物理内存中。例如，请参见图16-2。在那，您看到一个64KB的物理内存，其中有这三个段（并且为操作系统保留了16KB）。

![16-2](/images/OSTEP/16-2.png)

从图中可以看出，只有已使用的内存才在物理内存中分配了空间，因此可以容纳具有大量未使用地址空间的大地址空间（有时称为稀疏地址空间(sparse address space)）。MMU中支持分段所需的硬件结构正是您所期望的：在这种情况下，一组三个 base and bounds register pairs。下图6-3显示了上面示例的寄存器值，每个边界寄存器保存段的大小。

![16-3](/images/OSTEP/16-3.png)

从图中可以看到，代码段位于物理地址32KB，大小为2KB，heap segment位于34KB，大小为3KB。这里的段大小(size segment)与之前介绍的边界寄存器完全相同。它准确地告诉硬件该段中有多少字节有效（因此，使硬件能够确定程序何时在那些界限之外进行了非法访问）。

让我们使用图16-1中的地址空间进行示例转换。假定引用了虚拟地址100（位于代码段中，如图16-1中那样）。发生引用时（如，fetch指令），硬件会将base value添加到段的偏移量(offset)中（这里为100），以达到所需的物理地址：`100+32KB(32768)`（或32868）。它将检查该地址是否在范围之内（100小于2KB），找到该地址，然后发出对物理内存地址32868的引用。

现在让我们看一下堆中的一个地址，虚拟地址4200（图16-1）。如果仅将虚拟地址4200添加到heap的base（34KB），则会得到一个物理地址39016，这不是正确的物理地址。我们首先要做的是将偏移量(offset)提取到堆(heap)中，即地址指向该段中的哪个字节。因为heap从虚拟地址4KB(4096)开始，所以4200的偏移量实际上是4200减去4096（或104）。然后，我们将此偏移量添加到base register 物理地址(34K)中以获得所需的结果：34920。

如果我们试图引用一个超出堆末尾的非法地址（即7KB或更大的虚拟地址）怎么办？您可以想象会发生什么：硬件检测到地址超出范围，trap os，可能会终止违规进程。现在您知道了所有C程序员都学过的著名术语的起源：分段违规(segmentation violation)或分段错误(segmentation fault)。

<br>

> ASIDE: THE SEGMENTATION FAULT
> The term segmentation fault or violation arises from a memory access on a segmented machine to an illegal address. Humorously, the term persists, even on machines with no support for segmentation at all. Or not so humorously, if you can’t figure out why your code keeps faulting.



<br/>
<br/>



### 我们指的是哪个段

Which Segment Are We Referring To?


硬件在转换期间使用段寄存器(segment registers)。它如何知道一个段的偏移量，以及地址指向哪个段？

一种常见的方法（有时称为显式方法）是根据虚拟地址的前几位将地址空间划分为多个段。该技术一再VAX/VMS系统中使用。在上面的栗子中，我们有三个段。因此我们需要2bits来完成我们的任务。如果我们使用14-bit虚拟地址的前两位来选择段，我们的虚拟地址看起来如下：

![](/images/OSTEP/segment-1.png)

然后，在我们的示例中。如果高两位为00，则硬件知道虚拟地址在代码段(code segment)中，因此使用 code base and bounds pair 来重新定位到正确的物理位置。如果高两位为01，则硬件知道地址在heap中，因此使用heap base and bounds。让我们以上面栗子的堆虚拟地址(4200)进行转换，以确保其清晰明了。虚拟地址4200的二进制形式如下：

![](/images/OSTEP/segment-2.png)

从图中可看到，高两位(01)告诉硬件我们指的是哪个段。低12位是该段的偏移量（`0000 0110 1000`，十六进制`0x068`，或十进制`104`）。因此，硬件仅使用前两位来确定要使用的段寄存器，然后将接下来的12位用作段中的偏移量。通过将base register添加到offset，硬件达到最终的物理地址。请注意，偏移量也使边界检查(bounds check)变得很容易：我们可以简单地检查偏移量是否小于边界。因此，只需要检查偏移量是否小于边界即可。如果不是，则该地址是非法的。因此，如果base and bounds是数组（每段只有一个条目），则硬件将执行以下操作以获得所需的物理地址：

```c
// get top 2 bits of 14-bit VA
Segment = (VirtualAddress & SEG_MASK) >> SEG_SHIFT
// now get offset
Offset = VirtualAddress & OFFSET_MASK
if (Offset >= Bounds[Segment])
    RaiseException(PROTECTION_FAULT)
else
    PhysAddr = Base[Segment] + Offset
    Register = AccessMemory(PhysAddr)
```

在我们运行的栗子中，我们可以填写上述上述常量的值。具体来说，将SEG MASK设置为`0x3000`，将SEG SHIFT设置为`12`，将OFFSET MASK设置为`0xFFF`。

您可能还注意到，当我们使用前两位时，并且只有三个段(code, heap, stack)，地址空间的一个段将不使用。为了充分利用虚拟地址空间（并避免使用未使用的段），某些系统将代码(code)与堆(heap)放在同一段中，因此仅使用一位(1bit)来选择要使用的段。使用顶部这么多的最高位来选择段的另一个问题是，它限制了虚拟地址空间的使用。具体来说，每个段都被限制为最大大小，在我们的示例中为4KB（使用前两位选择意味着16KB的地址空间被分成4部分，在本例中为4KB）。如果正在运行的程序希望将段(stack, heap)增加到该最大值意外，则该程序不走运。

硬件还有其它方法可以确定特定地址位于哪个段中。在隐式方法中，硬件通过地址的形成方式来确定段。例如，如果该地址是从程序计数器生成的(fetch指令)，则该地址在代码段内；如果该地址是基于 stack或base pointer，则它必须在stack segment中；其它地址都必须的heap segment中。



<br/>
<br/>



### 栈

What About The Stack?


到目前为止，我们省略了地址空间的一个重要组成部分：栈(stack)。上图中，栈以重定位到物理地址28KB，但有一个关键的区别：它向后增长（即向较低的地址）。在物理内存中，它从28KB开始，然后增长回26KB，对应于16KB至14KB的虚拟地址。转换必须以不同的方式进行。

我们需要的第一件事是额外的硬件支持。硬件不仅需要base and bounds values，还需要知道段增长的方式（例如，当段向正方向增长时，将其设置为1，反之为0）。我们对硬件轨迹的更新视图如图16-4所示：

![16-4](/images/OSTEP/16-4.png)

有了硬件的了解，即段可能会向负方向增长，因此硬件现在必须稍微转换这些虚拟地址。让我们以栈虚拟地址为例，并对其进行转换以了解该过程。在此栗子中，假设我们希望访问虚拟地址15KB，该地址映射到物理地址27KB。因此，我们的虚拟地址的二进制形式：`11 1110 0000 0000`（十六进制`0x3C00`）。硬件使用最高的两位(11)来指定段，但随后我们留下了3KB的偏移量。为了获取正确的负偏移，我们必须从3KB中减去最大的段大小：在此示例中，一个段可以为4KB，因此正确的负偏移为`3KB - 4KB = -1KB`。我们秩序将负偏移量(`-1KB`)加到基数(base)(`28KB`)即可得到正确的物理地址(`27KB`)。可以通过确保负偏移的绝对值小于等于段的当前大小(2KB)来计算边界检查。



<br/>
<br/>



### 支持分享

Support for Sharing


随着对分段的支持的增加，系统设计人员很快意识到，只要多一点硬件支持，它们就可以实现新型的效率。具体来说，为了节省内存，有时在地址空间之间共享某些内存段很有用。特别是，代码共享(code sharing)是常见的，并且仍在当今的系统中使用。

为了支持共享，我们需要硬件以保护位(protection bits)的形式提供一些额外的支持。基本的支持在每个段上增加了几位，只是程序是否可以读取或写入段，或者执行段内的代码。通过将代码段设置为只读(read-only)，可在多个进程之间共享同一段代码，而不必担心隔离问题。尽管每个进程仍认为自己正在访问自己的私有内存，但操作系统秘密地共享了无法被进程修改的内存，因此保留了这种幻觉。

硬件和操作系统追踪的附加信息如图16-5所示。如您所见，代码段被设置为read和exe，因此内存中的同一物理段可以映射到多个虚拟空间。

![16-5](/images/OSTEP/16-5.png)

使用保护位，先前描述的硬件算法将不得不更改。除了检查虚拟地址是否在范围之内，硬件还必须检查是否允许特定访问。如果用户进程尝试写入只读段或执行不可执行的段，则硬件应引发异常，从而让操作系统处理有问题的进程。



<br/>
<br/>



### 细粒度与粗粒度分段

Fine-grained vs. Coarse-grained Segmentation


到目前为止，我们的大多数示例都集中在只有几个段(code, stack, heap)的系统上。我们可以认为这种分段是**粗粒度**(coarse-grained)的，因为它将地址空间切分为相对较大的粗块(coarse chunks)。但是，某些早期的系统更加灵活，并且允许地址空间由大量较小的段组成，称为**细粒度分段**(fine-grained segmentation)。

支持许多段需要进一步的硬件支持，并将某种类型的分段表(segmentation table)存储在内存中。这样的分段表通常支持创建大量的段，因此使系统能够以比我们目前为止讨论的方式更灵活的方式使用段。例如，诸如Burroughs B5000之类的早期机器就支持数千个段，并期望编译器将代码和数据分成单独的段，然后操作系统和硬件将支持这些段。当时的想法是，通过拥有细粒度的段，操作系统可以更好地了解正在使用的段以及哪些未使用的段，从而更有效地利用主内存。



<br/>
<br/>



### 操作系统支持

OS Support


您现在应该对分段的工作原理有了一个基本的了解。随着系统的运行，许多片(pieces of)地址空间会重新放置到物理内存中。因此相对与我们的更简单的方法（整个地址空间只有一个base/bound对），可以节省大量的物理内存。具体来说，不需要在物理内存中分配分配stack和heap之间未使用的空间，这使我们将更多地址空间放入物理内存中，并为每个进程支持较大且稀疏的虚拟地址空间。

但是，分段为操作系统带来了许多新问题：

- 第一个是一个老问题：操作系统应该在上下文切换上做什么？您现在应该有一个很好的猜测：段寄存器必须保存(saved)和恢复(restored)。显然，每个进程都有自己的虚拟地址空间，操作系统必须确保正确设置这些寄存器，然后才能再次运行该进程。
- 第二个是当段增长时的操作系统交互(interaction)。例如，程序可以调用`malloc()`分配对象。在某些情况下，现有的堆将能够处理请求，因此`malloc()`将为对象找到可用空间，并将指向该对象的指针返回给调用者(caller)。但是，在其它情况下，heap segment本身可能需要增长。在这种情况下，内存分配库将执行系统调用以增长堆（如，传统的UNIX `sbrk()` 系统调用）。然后，操作系统将提供更多孔家，将segment size register更新为新的(bigger)大小，并通知库成功。然后，库可以为新对象分配空间，并成功返回到调用程序。请注意，如果没有更多的物理内存可用，或操作系统确定调用进程已经有太多内存，则操作系统可能会拒绝该请求。
- 最后，也许是最重要的问题是管理物理内存中的可用空间(free space)。创建新的地址空间时，操作系统必须能够在物理内存中为其segment找到空间。先前，我们假设每个地址空间的大小相同，因此可以将物理内存视为一堆插槽(slots)，其中可以容纳多个进程。现在，每个进程有多个段，每个段可能不同大小。

出现的一般问题是物理内存很快就充满了可用空间的小洞(little holes of free space)，这使得分配新段或扩展现有的段变得困难。我们将此问题称为**外部碎片**(external fragmentation)，如图16-6左边。

在该示例中，出现了一个进程，并希望分配20KB的段。此例中，有24KB的可用空间，但不是在一个连续的段中（而是在三个非连续的块中）。因此，操作系统无法满足20KB的请求。当增长段的请求到达时，可能会发生类似的问题。如果物理空间的下一个如此之多的字节不可用，则操作系统将不得不拒绝该请求，即使物理内存中其它位置可能有可用的字节。

解决此问题的一种方法是通过重新布置现有的段来**压缩**(compact)物理内存。例如，操作系统可以停止任何正在运行的进程，将其数据复制到内存的一个连续区域，更改其段寄存器值以指向新的物理位置，从而可以使用很大的可用内存空间。通过这样做，操作系统使新的分配请求成功。但是，压缩是很昂贵的。因为复制段(copying segments)占用大量内存，并且通常会占用大量的处理器时间。请参见图16-6右边以获取压缩物理内存的示意图。压缩还会使增加现有的段的请求难以满足，因此可能会导致进一步调整以适应此类请求。

相反，一种更简单的方法可能是使用空闲列表(free list)管理法，该算法视图使大量内存可用于分配人们实际上采用了数百种方法，包括经典算法，例如最佳拟合（保留最佳空间列表，并返回大小最接近的空闲空间，满足对请求者的期望分配），最不适合(worst-fit, first-fit)以及更复杂的方案，如伙伴算法(buddy algorithm)。不幸的是，尽管算法多么聪明，但外部碎片仍然存在。因此，好的算法只是试图将其最小化。



<br/>
<br/>



### 总结

Summary


分段解决了许多问题，并帮助我们构建了更有效的内存虚拟化。除了动态重定位之外，分段还可以避免地址空间逻辑段之间潜在的巨大内存浪费，从而更好地支持稀疏地址空间。它很快，因为进行算术分段所需的操作很容易且非常适合硬件。转换的开销很小。附带的好处：代码共享。如果将代码放在单独的段中，则可能在多个正在运行的程序之间共享该段。

但是，据我们了解，在内存中分配大小可变的段会导致一些我们需要克服的问题。如上所述，第一个是外部碎片(external fragmentation)。因为段是可变的，所以空闲内存会被切成奇数大小的片段，因此很难满足内存分配请求。可以尝试使用智能算法或定期压缩内存，但是问题是根本的，很难避免。

第二个也许是更重要的问题是，分段仍然不够灵活，无法支持我们完全通用的稀疏地址空间。例如，如果我们在一个逻辑段中有一个大型但稀疏使用的堆，则整个堆仍必须驻留在内存中才能被访问。换句话说，如果我们关于地址空间使用方式的模型与底层分段的支持方式完全不匹配，那么分段就不能很好地工作。因此，我们需要找到一些新的解决方案。




<br/>
<br/>
<br/>




## 空闲空间管理

Free-Space Management


在本章中，我们从虚拟内存的讨论中走了一段弯路，以讨论任何内存管理系统的基本方面。无论是malloc库（管理进程堆的页面）还是操作系统本身（管理进程地址空间的各个部分）。具体来说，我们将讨论有关**空闲空间管理**(free memory management)的问题。

让我们使问题更具体。正如我们在讨论**分页**(paging)的概念时将看到的那样，管理可用空间肯定很容易。将要管理的空间划分为固定大小的单元很容易。在这种情况下，您只需保留这些固定大小单元的列表即可。当客户端请求其中一个时，返回第一个条目。

当您管理的空闲空间由大小可变的单元组成时，空闲空间管理会变得更加困难（有趣）。当使用分段实现虚拟内存时，这会在用户级(user level)内存分配库(memory allocation library)（如`malloc()`和`free()`）以及管理物理内存的操作系统中出现。无论哪种情况，存在的问题都称为外部碎片：空闲空间被切成不同大小的小碎片，因此碎片化。后续请求可能会失败，以为即使可用空间总量超过了请求的大小，也没有单个连续的空间可以满足请求。

![](/images/OSTEP/freespace-1.png)

该图显示了此问题的示例。在这种情况下，可用的总可用空间为20Byte。不幸的是，它被分为两个大小为10的块。结果，即使有20个可用字节，对15Byte的请求也将失败。因此，我们得出了本章要解决的问题。

> CRUX: HOW TO MANAGE FREE SPACE
> How should free space be managed, when satisfying variable-sized requests? What strategies can be used to minimize fragmentation? Whatare the time and space overheads of alternate approaches?



<br/>
<br/>



### 假设

Assumptions


大部分讨论将集中于用户级内存分配库中分配器的悠久历史。

我们假定一个基本接口，如`malloc()`和`free()`提供的接口。具体来说，`void * malloc(size)`采用单个参数，即应用程序请求的字节数。它会将一个指针交还给该大小(或更大)的区域。互补例程`void free(void *ptr`获取一个指针并释放相应的块。注意接口的含义：用户在释放空间时不会将其大小通知库。因此，该库必须能够弄清楚仅将指针分配给它时有多大的内存量。本章稍后将讨论如何执行此操作。

该库管理的空间在历史上称为堆(heap)，用于管理堆中空闲空间的通用数据结构是某种空闲列表(free list)。此结构包含对内存的托管区域中所有空闲空间块的引用。淡然，此数据结构本身不必是列表，而只需某种数据结构即可追踪空闲空间。

如上所述，我们进一步假设我们主要关注于外部碎片(external fragmentation)。分配器当然也有可能存在内部碎片(internal fragmentation)的问题。如果分配器分发(hand out)的内存块大于请求的内存块，则此类中未分配(因此未使用)的空间将被视为内部碎片（因为浪费发生在分配的单元内部），这是空间浪费的另一个示例。但是，为了简单起见，并且由于它是两种碎片类型中比较有趣的一种，我们将主要关注外部碎片。

我们还将假设，一旦将内存交付给客户端，便无法将其重定位到内存中的其它位置。例如，如果程序调用`malloc()`并被赋予指向堆中某些空间的指针，则该内存区域实质上该程序拥有的（并且不能被库移动），直到该程序通过相应的`free()`将其返回为止。因此，不可能压缩空闲空间，这将有助于对抗碎片。但是，在实现分段时，可在操作系统中使用压缩来处理分段。

最后，我们假设分配器管理一个连续的字节区域。在某些情况下，分配器可以要求该区域增长。例如，用户级内存分配库可能会在内核空间不足时调用内核来扩大堆（如调用sbrk之类）。但是，为了简单起见，我们仅假设该区域在整个生命周期中都是一个固定的大小。



<br/>
<br/>



### 低级机制

Low-level Mechanisms


在深入研究一些政策细节之前，我们将首先介绍大多数分配器中使用的一些常见机制。首先，我们将讨论拆分(splitting)和合并(coalescing)的基础知识，以及大多数分配器中的常用技术。其次，我们将展示如何快速轻松地追踪分配区域的大小。最后，我们将讨论如何在可用空间内构建一个简单列表，以追踪什么是空闲的以及什么不是。

<br>

**拆分和合并**
Splitting and Coalescing

空闲列表包含了一组元素，这些元素描述了堆中仍剩余的可用空间。因此，假定以下30字节堆：

![](/images/OSTEP/freespace-2.png)

该堆的空闲列表上将包含两个元素。一个条目描述了前十个字节空闲段(Bytes 0-9)，一个条目描述了另一个空闲段(Bytes 20-29)。

![](/images/OSTEP/freespace-3.png)

如上所述，任何大于10字节的请求都将失败（返回NULL）。只有一个连续的大内存块不可用。任意一个空闲块都可以轻松满足对大小恰好为10字节的请求。但是，如果请求的内容小于十个字节，会发生什么呢？

假设我们只请求一个字节的内存，在这种情况下，分配器将执行称为拆分(splitting)的操作：它将找到一个可以满足请求的空闲内存块，并将其拆分为两个。它的第一个块将返回给调用者，第二个块将保留在列表中。因此，在上面的示例中，如果请求了1Byte，并且分配器决定使用列表中的第二个元素来满足请求，则对`malloc()`的调用将返回20(1Byte的分配区域)，列表最终看起来像这样：

![](/images/OSTEP/freespace-4.png)

在图中，您可以看到列表基本保持不变。唯一的变化是，空闲区域现在从21开始，而不是20，并且该空闲区域的长度现在只有9。因此，当请求小于任何特定空闲块的大小时，拆分通常在分配器中使用。

在许多分配器中发现的推论机制被称为空闲空间合并(coalescing)。再次以上面的示例为例。给定这个heap，当应用程序调用`free(10)`并返回堆中间的空间时会发生什么？如果我们不加考虑就简单地将此可用空间添加会列表中，则最终可能会得到一个如下所示的列表：

![](/images/OSTEP/freespace-5.png)

注意此问题：虽然整个堆现在都是空闲的，但似乎将其分成三个块，每个块10个字节因此，如果用户请求20字节，则简单的列表遍历将找不到这样的空闲块，并返回失败。

为了避免此问题，分配器要做的是在释放一块内存时合并可用空间。这个想法很简单：在返回内存中的空闲块时，请仔细查看块的地址以及附近的空闲空间块。如果新释放的空间紧邻一个现有的空闲块，请将它们合并为一个等大的空闲块。因此，通过合并，我们的最终列表应如下表示：

![](/images/OSTEP/freespace-6.png)

确实，这是在进行任何分配之前首先出现的堆列表。通过合并，分配器可以更好地确保可以为应用程序提供较大的空闲扩展区。

<br>

**追踪分配区域的大小**
Tracking The Size Of Allocated Regions

您可能已经注意到`free(void *ptr)`的接口没有采用size参数。因此，假设给定一个指针，malloc库可以快速确定要释放的内存区域的大小，从而将空间合并放回空闲列表。

为了完成此任务，大多数分配器将一些额外的信息存储在header block中，该header block通常在分配的内存块之前保留在内存中。让我们再来看一个栗子（图17-1）。此例中，我们正在检查分配的大小为20Bytes的块，由ptr指向。假设用户调用了`malloc()`并将结果存储在ptr中，如`ptr=malloc(20)`。

![17-1](/images/OSTEP/17-1.png)

header至少包含分配的区域的大小（此例下为20）。它可能还包含其它用于加速释放(deallocation)的指针，用于提供其它完整性检查的幻数以及其它信息。让我们假设一个简单的header，其中包含区域的大小和一个魔幻数字，如下：

```c
typedef struct {
    int size;
    int magic;
} header_t;
```

上面的栗子看起来像图17-2所示。当用户调用`free(ptr)`时，该库将使用简单的指针算法找出header的起始位置：

```c
void free(void *ptr) {
    header_t *hptr = (header_t *) ptr - 1;
    ...
```

![17-2](/images/OSTEP/17-2.png)

在获得指向header的指标之后，库可以轻松地确定魔术数字是否与期望值匹配，作为健全性检查(`assert(hptr->magic == 1234567`))，并可以通过以下方式计算新释放区域的总大小：简单的数学运算（即，将header的大小添加到区域的大小）。请注意一个细节：空闲区域的大小是header的大小加上分配给用户的空间的大小。因此，当用户请求N个字节的内容时，库不搜索大小为N的空闲块，而实搜索大小为N加上header大小的空闲块。

<br>

**嵌入空闲列表**
Embedding A Free List

到目前为止，我们已经将简单的空闲列表视为概念实体。它只是描述堆中可用内存块的列表。但是，我们如何在空闲空间本身中建立这样的列表呢？

在一个更典型的列表中，当分配新节点时，仅在需要节点空间时才调用`malloc()`。不幸的是，在内存分配库中，您无法执行此操作！相反，您需要在可用空间本身内部(inside)构建列表。

假设我们要管理一个4096字节的内存块（即，堆为4KB）。为了将其作为空闲列表进行管理，我们首先必须初始化所述列表。最初，列表应具有一个条目，大小为4096（减去header大小）。这是列表节点的描述：

```c
typedef struct __node_t {
    int size;
    struct __node_t *next;
} node_t;
```

现在，让我们看一些代码。该代码初始化堆并将空闲列表的第一个元素放入该空间。我们假设堆是在铜鼓调用系统调用`mmap()`获得的一些可用空间内构建的。这不是构建此类堆的唯一方法，但在此示例中对我们有好处，代码：

```c
// mmap() returns a pointer to a chunk of free space
node_t *head = mmap(NULL, 4096, PROT_READ|PROT_WRITE,
                    MAP_ANON|MAP_PRIVATE, -1, 0);
head->size = 4096 - sizeof(node_t);
head->next = NULL;
```

运行此代码后，列表的状态是它只有一个条目，大小为4088。这是一个很小的堆，当为我们提供了一个很好的栗子。header 指针包含该范围的起始地址。假设它是16KB。因此，堆看起来如图17-3所示。

![17-3](/images/OSTEP/17-3.png)

现在，让我们想象一下，请求了一块内存，大小为100Bytes。为了满足该请求，库将首先找到一个足够大的块以容纳该请求。因为只有一个空闲块(4088)，所以选择此块。然后，该块将分为两部分：一个足以满足请求的块，以及剩余的空闲块。假设header为8字节，则堆中的空间现在看起来如图17-4所示。

![17-4](/images/OSTEP/17-4.png)

因此，在请求100字节时，库从现有的一个空闲块中分配了108个字节，并向其返回一个指针(ptr)，在分配的空间之前立即存储了header 信息，以便以后使用`free()`，并将列表中的一个空闲节点缩小到3980字节(`4088-108`)。

现在让我们看一下分配了三个区域，每个区域100字节（包括header是108）。此堆的可视化效果如图17-5所示：

![17-5](/images/OSTEP/17-5.png)

如您所见，现在分配了堆的前324个字节，因此我们看到该空间中的三个header以及调用程序正在使用的三个100字节区域。空闲列表仍然没有意思：只有一个节点(指向header)，但是在三个分割之后，现在只有3764个字节。但是，当调用程序通过`free()`返回一些内存时会发生什么呢？

在此示例中，应用程序通过调用`free(16500)`返回分配的内存的中间块。该值在上图中由指针sptr显示。

该库立即计算空闲区域的大小，然后将空闲块添加回空闲列表中。假设我们在空闲列表的开头插入，现在该空间看起来像图17-6所示：

![17-6](/images/OSTEP/17-6.png)

现在，我们有了一个列表，该列表以一个小的可用块(100字节，由列表的开头指向)和一个较大的空闲块(3764字节)开始。最后，我们的清单上有多个要素。是的，空闲空间是零散的，这是不幸的但却是很常见的情况。

最后一个栗子：现在假设释放了最后两个使用中的块。没有合并，最终会产生碎片，如图17-7。

![17-7](/images/OSTEP/17-7.png)

从图中可以看出，我们现在一团糟。为什么？很简单，我们忘记了合并列表。尽管所有内存都是空闲的，但它被切成碎片，因此尽管不是一个内存，但仍显示为碎片内存。解决方案很简单：遍历列表并合并相邻的块。完成后，堆将再次完整。

<br>

**堆越来越大**
Growing The Heap

我们应该讨论在许多分配库找到的最后一种机制。具体来说，如果堆空间不足，该怎么办？最简单的方法就是失败(fail)。在某些情况下，这是唯一的选择，因此返回NULL是一种明智的方法。

大多数传统的分配器从小堆(small-sized)开始，然后在耗尽时从操作系统请求更多内存。通常，这意味着它们进行某种系统调用（如，在大多数UNIX系统中为sbrk）来增长堆，然后从那里分配新的块。

为了满足`sbrk`请求，操作系统找到了空闲的物理页面，将它们映射到请求进程的地址空间中，然后返回新堆结尾的值。此时，可以使用更大的堆，并且可以成功处理请求。



<br/>
<br/>



### 基本策略

Basic Strategies


让我们再来看一些管理空闲空间的基本策略。这些方法主要基于您可以考虑的非常简单的策略。在阅读之前尝试一下，看看是否有所有其它选择。

理想的分配器既快速有可以最大程度地减少碎片。不幸的是，由于分配和释放请求的流(stream)可以是任意的（毕竟，它们由程序员确定），因此，如果输入设置错误，任何特定的策略都可能表现不佳。因此，我们将不描述最佳方法，而实讨论一些基础知识并讨论其优缺点。

<br>

**最佳拟合(best fit)**

最佳拟合的策略非常简单：首先，搜索空闲列表，找到大于请求大小的空闲内存块。然后，返回该组候选人中最小的一个。这就是所谓的最适合的块（也称为最小拟合）。遍历空闲列表就足以找到要返回的正确的块。

最佳拟合的直觉很简单：通过返回与用户请求接近的块，，最适合将尝试减少浪费的空间。但是，这是有代价的。幼稚的实现在对正确的空闲块进行详尽搜索时会付出沉重的性能损失。

<br>

**最差拟合(worst fit)**

最差拟合方法与最佳拟合相反。找到最大的块并返回请求的数量，将其余块保留在空闲列表中。因此，最差的拟合尝试使大块空闲，而不是由最佳拟合方法产生很多小块空闲。但是，再次需要完全搜索空闲空间，因此这种方法可能会很昂贵。大多数研究表明它的性能很差，导致碎片过多，同时开销仍然很高。

<br>

**首先拟合(first fit)**

首先拟合方法只是找到足够大的第一个块，然后将请求的量返回给用户。和以前一样，剩余的空闲空间将保留供后续请求使用。首次拟合具有速度优势——无需详尽搜索所有空闲空间，但有时会用小对象污染空闲列表的开头。因此，分配器如何管理空闲列表的顺序(order)成为一个问题。一种方法是使用基于地址的排序。通过使列表按空闲空间的地址排序，合并变得更加容易，并且碎片减少了。

<br>

**下一个拟合(next fit)**

下一个拟合算法不会始终在列表的开头开始首次拟合搜索，而实保留了指向列表中最后看的为止的额外指针。这个想法是将对空闲空间的搜索更均匀地分布在整个列表中，从而避免分散列表的开头。这种方法的性能与首次拟合非常相似，因此再次避免了详尽的搜索。

<br>

**栗子**

下面是上述策略的一些栗子。设想一个空闲列表，上面有三个元素，大小分别为10、30、20（在此我们将忽略head和其它详细信息，而只关注策略的运行方式）：

![](/images/OSTEP/freespace-7.png)

假设一个分配请求的大小为15。最佳拟合将搜索整个列表，并发现20是最适合的，因为它是可容纳请求的最小空闲空间。产生的空闲列表：

![](/images/OSTEP/freespace-8.png)

正如本例中所发生的，并且通常是采用最佳拟合的方法发生的，现在剩下了一个小的空闲块。最差拟合找到了最大的块，此例中为30。产生的空闲列表：

![](/images/OSTEP/freespace-9.png)

在此栗子中，最佳拟合和最差拟合执行相同的操作，还找到了可以满足请求的第一个空闲块。区别在于搜索成本。最佳拟合和最差拟合都在整个列表中浏览。首次拟合仅检查空闲块，直到找到合适的块，从而降低了搜索成本。

这些栗子只是分配策略的表面。为了更深入地了解，需要对实际工作负载和更复杂的分配器行为进行更详细的分析。




<br/>
<br/>



### 其余方法

Other Approaches


除了上述基本方法外，还有许多建议的技术和算法可以通过某种方式改善内存分配。我们在这里列出一些供您考虑。

<br>

**隔离列表(Segregated Lists)**

一段时间以来，一种有趣的方法是使用隔离列表。基本思想很简单：如果一个特定的应用程序发出一个（或几个）流行大小(popular-sized)的请求，则保留一个单独的列表以管理该大小的对象。所有其它请求都转发到更通用的内存分配器。

这种方法的好处是显而易见的。通过拥有一块专用于一个特定大小的请求的内存，碎片问题就不再那么重要了。此外，当分配和免费请求的大小合适时，它们可以很快得到服务，因为不需要复杂的列表搜索。

就像其它好主意一样，这种方法也将新的复杂性引入到系统中。例如，与通用池(general pool)相比，一个专用于给定大小的专用请求的内存池应占用多少内存？一个特殊的分配器——slab分配器，以一种相当不错的方式来处理此问题。

具体来说，当内核启动时，它将为可能经常被请求的内核对象（如，lock, file-system inode...）分配许多对象缓存。因此，每个对象高速缓存都是给定大小的空闲列表，它们分别为内存分配和空闲请求提供服务。当给定的高速缓存的空闲空间不足时，它会从更通用的内存分配器中请求一些内存块(slabs of memory)（请求的总量为页面大小和相关对象的倍数）。相反，当给定slab中对象的引用技术全部变为零时，通用分配器可以从专用分配器中回收它们，这通常在VM系统需要更多内存时执行。slab分配器还将通过将列表上的空闲对象保持在预先初始化的状态而超越了大多数隔离列表的方法。初始化和销毁数据结构的成本很高。通过将特定列表中的释放对象保持在其初始化状态，slab分配器避免了每个对象的频繁初始化和销毁周期，从而显着降低了开销。

<br>

> ASIDE: GREAT ENGINEERS ARE REALLY GREAT
> Engineers like Jeff Bonwick (who not only wrote the slab allocator mentioned herein but also was the lead of an amazing file system, ZFS) are the heart of Silicon Valley. Behind almost any great product or technology is a human (or small group of humans) who are way above average in their talents, abilities, and dedication. As Mark Zuckerberg (of Facebook) says: “Someone who is exceptional in their role is not just a little better than someone who is pretty good. They are 100 times better.” This is why, still today, one or two people can start a company that changes the face of the world forever (think Google, Apple, or Facebook). Work hard and you might become such a “100x” person as well. Failing that, work with such a person; you’ll learn more in a day than most learn in a month. Failing that, feel sad.

<br>

**Buddy Allocation**

因为合并对于分配器至关重要所以围绕简化合并设计了一些方法。在binary buddy allocator中找到了一个很好的栗子。在这样的系统中，首先从概念上将空闲内存视为大小为`2^N`的一个大空间。发出请求后，对可用空间的搜索将可用空间递归地除以2，直到找到一个足够大的块来容纳该请求。此时，所请求的块将返回给用户。这是一个在搜索7KB时划分64KB可用空间的栗子，如图17-8：

![](/images/OSTEP/17-8.png)

在该示例中，分配了最左边的8KB块并返回给用户。请注意，此方案可能会遭受内部碎片的困扰，因为只允许您给出2的幂次方的块。buddy allocation的美妙之处在于释放该块时会发生什么。当将8KB块返回到空闲列表时，分配器检查buddy 8KB是否空闲。如果空闲，它将两个块合并为一个16KB的块。然后，分配器检查16KB的buddy是否仍然空闲。如果是，它将合并这两个块。该递归合并过程在树上继续进行，恢复了整个可用空间，或者发现buddy正在使用时停止。

buddy allocation之所以有效的原因是，确定特定块的buddy很简单。考虑上面的可用空间中块的地址。如果您仔细考虑，就会发现每个buddy pair的地址仅仅相差一位(a single bit)。因此，您对binary buddy allocation方案的工作原理有基本了解。

<br>

**其它想法*(Other Ideas)*

上述许多方法的一个主要原因是它们缺乏伸缩性(scaling)。具体来说，搜索列表可能会非常慢。因此，高级分配器使用更复杂的数据结构来解决这些成本，从而简化性能。栗子包括平衡二叉树(balanced binary trees)、八叉树(splay trees)、部分有序树(partially-ordered trees)。

鉴于现代系统通常具有多个处理器并运行多线程工作负载，因此，在基于多处理器的系统上，花费了大量的精力使分配器正常工作也就不足为奇了 。



<br/>
<br/>



### 总结

在本章中，我们讨论了最基本的内存分配形式。这样的分配器无处不在，它链接到您编写的每个C程序，也存在于底层操作系统中，该操作系统为自己的数据结构管理内存。与许多系统一样，在构建这样的系统时需要进行很多权衡，并且您对分配给分配器的确切工作量了解的越多，您就可以做的越多，就需要对其进行调整以使其更好地适应该工作量。在现代计算机系统中，制造一种快速、节省空间、可扩展的分配器，使其能够很好地适用于各种工作负载，仍然是一个持续的挑战。




<br/>
<br/>
<br/>




## 分页

Paging


有时候，在解决大多数空间管理问题时，操作系统会采用两种方法之一。第一种方法是将事物切成大小可变(variable-sized)的段，如我们在虚拟内存中的分段所见。不幸的是，该解决方案具有固定的困难。特别是，将空间分成不同大小的块时，空间本身可能会变得碎片化，因此随着时间的推移分配会变得更具有挑战性。

因此，可能值得考虑第二种方法：将空间切成固定大小(fixed-sized)的片。在虚拟内存中，我们称这种想法为**分页**(paging)，它可以追溯到早期的重要系统Atlas。我们没有将进程的地址空间划分为一些可变大小的逻辑段（如，代码、堆、栈），而是将其划分为固定大小的单元，每个单元成为一个**页面**(page)。相应地，我们将物理内存视为固定大小的插槽数组，称为**页帧**(page frame)。这些帧中的每一个都可以包含一个虚拟内存页面(virtual memory page)。我们的挑战:

> HOW TO VIRTUALIZE MEMORY WITH PAGES
> How can we virtualize memory with pages, so as to avoid the problems of segmentation? What are the basic techniques? How do we make those techniques work well, with minimal space and time overheads?



<br/>
<br/>



### 栗子和概述

A Simple Example And Overview


为了使这种方法更清晰，让我们以一个简单的栗子进行说明。图18-2提供了一个很小的地址空间的栗子，该地址空间的总大小仅为64字节，具有四个16字节的页面（虚拟页面0、1、2、3）。当然，实际的地址空间要大得多，通常是32位(4GB)，甚至64位的地址空间。

![18-1](/images/OSTEP/18-1.png)

如图18-2所示，物理内存由多个大小固定的插槽组成，在这种情况下位八个页面帧(page frame)（用于128字节的物理内存）。如图所示，虚拟地址空间的页面已放置在整个物理内存中的不同位置。该图还显示了操作系统本身使用了一些物理内存。

![18-2](/images/OSTEP/18-2.png)

分页与以前的方法相比具有许多优点。可能最重要的改进是灵活性：通过完全开发的分页方法，该系统将能够有效地支持地址空间的抽象性，而与进程如何使用地址空间无关。

另一个优点是分页提供的空闲空间管理的简单性。例如，当操作系统希望将我们的64字节地址空间放入我们的八个页面物理内存时，它只会找到四个空闲页。也许操作系统为此保留了所有空闲页面的空闲列表，而只是从该列表中获取了前四个空闲页面。在此例中，操作系统将地址空间(AS)的虚拟页面0(page 0)放置在物理页帧3(page frame 3)中...页面帧4和6当前时空闲的。

为了记录地址空间的每个虚拟页在物理内存中的放置位置，操作系统通常保留每个进程的数据结构——称为**分页表**(page table)。分页表的主要作用是为地址空间的每个虚拟页面存储地址转换。对于我们的栗子，分页表将具有一下四个条目:

- virtual page 0 -> physical frame 3
- vp 1 -> pf 7
- vp 2 -> pf 5
- vp 3 -> pf 2

重要的是要记住，此分页表是按进程的数据结构。如果在上面的示例中要运行其它进程，则操作系统将不得不为其管理一个不同的分页表，因为其虚拟页面显然映射到了不同的物理页面。

让我们想象一下，地址空间很小(64Bytes)的进程正在执行内存访问：`movl <virtual address>, %eax`

具体来说，我们要注意将地址 virtual address 中的数据显式加载到寄存器eax中。

为了转换该进程生成的虚拟地址，我们必须首先将其分为两个部分：

- virtual page number(VPN)
- page offset

对于此栗子，由于进程的虚拟地址空间为64字节，因此我们总共需要6位虚拟地址(`2^6=64`)。因此，可将我们的虚拟地址概念化：

![](/images/OSTEP/paging-1.png)

在图中，Va5是虚拟地址的最高位，而Va0是虚拟地址的最低位。因为我们知道页面大小(16Bytes)，所以我们可以进一步划分地址。如下：

![](/images/OSTEP/paging-2.png)

在64字节的地址空间中，页面大小为16字节。因此，我们需要能够选择4个页面，并且地址的高两位就可以做到这一点。因此，我们有一个两位虚拟页码(vpn)。其余的位告诉我们页面的字节，在这种情况下为4位。我们称其为偏移量(offset)。

当进程生成虚拟地址时，操作系统和硬件必须结合起来才能将其转换为有意义的物理地址。例如，让我们假设上面的负载是虚拟地址21：`movl 21, %eax`

将21转换为二进制格式(`010101`)，因此我们可以检查该虚拟地址，并查看它如何分解为虚拟页码和偏移量：

![](/images/OSTEP/paging-3.png)

因此，虚拟地址21在虚拟页面(01)的第五个字节上(0101)。使用虚拟页码，我们现在可以索引我们的分页表并找到虚拟页面1(vp 1)驻留在哪个物理帧页中。在上面的分页表中，物理帧码(physical frame number, PFN)，有时又称为物理页面号(physical page numer, PPN)是7(pf 7, 二进制111)。因此，我们可以通过PFN替换VPN来转换此虚拟地址，然后将负载发布到物理内存。如图18-3。

![18-3](/images/OSTEP/18-3.png)

请注意，偏移量保持不变（即，不进行转换），因为偏移量仅告诉我们所需页面中的哪个字节。我们的最终物理地址为`1110101`（十进制117），正是我们希望从中获取数据的位置，如图18-2。

考虑到这一基本概述，我们现在可以问有关分页的一些基本问题。例如，这些分页表存储在哪里？分页表的典型内容是什么，有多大？分页会使系统变慢吗？



<br/>
<br/>



### 分页表存储在哪里

Where Are Page Tables Stored?


分页表(page table)会变得非常大，比我们之前讨论的小段(small segment)或base/bound pair要大得多。例如，设想一个典型的32位地址空间，4KB pages。该虚拟地址分为20位VPN和12位偏移量。

20位VPN表示操作系统必须为每个进程管理`2^20`个转换，假设每个分页表条目(page table entry)需要4字节来保存物理转换以及其它任何有用的东西，我们将为每个分页表获得4MB的巨大内存。那是很大的，现在想象有100个进程正在运行：这意味这操作系统仅需要所有这些地址转换就需要400MB内存！即使在机器拥有GB内存的现代时代，将很大一部分内存用于翻译也似乎有些疯狂，不是吗？而且，我们甚至都不会考虑这样的分页表对于64位地址空间会有多大。那太可怕了，也许会把你吓跑。

由于分页表非常大，因此我们不在MMU中保留任何特殊的片上硬件来存储当前正在运行的进程的分页表。相反，我们将每个进程的分页表存储在内存中的某个位置。现在假设分页表位于操作系统管理的物理内存中。稍后我们将看到许多操作系统内存本身可以被虚拟化，因此分页表可以存储在操作系统虚拟内存中（甚至交换到磁盘上），但是现在这太令人困惑了，因此我们将其忽略。图18-4中显示了操作系统内存中的分页表，在那看到很小的转换集。

![18-4](/images/OSTEP/18-4.png)

<br>

> ASIDE: DATA STRUCTURE — THE PAGE TABLE
> One of the most important data structures in the memory management subsystem of a modern OS is the page table. In general, a page table stores virtual-to-physical address translations, thus letting the system know where each page of an address space actually resides in physical memory. Because each address space requires such translations, in general there is one page table per process in the system. The exact structure of the page table is either determined by the hardware (older systems) or can be more flexibly managed by the OS (modern systems).



<br/>
<br/>



### 分页表中实际上是什么？

What’s Actually In The Page Table?


让我们谈谈分页表的组织方式。分页表只是一个数据结构，用于将虚拟地址(virtual page number)映射到物理地址(physical frame number)。因此，任何数据结构都可以工作。最简单的形式称为线性分页表(linear page table)，它只是一个数组。操作系统通过虚拟页码(vpn)位阵列建立索引(index)，并在该索引处查找分页表条目(pte)，以查找所需的物理帧号(pfn)。现在，我们将假定这种简单的线性结构。在后面的章节中，我们将使用更高级的数据结构来帮助解决分页中的某些问题。

至于每个pte的内容，我们在中有许多不同的地方值得一定程度的理解。**有效位**(valid bit)是通用的，用于指示特定转换是否有效。例如，当程序开始运行时，它将在其地址空间的一端具有代码和堆，而在另一端则具有栈。两者之间所有未使用的空间将被标记为无效(invalid)，并且如果该进程尝试访问此类内存，它将生成操作系统陷阱(trap)，这很可能会终止该进程。因此，有效位对于支持稀疏地址空间至关重要。通过简单地将地址空间中所有未使用的页面标记为无效，我们无需为这些页面分配物理帧，从而节省了大量内存。

我们还可能有**保护位**(protection bits)，指示是否可以读取、写入或执行页面。同样，以这些位不允许的方式访问页面将产生对操作系统的陷阱。

还有其它一些重要的方面，但我们暂时不会多谈。**当前位**(present bit)指示页面是在物理内存中还是在磁盘上(swapped out)。当我们研究如何将部分地址空间交换(swap)到磁盘以支持大于物理内存的地址空间时，我们将进一步理解这种机制。交换(swapping)使操作系统可以通过将很少使用的页面移动到磁盘来释放物理内存。**脏位**(dirty bit)也很常见，指示该页从进入内存以来是否已被修改。

**引用位**(reference bit, accessed bit)来跟踪页面是否已被访问，并且对于确定哪些页面受欢迎并因此应将其保留在内存中很有用。此类知识在页面替换期间至关重要，我们将在随后的章节中详细研究该主题。

图18-5显示了x86体系结构中的示例分页表条目(pte)。它包含：

- present bit(P)；
- read/write bit(R/W)，是否允许对该页面进行写操作；
- user/supervisor bit(U/S)，用于确定用户模式进程是否可以访问该页面；
- 一些位(PWT, PCD, PAT, G)确确定这些页面的硬件缓存工作方式；
- accessed bit(A)；
- dirty bit(D)；
- page frame number(PFN)；

![18-5](/images/OSTEP/18-5.png)

<br>

> ASIDE: WHY NO VALID BIT?
> You may notice that in the Intel example, there are no separate valid and present bits, but rather just a present bit (P). If that bit is set (P=1), it means the page is both present and valid. If not (P=0), it means that the page may not be present in memory (but is valid), or may not be valid. An access to a page with P=0 will trigger a trap to the OS; the OS must then use additional structures it keeps to determine whether the page is valid (and thus perhaps should be swapped back in) or not (and thus the program is attempting to access memory illegally). This sort of judiciousness is common in hardware, which often just provide the minimal set of features upon which the OS can build a full service.



<br/>
<br/>



### 分页: 也很慢

Paging: Also Too Slow


内存中的分页表，我们已经知道它们可能太大。事实证明，它们也可以放慢脚步。例如，采用简单的指令：`movl 21, %eax`

同样，让我们仅检查对地址21的显式引用，而不用担心指令提取(fetch)。此例中，我们假设硬件为我们执行转换。为了获取所需数据，系统必须首先将虚拟地址(21)转换为正确的物理地址(117)。因此，在从地址117提取数据之前，系统必须首先从进程的分页表中提取适当的页表项(page table entry)，执行转换，然后从物理内存中加载数据。

为此，硬件必须知道当前正在运行的进程的分页表所在的位置。现在让我们假设一个page table base register包含分页表起始位置的物理地址。为了找到所需PTE的位置，硬件将执行以下功能：

```c
VPN = (VirtualAddress & VPN_MASK) >> SHIFT
PTEAddr = PageTableBaseRegister + (VPN * sizeof(PTE))
```

在我们的栗子中，VPN_MASK将设置为`0x30`，这将从完整的虚拟地址中提取VPN位。SHIFT设置为4（偏移量中的位数），以便我们将VPN位向下移动以形成正确的整数虚拟页码。例如，使用虚拟地址21(010101)，并通过mask将其值转换为010000；转换后可根据需要将其转换为01或virtual page 1。然后，我们将此值用作page table base register指向的分页表条目(pte)数组的索引。

一旦知道了该物理地址，硬件就可以从内存中获取PTE，提取PFN，并将其与虚拟地址的偏移量连接起来以形成所需要的物理地址。具体来说，您可以考虑将PFN通过SHIFT左移，然后将其与偏移量按位或操作(OR)，以形成最终地址。

```c
offset = VirtualAddress & OFFSET_MASK
PhysAddr = (PFN << SHIFT) | offset
```

最后，硬件可以从内存中获取所需的数据，并将其放入寄存器eax中。该程序现在以成功从内存中加载值！

总而言之，我们现在描述每个内存引用上发生的事情的初始协议。对于每个内存引用（无论是获取指令还是显式加载或存储），分页都要求我们执行一个额外的内存引用，以便首先从分页表中获取转换。那是很多工作！额外的内存引用成本很高，在这种情况下，可能会使处理速度减慢两倍或更多。

```c
// Accessing Memory With Paging


// Extract the VPN from the virtual address
VPN = (VirtualAddress & VPN_MASK) >> SHIFT

// Form the address of the page-table entry (PTE)
PTEAddr = PTBR + (VPN * sizeof(PTE))

// Fetch the PTE
PTE = AccessMemory(PTEAddr)

// Check if process can access the page
if (PTE.Valid == False)
    RaiseException(SEGMENTATION_FAULT)
else if (CanAccess(PTE.ProtectBits) == False)
    RaiseException(PROTECTION_FAULT)
else
    // Access is OK: form physical address and fetch it
    offset = VirtualAddress & OFFSET_MASK
    PhysAddr = (PTE.PFN << PFN_SHIFT) | offset
    Register = AccessMemory(PhysAddr)
```

现在您可以看到有两个必须解决的实际问题。没有对硬件和软件进行仔细设计，分页表将导致系统运行太慢，并占用太多内存。虽然这似乎是满足我们的内存虚拟化需求的绝佳解决方案，但必须首先克服这两个关键问题。



<br/>
<br/>



### 内存追踪

A Memory Trace


在结束之前，我们现在追踪(trace)一个简单的内存访问示例，以演示使用分页时内存访问发生的所有的结果。示例代码片段(`array.c`文件)如下：

```c
int array[1000];
...
for (i = 0; i < 1000; i++)
    array[i] = 0;

//> 编译
// gcc -o array array.c -Wall -O
// 执行
// ./array
```

当然，要真正访问此代码段（只需初始化一个数组）的内存，我们就必须知道几件事。首先，我们必须对生成的二进制文件进行反汇编(disassemble)（在Linux上使用`objdump`，Mac上使用`otool`），以查看使用哪些汇编指令在循环中初始化数组。如下是生成的汇编代码：

```
1024 movl $0x0,(%edi,%eax,4)
1028 incl %eax
1032 cmpl $0x03e8,%eax
1036 jne 0x1024
```

如果了解一些x86的知识，则实际上很容易理解此代码。第一条指令将值0移至数组位置的虚拟内存地址。该地址通过将`%edi`的内容加上`%eax`乘4得出。因此，`%edi`保存数据的基地址，而`%eax`保存数组index(i)。将其乘4，因为该数组是一个整数数组，每个数组的大小为四字节。

第二条指令递增`%eax`中保存的数组索引。第三条指令将寄存器的内容与十六进制值`0x03e8`，或十进制1000进行比较。如果比较显示两个值还不相等(jne指令测试)，则第四条指令跳回循环的顶部。

为了了解该指令序列访问哪个内存（虚拟和物理级别上），我们必须假设一些有关代码片段和数组在虚拟内存中的位置以及分页表的内容和位置的信息。

对于此示例，我们假定虚拟地址空间的大小为64KB，我们还假定页面大小为1KB。现在我们只需要知道分页表的内容及其在物理内存中的位置。假设我们有一个基于数组的线性分页表，它位于物理地址1KB。至于它的内容，我们需要担心的几个虚拟页面，已经为此栗子映射了。首先，代码所在的虚拟页面。因为页面大小为1KB，所以虚拟地址1024位于虚拟地址空间的第二个页面上。假设此虚拟页面映射到物理帧4（vpn 1 -> pfn 4)。

接下来，是数组本身。它的大小为4000字节(1000个整数)，我们假定它位于40000到44000的虚拟地址。此范围的虚拟页面为vpn=39...42。因此，我们需要这些页面的映射。如：

- vpn 39 -> pfn 7
- vpn 40 -> pfn 8
- vpn 41 -> pfn 9
- vpn 42 -> pfn 10

现在，我们准备追踪程序的内存引用。在运行时，每条fetch指令将生成两个内存引用：一个指向分页表以查找指令所在的物理帧，另一个指向指令本身以将其提取给CPU以进行处理。另外，有一个以`mov`执行形式的显式内存引用。这将首先添加另一个分页表访问，然后添加阵列本身。

图18-7描述了前五个循环迭代的整个过程。

![18-7](/images/OSTEP/18-7.png)



<br/>
<br/>



### 总结

Summary


我们介绍了分页(paging)的概念，以解决我们虚拟化内存的挑战。与以前的方法（如，分段）相比，分页具有许多优点。首先，它不会导致外部碎片，因为分页将内存划分为固定大小的单元。其次，它非常灵活，可以稀疏地使用虚拟地址空间。

但是，不加注意地实现分页支持将导致机器速度变慢（具有许多额外的内存访问权限来访问分页表），以及内存浪费（内存被分页表填充，而不是有用的应用程序数据）。因此，我们将不得不更加努力地想出一种不仅可以工作而且很好运行的分页系统。幸运的是，接下来的两章将向我们展示如何做到这一点。




<br/>
<br/>
<br/>




## 地址转换缓存

Paging: Faster Translations (TLBs)

Translation Lookaside Buffer


使用分页作为支持虚拟内存的核心机制可能会导致高性能开销。通过将地址空间切成固定大小的小单元(即，页面)，分页需要打两个的映射信息。因为映射信息通常存储在物理内存中，所以分页逻辑上需要对程序生成的每个虚拟地址进行额外的内存查找。在每条fetch指令或显示加载或存储之前进入内存获取转换信息的速度过慢。因此，我们的问题是：

> HOW TO SPEED UP ADDRESS TRANSLATION
> How can we speed up address translation, and generally avoid the extra memory reference that paging seems to require? What hardware support is required? What OS involvement is needed?

当我们想要加快速度时，操作系统通常需要一些帮助。而且帮助通常来自操作系统的老朋友：硬件。为了加快地址转换的速度，我们将添加所谓的**转换后备缓冲器**(translation lookaside buffer, TLB)。TLB是内存管理单元(MMU)的一部分，并且只是流行的虚拟到物理地址转换的一个硬件缓存。因此，更好的名称是**地址转换缓存**(address translation cache)。在引用每个虚拟内存时，硬件首先检查TLB。如果是，执行转换(快速地)而无需查阅分页表。由于它们对性能的巨大影响，真正意义上的TLB使虚拟内存成为可能。



<br/>
<br/>



### TLB基本算法

TLB Basic Algorithm


```
// TLB Control Flow Algorithm


VPN = (VirtualAddress & VPN_MASK) >> SHIFT
 (Success, TlbEntry) = TLB_Lookup(VPN)
 if (Success == True) // TLB Hit
      if (CanAccess(TlbEntry.ProtectBits) == True)
          Offset = VirtualAddress & OFFSET_MASK
          PhysAddr = (TlbEntry.PFN << SHIFT) | Offset
          Register = AccessMemory(PhysAddr)
      else
          RaiseException(PROTECTION_FAULT)
 else // TLB Miss
     PTEAddr = PTBR + (VPN * sizeof(PTE))
     PTE = AccessMemory(PTEAddr)
     if (PTE.Valid == False)
         RaiseException(SEGMENTATION_FAULT)
     else if (CanAccess(PTE.ProtectBits) == False)
         RaiseException(PROTECTION_FAULT)
     else
         TLB_Insert(VPN, PTE.PFN, PTE.ProtectBits)
         RetryInstruction()
```

以上给出了硬件如何处理虚拟地址转换的示意图，假设有一个简单的线性分页表和由硬件管理的TLB（即，硬件承担了分页表访问的大部分责任）。硬件遵循的算法是这样的：首先，从虚拟地址中提取虚拟页码(vpn)，然后检查TLB是否有该vpn的转换。如果是，我们将获得一个TLB命中，这意味着TLB将保留转换。成功！现在，我们可以从相关的TLB条目中提取页帧号(pfn)，并将其连接到与原始虚拟地址的偏移量上，并形成所需的物理地址(pa)和访问内存，假设保护检查不会失败。

如果CPU在TLB中找不到转换(TLB miss)，我们还有更多工作要做。在此栗子中，硬件访问分页表以查找转换，并且假设该进程生成的虚拟内存引用是有效且可访问的，则使用此转换来更新TLB。这些操作的成本很高，主要是因为访问分页表需要额外的内存引用。最后，一旦更新了TLB，硬件将重新尝试该指令。这次，可在TLB中找到转换，并且可以快速地处理内存引用。

像所有高速缓存一样，TLB的构建前提是在通常情况下，在高速缓存中找到转换（即，命中(hit)）。如果是这样，则几乎不会增加任何开销，因为TLB位于处理核心附近，并且设计得非常快。发生未命中(miss)时，会产生很高的分页成本。必须访问分页表以查找转换，并获得额外的内存引用（更复杂的分页表则需要更多的内存引用）。如果这种情况经常发生，则程序运行速度可能会明显变慢。相对于大多数CPU指令，内存访问成本很高，而且TLB丢失会导致更多的内存访问。因此，我们希望尽可能避免TLB未命中。



<br/>
<br/>



### 访问数组的栗子

Example: Accessing An Array


为了清楚说明TLB的操作，让我们检查一个简单的虚拟地址追踪，看看TLB如何改善其性能。在此例中，假设内存中有一个10个4字节整数的数组，从虚拟地址100开始。进一步假设我们有一个小的16位页面和8位虚拟地址空间。因此，虚拟地址分为4位vpn(16 virtual pages)和4位偏移量。

图19-2显示了排列在系统的16个16字节页面上的数组。如您所见，数组的第一个条目`a[0]`开始于(vpn=06, offset=04)。该页面上只能容纳三个4字节整数。数组继续到下一页(vpn=07)，在此找到下四个条目(`a[3]..a[6]`)。下面也是如此。

![19-2](/images/OSTEP/19-2.png)

现在，让我们考虑一个访问每个数组元素的简单循环，在C中看起来如下：

```c
int sum = 0;
for (i = 0; i < 10; i++) {
    sum += a[i];
}


//>
```

为了简单起见，我们将假装循环生成的唯一内存访问是对数组的访问。当访问第一个数组元素(`a[0]`)时，CPU将看到虚拟地址100的负载。硬件从中提取vpn，并使用该地址检查TLB的有效转换。假设这是程序第一次访问数组，结果将是TLB丢失(miss)。

下一步访问(`a[1]`)，这里有一些好消息：TLB命中！由于数组的第二个元素紧挨着第一个元素，因此它位于同一个页上。因为我们在访问数组的第一个元素时已经刚问了此页面，所以转换已经加载到了TLB中。这是我们成功的原因。访问(`a[2]`)会遇到类似的成功，因为它与(`a[0]`)和(`a[1]`)位于同一页面上。

不幸的是，当程序访问(`a[3]`)时，我们遇到了另一个TLB丢失。但是，下一个条目(`a[4]..a[6]`)将再次位于TLB中，因为它们都位于内存中的同一个页上。

最后，访问(`a[7]`)会导致最后一个TLB丢失。硬件再次查询分页表以找出该虚拟分页在物理内存中的位置，并相应地更新TLB。最后两个访问(`a[8]`, `a[9]`)将获得此TLB更新的好处。当硬件在TLB中查找其转换时，又产生了两次命中。

让我们总结一下对数组的十次访问期间的TLB活动：miss, hit, hit, miss, hit, hit, hit, miss, hit, hit。因此，我们的TLB命中率为70%（实际上，我们希望命中率接近百分百）。即使这是程序第一次访问数组，TLB仍会由于空间位置而提高性能。数组的元素紧密地包装在页面中（即，它们在空间上彼此靠近），因此只有第一次访问页面上的元素才会产生TLB丢失。

还要注意在此栗子中页面大小的作用。如果页面大小是原来的两倍，则数组访问将遭受更小的丢失。由于典型的页面大小更像是4KB，因此这些类型的密集，基于数组的访问可实现出色的TLB性能，每页访问仅遇到一次丢失。

关于TLB性能的最后一点：如果程序在此循环完成后不久再次访问了数组，则假设我们有足够大的TLB来缓存所需的转换，那么我们可能会看到更好的结果: hit, hit, hit, hit, hit, hit, hit, hit, hit, hit。在这种情况下，由于时间上的局限性，即时间上的内存项快速重新引用，TLB命中率很高。像任何高速缓存(cache)一样，TLB依赖于空间和时间的局部性来获得成功，这是程序属性。如果感兴趣的程序表现出这种局限性，则TLB的命中率可能会很高。

<br>

> TIP: USE CACHING WHEN POSSIBLE
> Caching is one of the most fundamental performance techniques in computer systems, one that is used again and again to make the “commoncase fast” [HP06]. The idea behind hardware caches is to take advantage of locality in instruction and data references. There are usually two types of locality: temporal locality and spatial locality. With temporal locality, the idea is that an instruction or data item that has been recently accessed will likely be re-accessed soon in the future. Think of loop variables or instructions in a loop; they are accessed repeatedly over time. With spatial locality, the idea is that if a program accesses memory at address x, it will likely soon access memory near x. Imagine here streaming through an array of some kind, accessing one element and then the next. Of course, these properties depend on the exact nature of the program, and thus are not hard-and-fast laws but more like rules of thumb. Hardware caches, whether for instructions, data, or address translations (as in our TLB) take advantage of locality by keeping copies of memory in small, fast on-chip memory. Instead of having to go to a (slow) memory to satisfy a request, the processor can first check if a nearby copy exists in a cache; if it does, the processor can access it quickly (i.e., in a few CPU cycles) and avoid spending the costly time it takes to access memory (many nanoseconds).
> You might be wondering: if caches (like the TLB) are so great, why don’t we just make bigger caches and keep all of our data in them? Unfortunately, this is where we run into more fundamental laws like those of physics. If you want a fast cache, it has to be small, as issues like the speed-of-light and other physical constraints become relevant. Any large cache by definition is slow, and thus defeats the purpose. Thus, we are stuck with small, fast caches; the question that remains is how to best use them to improve performance.



<br/>
<br/>



### 谁处理TLB缺失

Who Handles The TLB Miss?


我们必须回答一个问题：谁处理TLB缺失？可能有两个答案：硬件或软件。

在过去，硬件具有复杂的指令集(complex instruction sets)（对于复杂指令集计算机，称为CISC），而构建硬件的人并不十分信任那些狡猾的操作系统用户。因此，硬件将完全处理TLB丢失。为此，硬件必须准确地知道分页表在内存中的位置（使用 page table base register），以及它们的确切格式。如果丢失，则硬件将遍历(walk)分页表，找到正确的分页表条目并提取所需的转换并更新TLB，然后重试指令。硬件管理的TLB的旧架构的一个示例是Intel x86架构，该架构使用固定的多级页表。

更加现代的架构（如，精简指令集计算机(reduced instruction set computers, RISC)）都具有所谓的软件管理的TLB。在发生TLB丢失时，硬件仅引发异常（如下代码），该异常将暂停当前指令流(instruction stream)，将权限级别提升至内核模式，然后跳转至陷阱处理程序(trap handler)。您可能会猜测，此陷阱处理程序是操作系统中的代码，其明确目的是处理TLB丢失。当它运行时，代码将在分页表中查找转换，使用特殊的特权指令(privileged instruction)更新TLB，然后从陷阱返回。此时，硬件重试指令（TLB将命中）。

```
// TLB Control Flow Algorithm (OS Handled)


VPN = (VirtualAddress & VPN_MASK) >> SHIFT
  (Success, TlbEntry) = TLB_Lookup(VPN)
  if (Success == True) // TLB Hit
      if (CanAccess(TlbEntry.ProtectBits) == True)
          Offset = VirtualAddress & OFFSET_MASK
          PhysAddr = (TlbEntry.PFN << SHIFT) | Offset
          Register = AccessMemory(PhysAddr)
      else
         RaiseException(PROTECTION_FAULT)
  else // TLB Miss
      RaiseException(TLB_MISS) //上面的引发异常
```

让我们讨论几个重要的细节。首先，从陷阱返回(return-from-trap)指令与我们在为系统调用提供服务之前所看到的从陷阱返回指令稍有不同。在后一种情况下，从陷阱返回应在陷阱进入操作系统之后的指令处恢复执行，就像从过程调用返回到调用该过程之后的指令一样。在前一种情况下，当从TLB丢失处理陷阱返回时，硬件必须在引起陷阱的指令处继续执行。否则，硬件将继续执行该指令。因此，此重试将使指令再次运行，这一次导致TLB命中。因此，根据导致陷阱或异常的方式，硬件必须在捕获到操作系统中时保存不同的PC，以便在需要时才能正确恢复。

其次，在运行TLB缺失处理代码时，操作系统需要格外小心，以免发生无限数量的TLB丢失链。存在许多解决方案。例如，您可以将TLB缺失处理程序保留在物理内存中（在这些内存中并未映射它们，并且不进行地址转换），或者在TLB中保留一些条目以进行永久有效的转换，并将在某些永久转换插槽用于处理程序代码本身。these wired translations always hit in the TLB.

软件管理方法的主要有点是灵活性：操作系统可以使用它实现想要实现分页表的任何数据结构，而无需更改硬件。从TLB控制流程中可以看出，另一个优点是简单。硬件在缺失方面没有做太多事情：仅引发异常，然后让操作系统TLB缺失处理程序完成其余工作。

<br>

> ASIDE: RISC VS. CISC
> In the 1980’s, a great battle took place in the computer architecture community. On one side was the CISC camp, which stood for Complex Instruction Set Computing; on the other side was RISC, for Reduced Instruction Set Computing [PS81]. The RISC side was spear-headed by David Patterson at Berkeley and John Hennessy at Stanford (who are also co-authors of some famous books [HP06]), although later John Cocke was recognized with a Turing award for his earliest work on RISC [CM00]. CISC instruction sets tend to have a lot of instructions in them, and each instruction is relatively powerful. For example, you might see a string copy, which takes two pointers and a length and copies bytes from source to destination. The idea behind CISC was that instructions should be high-level primitives, to make the assembly language itself easier to use, and to make code more compact.
> RISC instruction sets are exactly the opposite. A key observation behind RISC is that instruction sets are really compiler targets, and all compilers really want are a few simple primitives that they can use to generate high-performance code. Thus, RISC proponents argued, let’s rip out as much from the hardware as possible (especially the microcode), and make what’s left simple, uniform, and fast. 
> In the early days, RISC chips made a huge impact, as they were noticeably faster [BC91]; many papers were written; a few companies were formed (e.g., MIPS and Sun). However, as time progressed, CISC manufacturers such as Intel incorporated many RISC techniques into the core of their processors, for example by adding early pipeline stages that transformed complex instructions into micro-instructions which could then be processed in a RISC-like manner. These innovations, plus a growing number of transistors on each chip, allowed CISC to remain competitive. The end result is that the debate died down, and today both types of processors can be made to run fast.

<br>

> ASIDE: TLB VALID BIT 6= PAGE TABLE VALID BIT
> A common mistake is to confuse the valid bits found in a TLB with those found in a page table. In a page table, when a page-table entry (PTE) is marked invalid, it means that the page has not been allocated by the process, and should not be accessed by a correctly-working program. The usual response when an invalid page is accessed is to trap to the OS, which will respond by killing the process.
> A TLB valid bit, in contrast, simply refers to whether a TLB entry has a valid translation within it. When a system boots, for example, a common initial state for each TLB entry is to be set to invalid, because no address translations are yet cached there. Once virtual memory is enabled, and once programs start running and accessing their virtual address spaces, the TLB is slowly populated, and thus valid entries soon fill the TLB.
> The TLB valid bit is quite useful when performing a context switch too, as we’ll discuss further below. By setting all TLB entries to invalid, the system can ensure that the about-to-be-run process does not accidentally use a virtual-to-physical translation from a previous process.>



<br/>
<br/>



### TLB内容是什么

TLB Contents: What’s In There?


让我们更详细地了解硬件TLB的内容。一个典型的TLB可能有32、64或128个条目，这就是所谓的完全关联(a fully-associative)。基本上，这仅意味着任何给定的转换都可以在TLB中的任何位置，并且硬件将并行搜索整个TLB以找到所需的转换。一个TLB条目像这样：`VPN | PFN | other bits`。

请注意，VPN和PFN都存在于每个条目中，因为转换可能会发生在这些位置中的任何一个位置结束（就硬件而言，TLB被称为完全关联缓存）。硬件会并行搜索条目，以查看是否存在匹配项。

更有趣的是`other bits`。例如，TLB通常具有有效位，该位表明条目是否具有有效的转换。保护位也很常见，它们确定如何访问页面。例如，代码页可能被标记为read和execute，而堆页面可能被标记为read和write。可能还有一些其它字段，包括地址空间标识符、脏位...



<br/>
<br/>



### TLB问题：上下文切换

TLB Issue: Context Switches


使用TLB，在进程（以及地址空间）之间切换时会出现一些问题。具体来说，TLB包含从虚拟到物理的转换，这些转换仅对当前正在运行的进程有效，对其它进程没有意义。因此，从一个进程切换到另一个进程时，硬件或操作系统必须小心，以确保即将运行的进程不会意外地使用某些先前运行的进程的转换。

为了更好地了解这种情况，我们来看一个栗子。当一个进程(p1)正在运行时，它假定TLB可能正在缓存对其有效的转换，即来自p1的分页表的转换。对于此栗子，假定p1的第十个虚拟页面被映射到物理帧100。在此例中，假定存在领域给进程(p2)，并且操作系统很快可能会决定执行上下文切换并运行它。这里假设p2的第十个虚拟页面被映射到物理帧170。如果这两个进程的条目都在TLB中，则TLB的内容为：

```
vpn | pfn | valid | prot
10  | 100 |  1    | rwx
-   |  -  |  0    |  -
10  | 170 |  0    | rwx
-   |  -  |  0    |  -
```

在上面的TLB中，我们显然有一个问题：vpn(10) 转换为 pfn(100)(p1) 或 pfn(170)(p2)？但是硬件无法区分哪个条目代表哪个进程。因此，我们需要做更多的工作，以便TLB正确有效地支持跨多个进程的虚拟化。因此，症结如下：

> HOW TO MANAGE TLB CONTENTS ON A CONTEXT SWITCH
> When context-switching between processes, the translations in the TLB for the last process are not meaningful to the about-to-be-run process. What should the hardware or OS do in order to solve this problem?

有许多解决此问题的方法。一种方法是简单地**刷新**(flush)上下文切换上的TLB，从而在运行下一个进程之前将其清空。在基于软件的系统上，这可以通过明确的硬件指令来完成。如果使用硬件管理的TLB，则可以在更改page table base register时执行刷新操作。无论哪种情况，刷新操作都将所有有效位简单地设置为0，这实质上清除了TLB的内容。

通过刷新每个上下文切换上的TLB，我们现在有了一个可行的解决方案。因为一个进程将永远不会偶然在TLB中遇到错误的转换。但是，这是有代价的：每次运行进程时，它在接触其数据和代码页时都会导致TLB缺失。如果操作系统频繁在进程之间切换，则此成本可能很高。

为了减少这种开销，某些系统添加了硬件支持，以实现在上下文切换之间共享TLB。特别地，某些硬件系统在TLB中提供了**地址空间标识符**(address space identifier, ASID)字段。您可以将ASID视为与PID类似，只不过它的位数更少。

如果我们从上面的示例TLB并添加ASID，很明显，进程可以轻松共享TLB：仅需要ASID字段即可区分其它相同的转换。这是带有添加的ASID字段的TLB的描述：

```
vpn | pfn | valid | prot | ASID
10  | 100 |  1    | rwx  | 1
-   |  -  |  0    | -    | -
10  | 170 |  1    | rwx  | 2
-   |  -  |  0    | -    | -
```

因此，借助地址空间标识符，TLB可以同时保存来自不同进程的转换，而不会造成任何混淆。当然，硬件还需要知道当前正在运行哪个进程才能执行转换，因此操作系统必须在上下文切换时将一些特权寄存器(privileged register)设置为当前进程的ASID。

顺便说一句，您可能还想到了另一种情况。其中TLB的两个条目非常相似。在此示例中，两个不同进程的两个条目具有两个指向相同物理页面的不同VPN：

```
vpn | pfn | valid | prot | ASID
10  | 101 |  1    | r-x  | 1
-   | -   |  0    | -    | -
50  | 101 |  1    | r-x  | 2
-   | -   |  0    | -    | -
```

例如，当两个进程共享一个页面时，可能会出现这种情况。在上面的栗子中，p1与p2共享物理页面101。p1将该页面映射到其地址空间的第10页，而p2将该页面映射到其地址空间的第50页。共享代码页（在二进制文件或共享库中）很有用，因为它减少了正在使用的物理页面的数量，从而减少了内存开销。



<br/>
<br/>



### 问题：替换策略

Issue: Replacement Policy


与任何缓存一样，因此对于TLB，我们还必须考虑的另一个问题时**缓存替换**(cache replacement)。具体来说，当我们在TLB中安装新条目时，我们必须替换旧条目，从而产生了一个问题。我们要替换哪个条目？

<br>

> HOW TO DESIGN TLB REPLACEMENT POLICY
> Which TLB entry should be replaced when we add a new TLB entry? The goal, of course, being to minimize the miss rate (or increase hit rate) and thus improve performance.

<br>

在解决将页面交换到磁盘的问题时，我们将详细研究此类策略。在这里，我们只重点介绍一些典型的策略。一种常见的方法时逐出最近最少使用的LRU条目(least recently used)。LRU尝试利用内存引用流中的局部性，并假设最近未使用的条目很可能是驱逐的良好候选者。另一种典型的方法是使用随机策略，该策略会随机驱逐TLB映射。这样的策略由于其简单性和避免极端情况行为的能力而非常有用。



<br/>
<br/>



### 真实的TLB条目

A Real TLB Entry


最后，让我们简要看一下真正的TLB。该示例来自MIPS R4000。它是使用软件管理的TLB的现代系统。在图19-4中可看到简化的MIPS TLB条目。

MIPS R4000支持具有4KB页面的32位地址空间。因此，我们期望在典型的虚拟地址中有20位的VPN和12位的偏移量。但是，正如您在TLB中所看大的，VPN只有19位。事实证明，用户地址将仅来自地址空间的一般（其余部分保留给内核），因此仅需19位VPN。VPN最多可转换为24位物理帧(pfn)，因此可以支持具有64GB(物理)主内存(`2^24` 4KB pages)。

MIPS TLB中还有一些其它有趣的地方。我们看到一个全局为(global bit, G)，用于进程之间全局共享的页面。因此，如果设置了全局位，则将忽略ASID。我们也看到了8位ASID，操作系统可以使用它来区分地址空间。您遇到一个问题：如果一次运行的进程超过256(`2^8`)个，操作系统应怎么做？最后，我们看到3个 Coherence(C) bits，这些位确定硬件如何缓存页面。当页面被写入时被标记的脏位；一个有效位，该值高速硬件条目中是否存在有效的转换。还有一个页面掩码字段，它支持多种页面大小。我们将在后面看到为什么较大的页面可能会有用。最后，这64位中的一些未使用。

MIPS TLB通常包含32或64个条目，其中大多数在运行时由用户进程使用。但是，为操作系统保留了一些。可以由操作系统设置wired register，以告知硬件为操作系统保留多少个TLB插槽。操作系统会使用这些保留的映射来存储要在关键时间访问的代码和数据，而这在TLB丢失中会造成问题。

由于MIPS TLB是软件管理的，因此需要一些说明来更新TLB。MIPS 提供了以下四个这样的指令。操作系统使用这些说明来管理TLB的内容。当然，这些指令必须具有特权。想象一下，如果用户进程可以修改TLB的内容（几乎任何事情，包括接管机器，运行自己的恶意操作系统），该怎么办？

- TLBP，它探测TLB以查看其中是否存在特定转换；
- TLBR，它将TLB条目的内容读入寄存器；
- TLBWI，用于替换特定的TLB条目；
- TLBWR，它将替换随机的TLB条目。



<br/>
<br/>



### 总结

Summary


我们已经了解了硬件如何帮助我们更快地进行地址转换。通过提供一个小型的专用片上TLB作为地址转换缓存，可以希望处理大多数内存引用，而不必访问主内存中的分页表。因此，在通常情况下，程序的性能几乎就像完全没有对内存进行虚拟化一样，这对于操作系统来说是一项出色的成就，并且对于现代操作系统中的分页使用必不可少。

但是，TLB并不会使每个存在的程序都变得乐观。特别是，如果程序在短时间内访问的页面数据超过了适合TLB的页面数，则该程序生成大量的TLB丢失，因此运行起来会慢得多。我们称这种现象超出了TLB的覆盖范围(coverage)，对于某些程序来说可能是个问题。正如我们将在下一章中讨论的那样，一种解决方案是包括对更大页面尺寸的支持。通过将关键数据结构映射到程序地址空间的较大页面所映射的区域中，可以提高TLB的有效覆盖率。

对大型页面的支持经常被诸如数据库管理系统(DBMS)之类的程序所利用，这些程序具有某些即大又随机访问的数据结构。

值得一提的另一个TLB问题：TLB访问很容易成为CPU pipeline的瓶颈，尤其是所谓的物理索引缓存。使用这种高速缓存，必须在访问高速缓存之前进行地址转换，这可能会大大降低速度。由于存在这个潜在的问题，人们已经研究了各种巧妙的方法来访问具有虚拟地址的缓存，从而避免在缓存命中的情况下进行昂贵的转换步骤。这种虚拟索引的缓存解决了一些性能问题，但也将新问题引入了硬件机制。




<br/>
<br/>
<br/>




## 高级的分页表

Advanced page table

Paging: Smaller Tables


现在，我们解决介绍引入的第二个问题：分页表太大，因此消耗了太多内存。从线性分页表开始，您可能还记得，线性分页表变得很大。再次假定一个32位地址空间(`2^32`Bytes)，其中包含4KB(`2^12`Bytes)分页和4Bytes分页表条目。因此，一个地址空间中大约有一百万个虚拟页面(`2^32/2^12`)，乘以分页表条目大小，您会发现我们的分页表大小为4MB。还记得，我们通常为系统中的每个进程提供一个分页表！拥有一百个活跃的进程（在现代系统中并不罕见），我们仅为分页表分配数百兆的内容！结果，我们正在寻找减轻这种沉重负担的一些技术。这里有很多，让我们看看症结在哪：

> HOW TO MAKE PAGE TABLES SMALLER?
> Simple array-based page tables (usually called linear page tables) are too big, taking up far too much memory on typical systems. How can we make page tables smaller? What are the key ideas? What inefficiencies arise as a result of these new data structures?



<br/>
<br/>



### 简单的解决方案：更大的页面

Simple Solution: Bigger Pages


我们可以通过一种简单的方法来减少分页表的大小：使用更大的页面。再次使用32位地址空间，但这次假设使用16KB页面。因此，我们将拥有一个18位的VPN和一个14位的偏移量。假设每个PTE的大小相同(4Bytes)，则我们现在有`2^18`个条目，因此每个分页表的总大小为1MB，这是分页表大小减少4倍的原因。

但是，这种方法的主要问题在于，大页面会导致每个页面内部的浪费，这就是内部化碎片的问题。因此，应用程序最终只能分配页面，但每个页面只使用很少的点滴，而内存很会被这些过大的页面填满。因此，在通常情况下，大多数系统使用相对较小的页面大小：4KB(x86)或8KB(SPARC V9)。

> ASIDE: MULTIPLE PAGE SIZES
> As an aside, do note that many architectures (e.g., MIPS, SPARC, x86-64) now support multiple page sizes. Usually, a small (4KB or 8KB) page size is used. However, if a “smart” application requests it, a single large page (e.g., of size 4MB) can be used for a specific portion of the address space, enabling such applications to place a frequently-used (and large) data structure in such a space while consuming only a single TLB entry. This type of large page usage is common in database management systems and other high-end commercial applications. The main reason for multiple page sizes is not to save page table space, however; it is to reduce pressure on the TLB, enabling a program to access more of its address space without suffering from too many TLB misses. However, as researchers have shown [N+02], using multiple page sizes makes the OS virtual memory manager notably more complex, and thus large pages are sometimes most easily used simply by exporting a new interface to applications to request large pages directly



<br/>
<br/>



### 混合方法：分页和段

Hybrid Approach: Paging and Segments


每当您对生活中的某件事有两种合理但不同的方法时，都应始终检查这两种情况的组合，以了解是否可以同时兼顾两者。我们称这种组合为**混合**(hybrid)。

多年前，Multics的创造者在Multics虚拟内存系统的构建中碰巧出现了这种想法。具体来说，他将分页和分段结合在一起，以减少分页表的内存开销。我们可以通过更详细地检查典型的线性分页表来了解为什么这样做可行。假设我们有一个地址空间，其中堆和栈的已用部分很小。在此栗中，我们使用一个很小的16KB的地址空间和1KB页面（图20-1）。此地址空间和分页表在图20-2中。

![20-1](/images/OSTEP/20-1.png)

![20-2](/images/OSTEP/20-2.png)

此例中假定单个代码页(vpn 0)映射到pfn 10， 单个堆页面(vpn 4)映射到pfn 23，地址空间另一端的两个栈页面(vpn 14, 15)分别映射到(pfn 28, 4)。从图中可看出，大多数分页表都是未使用的，充满了无效的条目。真是浪费！这是一个很小的16KB地址空间。想象一下32位地址空间的分页表以及其中所有潜伏的浪费空间。太可怕了！

因此，我们采用了混合方法：不在进程的整个地址空间中使用分页表，而在每个逻辑段(logical segment)中使用分页表。因此，在此栗子中，我们可能有三个分页表：一个用于代码，一个用于堆、一个用于栈。

现在，请记住分段。我们有一个base register，告诉我们每个段在物理内存中的位置，还有一个bound register，告诉我们该段的大小。在我们的混合方法中，我们在MMU中仍然具有这些结构。在这里，我们使用base不是指向该段本身，而是保存该段的分页表的物理地址。bound register用于指示分页表的末尾（即，它具有多少有效页面）。

一个栗子。假定一个具有4KB页面的32位虚拟地址空间，并将一个地址空间分为四段。在此栗中，我们仅使用三个段：code, heap, stack。为了确定地址所指向的段，我们将使用地址空间的前两位。假设00是未使用的段，其中01代表代码，10代表堆，11代表栈。因此，虚拟地址可能像这样：

![](/images/OSTEP/20-1122.png)

因此，在硬件中，假设有三对 base/bound，每对用于代码、堆、栈。当进程正在运行时，这些段中的base register都包含该段线性分页表的物理地址。因此，系统中的每个进程现在都具有与其关联的三个分页表。在上下文切换时，必须更改这些寄存器以反映新运行的进程的分页表的位置。

在TLB丢失时（假设由硬件管理TLB），硬件使用segment bits(SN)来确定要使用的base/bound pair。然后，硬件在其中获取物理地址，并将其与VPN结合，如下所示以形成分页表项(PTE)的地址：

```
SN = (VirtualAddress & SEG_MASK) >> SN_SHIFT
VPN = (VirtualAddress & VPN_MASK) >> VPN_SHIFT
AddressOfPTE = Base[SN] + (VPN * sizeof(PTE))
```

这个顺序看起来应该很熟悉。它实际上与我们之前在线性分页表中看到的相同。当然，唯一的区别是使用了三个 segment base registers，而不是一个。

混合方案中的关键差异是每个段都有一个base register，基寄存器保存该段中最大有效页面的值。例如，如果代码正在使用其前三个页面(0, 1, 2)，则代码段分页表将仅分配三个条目，并且bound register将设置位3。段末尾以外的内存访问将产生异常，并可能导致进程终止。通过这种方式，混合方法与线性分页表相比节省了大量内存。栈和堆之间未分配的页面不在占用分页表中的空间（只是将其标记为无效）。

但是，您可能会注意到，这种方法并非没有问题。首先，它仍然需要我们使用分段。正如前面所讨论的，分段并不像我们所希望的那样灵活，因为它假定了地址空间的某种使用模式。例如，如果我们有一个很大但稀疏使用的堆，那么仍然会导致很多分页表浪费。其次，这种混合方法导致外部碎片再次出现。尽管大多数内存以页面大小为单位进行管理，但现在分页表可以具有任意大小(pte的倍数)。因此，在内存中为其找到空闲空间更加复杂。由于这些原因，人们继续寻找更好的方法来实现较小的分页表。

> TIP: USE HYBRIDS
> When you have two good and seemingly opposing ideas, you should always see if you can combine them into a hybrid that manages to achieve the best of both worlds. Hybrid corn species, for example, are known to be more robust than any naturally-occurring species. Of course, not all hybrids are a good idea; see the Zeedonk (or Zonkey), which is a cross of a Zebra and a Donkey. If you don’t believe such a creature exists, look it up, and prepare to be amazed.



<br/>
<br/>



### 多级分页表

Multi-level Page Tables


一种不同的方法不依赖于分段，而是攻击相同的问题：如果摆脱分页表中所有无效区域，而不是将它们全部保留在内存中？我们将这种方法称为**多级分页表**(multi level page table)，因为它将线性分页表变成了像树一样的东西。这种方法非常有效，以至于许多现代操作系统都采用这种方法（如，x86）。

多级分页表的基本思想很简单。首先，将页面切成页面大小(page-sized)的单位。然后，如果整个分页表条目(pte)无效，则根本不要分配该分页表的整个页面。若要追踪分页表的页面是否有效（如果有效，则在内存中的位置），请使用称为**页目录**(page directory)的新结构。因此，页目录可以用来告诉您分页表的页面在哪里，或者分页表的整个页面不包含有效页面。

![20-3](/images/OSTEP/20-3.png)

图20-3显示了一个栗子。图的左侧是经典的线性分页表。即地址空间的大多数中间区域无效，我们仍然需要为这些区域分配分页表空间。右边是一个多级分页表。页面目录仅将分页表的两个页面标记为有效（第一个和最后一个）。因此，只有分页表的那两个页驻留在内存中。因此，您可以看到一种可视化多级表正在执行的方法：它只是使线性分页表的某些部分消失（将那些框架释放出来供其它用途），并跟踪分页表中的哪些页与页目录一起分配。

页目录在一个简单的两级表中，在分页表的每页中包含一个条目。它由许多页目录条目(page directory entry, PDE)组成。PDE（至少）具有一个有效位(valid bit)和一个页帧号(pfn)，类似于PTE。但是，如前所述，该有效位的含义略有不同：如果PDE有效，则意味着该条目指向的分页表中的至少一个页面(通过pfn)是有效的，即在此PDE指向的页面上的至少一个PTE中，该PTE中的有效位设置为1.如果PDE无效(0)，则未定义PDE的其余部分。

到目前为止，多级分页表具有一些明显的优势。首先，也许是最明显的是，多级表仅根据您正在使用的地址空间量分配分页表空间。因此，它通常是紧凑的，并且支持稀疏的地址空间。其次，如果精心构建，分页表的每个部分都可以整齐地放在一个页面中，从而更易于管理内存。当操作系统需要分配或增加分页表时，操作系统可以简单地获取下一个空闲页。将此与一个简单的（非分页）线性分页表进行对比，该表只是一个由VPN索引的PTE数组。采用这种结构，整个线性分页表必须连续地驻留在物理内存中。对于大的分页表(如，4MB)，找到这么大的未使用的连续可用物理内存可能是一个挑战。通过多级结构，我们通过使用页面目录添加了一个间接级别(level of indirection)，该目录指向分页表的各个部分。这种间接允许我们将分页表页面放置在物理内存中的任何位置。

应该注意的是，多级表是有代价的。在TLB丢失时，将需要两次从内存中加载来从分页表中获取正确的转换信息（一个用于页目录，一个用于PTE自身），而线性分页表仅需要一次加载。因此，多级表只是时空(time-space)权衡(trade-off)的一个小栗子。我们想要更小的表（并得到它），但不是免费的。尽管在常见情况下(TLB hit)，性能显然是相同的，但使用较小的表会导致TLB丢失而导致成本较高。

另一个明显的负面影响是复杂性(complexity)。无论是硬件还是操作系统处理分页表查找，与简单的线性分页表查找相比，这样做无疑涉及更多。通常，我们愿意增加复杂性以提高性能或减少开销。对于多级表，为了节省宝贵的内存，我们使分页表查找更加复杂。

<br>

> TIP: UNDERSTAND TIME-SPACE TRADE-OFFS
> When building a data structure, one should always consider time-space trade-offs in its construction. Usually, if you wish to make access to a particular data structure faster, you will have to pay a space-usage penalty for the structure.

<br>

**详细的多级示例**
A Detailed Multi-Level Example

为了更好地理解多级分页表背后的思想，让我们举个栗子。想象一下一个小的16KB地址空间，具有64Bytes的页面。因此，我们有一个14位的虚拟地址空间，其中8位用于VPN，6位用于offset。即使只使用一小部分地址空间，线性分页表也将具有(`2^8=256`)个条目。图20-4给出了这种地址空间的一个栗子。

![](/images/OSTEP/20-4.png)

在此栗子中，vp 0, 1用于代码，vp 4, 5用于堆，vp 254, 255用于栈。地址空间的其余页面未使用。为了为该地址空间构建要给两级分页表，我们从完整的线性分页表开始，然后将其分解为页面大小的单元。假设每个PTE的大小为4Bytes。因此，我们的分页表大小为1KB(`256*4Bytes`)。假设有64Bytes的页面，则1KB分页表可以分为16个64Bytes的页面。每个页面可以容纳16个PTE。现在，我们需要了解的是如何使用VPN并使用它索引到页面目录，然后再索引到分页表的页面。请记住，每个条目都是一个数组。因此，我们需要弄清楚的是如何从VPN的各个部分构建索引。

让我们首先索引页目录。在此示例中，我们的分页表很小：256个条目，分布在16个页面上。页目录在分页表的每页上需要一个条目。因此，我们需要VPN的四位索引到目录中。我们使用VPN的前四位，如下：

![page-directory-index](/images/OSTEP/page-directory-index.png)

一旦从VPN中提取了页目录索引(page-directory index, PDIndex)，我们就可以使用它通过简单的计算来找到页目录条目(PDE)的地址：`PDE Addr = PageDirBase + (PDIndex*sizeof(PDE))`。此结果在我们的页目录中，现在我们将对其进行检查以在转换中取得进一步的进展。

如果页目录条目被标记为无效，则我们知道该访问无效。因此引发异常。但是，如果PDE有效，我们还有更多工作要做。具体来说，我们现在必须从此页目录条目指向的分页表的页面中获取分页表条目(PTE)。要找到此PTE，我们必须使用VPN的其余位来索引分页表的一部分：

![](/images/OSTEP/page-directory-index-2.png)

然后可以使用此分页表索引(PTIndex)来索引分页表本身，从而为我们提供PTE的地址：

```
PTEAddr = (PDE.PFN << SHIFT) + (PTIndex * sizeof(PTE))

// >>
```

请注意，从页目录获得的页面帧号(pfn)必须显左移到适当位置，然后再将其与分页表索引组合以形成PTE的地址。在图20-5中，您可以看到每个页目录条目(PDE)都描述了有关地址空间的分页表页面的内容。在这个例子中，我们在地址空间（开头和结尾）两个有效的区域，以及一些无效的映射在两者之间的。在物理页100（分页表第0页的物理帧号）中，我们在地址空间中具有前16个VPN的16页分页表条目的第一个页面。有关分页表此部分的内容，请参见图20-5。

分页表的此页面包含前16个映射VPN。在此例中，vpn 0, 1有效()code，vpn 4, 5有效(heap)。因此，该表具有每个页面的映射信息。其余条目被标记为无效。

![20-5](/images/OSTEP/20-5.png)

分页表的另一个有效页位于pfn 101内。此页包含地址空间的最后16个VPN的映射。

在该栗子中，vpn 254, 255(stack)具有有效的映射。希望从本示例中可以看到，使用多级索引结构可以节省多少空间。在此示例中，我们没有为线性分页表分配完整的十六个页面，而是仅分配了三个：一个用于页目录，两个用于具有映射的分页表的块。大型地址空间(32bit/64bit)的节省显然会更大。

最后，让我们使用此信息来执行转换。这是引用VPN 254的第0个字节的地址：0x3F80或二进制的11 111 1000 0000。

回想一下，我们将使用VPN的高4位索引到页目录。因此，1111将选择上面页目录的最后一个条目。这将我们指向位于地址101的分页表的有效页。然后，我们使用VPN的下4位(1110)索引到分页表的该页并找到所需的PTE。1110是页面上的倒数第二个条目，它告诉我们虚拟地址空间的页面254映射到物理页面55。通过将PFN=55（或十六进制`0x37`)与`offset=000000`串联，我们这样就可以形成我们想要的物理地址，并向存储系统发出请求：

```
PhysAddr = (PTE.PFN << SHIFT) + offset = 00 1101 1100 0000 = 0x0DC0

// >>
```

您现在应该对如何使用指向分页表页面的页目录构造一个二级分页表有所了解。然而不幸的是，我们的工作还没有完成。正如我们现在要讨论的那样，有时两个级别的分页表是不够的。

<br>

**不止两级**
More Than Two Levels

到目前为止，在示例中。我们假设多级分页表只有两个级别：页目录，然后是分页表的各个部分。在某些情况下，更深的树是可能的（确实的、必需的）。

举个栗子，并用它来说明为什么更深层的多级表很有用。假设有一个30位的虚拟地址空间和一个小的页面(512Bytes)。因此，我们的虚拟地址具有21位的虚拟页码部分和9位偏移量。记住我们构建多级分页表的目标：使分页表的每个部分都适合单个页面。到目前为止，我们仅考虑了分页表本身。但是，如果页目录太大，该怎么办？

为了确定多级表中需要多少级才能使分页表的所有部分都适合一个页面，我们首先确定一个页面中可以容纳多少分页表条目。给定的页面大小位512Bytes，并假设PTE大小为4Bytes，您应该看到可以在单个页面上容纳128个PTE。当我们索引到分页表的页面时，我们将需要vpn的最低有效位7位(`2^7=128`)作为索引：

![](/images/OSTEP/page-directory-index-3.png)

您还可能从上图中注意到，页目录中还剩下多少位：14。如果我们的页目录有`2^14`个条目，那么它跨越的不是一页而是128个，因此我们使多级分页表的每个部分都适合一个页面的目标就消失了。

为了解决此问题，我们通过将页目录本身拆分为多个页面，然后在该页面之上添加另一个页目录，以指向该页目录的页面，来构建树的进一步层次。因此，我们可以如下拆分虚拟地址：

![](/images/OSTEP/page-directory-index-4.png)

现在，在索引上级(upper-level)页目录时，我们使用虚拟地址的最高位（PDIndex 0）。该索引可用于从顶级页目录中获取页目录条目。如果有效，则通过组合来自顶层PDE和VPN的下一部分(PDIndex 1)的物理帧号来查询页目录的第二层。最后，如果有效，则可以通过使用页表索引与第二级PDE中的地址相结合来形成PTE地址。这是很多工作，所有这些只是为了在多级表中查找内容。

<br>

**转换进程：记住TLB**
The Translation Process: Remember the TLB

为了总结使用两级分页表的地址转换的整个过程，我们再次以算法形式给出如下控制流。它显示了在每个内存引用中硬件中发生的情况（假设由硬件管理的TLB）。

```
// Multi-level Page Table Control Flow


VPN = (VirtualAddress & VPN_MASK) >> SHIFT
(Success, TlbEntry) = TLB_Lookup(VPN)
if (Success == True) // TLB Hit
  if (CanAccess(TlbEntry.ProtectBits) == True)
    Offset = VirtualAddress & OFFSET_MASK
    PhysAddr = (TlbEntry.PFN << SHIFT) | Offset
    Register = AccessMemory(PhysAddr)
  else
    RaiseException(PROTECTION_FAULT)
else // TLB Miss
  // first, get page directory entry
  PDIndex = (VPN & PD_MASK) >> PD_SHIFT
  PDEAddr = PDBR + (PDIndex * sizeof(PDE))
  PDE = AccessMemory(PDEAddr)
  if (PDE.Valid == False)
    RaiseException(SEGMENTATION_FAULT)
  else
  // PDE is valid: now fetch PTE from page table
  PTIndex = (VPN & PT_MASK) >> PT_SHIFT
  PTEAddr = (PDE.PFN << SHIFT) + (PTIndex * sizeof(PTE))
  PTE = AccessMemory(PTEAddr)
  if (PTE.Valid == False)
    RaiseException(SEGMENTATION_FAULT)
  else if (CanAccess(PTE.ProtectBits) == False)
    RaiseException(PROTECTION_FAULT)
  else
    TLB_Insert(VPN, PTE.PFN, PTE.ProtectBits)
    RetryInstruction()

// >>over
```

从图中可以看出，在进行任何复杂的多级页表访问之前，硬件首先检查TLB。命中后，就像以前一样直接形成物理地址，而根本不访问分页表。只有在TLB未命中时，硬件才需要执行完整的多级查找。在这条路径上，您可以看到传统的两级分页表的成本：两个额外的内存访问以查找有效的转换。



<br/>
<br/>



### 反向分页表

Inverted Page Tables


使用**反向分页表**(inverted page tables)可以在分页表世界中节省更多空间。在这里，没有许多分页表（每个系统进程一个），而是保留了一个分页表，该表为系统的每个物理页面都有一个条目。该条目告诉我们哪个进程正在使用该页面，以及该进程的哪个虚拟页面映射到该物理页面。

现在，查找正确的条目只是搜索此数据结构的问题。线性扫描会很昂贵，因此通常在基本结构上构建哈希表以加快查找速度。PowerPC就是这种架构的一个示例。

更一般而言，反向分页表从一开始就说明了我们所说的话：分页表只是数据结构。您可以使用数据结构做很多疯狂的事情，使它们变小或变大、变快或变慢。多级和反向分页表只是一个人可以做的许多事情的两个栗子。



<br/>
<br/>



### 将分页表交换到磁盘

Swapping the Page Tables to Disk

最后，我们讨论一个最终假设。到目前为止，我们已经假定分页表驻留在内核拥有的物理内存中。即使我们有很多减少分页表大小的技巧，但是仍然有可能它们太大而无法一次全部放入内存中。因此，某些系统将这样的分页表放置在内核虚拟内存(kerl virtual memory)中，从而允许系统在内存压力变紧时将这些分页表中的某些交换(swap)到磁盘上。一旦我们了解了如何更详细将页面移入和移出内存，我们将在以后的章节中进一步讨论这一点。



<br/>
<br/>



### 总结

Summary


现在，我们已经看到了如何构建真实的分页表。不一定只是线性数组，而是更复杂的数据结构。这种表的权衡是在时间和空间上进行的，表越大，TLB丢失可以得到更快的服务。反之亦然。因此，结构的正确选择在很大程度上取决于给定环境的约束。

在受内存限制的系统中（旧的系统），小型结构是有意义的。在内存合理且工作负载主动使用大量页面的系统中，加快TLB丢失速度的较大表可能是正确的选择。借助软件管理的TLB，整个数据结构空间将为操作系统创新者带来惊喜。




<br/>
<br/>
<br/>




## 交换机制

Beyond Physical Memory(Swap) Mechanisms: <http://pages.cs.wisc.edu/~remzi/OSTEP/vm-beyondphys.pdf>


到目前为止，我们已经假定地址空间不切实际地小并且适合物理内存。实际上，我们一直假设每个运行进程的每个地址空间都适合内存。现在，我们将放宽这些大的假设，并假设我们希望支持许多同时运行的大地址空间。为此，我们需要在内存层次结构中增加一个级别。到目前为止，我们已经假设所有页面都驻留在物理内存中。但是，为了支持较大的地址空间，操作系统将需要一个地方来存放当前需求不大的部分地址空间。通常，这种位置的特征是它的容量应大于内存。结果，它通常会比较慢。在现代系统中，通常由硬盘驱动器(hard disk drive)担当此角色。因此，在我们的内存层次结构中，大而慢的硬盘驱动器位于底部，而内存位于上方。因此，我们得出了问题的症结：

> THE CRUX: HOW TO GO BEYOND PHYSICAL MEMORY
> How can the OS make use of a larger, slower device to transparently provide the illusion of a large virtual address space?

您可能会有一个问题，为什么我们要为一个进程支持一个大地址空间？答案再次是方便和易用。地址空间很大，您不必担心程序中的数据结构是否有足够的内存空间。相反，您只是自然地编写程序，并根据需要分配内存。操作系统提供了一种强大的幻觉，使您的生活大大简化。在使用内存覆盖(memory overlays)的旧系统中发现了一种对比，这要求程序员在需要时手动将代码或数据片段移入或移出内存。试想一下这是什么样的：在调用函数或访问某些数据之前，您需要首相将代码或数据安排在内存中。

除了单个进程外，交换空间(swap space)的增加还使操作系统能够为多个同时运行的进程提供大型虚拟内存的支持。多程序的发明（一次运行多个程序，以更好地利用机器）几乎需要交换掉某些页面的能力，因为早期的机器显然不能同时容纳所有进程所需的所有页面。因此，多程序和易用性的结合使我们想要支持使用比物理可用内存更多的内存。所有现代虚拟内存系统都可以做到这一点。现在，我们将了解更多信息。

<br>

> ASIDE: STORAGE TECHNOLOGIES
> We’ll delve much more deeply into how I/O devices actually work later (see the chapter on I/O devices). So be patient! And of course the slower device need not be a hard disk, but could be something more modern such as a Flash-based SSD. We’ll talk about those things too. For now, just assume we have a big and relatively-slow device which we can use to help us build the illusion of a very large virtual memory, even bigger than physical memory itself.



<br/>
<br/>



### 交换空间

Swap Space


我们需要做的第一件事是在磁盘上保留一些空间来回移动页面。在操作系统中，我们通常将此类空间称为**交换空间**(swap space)，因为我们将页面从内存中交换到该空间，并将页面从其中交换到内存。因此，我们仅假设操作系统可以以页面大小为单位读取和写入交换空间。为此，操作系统需要记住给定页面的磁盘地址(disk space)。

交换空间的大小很重要，因为它最终决定了系统在给定时间可以使用的最大内存页面数量。为了简单起见，让我们假设它现在很大。

在图21-1的栗子中，您可以看到一个4页面物理内存和8页面交换空间。在此例中，三个进程(P0, P1, P2)正在主动共享物理内存。但是，这三个中的每一个在内存中仅具有其部分有效页面，区域部分位于磁盘上的交换空间中。第四个进程(P3)将所有页面交换到磁盘，因此显然当前未运行。一块交换空间仍然是空闲的。即使从这个很小的栗子，也希望您能看到使用交换空间如何使系统假装内存大于实际内存。

![21-1](/images/OSTEP/21-1.png)

我们应该注意到，交换空间不是交换流量(swapping traffic)的唯一磁盘位置。例如，假设您正在运行一个二进制程序（如，ls）。最初在磁盘上找到此二进制文件的代码页(code pages)，并且在程序运行时将它们加载到内存中（既可以在程序开始执行时一次全部加载，也可以在现代系统中需要时一次加载一页）。但是，如果系统需要在物理内存中腾出空间来满足其它需求，则可以安全地重用这些代码页的存储空间，因为它知道以后可以从文件系统中的磁盘二进制文件中再次交换它们。



<br/>
<br/>



### 当前位

The Present Bit


现在，我们有一些空间在磁盘上。我们需要在系统中增加一些机制，以支持在磁盘之间交换页面。为了简单起见，让我们假设我们有一个带有硬件管理的TLB的系统。

首先回想一下内存引用中发生的情况。正在运行的进程会生成虚拟内存引用（用于指令提取，或数据访问），在这种情况下，硬件会在从内存中提取所需数据之前将其转换为物理地址。

请记住，硬件首先从虚拟地址中提取VPN，检查TLB是否匹配（TLB hit）。如果命中，将产生结果的物理地址并从内存中获取它。希望这是常见的情况，因为它速度很快（不需要额外的内存访问）。

如果在TLB中未找到VPN（TLB miss），则硬件会在内存中找到分页表（使用page table base register)，并使用VPN作为索引查找该页的分页表条目(PTE)。如果该页面有效并且存在于物理内存中，则硬件将从PTE中提取PFN，并将其写入TLB，然后重试指令，这一次将产生TLB命中，到目前为止，岁月静好。

但是，如果我们希望将页面交换到磁盘，则必须添加更多的设备。具体来说，当硬件在PTE中查找时，它可能会发现内存中不存在(not present)该页面。硬件确定这一点的方式是通过每个分页表条目中的一条信息——称为**当前位**(present bit)。如果当前位设置为1，则意味着该页存在于物理内存中，并且一切按上述步骤进行；如果其设置为0，则该页面不在内存中，而是在磁盘上的某个位置。访问不在物理内存中的页面的行为通常称为**页面错误**(page fault)。

出现页面错误时，将调用操作系统来服务该页面错误。正如我们现在所描述的，称为页面错误处理程序(page-fault handler)的特定代码运行，并必须服务于页面错误。

<br>

> ASIDE: SWAPPING TERMINOLOGY AND OTHER THINGS
> Terminology in virtual memory systems can be a little confusing and variable across machines and operating systems. For example, a page fault more generally could refer to any reference to a page table that generates a fault of some kind: this could include the type of fault we are discussing here, i.e., a page-not-present fault, but sometimes can referto illegal memory accesses. Indeed, it is odd that we call what is definitely a legal access (to a page mapped into the virtual address space of a process, but simply not in physical memory at the time) a “fault” at all; really, it should be called a page miss. But often, when people say a program is “page faulting”, they mean that it is accessing parts of its virtual address space that the OS has swapped out to disk.
> We suspect the reason that this behavior became known as a “fault” relates to the machinery in the operating system to handle it. When something unusual happens, i.e., when something the hardware doesn’t know how to handle occurs, the hardware simply transfers control to the OS, hoping it can make things better. In this case, a page that a process wants to access is missing from memory; the hardware does the only thing it can, which is raise an exception, and the OS takes over from there. As this is identical to what happens when a process does something illegal, it is perhaps not surprising that we term the activity a “fault.”



<br/>
<br/>



### 页面错误

The Page Fault


回想一下，对于TLB未命中，我们有两种类型的系统：硬件管理的TLB和软件管理的TLB。在这两种类型的系统中，如果页面不存在，则由操作系统负责处理页面错误。操作系统页面错误处理程序将运行以确定要执行的操作。实际上，所有系统都可以处理软件中的页面错误。即使使用硬件管理的TLB，硬件也信任操作系统来管理这一重要职责。

如果页面不存在并且已被交换到磁盘，则操作系统需要将页面交换到内存中已解决页面错误。因此，出现一个问题：操作系统如何知道在哪里可以找到所需的页面？在许多系统中，分页表时存储此类信息的天然场所。因此，操作系统可以将通常用于数据(pfn)的PTE中的位用于磁盘地址。当操作系统收到页面的页面错误时，它会在PTE中查找地址，然后向磁盘发出请求以将该页面读取到内存中。

磁盘I/O完成后，操作系统将更新分页表以将该页面标记为present，更新分页表条目(pte)的pfn字段以记录新获取的页面在内存中的位置，然后重试指令。下一个尝试可能会生成TLB未命中，然后服务并使用转换来更新TLB（为错误页面提供服务时，可以交替更新TLB以避免此步骤）。最后，最后一次重新启动将在TLB中找到转换，并因此从转换后的物理地址出的内存中获取所需的数据或指令。

请注意，在运行I/O时，该进程将处于阻塞状态(blocked state)。因此，在为页面错误提供服务时，操作系统将可以自由运行其它准备就绪的进程。因为I/O昂贵，所以一个进程的I/O和另一个进程的执行的这种重叠(overlap)是多程序系统可以最有效地利用其硬件的另一种方式。



<br/>
<br/>



### 如果内存满了怎么办

What If Memory Is Full?


在上述描述中，您可能会注意到我们假设有足够的可用内存在交换空间中的页面中进行分页。然而，事实并非如此。内存可能已满。因此，操作系统可能希望首先剔出一个或多个页面，以便为操作系统将要引入的新页面腾出空间。挑选要踢出或替换的页面的过程称为**页面替换策略**(page-replacement policy)。

事实证明，创建一个好的页面替换策略引起了很多思考，因为踢错页面(kicki out of the wrong page)可能会导致程序性能损失巨大。错误的决定会导致程序以磁盘速度而不是内存速度运行。在目前的技术中，这意味着程序的运行可能会慢一万倍。因此，这种政策是我们应该详细研究的。实际上，这正是我们将在下一章中进行的操作。到目前为止，了解存在这样的策略已经足够了。



<br/>
<br/>



### 页面错误控制流

Page Fault Control Flow


掌握了所有这些知识之后，我们现在可以大致概述内存访问的完整控制流程。换句话说，当有人问您程序从内存中获取一些数据时会发生什么？您应该对所有不同的可能性都有一个很好的了解。有关更多详细信息，请参考图21-2和21-3的控制流程。第一个图显示了硬件在转换过程中的作用，第二个图显示了页面错误时操作系统的作用。

![21-2](/images/OSTEP/21-2.png)

![21-3](/images/OSTEP/21-3.png)

从图21-2的硬件控制流程图中，请注意，当发生TLB丢失时，现在需要了解三个重要情况：

- 首先，该页面既存在又有效(line 18-21)。在这种情况下，TLB未命中处理程序可以简单地从PTE中获取PFN，重试指令（这一次导致TLB命中），从而按照前面所述继续操作。
- 在第二种情况下(line 22-23)，必须运行页面错误处理程序。尽管这是供进程访问的合放页面（毕竟它是有效的），但它不存在于物理内存中。
- 第三，访问可能是无效页面。例如由于程序中的错误(line 13-14)。在这种情况下，PTE中的其它位都不重要。硬件会批捕此无效访问，并且操作系统给陷阱处理程序将运行，可能会终止有问题的进程。

从图21-3的软件控制流程中，我们可以看到操作系统大致必须执行的操作才能解决页面错误。首先，操作系统必须为即将出现故障的页面找到驻留在其中的物理框架。如果没有这样的页面，我们将不得不等待替换算法进行并将其部分页面踢出内存，从而释放它们供此处使用。有了一个物理框架，处理程序便发出I/O请求以从交换空间中读取页面。最后，当该缓慢的操作完成时，操作系统将更新分页表并重试该指令。重试将导致TLB丢失，然后在再次重试时TLB命中，此时硬件将能够访问所需的项目。



<br/>
<br/>



### 真正发生替换时

When Replacements Really Occur


到目前为止，我们描述替换的方式是假设操作系统一直等到内存完全用完，然后才替换（逐出）页面为其它页面腾出空间。可以想象，这有点不切实际，并且操作系统有很多原因可以更主动地释放一小部分内存。

为了使少量内存可用，大多数操作系统因此具有某种**高水位**(high watermark, HW)和**低水位**(low watermark, LW)来帮助确定何时开始从内存中逐出页面。它的工作方式如下：当操作系统注意到可用的页面少于LW时，负责释放内存的后台线程将运行。该线程逐出页面，直到有可用的HW为止。后台线程（有时称为swap daemon，或page daemon）随后进入睡眠状态，很高兴它释放了一些内存以供正在运行的进程和操作系统使用。

通过一次执行许多替换，新的性能优化成为可能。例如，许多系统将集群(cluster)和分组(group)并将它们立即写出到交换分区，从而提高磁盘的效率。正如我们稍后将更详细地讨论磁盘时所看到的那样，这种集群减少了磁盘的查找和旋转开销，从而显著提高了性能。要使用后台分页进程，应略微修改图21-3中的控制流程。该算法将直接检查是否有可用的空闲页面，而不是直接执行替换。如果没有，它将通知后台分页线程需要空闲页面。当线程释放一些页面时，它将重新唤醒原始线程，然后可以在所需的页面中分页并继续其工作。

> TIP: DO WORK IN THE BACKGROUND
> When you have some work to do, it is often a good idea to do it in the background to increase efficiency and to allow for grouping of operations. Operating systems often do work in the background; for example, many systems buffer file writes in memory before actually writing the data to disk. Doing so has many possible benefits: increased disk efficiency, as the disk may now receive many writes at once and thus better be able to schedule them; improved latency of writes, as the application thinks the writes completed quite quickly; the possibility of work reduction, as the writes may need never to go to disk (i.e., if the file is deleted); and better use of idle time, as the background work may possibly be done when the system is otherwise idle, thus better utilizing the hardware.



<br/>
<br/>



### 总结

Summary


在简短的本章节中，我们介绍了访问比系统中实际存在的内存更多的内存的概念。这样做需要分页表结构中的更多复杂性，因为必须包括当前位(present bit)以告诉我们该页面是否存在于内存中。否则，操作系统页面错误处理程序将运行以处理页面错误，从而安排将所需的页面从磁盘传输到内存，也许首先替换内存中的某些页面以为即将被交换的那些页面腾出空间。

重要的是，记得这些动作对进程来说都是透明的。就进程而言，它只是在访问自己的私有连续虚拟内存。在幕后，页面被放置在物理内存中的任意(不连续)位置中，有时它们甚至不存在于内存中，需要从磁盘中获取。尽管我们希望在通常情况下可以快速访问内存，但在某些情况下仍需要多个磁盘操作来进行访问。在最坏的情况下，执行一条指令之类的简单操作可能需要花费几毫秒的时间才能完成。




<br/>
<br/>
<br/>




## 交换策略

Beyond Physical Memory(Swap) Policies: <http://pages.cs.wisc.edu/~remzi/OSTEP/vm-beyondphys-policy.pdf>


在虚拟内存呢管理器中，当您有大量可用内存时，生活很轻松。发生页面错误时，您在空闲页面列表中找到一个空闲页面，并将其分配给错误页面。不幸的是，只有很少的内存可用时，事情才会变得更加有趣。在这种情况下，**内存压力**(memory pressure)迫使操作系统开始调出页面，以便为活跃使用的页面腾出空间。使用包含在操作系统中的替换策略确定逐出哪个页面。从历史上看，这是早期虚拟内存系统做出的最重要的决定之一，因为旧的系统只有很少的物理内存。至少，这是一组有趣的策略，值得更多地了解。因此，我们的问题是：

> THE CRUX: HOW TO DECIDE WHICH PAGE TO EVICT
> How can the OS decide which page (or pages) to evict from memory? This decision is made by the replacement policy of the system, which usually follows some general principles (discussed below) but also includes certain tweaks to avoid corner-case behaviors.



<br/>
<br/>



### 缓存管理

Cache Management


在深入讨论策略之前，我们首先详细描述我们要解决的问题。假设主内存保存系统中所有页面的一些子集，那么可以正确地将其视为系统中虚拟内存页面的高速缓存(cache)。因此，我们为该高速缓存选择替换策略的目标是最小化高速缓存丢失(cache miss)的次数，即最小化我们必须从磁盘获取页面的次数。或，可以将我们的目标视为最大化高速缓存命中(cache hit)的次数，即在内存中找到被访问的页面的次数。

知道高速缓存命中和未命中的数量后，我们就可以计算程序的**平均内存访问时间**(average memory access time, AMAT)。具体来说，给定这些值，我们可以计算程序的AMAT：

$$AMAT=T_{M}+(P_{miss}*T_{D})$$

- $$T_{M}$$: 表示访问内存的成本
- $$T_{D}$$: 表示访问磁盘的成本
- $$P_{miss}$$: 表示在高速缓存中找不到数据的可能性(未命中)，在`0.0-1.0`之间变化，有时我们使用百分比来表示

请注意，您始终要支付访问内存中数据的费用。但是，当您错过时，您必须另外支付从磁盘中获取数据的费用。

例如，让我们想象一下一个具有微小地址空间的机器：4KB，具有256Bytes的页面。因此，虚拟地址的两个部分：4为VPN（最高有效位）和8位偏移（最低有效位）。因此，此示例中的进程可以访问`2^4=16`个虚拟页面。在此示例中，该进程生成如下内存引用（即，虚拟地址）：`0x000, 0x100, 0x200, 0x300, 0x400, 0x500, 0x600, 0x700, 0x800, 0x900`。这些虚拟地址指的是地址空间的前十个页面中每个页面的第一个字节。让我们进一步假设除虚拟页面3之外的每个页面都已经在内存中。因此，我们的内存引用序列将遇到以下行为：hit, hit, hit, miss, hit, hit, hit, hit, hit, hit。我们可以计算出命中率为90%，而未命中率为10%。

要计算AMAT，我们需要知道访问内存的成本和访问磁盘的成本。假设访问内存的成本约为100ns，访问磁盘的成文约为10ms。`AMAT=100ns+(0.1*10ms)=1.0001ms`，大约为1ms。如果我们的命中率很高(`99.9%`)，则结果将快100倍。

不幸的时，正如您所看到的那样，现代系统中磁盘访问的成本如此之高，以至于即使是很小的未命中也将很快主导正在运行的程序的整体AMAT。显然，我们需要以磁盘速度避免尽可能多的丢失。



<br/>
<br/>



### 最优替换策略

The Optimal Replacement Policy


为了更好地了解特定替换策略的工作原理，最好将其与最佳替换策略进行比较。事实证明，这样的最优策略是Belady多年前开发的（最初称为MIN）。最佳的提花策略导致总体上的遗漏最少。一种简单的方法（不幸的是，实现起来很困难）替代了将来被最远访问(furthest in the future)的页面，这是最佳策略，从而导致了最少的高速缓存丢失。

最优策略背后的直觉是有意义的。如果您必须扔掉一些页面，为什么不扔掉从现在开始最远的那一页呢？这样，您实际上是在说高速缓存中的所有其它页面比最远的页面更重要。这个原因很简单：在引用最远的内容之前，您将显先引用其它页面。

让我们通过一个简单的示例进行追踪，以了解最优策略的决策。假设程序访问以下虚拟页面流：0, 1, 2, 0, 1, 3, 0, 3, 1, 2, 1。图22-1显示了优化的行为，假设缓存是很三个页面。

![22-1](/images/OSTEP/22-1.png)

在该图中，您可以看到以下操作。毫不奇怪，由于高速缓存以空状态(empty state)开始，因此前三个访问是未命中。有时将这种丢失称为**冷启动丢失**(cold-start miss)（或强制丢失, compulsory miss）。然后，我们再次引用页面0和1，它们都在缓存中命中。最后，我们遇到另一个未命中的问题(page 3)，但这次缓存已满。必须进行替换！我们应该替换哪个页面？使用最佳策略，我们检查缓存中当前每个页面的未来(0, 1, 2)，发现几乎立即要访问0，稍后再访问1，将来访问最远的2。因此，最佳策略有一个简单的选择：逐出2，在缓存中生成0, 1, 3。接下来的三个引用会命中，但随后我们转到了之前逐出的2，但遭到了另一个丢失。在这，最佳策略再次检查缓存中每个页面(0, 1, 3)的未来，并发现只要不逐出页面1（将要访问的页面）就可以了。该示例显示page 3被驱逐，尽管page 0也是一个不错的选择。最后，我们命中page 1，追踪完成。

我们还可以计算缓存的命中率：6 hits and 5 misses， `6/(6+5)=54.5%`。您还可以计算强制丢失的命中率（即，忽略给定页面的第一个丢失），得出的命中率为85.7%。

不幸的是，正如我们之前在指定调度策略中所看到的那样，未来并不为人所知。您无法为通用操作系统构建最佳策略。因此，在指定实际的、可部署的策略时，我们将集中于找到其它方法来确定要逐出哪个页面。因此，最优策略将仅用作比较，以了解我们与完美的距离。

<br>

> TIP: COMPARING AGAINST OPTIMAL IS USEFUL
> Although optimal is not very practical as a real policy, it is incredibly useful as a comparison point in simulation or other studies. Saying that
your fancy new algorithm has a 80% hit rate isn’t meaningful in isolation; saying that optimal achieves an 82% hit rate (and thus your new approach is quite close to optimal) makes the result more meaningful and gives it context. Thus, in any study you perform, knowing what the optimal is lets you perform a better comparison, showing how much improvement is still possible, and also when you can stop making your policy better, because it is close enough to the ideal.

<br>

> ASIDE: TYPES OF CACHE MISSES
> In the computer architecture world, architects sometimes find it useful to characterize misses by type, into one of three categories: compulsory, capacity, and conflict misses, sometimes called the Three C’s [H87]. A compulsory miss (or cold-start miss [EF78]) occurs because the cache is
empty to begin with and this is the first  eference to the item; in contrast, a capacity miss occurs because the cache ran out of space and had to evict an item to bring a new item into the cache. The third type of miss (a conflict miss) arises in hardware because of limits on where an item can be placed in a hardware cache, due to something known as setassociativity; it does not arise in the OS page cache because such caches are always fully-associative, i.e., there are no restrictions on where in memory a page can be placed. See HP for details [HP06].



<br/>
<br/>



### 先进先出策略

A Simple Policy: FIFO


许多早期的系统避免了尝试达到最佳状态的复杂性，并采用了非常简单的替换策略。例如，某些系统使用了**先进先出**(first-in, first-out FIFO)替换，将页面在进入系统时仅放入队列中。当发生替换时，队列尾部的页面（先进入的页面）将被逐出。FIFO具有强大的优势：实现起来非常简单。FIFO有一个强大的优势：实现起来非常简单。让我们研究一下FIFO在引用流中的作用，如图2-2。

![22-2](/images/OSTEP/22-2.png)

我们再次从三个强制性丢失开始追踪，分别指向page 0, 1, 2，然后同时命中0和1。接下来，引用3，从而导致丢失。使用FIFO可以很容易地决定替换：选择作为先进入的第一个页面(page 0)。不幸的是，我们的下一个访问页面是page 0，从而导致另一个丢失和替换。然后，我们在3上命中，但在1和2上丢失，最后在3上命中。

将FIFO与最优进行比较，FIFO明显更糟：命中率为36.4%（不包括强制性丢失为57.1%）。先进先出根本无法确定块的重要性：即使page 0已被访问过多次，但FIFO仍将其踢出去，原因仅在于它是第一个进入内存的页面。

<br>

> ASIDE: BELADY’S ANOMALY
> Belady (of the optimal policy) and colleagues found an interesting reference stream that behaved a little unexpectedly [BNS69]. The memoryreference stream: 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5. The replacement policy they were studying was FIFO. The interesting part: how the cache hit rate changed when moving from a cache size of 3 to 4 pages.
> In general, you would expect the cache hit rate to increase (get better) when the cache gets larger. But in this case, with FIFO, it gets worse! Calculate the hits and misses yourself and see. This odd behavior is generally referred to as Belady’s Anomaly (to the chagrin of his co-authors).
> Some other policies, such as LRU, don’t suffer from this problem. Can you guess why? As it turns out, LRU has what is known as a stack property [M+70]. For algorithms with this property, a cache of size N + 1 naturally includes the contents of a cache of size N. Thus, when increasing the cache size, hit rate will either stay the same or improve. FIFO and Random (among others) clearly do not obey the stack property, and thus are susceptible to anomalous behavior.



<br/>
<br/>



### 其它简单策略：随机

Another Simple Policy: Random


另一个类似的替换策略是**随机**(Random)，它只是在内存压力下仅选择一个随机页面进行替换。随机具有类似于FIFO的属性，实施起来很简单，但是在选择块驱逐时并没有太聪明。让我们看一下随机策略的效果，如图22-3。

![22-3](/images/OSTEP/22-3.png)

当然，随机策略的表现完全取决于随机在其选择中的幸运度（或，不幸运度）。实际上，我们可以运行随机策略数千次，并确定其总体性能图22-4显示了随机进行一万多次实验的命中次数，每个实验都有不同的随机种子。如您所见，有时随机策略的效果最佳，有时效果会更差。随机策略的表现取决于抽奖的运气。

![22-4](/images/OSTEP/22-4.png)



<br/>
<br/>



### 使用历史记录

Using History: LRU(Least Rcently Used)


不幸的是，任何诸如FIFO或Random这样简单的策略都有可能会遇到一个普遍的问题：它可能会剔出一个重要的页面，该页面将再次被引用。FIFO踢出最先进入的页面，如果此页面上恰好有重要代码或数据结构，则无论如何都会被扔掉，即使它很快会被重新分页。因此，FIFO、Random和其它类似策略不太可能达到最佳状态。需要更聪明的东西。

与调度策略一样，为了提高对未来的猜测，我们再次依赖过去(past)，并以历史(history)为指导。例如，如果某个程序在近期访问过某个页面，则很可能在不久的将来再次访问该页面。

页面替换策略可以使用的一种历史信息是频率(frequency)。如果某个页面已被多次访问，则可能不应该替换该页面，因为它显然具有某些价值。页面最常用的属性是页面的访问频率。访问页面的时间越近，也许再次访问该页面的可能性就越大。

这一系列策略基于人们所说的本地性原则(principle of locality)，基本上只是对程序及其行为的观察。这个原理的意思很简单，就是程序倾向于非常频繁地访问某些代码序列和数据结构。因此，我们应尝试使用历史记录找出哪些页面很重要，并在驱逐时间(eviction time)将这些页面保留在内存中。

因此，诞生了一系列简单的基于历史的算法。**最不常使用**(Least Frenquently Used, LFU)策略将在必须进行逐出时替换最不常用的页面。同样，**最近最少使用**(Least Recently Used, LRU)策略替换了最近最少使用的页面。这些算法很容易记住，一旦知道名词，就确切知道它的作用。

为了更好地了解LRU，让我们检查一下LRU的表现。如图22-5。从图中可以看到，LRU如何使用历史记录来比无状态策略（如FIFO、Random）做得更好。

![22-5](/images/OSTEP/22-5.png)

我们还应注意到，这些算法存在相反的情况：**最常用**(Most Frequently Used, MFU)和**最近使用**(Most Recently Used, MRU)。在大多数情况下，这些策略效果不佳，因为它们忽略了大多数程序所展示的位置，而不是拥抱(embracing)它。

<br>

> ASIDE: TYPES OF LOCALITY
> There are two types of locality that programs tend to exhibit. The first is known as spatial locality, which states that if a page P is accessed, it is likely the pages around it (say P − 1 or P + 1) will also likely be accessed. The second is temporal locality, which states that pages that have been accessed in the near past are likely to be accessed again in the near future. The assumption of the presence of these types of locality plays a large role in the caching hierarchies of hardware systems, which deploy many levels of instruction, data, and address-translation caching to help programs run fast when such locality exists.
> Of course, the principle of locality, as it is often called, is no hard-andfast rule that all programs must obey. Indeed, some programs access memory (or disk) in rather random fashion and don’t exhibit much or any locality in their access streams. Thus, while locality is a good thing to keep in mind while designing caches of any kind (hardware or software), it does not guarantee success. Rather, it is a heuristic that often proves useful in the design of computer systems.



<br/>
<br/>



### 工作负载

Workload Examples


让我们再看几个栗子，以更好地理解其中一些策略的行为。在这里，我们将研究更复杂的工作负载，而不是细小的痕迹。但是，即使是这些工作量也大大简化。更好的研究应该包括应用程序追踪。

我们的第一个工作负载没有局部性，这意味着每个引用都是针对所访问页面集合的随机页面。在这个简单示例中，工作负载会随着时间访问100个唯一的页面，选择下一个页面进行随机引用。总共访问一万个页面。在实验中，我们将缓存大小从很小(1)更改为足以容纳所有唯一页面(100)，以查看每种策略在缓存大小范围内的行为。

![22-6](/images/OSTEP/22-6.png)

图22-6显示了最优、LRU、Random、FIFO的实验结果。我们可从图中得出许多结论。首先，当工作负载没有局限性时，使用哪种实际策略都无关紧要。它们都执行相同的操作，命中率完全由缓存的大小确定。其次，当缓存足够大以适合整个工作负载时，使用哪种策略页无关紧要。当所有引用的块都放入高速缓存中时，所有策略都收敛到100%的命中率。最后，您可以看到最优执行比实际策略要好得多。

我们检查的下一个工作负载称为80-20工作负载，它有局部性：80%的引用指向20%的页面(the hot pages)，区域20%的引用指向其余80%的页面(the cold pages)。在我们的工作量中，共有100个唯一页面。因此，热页面是大多数时候都引用的页面，冷页面是其余页面。图22-7显示了策略在此工作负载下的执行情况。

![22-7](/images/OSTEP/22-7.png)

从图中可以看出，尽管Random和FIFO都表现不错，但LRU表现更好，因为它更有可能保留在热页面上。由于这些页面在过去经常被引用，因此它们可能会在不久的将来在此被引用。优化策略再一次做的更好，表明LRU的历史的并不完美。

您现在可能想知道：LRU对Random和FIFO的改进真的有那么大的意义吗？答案通常是取决于情况。如果每个丢失的代价都很高（并不罕见），那么命中率的小幅度提高也会对性能产生巨大的影响。如果丢失不是那么昂贵，那么LRU可能带来的好处当然就不那么重要了。

让我们来看一个最终的工作负载，我们称其为**循环顺序**(looping sequential)工作负载 ，其中，我们依次引用了50个页面，从0、1...一直到49。然后我们循环、重复进行这些访问。总共一万次访问50个唯一页面。图22-8的最后一个图显示了此工作负载下的行为。

![22-8](/images/OSTEP/22-8.png)

在许多应用程序中常见的这种工作量对于LRU和FIFO都是最坏的情况。在循环顺序的工作量下，这些算法会淘汰旧页面。不幸的是，由于工作负载的循环性质，这些旧页面的访问时间将比策略希望保留在缓存中的页面要早。确实，即使使用大小为49的缓存，循环顺序的50页工作负载也导致命中率为0%。有趣的是，Random明显更好，没有达到最佳状态，但至少达到了非零的命中率。事实证明，Random有一些不错的特性。这样的属性之一就是没有奇怪的极端情况行为。



<br/>
<br/>



### 实施历史算法

Implementing Historical Algorithms


如您所见，诸如LRU之类的算法通常可以比诸如FIFO或Random之类的简单策略做的更好，后者可能会抛出重要页面。不幸的是，历史记录策略给我们带来了新的挑战：我们如何执行它们？

以LRU为例。要完美实现它，我们需要做很多工作。具体而言，在每次页面访问(page access)时，我们都必须更新某些数据结构，以将该页面移至列表的开头。将此与FIFO进行对比，仅当逐出页面或将新页面添加到列表中时，才访问FIFO页面列表。为了追踪使用最少的页面、最近使用的页面，系统必须对每个内存引用(every memory reference)进行一些统计工作。显然，如果不加小心，这种统计可能会大大降低性能。

可以帮助加快速度的一种方法是添加一点硬件支持。例如，一台机器可以在每次访问页面时更新内存中的时间字段。因此，当访问页面时，硬件将当前时间设置为时间字段的值。然后，在替换页面时，操作系统可以简单地扫描系统中的所有时间字段以找到最近最少使用的页面。

不幸的是，随着系统中页面数量的增长，扫描大量的时间来查找最近最少使用的绝对页面的成本过高。想象一下，一台具有4GB内存的现代机器，它被切成4KB的页面，这台机器有100万页面。即使在现代CPU速度下，查找LRU也将花费很长时间。我们真的需要找到绝对最旧的页面来替换吗？我们可以近似地生存吗？

<br>

> CRUX: HOW TO IMPLEMENT AN LRU REPLACEMENT POLICY
> Given that it will be expensive to implement perfect LRU, can we approximate it in some way, and still obtain the desired behavior?



<br/>
<br/>



### 近似地LRU

Approximating LRU


事实证明，答案是肯定的：从计算开销的角度来看，近似地LRU更可行，而实际上这是许多现代系统所做的。这个想法需要硬件支持，使用位(use bit)（有时称为参考位(reference bit)）的形式。系统每页有一个使用位，这些使用位生活在内存中的某个位置（它们可以在每个进程的分页表中，也可以仅在数组中的某个位置）。每当引用页面时，使用位都会由硬件设置位1.尽管硬件也不会清除该位。这是操作系统的责任。

操作系统如何用使用位(use bit)来近似LRU？可能有很多方法，但是对于**时钟算法**(clock algorithm)，建议一种简单的方法。想象一下以循环列表排列的系统的所有页面。**clock hand**指向某个特定的页面。当必须进行替换时，操作系统会检查当前指向的page P的使用位是1还是0.如果为1，则表明页面P最近被使用过，因此不是很好的替换对象。因此，P的使用位被设置为0，并且时针增加到下一页(P+1)。该算法继续进行，直到找到设置为0的使用位，这表明该页面最近未使用过。

注意，该方法不是采用使用位来近似LRU的唯一方法。实际上，任何定期清除使用位，然后区分哪些页面的使用位是1还是0来决定替换哪个页面的方法都可以。时钟算法只是一种早期的方法，取得了一些成功，并且具有不重复扫描所有内存以查找未使用页面的优点。

时钟算法变体的行为如图22-9所示。进行替换时，此变体会随机扫描页面。当遇到参考位设置为1的页面时，清除该位（将其设置为0）。当找到参考位设置位0的页面时，将其选择为牺牲品。如您所见，尽管它的LRU效果不如LRU完美，但比完全不考虑历史记录的方法要好。

![22-9](/images/OSTEP/22-9.png)



<br/>
<br/>



### 脏页

Considering Dirty Pages


通常对时钟算法的一个小修改是对内存中的页面是否已修改的其它考虑。这样做的原因是：如果页面已被修改(dirty)，则必须将其写回磁盘以驱逐它，这很昂贵。如果尚未修改(clean)，则驱逐是免费的。物理帧可以简单地重用于其它目的，而无需额外的I/0。因此，某些虚拟内存系统更喜欢将干净的页面移出脏页。

为支持此行为，硬件应包括一个修改后的位(modified bit, 又称dirty bit)。每次写入页面时都会设置此位，因此可以将其合并到页面替换算法中。例如，可以通过更改时钟算法，以扫描未使用的页面和干净的页面以优先逐出。找不到这些，然后找到脏的未使用的页面，依此类推。



<br/>
<br/>



### 其它虚拟内存策略

Other VM Policies


页面替换不是虚拟内存子系统采用的唯一策略（尽管它可能是最重要的）。例如，操作系统还必须决定何时将页面放入内存。该策略有时称为页面选择(page selection)策略，它为操作系统提供了一些不同的选项。

对于大多数页面，操作系统仅使用**按需分页**(demand page)，这意味着操作系统在访问页面时将页面按需地带入内存。当然，操作系统可能会猜测将要使用一个页面，从而提前将其带入内存。这种行为称为**预取**(prefetching)，只有在有合理的成功机会时才应该这样做。例如，某些系统假定，如果将代码页P带入内存中，则该代码页(P+1)可能很快就会被访问，因此也应将其放入内存中。

另一个策略确定操作系统如何将页面写出到磁盘。当然，可以一次将它们写出来。但是，许多系统会在内存中收集大量待处理的写入，然后通过一次写入(更高效)将它们写入磁盘。此行为通常称为集群(clustering)或写操作的简单分组(grouping of writes)，并且由于磁盘驱动器的性质有效，因为磁盘驱动器必许多小型驱动器更有效地执行单个大型写入。



<br/>
<br/>



### Thrashing

Thrashing


在结束之前，我们要解决一个最后的问题：如果仅是简单地过度地使用内存，并且正在运行的进程集合的内存需求仅超过可用的物理内存时，操作系统应该怎么做？在这种情况下，系统将不断进行分页，这种情况有时被称为**颠簸**(thrashing)。

一些较早的操作系统具有一套相当复杂的机制，可以在发生颠簸时检测并应对颠簸。例如，给定一组进程，系统可以决定不运行进程的子集，希望减少进程的工作集(working sets)的适合内存并因此可以取得进展。这种通常被称为准入控制(admission control)的方法指出，有时做好一些工作总比尝试一次做不好所有事情要好。，这是现实生活中以及现代操作系统中经常遇到的一种情况。

一些当前的系统对内存过载(memory overload)采取了更为严格的方法。例如，某些版本的Linux在内存超额(oversubscribed)时运行**out of memory killer**。此守护进程选择一个内存密集型(memory intensive)进程并将其终止，从而以一种不太微妙的方式减少了内存。在成功减少了内存压力的同时，此方法可能会遇到问题。例如，如果它终止了X Server，从而使任何需要显示(display)的应用程序无法使用。



<br/>
<br/>



### 总结

Summary


我们已经看到了许多页面替换策略的引入，它们是所有现代操作系统的虚拟内存子系统的一部分。现代系统对简单的LRU近似值进行了一些调整。例如，扫描电阻(scan resistance)是许多现代算法（如，ARC）的重要组成部分。它类似于LRU，但也尝试避免LRU的最坏情况行为，这在循环顺序工作负载中可以看到。因此，页面替换算法的发展还将继续。

但是，在许多情况下，随着内存访问时间和磁盘访问时间之间的差异增加，所述算法的重要性降低了。因为分页到磁盘是如此昂贵，所以频繁地分页的成本令人望而却步。因此，过度分页的最佳解决方案通常是一种简单的方法：购买更多内存。




<br/>
<br/>
<br/>




## 完整的虚拟内存系统

Complete Virtual Memory Systems: <http://pages.cs.wisc.edu/~remzi/OSTEP/vm-complete.pdf>


在结束对内存虚拟化的研究之前，让我们仔细研究一下如何将整个虚拟内存系统组合在一起。我们已经看到了此类系统的关键要素，包括众多的分页表设计、与TLB的交互、确定哪些页面要保留在内存中以及哪些页面应该被踢出去的策略。但是，还有许多其它功能可以构成一个完整的虚拟内存系统，包括许多用于性能、功能和安全性的功能。

<br>

> THE CRUX: HOW TO BUILD A COMPLETE VM SYSTEM
> What features are needed to realize a complete virtual memory system? How do they improve performance, increase security, or otherwise improve the system?

<br>

我们将介绍两个系统来完成此操作。
第一个是1970年代早期开发的**VAX/VMS**操作系统中发现的现代虚拟内存管理器的最早示例之一。直到今天，这种系统中无数的技术和方法一直存在，因此值得研究。
第二个是Linux，原因显而易见。Linux是一种广泛使用的系统，可以在像电话这样的小型且功能不足的系统上有效运行，而该系统却不像现代数据中心中可扩展性最高的多核系统那样。因此，虚拟内存操作系统必须足够灵活才能在所有这些情况下成功运行。我们将讨论每个系统，以说明前面几章提出的概念如何在完整的内存管理器中结合在一起。



<br/>
<br/>



### VAX/VMS虚拟内存

VAX/VMS Virtual Memory


VAX-11微型计算机体系结构是由Digital Equipment Corporation(DEC)在1970年代末期引入的。在小型计算机时代，DEC在计算机行业扮演着重要的角色。该体系结构通过多种方式实现，包括VAX-11/780和较弱的VAX-11/750。

该系统称为VAX/VMS，其主要架构师之一有Dave Cutler，他后来领带开发了Microsoft Windows NT。VMS存在一个普遍的问题，那就是它可以在各种机器上运行，包括非常便宜的VAXen到同一体系结构家族中的高端机器和功能强大的机器。因此，操作系统必须具有能够在如此庞大的系统范围内运行的机制和策略。

作为一个附加问题，VMS用于隐藏体系结构某些固有缺陷的软件创新的一个很好的栗子。尽管操作系统通常依靠硬件来构建有效的抽象和错觉，但有时硬件设计人员并不能完全正确地完成所有工作。

<br>

**内存管理硬件(Memory Management Hardware)**

VAX-11为每个进程提供了32位虚拟地址空间，分为512-byte pages。因此，虚拟地址由23位VPN和9位偏移量组成。此外，VPN的高两位用来区分页面驻留在哪个段中。因此，如前所述，该系统是分页(paging)和分段(segmentation)的混合体。

地址空间的下半部分被称为**进程空间**(process space)，并且对每个进程都是唯一的。在进程空间的前半部分中(称为P0)，找到了用户程序以及向下增长的堆(heap)。在进程空间的后半部分(P1)，我们找到了向上增长的栈(stack)。
地址空间的上半部分称为**系统空间**(system space, S)，尽管仅使用了一半。受保护的操作系统代码和数据位于此处，并且操作系统以这种方式在进程之间共享。VMS设计人员的一个主要问题是VAX硬件中的页面大小极小(512Bytes)。由于历史原因而选择此大小，其基本问题是使简单的线性分页表过大。因此，VMS设计人员的首要目标之一就是确保VMS不会因分页表而淹没(overwhelm)内存。

系统通过两种方式减少了内存中压力分页表的位置(reduced the pressure page tables place)。首先，通过分段将用户地址空间分成两部分，VAX 11为每个进程的的区域(P0和P1)提供了一个分页表。因此，在栈和堆之间的地址空间的未使用部分不需要分页表空间。the base and bounds registers按预期使用。base registers保存该段的分页表地址，boungds register保存其大小（即，分页表条目的数量）。

其次，操作系统通过将用户分页表(P0和P1，因此每个进程有两个)放置在内核虚拟系统中，从而进一步降低了内存压力。因此，当分配和增加分页表时，内核会在Segment S中从自己的虚拟内存中分配空间。如果内存承受这巨大的压力，内核可以将这些分页表的分页换(out to)到磁盘上，从而使物理内存对其它用途可用。

将分页表放入内核虚拟内存(kernel virtual memory)意味着地址转换更加复杂。例如，要转换P0和P1中的虚拟地址，硬件必须首先尝试在其分页表（该进程的P0和P1）中查找该页面的分页表条目。但是，这样做，硬件可能首先必须查询系统分页表（位于物理内存中）。完成转换后，硬件可以了解分页表的页面地址，然后最终了解所需的内存访问地址。幸运的是，VAX的硬件管理的TLB使所有这些操作变得更快，这些TLB通常(希望)绕过这种费力的查找。

<br>

> ASIDE: THE CURSE OF GENERALITY
> Operating systems often have a problem known as the curse of generality, where they are tasked with general support for a broad class of applications and systems. The fundamental result of the curse is that the OS is not likely to support any one installation very well. In the case of VMS, the curse was very real, as the VAX-11 architecture was realized in a number of different implementations. It is no less real today, where Linux is expected to run well on your phone, a TV set-top box, a laptop computer, desktop computer, and a high-end server running thousands of processes in a cloud-based datacenter.

<br/>

**真实的地址空间(A Real Address Space)**

研究VMS的一个巧妙方面是，我们可以看到如何构造真正的地址空间（如图23-1）。到目前为止，我们已经假定了一个仅包含用户代码、用户数据、用户堆的简单地址空间，但是正如我们在上面看到的，实际的地址空间要复杂的多。

![23-1](/images/OSTEP/23-1.png)

例如，代码段从不从page 0开始。该页面被标记为不可访问(inaccessible)，以便为检测空指针(null pointer)访问提供一些支持。因此，在设计地址空间时需要考虑的一个问题是调试的支持，此处无法访问的page 0以某种形式提供了调试。

也许更重要的是，内核虚拟地址空间（即其数据结构和代码）是每个用户地址空间的一部分。在上下文切换中，操作系统将P0和P1寄存器更改为指向即将运行(soon-to-be-run)的进程的相应分页表。但是，它不会更改S base and bounds registers。因此，相同内核结构并映射到每个用户地址空间。

出于多种原因，内核被映射到每个地址空间。这种构造使内核的工作轻松。例如，当操作系统收到来自用户程序的指针（如，`write()`系统调用）时，很容易将数据从该指针复制到自己的结构中。操作系统是自然编写和编译的，无需担心其访问的数据来自何处。相反，如果内核完全位于物理内存中，则很难进行诸如将分页表的页面交换到(swap)磁盘的操作。如果给内核提供了自己的地址空间，则在用户应用程序和内核之间移动数据将再次变得复杂而痛苦。通过这种构造(construction)（现已被广泛使用），内核几乎是应用程序的库(library)，尽管它是受保护的。

关于该地址空间的最后一点与保护有关。显然，操作系统不希望用户程序读取或写入操作系统数据和代码。因此，硬件必须支持页面的不同保护级别才能启用此功能。VAX通过在分页表中的保护位中指定CPU必须处于什么特权级别才能访问特定页面来做到这一点。因此，与用户数据和代码相比，将系统数据和代码设置为更高的保护级别。试图从用户代码访问此类信息将在操作系统中生成陷阱(trap)，并且可能会终止违规进程。

<br>

> ASIDE: WHY NULL POINTER ACCESSES CAUSE SEG FAULTS
> You should now have a good understanding of exactly what happens on a null-pointer dereference. A process generates a virtual address of 0, by doing something like this。
> The hardware tries to look up the VPN (also 0 here) in the TLB, and suffers a TLB miss. The page table is consulted, and the entry for VPN 0 is found to be marked invalid. Thus, we have an invalid access, which transfers control to the OS, which likely terminates the process (on UNIX systems, processes are sent a signal which allows them to react to such a fault; if uncaught, however, the process is killed).

```
int *p = NULL; // set p = 0
*p = 10; // try to store 10 to virtual addr 0
```

<br/>

**页面替换(Page Replacement)**

VAN中的分页表条目(PTE)包含以下位：有效位(a valid bit), 保护字段(protection bit 4 bits), 修改(脏)位(modify/dirty bit), 保留给操作系统使用的字段(5 bits), 将页面的位置存储在物理内存中的物理帧号(PFN)。机敏的读者可能会注意到：没有引用位(reference bit)！因此，VMS替换算法必须在没有硬件支持的情况下确定哪些页面处于活动状态。

开发人员还担心**内存消耗**(memory hogs)，程序占用大量内存，并使其它程序难以运行。迄今为止，我们研究的大多数策略都容易受到这种束缚。例如，LRU是一项全局策略，它不会在进程之间公平地共享内存。

<br>

> ASIDE: EMULATING REFERENCE BITS
> As it turns out, you don’t need a hardware reference bit in order to get some notion of which pages are in use in a system. In fact, in the early 1980’s, Babaoglu and Joy showed that protection bits on the VAX can be used to emulate reference bits [BJ81]. The basic idea: if you want to gain some understanding of which pages are actively being used in a system, mark all of the pages in the page table as inaccessible (but keep around the information as to which pages are really accessible by the process, perhaps in the “reserved OS field” portion of the page table entry). When a process accesses a page, it will generate a trap into the OS; the OS will then check if the page really should be accessible, and if so, revert the page to its normal protections (e.g., read-only, or read-write). At the time of a replacement, the OS can check which pages remain marked inaccessible, and thus get an idea of which pages have not been recently used.
> The key to this “emulation” of reference bits is reducing overhead while still obtaining a good idea of page usage. The OS must not be too aggressive in marking pages inaccessible, or overhead would be too high. The OS also must not be too passive in such marking, or all pages will end up referenced; the OS will again have no good idea which page to evict.

<br>

为了解决这两个问题，开发人员提出了**分段FIFO替换策略**(segmented FIFO)。这个想法很简单：每个进程最多可以保留在内存中的页面数称为**驻留集大小**(resident set size, RSS)。这些页面的每一个都保存在FIFO列表中。当进程超出其RSS时，将驱逐**先进入(first-in)**页面。FIFO显然不需要任何硬件的支持，因此易于实现。

当然，如我们先前所见，纯FIFO的性能不是特别好。为了提高FIFO的性能，VMS引入了两种**第二机会列表**(second chance lists)，在从内存中逐出页面之前先放置页面，特别是**a global clean-page free list**和**dirty-page list**。当进程P超过其RSS时，将从每个进程的FIFO中删除页面。如果是干净的(未修改)，则将其放在干净页面列表的末尾。如果是脏的(已修改)，则将其放在脏页列表的末尾。

如果另一个进程Q需要一个空闲页，它将第一个空闲页从全局干净列表中删除。但是，如果原始进程P在被回收之前在该页面上出错，则P从空闲（或脏）列表中回收该进程，从而避免了昂贵的磁盘访问。全局第二机会列表越大，分段FIFO算法对LRU的执行越接近。

VMS中使用的另一种优化还有助于克服VMS中较小的页面使用。特别是，使用如此小的页面(small pages)，交换过程中的磁盘I/O效率可能非常低，因为磁盘在进行大传输时表现更好。为了使swapping I/O更加有效，VMS添加了许多优化，但最重要的是**集群**(clustering)。通过集群，VMS将全局脏列表中的大批页面组合在一起，并一口气将它们写入磁盘（从而使它们干净）。在大多数现代系统中都使用集群，因为自由地将页面放置在交换空间的任何位置都可以使操作系统组页面(group pages)执行更少和更大的写入，从而提高性能。

<br/>

**其它整洁的技巧(Other Neat Tricks)**

VMS还有另外两个现在的标准技巧：demand zero, copy on write。现在我们来描述这些惰性优化。VMS中的一种惰性形式是页面需求归零(demand zeroing of pages)。为了更好地理解这一点，让我们考虑将页面添加到地址空间的示例，如，在堆中。在幼稚的实现中，操作系统通过在物理内存中查找页面并将其归零来响应将页面添加到堆中的请求，然后将其映射到您的地址空间。但是，幼稚的实现可能会付出高昂的代价，特别是如果该页面没有被进程使用的话。

当需求归零时，将页面添加到您的地址空间后，操作系统几乎不会执行任何工作。它将在分页表中放置一个条目，标记该页不可访问。如果该进程随后读取或写入页面，则会在操作系统中产生陷阱。处理陷阱时，操作系统会注意到这实际上是零需求页面。此时，操作系统完成了查找物理页面，将其归零并将其映射到进程的地址空间所需的工作。如果该进程从不访问该页面，则可以避免所有此类工作，从而可以将需求归零。

另一个很酷的优化是写时复制(copy on write, COW)。这个想法至少可以追溯到TENEX操作系统，它很简单：当操作系统需要将页面从一个地址空间复制到另一个地址空间，而不是复制它时，它可以将页面映射到目标地址空间并在两个地址空间中将其标记为只读。如果两个地址空间都只读取该页面，则不采取进一步的措施，因此操作系统实现了快速复制，而实际上没有移动任何数据。

但是，如果其中一个地址空间确实试图写入该页面，则它将触发操作系统的陷阱。然后，操作系统注意到该页面是COW页面，因此（懒惰地）分配一个新页面，用数据填充它，将此新页面映射到故障进程的地址空间中。然后，该进程继续进行，现在具有其页面的自己的私有副本。

出于多种原因，COW很有用。当然，任何类型的共享库都可以映射写时复制到许多进程的地址空间，从而节省了宝贵的内存空间。在Unix系统中，由于`fork()`和`exec()`的语义，COW更为重要。您可能还记得，`fork()`创建了调用程序地址空间的精确副本。如果地址空间较大，则运行此类复制的速度将很慢且数据密集。更糟的是，大多数地址空间被随后对`exec()`的调用所立即覆盖，该调用将调用进程的地址空间与即将执行的程序的地址空间覆盖在一起。通过代之以执行写时复制`fork()`，操作系统避免了很多不必要的复制，从而在提高性能的同时保留了正确的语义。

<br>

> TIP: BE LAZY
> Being lazy can be a virtue in both life as well as in operating systems. Laziness can put off work until later, which is beneficial within an OS for a number of reasons. First, putting off work might reduce the latency of the current operation, thus improving responsiveness; for example, operating systems often report that writes to a file succeeded immediately, and only write them to disk later in the background. Second, and more importantly, laziness sometimes obviates the need to do the work at all; for example, delaying a write until the file is deleted removes the need to do the write at all. Laziness is also good in life: for example, by putting off your OS project, you may find that the project specification bugs are worked out by your fellow classmates; however, the class project is unlikely to get canceled, so being too lazy may be problematic, leading to a late project, bad grade, and a sad professor. Don’t make professors sad!



<br/>
<br/>



### Linux虚拟内存系统

The Linux Virtual Memory System


现在，我们将讨论Linux虚拟内存系统的一些更有趣的方面。Linux工程师解决了生产中遇到的实际问题，推动了Linux的发展。因此，大量功能已缓慢地集成到现在已完全可用的功能齐全的虚拟内存系统中。

虽然我们无法讨论Linux虚拟内存的各个方面，但我们将介绍最重要的方面，尤其是超出了经典的虚拟内存系统（如，VAX/VMS）的范围。我们还将强调Linux与旧系统之间的共性。

在本次讨论中，我们将重点介绍Linux for Intel x86。尽管Linux可以并且确实可以在许多不同的处理器结构上运行，但是x86上的Linux是其最主要和最重要的部署，因此也是我们关注的焦点。

<br>

**The Linux Address Space**

与其它现代操作系统一样，也与VAX/VMS类似，Linux虚拟地址空间由用户部分和内核部分组成。与其它系统一样，在上下文切换时，当前运行的用户部分的地址空间也会更改。跨进程的内核部分是相同的。与其它系统一样，在用户模式下运行的程序无法访问内核虚拟页面。只有陷入内核(trapping into the kernel)并转换为特权模式，才能访问此类内存。

- **用户部分**(user portion)：user program code, stack, heap, other parts reside
- **内核部分**(kernel prtion)：kernel code, stack, heap, other parts reside

在典型的32位Linux中(即具有32位虚拟地址空间的Linux)中，地址空间中的用户部分和内核部分之间的拆分发生在地址`0xC0000000`或地址空间的四分之三处。64位Linux的拆分方式相似，但略有不同。图23-2显示了典型(简化)的地址空间。

- 用户虚拟地址范围：`0-0xBFFFFFFF`
- 内核虚拟地址范围：`0xC0000000-0xFFFFFFFF`

<br>

![23-2](/images/OSTEP/23-2.png)

<br>

Linux的一个有趣的方面是它包含两种类型的内核虚拟地址。

第一种称为**内核逻辑地址**(kernel logic address)。这就是您需要考虑的内核的正常虚拟地址空间。为了获得更多这种类型的内存，内核代码仅需要调用`kmalloc`。大多数内核数据结构都存在于此，例如分页表，每个进程的内核栈等等。与系统中的其它大多数内存不同，内核逻辑内存无法交换到磁盘。

内核逻辑地址最有趣的方面使它们与物理内存的连接。具体而言，内核逻辑地址与物理内存的第一部分之间存在直接映射。因此，内核逻辑地址`0xC0000000`转换为物理地址`0x00000000`，`0xC0000FFF`转换为`0x00000FFF`，依此类推。这种直接映射有两个含义。首先，在内核逻辑地址和物理地址之间来回转换很简单。因此，这些地址通常被视为确实是物理地址。第二个问题是，如果一块内存在内核逻辑地址空间中是连续的，那么它在物理内存中也是连续的。这使得内核地址空间的此部分中分配的内存适合需要连续物理内存才能正常工作的操作，例如通过目录内存访问(directory memory access, DMA)与设备之间的I/O传输。

内核地址的另一种类型是**内核虚拟地址**(kernel virtual address)。为了获得这种类型的内存，内核代码调用了另一个分配器`vmalloc`，该分配器返回一个指向所需大小的虚拟连续区域的指针。与内核逻辑内存不同，内核虚拟内存通常是不连续的。每个内核虚拟页面都可以映射到不连续的物理页面（因此不适合DMA）。但是，这样的内存因此更易于分配，因此可用于大型缓冲区(large buffer)，这些缓冲区中查找连续的大块物理内存将非常困难。

在32位Linux中，存在内核虚拟地址的原因是，它们使内核能够寻址超过(大约)1GB的内存。几年前，计算机的内存远少于此，并且可以访问超过1GB的内存也不成问题。但是，技术进步了，很快就需要使内核能够使用更多的内存。内核虚拟地址以及严格的从一对一映射到物理内存的断开连接使之成为可能。但是，随着向64位Linux的迁移，这一需求已不再那么紧迫，因为内核不仅限于虚地址的的最后1GB。

<br>

**分页表结构(Page Table Structure)**

因为我们专注于`x86`的Linux，所以我们的讨论将集中在`x86`提供的分页表结构的类型上，因为它决定了Linux可以做什么和不能做什么。如前所述，`x86`提供了一种硬件管理的多层分页表结构，每个进程一个分页表。操作系统仅在其内存中设置映射，将特权寄存器指向页面目录的开始，然后由硬件处理其余部分。如预期那样，操作系统会参与进程创建、删除、上下文切换，请确保在每种情况下，硬件MMU都使用正确的分页表来执行转换。

如上所述，近年来最大的变化可能是从32位x86到64位x86的转变。正如在VAX/VMS系统中看到的那样，32位地址空间已经存在了很长时间，并且随着技术的变化，它们最终开始真正成为程序的限制。虚拟内存使对系统进行编程变得很容易，但是对于包含许多GB内存的现代系统而言，32位已不足以引用每个内存。因此，下一个飞跃变得很必要。

移至64位地址会以预期的方式影响x86中的分页表结构。由于x86使用多级分页表，因此当前的64位系统使用四级表(four level table)。虚拟地址空间的完整64位性质尚未使用，但是仅使用了最低的48位。因此，虚拟地址可以如下查看：

![](/images/OSTEP/64-bit-vm.png)

如图所示，虚拟地址的前16位未使用（因此在转换中不起作用），后12位（由于4KB页面大小）用作偏移量（直接使用，不进行转换），剩下的虚拟地址的中间36位将参与转换。地址的P1部分用于索引到最顶层的页面目录中，然后从那里开始一次转换级别，直到分页表的实际页面被P4索引为止，从而生成所需的分页表条目。

随着系统内存的增大，此庞大的地址空间的更多部分将被启用。从而导致五级分页表、六级分页表...想象一下，一个简单的分页表查找需要六级转换，只是要弄清楚某个数据在内存中的位置。

<br>

**大页面支持(Large Page Support)**

Intel x86允许使用多种页面大小，而不仅仅是标准的4KB页面。具体而言，最近的设计在硬件上支持2MB甚至1GB的页面。因此，随着时间的流逝，Linux逐渐发展为允许应用程序利用这些巨大的页面(huge page)。

如前所述，使用大页面会带来很多好处。从VAX/VMS中可以看出，这样做可以减少分页表中所需的映射数。页面越大，映射越少。但是，较少的分页表条目并不是大页面背后的驱动力。相反，这是更好的TLB行为和相关的性能提升。当某个进程主动使用大量内存时，它将迅速用转换填充TLB。如果这些转换是针对4KB页面的，则只能访问少量的总内存，而不会导致TLB丢失。结果是，对于在具有大量GB内存的计算机上运行的现代大内存工作负载而言，这将带来明显的性能成本。最近的研究表明，某些应用程序将其周期的10%用于服务TLB丢失。

大页面允许进程通过使用更少的TLB插槽来访问大容量内存而不会TLB丢失，因此这是主要优势。但是，巨大的页面还有其它好处：较短的TLB丢失路径，这意味着当确实发生TLB丢失时，可以更快地提供服务。此外，分配可能非常快，这虽然很小但是有时很重要。

Linux对大页面的支持的一个有趣方面是它如何逐步完成的。刚开始，Linux开发人员直到这种支持仅对少数应用程序很重要，例如对性能有严格要求的大型数据库。因此，决定允许应用程序显式请求大页面的内存分配（通过`mmap()`或`shmget()`调用）。这样，大多数应用程序将不会受到影响。

最近，由于在许多应用程序中更常见的是需要更好的TLB行为，因此，Linux开发人员增加了透明的大页面支持。启用此功能后，操作系统会自动寻找分配大型页面（通常为2MB，但在某些系统上为1GB）的机会，而无需修改应用程序。

庞大的页面并非没有代价。潜在的最大成本是内部碎片(internal fragmentation)，即页面很大但是用稀疏的页面。这种浪费形式可以用很大但很少使用的页面填充内存。交换也不适用于大页面，有时会大大放大系统的I/O数量。分配的开销也可能不好。总的来说，有一件事很清楚：4KB的页面大小可以很好地服务于系统很多年，这并不是曾经的通用解决方案。不断增长的内存需求要求我们将大页面和其它解决方案视为VM系统必要发展的一部分。Linux对这种基于硬件的技术的缓慢采用证明了即将到来的变化。

<br>

**页面缓存(The Page Cache)**

为了降低访问持久化存储(persistent storage)的成本，大多数系统使用积极的**缓存(caching)**子系统将刘翔的数据项保留在内存中。在这方面，Linux与传统操作系统没有什么不同。

Linux页面缓存(page cache)是统一的，可以从三个主要来源将页面保留在内存中。这些实体保存在页面缓存哈希表中(page cache hash table)，以便在需要所述数据时进行快速查找。

- 内存映射文件(memory mapped files)
- 来自设备的文件数据(file data)和元数据(metadata)：通常通过将`read()`和`write()`调用定向到文件系统来访问
- 堆和栈(heap and stack)组成每个进程的页面：有时称为匿名内存(anonmous memory)，因为其下没有命名文件，而是交换空间

页面缓存追踪条目是干净的还是脏的。脏数据通过后台线程(称为`pdflush`)定期写入后备存储（即，写入文件的特定文件，或为匿名区域交换空间），从而确保最终将修改后的数据写回到持久性存储。此后台活动或者在特定时间段之后进行，或者如果认为太多页面是脏页面。

在某些情况下，系统的内存不足，Linux必须确定踢出内存的页面以释放空间。为此，Linux使用修改后的`2Q`替换，我们将在此进行描述。
基本思想很简单：标准LRU替换是有效的，但是可以通过某些常见的访问模式来破坏。例如，如果某个进程重复访问一个大文件，则LRU会将所有其它文件踢出内存。更糟糕的是：将该文件的部分保留在内存中，因为在被踢出内存之前，它们从未被重新引用过。

Linux版本的`2Q`替换算法通过保留两个列表并在两个列表之间划分内存来解决此问题。首次访问时，页面被放在一个队列中（Linux中称为 inactive list）。重新引用该页面后，该页面将被提升到另一个队列（Linux中为active list）。当需要进行替换时，替换候选者将从不活跃列表中获取。Linux还定期将页面从活跃列表的底部移至不活跃列表，使活跃列表保持在页面高速缓存总大小的三分之二处。

Linux最好以完美的LRU顺序管理这些列表，但是，正如前面各章所讨论的那样，这样做非常昂贵。因此，与许多操作系统一样，使用LRU的近似值。

这种2Q方法的行为通常与LRU相当，但是特别地通过将周期性访问的页面限制在不活跃列表中来处理周期性大文件访问的情况。因为所述页面在被踢出内存之前从未被重新引用，所以它们不会清除活跃列表中找到的其它有用页面。

> ASIDE: THE UBIQUITY OF MEMORY-MAPPING
> Memory mapping predates Linux by some years, and is used in many places within Linux and other modern systems. The idea is simple: by calling mmap() on an already opened file descriptor, a process is returned a pointer to the beginning of a region of virtual memory where the contents of the file seem to be located. By then using that pointer, a process can access any part of the file with a simple pointer dereference.
> Accesses to parts of a memory-mapped file that have not yet been brought into memory trigger page faults, at which point the OS will page in the relevant data and make it accessible by updating the page table of the process accordingly (i.e., demand paging).
> Every regular Linux process uses memory-mapped files, even the code in main() does not call mmap() directly, because of how Linux loads code from the executable and shared library code into memory. Below is the (highly abbreviated) output of the pmap command line tool, which shows what different mapping comprise the virtual address space of a running program (the shell, in this example, tcsh). The output shows four columns: the virtual address of the mapping, its size, the protection bits of the region, and the source of the mapping:
> 0000000000400000 372K r-x-- tcsh
> 00000000019d5000 1780K rw--- [anon ]
> 00007f4e7cf06000 1792K r-x-- libc-2.23.so
> 00007f4e7d2d0000 36K r-x-- libcrypt-2.23.so
> 00007f4e7d508000 148K r-x-- libtinfo.so.5.9
> 00007f4e7d731000 152K r-x-- ld-2.23.so
> 00007f4e7d932000 16K rw--- [stack ]
> As you can see from this output, the code from the tcsh binary, as well as code from libc, libcrypt, libtinfo, and code from the dynamic linker itself (ld.so) are all mapped into the address space. Also present are two anonymous regions, the heap (the second entry, labeled anon) and the stack (labeled stack). Memory-mapped files provide a straightforward and efficient way for the OS to construct a modern address space.

<br>

**安全性和缓冲区溢出(Security And Buffer Overflows)**

现代VM系统(Linux, Solaris, BSD)与旧VM系统(VAX/VMS)之间最大的区别可能是现代对安全性的重视。保护一直是操作系统的一个严重问题，但是随着计算机之间比以往任何时候都更加互联，开发人员实施了各种防御性对策以阻止哪些狡猾的黑客获得对系统的控制，这不足为奇。

缓冲区溢出(buffer overflow)攻击是一种主要威胁，可以用于普通用于程序，甚至内核本身。这些攻击的目的是在目标系统中发现一个漏洞，攻击者可以利用该漏洞将任意数据注入目标的地址空间。之所以会出现这种情况，是因为开发人员(错误地)认为输入不会太长，因此(可信地)将输入复制到缓冲区。因为输入实际上太长，所以它会使缓冲区溢出，从而覆盖目标的内存。如下所示的代码可能是问题的根源：

```c
int some_function(char *input) {
  char dest_buffer[100];
  strcpy(dest_buffer, input); // oops, unbounded copy!
}
```

在许多情况下，这样的溢出并不是灾难性的。例如，无意中给用户程序甚至操作系统造成的错误输入都可能导致崩溃，但并不糟。
但是，恶意程序员可以精心设计会使缓冲区溢出的输入，以便将自己的代码注入目标系统，从本质上允许他们接管并进行自己的竞标。如果在连接网络的用户程序上获得成功，则攻击者可以在受感染的系统上运行任意计算。如果在操作系统本身上获得成功，则攻击可以访问更多资源，并且是所谓的特权升级(privilege escalation)的一种形式（即，用户获得内核访问权限）。如果您无法猜测，这些都是坏事。

防止缓冲区溢出的第一个也是最简单的防御措施是防止执行在地址空间的某些区域中找到任何代码（如，栈内）。AMD在其x86版本中引入了NX bit，它是一种防御措施。它只是阻止从在相应分页表条目中设置了该位的任何页面执行。该方法可阻止攻击者注入到目标栈中的代码，从而减轻了问题。

但是，攻击者是聪明的。即使攻击者无法明确添加注入的代码，恶意代码也可以执行任意代码序列。这个想法以其一般的形式被称为**面向返回的编程(return-oriented programming, ROP)**，并且确实很棒。ROP背后的观察结果是，任何程序的地址空间中都有很多代码，尤其是与大量C库链接的C程序。因此，攻击者可以覆盖栈，以使当前执行功能中的返回地址指向所需的恶意指令，然后再返回指令。通过将大量小工具串在一起（即，确保每次返回都跳到下一个小工具），攻击者可以执行任意代码。

为了防御ROP，Linux添加了另一种防御，称为**地址空间布局随机化(address space layout randomization, ASLR)**。操作系统没有将代码、栈、堆放在虚拟地址空间内的固定位置上，而是将它们的放置位置随机化，从而使制作实现此类攻击所需的复杂代码变得颇具挑战性。因此，对易受攻击的用户程序的大多数攻击都将崩溃，但无法控制正在运行的程序。

有趣的是，您可以在实践中很容易地观察到这种随机性。这是一段在现代Linux系统上进行演示的代码：

```c
int main(int argc, char *argv[]) {
    int stack = 0;
    printf("%p\n", &stack);
    return 0;
}
```

这段代码只是在栈上打印处变量的(虚拟)地址。在较旧的non-ASLR系统中，该值每次都相同。但是，如下所示，该值随每次运行而变化：

```
prompt> ./random
0x7ffd3e55d2b4

prompt> ./random
0x7ffe1033b8f4

prompt> ./random
0x7ffe45522e94
```

ASLR是对用户级程序的一种有用防御措施，它还被并入内核，这是一种没有想象的功能，称为内核地址空间布局随机化(kernel address space layout randomization, KASLR)。但是，事实证明，内核可能还有更大的问题要处理。

<br>


**Other Security Problems: Meltdown And Spectre**

系统安全领域已经被两次新的相关攻击所颠覆。第一个称为**崩溃(Meltdown)**，第二个称为**(Spectre)**。它们是由四个不同的研究人员/工程师同时发现的，并引起了对上述计算机硬件和操作系统提供的基本保护的深切质疑。

这些攻击中每种漏洞利用的普遍缺点是，现代系统中发现的CPU会执行各种幕后花招，以提高性能。问题核心的一类技术称为**推测执行(speculative execution)**，其中CPU猜测将来会很快执行哪些指令，并提前开始执行它们。如果猜测正确，则程序运行速度更快；如果不是，则CPU再次尝试取消对架构状态（如，寄存器）的影响，这一次是正确的。

推测执行的问题在于，它倾向于在系统的各个部分（如，处理器缓存）中留下执行痕迹。因此，问题就来了：正如攻击者所表明的那样，这种状态会使内存的内容变得脆弱，甚至我们认为受MMU保护的内存也是如此。

因此，增加内核保护的一种方法是从每个用户进程中删除尽可能多的内核地址空间，而为大多数内核数据提供单独的内核分页表（kernel page table isolatio, KPTI)。因此，没有将内核的代码和数据结构映射到每个进程中，而是仅保留最小的最小值。当切换到内核时，现在需要切换到内核分页表。这样做可以提高安全性并避免某些攻击源，但是要付出代价：性能。切换分页表的成本很高。嗯，安全性成本：便利性和性能。

不幸的是，KPTI不能解决上面列出的所有安全问题，只是其中一些问题。而简单的解决方案（如，关闭推测）将毫无意义，因为系统运行速度会慢数千倍。因此，如果您需要系统安全，那么这是一个有趣的时代。

要真正了解这些攻击，您必须先学习很多知识。首先了解现代计算机体系结构，这是该主题的高级书籍中的内容，重点是推测以及实现该体系结构所需的所有机制。



<br/>
<br/>



### 总结

现在，您已经看到了两个虚拟内存系统的从上到下(top-to-bottom)的回顾。希望大多数细节都易于理解，因为您应该已经对基本机制和政策有了很好的理解。您还了解了一些关于Linux的知识，尽管它是一个又大又复杂的系统，但它继承了过去的许多好主意，其中许多我们没有余地进行详细讨论。例如，Linux在`fork()`上执行页面的惰性写时复制(lazy copy-on-write)，从而通过避免不必要的复制来降低开销。Linux还要求将页面清零（使用`/dev/zero`设备的内存映射），并具有后台交换守护进程(swapd)，它将页面交换到磁盘以减少内存压力。











<br/>
<br/>

---

<br/>
<br/>

























# 并发

Concurrency


<br/>


## 并发和线程

Concurrency and Thread: <http://pages.cs.wisc.edu/~remzi/OSTEP/threads-intro.pdf>


到目前为止，我们已经看到了操作系统执行的基本抽象的发展。我们已经看到了如何采用单个物理CPU并将其转变为多个虚拟CPU，从而使人们幻想同时运行多个程序。我们还看到了如何为每个进程创建大型私有虚拟内存的错觉。当操作系统确实在物理内存之间秘密地复用地址空间时，地址空间的这种抽象使每个程序的行为就好像它具有自己的内存一样。

在本章中，我们为单个正在运行的进程引入了新的抽象——**线程**(thread)。多线程程序具有一个以上的执行点(point of execution)（如，多台PC，每台PC都从中获取并执行），而不是我们对程序中的单个执行点的经典观点（即，从中获取并执行指令的单个PC）。也许另一种思考方式是：每个线程都非常像一个单独进程，只是有一个区别——它们共享相同的地址空间，因此可以访问相同的数据。

单线程的状态与进程的状态非常相似。它具有一个程序计数器(program conter, PC)，用于追踪程序从哪里获取指令。每个线程都有自己的专用寄存器集(private set of registers)，用于计算。因此，如果在单个处理器上运行着两个线程，当从运行的T1切换到运行另一个T2时，必须进行上下文切换(context switch)。线程之间的下上文切换与进程之间的上下文切换非常相似，因为在运行T2之前必须保存T1的寄存器状态并恢复T2的寄存器状态。我们将进程的状态保存到**进程控制块**(process control block, PCB)，将线程的的状态保存到**线程控制块**(thread control block, TCB)。但是，线程的上下文切换与进程相比，有一个主要区别：线程的地址空间保持不变（即，无需切换我们正在使用的分页表）。

线程和进程之间的另一个主要区别在于**栈**(stack)。在经典的地址空间的简单模型（称其为单线程进程(single-threaded process)）中，有一个栈，通常位于地址空间的底部，如图16-2左侧。但是，在多线程进程(multi-threaded process)中，每个线程都独立运行，并且当然可以调用各种例程来执行其正在执行的任何工作。不同于地址空间中的单个栈，每个线程将有一个栈。假设我们有一个多线程进程，其中有两个线程，则它的地址空间看起来有所不同，如图26-1右侧。

![26-1](/images/OSTEP/26-2.png)

在此图中，您可以看到两个栈分布在整个进程的地址空间中。因此，我们放在栈上的任何栈分配的变量(variables)、参数(parameters)、返回值(return values)和其它内容，都放置在有时称为**线程局部存储**(thread-local storage)的位置，即相关线程的栈中。

您可能还会注意到，这是如何破坏我们美丽的地址空间布局的。以前，栈和堆可以独立地增长，并且在地址空间不足时会出现麻烦。在这里，我们再也不会遇到这样的情况了。幸运的是，这通常时可以的。因为栈通常不必很大（例外是在大量使用递归的程序中）。



<br/>
<br/>



### 为什么要使用线程

Why Use Threads?


在详细介绍线程以及编写多线程程序可能遇到的一些问题，让我们首先回答一个更简单的问题。为什么要完全使用线程？

事实证明，使用线程至少有两个主要原因。
第一个很简单：**并行**(parallelism)。想象一下，您正在编写一个非常大的数组执行操作的程序。如，将两个大数组加在一起，或者将数组中的每个元素的值增加一定量。如果仅在单处理器上运行，则任务很简单：只需执行每个操作即可。但是，如果要在具有多个处理器的系统上执行程序，则有可能通过使用处理器分别执行部分工作来大大加快此进程。将标准单线程程序转换为可在多个CPU上进行此类工作的程序的任务称为**并行化**(parallelization)，而使用每个CPU的线程来执行此工作是使程序在现代硬件上更快运行的自然而典型的方法。
第二个原因更加微妙：避免由于I/O缓慢而阻塞程序进度。想象一下，您正在编写一个执行不同类型I/O的程序：等待发送或接受消息，完成显式磁盘I/O或隐式页面错误。您的程序可能希望执行其它操作，而不是等待，包括利用CPU执行计算，甚至发出其它I/O请求。使用线程是避免阻塞(block)的一个自然方法。当程序中的一些线程等待时（即被阻塞等待I/O时），CPU调度程序可以切换到其它线程，这些线程准备好运行并可以执行一些有用的操作。线程使I/O与单个程序中的其它活动重叠，就像多程序对跨程序的进程所做的一样。结果，许多现代的基于服务器的应用程序(Web, DB...)在其实现中都使用了线程。

当然，在上述情况下，您可以使用多进程取代多线程。但是，线程共享一个地址空间，因此很容易共享数据。因此在构造这些类型的程序时自然是一个选择。对于逻辑上独立的任务、对于几乎不需要共享内存中的数据结构的任务，进程是一个更合理的选择。



<br/>
<br/>



### 线程创建

An Example: Thread Creation


让我们来探讨一些细节。假设我们要运行一个创建两个线程的程序，每个线程执行一些独立的工作，本例中打印A或B。代码如下所示：

```c
// Simple Thread Creation Code (t0.c)

#include <stdio.h>
#include <assert.h>
#include <pthread.h>
#include "common.h"
#include "common_threads.h"

void *mythread(void *arg) {
    printf("%s\n", (char *) arg);
    return NULL;
}

int
main(int argc, char *argv[]) {
    pthread_t p1, p2;
    int rc;
    printf("main: begin\n");
    Pthread_create(&p1, NULL, mythread, "A");
    Pthread_create(&p2, NULL, mythread, "B");
    // join waits for the threads to finish
    Pthread_join(p1, NULL);
    Pthread_join(p2, NULL);
    printf("main: end\n");
    return 0;
}
```

创建两个线程（T1和T2）后，主线程(main thread)调用线程``join()，它等待特定线程完成。它执行两次，从而确保T1和T2在最终允许主线程再次运行之前可以运行并完成。总体而言，在此运行期间使用了三个线性: `main`, `T1`, `T2`。

让我们检查一下这个程序的可能执行顺序。如执行图26-3中所示。

![26-3](/images/OSTEP/26-3.png)

但是请注意，此排序不是唯一的排序。实际上，给定一系列指令，取决于调度程序决定在给定点运行哪个线程。例如，一旦创建线程，它可能立即运行，这将导致执行图26-4中所示的操作。

![26-4](/images/OSTEP/26-4.png)

如果说调度程序决定先运行T2（即使T1是较早创建的），我们甚至还可以看到在A之前打印B。没有理由假定首先创建的线程将首先运行。图26-5显示了此最终执行顺序，其中T2在T1之前执行任务。

![26-5](/images/OSTEP/26-5.png)

如您所见，思考线程创建的一种方法是它有点像运行函数调用。但是，系统不是先执行函数然后返回调用程序，而是为正在调用的例程创建一个新的执行线程，并且它独立于调用程序运行，可能在从创建返回之前，也有可能之后。接下来运行的内容由操作系统调度程序确定，尽管调度程序(scheduler)可能实现了一些明智的算法，但很难知道在任何给定的时间将运行什么。

从这个栗子中您还可以看出，线程使生活变得复杂。何时运行什么已经很难了！没有并发性(concurrency)，计算机就很难理解。不幸的是，并发只会使情况变得更糟。



<br/>
<br/>



### 为什么变得更糟：共享数据

Why It Gets Worse: Shared Data


上面显示的简单线程示例对于显示线程的创建方式以及如何根据调度程序决定如何运行它们的顺序以不同的顺序运行很有用。但是，它并没有向您显示线程在访问共享数据时如何交互。

让我们想象一个简单的栗子，其中两个线程希望更新全局共享变量。代码如下所示：

```c
// Sharing Data

#include <stdio.h>
#include <pthread.h>
#include "common.h"
#include "common_threads.h"

static volatile int counter = 0;

// mythread()
//
// Simply adds 1 to counter repeatedly, in a loop
// No, this is not how you would add 10,000,000 to
// a counter, but it shows the problem nicely.
//
void *mythread(void *arg) {
    printf("%s: begin\n", (char *) arg);
    int i;
    for (i = 0; i < 1e7; i++) {
        counter = counter + 1;
    }
    printf("%s: done\n", (char *) arg);
    return NULL;
}

// main()
//
// Just launches two threads (pthread_create)
// and then waits for them (pthread_join)
//
int main(int argc, char *argv[]) {
    pthread_t p1, p2;
    printf("main: begin (counter = %d)\n", counter);
    Pthread_create(&p1, NULL, mythread, "A");
    Pthread_create(&p2, NULL, mythread, "B");

    // join waits for the threads to finish
    Pthread_join(p1, NULL);
    Pthread_join(p2, NULL);
    printf("main: done with both (counter = %d)\n",
    counter);
    return 0;
}

// >over
```

效果：

```
gcc -o main main.c -Wall -pthread
./main

main: begin (counter = 0)
A: begin
B: begin
A: done
B: done
main: done with both (counter = 20000000)
```

不幸的是，即使在单个处理器上运行此代码，也不一定能够获得理想的结果。有时，我们得到：

```
./main

main: begin (counter = 0)
A: begin
B: begin
A: done
B: done
main: done with both (counter = 19345221)
```

让我们再尝试一次。计算机不应该产生确定性的结果吗？

```
./main

main: begin (counter = 0)
A: begin
B: begin
A: done
B: done
main: done with both (counter = 19221041)
```

每次运行不仅错误，而且产生不同的结果。还有一个大问题：为什么会这样？

<br>

> TIP: KNOW AND USE YOUR TOOLS
> You should always learn new tools that help you write, debug, and understand computer systems. Here, we use a neat tool called a disassembler. When you run a disassembler on an executable, it shows you what assembly instructions make up the program. For example, if we wish to understand the low-level code to update a counter (as in our example), we run objdump (Linux) to see the assembly code: ` objdump -d main`.
> Doing so produces a long listing of all the instructions in the program, neatly labeled (particularly if you compiled with the -g flag), which includes symbol information in the program. The objdump program is just one of many tools you should learn how to use; a debugger like gdb, memory profilers like valgrind or purify, and of course the compiler itself are others that you should spend time to learn more about; the better you are at using your tools, the better systems you’ll be able to build.



<br/>
<br/>



### 问题的核心：不受控制的调度

The Heart Of The Problem: Uncontrolled Scheduling


要了解为什么会发生这种情况，我们必须了解编译器生成的代码序列，来更新`counter`。在这种情况下，我们希望简单地在计数器上添加一个`number(1)`。因此，这样做的代码序列可能看起来像这样(x86中)：

```
mov 0x8049a1c, %eax
add $0x1, %eax
mov %eax, 0x8049a1c
```

本示例假定变量`counter`位于地址`0x8049a1c`。在此三个指令序列中，首先使用x86 `mov` 指令获取该地址处的内存值，并将其放入寄存器eax中。然后，执行`add`，将1(0x1)加到寄存器的内容中。最后，eax的内容存储到同一地址的内存中。

让我们想象一下，我们的两个线程之一的T1进入此代码区域，因此即将使计数器(counter)增加1。它将计数器的值（假设是50）加载到其寄存器eax中。因此，T1的`eax=50`。将其加1，因此，`eax=51`。现在，不幸的事情发生了。定时器中断关闭了(timer interrupt goes off)。因此，操作系统将当前正在运行的线程（其PC，包括eax的寄存器等）的状态保存到该线程的TCB中。

现在，更糟的事情发生了。选择运行T2，并进入相同的代码片。它同样也执行第一条指令，获取计数器的值并将其放入eax中（请记住，每个线程在运行时都有自己的专用寄存器。这些寄存器由保存和恢复它们的上下文切换代码虚拟化）。此时计数器的值仍为50，因此T2的`eax=50`。然后，假设T2执行了以下两条指令，将eax加1(`eax=51`)，然后将eax的内容保存的计数器(地址0x8049a1c)。因此，全局变量counter现在的值为51。

最后，发生另一个上下文切换，T1恢复运行。回想一下，它刚刚执行了`mov`和`add`，现在将要执行最终的`mov`指令。还记得`eax=51`。因此，最终的`mov`指令将执行，并将该值保存到内存中。计数器再次设置为51。

简而言之，发生的事情是这样的：递增counter的代码已经运行了两次，但是从50开始的counter现在仅等于51。该程序的正确版本应该是`counter=52`。

让我们看一下详细的执行追踪。对此例，假定上面的代码在内存中的地址100处加载，如下序列所示（请注意那些曾经使用过类似RISC的指令集：x86具有可变长度指令。此mov指令占用5个字节的内存，而add只有3个字节）。

![26-7](/images/OSTEP/26-7.png)

基于这些假设，发生的情况如图26-7所示。假定counter从值50开始，并通过此示例进行追踪以确保您了解发生了什么。我们在这里展示的内容称为**竞争条件(race conditon)**（或，**数据竞争**(data race)），结果取决于代码的时序执行。如果运气不好（即，在执行中不合时宜的时刻发生上下文切换），我们会得到错误的结果。实际上，我们每次都可能得到不同的结果。因此，我们将这个结果称为**不确定的**(indeterminate)，而不是一个好的确定性的计算(deterministic computation)。我们习惯于从计算机中使用该确定性计算。在这种情况下，未知的输出将是什么，并且在各次运行之间确实可能有所不同。

由于执行此代码的多个线程可能会导致竞争条件，因此我们将代码称为**关键部分**(critical section)。关键部分是用于访问共享变量（共享资源）的一段代码，并且不得由多个线程并发执行。

我们真正想要用于此代码的是所谓的**互斥**(mutual exclusion)。此属性保证如果一个线程在关键部分内执行，则其它线程将无法执行。

顺便说一下，几乎所有这些术语都是由Essger Dijkstra创造的，他是该领域的先去，并由于这项工作和其它工作而获得了图灵奖(Turing Award)。

<br>

> TIP: USE ATOMIC OPERATIONS
> Atomic operations are one of the most powerful underlying techniques in building computer systems, from the computer architecture, to concurrent code (what we are studying here), to file systems (which we’ll study soon enough), database management systems, and even distributed systems [L+93].
> The idea behind making a series of actions atomic is simply expressed with the phrase “all or nothing”; it should either appear as if all of the actions you wish to group together occurred, or that none of them occurred, with no in-between state visible. Sometimes, the grouping of many actions into a single atomic action is called a transaction, an idea developed in great detail in the world of databases and transaction processing [GR92].
> In our theme of exploring concurrency, we’ll be using synchronization primitives to turn short sequences of instructions into atomic blocks of execution, but the idea of atomicity is much bigger than that, as we will see. For example, file systems use techniques such as journaling or copyon-write in order to atomically transition their on-disk state, critical for operating correctly in the face of system failures. If that doesn’t make sense, don’t worry — it will, in some future chapter



<br/>
<br/>



### 原子性

The Wish For Atomicity


解决此问题的一种方法是拥有更强大的指令，只需一步即可完全完成我们需要的一切，从而消除了不及时中断的可能性。例如，如果我们有一个看起来像这样的超级指令怎么办：`memory-add 0x8049a1c, $0x1`。

假设该指令将一个值添加到内存位置，并且硬件保证其原子化执行。当指令需要执行时，它将根据需要执行更新。它不能再指令中间(mid-instrction)被中断，因为这恰恰是我们从硬件获得的保证：当发生中断时，指令要么根本没有运行，要么已经完成。没有中间状态。

原子化(atomically)，在此上下文中是指`as a unit`，有时我们将其视为`all or none`。我们想要以原子化执行这三个指令序列：

```
mov 0x8049a1c, %eax
add $0x1, %eax
mov %eax, 0x8049a1c
```

正如我们所说，如果只有一条指令来执行此操作，则必须发出(issue)该指令即可完成。但在一般情况下，我们不会有这样的指令。想象我们正在构建并发的B树(B-tree)，并希望对其进行更新。我们是否真的希望硬件支持B树的原子更新指令？至少在一个健全的指令集中，可能不是。

因此，我们要做的是向硬件询问一些有用的指令，在这些指令上我们可以构建通用的所谓**同步原语(synchronization primitives)**集。通过使用这种硬件支持，再结合操作系统的一些帮助，我们将能够构建多线程代码，以同步(synchronized)和受控制(controlled)的方式访问关键部分，从而尽管并发执行具有挑战性，但仍然可靠地产生正确的结果。非常棒，不是吗？

<br>

> THE CRUX: HOW TO SUPPORT SYNCHRONIZATION
> What support do we need from the hardware in order to build useful synchronization primitives? What support do we need from the OS? How can we build these primitives correctly and efficiently? How can programs use them to get the desired results?



<br/>
<br/>



### 等待另一个

One More Problem: Waiting For Another


本章设置了并发问题，就好像线程之间仅发生一种交互类型一样，即访问共享变量的交互类型以及对关键部分支持原子性的需求。事实证明，出现了另一种常见的交互作用，其中一个线程必须等待另一个线程完成某些操作才能继续。例如，当进程执行磁盘I/O并使其进入睡眠状态时，就会发生这种交互。当I/O完成时，需要从休眠状态唤醒该进程，以便继续进行。

因此，在接下来的章节中，我们将不仅研究如何构建对同步原语(synchronization primitives)的支持以支持原子性，而且还将研究如何支持这种在多线程程序中常见的**sleeping/waking**交互机制。如果现在没有理解，没关系。当您阅读到**条件变量**(condition variables)章节时就会学习相关内容。

<br>

> ASIDE: KEY CONCURRENCY TERMS CRITICAL SECTION, RACE CONDITION, INDETERMINATE, MUTUAL EXCLUSION
> These four terms are so central to concurrent code that we thought it worth while to call them out explicitly. See some of Dijkstra’s early work [D65,D68] for more details.
> A critical section is a piece of code that accesses a shared resource, usually a variable or data structure.
> A race condition (or data race [NM92]) arises if multiple threads of execution enter the critical section at roughly the same time; both attempt to update the shared data structure, leading to a surprising (and perhaps undesirable) outcome.
> An indeterminate program consists of one or more race conditions; the output of the program varies from run to run, depending on which threads ran when. The outcome is thus not deterministic, something we usually expect from computer systems.
> To avoid these problems, threads should use some kind of mutual exclusion primitives; doing so guarantees that only a single thread ever enters a critical section, thus avoiding races, and resulting in deterministic program outputs.



<br/>
<br/>



### 总结

Summary: Why in OS Class?

在总结之前，您可能会遇到一个问题是：为什么要在OS Class中研究它？历史(history)是一句话的答案。操作系统是第一个并发程序，并且创建了许多技术供自己使用。后来，对于多线程进程，程序员还必须考虑这些事情。

例如，假设有两个进程正在运行。假设它们都调用`write()`写入文件，并且都希望将数据追加到文件中（即，将数据添加到文件的末尾）。为此，双方都必须分配一个新块(new block)，在该块所在的文件的inode中记录该文件，并更改文件的大小以反映新的更大的大小。由于随时可能发生中断，因此更新这些共享结构(shared structures)的代码的关键部分。因此，操作系统的设计者从引入中断的一开始就不得不担心操作系统如何更新内部结构。不及时的中断会导致上述所有问题。毫不奇怪，必须使用适当的同步原语仔细地访问分页表，进程列表、文件系统结构以及几乎每个内核数据结构，以使其正常工作。




<br/>
<br/>
<br/>




## Thread API

Thread API: <http://pages.cs.wisc.edu/~remzi/OSTEP/threads-api.pdf>


本章简要介绍了Thread API的主要部分。当我们展示如何使用API时，将在后续章节中进一步解释每个部分。我们应该注意，后面的章节将以许多示例更慢地介绍锁(lock)和条件变量(condition variables)的概念。因此，本章可以更好地用作参考。

> CRUX: HOW TO CREATE AND CONTROL THREADS
> What interfaces should the OS present for thread creation and control? How should these interfaces be designed to enable ease of use as well as utility?



<br/>
<br/>



### 线程创建

Thread Creation


编写多线程程序必须要做的第一件事是创建新线程，因此必须存在某种线程创建接口(thread creation interface)。在POSIX中，这很容易：

```c
#include <pthread.h>
int
pthread_create(pthread_t      *thread,
         const pthread_attr_t *attr,
               void           *(*start_routie) (void*),
               void           *arg);
```

该声明可能看起来有点复杂，特别是如果您没有在C语言中使用函数指针，但实际上它还不错。有四个参数：

- `thread`：指向`pthread_t`类型结构的指针。我们将使用此结构与线程进行交互，因此我们需要将其传递个`pthread_create()`进行初始化。
- `attr`：指定此线程可能具有的任何属性。一些示例包括设置栈大小，或者可能设置有关线程的调度优先级的信息。通过对`pthread_attr_init()`的单独调用来初始化属性。但是，在大多数情况下，默认设置会很好。在这种情况下，我们将简单地传入NULL值。
- `start routine`：第三个参数是最复杂的，但实际上只是在问：该线程应该在哪个函数开始运行？在C语言中，我们将其称为**函数指针**(function pointer)，它告诉我们以下内容：函数名称(start routine)，该函数名称传递了单个类型为`void *(start routine)`，并且它返回`void *`类型的值（即void pointer）。
- `arg`：传递给线程开始执行的函数的参数。您可能会问：为什么我们需要这些空指针？好吧，答案很简单——将void pointer用作函数start routine的参数可以使我们传入任何类型的参数。将其作为返回值允许线程返回任何类型的结果。

如果此例程(routine)需要整数参数而不是void pointer，则声明将如下所示：

```c
int pthread_create(..., // first two args are the same
                   void *(*start_routine) (int),
                   int arg);
```

如果例程将void pointer作为参数，但返回整数，则它将如下所示：

```c
int pthread_create(..., // first two args are the same
                   int (*start_routine) (void *),
                   void *args);
```

<br>

让我们看一下下面代码的示例。在这里，我们只创建一个传递两个参数的线程，这些参数打包为我们自己定义的单个类型(`myart_t`)。线程一旦创建，就可以简单地将其参数转换为所需的类型，从而根据需要解压参数。

创建线程后，您实际上将拥有另一个活着的执行实体(live executing entity)，该实体具有其自己的调用栈(call stack)，并在与程序中所有当前现有线程相同的地址空间中运行。

```c
#include <stdio.h>
#include <pthread.h>
// Creating a Thread

typedef struct {
    int a;
    int b;
} myarg_t;

void *mythread(vid *arg) {
    myarg_t *args = (myarg_t *) args;
    printf("%d %d\n", args->a, args->b);
    return NULL;
}

int mmain(int argc, char *argv[]) {
    pthread_t p;
    myarg_t args = { 10, 20};

    int rc = pthread_create(&p, NULL, mythread, $args);
    ...
}
```



<br/>
<br/>



### 线程完成

Thread Complete


上面的栗子显示了如何创建线程。但是，如果您要等待线程完成怎么办？您需要做一些特殊的事情才能等待线程完成。特别是，您需要调用`pthread_join()`例程。

```c
int pthread_join(pthread_t thread, void **value_ptr);
```

该例程有两个参数:

- `pthread_t`：用于指定要等待的线程。该变量由线程创建例程初始化（当您将指向它的指针作为参数传递给`pthread_create()`）。如果保留它，则可以使用它来等待该线程终止。
- 第二个参数是指向您希望返回的返回值的指针。由于该例程可以返回任何内容，因此将其定义为返回指向void的指针。因为`pthread_join()`例程会更改传入参数的值，所以您需要传递指向该值的指针，而不仅仅是传递值本身。

让我们看看下面的代码示例。在代码中，再次创建了一个线程，并通过`myarg_t`结构传递几个参数。要返回值，请使用`myret_t`类型。线程完成运行后，一直在`pthread_join()`例程中等待返回的主线程将返回，并且我们可以访问从线程返回的值，即`myret_t`的值。

```c
// Waiting for Thread Completion
typedef struct { int a; int b; } myarg_t;
typedef struct { int x; int y; } myret_t;

void *mythread(void *arg) {
    myret_t *rvals = Malloc(sizeof(myret_t));
    rvals->x = 1;
    rvals->y = 2;
    return (void *) rvals;
}

int main(int argc, char *argv[]) {
    pthread_t p;
    myret_t *rvals;
    myarg_t args = { 10, 20 };
    Pthread_create(&p, NULL, mythread, &args);
    Pthread_join(p, (void **) &rvals);
    printf("returned %d %d\n", rvals->x, rvals->y);
    free(rvals);
    return 0;
}
```

有关此示例的一些注意事项。首先，通常情况下，我们不必进行所有痛苦的参数的打包和拆包操作。例如，如果我们仅创建一个不带参数的线程，则可在创建线程时将NULL作为参数传递。通用，如果我们不关心返回值，则可以将NULL传递给`pthread_join()`。

其次，如果我们只是传递单个值（如，a long long int)，则不必将其打包为参数。下面的代码显示了一个示例。在这种情况下，生活会更简单一些，因为我们不必在结构内部打包参数和返回值。第三，我们应该注意，从线程返回值的防止必须格外小心。具体来说，永远不要返回指向该线程的调用栈(call stack)中分配的内容的指针。如果这样做，想下会发生什么？下面有一段危险的代码，它从上面示例的代码修改而来。

```c
// Simpler Argument Passing to a Thread

void *mythread(void *args) {
    long long int value = (long long int) arg;
    printf("%lld\n", value);
    return (void *) (value + 1);
}

int main(int argc, char *argv[]) {
    pthread_t p;
    long long int rvalue;
    Pthread_create(&p, NULL, mythread, (void *) 100);
    Pthread_join(p, (void **), &rvalue);
    printf("returnd %lld\n", rvalue);
    return 0;
}
```

```c
// a dangerous piece of code

void *mythread(void *arg) {
    myarg_t *args = (myarg_t *) arg;
    printf("%d %d\n", args->a, args->b);
    myret_t oops; // ALLOCATED ON STACK: BAD!
    oops.x = 1;
    oops.y =2;
    return (void *) &oops;
}
```

在这种情况下，变量oops分配在`mythread`栈上。但是，当它返回时，该值会自动释放(deallocated)（这就是为什么栈是如此易于使用的原因！）因此，将指针传递回现在释放的变量将导致各种不良结果。当然，当您尝试打印出您认为返回的值时，您可能回感到惊讶。

最后，您可能会注意到，使用`pthread_create()`创建线程，然后立即调用`pthread_join()`，是创建线程的一种很奇怪的方法。实际上，有一种更简单的方法可以完成此确切任务。它称为**过程调用**(produre call)。显然，我们通常会创建多个线程并等待其完成，否则根本就没有太多用途。

我们应该注意到，并非所有多线程代码都使用join routine。例如，多线程Web Server可能会创建多个工作线程，然后使用主线程无限期地接受请求并将其传递给工作线程。这样的长期存在的程序因此可能不需要join。但是，创建线程以并行执行特定任务的并行程序很可能会使用join来确保所有此类工作在退出或进入下一阶段之前完成。




<br/>
<br/>
<br/>




## 锁

Locks: <http://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks.pdf>


从介绍到并发，我们看到了并发编程中的一个基本问题：我们希望原子地执行一系列指令，但是由于单个处理器（多处理器上同时执行多个线程）存在中断，我们不能。因此，在本章中，我们通过引入称为**锁(lock)**的方法来直接解决此问题。程序员用锁来注释源代码，将它们放在关键部分周围，从而确保任何这样的关键部分都像单个原子指令一样执行。



<br/>
<br/>



### 锁的基本思想

Locks: The Basic Idea


例如，假设我们的关键部分如下所示，即共享变量的规范更新：

```
balance = balance + 1;
```

当然，其它关键部分也是可能的，例如将元素添加到链表或对共享结构进行其它更复杂的更新，但是我们现在仅继续简单的示例。要使用锁，我们在关键部分周围添加一些代码，如下所示：

```
lock_t mutex; // some globally-allocated lock 'mutex'
...
lock(&mutex);
balance = balance + 1;
unlock(&mutex);
```

锁只是一个变量，因此要使用一个锁，您必须声明某种类型的锁变量(lock variable)（如上面的互斥锁）。这个锁变量在任何时候都保持锁的状态。它是可用的（available, unlocked or free），因此没有线程持有该锁，也没有线程获得(acquired, locked or held)，因此恰好有一个线程持有该锁，并且大概在关键部分。我们可以将其它信息存储在数据类型中，例如哪个线程持有锁，或者用于订购锁的队列，但是诸如此类的信息对于锁的用户是隐藏的。

`lock()`和`unlock()`例程的语义很简单。调用例程`lock()`尝试获取锁，如果没有其它线程持有该锁（即，它是空闲的），则该线程将将获取该锁并进入关键部分。有时将此线程称为锁的**所有者**(owner)。如果另一个线程然后对同一个锁(示例中的mutex)调用`lock()`，则当另一个线程持有该锁时它将不会返回。这样，可以防止其它线程进入关键部分，而持有锁的第一个线程在那里。

一旦锁的拥有者调用`unlock()`，锁现在就可以再次使用了(free)。如果没有其它线程在等待锁（即，没有其它线程调用`lock()`并停留在其中），则锁的状态将简单地更改为空闲(free)。如果有等待线程（卡在`lock()`中），其中一个将通知该锁状态的变化，获取该锁，然后进入关键部分。

锁为程序员提供了对调度的最小控制量。通常，我们将线程视为由程序员创建但由操作系统以操作系统选择的任何方式计划的实体。锁将某些控制权交还给程序员。通过在一段代码周围加一个锁，程序员可以保证在该代码中活动的线程最多为一个。因此，锁有助于将传统操作系统调度的混乱转变为更加受控的活动。



<br/>
<br/>



### Pthread Locks

POSIX library用于锁的名称是一个**互斥锁**(mutex)，因为它用于提供线程之间的互斥(mutex exclusion)。即，如果一个线程在关键部分中，它会在该部分完成之前阻止其它进入。因此，当您看到以下POSIX线程代码时，您应该了解它正在执行与上述相同的操作：

```
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

Pthread_mutex_lock(&lock); // wrapper; exits on failure
balance = balance + 1;
Pthread_mutex_unlock(&lock);
```

您可能还会在这里注意到POSIX版本传递了一个变量来锁定和解锁，因为我们使用不同的锁来保护不同的变量。这样做可以提高并发性：代替访问任何关键部分时都使用的一个大锁（粗粒度(coarse-grained)锁策略），通常可以保护不同的数据和具有不同锁的数据结构，从而可以一次将更多线程放入锁代码中（一种更细粒度(fine-grained)的方法）。



<br/>
<br/>



### 建立锁

Building A Lock


到目前为止，您应该从程序员的角度对锁的工作原理有所了解。但是，我们应该如何建立锁？需要什么样的硬件支持？什么样的操作系统支持？将在本章的其余部分中讨论这些问题。

要建立工作锁，我们将需要硬件和操作系统的帮助。多年以来，许多不同的硬件原语已被添加到各种计算机体系结构的指令集中。尽管我们不会研究这些指令的实现方式，但我们将如何研究如何使用它们来构建互斥锁原语。我们还将研究操作系统如何参与完成图片并使我们能够构建复杂的locking library。



<br/>
<br/>



### 评估锁

Evaluating Locks


在构建任何锁之前，我们应该首先了解我们的目标是什么，因此我们要问如何评估特定锁实现的功效。为了评估锁是否有效，我们应该建立一些基本标准。
首先是锁是否执行其基本任务，即提供互斥(mutual exclusion)。基本上，该锁是否起作用，从而防止多个线程进入关键部分。
第二个是**公平**(fairness)。争夺锁的每个线程一旦获得空闲，都可以获得公平的机会吗？另一种看待这种情况的方法是检查更极端的情况：争夺锁的线程是否在这样做的时候挨饿了(starve)，因此从不获取锁？
最终的标准是**性能**(performance)，特别是使用锁增加的时间开销。这里有一些不同的情况值得考虑。一种是不争夺的情况。当单个线程正在运行并获取和释放锁时，这样做的开销是多少？另一种情况是多个线程争用单个CPU上的锁。在这种情况下，是否存在性能问题？最后，当涉及多个CPU且每个线程争用该锁时，锁如何执行？通过比较这些不同的方案，我们可以更好地了解使用各种锁技术对于性能的影响，如下所述。



<br/>
<br/>



### 控制中断

Controlling Interrupts


提供互斥的最早解决方案之一是禁用关键部分的中断。此解决方案是为单处理器系统发明的。代码如下所示：

```c
void lock() {
    DisableInterrupts();
}
void unlock() {
    EnableInterrupts();
}
```

假设我们在单处理器系统上运行。通过在进入关键部分之前关闭中断（使用某种特殊的硬件指令），我们确保关键部分内的代码不会被中断，因此将像执行原子操作一样执行。完成后，我们重新启用中断（同样是通过硬件指令），因此程序照常进行。这种方法的主要优点是它的简单性。您当然不必太费力气，以弄清楚为什么这行得通。在不中断的情况下，线程可以确保其执行的代码将执行，并且没有其它线程会干扰它。

不幸的是，负面因素很多。首先，这种方法要求我们允许任何调用线程执行执行**特权操作(privileged operation)**（打开和关闭中断），并因此相信不会滥用此功能。如您所知，每当我们信任任意程序时，我们都可能会遇到麻烦。在这里，问题以多种方式表现出来：贪婪的程序可能在执行开始时调用`lock()`，从而垄断(monopolize)了处理器。更糟糕的是，一个错误或恶意的程序可能会调用`lock()`并进入一个无限循环(endless loop)。在后一种情况下，操作系统永远不会重新获得对系统的控制，并且只有一种方法：重启系统。将中断禁用作为通用同步解决方案需要对应用程序的过多信任。
其次，该方法不适用于多处理器。如果多个线程在不同的CPU上运行，并且每个线程都尝试进入相同的关键部分，则是否禁用中断都无关紧要。线程能够在其它处理器上运行，因此可以进入关键部分。由于多处理器现在很普遍，因此我们的通用解决方案必须做的更好。
第三，长时间关闭中断可能导致中断丢失，从而导致严重的系统问题。例如，如果CPU错过了磁盘设备已完成读取请求的事实。操作系统将如何知道唤醒等待读的进程？
最后，可能也是最不重要的，这种方法效率低下。与普通指令执行相比，屏蔽或取消屏蔽中断的代码往往会被现代CPU缓慢执行。

由于这些原因，关闭中断仅在有限的上下文中用作互斥原语。例如，在某些情况下，操作系统本身将使用中断屏蔽来确保访问其自己的数据结构时的原子性，或至少防止某些混乱的中断处理情况发生。这种用法很有意义，因为信任问题不再存在于操作系统内部，而操作系统始终信任自己以执行特权操作。



<br/>
<br/>



### 一个失败的尝试：仅使用Loads/Stores

A Failed Attempt: Just Using Loads/Stores


为了建立基于中断的技术，我们将不得不依靠CPU硬件机器提供的指令来建立适当的锁。首先，我们尝试使用单个标志变量来构建简单的锁。在这次失败的尝试中，我们将看到构建锁所需的一些基本思想，并看到了为什么仅使用单个变量并通过常规加载和存储访问它是不够的。

在第一次尝试中（下面代码），想法很简单：使用简单变量(标志)指示某个线程是否拥有锁。进入关键区域的第一个线程将调用`lock()`，该线程测试该标志是否等于1（这种情况下，它不等于1），然后将标志设置为1以指示该线程现在持有该锁。完成关键部分后，线程将调用`unlock()`病清除该标志，从而指示该锁已不再持有。

```
// A Simple Flag

typedef struct __lock_t { int flag; } lock_t;

void init(lock_t, *mutex) {
    // 0 -> lock is available, 1 -> held
    mutex -> flag = 0;
}

void lock(lock_t, *mutex) {
    while (mutex->flag == 1)  // TEST the flag
        ; // spin-wait (do nothing)
    mutex->flag = 1;  // now SET it!
}

void unlock(lock_t *mutex) {
    mutex->flag = 0;
}
```

如果在第一个线程处于关键部分时另一个线程恰巧调用`lock()`，则它将仅在while循环中自旋等待(spin-wait)，以便该线程调用`unlock()`并清除标志。一旦第一个线程执行了此操作，等待线程将退出while循环，将其自身的标志设置为1，然后进入关键部分。不幸的是，代码有两个问题：一是正确性，另一个是性能。一旦习惯了并发编程，就很容易看到正确性问题。想象一下下面的代码，假定`flag=0`。

Trace: No Mutual Exclusion

| Thread 1 | Thread 2 |
| - | - |
| `call lock()` <br> `while(flag == 1)` <br> interrupt: switch to Thread 2 | |
| - | `call lock()` <br> `while(flag == 1)` <br> `flag =1;` <br> interrupt: switch to Thread 1 |
| `flag = 1`; //set flag to 1 (too!) | |

从这种交错(interleaving)中可以看到，通过及时地(timely)（不及时地(untimely)）中断，我们可以很容易地产生一种情况——将两个线程的标志设置为1，因此两个线程都可以进如关键部分。这种行为被专业人员称为`bad`——我们显然未能提供最基本的要求：提供互斥。

性能问题是线程等待获取已持有的锁的方式：它无休止地检查标志的值，这是一种称为**自旋等待(spin-wait)**的技术。

自旋等待浪费时间等待另一个线程释放锁。在单处理器上浪费非常多，在单处理器上，等待者正在等待的线程甚至无法运行（至少在发生上下文切换之前）！因此，随着我们前进并开发更复杂的解决方案，我们还应该考虑避免这种浪费的方法。



<br/>
<br/>



### 通过`test and set`建立工作自旋锁

Building Working Spin Locks with Test-And-Set


由于禁用中断在多处理器上不起作用，并且由于使用loads and stores的简单方法不起作用，因此系统设计人员开始发明对锁定的硬件支持。最早的多处理器系统都提供这种支持。如今，即使对于单CPU系统，所有系统都提供这种类型的支持。

最简单的硬件支持称为`test and set`（或`atomic exchange`）指令。我们通过以下C代码片段定义`test and set`指令的作用：

```c
int TestAndSet(int *old_ptr, int new) {
    int old = *old_ptr; //fetch old value at old_ptr
    *old_ptr = new; // store 'new' into old_ptr
    return old; // return the old value
}
```

在1960年代，Dijkstra向他的朋友们提出了并发问题，其中一位叫Dekker的数学家提出了一个解决方案。与我们在此讨论的解决方案使用特殊的硬件指令甚至是操作系统支持不同，Dekker的算法仅使用`loads and stores`。

Peterson后来完善了Dekker的方法。再一次，仅使用`loads and stores`，其思想是确保两个线程永远不会同时进入关键部分。下面是Peterson的算法（针对两个线程），看看您是否能够理解代码。

```c
int flag[2];
int turn;

void init() {
    // indicate you intend to hold the lock 2/ 'flag'
    flag[0] = flag[1] = 0;
    // whose turn is it? (thread 0 or 1)
    turn = 0;
}
void lock() {
    // 'self' is the thread ID of caller
    flag[self] = 1;
    // make it other thread's turn
    turn = 1 - self;
    while ((flag[1-self] == 1) && (turn == 1 - self)); //spin-wait while it's not your turn
}
void unlock() {
    // simply undo your intent
    flag[self] = 0;
}
```

由于某种原因，开发在没有特殊硬件支持的情况下工作的锁已成为一种流行，这给理论类型带来了很多需要解决的问题。当然，当人们意识到承担一点硬件支持要容易得多时，这一工作就变得毫无用处。此外，由于宽松的内存一致性模型，上述算法无法在现代硬件上运行。因此，它们的功能比以前更加有用。

`test and set`指令的作用如下。它返回`old_ptr`指向的旧值，并同时将该值更新为`new`。当然，关键是此操作是原子执行的。之所以称之为`test and set`，是因为它使您可以测试旧值，同时将内存位置设置为新值。事实证明，此功能稍强的指令足以构建一个简单的自旋锁(spin-lock)，如下面代码所示。

```c
typedef struct __lock_t {
    int flag;
} lock_t;

void init(lock_t *lock) {
    // 0: lock is available, 1: lock is held
    lock->flag = 0;
}

void lock(lock_t *lock) {
    while (TestAndSet(&lock->flag, 1) == 1); // spin-wait (do nothing)
}

void unlock(lock_t *lock) {
    lock->flag = 0;
}
```

确保我们了解此锁的作用。首先想象一下一个线程调用`lock()`而当前没有其它线程持有该锁的情况。因此，标志应为0。当线程调用`TestAndSet(flag, 1)`时，例程将返回flag的旧值，即0。因此，正在测试flag值的调用线程不会在while循环中被不获，而将获得锁。该线程还将原子地将该值设置为1，从而指示该锁现在已被持有。当线程的关键部分结束时，它将调用`unlock()`将标志设置回零。

第二种情况是，一个线程已经持有了锁（即标志为1）。在这种情况下，该线程将调用`lock()`，然后也调用`TestAndSet(flag, 1)`。这次，`TestAndSet()`将在标志处返回旧值(1)（因为持有了锁），同时将其再次设置为1。只要锁由另一个线程持有，`TestAndSet()`将重复返回1，因此该线程自旋，知道最终释放该锁为止。当该标志最终被其它某个线程设置为0时，该线程将再次调用`TestAndSet()`，该调用现在将返回0，同时原子地将值设置为1，从而锁定并进入临界区。

通过将测试(旧锁值)和设置(新值)都将女性单个原子操作，我们确保只有一个线程获得该锁。这就是建立有效的互斥原语的方法。

您现在也可以理解为什么通常将这种类型的锁称为自旋锁(spin lock)。它是最简单的锁类型，可以使用CPU周期简单地自旋，直到锁可用。为了在单个处理器上正常工作，它需要一个抢**先式调度程序**(preemptive scheduler)（即，一个调度程序将通过计时器中断线程，以便不时运行另一个线程）。没有抢占，自旋锁在单个CPU上就没有多大意义，因为在CPU上旋转的线程永远不会放弃它。

<br>

> TIP: THINK ABOUT CONCURRENCY AS A MALICIOUS SCHEDULER
> From this example, you might get a sense of the approach you need to take to understand concurrent execution. What you should try to do is to pretend you are a malicious scheduler, one that interrupts threads at the most inopportune of times in order to foil their feeble attempts at building synchronization primitives. What a mean scheduler you are! Although the exact sequence of interrupts may be improbable, it is possible, and that is all we need to demonstrate that a particular approach does not work. It can be useful to think maliciously! (at least, sometimes)



<br/>
<br/>



### 评估自旋锁

Evaluating Spin Locks


有了基本的自旋锁，我们现在就可以评估先前描述的轴(zxes)的有效性。锁最重要的方面是**正确性(correctness)**：它是否提供互斥？答案是肯定的：自旋锁一次仅允许单个线程进入关键部分。因此，我们有一个正确的锁。

下一个轴是**公平性(fairness)**。自旋锁对正在等待的线程有多公平？您能否保证等待的线程会进入关键部分？不幸的是，答案是个坏消息：自旋锁不提供任何公平性保证。实际上，在争用的情况下，自旋锁可能会永远自旋。简单的自旋锁是不公平的，可能会导饥饿(starvation)。

最终的轴是**性能(performance)**。使用自旋锁的开销是多少？为了更仔细地分析这一点，我们建议考虑一些不同的情况。首先，想象一下线程争夺单个处理器上的锁；其次，考虑线程分布在多个CPU上。

对于自旋锁，在单CPU的情况下，性能开销可能会非常痛苦。设想在关键部分内抢占持有锁的线程的情况。然后，调度程序可能会运行其它所有线程（假设，还有N-1个线程），每个线程都试图获取锁。在这种情况下，这些线程中的每个线程都会在放弃CPU之前的一段时间内自旋，这浪费了CPU周期。

但是，在多个CPU上，自旋锁运行的很好（如果线程数大致等于CPU数）。思路如下：设想CPU1上的线程A与CPU2上的线程B都在争夺锁。如果线程A(CPU1)抓住了锁，然后线程B(CPU2)尝试这样做，则B将自旋。但是，假设关键部分很短，因此很快就可以使用该锁，并被线程B获取。在这种情况下，自旋等待另一个处理器上持有的锁不会浪费很多周期，因此很有效。



<br/>
<br/>



### Compare-And-Swap

一些系统提供的另一种硬件原语称为`compare-and-swap`(在SPARC上)或`compare-and-exchange`(在x86上)指令。下面是该指令的C伪代码。

```c
// compare and swap

int CompareAndSwap(int *ptr, int expected, int new) {
    int original = *ptr;
    if (original == expected)
        *ptr = new;
    return original;
}
```

`compare and swap`的基本思想是测试`ptr`地址处的值是否等于`expected`。如果是，请使用新值更新ptr指向的内存地址。如果不是，什么也不做。无论哪一种情况，都应在该内存位置返回原始值，从而使调用`compare-adn-swap`的代码知道其是否成功。

使用`compare-and-swap`指令，我们可以以`test-and-set`非常相似的放肆构建锁。例如，我们可将上面的`lock()`例程替换为一下内容:

```c
void lock(lock_t *lock) {
    while (CompareAndSwap(&lock->flag, 0, 1) == 1); // spin
}
```

其余代码与`test-and-set`示例相同。它只是检查标志是否为0，如果是，则原子地交换为1，从而获得锁。试图在持有锁的同时获取锁的线程将卡住，知道锁最终被释放。
最后，您可能已经感觉到了，`compare-and-swap`比`test-and-set`更强大。将来，当我们简要研究诸如**lock-free synchronization**之类的主题时，我们将利用这种功能。但是，如果仅使用它构建一个简单的自旋锁，则其行为与我们上面分析的自旋锁相同。



<br/>
<br/>



### Load-Linked and Store-Conditional

一些平台提供了一对协同工作的指令，以帮助构架关键部分。例如，在MIPS架构上，可使用`load-linked`和`store-conditional`指令来构建锁和其它并发结构。这些指令的C伪代码如下所示。Alpha、PowerPC、ARM提供类似的指令。

```c
// Load-linked And Store-conditional

int LoadLinked(int *ptr) {
    return *ptr;
}

int StoreConditional(int *ptr, int value) {
    if (no update to *ptr since LoadLinked to this address) {
        *ptr = value;
        return 1; // success!
    } else {
        return 0; // failed to update
    }
}
```

`load-linked`的操纵非常类似于典型的`load`指令，并仅从内存中获取一个值并将其放置在寄存器中。关键的区别在于`store-conditional`，只有在没有发生中间存储到地址的情况下，该条件才会成功（并更新存储在刚刚`load-linked`的地址上的值）。如果成功，则存储条件返回1并将`ptr`处的值更新为`value`，如果失败，则不会更新`ptr`的值，并返回0。

作为对自己的挑战，请尝试考虑如何使用`load-linked`和`store-confitional`的方式构建锁。完成后，查看下面的代码。改代码提供了一种简单的解决方案，如下所示。

```c
// Using LL/SC To Build A Lock

void lock(lock_t *lock) {
    while (1) {
        while (LoadLinked(&lock->flag) == 1); // spin until it's zero
        if (StoreConditional(&lock->flag, 1) == 1)
            return; // if set-it-to-1 was success: all done
                    // otherwise: try it all over again
    }
}

void unlock(lock_t, *lock) {
    lock->flag = 0;
}
```

`lock()`代码是唯一有趣的部分。首先，线程自旋以等待将标志设置为0（从而指示为持有该锁）。一旦这样，线程尝试通过存储条件获取锁。如果成功，则该线程自动将标志的值更改为1，因此可以进入关键部分。

请注意`store-conditional`的失败可能如何发生。一个线程调用`lock()`并执行`load-linked`，由于未持有该锁，因此返回0。在尝试`store-conditional`之前，它被中断，另一个线程进入锁代码，同时执行`load-linked`指令，并获得0并继续。在这一点上，两个线程已经执行了`load-linked`，并且每个线程都将尝试执行`store-conditional`。这些指令的关键特征是这些线程中只有一个可以成功将标志更新为1，从而获得锁。第二个尝试尝试`store-conditional`的线程将失败（因为另一个线程更新了其load-linked和store-conditional之间的标志的值），因此必须尝试再次获取该锁。

```c
void lock(lock_t *lock) {
    while (LoadLinked(&lock->flag) ||
           !StoreConditional(&lock->flag, 1)); // spin
}
```



<br/>
<br/>



### Fetch-And-Add

最后一个硬件原语是`fetch-and-add`指令，该指令原子地递增值，同时在特定地址返回旧值。它的C伪代码如下所示：

```c
int FetchAndAdd(int *ptr) {
    int old = *ptr;
    *ptr = old + 1;
    return old;
}
```

在此示例中，我们将使用`fetch-and-add`来构建一个更有趣的**ticket lock**。代码在下面。

```c
typedef struct __lock_t {
    int ticket;
    int turn;
} lock_t;

void lock_init(lock_t, *lock) {
    lock->ticket = 0;
    lock->turn = 0;
}

void lock(lock_t *lock) {
    int myturn = FetchAndAdd(&lock->ticket);
    while (lock->turn != myturn); // spin
}

void unlock(lock_t *lock) {
    lock->turn = lock->turn + 1;
}
```

该解决方案使用单个票(ticket)和转向变量(turn variable)来组合以构建锁，而不是使用单个值。基本操作非常简单：当线程希望获取锁时，它首先对票值进行原子`fetch-and-add`，现在将该值视为该线程的转向(turn)。然后使用全局共享的`lock->turn`来确定它是哪个线程。当给定线程`myturn == turn`时，轮到线程进入关键部分。只需通过增加转向即可完成解锁，以便下一个等待线程现在可以进入关键部分。

请注意，此解决方案与我们之前的尝试有一个重要区别：它确保所有线程的进度。一旦线程分配了票值(ticket value)，它将在将来的某个时间进行调度（一旦前面的通过了关键部分并释放了锁）。在我们以前的尝试中，不存在这样的保证。即使在其它线程获得并释放锁的情况下，它也可能永远自旋。

<br>

> TIP: LESS CODE IS BETTER CODE (LAUER’S LAW)
> Programmers tend to brag about how much code they wrote to do something. Doing so is fundamentally broken. What one should brag about, rather, is how little code one wrote to accomplish a given task. Short, concise code is always preferred; it is likely easier to understand and has fewer bugs. As Hugh Lauer said, when discussing the construction of the Pilot operating system: “If the same people had twice as much time, they could produce as good of a system in half the code.” [L81] We’ll call this Lauer’s Law, and it is well worth remembering. So next time you’re bragging about how much code you wrote to finish the assignment, think again, or better yet, go back, rewrite, and make the code as clear and concise as possible.



<br/>
<br/>



### 自旋太多：现在怎么办

Too Much Spinning: What Now?


我们简单的基于硬件的锁很简单（只有几行代码），并且可以正常工作（您甚至可以通过编写一些代码来证明这一点），这是任何系统或代码的两个出色特性。然而，在某些情况下，这些解决方案可能效率很低。假设您在单处理器上运行两个线程。现在，想象一个线程(T0)在关键部分中，因此持有锁，不幸的是被中断了。现在，第二个线程(T1)尝试获取该锁，但发现它已被持有。因此，它开始自旋、自旋...最后，定时器中断消失，T0再次运行，从而释放锁。最后，T1不必自旋太多，就能获得锁。因此，在此情况下，每当线程陷入自旋状态时，它都会浪费时间片只检查不会改变的值，而什么都不做。随着N个线程争用锁，问题变得更加严重。可以以类似的方式浪费N-1个时间片，只需自旋并等待单个线程释放锁即可。因此，我们的下一个问题是：

> THE CRUX: HOW TO AVOID SPINNING
> How can we develop a lock that doesn’t needlessly waste time spinning on the CPU?

单靠硬件支持无法解决问题。我们也需要操作系统执支持！现在，让我们弄清楚这可能如何工作。



<br/>
<br/>



### Just Yield

A Simple Approach: Just Yield, Baby


硬件支持使我们走得很远：工作锁，甚至在获取锁方面的公平性。但是，我们仍然有一个问题：当关键部分发生上下文切换时，并且线程开始无休止地旋转，等待被中断的线程再次运行，该怎么办？

我们的第一个尝试是一种简单而友好的方法：当要自旋时，将CPU放弃给另一个线程。正如Davis可能说的那样，"Just yield, baby!" 下面显示了此方法。

```c
// Lock With Test-and-set And Yield

void init() {
    flag = 0;
}

void lock() {
    while (TestAndSet (&flag, 1) == 1)
        yield();  // give up the CPU
}

void unlock() {
    flag =  0;
}
```

在此方法中，我们假设一个操作系统原始的`yield()`，当一个线程想要放弃CPU并让另一个线程运行时，该线程可以调用它。线程可处于三种状态（running, ready, blocked）。yield只是一个系统调用，它将调用程序从运行状态转(running)移到就绪状态(ready)，从而将另一个线程提升为运行状态。因此，yielding线程本质上使自身调度(deschedules)。

考虑一个CPU上有两个线程的例子。在这种情况下，我们基于屈服的方法效果很好。如果一个线程恰巧调用`lock()`并找到一个持有锁，则它只会屈服CPU，因此另一个线程将运行并完成起关键部分。在这种简单情况下，屈服方法效果很好。

现在，让我们考虑有很多线程（假如100个）反复争用锁的情况。在这种情况下，如果一个线程获取了该锁并在释放前被抢占，则另外的99个线程将分别调用`lock()`，找到持有锁，并屈服于CPU。假设采用某种循环调度(RR)程序，则在持有该锁的线程再次运行之前，这99个轮询程序将执行`run-and-yield`模式。尽管比我们的自旋方法方法更好（浪费99个时间片进行自旋），但这种方法仍然昂贵。上下文切换的成本可能很高，因此有很多浪费。

更糟的是，我们根本没有解决饥饿问题(starvation)。一个线程可能陷入无限的屈服循环中，而其它线程则反复进入和退出关键部分。显然，我们将需要一种直接解决此问题的方法。



<br/>
<br/>



### Using Queues

Using Queues: Sleeping Instead Of Spinning


我们以前的方法的真正问题在于它们留下了太多机会。调度程序确定下一步运行哪个线程。如果调度程序做出了错误地选择，则线程必须自旋以等待锁来运行线程（第一种方法），或立即屈服于CPU（第二种方法）。无论哪种方法，都有浪费的可能，并且无法阻止饥饿。

因此，我们必须明确地控制当前持有者释放锁之后，下一个线程将获得锁。为此，我们将需要更多的操作系统的支持，以及一个队列来跟踪哪些线程正在等待获取锁。

为了简单起见，我们将通过使用由Solaris提供支持的两个调用程序：`park()`调用线程进入睡眠状态(sleep)，`unpark(threadID)`唤醒由threadID指定的特定线程。可以串联使用这两个例程来构建一个锁，如果调用放可以使其进入睡眠状态，并在锁释放时将其唤醒。来看下下面的代码，以了解此类原语。

```c
// Lock With Queues, Test-and-set, Yield, And Wakeup


typedef struct __lock_t {
    int flag;
    int guard;
    queue_t *q;
} lock_t;

void lock_init(lock_t *m) {
    m->flag = 0;
    m->guard = 0;
    queue_init(m->q);
}

void lock(lock_t *m) {
    whild (TestAndSet(&m->guard, 1) == 1); // acquire guard lock by spinning
    if (m->flag == 0) {
        m->flag = 1; //lock is acquired
        m->guard = 0;
    } else {
        queue_add(m->q, gettid());
        m->guard = 0;
        park();
    }
}

void unlock(lock_t *m) {
    while (TestAndSet (&m->guard, 1) == 1); // acquire guard lock by spinning
    if (queue_empty(m->q))
        m->flag = 0; // let go of lock; no now wats it
    else
        unpark(queue_remove(m->q)); // hold lock
                                    // for next thread!
    m->guard = 0;
}
```

<br>

> ASIDE: MORE REASON TO AVOID SPINNING: PRIORITY INVERSION
> One good reason to avoid spin locks is performance: as described in the main text, if a thread is interrupted while holding a lock, other threads that use spin locks will spend a large amount of CPU time just waiting for the lock to become available. However, it turns out there is another interesting reason to avoid spin locks on some systems: correctness. The problem to be wary of is known as priority inversion, which unfortunately is an intergalactic scourge, occurring on Earth [M15] and Mars [R97]!
> Let’s assume there are two threads in a system. Thread 2 (T2) has a high scheduling priority, and Thread 1 (T1) has lower priority. In this example, let’s assume that the CPU scheduler will always run T2 over T1, if indeed both are runnable; T1 only runs when T2 is not able to do so (e.g., when T2 is blocked on I/O).
> Now, the problem. Assume T2 is blocked for some reason. So T1 runs, grabs a spin lock, and enters a critical section. T2 now becomes unblocked (perhaps because an I/O completed), and the CPU scheduler immediately schedules it (thus descheduling T1). T2 now tries to acquire the lock, and because it can’t (T1 holds the lock), it just keeps spinning. Because the lock is a spin lock, T2 spins forever, and the system is hung.
> Just avoiding the use of spin locks, unfortunately, does not avoid the problem of inversion (alas). Imagine three threads, T1, T2, and T3, with T3 at the highest priority, and T1 the lowest. Imagine now that T1 grabs a lock. T3 then starts, and because it is higher priority than T1, runs immediately (preempting T1). T3 tries to acquire the lock that T1 holds, but gets stuck waiting, because T1 still holds it. If T2 starts to run, it will have higher priority than T1, and thus it will run. T3, which is higher priority than T2, is stuck waiting for T1, which may never run now that T2 is running. Isn’t it sad that the mighty T3 can’t run, while lowly T2 controls the CPU? Having high priority just ain’t what it used to be.
> You can address the priority inversion problem in a number of ways. In the specific case where spin locks cause the problem, you can avoid using spin locks (described more below). More generally, a higher-priority thread waiting for a lower-priority thread can temporarily boost the lower thread’s priority, thus enabling it to run and overcoming the inversion, a technique known as priority inheritance. A last solution is simplest: ensure all threads have the same priority



<br/>
<br/>



### 不同的操作系统，不同的支持

Different OS, Different Support


到目前为止，我们已经看到了操作系统可以提供的一种支持，以便在线程库中建立更有效的锁。其它操作系统也提供类似的支持，细节各不相同。例如，Linux提供了一个于Solaris接口相似的`futex`，但提供了更多的内核功能。具体来说，每个`futex`都与一个特定的物理内存位置以及每个`futex`内核队列相关联。调用程序可以根据需要使用futex calls进行睡眠和唤醒工作。

具体来说，有两个调用可用。假定地址处的值等于预期值，调用`futex_wait(address, expected)`会使线程进入睡眠状态。如果不相等，则调用立即返回。调用例程`futex_wake(address)`将唤醒正在队列中等待的一个线程。Linux互斥锁中这些调用的用法如下所示。

```c
// Linux-based Futex Locks

void mutex_lock (int *mutex) {
    int v;
    /* Bit 31 was clear, we got the mutex (the fastpath) */
    if (atomic_bit_test_set (mutex, 31) == 0)
        return;
    atomic_increment (mutex);
    while (1) {
        if (atomic_bit_test_set (mutex, 31) == 0) {
            atomic_decrement (mutex);
            return;
        }
        /* We have to waitFirst make sure the futex value
           we are monitoring is truly negative (locked). */
        v = *mutex;
        if (v >= 0)
            continue;
        futex_wait (mutex, v);
    }
}

void mutex_unlock (int *mutex) {
    /* Adding 0x80000000 to counter results in 0 if and
       only if there are not other interested threads */
    if (atomic_add_zero (mutex, 0x80000000))
        return;

    /* There are other threads waiting for this mutex,
        wake one of them up. */
    futex_wake (mutex);
}
```

nptl库(gnu libc库的一部分)中`lowlevellock.h`中的代码很有趣，原因有几个。首先，它使用单个整数来跟踪是否持有锁和锁上的等待数。因此，如果锁为负，则将其保留。其次，代码片段展示了如何针对常见情况进行优化，特别是在没有争用锁的情况下；仅使用一个线程来获取和释放锁，就完成了很少的工作。



<br/>
<br/>



### Two-Phase Locks


最后一点要注意：Linux方法具有一种古老的方法，这种方法已经使用了好几年，至少可以追溯的1960年代初期的Dahm Locks，现在称为`two-phase lock`。它意识到自旋很有用，特别是在锁即将被释放时。

因此，在第一阶段，锁自旋了一段时间，希望它可以获取锁。但是，如果在第一个自旋阶段未获取锁，则进入第二阶段，在此阶段，调用者进入睡眠状态，并且仅在以后释放锁时才唤醒。上面的Linux锁是这种锁的一种形式，但是它只会自旋一次。对此的一般化可能会在使用futex支持进入睡眠之前，在固定的时间内自旋一个循环。

两阶段锁是混合方法的又一实例，其中结合两个好主意确实可以产生更好的主意。当然，它是否确定取决于很多因素，包括硬件环境，线程数和其它工作负载详细信息。与往常一样，制作一个适用于所有可能用例的通用锁是很大的挑战。



<br/>
<br/>



### 总结

上面的方法显示了如何构建真正的锁：某些硬件支持加上某些操作系统的支持。当然，细节有所不同，执行这种锁的确切代码通常经过高度调整。如果要查看更多详细信息，请查看Solaris或Linux代码库。




<br/>
<br/>
<br/>




## 基于锁的并发数据结构

Lock-based Concurrent Data Structures: <http://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks-usage.pdf>


在离开锁之前，我们将首先介绍如何在一些常见的数据结构中使用锁。在数据结构中添加锁以使其可被线程使用，使该结构线程安全(thread safe)。当然，准确地添加此类锁的方式决定了数据结构的正确性和性能。因此，我们面临的挑战是：

> CRUX: HOW TO ADD LOCKS TO DATA STRUCTURES
> When given a particular data structure, how should we add locks to it, in order to make it work correctly? Further, how do we add locks such that the data structure yields high performance, enabling many threads to access the structure at once, i.e., concurrently?



<br/>
<br/>



### 并发计数器

Concurrent Counters


计数器(counter)是最简单的数据结构之一。它是一种常用的结构，具有简单的接口。下面定义了一个简单的非并行计数器(non-concurrent counter)。

```c
// A Counter without Locks

typedef struct __counter_t {
    int value;
} counter_t;

void init(counter_t *c) {
    c->value = 0;
}

void increment(counter_t *c) {
    c->value++;
}

void decrement(counter_t *c) {
    c->value--;
}

int get(cunter_t *c) {
    return c->value;
}
```

<br>

**简单但不可扩展(Simple But Not Scalable)**

如您所见，非同步计数器(non-synchronized)是一个微不足道的数据结构，需要少量的代码来实现。现在，我们面临下一个挑战：如何使此代码线程安全？下面展示了我们怎样做。

```c
// A Counter With Locks

typedef struct __counter_t {
    int value;
    pthread_mutex_t lock;
} counter_t;

void init(counter_t *c) {
    c->value = 0;
    Pthread_mutex_init(&c->lock, NULL);
}

void increment(counter_t *c) {
    Pthread_mutex_lock(&c->lock);
    c->value++;
    Pthread_mutex_unlock(&c->lock);
}

void decrement(counter_t *c) {
    Pthread_mutex_lock(&c->lock);
    c->value--;
    Phread_mutex_unlock(&c->lock);
}

int get(counter_t *c) {
    Pthread_mutex_lock(&c->lock);
    int rc = c->value;
    Pthread_mutex_unlock(&c->lock);
    return rc;
}
```

<br>

此并发计数器很简单，并且可以正常工作。实际上，它遵循最简单和最基本的并发数据结构共有的设计模式：它仅添加一个锁，该锁在调用操纵数据结构的例程时获取，并在从调用返回时释放。以这种方式，它类似于使用监视器构建的数据结构，其中在您从对象方法调用和返回时自动获取并释放锁。至此，您已经有了一个有效的并发数据结构。您可能回遇到性能问题。如果您的数据结构太慢，则您不仅需要添加一个锁，还需要做更多的事情。因此，如有必要，此类优化是本章其余部分的主题。

为了了解这种简单方法的性能成本，我们运行了一个基准测试(benchmark)，每个线程将一个共享计数器更新固定的次数。然后，我们更改线程数。图29-5显示了总的时间花费，其中一到四线程处于活动状态。每个线程将计数器更新一百万次。该实验是在具有四个CPU的Intel 2.7GHZ i5的iMac上运行的。随着更多CPU的活动，我们希望每单位时间(per unit time)完成更多的工作。

![29-5](/images/OSTEP/29-5.png)

理想情况下，您希望看到线程在多个处理器上完成的速度与单个线程在单处理器上完成的速度一样快。实现这一目标称为完美伸缩(perfect scaling)。即使要完成更多工作，它也是并行完成的，因此完成任务所需的时间不会增加。

<br>

**伸缩计数(Scalable Counting)**

令人惊讶的是，研究人员多年来研究如何构建更多可扩展的计数器。正如操作系统性能分析的最新研究结果表明的那样，可扩展计数器至关重要。如果不进行可扩展计算，Linux上运行的某些工作负载将在多核计算机上遭受严重的可扩展性问题。

已经开发出的许多技术来解决这个问题。我们将介绍一种称为近似计数器(approximate counter)的方法。
近似计数器的工作原理是通过多个本地物理计数器(local physical counter)（每个CPU core一个），和一个全局计数器(global counter)来表示单个逻辑计数器。具体来说，在一台具有四个CPU的计算机上，有四个本地计数器和一个全局计数器。除了这些计数器外，还有锁：每个本地计数器一个、全局计数器一个。

近似计数器的基本思想如下。当运行在给定core上的线程希望增加计数器时，它会增加其本地计数器。通过相应的本地锁同步对此本地计数器的访问。因为每个CPU都有自己的本地计数器，所以CPU上的线程可以在不争用的情况下更新本地计数器，因此该计数器的更新是可伸缩的。

但是，为了使全局计数器保持更新，通过获取全局锁并将其递增本地计数器的值，本地值会定期传输到全局计数器。然后将本地计数器归零。此本地到全局(local-to-global)传输的发生频率由阈值S决定。S越小，计数器的行为就越类似于上面的不可伸缩计数器；S越大，计数器的可伸缩性就越大，但是全局值与实际计数的距离可能越远。可以简单地获取所有本地锁和全局锁来获取确切的值，但这是不可伸缩的。

为了清楚起见，我们来看一个示例。下面的例子中，阈值S设置为5，并且四个CPU的每一个上都有线程更新其本地计数器L1...L4。追踪还显示了全局计数器值，随着时间的增加而下降。在每个时间步长，本地计数器都可以增加；如果本地值达到了阈值S，则将本地值传输到全局计数器并重置本地计数器。

![29-3](/images/OSTEP/29-3.png)

图29-5显示了阈值S为1024的近似计数器的性能。性能出色。在四个处理器上更新计数器四百万次所花费的时间几乎不比在一个处理器上更新计数器一百万次所花费的时间高。

图29-6显示了阈值S的重要性，四个线程在四个CPU上分别使计数器递增100万次。如果S低，则性能不佳；如果S高，则性能出色，但全局计数器滞后。这种精度/性能的折衷是近似计数器实现的。

![29-6](/images/OSTEP/29-6.png)

<br>

> TIP: MORE CONCURRENCY ISN’T NECESSARILY FASTER
> If the scheme you design adds a lot of overhead (for example, by acquiring and releasing locks frequently, instead of once), the fact that it is more concurrent may not be important. Simple schemes tend to work well, especially if they use costly routines rarely. Adding more locks and complexity can be your downfall. All of that said, there is one way to really know: build both alternatives (simple but less concurrent, and complex but more concurrent) and measure how they do. In the end, you can’t cheat on performance; your idea is either faster, or it isn’t.



<br/>
<br/>



### 并发链接列表

Concurrent Linked Lists


























