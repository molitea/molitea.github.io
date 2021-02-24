---
title: 札记or备忘录
tags: 
---

# 札记or备忘录

## Python数据可视化库

pyecharts——[Echarts](https://github.com/ecomfe/echarts) 是一个由百度开源的数据可视化，凭借着良好的交互性，精巧的图表设计，得到了众多开发者的认可。而 Python 是一门富有表达力的语言，很适合用于数据处理。当数据分析遇上数据可视化时，[pyecharts](https://github.com/pyecharts/pyecharts) 诞生了。

igraph——igraph是网络分析工具的集合，着重于效率，可移植性和易用性。igraph开源、免费，可以使用R、Python、Mathematica和C/C++进行编程。（构建图结构）

networkx——networkx在2002年5月产生，是一个用Python语言开发的图论与复杂网络建模工具，内置了常用的图与复杂网络分析算法，可以方便的进行复杂网络数据分析、仿真建模等工作。networkx支持创建简单无向图、有向图和多重图；内置许多标准的图论算法，节点可为任意数据；支持任意的边值维度，功能丰富，简单易用。

## 网络流量分析

Zeek（以前称为Bro）——一款被动的开源网络流量分析器，它的主要用作一种安全监视器，可以对链接上的所有流量进行深入检查，并且生成大量的日志文件，以便于查找可疑活动的迹象。

Moloch——Moloch是一个开源，大规模，完整的数据包捕获，索引和数据库系统。

[《14个网络管理员必备的最佳网络流量分析工具》](https://blog.51cto.com/liufei888/2455000)

[BRIM](https://www.brimsecurity.com/)

## 网络侦查框架工具

ivre、reNgine

FinalRecon、Sandmap

## [取证工具](http://www.yidianzixun.com/article/0JGUTuyf)

### 内存取证

Windows：dumpit\RAMCapturer\Magnet RAM Capture\WinEn\Winpmem\EnCase Imager\FTK Imager\取证大师\取证神探

Linux：

dd（适合Linux早期版本）

[LiME](https://github.com/504ensicslabs/lime)\linpmem\Draugr\\[Volatilitux](https://code.google.com/archive/p/volatilitux/)\\[Memfetch](https://lcamtuf.coredump.cx/)\\[Memdump](http://manpages.ubuntu.com/manpages/bionic/en/man1/memdump.1.html)

[《Linux Memory Analysis》](https://www.jamesbower.com/linux-memory-analysis/)

**[AVML](https://github.com/microsoft/avml)**——[《**Intro to Linux memory forensics**》](https://stuxnet999.github.io/dfir/2020/09/20/Linux-Memory-Forensics.html)

linux内存镜像分析的时候需要Profile，所以也许取单个进程的内存是一个好的选择。（使用gdb dump地址空间数据）

### 内存分析

[Comparative Analysis of Free Tools for Physical Memory Dumps Parsing](https://soshace.com/comparative-analysis-of-free-tools-for-physical-memory-dumps-parsing/)