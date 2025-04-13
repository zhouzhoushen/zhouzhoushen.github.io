---
date: '2024-04-13T23:38:32+08:00'
draft: true
title: 'XV6环境配置'
tags: ['Operating System']
categories: ['Operating System']
summary: ""
math: true
---
xv6是MIT开发的用于教学的操作系统。相关资料链接如下：

* [xv6-riscv源码](https://github.com/mit-pdos/xv6-riscv)
* [xv6课程官网](https://pdos.csail.mit.edu/6.828/2024/index.html)

## xv6环境配置

我们进入xv6源代码仓库，遵循`README.md`的指引安装必需的依赖后执行`make`命令构建指定的目标即可。

运行xv6需要安装的依赖有两个。

### qemu

qemu用于提供模拟的硬件环境。我们进入[qemu官网](https://www.qemu.org)，选择**从源代码构建**的安装方式，遵循官网列出的指令依次执行即可。

这里我们需要qemu模拟特定的硬件环境，riscv64-softmmu，需要遵循[qemu wiki](https://wiki.qemu.org/Documentation/Platforms/RISCV)中的说明修改构建配置选项。

### riscv-gnu-toolchain

该工具链包含了将源代码编译到riscv平台的编译工具。我们遵循xv6代码仓库中说明文档的指示，进入该工具链的代码仓库，遵循该仓库中的`README.md`说明文档拉取仓库，安装依赖工具，配置构建选项并执行构建即可。

当上述两项依赖都成功构建后，我们进入xv6代码仓库的根目录，执行其`README.md`中所说的`make qemu`，即可在`qemu`模拟器上启动`xv6`操作系统，按`ctrl-A + X`可以终止`qemu`，返回原命令行。