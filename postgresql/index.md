# PostgreSQL


PGSQL 文档笔记。

<!--more-->

---

参考：

- [PG14 中文文档](http://www.postgres.cn/docs/14/)
- [PG Docs](https://www.postgresql.org/docs/current/index.html)

<br/>
<br/>

# 前言

<br/>
<br/>

## PG是什么

PostgreSQL 是一个对象关系型数据库管理系统。因为自由宽松的许可证，任何人都可以以任何目的免费使用、修改和分发PostgreSQL， 不管是私用、商用还是学术研究目的。

<br/>
<br/>

## PG简史

伯克利的 POSTGRES 项目，到 Postgres95，到 PostgreSQL。

<br/>
<br/>

## 约定

以下约定被用于命令大纲：

- 方括弧（`[]`）表示可选部分。
- 花括弧（`{}`）和竖线（`|`）表示你必须选取一个候选。
- 点（`...`）表示它前面的元素可以被重复。

<br/>

---

<br/>

# 教程

<br/>
<br/>

## 从头开始

<br/>
<br/>

### 基础架构

PG 是一种客户端-服务器的模型。一次 PG 会话由下列相关的进程组成：

- 服务器进程（postgres）：管理数据库文件、接受来自客户端应用与数据库的连接并且代表客户端在数据库上执行操作。
- 客户端应用：客户端是多种多样的。

PG 服务器可以处理来自客户端的多个并发请求。它为每个连接启动（fork）一个新的进程。从这时开始，客户端和服务器新的进程就不再经过最初的 postgre 进程的干涉进行通讯。因此，服务器守护者进程总是在运行并等待客户端连接，而客户端和相关联的服务器进程则是起起停停。

<br/>
<br/>

### 创建一个数据库

数据库名必须是以字母开头并且小于 63 个字符长。

```sh
# 创建一个新的数据库
createdb mydb

# 删除数据库
dropdb mydb
```

<br/>
<br/>

### 访问数据库

可以通过以下方式访问 PG：

- 使用 `pgsql` 交互式客户端程序
- 使用图形化管理程序
- 通过编程语言

```sh
pgsql mydb

# mydb=# 提示符意味着数据库超级用户

# 内部命令，以 \ 开头
mydb=> \h
mydb=> \q

mydb=> SELECT version();
mydb=> SELECT current_date;
```

<br/>

---

<br/>

# SQL语言

SQL 语言: <http://www.postgres.cn/docs/14/sql.html>

使用 SQL 语言来执行操作，SQL 的使用详情这里不写了，之前的博客有写。

<br/>

---

<br/>

# 相关参考

提供关于相应主题的权威、完整和正式的总结。

<br/>
<br/>

## SQL命令

SQL 命令: <http://www.postgres.cn/docs/14/sql-commands.html>

<br/>
<br/>

## PG客户端应用

PG 客户端应用和工具：<http://www.postgres.cn/docs/14/reference-client.html>

- `clusterdb`：聚簇一个 PG 数据库
- `createdb`：创建一个新的 PG 数据库
- `createuser`：定义一个新的 PG 用户账户
- `dropdb`：移除一个 PG 数据库
- `dropuser`：一个一个 PG 用户账户
- `ecpg`：嵌入式 SQL C 预处理器
- `pg_amcheck`：在一个或多个 PG 数据库中检查损坏
- `pg_basebackup`：获得一个 PG 集簇的一个基础备份
- `pgbench`：在 PG 上运行一个基准测试
- `pg_config`：获取已安装 PG 的信息
- `pg_dump`：将 PG 数据库抽取为一个脚本文件或其他归档文件
- `pg_dumpall`：将一个 PG 数据库集簇抽取到一个脚本文件中
- `pg_isready`：检查一个 PG 服务器的连接状态
- `pg_receivewal`：以流的方式从一个 PG 服务器得到预写式日志
- `pg_recvlogical`：控制 PG 逻辑解码流
- `pg_restore`：从一个由 pg_dump 创建的归档文件中恢复一个 PG 数据库
- `pg_verifybackup`：验证 PG 集群的基础备份的完整性
- `psql`：PG 的交互式终端
- `reindexdb`：重新索引一个 PG 数据库
- `vacuumdb`：对一个 PG 数据库进行垃圾收集和分析

<br/>
<br/>

## PG服务器应用

PG 服务器应用和工具：<http://www.postgres.cn/docs/14/reference-server.html>

- `initdb`：创建一个新的 PG 数据库集簇
- `pg_archivecleanup`：清理 PG WAL 归档文件
- `pg_checksums`：在 PG 数据库集簇中启用、禁用或检查数据校验和
- `pg_controldata`：显示一个 PG 数据库集簇的控制信息
- `pg_ctl`：初始化、启动、停止或控制一个 PG 服务器
- `pg_resetwal`：重置一个 PG 数据库集簇的预写式日志以及其他控制信息
- `pg_rewind`：把一个 PG 数据目录与另一个从该目录中复制出来的数据目录同步
- `pg_test_fsync`：为 PG 判断最快的 wal_sync_method
- `pg_test_timing`：度量计时开销
- `pg_upgrade`：升级 PG 服务器实例
- `pg_waldump`：以人类可读的形式显示一个 PG 数据库集簇的预写式日志
- `postgres`：PG 数据库服务器
- `postmaster`：PG 数据库服务器

<br/>

---

<br/>

# 服务器管理

包括软件安装、搭建和配置一个服务器，管理用户和数据库以及维护任务。

<br/>
<br/>

## 二进制安装

下载地址：<https://www.postgresql.org/download/>

<br/>
<br/>

## 源码安装

<br/>
<br/>

### 简单版

默认是安装到 `/usr/local/pgsql` 下，可以修改。

```sh
./configure
make
su
make install
adduser postgres
mkdir /usr/local/pgsql/data
chown postgres /usr/local/pgsql/data
su - postgres
/usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data
/usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data -l logfile start
/usr/local/pgsql/bin/createdb test
/usr/local/pgsql/bin/psql test
```

<br/>
<br/>

### 要求

编译 PG 需要以下软件：

- GNU make 3.80 或以上
- GCC
- 解压缩软件
- 默认时将自动使用GNU Readline库。
- 默认的时候将使用zlib压缩库。 如果你不想使用它，那么你必须给configure声明--without-zlib选项。使用这个选项关闭了在pg_dump和pg_restore中对压缩归档的支持。

<br/>
<br/>

### 获取源码

从下载地址下载源码包。

<br/>
<br/>

### 安装过程

```sh
# 配置
./configure

# 构建
make
make all
# make world: 构建可以构建的所有内容，包括文档（HTML 和手册页）和附加模块
# make world-bin: 构建可以构建的所有内容，和附加模块，除了文档

# 回归测试
make check

# 安装文件
make install
# make install-docs: 安装文档

# 如果只想安装客户端应用程序和接口库
make -C src/bin install
make -C src/include install
make -C src/interfaces install
make -C doc install

# 撤销安装
make uninstall

# 清理
make clean
```

<br/>
<br/>

### 安装后设置

设置共享库。最广泛的方法是设置环境变了 `LD_LIBRARY_PATH`。

设置环境变量 PATH。

```sh
LD_LIBRARY_PATH=/usr/local/pgsql/lib
export LD_LIBRARY_PATH

PATH=/usr/local/pgsql/bin:$PATH
export PATH
```

<br/>
<br/>

## 服务器设置和操作

<br/>
<br/>

### 用户账户

PG 的预打包版本通常会在安装期间自动创建一个合适的账户（通常是 `postgres`）。你也可以使用 `useradd` 命令添加用户。

<br/>
<br/>

### 创建一个数据库集簇

在你能做任何事情之前，你必须在磁盘上初始化一个数据库存储区域。我们称之为一个数据库集簇。一个数据库集簇是被一个运行数据库服务器的单一实例所管理的多个数据库的集合。

一个数据库集簇将包含一个名为 postgres 的数据库，它表示被功能、用户和第三方应用所使用的默认数据库。数据库服务器本身并不要求 postgres 数据库存在。

在文件系统术语中，一个数据库集簇是一个单一目录，所有数据都将被存储在其中。如比较流行的位置 `/var/lib/pgsql/data` 或 `/usr/local/pgsql/data`。 数据目录必须在使用前初始化，必须使用与 PostgreSQL 一起安装的程序 initdb。

如果数据目录已经包含文件，initdb 将拒绝运行，避免覆盖一个已有的安装。

```sh
# 必须登录 PG 用户后才能执行命令
su - postgres

# 手动初始化数据库集群
initdb -D /usr/local/pgsql/data

# 或通过 pg_ctl 程序
pg_ctl -D /usr/local/pgsql/data initdb
```

虽然目录的内容是安全的，但默认的客户端认证设置允许任意本地用户连接到数据库甚至成为数据库超级用户。

如果你不信任其他本地用户， 我们建议你使用 initdb 的 `-W`、`--pwprompt` 或 `--pwfile` 选项之一给数据库超级用户赋予一个口令。还可以指定 `-A md5` 或 `-A password`，这样就不会使用默认的 trust 身份认证。或者修改 `pg_hba.conf` 配置文件。

<br/>
<br/>

### 启动数据库服务器

根据不同的安装方式，启动命令也有所不同。

```sh
postgres -D /usr/local/pgsql/data >logfile 2>&1 &

# pg_ctl 命令简化
pg_ctl start -l logfile

# systemd
systemctl start postgresql
```

<br/>
<br/>

### 管理内核资源

PG 某些时候会耗尽操作系统的各种资源限制，本节解释 PG 使用额内核资源以及你可以采取的解决内核资源耗尽的相关问题。

<br/>
<br/>

#### 共享内存和信号量

PG 需要操作系统提供进程间通信特性，特别是共享内存和信号量。Unix 系统通常提供 "System V" 和 "POSIX" IPC。

默认情况下，PG 分配很少量的 System V 共享内存，和大量的匿名的 mmap 共享内存。另外，在服务器启动时会创建大量信号量。目前，POSIX 信号量用于 Linux 和 FreeBSD 系统，其它平台则使用 System V 信号量。

可以使用 `sysctl` 命令来修改默认配置，最后请记得写入 `/etc/sysctl.conf` 文件。

```sh
sysctl -w kernel.shmmax=17179869184
sysctl -w kernel.shmall=4194304
```

<br/>
<br/>

#### Systemd RemoveIPC

如果正在使用 systemd，则必须注意 IPC 资源不会被操作系统过早删除。

控制当用户完全退出时是否移除IPC对象。系统用户免除。 此设置在死板的 systemd 中默认为 on， 但某些操作系统分配默认为关闭。

在 `/etc/systemd/logind.conf` 或其他适当的配置文件中。

<br/>
<br/>

#### 资源限制

Linux 系统强制了许多资源限制，这些限制可能干扰 PG 服务器的操作。

尤其重要的是对每个用户的进程数目的限制、每个进程打开文件数目的限制以及每个进程可用的内存的限制。

<br/>
<br/>

#### 内存过量使用

Linux 上默认的虚拟内存行为对 PG 不是最优的，如果 PG 进程内存要求导致系统用光虚拟内存，那么内核可能会 OOM PG 的 postmaster 主进程。

如果 PG 本身导致系统内存耗尽，可以通过修改配置来避免。

```sh
# 尽管此设置不会阻止OOM 杀手被调用，但它可以显著地降低其可能性并且将因此得到更鲁棒的系统行为。
sysctl -w vm.overcommit_memory=2

# 进程相关的OOM score adjustment值设置为-1000，从而保证它不会成为 OOM 杀手的目标。这样做最简单的方法是在 postmaster 的启动脚本中执行
echo -1000 > /proc/self/oom_score_adj
export PG_OOM_ADJUST_FILE=/proc/self/oom_score_adj
export PG_OOM_ADJUST_VALUE=0
```

<br/>
<br/>

#### 大页面

当 PG 使用大量连续的内存块时，使用大页面会减少开销，特别是在使用大 shared buffers 时。

```sh
# 通过 huge_page_size 明确要求为2MB或1GB。 假设 2MB 大页面，6490428 / 2048 大约得出3169.154， 所以在这里例子里我们需要至少 3170 大页面。
sysctl -w vm.nr_hugepages=3170

echo 3170 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages
```

<br/>
<br/>

### 关闭服务器

有几种方法关闭服务器。在后台，它们都简化为向主管 postgres 进程发送信号。

- `SIGTERM`：智能关闭模式。接收此信号后，服务器将不允许新连接，但是会让现有的会话正常结束它们的工作。仅当所有的会话终止后它才关闭。
- `SIGINT`：快速关闭模式。服务器不再允许新的连接，并向所有现有服务器进程发送 SIGTERM，让它们中断当前事务并立刻退出。
- `SIGQUIT`：立即关闭模式。服务器将给所有子进程发送 SIGQUIT 并且等待它们终止。如果有任何进程没有在 5 秒内终止，它们将被发送 SIGKILL。主管服务器进程将在所有子进程推出之后立刻退出，而无需做普通的数据库关闭处理。这将导致在下一次启动时（通过重放 WAL 日志）恢复。不推荐使用此方式。

`pg_ctl` 程序提供了一个发送这些信号关闭服务器的方便的接口。或者直接给相关进程发送信号。

```sh
kill -INT `head -1 /usr/local/pgsql/data/postmaster.pid`
```

<br/>

最好不要使用 SIGKILL（9）关闭服务器。这样做将会阻止服务器释放共享内存和信号量。此外，使用 SIGKILL 杀掉 postgres 进程时， postgres 不会有机会将信号传播到它的子进程，所以可能也必须手工杀掉单个的子进程。

要终止单个会话同时允许其他会话继续，使用 `pg_terminate_backend()` 或发送 SIGTERM 信号到该会话相关的子进程。

<br/>
<br/>

### 升级一个PG集簇

如何把你的 PG 升级到一个更新的发行版。

PG 版本号由主要版本和次要版本组成。次要版本从来不改变内部存储格式并且总是向后兼容同一主版本中的次要版本。例如 10.1 与 10.0 和 10.6 兼容。

要在兼容的版本间升级，只需要简单地在服务器关闭时替换可执行文件并重启服务器。

对于 PG 的主发行版，内部数据存储格式经常被改变，这使得升级复杂化。传统的方法是数据转储（`pg_dump`）然后重新载入到数据库，不过这可能会很慢。一种更快的方式是 `pg_upgrade`。

<br/>
<br/>

#### 通过pg_dumpall升级数据

使用 `pg_dumpall` 逻辑备份工具，然后重新载入的新的发行版中。

```sh
# 备份
pg_dumpall > outputfile

# 关停旧服务器
pg_ctl stop
/etc/rc.d/init.d/postgresql stop

# 备份旧目录
mv {/usr/local/pgsql,-old}

# 安装新发行版
# 修改配置
# 启动新发行版
# 从备份文件中恢复
/usr/local/pgsql/bin/psql -d postgres -f outputfile
```

<br/>

通过在一个不同的目录中安装新的服务器并且并行地在不同的端口运行新旧发行版，可以达到最低的停机时间。

```sh
pg_dumpall -p 5432 | psql -d postgres -p 5433
```

<br/>
<br/>

#### 通过pg_upgrade升级数据

pg_upgrade 模块允许一个安装从一个 PGSQL 主版本就地升级成另一个主版本。升级可以在数分钟内被执行，特别是使用 `--link` 模式时。

[pg_upgrade 文档](http://www.postgres.cn/docs/14/pgupgrade.html)

<br/>
<br/>

#### 通过复制升级数据

也可以用 PG 的已更新版本逻辑复制来创建一个后备服务器，逻辑复制支持在不同主版本的 PG 之间的复制。一旦它与主服务器同不好，你可以切换主并将后备服务器作为主，然后关闭旧的数据库实例。这样一种切换使得一次升级的停机时间只有数秒。

<br/>
<br/>

### 阻止服务器欺骗

服务器在运行时，它不可能让恶意代码取代正常的数据库服务器。然而，当服务器关闭时，一个本地用户可以通过启动它们自己的服务器来欺骗正常的服务器。行骗的服务器可以读取客户端发送的密码和查询语句，但不会返回任何数据，因为 PG_DATA 目录权限是安全的。欺骗是可能的，因为任何用户都可以启动一个数据库服务器。客户端无法识别一个无效的服务器，除非专门配置。

一种阻止 local 连接七篇的方法是使用 Unix 域套接字目录，该目录只对一个被信任的本地用户有写权限。这可以防止恶意用户在该目录中创建自己的套接字文件。

local 连接的另一个选项是对客户端使用 requirepeer 指定所需的连接到该套接字的服务器进程的拥有者。

要在 TCP 连接上防止欺骗，要么使用 SSL 证书，并且确保客户检查服务器的证书，或者使用 GSSAPI 加密。

要防止 SSL 欺骗，服务器必须配置为仅接受 hostssl 连接，并且有 SSL 密钥和证书文件。

要防止 GSSAPI 欺骗，必须将服务器配置为仅接受 hostgssenc 连接并对它们使用 gss 身份认证。

<br/>
<br/>

### 加密选项

PG 提供了几个不同级别的加密。

<br/>
<br/>

### 用SSL进行安全的连接

使用 SSL 连接加密客户端/服务器通讯的本地支持，它可以增加安全性。

<br/>
<br/>

### GSSAPI加密

<br/>
<br/>

### 使用SSH隧道的安全连接

<br/>
<br/>

## 服务器配置

<br/>
<br/>

### 设置参数

参数名称和值。

所有参数名都是大小写不敏感的。每个参数都可以接受五种类型之一的值： 布尔、字符串、整数、 浮点数或枚举。

<br/>

通过配置文件影响参数。

设置这些参数最基本的方法是编辑 `postgresql.conf` 文件，它通常保存在数据目录中。

主服务器进程每次收到 SIGHUP 信号（最简单的方法是运行 `pg_ctl reload` 或者调用 SQL 函数 `pg_reload_conf()` 来发送信号）后都会重新读取此配置文件。

注意，有些参数只能在服务器启动的时候生效，对于这些参数，直到服务器下次重启才会生效。

PG 数据目录还有一个 `postgresql.auto.conf`，它具有和 `postgresql.conf` 相同的格式但是原自动编辑，而不是手工编辑。

<br>

通过 SQL 影响参数。

PG 提供了三个 SQL 命令来配置参数：

- `ALTER SYSTEM`：等效于 `postgresql.conf` 文件。
- `ALERT DATABASE`：针对一个数据库覆盖其全局设置。
- `ALTER ROLE`：用户指定的值来覆盖全局设置和数据库设置。

一旦一个客户端连接到数据库，PG 会提供两个额外的 SQL 命令用以影响会话本地的设置配置：

- `SHOW`：查看任何参数的当前值。
- `SET`：修改一个会话可以本地设置的当前值，不影响其它会话。

此外，系统试图 `pg_settings` 可以查看和改变会话本地的值：

- 查询这个试图与使用 `SHOW ALL` 相似，但是可以提供更多细节。
- 在这个视图上使用 `UPDATE` 指定更新 settings 列，其效果等同于 `SET` 命令。

```sql
SET configuration_parameter TO DEFAULT;

# 等效于
UPDATE pg_settings SET setting = reset_val WHERE name = 'configuration_parameter';
```

<br/>

通过 shell 影响参数。

```sh
# 通过 -c 传递参数
postgres -c log_connections=yes -c log_destination='syslog'

# 当通过libpq启动一个客户端会话时，可以使用PGOPTIONS 环境变量指定参数设置。
env PGOPTIONS="-c geqo=off -c statement_timeout=5min" psql
```

<br/>

管理配置文件内容。

PG 提供了一些特性用于把复杂的 `postgresql.conf` 文件分解成子文件。

```conf
# 包含其它文件
include 'filename'

# 包含其它目录
include_dir 'directory'
```

<br/>
<br/>

### 文件位置

除了 `postgresql.conf` 文件外，PG 还有另外两个手工编辑的配置文件，它们控制客户端认证。默认情况下，这三个文件都存放在数据库集簇的数据目录中。

```conf
# 指定用于数据存储的目录
data_directory (string)

# 指定主服务器配置文件（通常叫postgresql.conf）
config_file (string)

# 指定基于主机认证配置文件（通常叫pg_hba.conf）
hba_file (string)

# 指定用于用户名称映射的配置文件（通常叫pg_ident.conf）
ident_file (string)

# 指定可被服务器创建的用于管理程序的额外进程 ID（PID）文件
external_pid_file (string)
```

在默认安装中不会显式设置以上参数。相反，命令行参数 `-D` 或环境变量 `PGDATA` 必须指向包含配置文件的目录。

<br/>
<br/>

### 连接和认证

连接和认证的相关参数。

<br/>
<br/>

### 资源消耗

内存、磁盘、内核、清理、后台写入、异步等相关参数。

<br/>
<br/>

### 预写式日志

WAL、检查点、归档、归档恢复等相关参数。

<br/>
<br/>

### 复制

复制、主服务器、备服务器、订阅者相关参数。

服务器将可以是主控服务器或后备服务器。主控机能发送数据，而后备机总是被复制数据的接收者。当使用级联复制时，后备服务器也可以是发送者，同时也是接收者。这些参数主要用于发送服务器和后备服务器，尽管某些只在主服务器上有意义。如果有必要，设置可以在集群中变化而不出问题。

<br/>
<br/>

### 查询规划

这些配置参数提供了影响查询优化器选择查询规划的原始方法。

<br/>
<br/>

### 错误报告和日志

日志输出相关参数。

<br/>
<br/>

### 运行时统计数据

查询、索引、监控等相关参数。

<br/>
<br/>

### 自动清理

自动清理相关参数。

<br/>
<br/>

### 客户端默认值

客户管连接的相关参数。

<br/>
<br/>

### 锁管理

锁相关参数。

<br/>
<br/>

### 版本和平台兼容性

版本兼容性的相关参数。

<br/>
<br/>

### 错误处理

错误处理相关参数。

<br/>
<br/>

### 预置选项

这些参数是只读的，并被排除在 `postgresql.conf` 文件例子之外。所有这些都是在 PG 被编译或者它被安装时决定的。

<br/>
<br/>

### 自定义选项

这个特性被设计用来由附加模块向 PG 添加通常不为系统知道的参数（例如过程语言）。这允许使用标准方法配制扩展模块。

<br/>
<br/>

### 开发者选项

这些参数目的是用在开发测试上， 并且永远不能用于生产数据库。 但是，它们中的一些能够用于帮助恢复严重损坏的数据库。 同样，它们被从例子 `postgresql.conf` 文件中排除。 请注意许多这些参数要求特殊的源代码编译标志才能工作。

<br/>
<br/>

## 客户端认证

当一个客户端应用连接到数据库服务器时，它将指定以哪个 PG 数据库用户名连接，这决定了对数据库对象的访问权限。

PG 实际上以角色来进行权限管理。

PG 提供多种不同的客户端认证方式。被用来认证一个特定客户端连接的方法可以基于主机地址、数据库和用户来选择。

PG 数据库用户在逻辑上和操作系统中的用户时相互独立的。

<br/>
<br/>

### pg_hba文件

客户端认证由一个配置文件（通常是  `pg_hba.conf`）控制，HBA 表示基于主机的认证。

pg_hba 文件的常用格式是一组记录，每行一条。空白行将被忽略，注释符（`#`）后面的任何文本也被忽略。记录可以延续到下一行并以反斜线结束改行。

每条记录指定一种连接类型、一个客户端 IP 地址范围、一个数据库名、一个用户名以及对匹配这些参数的连接使用的认证方法。

记录可以由多种格式:

```conf
# TYPE  DATABASE        USER            ADDRESS                 METHOD
local         database  user  auth-method [auth-options]
host          database  user  address     auth-method  [auth-options]
hostssl       database  user  address     auth-method  [auth-options]
hostnossl     database  user  address     auth-method  [auth-options]
hostgssenc    database  user  address     auth-method  [auth-options]
hostnogssenc  database  user  address     auth-method  [auth-options]
host          database  user  IP-address  IP-mask      auth-method  [auth-options]
hostssl       database  user  IP-address  IP-mask      auth-method  [auth-options]
hostnossl     database  user  IP-address  IP-mask      auth-method  [auth-options]
hostgssenc    database  user  IP-address  IP-mask      auth-method  [auth-options]
hostnogssenc  database  user  IP-address  IP-mask      auth-method  [auth-options]
```

<br/>

类型

- local：这条记录匹配企图使用 Unix 域套接字的连接。 如果没有这种类型的记录，就不允许 Unix 域套接字连接。
- host：这条记录匹配企图使用 TCP/IP 建立的连接。 host记录匹配SSL和非SSL的连接尝试， 此外还有GSSAPI 加密的或non-GSSAPI 加密的连接尝试。
- hostssl：这条记录匹配企图使用 TCP/IP 建立的连接，但必须是使用SSL加密的连接。
- hostnossl：这条记录的行为与 hostssl 相反；它只匹配那些在 TCP/IP上不使用SSL的连接企图。
- hostgssenc：这条记录匹配企图使用TCP/IP建立的连接，但仅当使用GSSAPI加密建立连接时。
- hostnogssenc：这个记录类型具有与 hostgssenc 相反的表现; 它仅匹配通过不使用GSSAPI加密的TCP/IP进行的连接尝试。

<br/>

数据库，指定记录所匹配的数据库名称。

- 值 all 指定该记录匹配所有数据库。
- 值 replication 指定如果一个物理复制连接被请求则该记录匹配，不过，它不匹配逻辑复制连接。
- 可以通过用逗号分隔的方法指定多个数据库，也可以通过在文件名前面放 `@` 来指定一个包含数据库名的文件。

<br/>

用户，指定这条记录匹配哪些数据库用户名。

- 值 all 指定它匹配所有用户。
- 多个用户名可以通过用逗号分隔的方法提供。一个包含用户名的文件可以通过在文件名前面加上 `@` 来指定。

<br/>

地址，指定这个记录匹配的客户端机器地址。这个域可以包含一个主机名、一个 IP 地址范围或下文提到的特殊关键字之一。

- all 匹配任何 IP 地址。
- ip/mask CIDR 地址。
- 主机名会解析出特定的 IP 地址，主机名比较是大小写敏感的。
- 以点号（`.`）开始的主机名声明匹配实际主机名的后缀。
- 这个域不适用于 localhost 记录。

<br/>

认证方法，指定当一个连接匹配这个记录时，要使用的认证方法。

- trust：无条件地允许连接，不需要口令或者其他任何认证。
- reject：无条件地拒绝连接。
- scram-sha-256：执行 SCRAM-SHA-256 认证来验证用户的口令。
- md5：执行 SCRAM-SHA-256 或 MD5 认证来验证用户的口令。
- password：要求客户端提供一个未加密的口令进行认证。因为口令是以明文形式在网络上发送的，所以我们不应该在不可信的网络上使用这种方式。
- gss：用 GSSAPI 认证用户。只对 TCP/IP 连接可用。
- sspi：用 SSPI 来认证用户。只在 Windows 上可用。
- ident：通过联系客户端的 ident 服务器获取客户端的操作系统名，并且检查它是否匹配被请求的数据库用户名。Ident 认证只能在 TCIP/IP 连接上使用。当为本地连接指定这种认证方式时，将用 peer 认证来替代。
- peer：从操作系统获得客户端的操作系统用户，并且检查它是否匹配被请求的数据库用户名。这只对本地连接可用。
- ldap：使用LDAP服务器认证。
- radius：用 RADIUS 服务器认证。
- cert：使用 SSL 客户端证书认证。
- pam：使用操作系统提供的可插入认证模块服务（PAM）认证。
- bsd：使用由操作系统提供的 BSD 认证服务进行认证。

<br/>

认证选项，在认证方法域的后米娜，可以是 `name=value` 的域，它们指定认证方法的选项。

<br/>
<br/>

### 用户名映射

当使用像 Ident 或者 GSSAPI 之类的外部认证系统时，发起连接的操作系统用户名可能不同于要被使用的数据库用户（角色）。在这种情况下，一个用户名映射可被用来把操作系统用户名映射到数据库用户。

要使用用户名映射，在 `pg_hba.conf` 的选项域指定 `map=map-name`。此选项支持所有接收外部用户名的认证方法。由于不同的连接可能需要不同的映射，在 `pg_hba.conf` 中的 `map-name` 参数中指定要被使用的映射名，用以指示哪个映射用于每个个体连接。

用户名映射定义在 ident 映射文件中，默认情况下它被命名为 `pg_ident.conf` 并被存储在集簇的数据目录中。

```conf
map-name system-username database-username
```

<br/>
<br/>

## PG的角色

PG 使用角色的概念管理数据库访问权限。一个角色可以被堪称一个数据库用户或用户组。角色可以拥有数据库对象，并且能够把哪些对象上的权限赋值给其他角色来控制谁能访问哪些对象。

<br/>
<br/>

### 数据库角色

系统预定义的角色总是 superuser 权限，这个角色被命名为 postgres。

```sql
CREATE ROLE name;
DROP ROLE name;
ALTER ROLE name SET xxx;

# 查看角色
SELECT rolname FROM pg_roles;
# 或
\du;
                                   List of roles
 Role name |                         Attributes                         | Member of
-----------+------------------------------------------------------------+-----------
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
```

SQL 命令的包装器：

```sh
createuser name
dropuser name
```

<br/>
<br/>

### 角色属性

数据库角色的属性：

- LOGIN：数据库连接的权限。
- SUPERUSER：超级用户会绕开所有权限检查，除了登入的权利。
- CREATEDB：创建数据库的权限。
- CREATEROLE：创建、修改和删除角色的权限。
- REPLICATION：复制流的权限。一个被用于流复制的角色必须也具有 LOGIN 权限。
- PASSWORD：只有当客户端认证方法要求用户在连接数据库时提供一个口令时，一个口令才有意义。password 和 md5 认证方法使用口令。

```sql
CREATE ROLE name [role_attr...];
```

<br/>
<br/>

### 角色成员关系

把用户分组在一起来便于管理权限常常很方便，权限可以被授予一整个组或从一整个组回收。

```sql
CREATE ROLE name;

# 给组角色增加或移除成员
GRANT group_role TO role1, ... ;
REVOKE group_role FROM role1, ... ;
```

可以为其它组角色授予成员关系（因为组角色和非组角色之间没有任何区别）。

组角色的成员可以以两种方式使用角色的权限。第一，一个组的每一个成员可以显式地做 `SET ROLE` 来临时成为组角色。第二，有 `INHERIT` 属性的成员角色自动地具有它们所属角色的权限，包括任何组角色继承得到的权限。

```sql
CREATE ROLE joe LOGIN INHERIT;
CREATE ROLE admin NOINHERIT;
CREATE ROLE wheel NOINHERIT;
GRANT admin TO joe;
GRANT wheel TO admin;

SET ROLE admin;
```

<br/>
<br/>

### 删除角色

由于角色可以拥有数据库对象并且能持有访问其他对象的特权，删除一个角色常常并非一次 `DROP ROLE` 就能解决。 任何被该用户所拥有的对象必须首先被删除或者转移给其他拥有者，并且任何已被授予给该角色的权限必须被收回。

<br/>
<br/>

### 预定义角色

PG 提供了一组预定义角色。

| 角色 | 允许的访问 |
| - | - |
| pg_read_all_data | 读所有数据 |
| pg_write_all_data | 写全部数据 |
| pg_read_all_settings | 读取所有配置变量 |
| pg_read_all_stats | 读取所有的 `pg_stat_*` 视图并且使用与扩展相关的各种统计信息 |
| pg_stat_scan_tables | 执行可能会在表上取得 `ACCESS SHARE` 锁的监控函数（可能会持锁很长时间） |
| pg_monitor | 读取/执行各种不同的监控视图和函数 |
| pg_database_owner | 当前数据库的所有者 |
| pg_signal_backend | 发信号到其他后端以取消查询或中止它的会话 |
| pg_read_server_files | 允许使用 `COPY` 以及其他文件访问函数从服务器上该数据库可访问的任意位置读取文件 |
| pg_write_server_files | 允许使用 `COPY` 以及其他文件访问函数在服务器上该数据库可访问的任意位置中写入文件 |
| pg_execute_server_program | 允许用运行该数据库的用户执行数据库服务器上的程序来配合 `COPY` 和其他允许执行服务器端程序的函数 |

管理员可以这些预定义角色授予用户：

```sql
GRANT pg_signal_backend TO admin_user;
```

<br/>
<br/>

### 函数和触发器安全性

函数、触发器以及行级安全性策略允许用户在后端服务器中插入代码，其他用户不会注意到这些代码的执行。因此，这些机制允许用户相对容易地为其他人设置“特洛伊木马”。最强的保护是严格控制哪些人能定义对象。如果做不到，则编写查询时应该只引用具有可信任拥有者的对象。

<br/>
<br/>

## 管理数据库

在组织数据库对象的层次中，数据库位于最顶层。本章描述数据库的属性，以及如何创建、管理、删除它们。

<br/>
<br/>

### 数据库概述

集群内部有多个数据库，相互之间彼此隔离，但可以访问集群级对象。 每个数据库内部都有多个模式，它们包含表和函数等对象。因此，完整的层级结构为:集群、数据库、模式、表(或一些其他类型的对象，如函数)。

SQL 标准把数据库称为目录，不过实际上没有区别。

虽然可以在单个集群中创建多个数据库，但建议仔细考虑好处是否大于风险和限制。 特别是，共享 WAL 对备份和恢复选项的影响。从用户的角度来看，集群中的各个数据库是隔离的，但从数据库管理员的角度来看，它们是紧密绑定的。

```sql
# 列出数据库
\l
```

<br/>
<br/>

### 创建数据库

当前角色自动成为该新数据库的拥有者。以后删除这个数据库也是该拥有者的特权。

```sql
# 
CREATE DATABASE dbname;

# 为其他人创建一个数据库
CREATE DATABASE dbname OWNER rolename;
```

同等的包装命令，个人觉得包装命令不够显式，所以尽量少用。

```sh
# create 命令连接到 postgres 数据库并发出 CREATE DATABASE 命令
create dbname
```

<br/>
<br/>

### 模板数据库

`CREATE DATABASE` 实际上通过拷贝一个已有数据库进行工作。默认情况下，它拷贝名为 `template1` 的标准系统数据库，所以它是创建新数据库的模板。

如果你为 template1 数据库增加对象，这些对象将被拷贝到后续创建的用户数据库中。

系统里还有名为 `template0` 的第二个标准系统数据库，它包含和 template1初始内容一样的数据，也就是只包含 PG 版本预定义的标准对象。在数据库集簇被初始化之后，不应该对 template0 做任何修改。在创建库时指示使用 template0 模板，你可以创建一个原始的数据库，它不会包含 template1 中的站点本地附加物。这一点在恢复一个 `pg_dump` 转储时非常方便：转储脚本应该在一个原始的数据库中恢复以确保我们重建被转储数据库的正确内容，而不和任何现在可能已经被加入到 template1 中的附加物相冲突。

```sql
CREATE DATABASE dbname TEMPLATE template0;
```

<br/>
<br/>

### 数据库配置

通过 `SET` 命令修改特定数据库的配置。

```sql
ALTER DATABASE mydb SET geqo TO off;
```

<br/>
<br/>

### 销毁数据库

只有数据库的拥有者或者超级用户才可以删除数据库。删除数据库会移除其中包括的所有对象。数据库的删除不能被撤销。

```sql
DROP DATABASE name;
```

<br/>
<br/>

### 表空间

表空间允许数据库管理员在文件系统中定义用来存放表示数据库对象的文件的位置。一旦被创建，表空间就可以在创建数据库对象时通过名称引用。

通过使用表空间，可以控制 PG 安装的磁盘布局。这么做至少有两个用处。

- 首先，如果初始化集簇所在的分区或者卷用光了空间，而又不能在逻辑上扩展或者做别的什么操作，那么表空间可以被创建在一个不同的分区上，直到系统可以被重新配置。
- 其次，表空间允许管理员根据数据库对象的使用模式来优化性能。例如，一个很频繁使用的索引可以被放在 SSD 上；一个很少使用的或者对性能要求不高的存储归档数据的表可以存储在一个便宜但比较慢的磁盘系统上。

<br/>

> 警告：<br/>
> 即便是位于主要 PG 数据目录之外，表空间也是数据库集簇的一部分并且不能被视作数据文件的一个自治集合。它们依赖于包含在主数据目录中的元数据，并且因此不能被附加到一个 不同的数据库集簇或者单独备份。<br/>
> 如果丢失一个表空间（文件删除、磁盘失效等）， 数据库集簇可能会变成不可读或者无法启动。把一个表空间放在一个临时文件系统 （如一个内存虚拟盘）上会带来整个集簇的可靠性风险。

<br/>

定义一个表空间，必须是已存在的空目录，属于 PG 操作的用户。所有后续在该表空间中创建的对象都将被存放在这个目录下的文件中。该位置不能放在可移动 或者瞬时存储上，因为如果表空间丢失会导致集簇无法工作。

表、索引和整个数据库都可以被分配到特定的表空间。

```sql
CREATE TABLESPACE fastspace LOCATION '/ssd1/postgresql/data';

# 在表空间 space1 中创建一个表
CREATE TABLE foo(i int) TABLESPACE space1;

# 还可以使用 default_tablespace 参数来指定默认的表空间。
SET default_tablespace = space1;
CREATE TABLE foo(i int);

# 查看表空间
SELECT spcname FROM pg_tablespace;
# 或
\db
```

<br/>
<br/>

## 本地化

PG 支持两种本地化方法：

- 利用操作系统的区域（locale）特性。
- 提供一些不同的字符集来支持存储所有 种类语言的文本，并提供在客户端和服务器之间的字符集转换。

<br/>
<br/>

### 区域支持

PG 使用服务器操作系统提供的标准 ISO C 和 POSIX 的区域机制。

区域支持是在使用 `iniitdb` 创建数据库集簇时自动被初始化的。默认情况下，initdb 将会按照它的执行环境的区域设置初始刷数据库集簇。

```sh
# 设置其它区域
initdb --locale=en_US
```

<br/>

区域设置特别影响下面的 SQL 特性：

- 在文本数据上使用 `ORDER BY` 或标准比较操作符的查询中的排序顺序
- 函数 `upper`、`lower` 和 `initcap`
- 模式匹配操作符（`LIKE`、`SIMILAR TO` 和 POSIX 风格的正则表达式）；区域影响大小写不敏感匹配和通过字符类正则表达式的字符分类
- `to_char` 函数家族
- 为 `LIKE` 子句使用索引的能力

<br/>

检查系统中安装了哪些区域，使用 `locale -a` 命令。

PG 源码目录 `src/test/locale` 中包含了区域支持的测试套件。

<br/>
<br/>

### 排序规则支持

排序规则特性允许指定每一列甚至每一个操作的数据的排序顺序和字符分类行为。

在概念上，一种可排序数据类型的每一种表达式都有一个排序规则。如果该表达式是一个列引用，该表达式的排序规则就是列所定义的排序规则。如果该表达式是一个常量，排序规则就是该常量数据类型的默认排序规则。

<br/>
<br/>

### 字符集支持

一个重要的限制是每个数据库的字符集必须和数据库的 LC_CTYPE （字符分类）和 LC_COLLATE （字符串排序顺序）设置兼容。

`initdb` 为一个 PG 集簇定义缺省的字符集（编码）。

```sh
initdb -E/--encoding
```

或使用 SQL 来指定。

```sql
CREATE DATABASE korean WITH ENCODING 'EUC_KR' LC_COLLATE='ko_KR.euckr' LC_CTYPE='ko_KR.euckr' TEMPLATE=template0;
```

<br/>
<br/>

## 数据库维护

一个是定期创建数据库数据的后备拷贝。另一种是周期性地清理数据库。另一项是周期性的日志文件管理。

`check_postgres` 可用于检测数据库的健康并报告异常情况。

<br/>
<br/>

### 日常清理工作

<br/>
<br/>

#### 清理的基础知识

PG 的 VACUUM （垃圾收集并根据需要分析一个库）命令出于几个原因必须定期处理一个表：

- 恢复或重用已更新或已删除行所占用的磁盘空间。
- 更新被 PG 查询规划器使用的数据统计信息。
- 更新可见性映射，它可以加速只用索引的扫描。
- 保护老旧数据不会由于事务 ID 回卷或多事务 ID 回卷而丢失。

<br/>

有两种 VACUUM 的变体：

- 标准 VACUUM：标准形式可以和生产数据库操作并行允许（SELECT, INSERT, UPDATE 和 DELETE 等命令将继续正常工作，但在清理期间无法使用 ALTER TABLE 等命令来更新表的定义。）
- VACUUM FULL：可以收回更多磁盘空间，但运行起来更慢。它会要求在其工作的表上得到一个 ACCESS EXCLUSIVE 锁，因此无法和对此表的其他使用并行。

因此，通常管理员应该努力使用标准方式。VACUUM 会产生大量 IO 流，这将导致其他活动会话性能变差。

<br/>
<br/>

#### 恢复磁盘空间

在 PG 中，一次行的 UPDATE 或 DELETE 不会立即移除该行的旧版本。这种方法对于从多版本并发控制获益是必需的：当旧版本仍可能对其他事务可见时，它不能被删除。但是最后，任何事务都不会再对一个过时的/被删除的行版本感兴趣。它所占用的空间必须回收来用于新行，这样可避免磁盘空间需求的无限制增长。这通过运行 VACUUM 完成。

一些管理员更喜欢自己计划清理，例如在晚上负载低时做所有的工作。

如果你在一个集簇中有多个数据库，别忘记VACUUM每一个，你会用得上 vacuumdb 程序。

<br/>
<br/>

#### 更新规划器统计信息

PG 查询规划器依赖于有关表内容的统计信息来为查询产生好的计划。这些统计信息由 `ANALYZE` 命令收集。

可以在指定表上运行 ANALYZE 甚至在表的指定列上运行。因此如果你的应用需要，可以更加频繁地更新某些统计。但实际上，通常只分析整个数据库是最好的，因为它是一种很快的操作。ANALYZE 对一个表的行使用一种统计的随机采样，而不是读取每一个单一行。

<br/>
<br/>

#### 更新可见性映射

清理机制为每一个表维护着一个可见性映射，它被用来跟踪哪些页面只包含对所有活动事务可见的元组。

这样做有两个目的。第一，清理本身可以在下一次运行时跳过这样的页面，因为其中没有什么需要被清除。

第二，这允许 PG 回答一些只用索引的查询，而不需要引用底层表。

<br/>
<br/>

#### 防止事务ID回卷失败

PG 的 MVCC 事务语义依赖于能够比较事务 ID（XID） 数字：如果一个行版本的插入 XID 大于当前事务的 XID，它就是属于未来的并且不应该对当前事务可见。

因为事务 ID 的尺寸有限（32位），一个长时间（超过 40 亿个事务）运行的集簇会遭受到事务 ID 回卷问题：XID 计数器回卷到 0，并且本来属于过去的事务突然间就变成了属于未来 — 这意味着它们的输出变成不可见。简而言之，灾难性的数据丢失（实际上数据仍然在那里，但是如果你不能得到它也无济于事）。为了避免发生这种情况，有必要至少每 20 亿个事务就清理每个数据库中的每个表。

<br/>
<br/>

#### 自动清理后台进程

PG 有一个可选但是被高度推荐的特性 autovacuum，它的目的是自动执行 VACUUM 和 ANALYZE 命令。

当它被启用时，自动清理会检查被大量插入、更新或删除元组的表。这些检查会利用统计信息收集功能，因此除非 track_counts 被设置为 true，自动清理不能被使用。在默认配置下，自动清理是被启用的并且相关配置参数已被正确配置。

<br/>
<br/>

### 日常重建索引

在某种情况下值得周期性地使用 REINDEX 命令或一系列独立重构步骤来重建索引。

已经完全变成空的 B-Tree 索引页面被收回重用。但是，还是有一种低效的空间利用的可能性：如果一个页面上除少量索引键之外的全部键被删除，该页面仍然被分配。因此，在这种每个范围中大部分但不是全部键最终被删除的使用模式中，可以看到空间的使用是很差的。对于这样的使用模式，推荐使用定期重新索引。

对于非 B-Tree 索引可能的膨胀还没有很好地定量分析，因此定期监控索引的物理尺寸是个好主意。

对于 B 树索引，一个新建立的索引比更新了多次的索引访问起来要略快， 因为在新建立的索引上，逻辑上相邻的页面通常物理上也相邻（这样的考虑目前并不适用于非B树索引）。仅仅为了提高访问速度也值得定期重新索引。

REINDEX 在所有情况下都可以安全和容易地使用。 默认情况下，此命令需要一个 ACCESS EXCLUSIVE 锁，因此通常最好使用 CONCURRENTLY 选项执行它，该选项仅需要获取 SHARE UPDATE EXCLUSIVE 锁。

<br/>
<br/>

### 日志文件维护

PG 有一个内建的日志轮转程序，你可以通过在 `postgresql.conf` 里设置配置参数 `logging_collector` 为 true 的办法启用它。

或者你使用外部日志轮转程序。您可以通过设置 logrotate 来收集由 PG 内置日志收集器生成的日志文件来组合这些方法。这通常是通过 postrotate 脚本完成的，该脚本向应用程序发送SIGHUP 信号，使其重新打开日志文件。

另外一种生产级的管理日志输出的方法就是把它们发送给 syslog，让 syslog 处理文件轮转。不过，在很多系统上，syslog 不是非常可靠，特别是在面对大量日志消息的情况下。它可能在你最需要那些消息的时候截断或者丢弃它们。另外，在 Linux，syslog 会把每个消息刷写到磁盘上， 这将导致很差的性能。

<br/>
<br/>

## 备份和恢复

PG 数据库应当被定期地备份。虽然过程相当简单，但清晰地理解其底层技术和假设却很重要。

有三种不同的基本方法来备份 PG 数据：

- SQL 转储
- 文件系统级备份
- 连续归档

<br/>
<br/>

### SQL转储

创建一个由 SQL 命令组成的文件，服务器用其中的 SQL 命令重建数据库。

```sh
# -U --user, -h --host, -p --port, -d --dbname, -W --password, -f --file
# 默认使用当前用户进行连接
pg_dump dbname > dumpfile

pg_dump -U user -h host -p port -d dbname -W xxx -f dump.sql
```

<br/>
<br/>

#### 从转储中恢复

生成的文件可由 psql 程序读取。

```sh
# 此命令不会创建数据库，你必须先创建数据库
psql dbname < dumpfile

psql -U user -h host -p port -d dbname -W pwd -f dump.sql

# 从一个服务器转储一个数据库到另一个服务器
pg_dump -h host1 dbname | psql -h host2 dbname
```

<br/>

`pg_dump` 产生的转储是相对于 template0。这意味着在 template1 中加入的任何语言、过程等都会被 pg_dump 转储。结果是，如果在恢复时使用的是一个自定义的 template1，你必须从 template0 创建一个空的数据库。

```sh
createdb -T template0 dbname
```

<br/>
<br/>

#### pg_dumpall

pg_dump 每次只转储一个数据库，而且它不会转储关于角色或表空间（因为它们是集簇范围的）的信息。为了支持方便地转储一个数据库集簇的全部内容，提供了 `pg_dumpall` 程序。pg_dumpall 备份一个给定集簇中的每一个数据库，并且也保留了集簇范围的数据，如角色和表空间定义。

```sh
pg_dumpall > dumpfile

psql -f dumpfile postgres
```

<br/>
<br/>

#### 处理大型数据库

在一些具有最大文件尺寸限制的操作系统上创建大型的 pg_dump 输出文件可能会出现问题。幸运地是，pg_dump 可以写出到标准输出，因此你可以使用标准 Unix 工具来处理这种潜在的问题。

```sh
# gzip
pg_dump dbname | gzip > filename.gz
gunzip -c filename.gz | psql dbname

# split 拆分为多个文件
pg_dump dbname | split -b 2G - filename
cat filename* | psql dbname
```

<br/>
<br/>

### 文件系统级别备份

直接复制 PG 用于存储数据库中数据的文件。

```sh
tar -cf pg-backup.tar /usr/local/pgsql/data
```

此方法有两个限制，使得此方法比 pg_dump 差：

- 为了得到一个可用的备份，数据库服务器必须被关闭。
- 文件系统备份值适合于完整地备份或恢复整个数据库集簇。

<br/>

另一种文件系统备份方法是创建一个数据目录的一致快照。但是，以这种方式创建的备份保存的文件看起来就像数据库没有被正确关闭时的状态。因此，当你从备份数据上启动数据库服务器时，它会认为上一次的服务器实例崩溃了并尝试重放WAL日志。

<br/>

还有一种选择是使用rsync来执行一次文件系统备份。其做法是先在数据库服务器运行时执行rsync，然后关闭数据库服务器足够长时间来做一次 rsync --checksum （--checksum是必需的，因为rsync的文件修改 时间粒度只能精确到秒）。第二次 rsync 会比第一次快，因为它只需要传送相对很少的数据，由于服务器是停止的，所以最终结果将是一致的。这种方法允许在最小停机时间内执行一次文件系统备份。

<br/>

注意一个文件系统备份通常会比一个SQL转储体积更大（例如 pg_dump 不需要转储索引的内容，而是转储用于重建索引的命令）。但是，做一次文件系统备份可能更快。

<br/>
<br/>

### 连续归档和时间点恢复

在任何时间，PG 在数据集簇目录的 `pg_wal/` 目录下都有一个预写式日志（WAL）。这个日志存在的目的是为了保证崩溃后的安全：如果系统崩溃，可以重放从最后一次检查点以来的日志项来恢复数据库的一致性。

该日志的存在，使得第三种备份数据库的策略变得可能。我们可以把一个文件系统级别的备份和 WA L文件的备份结合起来。当需要恢复时，我们先恢复文件系统备份，然后从备份的 WAL 文件中重放来把系统带到一个当前状态。

<br/>
<br/>

#### 建立WAL归档

抽象地来说，一个运行中的 PG 系统产生一个无穷长的 WAL 记录序列。系统从物理上将这个序列划分成 WAL 段文件，通常是每个 16MB（段尺寸在 initdb 期间可修改）。段文件会被分配一个数字名称以便反映它在整个抽象 WAL 序列中的位置。

归档命令通常应该被设计成拒绝覆盖已经存在的归档文件。这是一个非常重要的安全特性， 可以在管理员操作失误（比如把两个不同的服务器的输出发送到同一个归档目录）的时候保持你的归档的完整性。

<br/>
<br/>

#### 制作一个基础备份

执行一次基础备份最简单的方法是使用 `pg_basebackup` 工具。

<br/>
<br/>

#### 使用一个连续归档备份进行恢复

过程：

1. 如果服务器仍在运行，停止它。
2. 如果你具有足够的空间，将整个集簇数据目录和表空间复制到一个临时位置。
3. 移除所有位于集簇数据目录和正在使用的表空间目录下的文件和子目录
4. 从你的文件系统备份中恢复数据库文件，注意权限。
5. 移除 `pg_wal` 中的任何文件。
6. 如果有保存的未归档的 WAL 段文件，把它们拷贝到 pg_wal 目录下。
7. 设定 `postgresql.conf` 中的恢复配置设置，并且在集簇数据目录中创建一个 `recovery.signal` 文件。
8. 启动服务器。服务器将会进入恢复模式并且进而根据需要读取归档 WAL 文件。服务器将删除 `recovery.signal`（为了阻止以后意外地重新进入恢复模式），并且开始正常数据库操作。
9. 检查数据库的内容来确保你已经恢复到了期望的状态。

<br/>
<br/>

#### 时间线

将数据库回复到一个之前的时间点带来了一些复杂性。假如你在周二晚是丢弃了一个关键表，但到周三中午才意识到错误。你需要将完成时间点恢复后生成的WAL记录序列与初始数据库历史中产生的WAL记录序列区分开来。

要解决这个问题，PG 有一个时间线概念。无论何时当一次归档恢复完成，一个新的时间线被创建来标识恢复之后生成的 WAL 记录序列。时间线 ID 号是 WAL 段文件名的一部分，因此一个新的时间线不会重写由之前的时间线生成的 WAL 数据。而有了时间线，你可以恢复到任何之前的状态，包括早先被你放弃的时间线分支中的状态。

<br/>
<br/>

#### 建议和例子

单机热备份。

<br/>

压缩的归档日志。

<br/>

`archive_command` 脚本。

<br/>
<br/>

## 高可用与负载均衡和复制

<br/>
<br/>

### 不同方案的比较

高可用、负载均衡和复制特性：

- 共享磁盘故障转移
- 文件系统（块设备）复制
- 预写式日志（WAL）传送
- 逻辑复制
- 基于触发器的主备复制
- 基于 SQL 的复制中间件
- 异步多主控机复制
- 同步多主控机复制

<br/>

有一些方案不适合上述的类别：

- 数据分区
- 多服务器并行查询执行

<br/>
<br/>

### 日志传送后备服务器

连续归档可用来创建一个高可用性（HA）集群配置，其中有一个或多个后备服务器随时准备在主服务器失效时接管操作。这种能力被广泛地称为温备或日志传送。

主服务器在连续归档模式下操作，而每一个后备服务器在连续恢复模式下操作并且持续从主服务器读取 WAL 文件。

直接从一个数据库服务器移动 WAL 记录到另一台服务器通常被称为日志传送。基于记录的日志传送具有更细的粒度并且能够在网络连接上以流的方式增量传递 WAL 的改变。

日志传送是异步的，即 WAL 记录是在事务提交后才被传送。在一个窗口期内如果主服务器发生灾难性的失效则会导致数据丢失，还没有被传送的事务将会被丢失。基于文件的日志传送中这个数据丢失窗口的尺寸可以通过使用参数 archive_timeout 进行限制

这种配置的恢复性能是足够好的，后备服务器在被激活后通常只有片刻就可以到达完全可用。因此，这被称为一种提供高可用性的温备配置。一台后备服务器也可以被用于只读查询，在这种情况下它被称为一台热备服务器。

<br/>
<br/>

#### 规划主备服务器

创建主服务器和备服务器是明智的，因此它们应该尽可能相似。

<br/>
<br/>

#### 后备服务器操作

在后备模式中，服务器持续地应用从主控服务器接收到的 WAL。

<br/>
<br/>

#### 准备主服务器

在主服务器上设置连续归档到一个后备服务器可访问的归档目录。即使主服务器垮掉该归档位置也应当是后备服务器可访问的，即它应当位于后备服务器本身或者另一个可信赖的服务器，而不是位于主控服务器上。

如果使用流复制，需要在主上配置认证来允许来自备的复制连接。即创建一个角色并且在 `pg_hba.conf` 中提供一个或多个数据库域被设置为 `replication` 的项。还要保证在主服务器的配置文件中 `max_wal_senders` 被设置为足够大的值。如果要使用复制槽，请确保 `max_replication_slots` 也被设置得足够高。

<br/>
<br/>

#### 建立一个后备服务器

要建立备，恢复从主取得的基础备份。在备的集簇数据目录中创建一个文件 `standby.signal`。将 `restore_command` 设置为一个从 WAL 归档中复制文件的简单命令。

可以有任意数量的备，但是如果你使用流复制，确保你在主服务器上将 `max_wal_senders` 设置得足够高，这样可以允许它们能同时连接。

<br/>
<br/>

#### 流复制

流复制允许一台备比使用基于文件的日志传送更能保持为最新的状态。备连接到主，主则在 WAL 记录产生时将它们以流式传送给备而不必等到 WAL 文件被填充。

默认情况下流复制是异步的，主上提交一个事务与该变化在备上可见之间存在短暂的延迟。不过这种延迟比基于文件的日志传送方式中要小得多。

当后备服务器被启动并且 `primary_conninfo` 被正确设置，后备服务器将在重放完归档中所有可用的 WAL 文件之后连接到主服务器。 如果连接被成功建立，你将在后备服务器中看到一个 walreceiver，并且在主服务器中有一个相应的 walsender 进程。

<br/>

设置好用于复制的访问权限非常重要，这样只有受信任的用户可以读取 WAL 流。备必须作为一个具有 REPLICATION 特权的账户或一个超级用户来向主认证。推荐为复制创建一个专用的具有 REPLICATION 和 LOGIN 特权的用户账户。

虽然 REPLICATION 特权给出了非常高的权限，但它不允许用户修改主系统上的任何数据，而 SUPERUSER 特权则可以。

<br/>

一个 `pg_hba.conf` 中关于复制流的示例内容：

```cnof
# 允许来自 192.168.1.100 的用户 "foo" 在提供了正确的口令时作为一个复制后备机连接到主控机。
#
# TYPE  DATABASE        USER            ADDRESS                 METHOD
host    replication     foo             192.168.1.100/32        md5
```

<br/>

主的主机名、端口号、用户名和口令在 `primary_conninfo` 中指定。在备上还可以在 `~/.pgpass` 文件中设置口令。

一个备的 `postgresql.conf` 文件示例：

```conf
# 后备机要连接到的主控机运行在主机 192.168.1.50 上，
# 端口号是 5432，连接所用的用户名是 "foo"，口令是 "foopass"。
primary_conninfo = 'host=192.168.1.50 port=5432 user=foo password=foopass'
```

<br/>

流复制的一个重要健康指标是主上产生但还没在备上应用的 WAL 记录数，通过通过比较主上 WAL 写位置（`pg_current_wal_lsn`）和备上收到的最后一个 WAL 位置（`pg_last_wal_receive_lsn`）来计算这个滞后量。后备服务器的最后 WAL 接收位置也被显示在 WAL 接收者进程的进程状态中，即使用 ps 命令显示的状态。

<br/>
<br/>

#### 复制槽

复制槽提供了一种自动化的方法来确保主控机在所有的后备机收到 WAL 段之前不会移除它们，并且主控机也不会移除可能导致恢复冲突的行，即时后备机断开也是如此。

<br/>
<br/>

#### 级联复制

级联复制特性允许一台后备服务器接收复制连接并且把 WAL 记录流式传送给其他后备服务器，就像一个转发器一样。这可以被用来减小对于主控机的直接连接数并且使得站点间的带宽开销最小化。

<br/>
<br/>

#### 同步复制

PG 流复制默认是异步的。如果主崩溃，则某些已被提交的事务可能还没有被复制到后备服务器，这会导致数据丢失。数据的丢失与故障转移时的复制延迟成比例。

同步复制能够保证一个事务的所有修改都能被传送到一台或者多台同步后备服务器。

在请求同步复制时，一个写事务的每次提交将一直等待，直到收到一个确认表明该提交在主服务器和后备服务器上都已经被写入到磁盘上的预写式日志中。

<br/>
<br/>

#### 在后备机上连续归档

在一个备上使用连续归档时，有两种不同的情景：WAL 归档在主和备之间共享，或备有自己的 WAL 归档。

<br/>
<br/>

### 故障转移

如果主失效，则备应该开始故障转移过程。

<br/>
<br/>

### 热备

术语热备用来描述处于归档恢复或后台模式中的服务器连接到服务器并运行只读查询的能力。这有助于复制目的以及高精度恢复一个备份到一个期望的状态。

术语热备也指服务器从恢复转移到正常操作而用户能继续运行查询并保持其连接打开的能力。

<br/>
<br/>

## 监控数据库活动

搞清楚系统则正在做什么。

<br/>

可通过 ps 命令查看进程：

```sh
ps auxww | grep ^postgres
postgres  15551  0.0  0.1  57536  7132 pts/0    S    18:02   0:00 postgres -i
postgres  15554  0.0  0.0  57536  1184 ?        Ss   18:02   0:00 postgres: background writer
postgres  15555  0.0  0.0  57536   916 ?        Ss   18:02   0:00 postgres: checkpointer
postgres  15556  0.0  0.0  57536   916 ?        Ss   18:02   0:00 postgres: walwriter
postgres  15557  0.0  0.0  58504  2244 ?        Ss   18:02   0:00 postgres: autovacuum launcher
postgres  15558  0.0  0.0  17512  1068 ?        Ss   18:02   0:00 postgres: stats collector
postgres  15582  0.0  0.0  58772  3080 ?        Ss   18:04   0:00 postgres: joe runbug 127.0.0.1 idle
postgres  15606  0.0  0.0  58772  3052 ?        Ss   18:07   0:00 postgres: tgl regression [local] SELECT waiting
postgres  15610  0.0  0.0  58772  3056 ?        Ss   18:07   0:00 postgres: tgl regression [local] idle in transaction
```

<br/>
<br/>

### 统计收集器

PG 的统计收集器是一个支持收集和报告服务器活动信息的子系统。

<br/>
<br/>

#### 统计收集配置

因为统计收集给查询执行增加了一些负荷，系统可以被配置为收集或不收集信息。在配置文件中配置。

统计收集器通过临时文件将收集到的信息传送给其他 PG 进程。

<br/>
<br/>

#### 查看统计信息

<br/>
<br/>

### 查看锁

另一个有用的工具是 `pg_locks` 系统表。可查看在锁管理器里面未解决的锁的信息。此功能可用于：

- 查看当前所有未解决的锁、在一个特定数据库中的关系上所有的锁、在一个特定关系上所有的锁，或由一个特定 PG 会话持有的所有的锁。
- 判断当前数据库中带有最多未授予锁的关系。
- 判断锁定竞争给数据库总体性能带来的影响，以及锁竞争随着整个数据库流量的变化范围。

<br/>
<br/>

### 过程上报

PG 具有在命令执行过程中报告某些命令进度的能力。目前，支持进度报告的命令有 `ANALYZE`, `CLUSTER`, `CREATE INDEX`, `VACUUM`, `COPY` 和 `BASE_BACKUP`。

<br/>
<br/>

### 动态追踪

PG 提供了功能来支持数据库服务器的动态追踪。这样就允许在代码中的特定点上调用外部工具来追踪执行过程。

一些探针或追踪点已被插入在源代码中。默认情况下，探针不被编译到 PG 中，用户需要显式地告诉配置脚本使探针可用。

<br/>
<br/>

## 监控磁盘使用

监控 PG 数据库系统的磁盘使用量。

<br/>
<br/>

### 判断磁盘使用量

每个表都有一个主要的堆磁盘文件，大多数数据都存储在其中。

如果一个表有很宽的列，则另外还有一个 TOAST 文件与这个表相关联，用于存储因为太宽而不能存储在主表里面的值。如果有这个附属文件，那么 TOAST 表上会有一个可用的索引。当然，同事还可能有索引和基表关联。每个表和索引都存放在单独的磁盘文件里——如果文件超过 1G，甚至可能多于一个文件。

有三种方式监视磁盘空间：使用 SQL 函数；使用 oid2name 模块；人工观察系统目录。

SQL 函数最容易，也是推荐的方法。

<br/>

查看任意表的磁盘用量：

```sql
SELECT pg_relation_filepath(oid), relpages FROM pg_class WHERE relname = 'customer';

 pg_relation_filepath | relpages
----------------------+----------
 base/16384/16806     |       60
(1 row)
```

每个页通常都是 8K 字节。如果相直接检查表的磁盘文件，那么文件路径名应该有用。

<br/>

要显示 TOAST 表使用的空间，可以使用一个类似的查询：

```sql
SELECT relname, relpages
FROM pg_class,
     (SELECT reltoastrelid
      FROM pg_class
      WHERE relname = 'customer') AS ss
WHERE oid = ss.reltoastrelid OR
      oid = (SELECT indexrelid
             FROM pg_index
             WHERE indrelid = ss.reltoastrelid)
ORDER BY relname;

       relname        | relpages
----------------------+----------
 pg_toast_16806       |        0
 pg_toast_16806_index |        1
```

<br/>

显示索引的大小：

```sql
SELECT c2.relname, c2.relpages
FROM pg_class c, pg_class c2, pg_index i
WHERE c.relname = 'customer' AND
      c.oid = i.indrelid AND
      c2.oid = i.indexrelid
ORDER BY c2.relname;

      relname      | relpages
-------------------+----------
 customer_id_index |       26
```

<br/>

用下面的信息找出最大的表和索引：

```sql
SELECT relname, relpages
FROM pg_class
ORDER BY relpages DESC;

       relname        | relpages
----------------------+----------
 bigtable             |     3290
 customer             |     3144
```

<br/>
<br/>

### 磁盘满失败

一个写满了的数据磁盘可能不会导致数据的崩溃，但它肯定会让系统变得不可用。如果保存 WAL 文件的磁盘变满，会发生数据库服务器致命错误并且可能发生关闭。

如果你不能通过删除一些其他的东西来释放一些磁盘空间，那么你可以通过使用表空间把一些数据库文件移动到其他文件系统上去。

<br/>
<br/>

## 可靠性和预写式日志

解释预写式日志如何用于获得有效的、可靠的操作。










