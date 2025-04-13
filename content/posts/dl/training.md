---
date: '2024-04-13T23:38:32+08:00'
draft: false
title: '模型训练'
tags: ['Deep Learning']
categories: ['Deep Learning']
description: ""
summary: ""
math: true
---
模型训练的时间通常很长，我们希望能在断开ssh连接后，模型依然在远程服务器上训练，并且希望在重新建立ssh连接后查看训练进度。这样，我们就可以将等待模型训练或推理的时间变成更有意义的时间😊

笔者选择使用screen来实现上述目的。screen是一个终端复用器，它允许我们在一个终端会话中运行多个会话，并且可以在断开连接后继续运行。补充：tmux是另一种选择。

### Step 1: 创建一个窗口

* 创建窗口：

```bash
screen -S [screen_name]
```

* 查看screen列表：

```bash
screen -ls
```

上面的命令输出如下：

```bash
There is a screen on:
        65141.screen_name    (current time)   (Attached)
1 Socket in /var/run/screen/S-user.
```

其中，`65141.screen_name`是窗口的名字。

断开连接后，`attached`会变成`detached`。

### Step 2: 恢复窗口

```bash
screen -D  -r 65141.screen_name
```

`screen -D`选项用于断开所有现有的`screen`会话连接，并将指定的会话重新附加到当前终端。`screen -r`用于恢复指定的screen会话。

可以用`screen -X`来发送命令到指定的screen会话，因此

```bash
screen -S [窗口名称] -X quit
```

用于删除某个窗口。

### 补充

linux在进入screen模式下之后，发现是无法在终端使用鼠标滚轮进行上下翻页拉动的，无法查看上面的终端输出内容了。直接使用鼠标或触摸板滚动只会在命令行中输出对应的字符。要想在screen中滚动，首先需要进入回滚模式。

```bash
先按Ctrl+a键，然后释放，然后再按[键即可进入翻页模式。
```

通过`Ctrl+c`可以切换会之前的模式。