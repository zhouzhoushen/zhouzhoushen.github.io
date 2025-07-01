---
date: '2024-04-13T23:38:32+08:00'
draft: false
title: '模型保存'
tags: ['Deep Learning']
categories: ['Deep Learning']
summary: ""
math: true
---

# 模型保存

为了项目协作以及分享，我们需要将模型（及其参数和数据集）保存到某些平台上。由于GitHub免费用户的LFS文件大小限制为2GB，对于一些较大的数据集或模型不够用，所以笔者选择将模型保存到[Hugging Face](https://huggingface.co)。

将模型妥善保存的步骤如下：

## 第一步

保证模型在本地能正常训练和推理，主要检查以下环境依赖。
* Python虚拟环境中的依赖齐全且不存在冲突。
* 项目代码中需要定制的的文件路径等配置正确。
* 模型参数能正确保存和载入。
* 数据集正确，可通过校验和验证其完整性。

## 第二步

使用pip/conda将虚拟环境的信息保存到requirements.txt或environment.yml文件中，确保虚拟环境可复现。

## 第三步

在README文件中作项目介绍，包括模型介绍、数据集介绍、评测结果、项目代码结构与复现方式、版权声明等。

## 第四步

使用git lfs标记项目中的大文件，其余操作和常规git操作相同。

由于项目中存在数据集、模型参数等大文件，需要使用`git lfs`工具来跟踪大文件。[Git LFS官网](https://git-lfs.com/)上有它安装和使用的详细说明。

安装完git lfs后，我们执行命令`pip install huggingface_hub`安装`huggingface-cli`命令行工具，然后去Hugging Face官网创建一个write token，然后执行`huggingface-cli login`并输入token值，以建立安全的传输通道。我们可以通过`huggingface-cli whoami`和`huggingface-cli env`来查看当前状态。当然，在本地创建SSH密钥对并将公钥上传到官网也可以实现认证。

根据Hugging Face官网上提出的一些issue，执行`git remote set-url origin https://<user_name>:<token>@huggingface.co/<repo_path>`可以避免一些认证失败问题，或者使用SSH协议对应的地址。

如果要创建新的模型项目，先执行`huggingface-cli repo create <model_name>`在官网上创建一个新的远程仓库，然后使用`git clone`克隆到本地，然后把本地的项目文件全部转移到其中。

如果要上传的文件大小大于5GB，需要执行`huggingface-cli lfs-enable-largefiles <./path/to/your/local_repo>`，否则会被服务器拒绝。

使用`git lfs track <file_name>`的方式跟踪项目中的大文件，这与直接修改项目中的.gitattributes文件等价，注意.gitattributes不能被.gitignore包括。如果大文件没有被git lfs跟踪，那么在push时可能会遭到服务器的拒绝。

这里需要注意，项目的.git文件中不能有大文件，也就是说，项目的commit历史中不允许有没被git lfs track的大文件。因为git为了提高性能，每一个项目版本都重新保存了一遍改动文件的副本，如果在某个大文件被git lfs track之前某次commit就包含了它，那么.git中对应那次commit的文件夹中就会保存一份当时commit的大文件副本，这样.git就会变得很大。所以，所有大文件在第一次被纳入某个版本前都应被git lfs track，不然.git中就会留有它的副本，这种情况下的push请求会被Hugging Face拒绝，此时只能调整commit历史或者删除.git重置仓库。

所以对于一个新项目或者有新纳入大文件的项目，在正式执行git指令前，应该执行`huggingface-cli lfs-enable-largefiles <./path/to/your/local_repo>`和`git lfs track <file_name>`保证所有大文件在commit前都处于被git lfs追踪的状态。

使用`git add`，`git commit`，`git push`完成项目上传。

**注意，Hugging Face官网在中国大陆境内访问受限，上述过程中所有与其交互的过程均需使用ProxyChains等工具代理流量。**

### 使用Hugging Face镜像站

一种解决国内访问Hugging Face受限问题的方式是使用镜像站，例如[HF-Mirror](https://hf-mirror.com/)。具体使用方式可以参见镜像网站上的说明。简单来说，可以通过`export HF_ENDPOINT=<url_of_mirror>`来修改huggingface-cli的数据源，也可以克隆镜像站的仓库避免科学上网。笔者目前只使用过镜像站来克隆仓库或下载文件，没有尝试过上传仓库（即将git remote指向的远程仓库置为镜像站的相应仓库），也许镜像站能在git push后提供中转功能。

### 问题遗留

笔者在往Hugging Face官网仓库push时遇到了一些问题，暂时没有push成功的过程。下面对笔者的操作流程进行叙述。

首先，笔者下载并安装并初始化了git lfs，然后下载了huggingface-cli并使用创建的write token登录，使用`huggingface-cli whoami`表明处于登录状态，笔者也上传了与本地机器上的密钥对应的公钥，使用`ssh -T git@hf.co`验证SSH认证没有问题。

笔者接着使用`huggingface-cli repo create`命令在官网创建了一个空仓库（只包含.gitattributes文件），然后使用ProxyChains代理git clone命令将仓库成功克隆到了本地，笔者接着将本地的一个项目的除.git外的所有文件转移到克隆下来的文件夹中，然后将这个新的文件夹作为项目的根目录。

笔者接着在项目根目录中执行`huggingface-cli lfs-enable-largefiles .`以表明这个项目中有大文件，然后笔者使用`git lfs track`跟踪了所有大于等于5MB的文件。

笔者配置了项目的本地配置，用户名、邮箱都与Hugging Face上的账号一致，git remote地址设置为`https://<user_name>:<token>@huggingface.co/<repo_path>`。

然后笔者使用git add/commit/push尝试上传项目。在git push时一直停留在Upload large file的进度条的零点，上传速度也显示为0。笔者多次执行git push命令，有时会输出`fatal: unable to access 'https://huggingface.co/<repo_path>/': gnutls_handshake() failed: Error in the pull function.`，有时就是卡在上述进度条起始处长时间没有进展。这里的git push通过ProxyChains代理流量，否则无法基于https协议与Hugging Face通信。

笔者能够确认这不是认证的问题，因为能够进入Upload large file的过程说明了认证成功。笔者将git remote地址中的token改错一位后再次执行git push，报错结果会明确指出认证失败，而不是停留在Upload large file的零点无法动弹。

理论上来说是否使用代理不会产生影响，为了验证这一点，笔者在`ssh -T git@hf.co`输出登录成功的情况下，将git remote远程仓库地址改为使用SSH协议访问的地址，即`git@hf.co:<repo_path>`，然后再执行git push，结果依然停留再Upload large file的进度条零点，无论等待多长时间。这表明问题的产生过程中使用代理服务器不是决定性因素。

问题也许源于笔者上述操作过程中的疏漏，但笔者反复检查和查阅常见教程、讨论与资料后并没有发现有误操作或漏操作，笔者没有详细浏览所有的错误相关讨论，也许问题出在笔者目前未知的位置；问题也许源于git lfs的传输端或Hugging Face官网的接受端，这没有明确的证据表征。

笔者最后尝试将git remote地址指向HF-Mirror的镜像仓库，因为服务器在国内，所以直接执行git push。这次Upload large files进度条正常推进，上传速度为正常的速度。但是在上传到某个特定进度时，上传速度会变为0，进度会卡住不动，等待一段时间后报错`error: failed to push some refs to <remote_repo_url>`。笔者尝试了多次，结果完全相同。

截至目前，笔者没有成功上传包含大文件的项目到Hugging Face的经历，也没有找出造成上述问题的原因。后续读者会继续阅读相关资讯，总结经验并继续做合理的尝试，如有新的进展，读者会在这里更新。

### 问题排查

为了细粒度地排查原因，笔者在保留本地仓库中所有文件内容不变的基础上，将代码版本回退到空仓库时期，即回滚一次版本，执行`git reset HEAD~`即可。此时目录中只有.gitattributes是被git追踪的，其余所有文件都处于没有被git add的状态。此时.gitattributes中已经记录了将要被track的大文件。（注意，大文件应该在第一次被add前就使用git lfs track标记。）

接着，笔者将所有不在.gitattributes中的文件，即仓库中所有不被视为大文件的文件，通过git add纳入git追踪范围，然后git commit，然后proxychains git push。这里push成功了，远程仓库成功被更新了。笔者接着直接在远程仓库上修改了某些文件并点击commit按钮实现远程仓库的更新，然后在本地通过proxychains git pull成功更新了本地仓库。

通过以上现象，可以认为，在git lfs没有真正track上大文件时，身份认证、网络、代理都不是问题的成因。

笔者接着使用git add添加了一个大文件。在这步前，执行`git lfs ls-files`没有输出内容，虽然.gitattributes已经指定了将被追踪的大文件，这些大文件还未被git add进来。执行git add 大文件后，执行`git lfs ls-files`就能看到实际被git lfs追踪的文件列表了。然后git commit + git push。此时，在执行git push时会报错`fatal: unable to access 'https://huggingface.co/<repo_path>/': gnutls_handshake() failed: Error in the pull function.`，或者始终停留在Upload large file的进度起始处不动。笔者连续执行多次，出现的现象就包括这两种。

笔者根据报错内容和相关关键字搜索，然后看到了[这篇文章](https://blog.csdn.net/inghoG/article/details/117927858)，问题的成因可能是传输双方的tls加密库不兼容

将git remote地址改为基于SSH协议的git@hf.co:<repo_path>是不可行的，因为当git lfs track上大文件后，执行git push时，小文件由git通过ssh协议传到远程仓库，大文件由git lfs通过https协议传到另一个地方。由于大文件传输的目标地址国内访问受限，需要代理，而proxychains不支持代理ssh协议的流量。所以不使用proxychains代理，git lfs上传大文件会失败，使用proxychains代理，会因为不支持git通过ssh协议传输小文件而报错。总之，当git lfs track上大文件时，将git remote设为SSH协议的目标地址无法解决问题。

而将git remote置为https协议对应的地址时，理论上执行proxychains git push时，所有流量都会被代理，不存在访问受限的问题，但是客观现象就是前述的进度卡在0处或者tls库不兼容。笔者接着使用`export GIT_TRACE=1 && export GIT_LFS_TRACE=1`来观察执行git push时的日志信息，观察到proxychains git push命令执行时打印的调试信息显示，在代理服务器向存放git lfs跟踪的大文件的服务器发送HTTP POST请求后会等待较长一段时间，最后显示超时，io timeout，笔者在git全局配置文件中设了超时阈值是60秒，在网络情况验证为良好，代理服务器验证为可用，上传文件大小并不大，约为几十MB的情况下，超时显然是反常的现象。笔者怀疑是git使用的gnutls库和服务器使用的加密库不兼容。

笔者通过如下步骤将git改为依赖openssl的版本，在这一过程中，git全局配置文件~/.gitconfig和项目配置文件.git/config都不会改变。这些步骤其实是GPT生成的，笔者亲测可行，笔者建议在执行这些命令时具体询问命令的作用及其参数的含义，有时需要自己调整参数。

1. 安装依赖项

```sh
sudo apt update
sudo apt install build-essential libssl-dev libcurl4-openssl-dev libexpat1-dev libz-dev tcl
```

2. 下载 Git 源代码

```sh
wget https://github.com/git/git/archive/refs/tags/v2.34.1.tar.gz
tar -xzvf v2.34.1.tar.gz
cd git-2.34.1
```

3. 配置编译选项

```sh
./configure --with-openssl=/usr
```

4. 编译Git

```sh
make -j$(nproc)
```

5. 安装Git

```sh
sudo make prefix=/usr install
```

6. 验证安装

```sh
git --version
git config --get http.sslBackend
```

可以手动在配置文件中修改http.sslBackend为openssl，然后在运行时git没有报错，就说明git确实使用了这个库进行加密。

此时，我们将git更换成了依赖openssl的版本，且其他所有环境配置都没有改变。然后笔者执行proxychains git push，主要会出现以下两种情况：

* OpenSSL SSL_connect: Connection reset by peer in connection to huggingface.co:443

这是由于网络波动造成的临时错误，可以忽略。笔者使用`proxychains curl https://huggingface.co`时也会遇到这种情况，多curl几次就能成功获取网页内容，说明这只是网络波动问题，是与push是否成功无关的因素。

* Upload large files卡在进度0处不动，上传速度显示0B/s，无论等待多久都不会变化。

笔者接着执行`export GIT-TRACE=1`和`export GIT-LFS-TRACE=1`以查看详细的运行调试输出信息，结果是卡在向存放被git lfs追踪的大文件的服务器发送HTTP POST请求后就不动了，应该是久久得不到应答。

笔者接着使用`git reset HEAD~`将仓库版本回滚到大文件没被add的状态，然后尝试新增一个小文件，使用git add，git commit，git push都没有问题，笔者然后尝试将一个不大的文件用git lfs track，具体来说就是先在.gitattributes中添加对应的项，然后再新建一个同名的小文件并使用git add纳入追踪，git commit纳入版本。这样这个实际上的小文件就被git lfs追踪了，在push时会被传到专门存放大文件的服务器上，笔者接着执行了git push，发现依然卡在Upload large files的进度零处，上传速度显示为0。笔者在前后多次使用proxychains curl https://www.huggingface.co验证网络以及代理服务器没有问题。笔者基于这个现象，认为代理服务器无法访问到Hugging Face用于存放大文件的服务器，就像国内无法访问Hugging Face。

最后，笔者将项目的git remote仓库修改为[HF-Mirror](https://hf-mirror.com)的对应镜像仓库，注意要在url中包含用户名和token，即`git remote set-url origin https://<user_name>:<token>@hf-mirror.com/<repo_path>`，由于其在国内，直接执行git push，发现大文件（实际上是一个测试小文件）能成功上传，并且在Hugging Face官网的仓库中也同步了更新，说明HF-Mirror会帮助我们将我们的push流量转发到Hugging Face官网。在修改git版本前，笔者也尝试过这种方式，但是没有成功，现象是Upload large files进度推进到一定程度时，上传速率就会降为0，然后很长一段时间内没有变化，最后报错`error: failed to push refs to <remote_repo>`，由于当时并没有报gnutls库相关的错误，笔者认为HF-Mirror的服务器的ssl加密库和gnutls是兼容的，当时错误发生的原因可能是，一次性上传的大文件数目过多（当时的总大小超过5G），本机（自己租的云服务器）因共享流量而常有网速波动（和同时在使用的用户数负相关，或者笔者在一定时间内传输流量太大而被调度器限制了），然后超时阈值较小，请求超时后就会输出error并终止传输进程。

后续笔者用git lfs track了真正的大文件，并能够使用git push，经HF-Mirror中转后，分别将流量送到小文件和大文件的远程仓库服务器。笔者也测试了在Hugging Face的远程仓库更新后使用git pull自动拉取合并，虽然本地仓库的git remote url指向HF-Mirror的镜像仓库，这也能成功，这是因为HF-Mirror和Hugging Face是实时同步的。

至此，笔者成功地将本地的模型及其使用的数据集和训练保持的参数存档上传到了Hugging Face官网，也成功地从Hugging Face官网拉取了数据。

## 总结

本次的任务是：将本地模型、数据集、参数检查点上传到Hugging Face的远程仓库上。

整体的流程在Hugging Face官网文档或者各大论坛上都有说明，具体工具的使用细节在工具官网上也有说明，遇到的问题也可以在各大论坛上找到相似案例并获取可能的解决方案。

在推进过程中遇到的关键点有：

* 大文件需要在首次被add前被git lfs track。
* 某些版本的git依赖gnutls库，可能与服务器的ssl库不兼容。
* 网络波动带来的噪声（一次性传输的文件量小点是可以成功的，但是一次性传输的文件量太大，加以网络波动、流量限制、远程服务器的接受量限制，导致最终结果的失败，会让错误的成因更加扑朔迷离，毕竟网络和代理在验证过程中都没有问题）。
* 代理服务器也无法访问远程服务器。

总结来说这是一个发现错误，错误成因的怀疑与验证，错误解决的循环过程。
