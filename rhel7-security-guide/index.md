# RHEL7-安全手册


参考:

- [RHEL7-安全手册](https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/7/html/security_guide/index)

<br/>

---

<!--more-->

<br/>

# 安全主题概述

许多组织以及个人都认为安全性是自带的，被人忽略的是为了提高功能、生产率、便利性、易用性等问题。通常会在发生了未经授权的入侵后，进行适当的安全实施。在将站点连接到不可信网络之前，采取正确的措施是抵御入侵尝试的有效方法。

<br/>
<br/>

## 什么是计算机安全

计算机安全性是一个涵盖计算和信息处理范围的一般术语。依靠计算机系统和网络进行日常业务交易和访问重要信息的行业将数据视为其整体资产的重要组成部分。

<br/>
<br/>

### 标准化安全性

每个行业的企业依赖制定标准机构设定的法规和规则。对于信息安全性，同样也是一样的理想选择。许多安全顾问和供应商都同意了称为 CIA 或机密性、完整性和可用性的标准安全模型。这种三层模式是普遍认可的组件，用于评估敏感信息的风险和建立安全策略。

CIA 模型：

- **机密性**，敏感信息必须只对一组预定义的个人可用。应限制未经授权的信息传输和使用。
- **完整性**，不应以导致信息不完整或不正确的方式更改信息。未授权用户应受限于修改或销毁敏感信息的能力。
- **可用性**，授权用户可随时根据需要访问信息。可用性是一种保证，即可以按照商定的频率和及时性获得信息。这通常以百分比来衡量，并同意在网络服务提供商及其企业客户的服务级别协议(SLA)中正式制定。

<br/>
<br/>

### 机密软件和认证

红帽支持了安全模块和智能卡等。

<br/>
<br/>

## 安全控制

计算机安全性通常分为三种不通的 master 类别，通常称为控制：

- 物理
- 技术
- 管理

这三大类别定义了适当安全实施的主要目标。

<br/>
<br/>

### 物理控制

物理控制是在定义的结构中实施安全措施，用于阻止或阻止对敏感材料进行未经授权的访问。

- 闭路监控
- 安全保护
- 门禁系统
- 照片 ID
- 生物统计学

<br/>
<br/>

### 技术控制

技术控制使用技术作为控制物理结构和网络上敏感数据访问和使用的基础。

- 加密
- 智能卡
- 网络验证
- 访问控制
- 文件完整性校验

<br/>
<br/>

### 管理控制

管理控制确定了安全的人为因素。

- 培训并认知
- 灾备和恢复
- 人员与隔离
- 人员注册和核算

<br/>
<br/>

## 漏洞评估

根据时间、资源和动机，攻击者几乎可以进入任何系统。当前提供的所有安全程序和技术都无法保证所有系统完全不受入侵。

漏洞评估是对您的网络和系统安全性的内部审计；其结果表明网络的机密性、完整性和可用性。通常，漏洞评估从分析阶段开始，在该阶段收集有关目标系统和资源的重要数据。此阶段会导致系统就绪阶段，将基本检查所有已知的漏洞目标。报告阶段结束，调查结果分为高、中度和低风险类别；并讨论提高目标安全（或降低漏洞风险）的方法。

<br/>
<br/>

### 定义评估和测试

漏洞评估可分为两种类型：

- **外部评估**，试图从外部破坏你的系统。
- **内部评估**，因为您是内部的，而且您的状态已提升为可信。你会有比外部更多的特权。

漏洞评估和渗透测试之间有区别。可将评估作为入透测试的第一步。从评估中获得的信息用于测试。评估会检查漏洞和潜在漏洞，而渗透测试实际上会尝试利用发现。

<br/>

> **警告**<br/>
> 不要尝试在生产环境中利用漏洞。这样做会对您的系统和网络的生产率效率造成负面影响。

<br/>
<br/>

### 建立漏洞评估方法

为了协助选择用于漏洞评估的工具，建立漏洞评估方法很有帮助。

