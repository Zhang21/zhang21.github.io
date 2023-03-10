# 计算机集群




参考：

- 《老男孩Linux运维》
- 《服务器集群系统各概念》: <https://segmentfault.com/a/1190000009923581>
- 《WEB的负载均衡、集群、高可用解决方案》： <https://zhuanlan.zhihu.com/p/23826048>
- [计算机集群维基百科](https://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E9%9B%86%E7%BE%A4)



<br>

<!--more-->

---

<br/>


# 计算机集群

计算机集群简称**集群**(Clusters)，是一种计算机系统。它通过一组散列集成的软件或硬件 连接起来高度紧密地协作完成计算工作。在某种意义上，他们可以被看做是一台计算机。

集群就是指一组（若干）相互独立的计算机，利用高速通信网络组成的一个较大的计算机服务系统，每个集群结点都是运行各自服务的独立服务器。这些服务器之间可以彼此通信，协同向用户提供应用程序、系统资源和数据，并以单一系统的模式加以管理。

当客户机请求集群系统时，集群给用户的感觉就是一个单一独立的服务器，而实际上用户请求的是一组集群服务器。

集群系统中的单个计算机通常称为节点，通常通过内网连接，但也有其它的可能连接方式。集群计算机通常用来改进单个计算机的计算速度和可靠性。



<br>
<br/>

## 服务器集群概念

集群、冗余、负载均衡、主从复制、读写分离、分布式、分布式计算、分布式计算平台、并行计算......

实际生产环境中常有的问题：

- 当数据库性能遇到问题时，是否能够横向扩展，通过添加服务器的方式达到更高的吞吐量，从而充分利用现有的硬件实现更好的投资回报率;
- 是否拥有实时同步的副本，当数据库面临灾难时，可以短时间内通过故障转移的方式保证数据库的可用性。此外，当数据丢失或损坏时，能否通过所谓的实时副本（热备）实现数据的零损失;
- 数据库的横向扩展是都对应用程序透明，如果数据库的横向扩展需要应用程序端进行大量修改，则所带来的后果不仅仅是高昂的开发成本，同时也会带来很多潜在和非潜在的风险.


<br>

### 集群和冗余

集群和冗余并不对立，多台服务器做集群（不是主从），本身就有冗余和负载均衡的效果。
狭义上来说，集群就是把多台服务器虚拟成一台服务器，而冗余的每台服务器都是独立的。

- 集群的侧重点在于协同，多台服务器系统分担工作，提升效率；
- 冗余的侧重点在于防止单点故障，一主多备的架构，也就是主从复制；

> 数据冗余==高可用性==主从

- 主从一定程度上起到了负载均衡的作用，但主要目的还是为了保证数据冗余和高可用性
- 主从只提供一种成本较低的数据备份方案加上不完美的灾难和负载均衡，由于复制存在时间差，不能同步读，所以只是不完善的负载均衡和有损灾备
- 主从显然达不到集群的严格度，不论是 HA 还是 AA（多活并行集群），主从都达不到数据一致性的集群要求



<br/>
<br>

## 为什么要使用集群

- 高性能（Performance）
	大型网站谷歌、淘宝、百度等，都不是几台大型机可以构建的，都是上万台服务器组成的高性能集群，分布于不同的地点。
	只有当并发或总请求数量超过单台服务器的承受能力时，服务器集群的优势才会体现出来。
- 价格有效性（Cost-effectiveness）
	在达到同样性能的需求下，采用计算机集群架构比采用同等运算能力的大型计算机具有更高的性价比。
- 可伸缩性（Scalability）
	当服务负载、压力增长时，针对集群系统进行较简单的扩展即可满足需求，且不会降低服务质量。
- 高可用（Availability）
	单一计算机发生故障时，就无法正常提供服务；而集群架构技术可以是得系统在若干硬件设备发生故障时仍可以继续工作。
	集群系统在提高系统可靠性的同时，也大大减小了系统故障带来的业务损失，目前几乎100%的网站都要求7x24h提供服务。
- 透明性（Transparency）
	多个独立计算机组成的耦合集群系统构成一个虚拟服务器。用户访问集群系统时，就像访问一台高性能、高可用的服务器一样，集群中一部分服务器的上线、下线不会中断整个系统服务，这对用户也是透明的。
- 可管理性（Manageability）
	这个系统可能在物理上很大，但其实很容易管理，就像管理一个单一映像系统一样。
- 可编程性（Programmability）
	在集群系统上，容易开发及修改各类应用程序。




<br>

---

<br/>

# 集群分类

集群分为同构和异构，他们区别在于 “组成集群系统的计算机之间的体系结构是否相同”。

集群计算机按功能和结构可以分为以下几类：

- 均衡集群（Load balancing clusters）
- 用性集群（High-availability clusters）
- 能计算集群（High-performance cluster）
- 计算集群（Grid computing）

> 负载均衡集群（LB）和高可用性集群（HA）是互联网行业常用的集群架构模式


<br>
<br>

## 负载均衡集群

**负载均衡集群用于抗并发。**

> 负载均衡集群典型的开源软件包括：LVS、Nginx、Haproxy 等。

<br>

负载均衡集群可以把很多客户集中的访问请求负载压力尽可能平均分摊在计算机集群中处理。
集群中每个节点都可以一定的访问请求负载压力，并且可以实现访问请求在各节点之间动态分配，以实现负载均衡。
负载均衡集群运行时，一般是通过一个或多个前端负载均衡器（Director）将客户访问请求分发到后端的一组服务器上，从而达到整个系统的高性能和高可用性。
一般高可用性集群和负载均衡集群会使用类似的技术，或同时具有高可用性与负载均衡的特点。

Linux虚拟服务器（LVS）项目 在Linux操作系统上提供最常用的负载均衡软件。

<br>

负载均衡的作用：

- 用户访问请求及数据流量（负载均衡）
- 业务连续性，即7x24h服务（高可用）
- 于Web业务及数据库从库等服务器的业务


<br>
<br>

## 高可用性集群

**高可用性集群用于避免单点故障。**

> 高可用性集群常用开源软件包括：Keepalived、Heartbeat 等。

<br>

一般是指集群中任意一个节点失效的情况下，该节点上的所有任务会自动转移到其他正常的节点上。此过程不会影响整个集群的运行。

当集群中的一个节点系统发生故障时，运行着的集群服务器会迅速做出反应，将该系统的服务分配到集群中其他正在工作的系统上运行。考虑到计算机硬件和软件的容错性，高可用性集群的主要目的是使局群的整体服务尽可能可用。
如果高可用集群中的主节点发生了故障，那么这段时间内将由备节点代替它。备节点通常是主节点的镜像。当它代替主节点时，它可以完全接管主节点（包括Ip和其他资源）提供服务，因此，使集群系统环境对系统环境来说是一致的，既不会影响用户的访问。

高可用性集群使服务器系统的运行速度和响应速度会尽可能的快。它们经常利用在多台机器上运行的冗余节点和服务来相互跟踪。
如果某个节点失败，它的替补者将在几秒钟或更多时间内接管它的职责。因此，对于用户来说，集群里的任意一台机器宕机，业务都不会受影响。

高可用性集群的作用：

- 当一台机器宕机后，另外一台机器接管宕机的机器的Ip资源和服务资源，提供服务；
- 常用于不易实现负载均衡的应用，如负载均衡器、主数据库、主存储对之间；



<br/>
<br>

## 高性能计算集群

高性能计算集群也称并行计算。通常，高性能计算集群涉及为集群开发的并行应用程序，以解决复杂的科学问题。

高性能计算集群对外就好像一个超级计算机，这种超级计算机内部由数万个独立服务器组成，并且在公共消息传递层上进行通信以运行并行应用程序。



<br>
<br/>

## 高可用与负载均衡有什么区别

- HA偏重于备用资源，切机时会有业务的断开的，保证了数据的安全，但造成资源的浪费；
- LB侧重于资源的充分应用，没有主备的概念，只有资源的最大限度的加权平均应用，基本不会业务的中断；
- HA的目的是不中断服务，LB的目的是为了提高接入能力。虽然经常放一起用，但确实是两个不同的领域；
- HA在一条路不通的时候提供另一条路可走，而 LB 就类似于是春运时的多个窗口；




<br/>

---

<br>


# 集群软硬件


<br>

企业运维中常见集群产品：

- 开源集群软件：
		+ Nginx, LVS, Haproxy, Keepalived, Heartbear...
- 商业集群硬件：
		+ F5， Netscaler,Radware, A10...


如何选择开源集群软件：

- 网站在并发访问和总访问量不是很大的情况下，建议首选Nginx负载均衡，Nginx配置简单使用方便安全稳定。 另一个实现负载均衡的产品为Haproxy
- 如果要考虑Nginx负载均衡的高可用功能，建议首选Keepalived软件，因为安装配置简单方便稳定。类似高可用软件还有Heartbeat，但比较复杂
- 如果是大型企业，负载均衡可以使用 LVS+Keepalived 在前端做四层转发，后端使用Nginx或Haproxy做七层转发，再后面是应用服务器。如果是数据库与存储的负载均衡和高可用，可选用`LVS+Heartbeat`

![](/images/Zabbix/cluster.png)




<br>

---

<br/>


# 负载均衡

所谓负载均衡，就是把大访问量分发给不同的服务器，也就是分流请求。


<br>

## HTTP重定向协议实现负载均衡

HTTP 重定向就是应用层的请求转发，用户的请求其实已经到了HTTP重定向负载均衡服务器，服务器根据算法要求用户重定向，用户收到重定向请求后，再次请求真正的集群.

- 优点：简单
- 缺点：性能较差


<br>
<br>

## DNS域名解析负载均衡

DNS域名解析负载均衡就是在用户请求DNS服务器，获取域名对应的IP地址时，DNS服务器直接给出负载均衡后的服务器IP。

- 优点：交给DNS，不用我们去维护负载均衡服务器
- 缺点：当一个应用服务器挂了，不能及时通知DNS，而且DNS负载均衡的控制权在域名服务商那里，网站无法做更多的改善和更强大的管理


<br>
<br>

## 反向代理负载均衡

在用户的请求到达方向代理服务器时（已到达网站机房），由于反向代理服务器根据算法转发到具体的服务器，常用的Apache，Nginx都可以充当反向代理服务器。

- 优点：部署简单
- 缺点：代理服务器可能成为性能的瓶颈，特别是一次上传大文件


<br>
<br>

## IP负载均衡(LVS-NAT)

LVS集群中实现的三种IP负载均衡技术。

<br>

在请求到达负载均衡器后，负载均衡器通过修改请求的目的IP地址，从而实现请求的转发，做到负载均衡。

- 优点：性能更好
- 缺点：负载均衡器的带宽称为瓶颈


<br>
<br>

## 直接路由负载均衡(LVS-DR)

数据链路层负载均衡，在请求到达负载均衡器后，负载均衡器通过修改请求的Mac地址，从而做到负载均衡，与IP负载均衡不一样的是，当请求访问完服务器之后，直接返回客户，而无需在经过负载均衡器。


<br/>
<br>

### IP隧道负载均衡(LVS-TUN)





<br>

---

<br>


# 主从复制


主从是一种用于数据容错和灾备的高可用解决方案，而不是一种处理高并发压力的解决方案（负载均衡是用来抗并发的）。

> 如MySQL主从复制，MongoDB主从复制(副本集)

- 主机负责查询，从机负责增删改
- 可以在从机上执行备份，以避免备份期间影响主机的服务
- 主从复制后，也可以在从机上查询，以降低主机的访问压力。但是，只有更新不频繁的数据或者对实时性要求不高的数据可以通过从服务器查询，实时性要求高的数据仍需在主服务器查询（因为主从复制有同步延迟，所以不能保证强数据一致性）


<br>
<br>

## 主从复制和读写分离

- 主从复制是实现读写分离的技术之一，也是实现读写分离的前提条件
- 做读写分离时最重要的就是确保 读库 和 写库 的数据统一，而主从复制是实现数据统一最简单的方法（并不能够保证强数据的一致性）
- 读写分离，顾名思义，就是一个表只负责向前台页面展示数据，而后台管理人员对表的增删改在另一个表中，把两个表分开，就是读写分离
- 主从复制则是一个表数据 增删改 之后会及时更新到另一个表中，保证两个表的数据一致


<br>
<br>

## 主从类型

- 双机热备=主机+备机
- 主要应用运行在主机，备机即备用机器。备机不工作，主机出现故障时备机接管主机的所有工作
- 双机互备=主机（备机） + 备机（主机）
- 互为主备，部分应用运行于主机，部分应用运行于备机，主机备机同时工作
- 双机双工=主机+主机
- 两台主机同时运行应用，主机备机同时工作




<br>

---

<br>


# 分布式

- 广义上的分布式是指，将不同的服务分布在不同的服务器上
- 集群是指，将几台服务器集中在一起，实现同一业务
- 分布式中的每一个节点都可以做集群，而集群并不一定是分布式的




































