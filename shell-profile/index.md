# Shell配置文件加载流程


Linux 操作系统登录 Shell 时会加载哪些配置文件，它们的顺序和作用是什么。

<br/>

<!--more-->

<br/>

---

<br/>

# 概述

Linux 操作系统，Bash 环境下，登录 Shell 会加载哪些配置文件，它们的顺序和作用是什么。

登录 shell 时，有两种类型：

- **login**：取得 bash 需要完整的登录流程。通常是登录使用的用户。会读取 `/etc/profile` 和 `~/.bash_profile` 文件来应用新的环境变量。
- **no-login**：取得 bash 不需要登录。通常是程序启动使用的用户。读取 `~/.bashrc` 文件来应用新的环境变量。

<br/>

---

<br/>

# 配置读取流程

`su` 命令切换用户没有获取用户的环境，也就是默认执行了 `nologin` shell。而 `su -`（-, -l, --login） 命令则会执行 `login` shell，从而读取用户环境。

<br/>
<br/>

## login配置读取

![](https://raw.githubusercontent.com/zhang21/images/master/cs/linux/shell/login-shell.png)

<br/>
<br/>

## nologin配置读取

![](https://raw.githubusercontent.com/zhang21/images/master/cs/linux/shell/nologin-shell.png)

<br/>

---

<br/>

# 配置文件介绍

系统级别 shell 配置文件：

- `/etc/profile`：包含系统范围内的环境变量（如 `PATH`, `USER`, `HOME`等）、别名、函数等配置，此文件对所有用户生效。它会调用以下配置文件：
  - `/etc/inputrc`：配置 readline 库，提供命令行编辑功能（如历史记录、补全、编辑、快捷键等）。
  - `/etc/profile.d/*.sh`：定义的一些脚本。
  - `/etc/sysconfig/i18n`：系统语系（语言、字符集、区域等）。
- `/etc/bashrc`：包含交互式 shell 的配置（如命令提示符、别名、函数等）。此文件对所有用户生效。

<br/>

用户级别 shell 配置文件：

- 用户登录时：
  - `~/.bash_profile`：如果存在，则忽略下面两个。它会调用 `~/.bashrc` 文件。
  - `~/.bash_login`：上个不存在，则读取。
  - `~/.profile`：上两个都不存在，则读取。
  - `~/.bashrc`：它会调用 `/etc/bashrc` 文件。
- 用户退出时
  - `~/.bash_history`：记录用户的 bash 历史命令。
  - `~/.bash_logout`：记录 bash 退出时系统做了什么。

<br/>

---

<br/>

# 注意事项

有些木马，除了会在定时任务（如 `/var/spool/cron/*`, `/etc/cron*/`）里隐藏木马程序外，也可能在上述的系统或用户级别的配置文件里隐藏木马程序，请注意！

