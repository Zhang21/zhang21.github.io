# 负载均衡器


常用的负载均衡器 Nginx, HAProxy 和 LVS 学习笔记。

<br/>

<!--more-->

参考：

- [Nginxg 官方文档](https://nginx.org/en/docs/)
- [HAProxy 官方文档](https://docs.haproxy.org/)
- [LVS 官方文档](http://www.linuxvirtualserver.org/Documents.html)

<br/>

---

<br/>

# 介绍

| 负载均衡 | 层级 | 性能 | 特点 | 使用场景 |
| - | - | - | - | - |
| Nginx | L7/L4 | 中等 | 反向代理、动静分离、缓存、压缩 | 适用于 Web 负载均衡 |
| HAProxy | L4/L7 | 高 | 高性能、健康检查强大 | 适用于高并发业务和数据库负载均衡 |
| LVS | L4 | 最高 | 内核级、超高性能 | 适用于大规模集群、数据库 |








