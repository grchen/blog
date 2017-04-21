---
title: Mac OSX虚拟化技术（1） - xhyve相关介绍
toc: true
date: 2017-04-15 00:37:12
tags: [内核, 虚拟化, MacOSX]
categories: 技术
description: Mac OSX虚拟化技术 - xhyve相关介绍，本篇纪录下前段时间自己折腾xhyve的一些纪录，可能对大家有点帮助。后面再搜集介绍下virtual box，Parralle Desktop 和 Vmware fusion等老牌的虚拟化产品。
---
## MacOSX操作系统介绍

{% asset_img Diagram_of_Mac_OS_X_architecture.svg OS X 体系结构图解 %}

OS X 是苹果公司 Mac OS 操作系统替代品的产物。 在多次失败的尝试之后，苹果于1994年启动了 Pink 项目（后来和 IBM 进行了合作），这就是 Taligent 和 Copland ，两年后这一项目取消。
通过收购获得了 NeXT 和其 NeXTSTEP 操作系统之后，苹果公司开始着手开发他们最新的操作系统 (Mac OS X)  OS X 首次出现是1999年的 OS X Server 1.0，第一个正式的 OS X 桌面版本发布于2001年3月24日。 从10.5版本开始，OS X 通过了 Open group Unix O3 单一 Unix 规范认证。
2016年6月，苹果公司宣布OS X更名为macOS，以便与苹果其他操作系统如iOS、watchOS和tvOS保持统一的命名风格。
Mac OS X 包含两个主要的部分：以FreeBSD源代码和Mach微核心为基础的 XNU 混合内核，并在 XNU 上构建的 Darwin 核心系统；及一个由苹果开发，称为 Aqua 的闭源、独占版权的图形用户界面。 细分的看，Mac OS X 系统可以分成五层结构，每一层有其代表性的技术。
https://zh.wikipedia.org/wiki/MacOS結構

## MacOSX虚拟化技术介绍
1. 从硬件角度来说，macOS的虚拟化也是Intel VT的技术实现，和windows下以及linux下没有本质区别。在10.10 (Yosemite)版本之前的虚拟化产品，都是通过自己来写内核扩展kernel extension (KEXT)来实现的，比如说Parralle Desktop和VMWare Fusion等产品。但是因为MacOS的内核很多代码并没有开放，对应第三方实现的虚拟化平台会存在性能以及兼容性等问题，没有Linux平台上的KVM／Xen等虚拟化平台使用起来顺畅。
2. MacOS从10.10 (Yosemite)版本开始，引入Hypervisor.Framework，从内核方面把虚拟化的功能进行封装，对用户来说，只需要在这个基础上进行二次开发，可以在用户态的层面使用虚拟化技术。所有后来就有了xhyve，一个轻量化的用户态的虚拟化框架。
3. 从这个角度来说，当前MacOS的虚拟化存在两种形式，一种是直接通过实现内核KEXT来实现的虚拟化技术，另外一种就是使用Hypervisor.Framework的虚拟化技术，当前比较热门的Docker for mac以及Veertu Desktop等产品都是用的后面一种，宣称是Native的虚拟化技术，性能好，兼容性好，使用方便，不会对MacOS本身造成破坏（因为是用户态的），下面会具体介绍。主要介绍新的Hypervisor.Framework技术。

## 几种虚拟化技术
### Hypervisor.Framework
{% asset_img Hypervisor.framework.png VM生命周期图 %}

这里是官方的介绍文档
https://developer.apple.com/reference/hypervisor

有一篇Veertu写的一篇具体流程的文章
https://veertu.com/uncovering-os-x-hypervisor-framework/

还有一篇写怎么利用这个技术来实现一个dos模拟器：
http://www.pagetable.com/?p=764

### xhyve - Hypervisor.Framework的具体实现
xhyve是一个Hypervisor.Framework的具体实现，是从FreeBSD的bhyve移植过来的。FreeBSD 下的虚拟技术 bhyve (The BSD Hypervisor) 是2014年1月份正式发布的，包含在了 FreeBSD 10.0 发行版中。 xhyve 是基于 bhyve 的 Mac OS X 移植版本。xhyve 超级小，只有 230 KB，不依赖其他软件或库。有兴趣大家可以玩一玩。

几篇介绍的文章：
http://www.pagetable.com/?p=831
http://www.vpsee.com/2015/06/mac-os-x-hypervisor-xhyve-based-on-bhyve/
上面的例子使用的是Ubuntu系统，本人按照对应的方法将Arch和Gentoo对应安装和运行玩了一遍。安装的时候采用CD启动，完成后需要修改sh脚本采用HD启动，具体的参数上面的文档种有详细说明。也可以参考github的具体描述：
https://github.com/mist64/xhyve
在工程对应test目录下有几个例子可以参考。


