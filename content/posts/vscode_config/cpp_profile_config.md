---
date: '2024-04-13T23:38:32+08:00'
draft: false
title: 'VsCode C++ Profile配置'
tags: ['VsCode', 'Config']
categories: ['Config']
summary: ""
math: true
---

# 在VsCode上配置C++开发环境

## Step 1

下载c++开发软件包（例如mingw、clang等，要包括编译器、调试器等）以及cmake构建工具。

把它们加到环境变量中。

## Step 2

在vscode中创建纯净（即全是默认配置）的profile，下载microsoft的c++开发插件。

由于所需工具的路径已经添加到环境变量中，不必在插件设置中手动设置路径。

## Step 3

环境已经配置完成，你可以在.vscode文件夹中创建tasks.json与launch.json来指定编译与运行行为，你可以参考官方doc了解如何使用vscode中的cmake集成特性。

具体cmake怎么用，配置json文件怎么写，可以在google上搜索教程，如果你确实需要详细了解，请保持耐心，耐心阅读、思考和实践其实是最快的路径。

## 参考文献

[vscode官方文档](https://code.visualstudio.com/docs)