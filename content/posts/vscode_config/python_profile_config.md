---
date: '2024-04-13T23:38:32+08:00'
draft: false
title: 'VsCode Python Profile配置'
tags: ['VsCode', 'Config']
categories: ['Config']
summary: ""
math: true
---

在PyCharm中可以正常运行的项目在VsCode中需要一定的配置后才能运行。

## Step 1

首先需要安装Python插件，VsCode的docs中有详尽的说明，耐心地阅读并跟随即可。

## Step 2

打开项目文件夹后，首先需要指定解释器（和虚拟环境一一对应），Ctrl+P在命令面板或者集成终端中选择即可。

## Step 3

和PyCharm不一样，用VSCode写自定义module的时候，会出现找不到module的报错，原因是VSCode不会像IDE那样，帮用户把项目目录临时性加入到系统PATH中去。

一种办法是配置一个系统变量PYTHONPATH，python在查找module的时候会到这个变量定义的目录里去查找，所以在不同平台下临时性定义这个系统变量，也是个解决导入自定义模块的办法。我猜想PyCharm就是用了这个方法来让用户方便导入模块的吧。

要让VsCode在执行是完成如上任务，需要添加如下配置。

首先在.vscode下的launch.json中添加"env": {"PYTHONPATH": "${workspaceRoot}"}。

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            // 省略其他配置
            "env": {
                "PYTHONPATH": "${workspaceRoot}"
            }
        }
    ]
}
```

然后在.vscode下的setting.json中添加如下配置。

```json
{
    // 省略其他配置
    "terminal.integrated.env.windows": {
        "PYTHONPATH": "${workspaceFolder};${env:PYTHONPATH}"
    }
}
```

你可以把这些配置添加到VsCode Profile的全局配置文件中来使这些配置对用该Profile打开的所有项目生效。（VsCode中的设置和插件都有Apply to All Profiles的选项。）

重启后，在项目根目录下的模块就能够被找到了。