arch的安装和运行参考：
https://gist.github.com/lloeki/998675988a96ef286e0a

freebsd的安装和运行：
https://gist.github.com/tanb/f8fefa22332edc7a641d
https://gist.github.com/tanb/339f2ffb59281812d766

gentoo的安装和运行：
https://github.com/mofm/xhyve/tree/master/gentoo
（该github还有其他几个操作操作系统镜像具体运行脚本）

coreos的安装和运行：
https://coreos.com/blog/coreos-and-xhyve-tech-preview.html

*说明：* 初步安装问题不大，但是如果要升级内核，基本上都很复杂。需要在操作系统内将内核升级完毕后，在将内核文件和内核对应的ramdisk文件copy出来，替换以前的老版本。但是这样可能会启动不起来， 需要自己的编译内核以及调整启动参数，使内核对应驱动能够加载，还是非常麻烦的。本人在arch系统下升级内核成功，在gentoo上多次崩溃，后来使用veertu后，没有再继续探索下去。

*优缺点：* 优点是启动快，基本系统完备，可以替代其他商业虚拟机软件。缺点一个是需要本身对操作系统等有深入的了解，需要动手能力强的人来使用。另外镜像只支持RAW格式，内核升级比较麻烦。

#### xhyve和主机通讯的几种方法：
A. 使用linux的nc命令，上面的几个使用文档种有介绍
B. 使用nfs
http://hxzqlh.com/2016/05/20/NFS-让-Mac-OS-X-和-Linux-手牵手/
C. 使用samba：该方式已经比较成熟，不详细介绍
D. 使用sftp：搭建ssh服务器，在host上使用sftp连接上虚拟机，然后进行文件上传和下载。比如Atom编辑器通过Remote FTP插件可以完美的实现远程环境的编码和运行，参见：http://www.jianshu.com/p/da8951db17ca
本博客就是在MacOS上，用Atom连接到veertu上的arch虚拟机下写作的。在MacOS上写作，hexo的运行在veertu的arch虚拟机上（具体原因是因为MacOS上安装hexo总是报错，不想继续折腾这个事情，所以用虚拟机代替了）

### xhyve增强 - veertu desktop
本人使用xhyve后，发现还是有很大限制，操作起来比较繁琐（安装，升级操作系统），所以有找了以下，发现veertu desktop，是我需要的最佳选择。

{% asset_img hp-banner.png "Veertu desktop" %}

网站：https://veertu.com/
对应的技术介绍：https://veertu.com/technology/
相关的文章：https://veertu.com/knowledge-base/
使用报告：
http://www.waerfa.com/veertu-the-first-os-x-navie-virtualization-tools
说明：由于AppStore的原因，veertu已经可以从网站免费下载，不再需要解锁的费用。

### Docker for Mac - 已经切换使用xhyve
Docker是一种轻量化的虚拟化技术，在linux上内核原生支持，在windows上和MacOS上一般都是通过虚拟机技术来实现的。xhyve出现以后，Docker for Mac也从virtual box（Boot2Docker镜像）的虚拟机切换到xhyve虚拟机，大大提升了对应性能，以及用户体验。

{% asset_img docker-for-mac-and-toolbox.png "Docker for Mac 和 Docker toolbox" %}


https://docs.docker.com/docker-for-mac/
https://docs.docker.com/docker-for-mac/docker-toolbox/
中文版本的：
http://www.jianshu.com/p/893ff201d017
http://www.jianshu.com/p/ab6f8be85470

网络功能：
http://www.jianshu.com/p/b0e1f8af7ec2

安装加速器：
http://www.jianshu.com/p/b80a47d64d75

使用体验：
https://ipfans.github.io/2016/04/docker-for-mac-beta/

Docker开源组件：HyperKit、VPNKit和DataKit介绍
http://dockone.io/article/1329

*说明：* Docker for Mac确实比以前好用多了，本人体验后，还是发现一些问题：对应镜像大小增长比较快，而且当前社区没有解决这个bug，需要自己手工的处理。另外就是在docker上安装操作系统内核无法升级，毕竟是Docker。但是Docker的优势是部署和运行方便，这个也是Docker当前最大的发展方向，作为未来软件发布的主要渠道，他完美的解决了软件依赖和部署的问题。

镜像大小问题的一些解决方法参考：
https://forums.docker.com/t/consistently-out-of-disk-space-in-docker-beta/9438/9


## 参考连接
http://www.jianshu.com/p/f0f50d471312
https://veertu.com/uncovering-os-x-hypervisor-framework/
