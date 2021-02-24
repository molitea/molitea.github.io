---
title: 路由器固件模拟
tags: [iot, router]
---

## 固件提取——binwalk

```shell
$ sudo apt-get update
$ sudo apt-get install build-essential autoconf git

$ git clone https://github.com/devttys0/binwalk.git
$ cd binwalk

# python2.7安装
$ sudo python setup.py install

# 自动安装依赖库文件
$ sudo ./deps.sh

# 提取固件 -M递归提取
$ binwalk -Me XX.bin
```

## 模拟执行——qemu

### qemu安装

```shell
sudo apt-get install qemu 
#user mode,包含qemu-mips-static，qemu-mipsel-static,qemu-arm-static等
sudo apt-get install qemu-user-static
#system mode，包含qemu-system-mips，qemu-system-mipsel,qemu-system-arm等
sudo apt-get install qemu-system
```

### qemu有两种运行模式

user mode : qemu-mips(mipsel/arm)-static。用户只需要将各种不同平台的处理编译得到的Linux程序放在QEMU虚拟中运行即可，其他的事情全部由QEMU虚拟机来完成，不需要用户自定义内核和虚拟磁盘等文件；

system mode:qemu-system-mips(mipsel) : 用户可以为QEMU虚拟机指定运行的内核或者虚拟硬盘等文件，简单来说系统模式下QEMU虚拟机是可根据用户的要求配置的。

#### user mode

用户模式下需要切换"/"目录，使用的是chroot命令，将根目录切换到binwalk提取出的文件系统目录下，例如squashfs-root，cpio-root。

在需要指定新的动态库实现一些函数的劫持时，用-E LD_PRELOAD="XX.so"

开启调试模式时，使用-g 1234

```shell
#将qemu-mips-static拷贝到当前目录下
$ sudo cp $(which qemu-mips-static) .
$ sudo chroot . ./qemu-mips-static -E LD_PRELOAD="XX.so" -g 1234 bin/boa
```

#### system mode

用这个模式稍微麻烦一点点，但是能获取到的信息也比较多，比如能够查看qemu内部进程的内存分布，设备之类的情况。

1.配置网卡

参考《qemu网络配置》

2.启动qemu

两个镜像下载：[debian](https://people.debian.org/~aurel32/qemu/mips/)

```shell
$ sudo qemu-system-mips -M malta -kernel vmlinux-2.6.32-5-4kc-malta \
-hda debian_squeeze_mips_standard.qcow2 \
-append "root=/dev/sda1 console=tty0" \
-net nic -net tap -nographic -s

参数说明：
-M 指定开发板 你能够使用-M ?參数来获取该qemu版本号支持的全部单板 （qemu-system-mips -M help 可查看支持列表）
-kernel 内核镜像路径
-hda/-hdb IDE硬盘镜像
-append 内核启动参数 内核命令行
-s 等同于-g 1234
```

3.开启qemu之后，将binwalk提取出的文件系统拷贝到虚拟机中

```shell
$ scp -r ./cpio-root  root@192.168.84.129:/root/
```

4.接下来就可以在远端mips虚拟机上运行程序

```shell
$ export LD_PRELOAD='XX.so'
$ chroot . bin/busybox
```

## 参考资料

[《一步一步PWN路由器之环境搭建》](https://xz.aliyun.com/t/1508)

[《路由器固件模拟环境搭建》](https://xz.aliyun.com/t/5697)

