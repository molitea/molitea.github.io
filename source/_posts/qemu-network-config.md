---
title: QEMU网络配置
tags: qemu
---

[《QEMU 网络配置》](https://wzt.ac.cn/2019/09/10/QEMU-networking/)

```shell
apt-get install qemu 
apt-get install qemu-user-static
apt-get install qemu-system
apt-get install uml-utilities
apt-get install bridge-utils

ifconfig <你的网卡名称> down
brctl addbr br0     # add bridge br0
brctl addif br0 <你的网卡名称>     # add interface to br0
brctl stp br0 off   # 如果只有一个网桥，则关闭生成树协议
brctl setfd br0 1   # 设置br0的转发延迟
brctl sethello br0 1    # 设置br0的hello时间
ifconfig br0 0.0.0.0 promisc up # 启用br0
ifconfig <你的网卡名称> 0.0.0.0 promisc up
dhclient br0        # 从dhcp服务器获取br0的IP地址
brctl show br0

tunctl -t tap0 -u root	# 创建一个tap0的接口，只允许root用户访问
brctl addif br0 tap0 # 在虚拟网桥中添加一个tap0接口
ifconfig tap0 0.0.0.0 promisc up # 启用tap0接口
brctl showstp br0   # 显示 br0 的各个接口
```

```shell
# 启用qemu
sudo qemu-system-mipsel -M malta -kernel vmlinux-3.2.0-4-4kc-malta -hda debian_squeeze_mipsel_standard.qcow2 -append "root=/dev/sda1 console=tty0" -nographic -net nic -net tap,ifname=tap0,script=no,downscript=no

#特别说明一下参数含义：-net nic 表示希望 QEMU 在虚拟机中创建一张虚拟网卡，-net tap 表示连接类型为 TAP，并且指定了网卡接口名称(就是刚才创建的 tap0，相当于把虚拟机接入网桥)。script 和 downscript 两个选项的作用是告诉 QEMU 在启动系统的时候是否调用脚本自动配置网络环境，如果这两个选项为空，那么 QEMU 启动和退出时会自动选择第一个不存在的 tap 接口(通常是 tap0)为参数，调用脚本 /etc/qemu-ifup 和 /etc/qemu-ifdown。由于我们已经配置完毕，所以这两个参数设置为 no 即可。
```