要了解更多有关建立方法的信息，请参考 [开放 Web 应用程序安全项目](https://owasp.org/)。

<br/>
<br/>

### 漏洞评估工具

<br/>

#### Nmap

nmap 是一个流行工具，可用于确定网络布局。管理员可以使用网络上的 Nmap 来查找主机系统和这些系统上的开放端口。

```bash
# 安装
yum install nmap

# 使用
 nmap <hostname>
```

<br/>
<br/>

#### Nessus

[Nessus](http://www.nessus.org/) 是一款全面服务的安全扫描器。这款软件需要订阅。

<br/>
<br/>

#### openVAS

[OpenVAS](http://www.openvas.org/)（开放式漏洞评估系统 ）是一组可用于扫描漏洞和全面的漏洞管理的工具和服务。这款软件不需要任何订阅。

<br/>
<br/>

#### Nikto

[Nikto](http://cirt.net/nikto2) 是卓越的通用网关接口( CGI)脚本扫描程序。

<br/>
<br/>

## 安全错误

<br/>

### 网络安全隐患

配置网络的以下方面的错误做法可能会增加攻击风险：

- 不安全的架构
- 广播网络
- 集中式服务器

<br/>
<br/>

### 服务器安全隐患

服务器安全性同样很重要。

一些主要问题：

- 未使用的服务和开放端口
- 未修补的服务
- 不小心管理
- 本质上不安全的服务

<br/>
<br/>

### 工作站和个人电脑的安全威胁

工作站和家用电脑可能不会受到网络或服务器的攻击，而是因为它们通常包含敏感数据，如信用卡信息，它们都是系统攻击者的目标。

- 错误密码
- 存在安全漏洞的客户端应用程序

<br/>
<br/>

## 常见的入侵和攻击

常见的入侵和攻击：

- null 或默认密码：将管理密码留空，或使用产品供应商设置的默认密码。使用默认密码就能登录。
- 默认共享密钥：有时，安全服务会打包用于开发或评估测试目的的默认安全密钥。如果这些密钥保持不变，并放置在互联网上的生产环境中，则具有相同默认密钥的所有用户都可以访问该共享密钥资源及其包含的任何敏感信息。
- IP Spoofing：远程计算机充当本地网络上的节点，找到您的服务器的漏洞，并安装一个后门程序或 Trojan horse 来控制您的网络资源。
- Seavesdropping：通过窃听两个节点之间的连接，在网络上的两个活跃节点之间传递数据。
- 服务漏洞：攻击者发现在互联网上运行的服务有缺陷或漏洞；通过此漏洞，攻击者破坏整个系统以及可能保存的任何数据，并可能破坏网络中的其他系统。
- 应用程序漏洞：攻击者在桌面和工作站应用程序中发现故障，执行任意代码，为将来的入侵或崩溃系统而模仿 Trojan 马车。
- DoS 攻击：攻击者或攻击者组通过向目标主机（服务器、路由器或工作站）发送未经授权的数据包，针对组织的网络或服务器资源进行协调。这会强制资源对合法用户不可用。

<br/>

---

<br/>

# 安装的安全提示

<br/>
<br/>

## 保护BIOS

对 BIOS 和启动加载程序的密码保护可防止对系统具有物理访问权限的未授权用户使用可移动介质启动，或通过单用户模式获得 root 权限。

密码保护计算机 BIOS的两个主要原因：

- 防止更改 BIOS 设置
- 防止系统引导

<br/>
<br/>

## 分区磁盘

红帽建议为以下目录单独分区：

- `/boot`：这个分区是系统在启动过程中读取的第一个分区，用于将系统引导至 RHEL7 的引导装载程序和内核映像存储在这个分区中。此分区不应加密。如果此分区包含在根分区中，并且该分区已加密或者不可用，那么您的系统将无法引导。
- `/home`：当用户目录在根分区中时，分区可能会填满，从而导致操作系统不稳定。
- `/tmp` 和 `/var/tmp`：目录都用来存储不需要长期存储的数据。但是，如果大量数据填充了其中一个目录，则它可以消耗您的所有存储空间。如果发生这种情况，且这些目录存储在根分区中，则您的系统可能会变得不稳定并崩溃。
- 其他数据分区

<br/>
<br/>

## 安装所需的最小软件包量

最好仅安装您将使用的软件包，因为计算机上的每一款软件可能包含漏洞。

<br/>
<br/>

## 安装过程中限制网络连接

安装 RHEL 时，安装介质代表系统在特定时间的快照。因此，它可能不是最新的安全修复程序，而且可能会受到仅在安装介质提供的系统发布后解决的某些问题的影响。

安装有潜在漏洞的操作系统时，始终只限制对最接近的必要网络区域的影响。

<br/>
<br/>

## 安装后流程

安装 RHEL 之后执行的安全相关步骤：

```bash
# 更新系统
yum update

# 防火墙
# 我一般是在网络层做防火墙策略，宿主机关闭防火墙

# 要提高安全性，禁用您不需要的服务
# 如果您的计算机上没有安装打印机，使用以下命令禁用 cups 服务
systemctl disable cups

# 查看活跃的服务
systemctl list-units | grep service
```

<br/>
<br/>

# 保持系统正常运行

<br/>
<br/>

## 维护安装的软件

随着安全漏洞的发现，必须更新受影响的软件，以限制任何潜在的安全风险。

<br/>
<br/>

### 规划和配置安全更新

Yum 软件包管理器包含多个与安全相关的功能，可用于搜索、列出、显示和安装安全勘误表。这些功能还使得可以使用 Yum 进行仅安装安全更新。

```bash
# 检查您的系统可用的安全相关更新
yum check-update --security

# 仅安装与安全相关的更新
yum update --security

# 使用 updateinfo 子命令显示或操作存储库提供的有关可用更新的信息
```

<br/>
<br/>

### 更新和安装软件包

在系统上更新软件时，务必要从可信来源下载更新。

验证签名的软件包，安装签名的软件包。

<br/>
<br/>

### 应用由已安装更新引入的更改

下载并安装安全勘误和更新后，必须停止旧软件的使用，再开始使用新软件。

通常，重新启动系统是确保使用最新版本的软件包的最可靠方法；但是，此选项并非始终必需，系统管理员也不始终可用。

- 应用程序：用户空间应用是可由用户发起的任何程序。更新此类用户空间应用后，需要重启。
- 内核：内核是操作系统的核心组件。它管理对内存、处理器和外围设备的访问，并且调度所有任务。更新内核后，需要重启。
- KVM：更新 qemu-kvm 和 libvirt 软件包后，需要重启。
- 共享库：共享库是 glibc 等代码单元，供多个应用和服务使用。利用共享库的应用程序通常会在应用初始化时加载共享代码，因此需要重启。
- systemd 服务：服务是通常在启动过程中启动的持久服务器程序。由于这些程序通常在内存中保留，只要计算机正在运行，必须停止每个更新的 systemd 服务并在其软件包升级后重新启动。
- 其他软件

```bash
# 确定针对特定库运行的应用程序链接
lsof /lib64/libwrap.so.0

# 服务重启
systemctl restart service_name
```

<br/>
<br/>

## 使用红帽门户网站

[红帽客户门户网站](https://access.redhat.com/)是与红帽产品相关的官方信息的主要面向客户的资源。您可以使用它查找文档、管理订阅、下载产品和更新、打开支持案例以及了解安全更新。

- 查看安全公告
- 查看 CVE (通用漏洞和风险)
- 了解问题严重分级

<br/>
<br/>

# 使用工具和服务强化您的系统

<br/>
<br/>

## 桌面安全

本节论述了有关用户密码、会话和帐户锁定以及可移动介质安全处理的建议做法。

<br/>
<br/>

### 密码安全性

密码是用于验证用户身份的主要方法。这就是密码安全性对于保护用户、工作站和网络非常重要的原因。

出于安全考虑，安装程序将系统配置为使用安全哈希算法 512(SHA512 )和影子密码。强烈建议您不要更改这些设置。

如果在安装期间取消选择影子密码，则所有密码都作为单向哈希存储在全局可读的 `/etc/passwd` 文件中，这使得系统易受离线密码破解攻击。如果入侵者能够以普通用户身份访问计算机，他可以将 `/etc/passwd` 文件复制到自己的计算机上，并针对它运行任意数量的密码破解程序。如果文件中存在不安全的密码，则仅需等待密码破解程序发现密码。

影子密码通过将密码哈希存储在文件 `/etc/shadow` 中（仅可由 root 用户读取）来消除这种类型的攻击。

这强制潜在攻击者通过登录计算机上的网络服务（如 SSH 或 FTP）来远程尝试窃取密码。这种暴力攻击的速度非常慢，随着数百次登录尝试写入到系统文件，会出现明显的跟踪。当然，如果攻击者在密码较弱的系统上夜间开始攻击，破解者在进入并编辑日志文件以覆盖他的记录之前便获得了访问权限。

除了格式和存储注意事项之外，还需要考虑内容问题。为了防止其帐户遭到密码破解攻击，用户可以执行的一项最重要的事情就是创建强大的密码。

<br/>
<br/>

#### 创建强密码

在创建安全密码时，用户必须记住，很长的密码比短而复杂的密码强大。创建仅包含八个字符的密码并不理想，即使它包含数字、特殊字符和大写字母。

在信息理论中，熵是与随机变量相关的不确定性级别，以位数表示。熵值越大，密码安全性越高。

自我创建密码的另一种方法是使用密码生成器。Thepwmake 是用于生成随机密码的命令行工具，由全部四组字符组成，即大写、小写、数字和特殊字符。

```bash
# 如果您指定了无效的熵位数，pwmake 将使用默认位数。要创建 128 位的密码
pwmake 128
```

<br/>

虽然创建安全密码的方法有所不同，但请务必避免以下错误做法：

- 使用单个字典单词、外部语言中的词语、颠倒的词语或仅数字。
- 将少于 10 个字符用作密码或密码短语。
- 使用键盘布局中的一系列键。
- 写下您的密码。
- 使用密码中的个人信息，如生日、横线、成员姓名或宠物名称。
- 在多台计算机上使用相同的密语或密码。

<br/>
<br/>

#### 强制使用强密码

系统管理员可以通过两个基本选项来强制使用强密码：

- 可以为用户创建密码。
- 可以让用户在验证密码时创建自己的密码。

为用户创建密码可确保密码正确，但随着组织的扩展，它是一项艰巨的任务。它还会增加用户写密码的风险，从而公开密码。

因此，大多数系统管理员更喜欢让用户创建自己的密码，但会主动验证这些密码是否足够强大。在某些情况下，管理员可以强制用户通过密码有效期定期更改其密码。

<br/>

> 注意<br/>
> 在红帽企业 Linux 7 中，`pam_pwquality` PAM 模块取代 `pam_cracklib`，该模块在红帽企业 Linux 6 中用作密码质量检查的默认模块。它使用与 `pam_cracklib` 相同的后端。

当要求用户创建或更改密码时，他们可以使用 `passwd` 命令行实用程序（即 `PAM-a` 可插拔验证模块）并检查密码是否太短或易于破解。此检查由 `pam_pwquality.so` PAM 模块执行。

`pam_pwquality` 模块用于根据一组规则检查密码的强度。其过程由两个步骤组成：首先检查提供的密码是否在字典中找到。如果没有，它会继续执行几个附加检查。`pam_pwquality` 与 `/etc/pam.d/passwd` 文件 `的密码` 组件中的其他 PAM 模块一起堆叠，自定义规则集在 `/etc/security/pwquality.conf` 配置文件中指定。更详细的信息，可以查看 `pwquality.conf` 手册。

<br/>

> 注意<br/>
> 由于 root 用户是强制执行密码创建规则的用户，他们可以为自己或普通用户设置任何密码，尽管有警告消息。

配置密码强度检查 `inpwquality.conf`。

```txt
# 要使用 pam_quality，请在 /etc/pam.d/passwd 文件中，密码堆栈中添加以下行
password    required    pam_pwquality.so retry=3

# 检查的选项每行指定一个
# 例如密码长度，包含字符类型
minlen = 8
minclass = 4

# 要为字符序列和相同连续字符设置密码强度检查，请在 /etc/security/pwquality.conf 添加以下行
# 比如不能包含3个以上字符，如 abcd, 1111
maxsequence = 3
maxrepeat = 3
```

<br/>
<br/>

#### 配置密码期限

密码过期意味着，在指定期间（通常为 90 天）后，会提示用户创建新密码。其背后的理论是，如果强制用户定期更改其密码，破解的密码仅在有限时间内对入侵者有用。但是，密码过期的缺点是用户更有可能写下密码。

要在 RHEL7 中指定密码期限，请使用 `chage` 命令。

```bash
# -M 选项指定密码有效的最长天数
chage -M 90 username

# 禁用密码过期
chage -M -1 username

# 查看用户密码过期设置
chage -l username

# 将密码配置为在用户第一次登录时过期。这会强制用户立即更改密码。
 chage -d 0 username
```

<br/>
<br/>

### 账户锁定

在 RHEL7 中，`pam_faillock` PAM 模块允许系统管理员在指定次数的尝试失败后锁定用户帐户。限制用户登录尝试主要是一种安全措施，旨在防止可能针对获取用户帐户密码的暴力攻击。

使用 pam_faillock 模块时，失败的登录尝试会存储在 `/var/run/faillock` 目录中每个用户的单独文件中。

失败尝试日志文件中的行顺序非常重要。此顺序的任何更改都可锁定所有用户帐户，包括使用 `even_deny_root` 选项时 root 用户帐户。

<br/>

按照以下步骤设置账户锁定：

```bash
# 在 10 分钟后尝试三次并解锁该用户后锁定任何非 root 用户
# 请在 /etc/pam.d /system-auth 和 /etc/pam.d/password-auth 文件的 auth 部分添加两行。
# 下面的 2 和 4 行号
# 加上 even_deny_root 条件可以锁定 root 用户（deny=3 even_deny_root unlock_time=600）
auth        required      pam_env.so
auth        required      pam_faillock.so preauth silent audit deny=3 unlock_time=600
auth        sufficient    pam_unix.so nullok try_first_pass
auth        [default=die] pam_faillock.so authfail audit deny=3 unlock_time=600
auth        requisite     pam_succeed_if.so uid >= 1000 quiet_success
auth        required      pam_deny.so

# 在上一步中指定两个文件的 account 部分中添加以下行
account     required      pam_faillock.so

# 要防止系统在多次登录失败后锁定用户
# 请在首次在 /etc/pam.d/system-auth 和 /etc/pam.d/password-auth 中第一次调用 pam_faillock 的行上方添加以下行
# user1, user2, user3 替换为真实的用户名
auth [success=1 default=ignore] pam_succeed_if.so user in user1:user2:user3

# 查看每位用户失败的尝试次数
faillock

# 解锁用户帐户
 --user <username> --reset
```

<br/>

使用 authconfig 保留自定义设置。

使用 authconfig 实用程序修改身份验证配置时，`system-auth` 和 `password-auth` 文件会被 authconfig 实用程序的设置覆盖。这可以通过创建符号链接来代替配置文件（authconfig 可识别且不会覆盖这些文件）来避免这种情况。

<br/>
<br/>

### 会话锁定

由于日常操作期间的许多原因，用户可能需要无人值守的工作站。这为攻击者提供了物理访问计算机的机会。由于笔记本电脑的移动干扰物理安全性，因此这些笔记本电脑会特别暴露。您可以使用会话锁定功能来缓解这些风险，该功能阻止访问系统，直到输入了正确的密码。

锁定屏幕而非注销的主要优点在于锁定允许用户的进程（如文件传输）继续运行。注销将停止这些进程。

使用 vlock 锁定虚拟控制台：

```bash
yum install vlock

# 可以使用 vlock 命令锁定任何控制台会话，而无需任何附加参数。这会锁定当前活动的虚拟控制台会话，同时仍允许访问其他虚拟控制台。
# 要阻止访问工作站中的所有虚拟控制台，请执行以下操作。
# 在这种情况下，vlock 会锁定当前活动的控制台，而 -a 选项会阻止切换到其他虚拟控制台。
vlock -a
```

<br/>
<br/>

### 强制只手动挂载可移动介质

要强制以只读方式挂载可移动介质，管理员可以使用 udev 规则检测可移动介质并使用 blockdev 实用程序将其配置为只读挂载。这足以强制以只读方式挂载物理介质。

<br/>
<br/>

## 控制Root访问

在管理主计算机时，用户必须以 root 用户身份执行某些任务，或使用 setuid 程序（如 sudo 或 su ）获得有效的 root 权限。

setuid 程序是使用程序所有者而不是操作程序的用户的用户 ID(UID)运行的程序。此类程序由长格式列表的 owner 部分中的 s 表示。

```bash
ls -l /bin/su
-rwsr-xr-x. 1 root root 34904 Mar 10  2011 /bin/su

# s 可以是大写或小写。如果它显示为大写，则表示尚未设置底层权限位
```

但是，对于组织的系统管理员而言，必须选择组织内的管理访问用户应对其计算机进行多少管理访问。通过名为 pam_console.so 的 PAM 模块，对于在物理控制台中登录的第一个用户，通常仅为 root 用户保留的某些活动，如重新引导和挂载可移动介质等。

<br/>
<br/>

### 禁止 Root 访问

管理员可以进一步确保不允许 root 登录的四种不同方式：

- 更改 root shell
- 使用任何控制台设备(tty)禁用 root 访问权限
- 禁用 root ssh 登录
- 使用 PAM 限制对服务的 root 访问权限

<br/>
<br/>

#### 更改 root shell

为防止用户直接以 root 身份登录，系统管理员可以在 /etc/passwd 文件中将 root 帐户的 shell 设置为 `/sbin/nologin `。

禁用 root shell 之后，以下程序无法访问 root 账户：

- login
- gdm
- kdm
- xdm
- su
- ssh
- scp
- sftp

不需要 shell 的程序，以下程序没有阻止访问 root 账户：

- sudo
- FTP 客户端
- 电子邮件客户端

<br/>
<br/>

#### 控制台设备禁用 root 访问权限

管理员可以编辑 `/etc/securetty` 文件，在控制台上禁用 root 登录。此文件列出了 root 用户被允许登录的所有设备。如果文件完全不存在，root 用户可以通过系统上的任何通信设备（无论是通过控制台还是原始网络接口）登录。这很危险，因为用户可以使用 Telnet 以 root 用户身份登录其计算机，它通过网络以纯文本形式传输密码。

空白 `/etc/securetty` 文件不会阻止 root 用户使用 OpenSSH 工具套件远程登录，因为在验证后才会打开控制台。

<br/>
<br/>

#### 禁用 root ssh 登录

要防止 root 通过 SSH 协议登录，请编辑 SSH 守护进程的配置文件。

```bash
# /etc/ssh/sshd_config

#PermitRootLogin yes
PermitRootLogin no
```

影响使用 OpenSSH 工具套件进行 root 登录： ssh, scp, sftp。

<br/>
<br/>

#### 使用 PAM 限制对服务的 root 访问权限

PAM 通过 `/lib/security/pam_listfile.so` 模块，提供了极大的灵活性来拒绝特定帐户。管理员可以使用此模块引用不允许登录的用户列表。

若要限制对系统服务的 root 访问权限，请在 `/etc/pam.d/` 目录中编辑目标服务的 文件，并确保需要 `pam_listfile.so` 模块进行身份验证。

栗子 `/etc/pam.d/vsftpd` PAM 配置文件。这将指示 PAM 查阅 `/etc/vsftpd.ftpusers` 文件，并拒绝任何列出的用户对该服务的访问。

```conf
auth   required   /lib/security/pam_listfile.so   item=user sense=deny file=/etc/vsftpd.ftpusers onerr=succeed
```

<br/>
<br/>

### 允许 Root 访问

为单个用户授权 root 访问权限可能会导致一些问题。

<br/>
<br/>

### 限制 Root 访问

管理员可能希望仅通过 setuid 程序（如 su 或 sudo ）来允许对 root 用户的访问，而不是完全拒绝访问。

<br/>
<br/>

### 启用自动注销

当用户以 root 身份登录时，无人值守的登录会话可能会带来重大的安全风险。要降低此风险，您可以将系统配置为在固定的时间段后自动注销空闲用户。

```bash
# 在 /etc/profile 文件的开头添加以下行，以确保无法中断对该文件的处理
trap "" 1 2 3 15

# 将以下行插入到 /etc/profile 文件中，以在 120 秒后自动注销
export TMOUT=120
readonly TMOUT
```

<br/>
<br/>

### 保护引导装载程序

密码保护 Linux 引导装载程序：

- 防止访问单用户模式 
- 防止访问 GRUB 2 控制台
- 防止访问 Insecure 操作系统

<br/>

要防止用户以 root 身份以互动方式启动系统，请在 `/etc/sysconfig/init` 文件中禁用 PROMPT 参数：

```conf
PROMPT=no
```

<br/>
<br/>

### 保护硬链接和符号链接

为了防止恶意用户利用未受保护的硬链接和符号链接导致的潜在漏洞。

硬链接：

- 用户拥有他们链接到的文件。
- 用户已对其链接的文件具有读写访问权限。

符号链接：

- 符号链接后面的进程是符号链接的所有者
- 目录的所有者与符号链接的所有者相同。

<br/>
<br/>

## 保护服务

如果网络服务正在计算机上运行，则服务器应用程序（称为守护进程）正在侦听一个或多个网络端口上的连接。其中每个服务器都应被视为潜在的攻击途径。

<br/>
<br/>

### 服务风险

网络服务可能会给系统带来很多风险，为限制网络收到攻击的风险，应关闭所有未使用的服务。

- 拒绝服务攻击(DoS)
- 分布式拒绝服务攻击(DDoS)
- 脚本漏洞攻击
- 缓冲区溢出攻击

<br/>
<br/>

### 识别和配置服务

为增强安全性，随 RHEL7 一起安装的大多数网络服务都默认关闭。然而，有一些值得注意的例外：

- cpups: 打印服务
- cpus-ldp：替代打印服务器
- xinetd：控制到一系列从属服务器的连接的超级服务器
- sshd：OpenSSH 服务器，这是 Telnet 的安全替代品

例如，如果打印机不可用，则不要让 cups 保持运行。如果不挂载 NFS v3 卷，或使用 NIS (ypbind 服务)，则应该禁用 rpcbind。

<br/>
<br/>

### 不安全的服务

或许，任何网络服务都不安全。这就是为什么关闭未使用的服务非常重要。服务漏洞定期被发现和修补，这使得定期更新与任何网络服务相关的软件包非常重要。

某些网络协议或服务不够安全：

- 通过未加密的网络传输用户名和密码 - 许多较旧的协议（如 Telnet 和 FTP）不加密身份验证会话，应该尽可能避免。
- 通过网络未加密传输敏感数据 - 许多协议通过网络未加密传输数据。这些协议包括 Telnet、FTP、HTTP 和 SMTP。NFS 和 SMB 等许多网络文件系统也通过网络未加密传输信息。使用这些协议限制传输的数据类型时，用户的责任。
- 本质上不安全的服务示例包括 rlogin、rsh、telnet 和 vsftpd。

<br/>
<br/>

### 保护rpcbind

Therpcbind 服务是用于 RPC 服务（如 NIS 和 NFS）的动态端口分配守护进程。它具有较弱的身份验证机制，能够为其控制的服务分配广泛的端口。由于这些原因，很难保证。

Securingrpcbind 仅影响 NFSv2 和 NFSv3 实施，因为 NFSv4 不再需要它。

<br/>
<br/>

### 保护NIS

网络信息服务(NIS)是一种 RPC 服务，称为 ypserv ，它与 rpcbind 和其他相关服务一起使用，以向声称在其域中的任何计算机分发用户名、密码和其他敏感信息的映射。

根据当今的标准，NIS 稍微不安全。它没有主机身份验证机制，并且没有通过网络未加密传输其所有信息，包括密码哈希。因此，设置使用 NIS 的网络时必须特别小心。

<br/>
<br/>

### 保护NFS

NFS 流量可以在所有版本中使用 TCP 发送，应与 NFSv3 而不是 UDP 一起使用，在使用 NFSv4 时需要该通信。所有版本的 NFS 支持 Kerberos 用户和组身份验证，作为 RPCSEC_GSS 内核模块的一部分。

<br/>
<br/>

### 保护HTTP

<br/>
<br/>

#### 保护Apache

<br/>
<br/>

#### 保护Nginx

保护 Nginx 的一些 server 部分的配置：

```conf
# 禁用版本字符串
server_tokens        off;

# 由 NGINX 提供的每个请求都可以包含额外的 HTTP 标头，以缓解某些已知的 Web 应用程序漏洞
# add_header X-Frame-Options SAMEORIGIN; - 此选项拒绝域外的任何页面设置 NGINX 提供的任何内容，从而有效缓解了 clickjacking 攻击。
# add_header X-Content-Type-Options nosniff; - 这个选项可在某些较旧的浏览器中防止 MIME 类型嗅探。
# add_header X-XSS-Protection "1; mode=block"; - 此选项允许跨站点脚本(XSS)过滤，这样可防止浏览器呈现 NGINX 响应中含有的潜在恶意内容。

# 禁用 Potentially Harmful HTTP 方法
# 可以通过只将允许的方法列入白名单来禁止这些恶意 HTTP 方法以及任意方法。
# Allow GET, PUT, POST; return "405 Method Not Allowed" for all others.
if ( $request_method !~ ^(GET|PUT|POST)$ ) {
    return 405;
}

# 配置 SSL
```

<br/>
<br/>

### 保护FTP

文件传输协议(FTP )是较旧的 TCP 协议，旨在通过网络传输文件。由于与服务器的所有事务（包括用户身份验证）都未加密，因此它被视为不安全的协议，应仔细配置。

<br/>
<br/>

### 保护Postfix

Postfix 是一个邮件传输代理(MTA)，它使用简单邮件传输协议(SMTP)在其他 MTA 之间传递电子邮件并通过电子邮件客户端或发送代理。尽管很多 MTA 能够加密彼此之间的通信，但大多数 MTA 都不加密，因此通过任何公共网络发送电子邮件被视为本质上不安全的通信形式。Postfix 在 RHEL7 中将 Sendmail 替换为默认 MTA。

<br/>
<br/>

### 保护SSH

Secure Shell(SSH ) 是一个功能强大的网络协议，用于通过安全通道与其他系统通信。通过 SSH 传输是加密的，并防止拦截。

- 加密登录：也就是启用密钥登录，禁用密码登录
- 多种身份验证方法：这个配置 AuthenticationMethods
- 协议版本：建议使用 SSH-2 协议
- 密钥类型：ssh-keygen 命令默认生成 SSH-2 RSA密钥，也可以使用 ESA 或 ECDSA 密钥。
- 非默认端口：不使用 22 端口
- 禁用 root 登录
- 红帽建议在连接到不可信主机时不要使用 X11 转发。

<br/>
<br/>

### 保护PGSQL

<br/>
<br/>

### 保护Docker

可参考 RHEL7 容器安全：<https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html/container_security_guide/index>

<br/>
<br/>

## 保护网络访问

<br/>
<br/>

### 使用TCP wrapper和xinetd保护服务

TCP 封装器(wrapper) 的能力远远不止于拒绝对服务的访问。本节介绍如何使用它们发送连接横幅、来自特定主机的攻击警告，以及增强日志记录功能。

<br/>
<br/>

#### TCP封装器和连接程序

当用户连接到服务时，显示合适的横幅是让潜在攻击者知道系统管理员正在被滥用的好方法。您还可以控制向用户显示有关系统的信息。要为服务实施 TCP wrappers 横幅，请使用 banner 选项。

这个示例为 vsftpd 实施横幅。首先，创建一个横幅文件。它可以是系统上的任何位置，但名称必须与守护进程相同。在本例中，该文件名为 `/etc/banners/vsftpd`。

```conf
220-Hello, %c
220-All activity on ftp.example.com is logged.
220-Inappropriate use will result in your access privileges being removed.
```

`%c` 令牌提供各种客户端信息，如用户名和主机名，或者用户名和 IP 地址，以使连接更加令人生畏。

要使此横幅显示在传入的连接中，请在 /etc/hosts.allow 文件中添加以下行：

```txt
vsftpd : ALL : banners /etc/banners/
```

<br/>

#### TCP封装器和攻击警告

如果检测到特定主机或网络攻击服务器，则可以使用 TCP wrapper 警告管理员使用 generate 指令发送该主机或网络的后续攻击。

在本例中，假设来自 `a.b.c.d/32` 网络的攻击者已被检测到试图攻击服务器。将以下行放在 `/etc/hosts.deny` 文件中，以拒绝来自该网络的任何连接尝试，并将尝试记录到特殊文件：

```txt
ALL : a.b.c.d : spawn /bin/echo `date` %c %d >> /var/log/intruder_alert
```

`%d` 令牌提供攻击者试图访问的服务名称。

要允许连接并进行记录，请将 generate 指令放在 `/etc/hosts.allow` 文件中。

由于生成指令执行任何 shell 命令，因此最好创建一个特殊脚本来通知管理员，或者在特定客户端尝试连接服务器时执行命令链。

<br/>
<br/>

#### TCP封装器和增强的日志记录

如果某些类型的连接比其他类型的关注更大，那么可以使用 severity 选项提升该服务的日志级别。

在本例中，假设尝试连接到 FTP 服务器上的端口 23（ Telnet 端口）都是攻击者。若要表示这一点，请在日志文件中放置 emerg 标志，而不是默认的标志、Info 和拒绝连接。

要做到这一点，请在 `/etc/hosts.deny` 中添加以下行：

```txt
in.telnetd : ALL : severity emerg
```

这将使用默认的 authpriv 日志记录功能，但将优先级从 info 的默认值提升为 emerg，后者将日志消息直接发送到控制台。

<br/>
<br/>

### 验证正在侦听哪些端口

关闭未使用的端口非常重要，以避免可能受到的攻击。对于处于侦听状态的意外端口，您应该调查可能的入侵信号。

- 使用 netstat 进行开放端口扫描
- 使用 ss 进行开放端口扫描
- 使用 nmap 工具进行外部检查
- 使用 lsof 进行端口连接查看

<br/>
<br/>

### 禁用源路由

源路由是一种互联网协议机制，它允许 IP 数据包传输信息、地址列表，向路由器告知数据包必须采用的路径。还有一个在路由被遍历时记录跃点的选项。"路由记录"获取的跃点列表为目的地提供源的返回路径。这允许源（发送主机）以松散或严格方式指定路由，忽略部分或所有路由器的路由表。它允许用户重定向网络流量以满足恶意用途。因此，应禁用基于源的路由。

```bash
# 禁用源路由
/sbin/sysctl -w net.ipv4.conf.all.accept_source_route=0

# 如果可能，禁用数据包转发也应与上述操作结合使用（禁用转发可能会干扰虚拟化）
# 有 docker 等的转发，不要禁用包转发
# /sbin/sysctl -w net.ipv4.conf.all.forwarding=0
# /sbin/sysctl -w net.ipv6.conf.all.forwarding=0

# 这些命令禁用在所有接口上转发所有多播数据包
/sbin/sysctl -w net.ipv4.conf.all.mc_forwarding=0
/sbin/sysctl -w net.ipv6.conf.all.mc_forwarding=0

# 这些命令禁止在所有接口上接受所有 ICMP 重定向的数据包
/sbin/sysctl -w net.ipv4.conf.all.accept_redirects=0
/sbin/sysctl -w net.ipv6.conf.all.accept_redirects=0

# 这个命令禁止在所有接口上接受安全 ICMP 重定向的数据包
/sbin/sysctl -w net.ipv4.conf.all.secure_redirects=0
/sbin/sysctl -w net.ipv4.conf.all.send_redirects=0
```

要使这些设置在重新引导后保留，请修改 `/etc/sysctl.conf` 文件。

<br/>
<br/>

## 使用DNSSEC保护DNS流量

DNSSEC 是一组域名系统安全扩展 (DNSSEC)，使 DNS 客户端能够验证和检查来自 DNS 名称服务器的响应的完整性，以便验证其源并确认它们是否在传输中被篡改。

DNS 客户端无法确信，显示来自给定 DNS 名称服务器的回复是身份验证的，并且未被篡改。更重要的是，递归名称服务器无法确保从其他名称服务器获得的记录是真正的。DNS 协议没有为客户端提供一种机制，以确保其不会受到中间人攻击。引入了 DNSSEC，以解决在使用 DNS 解析域名时缺少身份验证和完整性检查的问题。它没有解决机密性问题。

<br/>
<br/>

## 使用Libreswan保护虚拟网络(VPN)

使用 Libreswan 应用程序支持的 IPsec 协议来配置虚拟专用网络( VPN)。

<br/>
<br/>

## 使用OpenSSL

OpenSSL 是一个为应用程序提供加密协议的库。penssl 命令行实用程序启用使用 shell 中的加密功能。它包括交互模式。

<br/>
<br/>

### 创建和管理加密密钥

使用 OpenSSL 时，公钥派生自对应的私钥。因此，一旦决定算法，第一步是生成私钥。

```bash
# 生成私钥
openssl genpkey -algorithm RSA -out privkey.pem

# 用 3 创建 2048 位 RSA 私钥
openssl genpkey -algorithm RSA -out privkey.pem -pkeyopt rsa_keygen_bits:2048 \ -pkeyopt rsa_keygen_pubexp:3

# 要使用 128 位 AES 和密码 hello 对私钥进行加密
openssl genpkey -algorithm RSA -out privkey.pem -aes-128-cbc -pass pass:hello
```

<br/>
<br/>

### 生成证书

要使用 OpenSSL 生成证书，需要有一个可用的私钥。

要让证书认证机构 (CA) 签署证书，必须生成证书并将其发送到 CA 进行签名。这称为证书签名请求。

证书创建过程中使用的默认设置包含在 `/etc/pki/tls/openssl.cnf` 文件中

```bash
# 创建证书签名请求
# 将创建一个名为 cert.csr 的 X.509 证书，以默认增强隐私的电子邮件 (PEM)格式编码
openssl req -new -key privkey.pem -out cert.csr

# 创建自签名证书
# 生成自签名证书，有效时间为 3650 天
openssl req -new -x509 -key privkey.pem -out selfcert.pem -days 3650

# 使用 Makefile 创建证书
# /etc/pki/tls/certs/Makefile，可用于使用 make 命令创建证书
# 查看帮助信息
make -f /etc/pki/tls/certs/Makefile usage
```

<br/>
<br/>

### 验证证书

由 CA 签名的证书被称为可信证书。因此，自签名证书是一个不受信任的证书。

```bash
# 验证 PEM 格式的多个单独的 X.509 证书
openssl verify cert1.pem cert2.pem

# 验证证书链
openssl verify -untrusted untrusted.pem -CAfile cacert.pem cert.pem
```

<br/>
<br/>

### 加密和解密文件

对于使用 OpenSSL 加密和解密文件，可以使用 pkeyutl 或enc-in 命令。

```bash
# 加密 plaintext 文件
openssl pkeyutl -in plaintext -out cyphertext -inkey privkey.pem

# 要为名为 plaintext 的数据文件签名
openssl pkeyutl -sign -in plaintext -out sigtext -inkey privkey.pem

# 要验证签名的数据文件并提取数据
openssl pkeyutl -verifyrecover -in sig -inkey key.pem
```

<br/>
<br/>

### 生成消息摘要

dgst 命令以十六进制格式生成提供的文件或文件的消息摘要。命令也可用于数字签名和验证。

```bash
# 消息摘要命令
# openssl dgst algorithm -out filename -sign private-key

# 使用 sha1 算法以默认 Hex 格式生成消息摘要
openssl dgst sha1 -out digest-file

# 以数字方式使用私钥以数字方式签署摘要
openssl dgst sha1 -out digest-file -sign privkey.pem
```

<br/>
<br/>

### 生成密码哈希

passwd 命令计算密码的哈希。

```bash
# 计算密码哈希
openssl passwd password
```

<br/>
<br/>

### 生成随机数据

使用 seed 文件生成包含随机数据的文件。

```bash
# 生成包含随机数据的文件
openssl rand -out rand-file -rand seed-file
```

<br/>
<br/>

### 对您的系统进行基准测试

测试给定算法的系统计算速度。

```bash
# 测试给定算法的系统计算速度
openssl speed algorithm
```

<br/>
<br/>

## 使用Stunnel

Stunnel 程序是客户端和服务器之间的加密打包程序。它侦听其 配置文件中指定的端口，使用客户端加密连接，并将数据转发到侦听其常用端口的原始守护进程。

<br/>
<br/>

## 加密

一些加密：

- 磁盘加密
  - 加密分区
  - 加密目录
  - 添加新密码到现有设备
  - 从现有设备中删除密码
  - 加密块设备

<br/>
<br/>

## 使用AIDE检查完整性

高级入侵检测环境(AIDE) 是一个实用程序，可在系统上创建文件数据库，然后使用该数据库来确保文件的完整性并检测系统入侵。

<br/>
<br/>

## 使用USBGuard

USBGuard 软件框架通过基于设备属性实施基本的白名单和黑名单功能，为防止入侵 USB 设备提供系统保护。为强制实施用户定义的策略，USBGuard 使用 Linux 内核 USB 设备授权功能。

<br/>
<br/>

## 强化TLS配置

TLS（传输层安全性）是用于保护网络通信的加密协议。

<br/>

---

<br/>

# 使用防火墙

防火墙是保护机器不受来自外部的不需要的流量的一种方式。它允许用户通过定义一组防火墙规则来控制主机上的传入网络流量。这些规则用于对进入的流量进行排序，并可以阻断或允许流量。

<br/>
<br/>

## firewalld入门

firewalld 是一个防火墙服务守护进程，通过 D-Bus 接口提供动态可定制的主机防火墙。

firewalld 和 iptables 之间的基本区别：

- iptables 服务将配置存储在 `/etc/sysconfig/iptables` 中。此文件默认不存在，因为 RHEL7 中默认安装了 firewalld。
- firewalld 将其存储在 `/usr/lib/firewalld` 和 `/etc/firewalld` 中的各种 XML 文件中。
- 使用 iptables 服务时，每次更改都意味着清除所有就规则并从配置文件中读取所有规则。
- 而 firewalld 不会重新创建所有规则，仅应用差异。因此可以在不丢失现有连接的情况下运行时更改配置。
- 两者都使用 iptables 工具与内核数据包过滤器进行通信。

<br/>

![防火墙堆栈](https://raw.githubusercontent.com/zhang21/images/master/cs/linux/security/firewalld-comparison-rhel7.png)

<br/>

---

<br/>

# nftables入门

nftables 框架提供数据包分类工具，它是 iptables、ip6tables、arptables、ebtables 和 ipset 工具的指定后继设备。与之前的数据包过滤工具相比，它在方便、特性和性能方面提供了大量改进，最重要的是：

- 内置查找表而不是线性处理
- IPv4 和 IPv6 协议的单一框架
- 规则会以一个整体被应用，而不是分为抓取、更新和存储完整的规则集的步骤
- 支持在规则集(nftrace) 中调试和追踪以及监控追踪事件
- 更加一致和压缩的语法，没有特定协议的扩展
- 用于第三方应用程序的 Netlink API

与 iptables 类似，nftables 使用表来存储链。链包含执行动作的独立规则。`ntf` 工具取代了之前数据包过滤框架的所有工具。`libnftnl` 库可用于通过 `libmnl` 库与 `nftables` Newlink API 进行低级交互。

<br/>

---

<br/>

# 系统审计

Linux Audit 系统提供了一种方式来跟踪系统中的安全相关信息。根据预配置的规则，审计会生成日志条目，以记录有关系统上发生事件的尽可能多的信息。

以下列表总结了审计可以在其日志文件中记录的一些信息：

- 事件的日期和时间、类型和结果。
- 主题和对象的敏感度标签。
- 事件与触发事件的用户的身份相关联。
- 对 Audit 配置的所有修改，并尝试访问 Audit 日志文件。
- 所有身份验证机制的使用，如 SSH 和 Kerberos 等。
- 对任何受信任数据库的更改，如 `/etc/passwd`。
- 尝试从系统导入或导出信息
- 根据用户身份、主题和对象标签以及其他属性，包含或排除事件。

<br/>

使用审计系统也是许多安全相关认证的一项要求。审计旨在满足或超过以下认证或合规指南的要求：

- 受控访问保护配置文件(CAPP)
- 标记的安全保护配置文件(LSPP)
- 规则集基本访问控制(RSBAC)
- 国家工业安全计划操作手册(NISPOM)
- 联邦信息安全管理法案(FISMA)
- 支付卡行业 - 数据安全标准(PCI-DSS)
- 安全技术实施指南(STIG)

<br/>

使用案例：

- 监视文件访问
- 监控系统调用
- 记录用户运行的命令
- 记录系统路径名称的执行
- 记录安全事件
- 搜索事件
- 运行摘要报告
- 监控网络访问

<br/>
<br/>

## 审计系统架构

Audit 系统由两个主要部分组成：用户空间应用程序和实用程序，以及内核端系统调用处理。

```bash
 # 安装audit软件包
 yum install audit
 
# 配置文件 /etc/audit/auditd.conf

# 启动
service start auditd
```

<br/>
<br/>

## 定义审计规则

可以指定以下审计规则类型：

- 控制规则：允许修改审计系统的行为及其部分配置。
- 文件系统规则：文件监视，允许审核特定文件或目录的访问权限。
- 系统调用规则：允许记录任何指定程序进行的系统调用。

<br/>
<br/>

### 使用auditctl定义审计规则

使用 `auditctl` 命令拉控制审计系统的基本功能，并定义相关的审计规则。

定义控制规则，修改审计系统的行为。

```bash
# 在内核中设置最大现有审计缓冲量
auditctl -b 8192

# 设置在检测到关键错误时执行的操作，如果出现严重错误，以上配置会触发内核 panic。
auditctl -f 2

# 启用或禁用审计系统或锁定其配置，锁定审计配置
auditctl -e 2

# 设置每秒生成的消息率，不设置所生成消息的速率限制
auditctl -r 0

# 报告审计系统的状态
auditctl -s

# 列出所有当前载入的审计规则
auditctl -l

# 删除所有当前载入的审计规则
auditctl -D
```

<br/>
<br/>

#### 定义文件系统规则

定义文件系统规则。

```bash
# 语法
# path_to_file: 被审计的文件或目录
# 权限： r/w/x/a
# key_name: 可选字符串，可帮助您识别生成了特定日志条目的规则或一组规则。
auditctl -w path_to_file -p permissions -k key_name
```

示例:

```bash
# 要定义一条规则，记录 /etc/passwd 文件的所有写入访问权限和每个属性更改
# 请注意，-k 选项后面的字符串是任意的
auditctl -w /etc/passwd -p wa -k passwd_changes

# 要定义一条规则，记录 /etc/selinux/ 目录中所有文件的写入访问和每个属性更改
auditctl -w /etc/selinux/ -p wa -k selinux_changes
```

<br/>
<br/>

#### 定义系统调用规则

定义系统调用规则。

```bash
# 语法
# 操作和过滤指定记录特定事件的时间。
# action 可以是 always 或 never。
# filter 指定将哪个内核规则匹配过滤器应用到事件，过滤器可以是：task、exit、user 和 exclude。
# system_call 指定系统调用的名称。可以在 /usr/include/asm/unistd_64.h 文件中找到所有系统调用的列表。
# Field=value 指定进一步修改规则以根据指定的体系结构、组 ID、进程 ID 和其他选项匹配的额外选项。
# key_name 是一个可选字符串，可帮助您识别生成了特定日志条目的规则或一组规则。
auditctl -a action,filter -S system_call -F field=value -k key_name
```

示例：

```bash
# 要定义当程序每次使用 adjtimex 或 settimeofday 系统调用时创建日志条目的规则，且系统使用 64 位构架
auditctl -a always,exit -F arch=b64 -S adjtimex -S settimeofday -k time_change

# 要定义规则，在每次删除文件时创建一个日志条目，或者由 ID 为 1000 或更高版本的系统用户重命名
auditctl -a always,exit -S unlink -S unlinkat -S rename -S renameat -F auid>=1000 -F auid!=4294967295 -k delete
```

<br/>
<br/>

#### 定义可执行文件规则

定义可执行文件规则。

```bash
# 语法
# 操作和过滤指定记录特定事件的时间。
# action 可以是 always 或 never。
# filter 指定将哪个内核规则匹配过滤器应用到事件，过滤器可以是：task、exit、user 和 exclude。
# system_call 指定系统调用的名称。可以在 /usr/include/asm/unistd_64.h 文件中找到所有系统调用的列表。
# path_to_executable_file 是被审计的可执行文件的绝对路径。
key_name 是一个可选字符串，可帮助您识别生成了特定日志条目的规则或一组规则。
auditctl  -a action,filter [ -F arch=cpu -S system_call] -F exe=path_to_executable_file -k key_name
```

示例：

```bash
# 要定义记录所有 /bin/id 程序执行的规则
auditctl -a always,exit -F exe=/bin/id -F arch=b64 -S execve -k execution_bin_id
```

<br/>
<br/>

### 定义持久化的审计规则和控制

要用就保存审计规则，您必须直接将其包含在 `/etc/audit/audit.rules` 文件中，或者使用 augenrules 程序读取位于 `/etc/audit/rules.d/` 目录中的规则。

`auditctl` 命令可使用 `-R` 选项从指定文件读取规则：

```bash
# 例如
auditctl -R /usr/share/doc/audit/rules/30-stig.rules
```

<br/>

在文件中定义控制规则。

```conf
# audit.rules
# Delete all previous rules
-D

# Set buffer size
-b 8192

# Make the configuration immutable -- reboot is required to change audit rules
-e 2

# Panic when a failure occurs
-f 2

# Generate at most 100 audit messages per second
-r 100

# Make login UID immutable once it is set (may break containers)
--loginuid-immutable 1
```

<br/>

在文件中定义文件系统和系统调用规则。

```conf
# audit.rules
-w /etc/passwd -p wa -k passwd_changes
-w /etc/selinux/ -p wa -k selinux_changes
-w /sbin/insmod -p x -k module_insertion

-a always,exit -F arch=b64 -S adjtimex -S settimeofday -k time_change
-a always,exit -S unlink -S unlinkat -S rename -S renameat -F auid>=1000 -F auid!=4294967295 -k delete
```

<br/>

使用 augenrules 定义持久性规则。

augenrules 脚本读取位于 `/etc/audit/rules.d/` 目录中的规则，并将它们编译到 `audit.rules` 文件中。这个脚本会根据自然的排序顺序按特定顺序处理以 `.rules` 结尾的所有文件。这个目录中的文件被组织到组中，其含义如下：

- 10：内核和 auditctl 配置
- 20：可与常规规则匹配但您希望不同匹配的规则
- 30：主要规则
- 40：可选规则
- 50：服务器特定规则
- 70：系统本地规则
- 90：结束（不可变）

```bash
# 在 /etc/audit/rules.d/ 目录中有规则后，使用 --load 指令运行 augenrules 脚本来加载它们
augenrules --load
```

<br/>
<br/>

## 了解审计日志文件

默认情况下，审计系统将日志条目存储在 `/var/log/audit/audit.log` 文件中。

审计日志由四个记录组成，它们共享相同的时间戳和序列号。记录始终以 `type=` 关键字开头。每个记录由多个 `name=值` 组成。

示例审计文件:

```log
type=SYSCALL msg=audit(1364481363.243:24287): arch=c000003e syscall=2 success=no exit=-13 a0=7fffd19c5592 a1=0 a2=7fffd19c4b50 a3=a items=1 ppid=2686 pid=3538 auid=1000 uid=1000 gid=1000 euid=1000 suid=1000 fsuid=1000 egid=1000 sgid=1000 fsgid=1000 tty=pts0 ses=1 comm="cat" exe="/bin/cat" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="sshd_config"
type=CWD msg=audit(1364481363.243:24287):  cwd="/home/shadowman"
type=PATH msg=audit(1364481363.243:24287): item=0 name="/etc/ssh/sshd_config" inode=409248 dev=fd:00 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:etc_t:s0  objtype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
type=PROCTITLE msg=audit(1364481363.243:24287) : proctitle=636174002F6574632F7373682F737368645F636F6E666967
```

<br/>
<br/>

## 搜索审计日志文件

`ausearch` 实用程序允许您搜索审计日志文件特定事件。默认情况下，ausearch 搜索 `/var/log/audit/audit.log` 文件。您可以使用 `ausearch -if file_name` 命令指定不同的文件。

示例：

```bash
# 查找失败的登录尝试
ausearch --message USER_LOGIN --success no --interpret

# 要搜索所有帐户、组和角色更改
ausearch -m ADD_USER -m DEL_USER -m ADD_GROUP -m USER_CHAUTHTOK -m DEL_GROUP -m CHGRP_ID -m ROLE_ASSIGN -m ROLE_REMOVE -i

# 要搜索特定用户执行的所有日志操作，请使用用户的登录 ID
ausearch -ua 1000 -i

# 要搜索直到现在为止所有失败的系统调用
ausearch --start yesterday --end now -m SYSCALL -sv no -i
```

<br/>
<br/>

## 创建审计报告

`aureport` 实用程序允许您针对审计日志文件中记录的事件生成摘要和列报告。默认情况下，会查询 `/var/log/audit/` 目录中的所有 `audit.log` 文件来创建报告。您可以使用 `aureport -if file_name` 命令指定针对 执行报告的其他文件。

示例:

```bash
# 要为过去三天（除当前示例日除外）内的日志事件生成报告
aureport --start 04/08/2013 00:00:00 --end 04/11/2013 00:00:00

# 要生成所有可执行文件事件的报告
aureport -x

# 要生成上述可执行文件事件报告的摘要
aureport -x --summary

# 要为所有用户生成失败事件的摘要报告
aureport -u --failed --summary -i

# 要为每个系统用户生成所有失败登录尝试的摘要报告
aureport --login --summary -i

# 要生成查询的所有 Audit 文件的报告以及包括的事件的时间范围
aureport -t
```

<br/>

---

<br/>

# 扫描系统以了解配置合规性和漏洞

合规审计是一个确定给定对象是否遵循合规性策略中指定的所有规则的过程。合规策略由安全专业人员定义，他们通常以检查清单的形式指定计算环境应使用的必要设置。

<br/>
<br/>

## RHEL中的配置合规工具

RHEL 提供了一些工具，使你可以执行完全自动化的合规性审计。

- SCAP Workbench
- OpenSCAP
- SCAP SSG
- 脚本检测引擎

<br/>
<br/>

## 漏洞扫描

<br/>
<br/>

### 红帽安全公告

RHEL 安全审计功能基于安全内容自动化协议(SCAP)标准。SCAP 是一种多用途规格框架，支持自动化配置、漏洞和补丁检查、技术控制合规性活动和安全衡量。

开放式漏洞评估语言(OVAL)是 SCAP 的基本和最旧组件。与其他工具和自定义脚本不同，OVAL 以声明性方式描述资源的必需状态。OVAL 代码绝不直接执行，而是使用称为扫描器的 OVAL 解释器工具。OVAL 的声明性质可确保评估的系统状态不会被意外修改。

与所有其他 SCAP 组件一样，OVAL 也基于 XML。SCAP 标准定义多种文档格式。它们各自包括一种不同的信息，用于不同的目的。

<br/>
<br/>

### 扫描系统是否有漏洞

Theoscap 命令行实用程序使您能够扫描本地系统，验证配置合规性内容，并根据这些扫描和评估生成报告和指南。此实用程序充当 OpenSCAP 库的前端，并根据它所处理的 SCAP 内容类型将其功能分组到模块。

```bash
# 安装
yum install openscap-scanner bzip2

# 下载系统的最新 RHSA OVAL 定义
wget -O - https://www.redhat.com/security/data/oval/v2/RHEL7/rhel-7.oval.xml.bz2 | bzip2 --decompress > rhel-7.oval.xml

# 扫描系统是否有漏洞，并将结果保存到 html 文件
oscap oval eval --report vulnerability.html rhel-7.oval.xml

# 扫描结果中，结果为 True 意味着系统存在安全漏洞。
# 使用浏览器打开 html 文件
```

<br/>
<br/>

### 扫描远程系统是否有漏洞

你还可以通过 SSH 协议使用 `oscap-ssh` 工具通过 OpenSCAP 扫描检查远程系统(需按照 openscap-scanner)是否有漏洞。

```bash
oscap-ssh joesec@machine1 22 oval eval --report remote-vulnerability.html rhel-7.oval.xml
```

<br/>
<br/>

## 配置合规性

您可以使用配置合规扫描来符合特定组织定义的基准。例如，如果您与美国政府合作，您可能需要遵守操作系统保护配置文件(OSPP)，如果您是一个支付处理器，您可能必须遵循支付卡行业数据安全标准(PCI-DSS)。您还可以执行配置合规性扫描来强化您的系统安全性。

红帽建议您遵循 SCAP 安全指南软件包中提供的安全内容自动化协议内容，因为它符合受影响组件的红帽最佳实践。

同样使用 OpenSCAP 来扫描程序。

<br/>
<br/>

## 漏洞扫描容器和容器镜像

你可以使用 `aoscap-docker` 命令行工具或 `atomic` 扫描命令行工具来查找容器或镜像中的安全漏洞。

```bash
# 安装
yum install openscap-containers -y

# 扫描镜像
oscap-docker image-cve 镜像ID --report vulnerability.html

# 扫描容器
oscap-docker container-cve 容器ID --report vulnerability.html

# 在浏览器中打开 html 文件

# 使用 atomic 扫描
yum install -y atomic
atomic scan [OPTIONS] [ID]
```

<br/>
<br/>

## 使用特定基础镜像评估容器或镜像的配置合规性

利用特定的安全基准评估容器或容器镜像的合规性。

```bash
# 安装
yum install openscap-utils scap-security-guide

# 评估容器镜像与 OSPP 配置集的合规性，并将结果保存到 html 文件
oscap-docker 镜像ID xccdf eval --report report.html --profile ospp /usr/share/xml/scap/ssg/content/ssg-rhel7-ds.xml
```

