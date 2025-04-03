# Nginx指南


Nginx 学习笔记。

<br/>

<!--more-->

参考：

- Nginx Cookbook
- [Nginx docs](https://nginx.org/en/docs/)

<br/>

---

<br/>

# 安装

<br/>
<br/>

## 源仓库安装

使用 `yum` 或 `apt` 工具进行安装。

<br/>
<br/>

## 源码安装

源码安装: <https://nginx.org/en/docs/configure.html>

```sh
cd /usr/local

wget http://xxx.xx.com/nginx.tar.gz
tar -zxvf nginx.tar.gz

cd nginx
./configure
    --prefix=/opt/nginx
    --with-http_ssl_module
    --with-pcre=/tools/pcre2-10.39
    --with-zlib=/tools/zlib-1.3
    --with-openssl=/tools/openssl-1.1.1i

make && make install

```

<br/>
<br/>

## 命令

```sh
nginx -h

# 打印版本
-v
# 打印编译器版本和配置参数
-V

# 指定配置文件
-c file

# 使用其他错误日志存储日志
-e file

# 设置全局配置
-g directives

# 在配置测试期间抑制非错误信息
-q

# 设置路径前缀
-p

# 测试配置文件
-t
# 会将配置文件转储到标准输出
-T

# 向主进程发送信号
nginx -s signal
# kill -s signal PID
# stop: 立即停止进程
# quit: 在完成当前正在处理的请求后停止进程，优雅退出
# reload: 重载配置
# reopen: 重新打开日志文件

```

<br/>
<br/>

## 控制

nginx 可用信号控制。主进程的 PID 文件 `nginx.pid`。主进程支持以下信号：

- TERM, INT：快速关停进程
- QUIT：优雅关停进程
- HUP：更改配置，使用新配置启动新的工作进程，优雅关闭旧的工作进程
- USR1：重新打开日志文件
- USR2：升级可执行文件
- WINCH：优雅关闭工作进程

单个工作进程也可以用信号控制，非必须。支持的信号如下：

- TERM, INT
- QUIT
- USR1
- WINCH

<br/>

---

<br/>

# 配置

Nginx 由多个模块组成，这些模块由配置文件中的指令控制。

```conf
# 块指令
{
  # 简单指令
  xxx ;
  ...
  
}
```

<br/>
<br/>

## 配置文件架构

配置文件分为四部分：

- main
- server
- upstream
- location

```conf
user nginx;
worker_processes 4;

events {
  worker_connections 4096;
}

http {
  include /etc/nginx/mime.types;
  ...

  upstream {
    ...
  }

  server {
    ...
    location / {
      ...
    }
  }
}
```

<br/>
<br/>

## 连接处理方法

nginx 支持多种连接处理方法。特定方法的可用性取决于所使用的平台。nginx 通常会自动选择最有效的方法。但可以使用 `use` 命令指定。

支持以下连接处理方法：

- select：标准方法。
- poll：标准方法。
- kqueue：在 FreeBSD 4.1+, OpenBSD 2.9+, NetBSD 2.0 和 macOS 上使用的高效方法。
- epoll：在 Linux 2.6+ 上使用的高效方法。
- /dev/poll：在 Solaris, HP/UX, Unix 上使用的高效方法。
- eventport：Solaris 10+ 上使用的方法，由于已知问题，建议使用 /dev/poll 方法。

<br/>
<br/>

## 测量单位

nginx 支持多种测量单位，用于在配置文件中指定大小、偏移和时间间隔。

<br/>

### 大小和偏移

- k/K: kb
- m/M: mb
- g/G: gb

<br/>

### 时间间隔

多个单位可以合并为一个值，如 1h 30m。

- ms: milliseconds
- s: seconds(默认)
- m: minutes
- h: hours
- d: days
- w: weeks
- M: months/30 days
- y: years/365 days

<br/>
<br/>

## 路径正则匹配

| 符号 | 含义 | 优先级 | 用法 |
| - | - | - | - |
| `=` | 精确匹配 | 最高 |  `location =` |
| `~` | 区分大小写的正则匹配 | 次次之 | `location ~` |
| `~*` | 不区分大小写的正则匹配 | 次次之 | `location ~*` |
| `^~` | 常规字符串匹配 | 次之 | `location ^~` |
| `/` | 通用匹配 | 最低 | `location /` |

<br/>
<br/>

## 全局变量

| 变量名称 | 含义 |
| - | - |
| args | 请求行中的参数 |
| body_bytes_sent | 响应时发送的 body 字节数 |
| content_length | 请求头中的 Content-Length 字段 |
| content_type | 请求头中的 Content-Type 字段 |
| document_root | 当前根路径 |
| host | 请求主机头字段，否则为服务器名称 |
| hostname | 主机名 |
| http_user_agent | 客户端浏览器信息 |
| http_cookie | 客户端 cookie 信息 |
| http_referer | 从哪个页面链接访问过来 |
| http_x_forward_for | 当前端有代理服务器时，设置节点记录客户端地址 |
| remote_addr | 客户端地址 |
| remote_port | 客户端端口 |
| remote_user | 经过验证的用户名 |
| request | 请求信息 |
| request_method | 请求方法 |
| request_body | POST 的数据信息 |
| request_filename | 请求的文件路径 |
| scheme | HTTP 方法 |
| server_protocol | 请求使用的协议 |
| server_addr | 服务器地址 |
| server_name | 服务器名称 |
| server_port | 服务器端口 |
| status | 状态码 |
| request_uri | 带参数的 URI 路径，不含主机名 |
| uri | 不带参数的 URI 路径，不含主机名 |

<br/>
<br/>

## URL重写

rewrite 规则支持 PCRE 正则。

```conf
if (condition) {
  rewrite regex replacement [flag];
}
```

<br/>

---

<br/>

# 示例介绍

<br/>

## nginx如何处理请求

<br/>

### 基于名称的虚拟服务器

nginx 首先会决定由哪台服务器处理请求。

下面是一个简单的配置。

```conf
server {
    listen      80;
    server_name example.org www.example.org;
    ...
}

server {
    listen      80;
    server_name example.net www.example.net;
    ...
}
```

此配置下，nginx 只测试请求头的 "Host" 字段，以确定请求应该路由到哪个服务器。如果请求头不匹配，那么 nginx 就会将请求路由到该端口的默认服务器。如果没有配置默认，则路由到第一个。

注意，默认服务器是监听端口的属性，而不是服务器名称的属性。

```conf
server {
    listen      80 default_server;
    server_name example.net www.example.net;
    ...
}
```

<br/>
<br/>

### 如何防止处理未定义的服务器名称的请求

如果不允许不带 "Host" 头字段的请求，则可以定义一个服务器，直接丢弃这些请求。

```conf
server {
    listen      80;
    server_name "";
    return      444;
}
```

<br/>
<br/>

### 基于名称和基于IP的混合虚拟服务器

一个更复杂的配置：

```conf
server {
    listen      192.168.1.1:80;
    server_name example.org www.example.org;
    ...
}

server {
    listen      192.168.1.1:80 default_server;
    server_name example.net www.example.net;
    ...
}

server {
    listen      192.168.1.2:80 default_server;
    server_name example.com www.example.com;
    ...
}
}
```

在此配置中，nginx 首先根据监听指令请求 IP 和端口。然后，根据与 IP 和端口匹配的服务器名称。

<br/>
<br/>

## 服务器名称

服务器名称由 `server_name` 指令定义，并决定特定请求使用哪个服务器块。

可以使用精确名称、通配符名称或正则来定义。优先级：

1. 精确名
2. 以星号开头的最长通配符名
3. 以星号结尾的最长通配符名
4. 第一个匹配的正则表达式

```conf
server {
    listen       80;
    server_name  example.org  www.example.org;
    ...
}

server {
    listen       80;
    server_name  *.example.org;
    ...
}

server {
    listen       80;
    server_name  mail.*;
    ...
}

server {
    listen       80;
    server_name  ~^(?<user>.+)\.example\.net$;
    ...
}
```

<br/>
<br/>

### 国际化名称

国际化域名应使用 ASCII 表示法指定。

```conf
server {
    listen       80;
    server_name  xn--e1afmkfd.xn--80akhbyknj4f;  # пример.испытание
    ...
}
```

<br/>
<br/>

### 虚拟服务器选择

- 在 SSL 握手期间，根据 SNI
- 处理请求行之后
- 处理 Host 标头后
- 如果无法确定服务器名称，nginx 将使用空名称作为服务器名称。

<br/>
<br/>

### 配置优化

精确名称、以星号开头的通配符名称，以星号结尾的通配符名称，存储在与监控端口绑定的三个哈希表中。哈希表的大小在配置阶段进行了优化，以便能以最小的 CPU 缓存丢失找到名称。

首先搜索精确名称哈希表。如果未找到，则搜索以星号开头的通配符哈希表。如果未找到，则搜索以星号结尾的通配符哈希表。

搜索通配符名称哈希表比精确哈希表要慢，因为名称是按域名部分搜索的。请注意，特殊通配符形式 `.example.org` 存储在通配符哈希表中，而不是精确哈希表中。

正则表达式是按顺序测试的，因此是最慢的，而且不可扩展。

如果定义了许多服务器名称，或定义了很长的服务器名称，则可能需要在 http 层调整 `server_names_hash_max_size` 和 `server_names_hash_bucket_size` 指令。

<br/>
<br/>

## 作为HTTP负载均衡器

可将 nginx 用作高效的 HTTP 负载均衡器，将流量分配给多个应用服务器，并通过 nginx 提高网络应用的性能、可扩展性和可靠性。

<br/>
<br/>

### 负载均衡器方法

nginx 支持的负载均衡器方法：

- 轮询
- 最少连接
- ip-hash

<br/>
<br/>

### 轮询

一个简单的负载均衡配置，默认是轮询方法。

```conf
http {
    upstream myapp1 {
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
        }
    }
}
```

nginx 中的反向代理实现包括 http/https, fastcgi, uwsgi, scgi, memcached 和 grpc。

```conf
fastcgi_pass
uwsgi_pass
memecached_pass
grpc_pass

```

<br/>
<br/>

### 最少连接数

```conf
    upstream myapp1 {
        least_conn;
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }
```

<br/>
<br/>

### 会话保持

使用 ip-hash 将客户端与特定应用服务器绑定。客户端的 IP 地址会被作为散列密钥，以确定客户端的请求应选择服务器组中的哪台服务器，确保同一客户端的请求始终指向同一服务器，除非该服务器不可用。

```conf
upstream myapp1 {
    ip_hash;
    server srv1.example.com;
    server srv2.example.com;
    server srv3.example.com;
}
```

<br/>
<br/>

### 加权负载均衡

通过权重，可进一步影响 nginx 负载均衡算法。

```conf
    upstream myapp1 {
        server srv1.example.com weight=3;
        server srv2.example.com;
        server srv3.example.com;
    }
```

在上述配置下，每 5 个新请求的分配：3 个指向 srv1，一个请求指向 srv2，另一个请求指向 srv3。

<br/>
<br/>

### 健康检查

nginx 开源版本只有被动式的服务器健康检查。如果某个服务器的响应出现错误，nginx 会将该服务器标记为失败，并在一段时间内尽量避免选择该服务器处理后续入站请求。

使用 `max_fails` 和 `fail_timeout` 指令。

<br/>
<br/>

## 配置HTTPS

服务器证书是一个公共实体，它会发送给连接服务器的每个客户端。私钥是一个安全实体，应存储在限制访问的文件中，但 nginx 主进程必须能够读取它。

```conf
server {
    listen              443 ssl;
    server_name         www.example.com;
    ssl_certificate     www.example.com.crt;
    ssl_certificate_key www.example.com.key;
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ...
}
```

<br/>
<br/>

### SSL证书链

有些浏览器可能会抱怨由知名机构签署的证书，而其他浏览器可能会接受该证书。出现这种情况的原因是，签发机构使用了中间证书签署服务器证书，而改中间证书不在与特定浏览器一起分发的知名可信证书颁发机构证书库中。

在这种情况下，证书颁发机构会提供一捆链式证书，这些证书应与以签名服务器证书连接在一起。在合并文件中，服务器证书必须出现在链式证书之前。

```sh
cat www.example.com.crt bundle.crt > www.example.com.chained.crt
```

```conf
server {
    listen              443 ssl;
    server_name         www.example.com;
    ssl_certificate     www.example.com.chained.crt;
    ssl_certificate_key www.example.com.key;
    ...
}
```

<br/>

---

<br/>

# 高性能负载均衡

Nginx 支持 HTTP, TCP 和 UDP 的负载均衡。

Nginx 提供了多种跟踪 cookie 或路由的办法来保证会话保持。

确保 Nginx 所服务的应用的健康状态很重要，负载均衡器必须足够只能，以检测上游服务器的故障，并停止向他们传输流量。

Nginx 提供了两种不同类型的健康检查方法：

- 被动式（Nginx 开源版提供）
- 主动式（Nginx Plus 提供）

<br/>
<br/>

## HTTP负载均衡

```conf
upstream backend {
  server 10.10.12.45:80 weight=1;
  server app.example.com:80 weight=2;
  server spare.example.com:80 backup;
 }
 server {
  location / {
    proxy_pass http://backend;
  }
 }

```

<br/>
<br/>

## TCP负载均衡

```conf
stream {
  upstream mysql_read {
    server read1.example.com:3306 weight=5;
    server read2.example.com:3306;
    server 10.10.12.34:3306 backup;
  }
  server {
    listen 3306;
    proxy_pass mysql_read;
  }
}

```

<br/>
<br/>

## UDP负载均衡

```conf
stream {
  upstream ntp {
    server ntp1.example.com:123 weight=2;
    server ntp2.example.com:123;
  }
  server {
    listen 123 udp;
    proxy_pass ntp;
  }
}

```

<br/>
<br/>

## 负载均衡方式

- 轮询：默认
- 最少连接：least_conn
- 最短时间：least_time
- 通用哈希：hash
- IP 哈希：ip_hash
- 随机算法：random

<br/>
<br/>

## Plus Sticky

Plus 支持更多功能。

<br/>
<br/>

## 被动健康检查

```conf
upstream backend {
  server backend1.example.com:1234 max_fails=3 fail_timeout=3s;
  server backend2.example.com:1234 max_fails=3 fail_timeout=3s;
}

# max_fails 默认值 1
# fail_timeout 默认值 10s
```

<br/>

---

<br/>

# 流量管理

<br/>
<br/>

## AB测试

使用 `split_clients` 模块将一定比例的客户流量定向到不同的上游池。

```conf
split_clients "${remote_addr}AAA" $variant {
  20.0% "backendv2";
  * "backendv1";
}

location / {
  proxy_pass http://$variant
}
```

<br/>
<br/>

## 定位客户端位置

使用模块 nginx-module-geoip 定位客户端的位置。

<br/>
<br/>

## 限制连接数

使用 `limit_conn` 限制打开的连接数。

<br/>
<br/>

## 限制速率

使用 `limit_req_zone` 限制请求速率。

<br/>
<br/>

## 限制带宽

使用 `limit_rate` 和 `limit_rate_after` 指令限制响应客户端的带宽。

<br/>

---

<br/>

# 内容缓存

缓存能够存储响应结果，以供未来再次使用，进而加速内容的提供。

<br/>
<br/>

## 缓存区

使用 `proxy_cache_path` 指令定义共享内存缓存区和内容。

<br/>
<br/>

## 缓存哈希键

使用 `proxy_cache_key` 指令和变量定义缓存的命中和未命中。

<br/>
<br/>

## 缓存锁定

使用 `proxy_cache_lock` 指令确保一次只能将一个请求写入缓存。

<br/>
<br/>

## 使用陈旧缓存

使用 `proxy_cache_use_stale` 指令来定义使用陈旧缓存的场景。

<br/>
<br/>

## 绕过缓存

使用 `proxy_cache_bypass` 指令。

<br/>
<br/>

## 缓存切片

使用 `slice` 将资源分段来提高缓存。

<br/>

---

<br/>

# 可编程性和自动化

<br/>

---

<br/>

# 身份验证

开源版包括基本身份验证和身份验证子请求。Plus 专有的 JWT 验证模块可使用身份验证标准的第三方身份验证提供商集成。

<br/>
<br/>

## 基本身份验证

Nginx 能理解几种不同的密码格式。

```conf
# 密码文件
name1:pwd-hash
name2:pwd2-hash:comment
```

<br/>

```sh
openssl passwd myPWD-123

yum install -y httpd-tools
htpasswd .auth  user1
# 输入密码

curl --user user:myPWD-123 http://localhost
```

<br/>

```conf
location / {
  auth_basic "Private site";
  auth_basic_user_file conf.d/passwd;
}
```

<br/>
<br/>

## 身份验证子请求

使用 `http_auth_request_module` 请求访问身份验证服务，在响应之前验证身份。

```conf
location /private/ {
  auth_request /auth;
  auth_request_set $auth_status $upstream_status;
 }
```

<br/>

---

<br/>

# 安全配置

<br/>
<br/>

## 基于IP的访问

```conf
  allow 10.0.0.0/8;
  deny all
```

<br/>
<br/>

## 允许跨域资源共享

根据 request 方法更改请求头，启用跨域资源共享（CORS）。

```conf
map $request_method $cors_method {
  OPTIONS 11;
  GET  1;
  POST 1;
  default 0;
 }
server {
  # ...
  location / {
    if ($cors_method ~ '1') {
      add_header 'Access-Control-Allow-Methods' 
        'GET,POST,OPTIONS';
      add_header 'Access-Control-Allow-Origin' 
        '*.example.com';
      add_header 'Access-Control-Allow-Headers'
        'DNT,
        ...
```

<br/>
<br/>

## 客户端加密

使用 ssl 模块配置证书加密流量。

```conf
http {
  server {
    listen 8443 ssl;
    ssl_certificate /etc/nginx/ssl/example.crt;
    ssl_certificate_key /etc/nginx/ssl/example.key;
  }
}
```

<br/>
<br/>

## 高级客户端加密

```conf
http {
  server {
    listen 8443 ssl;
    # 设置接受的协议和密码
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    # 从文件中加载 RSA 证书链
    ssl_certificate /etc/nginx/ssl/example.crt;
    # 从文件中加载 RSA 加密密钥
    ssl_certificate_key /etc/nginx/ssl/example.pem;
    # 从变量值生成椭圆曲线证书
    ssl_certificate $ecdsa_cert;
    # 作为文件路径变量的椭圆曲线密钥
    ssl_certificate_key data:$ecdsa_key_path;
    # 客户端-服务器协商缓存
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
  }
```

<br/>
<br/>

## upstream加密

```conf
location / {
  proxy_pass https://upstream.example.com;
  proxy_ssl_verify on;
  proxy_ssl_verify_depth 2;
  proxy_ssl_protocols TLSv1.3;
}
```

<br/>
<br/>

## 保护location

通过 `secure_link` 和 `secure_link_secret` 仅允许拥有安全链接的用户访问资源。

```conf
location /resources {
  secure_link_secret mySecret;
  if ($secure_link = "") { return 403; }
  rewrite ^ /secured/$secure_link;
 }
```

<br/>
<br/>

## 使用过期日期保护location

让某个位置的链接在未来某个时间过期。

```conf
location /resources {
  root /var/www;
  secure_link $arg_md5,$arg_expires;
  secure_link_md5 "$secure_link_expires$uri$remote_addrmySecret";
  if ($secure_link = "") { return 403; }
  if ($secure_link = "0") { return 410; }
}
```

<br/>
<br/>

## 生成会过期的链接

生成一个会过期的链接。

```
index.html?md5=xxx&expires=1924905600
```

<br/>
<br/>

## HTTPS重定向

使用 `rewrite` 将所有 HTTP 流量发送到 HTTPS。

```conf
  listen 80 default_server;
  listen [::]:80 default_server;
  server_name _;
  return 301 https://$host$request_uri;
  # 或
  # if ($http_x_forwarded_proto = 'http') {
  #   return 301 https://$host$request_uri;
  # }
 }

 server {
  listen 443 ssl;
  listen [::]:443 ssl;
  ssl_certificate /etc/nginx/ssl/example.crt; 
  ssl_certificate_key /etc/nginx/ssl/example.key;
  ...
 }
```

<br/>
<br/>

## 基于国家或地区的访问限制

<br/>
<br/>

## 动态应用层DDOS防护

Plus。

<br/>
<br/>

## WAF模块

Plus。

<br/>

---

<br/>

# HTTP2和3

<br/>
<br/>

## 启用HTTP2

```conf
server {
  listen 443 ssl default_server;
  http2 on;
 
  ssl_certificate server.crt;
  ssl_certificate_key server.key;
  # ...
}
```

<br/>
<br/>

## 启用HTTP3

```conf
server {
  # 为了提高兼容性，我们建议
  # 对 QUIC 和 TCP 使用相同的端口号
  listen 443 quic reuseport; # QUIC
  listen 443 ssl; # TCP
  ssl_certificate certs/example.com.crt;
  ssl_certificate_key certs/example.com.key;
  ssl_protocols TLSv1.3;
  location / {
    # 告知 QUIC 在配置端口上可用
    add_header Alt-Svc 'h3=":$server_port"; ma=86400';
    # ...
  }
}
```

<br/>
<br/>

## gRPC

代理 gRPC 连接。

```conf
upstream grpcservers {
  server backend1.local:50051;
  server backend2.local:50051;
}

server {
  listen 80
  http2 on;
  location / {
    grpc_pass grpc://grpcservers;
  }
}
```

<br/>

---

<br/>

# 媒体流

<br/>
<br/>

# 性能调优

<br/>
<br/>

## 控制浏览器缓存

使用客户端的 `cache-control` 请求头。

```conf
location ~* \.(css|js)$ {
  expires 1y;
  add_header Cache-Control "public";
}
```

<br/>
<br/>

## 保持客户端长链接

```conf
http {
  keepalive_requests 320;
  keepalive_timeout 300s;
  # ...
}
```

<br/>
<br/>

## 保持上游长连接

```conf
proxy_http_version 1.1;
 proxy_set_header Connection ""
 upstream backend {
  server 10.0.0.42;
  server 10.0.2.56;
 
  keepalive 32;
 }
```

<br/>
<br/>

## 响应缓冲

允许将响应内容写入缓冲区。

```conf
server {
  proxy_buffering on;
  proxy_buffer_size 8k;
  proxy_buffers 8 32k;
  proxy_busy_buffer_size 64k;
  # ...
}
```

<br/>
<br/>

## 日志缓冲

当系统处于高负载时，希望启用日志缓冲，降低 nginx worker 进程发生阻塞的可能性。

```conf
http {
  access_log /var/log/nginx/access.log main buffer=32k
    flush=1m gzip=1;
}
```

<br/>
<br/>

## 操作系统调优

```conf
# 配置 
worker_connections
worker_rlimit_nofile

# 内核可排队等待 nginx 处理的最大连接数
net.core.comaxconn

# 打开文件描述符
sys.fs.file_max
/etc/security/limits.conf

```





