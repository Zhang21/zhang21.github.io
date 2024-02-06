# CentOS7升级Glibc到2.28


参考:

- [CentOS7 升级 Glibc 2.17 到2.28](https://roy.wang/centos7-upgrade-glibc/)
- [centos7 glibc2.17升级到glibc2.28](https://blog.csdn.net/esdhhh/article/details/121196317?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-121196317-blog-107057646.235%5Ev36%5Epc_relevant_default_base&spm=1001.2101.3001.4242.1&utm_relevant_index=1)
- [https://www.cnblogs.com/jasondayee/p/13403245.html](https://www.cnblogs.com/jasondayee/p/13403245.html)

<br/>

---

<!--more-->

<br/>

# 问题

参考上面的文章，遇到了几个问题。

- `make install` 报错，虽然可以忽略，glibc 也成功升级。
- 系统语系和编码异常，导致中文显示乱码和程序编码异常。
- 默认的 `ldd` 命令还是旧的 glibc 版本。

<br/>

`make install` 异常。

```sh
# /usr/bin/ld: cannot find -lnss_test2

# 需要修改 scripts/test-installation.pl 文件

# 参数也发生变化
../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin --enable-obsolete-nsl
```

<br/>

系统语系和编码异常，这个问题找了好久才解决。

```sh
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_COLLATE to default locale: No such file or directory

# 需要更新 locale 相关文件
cd build
make localedata/install-locales
# 它会更新 /usr/lib/locale/locale-archive 这个文件，之后就正常了。
```

<br/>

默认的 `ldd` 命令还是识别的旧的 glibc 2.17 版本，因此需要替换下 ldd 命令。

```sh
# 找到新的 ldd
find / -name ldd

# 我的
ls /opt/glibc-2.28/build/elf/ldd 

# 备份旧的
cp /usr/bin/ldd{,-2.17}
# 复制新的
cp /opt/glibc-2.28/build/elf/ldd /usr/bin/ldd
chmod +x /usr/bin/ldd
```

