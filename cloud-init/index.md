# Cloud-init


参考:

- [Cloud-init文档](https://cloudinit.readthedocs.io/en/latest/)

<br/>

<!--more-->

<br/>

# 教程

Cloud-init 是是一个 Linux 软件包，负责处理实例的早期初始化。可使用它来安装程序包和写入文件，或者配置用户和安全性。

<br/>
<br/>

## 使用QEMU的核心教程

本教程中，我们将在虚拟机中启动一个 Ubuntu 云镜像，并使用 cloud-init 来预先配置系统。

QEMU 是一个跨平台的仿真器，能够运行高性能的虚拟机。

安装:

```bash
# ubuntu
apt-get install qemu

# rhel
yum install qemu-kvm
```

<br/>
<br/>

### 定义我们的用户数据

云计算镜像通常都是预安装了 cloud-init，并被配置为在首次启动时运行。

现在需要创建我们的用户数据文件。这个示例用户数据配置了默认用户的密码，并将改密码设置为永不过期。

示例 user-data 文件:

```yaml
#cloud-config
password: password
chpasswd:
  expire: False
```

第一行 `#cloud-config` 告诉 cloud-init 配置中的用户数据是什么类型。Cloud-config 是一种基于 YAML 的配置类型，告诉 cloud-init 如何配置虚拟机实例。

第二行 `password: password`，根据用户和组模块来设置用户的密码。

第三和第四行执行 cloud-init 不要在第一次登录时重置密码。

<br/>
<br/>

### 定义我们的元数据

创建 meta-data 元数据文件:

```yaml
instance-id: someid/somehostname
local-hostname: jammy
```

<br/>
<br/>

### 定义我们的供应商数据

创建 vendor-data 空文件:

```bash
touch tmp/vendor-data
```

<br/>
<br/>

### 启动一个webserver

实例元数据服务（IMDS, Instance Metadata Service）是由大多数云提供商提供的一种服务，作为向虚拟机实例提供信息的一种手段。

本教程中，使用 QEMU 和一个简单的 Python webserver 来模拟这个工作流程。

```bash
cd temp
python3 -m http.server --directory .
```

<br/>
<br/>

### 用我们的用户数据启动虚拟机

下载云镜像:

```bash
wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img
```

<br/>

使用下面的命令启动我们的虚拟机:

```bash
qemu-system-x86_64                                              \
    -net nic                                                    \
    -net user                                                   \
    -machine accel=kvm:tcg                                      \
    -cpu host                                                   \
    -m 512                                                      \
    -nographic                                                  \
    -hda jammy-server-cloudimg-amd64.img                        \
    -smbios type=1,serial=ds='nocloud-net;s=http://10.0.2.2:8000/'
```

地址是上面启动的 webserver 的地址。

<br/>
<br/>

### 验证是否正常运行

启动虚拟机后，我们应该可以使用默认的用户（`ubuntu`） 和定义的密码来连接到实例。

<br/>
<br/>

### 检查cloud-init的状态

```bash
cloud-init status --wait
```

<br/>
<br/>

## 使用LXD的教程

LXD 是基于容器的，因此允许我们快速测试和迭代我们的用户数据定义。

```bash
# 安装
sudo snap install lxd

# 初始化
lxd init --minimal
```

<br/>

### 定义用户数据

`/tmp/my-user-data` 数据，使用 runcmd 模块定义命令来执行。

```yaml
#cloud-config
runcmd:
  - echo 'Hello, World!' > /var/tmp/hello-world.txt
```

<br/>
<br/>

### 使用用户数据启动一个LXD容器

```bash
# 启动
lxc launch ubuntu:focal my-test --config=user.user-data="$(cat /tmp/my-user-data)"

# 验证
lxc shell my-test
cloud-init status --wait

# 验证用户数据
cloud-init status --wait

# 停止和移除lxd容器
lxd stop my-test
lxd rm my-test
```

<br/>

---

<br/>

# 使用指南

使用 cloud-init 完成一些常见的操作和任务。

<br/>
<br/>

## 在本地测试cloud-init

How to test cloud-init locally before deploying

在将云计算部署到云端时，你可能想在本地测试 cloud-init。幸运的是，有虚机和容器工具来做这个测试：

- Multipass: 一个跨平台工具，用于在Linux、Windows和MacOS上启动Ubuntu虚拟机。
- LXD: 为使用 Linux 系统容器提供了一个简化的用户体验。
- QEMU

<br/>
<br/>

## 改变模块的运行频率

How to change a module’s run frequency

你可能想改变模块运行的默认频率，例如，使模块在每次启动时都运行。

你可以修改 `/etc/cloud/cloud.cfg` 配置文件：

```yml
     cloud_final_modules:
     # list shortened for brevity
      - [phone_home, always]
      - final_message
      - power_state_change
```

<br/>
<br/>

## 如何禁用cloud-init

人们可能希望禁用 cloud-init 以确保它在随后的启动中不做任何事情。

有两种方法来禁用它：

- 创建禁用的空文件
- 修改内核命令行

```bash
# 创建禁用空文件
touch /etc/cloud/cloud-init.disabled

# 修改内核命令行
echo 'GRUB_CMDLINE_LINUX=cloud-init.disabled' >> /etc/default/grub
grub-mkconfig -o /boot/efi/EFI/ubuntu/grub.cfg
```

<br/>

---

<br/>

# 解释

解释性和概念性指南是为了让您更好地了解 cloud-init 是如何工作的。

<br/>
<br/>

## 配置源

Configuration sources

cloud-init 构建了一个单一的配置，然后在整个生命周期中被引用。该配置由多个来源建立，如果一个键在多个来源中被定义，则优先级较高的来源会覆盖优先级较低的来源。

<br/>
<br/>

### 基础配置

从最低优先级到最高，以下四个配置源构成了基本配置：

- 硬编码的配置：存在于 cloud-init 源的配置，不能被修改。
- 配置目录：
  - `/etc/cloud/cloud.cfg`
  - `/etc/cloud/cloud.cfg.d`
- 运行时配置：`/run/cloud-init/cloud.cfg`
- 内核命令行：在内核命令行中，在 `cc:` 和 `end_cc` 之间发现的任何东西都将被解释为云配置用户数据。

<br/>
<br/>

### 供应商和用户数据

Vendor and user data

这些数据都是从数据源中获取，并在实例启动时定义。

<br/>
<br/>

### 网络配置

Network configuration

网络配置独立于其他 cloud-init 配置而发生。

<br/>
<br/>

### 特定配置

Specifying configuration

终端用户定义的配置、发行商定义的配置和云提供商定义的配置。

<br/>
<br/>

## 启动阶段

Boot stages

cloud-init 启动有五个阶段：

- 生成器阶段（Generator）
- 本地阶段（Local）
- 网络阶段（Network）
- 配置阶段（Config）
- 最终阶段（Final）

<br/>
<br/>

### 生成器阶段

在 systemd 下启动时，将运行一个生成器，确定 `cloud-init.target` 是否应包含在启动目标中。ds-identify 在此阶段运行。

<br/>
<br/>

### 本地阶段

`cloud-init-local.service` 服务。

本地阶段的目的是：

- 找到本地数据源
- 并在系统中应用网络配置

<br/>
<br/>

### 网络阶段

`cloud-init.service` 服务。

这个阶段要求所有配置的网络都是在线的，因为它将完全处理发现的任何用户数据。这意味着：

- 检索任何 `#include` 或 `#include-once`，包括 http
- 解压任何压缩的内容
- 运行任何发现的部分处理程序（part-handler）

这个阶段运行 `disk_setup` 和 `mounts` 模块，这些模块可以对磁盘进行分区和格式化，并配置挂载点（如 `/etc/fstab`）。这些模块不能提前运行，因为它们可能从只能通过网络获得的资源中接收配置输入。

<br/>
<br/>

### 配置阶段

`cloud-config.service` 阶段。

这个阶段仅运行配置模块。对启动的其他阶段没有实际影响的模块在这里运行，包括 `runcmd`。

<br/>
<br/>

### 最终阶段

`cloud-final.service` 服务。

这个阶段在启动时尽可能晚地执行。任何用户习惯于在登录系统后运行的脚本都应该在这里正确运行。包括：

- 包安装
- 配置管理插件（Ansible， Chef等）
- 用户定义的脚本

<br/>
<br/>

### 首次启动确定

First boot determination

cloud-init 必须去顶当前的启动是否为新实例的第一次启动，这样它就可以应用适当的配置。在实例的第一次启动时，它应该运行所有的“每实例（per-instance）”配置，而在随后的启动中，它应该只运行“每启动（per-boot）”配置。本节描述了 cloud-init 如何执行这一决定，以及为什么它是必要的。

当它运行时，cloud-init 会存储其内部状态的缓存，以便在各阶段和启动时使用。

如果缓存存在，那么 cloud-init 曾经在这个系统上运行过。有两种情况：

- 最常见的情况是，实例已经被重启，这是后续的启动。
- 或者，文件系统被连接到一个新的实例上，而这是该实例的第一次启动。最明显的情况是，当一个实例从一个已启动的实例捕获的镜像中启动时，这种情况回发生。

默认情况下，cloud-init 试图通过检查缓存中的实例 ID 与它在运行时确定的实例 ID，来确定它在哪个情况下运行。如果它们不匹配，那么这是一个实例的第一次启动；否则，这是一次后续启动。cloud-init 将这种行为称为检查。

这种行为对于从启动的实例中捕获的图像来说是必需的，因此是通用的云镜像的默认配置。然而在某些情况下，它可能会导致问题。对于这些情况，cloud-init 支持修改其行为，以无条件信任系统中存在的 实例 ID。这意味着当缓存存在时，cloud-init 将永远不会检测到一个新的实例，由此可见，导致 cloud-init 检测到一个新的实例的唯一方法是手动删除 cloud-init 的缓存，这种行为被称为信任。

要配置使用这些行为中的哪一种，cloud-init 暴露了 `manul_cache_clean` 配置项。当为 `false` （默认值）时，如果实例 ID 不匹配，cloud-init 将检查并清除缓存。当为 `true` 时，cloud-init 将信任现有的缓存（因此并不清理）。

<br/>
<br/>

### 手动清理缓存

Manual cache cleaning

cloud-init 提供了一个用于手动清理缓存的命令：`cloud-init clean`。

<br/>
<br/>

## 用户数据格式

User data formats

<br/>

### cloud-config数据

Cloud config data

使用 cloud-config 的语法，文件必须是有效的 YAML 语法。用户可以以人性化的格式指定某些事情。这些事情包括：

- 首次启动时运行 `apt upgrade`
- 使用不同的 `apt` 镜像
- 添加不同的 `apt` 源
- 导入特定的 SSH 密钥
- 其他事情

以 `#cloud-config` 开始，或当使用 MIME 时的 `Content-Type: text/cloud-config`。

cloud-init v18.4 支持使用 Jinja 模板来渲染实例的元数据变量。

<br/>
<br/>

### 用户数据脚本

User data script

可以执行用户定义的 shell script。

以 `#!` 开头，或在 MIME 中使用 `Content-Type: text/x-shellscript`。

用户数据脚本可以选择使用 Jinja 模板渲染云实例元数据变量。

一个示例用户脚本，并运行。

```bash
#!/bin/sh
echo "Hello World.  The time is now $(date -R)!" | tee /root/output.txt
```

```bash
euca-run-instances --key mykey --user-data-file myscript.sh ami-a07d95c9
```

<br/>
<br/>

### 内核命令行数据

Kernel command line

用户可以通过内核命令行参数传递用户数据。

<br/>
<br/>

### Gzip压缩内容

Gzip compressed content

发现被 gzip 压缩的内容将被解压缩。然后，未压缩的数据将被当作未压缩的数据使用。这通常是有用的，因为用户数据被限制在 16,384 字节。

<br/>
<br/>

### MIME多部份档案

MIME multi-part archive

使用 MIME 多部份文件，用户可以指定一个以上的数据类型。

查看支持的类型:

```bash
cloud-init devel make-mime --list-types

# 输出
cloud-boothook
cloud-config
cloud-config-archive
cloud-config-jsonp
jinja2
part-handler
x-include-once-url
x-include-url
x-shellscript
x-shellscript-per-boot
x-shellscript-per-instance
x-shellscript-per-once
```

<br/>
<br/>

### 包括文件

include file

该文件包含一个 URL 的列表，每行一个。

以 `#include` 开头，或在 MIME 的 `Content-Type: text/x-include-url`。

<br/>
<br/>

### cloud-boothook

启动钩子，它存储在 `/var/lib/cloud` 下的一个文件中并立即执行。这是最早的钩子。注意，没有提供只运行一次的机制，boothook 必须自己来处理这个问题。

以 `#cloud-boothook` 开头，或在 MIME 的 `Content-Type: text/cloud-boothook`。

<br/>
<br/>

### Part-handler

它包含自定义代码，用于支持多部份用户数据中的新的 mime-types，或覆盖现有的支持邮件类型的处理程序。它将被写入 `/var/lib/cloud/data` 中的一个文件，基于其文件名。

这必须是包含一个 `list_types` 函数和一个 `handle_part` 函数的 Python 代码。一旦该部分被读取，`list_types` 方法将被调用。它必须返回这个 part-handler 处理的 mime-types 的列表。由于 MIME 部分是按顺序处理，一个 part-handler 的部分必须在它预期的同一用户数据中处理的任何具有 mime-types 的部分之前。

`handle_part` 函数必须像这样被定义：

```py
def handle_part(data, ctype, filename, payload):
  # data = the cloudinit object
  # ctype = "__begin__", "__end__", or the mime-type of the part that is being handled.
  # filename = the filename of the part (or a generated filename if none is present in mime data)
  # payload = the parts' content
```

<br/>

cloud-init 将在处理任何部件之前调用这个 `handler_part` 函数，每收到一个部件调用一次，在处理完所有部件之后调用一次。`__begin__` 和 `__end__` 发送器允许部件处理程序在接收任何部件之前或之后进行初始化或拆分。

以 `#part-handler` 开头，或在 MIME 中的 `Content-Type: text/part-handler`。

<br/>

示例：

```py
#part-handler

def list_types():
    # return a list of mime-types that are handled by this module
    return(["text/plain", "text/go-cubs-go"])

def handle_part(data, ctype, filename, payload):
    # data: the cloudinit object
    # ctype: '__begin__', '__end__', or the specific mime-type of the part
    # filename: the filename for the part, or dynamically generated part if
    #           no filename is given attribute is present
    # payload: the content of the part (empty for begin or end)
    if ctype == "__begin__":
       print("my handler is beginning")
       return
    if ctype == "__end__":
       print("my handler is ending")
       return

    print(f"==== received ctype={ctype} filename={filename} ====")
    print(payload)
    print(f"==== end ctype={ctype} filename={filename}")
```

<br/>
<br/>

### 禁用用户数据

Disabling user data

cloud-init 可以配置来忽略任意用户数据。在配置文件中设置 `allow_userdata: false` 将禁用 cloud-init 处理用户数据。

<br/>
<br/>

## 事件和更新

Events and updates

<br/>

### 事件

Events

cloud-init 将在几种事件类型中获取并应用云和用户数据的配置。对 cloud-init 而言，两个最常见的事件是：实例首次启动和此后的任何启动。除了启动事件外，cloud-init 还对用户和供应商添加的设备的时间感兴趣。cloud-init 当前支持以下事件类型：

- `BOOT_NEW_INSTANCE`：新实例的首次启动。
- `BOOT`：此后的任何启动。
- `BOOT_LEGACY`：在每次启动时应用网络配置两次。
- `HOTPLUG`：动态添加一个系统设备。

未来可能包括的事件：

- `METADATA_CHANGE`：实例的元数据发生改变。
- `USER_REQUEST`：指示请求更新。

<br/>
<br/>

### 数据源事件支持

Datasource event support

所有数据源默认支持 `BOOT_NEW_INSTANCE` 事件。每个数据源都会声明它能够处理的这些事件的集合。

<br/>
<br/>

### 配置事件更新

Configuring event updates

更新配置可以通过用户数据来指定，可以用来启用或禁用对特定事件的处理。只要数据源支持这些事件，这种配置就会被遵守。然而，配置总是在第一次启动时被应用，不管指定的用户数据如何。

更新策略配置定义了哪些事件允许被处理。

- `scope`：事件策略的范围的名称
- `when`：一个特定范围处理的事件列表

<br/>
<br/>

### 热插拔

Hotplug

当热插拔事件被数据源支持并在用户数据中配置后，cloud-init 将响应系统中网络接口的添加或移除。除了获取和更新系统元数据外，cloud-init 还将调出/调入新添加的接口。

<br/>
<br/>

## 实例元数据

Instance metadata

<br/>

### 什么是实例数据

What is instance-data

每个云提供商向启动的云实例提供唯一的配置元数据（metadate）。cloud-init 会抓取这些元素据，然后缓存并将这些信息作为标准化和版本化的 JSON 对象公开，即实例数据（instance-data）。实例数据可以被查询，或随后被 cloud-init 在模板化配置和脚本中使用。

一个启动的 EC2 实例上的实例数据的小子集：

```json
{
  "v1": {
    "cloud_name": "aws",
    "distro": "ubuntu",
    "distro_release": "jammy",
    "distro_version": "22.04",
    "instance_id": "i-06b5687b4d7b8595d",
    "machine": "x86_64",
    "platform": "ec2",
    "python_version": "3.10.4",
    "region": "us-east-2",
    "variant": "ubuntu"
  }
}
```

可以使用 cloud-init query 工具在机器上探索 instance-data 变量。

<br/>
<br/>

### 使用实例数据

Using instance-data

实例数据可以用于：

- 用户脚本数据
- cloud-config 数据
- 基本配置
- cloud-int 命令行接口

使用 `## template: jinja` 开头，cloud-init 将使用 jinja 模板来渲染配置文件。任何实例数据变量都将作为 jinja  模板变量浮现。

敏感数据（如密码）可能包含在实例数据中，cloud-init 将这些敏感数据分离出来，使其只能由 root 用户读取。

<br/>

示例：使用云配置的实例数据。

```jinja
## template: jinja
#cloud-config
runcmd:
    - echo 'EC2 public hostname allocated to instance: {{
      ds.meta_data.public_hostname }}' > /tmp/instance_metadata
    - echo 'EC2 availability zone: {{ v1.availability_zone }}' >>
      /tmp/instance_metadata
    - curl -X POST -d '{"hostname": "{{ds.meta_data.public_hostname }}",
      "availability-zone": "{{ v1.availability_zone }}"}'
      https://example.com
```

<br/>

示例：使用用户脚本的实例数据。

```jinja
## template: jinja
#!/bin/bash
{% if v1.region == 'us-east-2' -%}
echo 'Installing custom proxies for {{ v1.region }}'
sudo apt-get install my-xtra-fast-stack
{%- endif %}
...
```

<br/>

示例：实例数据的命令行发现

```jinja
# List all instance-data keys and values as root user
sudo cloud-init query --all
{...}

# List all top-level instance-data keys available
cloud-init query --list-keys

# Introspect nested keys on an object
cloud-init query -f "{{ds.keys()}}"
dict_keys(['meta_data', '_doc'])

# Failure to reference valid dot-delimited key path on a known top-level key
cloud-init query v1.not_here
ERROR: instance-data 'v1' has no 'not_here'

# Test expected value using valid instance-data key path
cloud-init query -f "My AMI: {{ds.meta_data.ami_id}}"
My AMI: ami-0fecc35d3c8ba8d60

# The --format command renders jinja templates, this can also be used
# to develop and test jinja template constructs
cat > test-templating.yaml <<EOF
  {% for val in ds.meta_data.keys() %}
  - {{ val }}
  {% endfor %}
  EOF
cloud-init query --format="$( cat test-templating.yaml )"
- instance_id
- dsmode
- local_hostname
```

<br/>
<br/>

### 实例数据参考

存储路径：

- `/run/cloud-init/instance-data.json`：含有标准化密钥的可读 JSON，敏感密钥被编辑。
- `/run/cloud-init/instance-data-sensitive.json`：root 可读的未经编辑的 JSON blob。

<br/>

`instance-data.json` 顶级键：

- `base64_encoded_keys`
- `sensitive_keys`
- `merged_cfg`
- `ds`
- `sys_info`
- `v1`
  - `v1._beta_keys`
  - `v1.cloud_name`
  - `v1.distro`, `v1.distro_version`, `v1.distro_release`
  - `v1.instance_id`
  - `v1.kernel_release`
  - `v1.local_hostname`
  - `v1.machine`
  - `v1.platform`
  - `v1.subplatform`
  - `v1.public_ssh_keys`
  - `v1.python_version`
  - `v1.region`
  - `v1.availability_zone`

<br/>

一个示例输出：

<br/>
<br/>

### 内核命令行配置数据

通过内核命令行提供配置数据，在某种程度上是最后一种手段。因为这种方法只支持以 `#cloud-config` 开头的云配置，而许多数据源不支持在不修改引导程序的情况下注入内核命令行参数。

<br/>
<br/>

## 供应商数据

Vendor data

供应商数据是由启动实例的实体（如云提供商）提供的数据，这个数据可以用来定制镜像，以适应它所运行的特定环境。

- 用户对供应商数据有最终控制权。
- 默认情况下，旨在首次启动时运行。
- 用户可以禁用，但如果示例运行需要使用供应商数据，那就不应该禁用。
- 用户提供的云配置被合并到供应商数据之上。

<br/>

提供云配置数据的用户可以使用 `#cloud-config-jsonp` 方法更精细地控制数据。

示例：

```yml
#cloud-config-jsonp
[{ "op": "add", "path": "/runcmd", "value": ["my", "command", "here"]}]
```

<br/>

cloud-init 将下载并缓存它发现的任何供应商数据到文件系统。它的处理与用户数据完全相同，它也可以提供多部份的输入，并以与用户数据相同的方式对这些部分进行操作。

两者的不同之处：

- 供应商数据定义的脚本与用户数据定义的脚本存储在不同的为止
- 用户可以通过云配置设置禁用部件处理程序。

```yml
#cloud-config
vendordata: {excluded: 'text/part-handler'}
```

<br/>
<br/>

## 安全性

Security

<br/>
<br/>

## 性能

Performance

`analyze` 子命令可以帮助分析性能。

```bash
cloud-init analyze blame
cloud-init analyze show
cloud-init analyze dump
cloud-init analyze boot
```

<br/>
<br/>

## 内核命令行

Kernel command line

通过内核命令行提供配置数据在某种程度上是一种最后的手段，此方法只支持以 `#cloud-config` 开头的 cloud config，而许多数据源不支持在不修改引导程序的情况下注入内核命令行参数。

<br/>
<br/>

### 数据源发现覆盖

Datasource discovery override

在启动过程中，cloud-init 必须确定它在哪个数据源上运行（OpenStack、AWS、Azure、GCP等）。这个步骤可以通过指定数据源来选择性覆盖：

```txt
root=/dev/sda ro ds=openstack
```

<br/>
<br/>

### 内核cloud-config-url配置

为了让一个短暂的，或其他原始的镜像接受一些配置，cloud-init 可以读取一个由内核命令行指示的 URL，并像其数据以前存在一样运行。

这允许配置一个元数据服务，或其他数据。

当本地阶段运行时，它将检查 `cloud-config-url` 是否以 `key/value` 方式出现在命令行中。

```cfg
root=/dev/sda ro cloud-config-url=http://foo.bar.zee/abcde
```

cloud-init 将读取指定 URL 的内容。如果内容以 `#cloud-config` 开头，它将把这些数据存储到本地文件系统的静态文件名 `/etc/cloud/cloud.cfg.d/91_kernel_cmdline_url.cfg` 中，并从这一点触发将其视为配置的一部分。如果此文件已存在，cloud-init 不会覆盖该文件，并且 cloud-config-url 参数将被完全忽略。

示例：

```yml
#cloud-config
datasource:
  MAAS:
    metadata_url: http://mass-host.localdomain/source
    consumer_key: Xh234sdkljf
    token_key: kjfhgb3n
    token_secret: 24uysdfx1w4
```

<br/>
<br/>

# 参考资料

Reference

