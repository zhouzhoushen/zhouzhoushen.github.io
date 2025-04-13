---
date: '2024-04-13T23:38:32+08:00'
draft: false
title: '项目迁移'
tags: ['Deep Learning']
categories: ['Deep Learning']
summary: ""
math: true
---
笔者在学习深度学习的过程中，遇到一些本地计算资源不足以支持较复杂模型训练与推理的情况，因此想把本地的代码仓库转移到算力更强的远程服务器上。由于笔者经验不足，这个过程花了不少时间，笔者将转移的步骤记录在这里，当作总结回顾。

转移的步骤主要是：

* 本地仓库整理与仓库上传
* 仓库拉取与环境复现

### 本地仓库整理与仓库上传

首先要整理本地仓库，保证项目能跑，环境导出文件正确，文档说明完善。笔者的项目重度依赖conda虚拟环境中某个包的某个模块（一个模块就是一个.py文件），这个包在使用pip下载后，笔者对这个模块在本地做了一些修改。为了方便，笔者使用`pip show <package_name>`找到该包的路径，直接将该模块的.py文件复制到项目目录下。

这里有这么些关键点。

笔者使用VSCode作为集成开发环境，内嵌的命令行程序默认使用Git Bash，然而在使用`conda activate`切换虚拟环境时，VSCode内嵌的Git Bash不会切换对应的pip路径，也就是说，即使切换到其他虚拟环境，使用`which pip`检查当前环境变量中的pip路径时始终显示base环境的pip路径，这样的话，不管在什么虚拟环境下使用`pip install`，都只会将package安装到base环境中，且`pip show`并不能正常显示当前环境中已安装的包的信息，因为这个pip是base环境的。而笔者使用内嵌的cmd和power shell命令行程序并不会出现这种异常情况，所以笔者目前将默认命令行程序切换到了cmd。因此，在使用`conda activate`切换到某个虚拟环境时，在想用`pip install`安装package前，建议使用`which pip`检查下当前使用的pip是不是当前虚拟环境的pip。这些在切换到cmd程序后恢复正常。

笔者将模块文件复制到项目中时，VSCode的Python插件会自动refactor，包括一些重命名文件中模块名称的行为，在没有确定这是否是自己想要的逻辑前，笔者建议始终拒绝refactor，避免它将文件某些地方错改而引入难以察觉、难以恢复或难以纠正的问题。

模块移到项目中后需要检查代码中的import路径是否需要改变，在VSCode等IDE中，打开的项目根路径中的package能被项目中任何地方的文件找到，这可能是IDE有将当前项目根目录纳入PYTHON_PATH环境变量的缘故，而在远程服务器没有IDE只有vim编辑器的情况下，我们使用`python <file_name>`时，由于并没有IDE为我们维护“项目”这一概念，代码仓库子目录中的文件执行时看不到项目根目录下的package，需要将package转移到和所运行的文件同目录下，或者添加PYTHON_PATH环境变量，或者修改import路径。

接下来需要导出当前虚拟环境安装的package信息汇总便于仓库转移后环境复现，有三种方法。

一是使用conda导出对应的`.yml`文件，因为笔者的package都是用pip安装的，懒得确认conda是否都追踪了这些package，所以没有选择这种方法。

二是使用`pip freeze > requirements.txt`导出所有依赖包信息，这种方式生成的`requirements.txt`包含了许多默认的，固有的package，用`pip install -r requirements.txt`时会引入许多不必要的检索，不太推荐。

三是先使用`pip install pipreqs`安装`pipreqs`工具，再使用`pipreqs <project_path>`来生成`requirements.txt`。这个工具会在项目import行为分析的基础上生成`requirements.txt`，从而只把真正必要的包的信息保存下来，笔者选用了这种方法。

即使如此，使用`pip install requirements.txt`的过程大概率会报错，因为`requirements.txt`中有pip源中检索不到的版本，例如`torch==2.4.1+cu124`等，这时需要手动安装，一般在官网或者镜像站中获取数据源URL。还会有一些纯粹检索不到的版本号，例如`<package_name>=12.0.1`，而pip库中最新的版本只到10.5.0，这种情况会在pip的报错信息中有所表示，一般选择pip源中存有的最新版本即可。

需要注意的是，`pipreqs`工具在获取package信息时需要访问pip源，笔者将pip源配置为清华大学的镜像站，然而在运行命令时使用`set HTTPS_PROXY=<proxy_url:port>`将流量转到本地运行的VPN监听的端口，VPN中选的代理服务器在海外，导致执行`pipreqs`时始终报错，这困扰了笔者很长时间，后来发现是来自海外代理的请求被清华大学镜像站拒绝了，笔者将代理关闭后就能顺利拉取到package的信息了。

还有需要注意的一点是，笔者一开始没有注意到仓库中有一些较大的文件（>50MB），之前把它们用git追踪了后，即使后来将它们加到.gitignore中，由于之前的commit需要留存这些文件的副本，导致.git文件还是很大。笔者在git clone一个本该很小的仓库时发现了这一点，因为git clone时下载的时间很长，显示的仓库大小很大，但仓库中只有一些代码文件，后来发现之前该仓库track过一些大文件，即使后来删除这些大文件，或者添加到.gitignore中，由于先前的commit保存了副本，导致.git文件夹非常大。笔者最后使用`git checkout --orphan <branch_name>`的方式新建了一个空白分支，以清空commit历史，`git branch -D main`和`git branch -M main`将其转为主支，这样伴随先前的commit的历史副本就被删除了，后面git clone时显示的项目大小就是正常的了。

### 仓库拉取与环境复现

待本地仓库上传到GitHub等站点后，就可以在远程服务器上拉取并运行了，这一过程和[模型部署](模型部署.md)完全一致。

需要注意的有以下几点：

* 远程服务器只用CLI，没有IDE，需要在import项目内部的package是检查路径是否正确
* requirements.txt中的一些package需要手动安装，尽量保持版本一致
* 使用`conda activate`切换虚拟环境后，使用`which pip`检查pip是否和该环境对应
