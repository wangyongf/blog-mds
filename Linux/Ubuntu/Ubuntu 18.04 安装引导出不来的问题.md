# Ubuntu 18.04 安装引导出不来的问题

## 1. 涉及到的软硬件环境

1. Ubuntu 18.04 待安装
2. [深度启动盘制作工具](https://www.deepin.org/original/deepin-boot-maker/)
3. Windows 10
4. AMD Ryzen 1700 CPU
5. 华硕 B350 Plus 主板
6. 闪迪 16G U 盘
7. 顺带说一句，我的电脑是 UEFI 启动的。。。

## 2. 操作简述

1. 刻录 U 盘启动盘
2. 设置成 U 盘启动
3. 开始安装 Ubuntu 18.04
4. 然后就「出问题了」。。。

## 3. 问题描述

系统本身使用 UEFI 启动，因此普通的启动盘制作工具都出现了各种各样的问题。

### 3.1 开始的坎坷

先后试过 UltraISO, Win32 Disk Imager。使用 UltraISO 刻录的启动盘直接就引导不了，根本进不了安装界面，pass。然后换成了 Win32 Disk Imager，刻录成功了，很正常的流程，然后开始重启引导安装。

问题来了，走到安装的最后一步的时候，系统提示我 grub 安装失败了，装失败了，失败了，败了，了。。。我心想，不就是 grub 安装失败吗，得嘞，简单，反正系统装好就行，大不了我再进 Windows10 系统用 EasyBCD 加一个引导不就行了。忘了说了，我装的是双系统（Windows 10+Ubuntu 18.04）。

安装系统时提示的 grub 安装失败错误信息：

```
GRUB installation failed
The grub-efi-amd64-signed' package failed to install into /target/. Without the GRUB boot loader, the installed system will not boot.
```

### 3.2 噩梦开始了

事实证明，事情一般都不会那么简单的，在系统装好以后，我重启系统，然后进入 Windows 10，打开了 EasyBCD，一顿操作行云流水，丝毫不带有任何拖沓，宛如一个老司机，新建了一个启动项，选的是「Linux/grub2」，新的引导取名就叫 Ubuntu 18.04。

添加完毕，保存设置，重启系统，果然，Ubuntu 18.04 引导出现了，我满怀喜悦之情，感觉自己仿佛成为了世界的主宰，鸡冻得选择了「Ubuntu 18.04」这个引导项。咦！怎么进了 grub4dos 界面？？？？瞬间，我整个人都不好了，满脸的黑人问号，哎哟我去，怎么是 grub4dos，这玩意儿以前可没有玩过，以前一直接触的都是 grub2。

不过转念一想，无所谓啦，反正都是 grub 开头的，估计差不多，老子当年大学学操作系统那会儿，不知道装了多少次系统，引导都搞坏了多少次，对 grub rescue 再熟悉不过了（PS：这时，我以为这次和进 grub rescue 完全没什么鸟区别）。打开百度搜索 grub rescue（PS：对的，我还在用百度搜索东西 →_→），找到一篇博客园的文章，点进去准备操作，我一般对博客园的文章比较有好感（相对 CSDN 而言）。

首先就是输入了 ls 命令，本以为会列出来各种分区，结果是下面这样的，呜呜呜。。。

```
Error 14: Filesystem compatibility error, cannot read whole file
```

咋和以前遇到的 grub rescue 不一样呢，老子不玩了，哼，开个玩笑，继续开搞。ε=(´ο ｀\*)))唉，既然出了问题，继续解决问题呗，兵来将挡水来土掩，我反手就是打开一个 Chrome 标签页，输入了上述错误内容，然后给我弹出了一堆搜索结果，然后，我在[这里](https://ubuntuforums.org/showthread.php?t=2358581)找到了一点儿信息：

```
Some of the filesystem reading code in GRUB has limits on the length of the files it can read. This error is returned when the user runs into such a limit.
```

WTF？元芳，你怎么看？属下觉得可能就是这东西引起的幺蛾子，使用的是 grub4dos 2014 版本，一看，果然，这个版本的 grub4dos 支持的硬盘容量有限，硬盘要是太大了？对不起，我选择罢工，比较尴尬的是，我的移动硬盘就是 1TB 的，人家消化不了，难怪报错了。

这可咋办，总不能把移动硬盘给拆成几瓣儿吧，这个 grub4dos 貌似是 EasyBCD 配置的（因为引导就是它配置的），不知道咋给它升级呀，烦人。

### 3.3 求生之路

求生之路开始了，我把 EasyBCD 当成了突破口，认为，系统已经安装成功了，剩下的就是引导的问题，那就使劲儿造呗。

首先，我又用 EasyBCD 建了一个启动项，选的是「Linux/Grub Legacy」，卒。

然后，我开始尝试「NeoGrub」，这东西没玩过，试试吧。于是使用 EasyBCD 安装了下「NeoGrub」，结果，这东西是需要配置的，点击编辑配置文件，规则不懂啊，打开官网，我的妈，还是看不懂，打退堂鼓了，自觉此路不通，卒。

话说，写到这里，想说一句，如果有看官大佬懂得使用 EasyBCD 的 NeoGrub 的，欢迎赐教，感激不尽（PS：不过，我怀疑会有看官么，我写在 [Gayhub](https://github.com/wangyongf/blog-mds) 上的文章估计没人能看到，哈哈哈 😁，自娱自乐罢了）。

### 3.4 使用 Ubuntu 18.04 LiveCD 尝试重建启动项

哎哟我滴妈，引导咋一直就出不来，这差不多万里长征到了最后一步死了，惨惨惨。悲痛之余，我开始思考，真的是引导的问题吗，按照常规操作一直有问题。我突然想起来，我特么是 AMD Ryzen 的 CPU，常规操作适用的貌似都是 Intel 牙膏厂的 CPU，可能是这个原因。好呀，既然如此，用 EasyBCD 建引导不行，我手动来还不行么。

说干就干，我打开 Ubuntu 18.04 的 LiveCD 系统（也就是那个 Try Ubuntu 的选项），打开了 GParted（为了看分区号），打开了终端（为了执行各种命令呀），然后打开在 Google 上找到的 [Ubuntu 官方 Grub 文档](https://help.ubuntu.com/community/Grub2/Installing)，开始执行各种命令，然鹅，并没有什么卵用，这些个命令执行都会有点儿问题。

### 3.5 曙光初现（启动项）

神啊，求求你收了我吧，我真的不想再装系统了，真的，这辈子都不想了。抬头看了看电脑右上角的时间，我滴妈，都已经凌晨两点半了，今晚还让我睡觉不，妈的，不让是吧，老子陪你耗到底，继续战斗！

我冷静地思考了一下，发现，之前自己都是在纠结引导建不好的问题，要不要换个思路呢？其实吧，我在几个小时前写启动盘遇到问题的时候就看过论坛里有个人说自己用[深度启动盘制作工具](https://www.deepin.org/original/deepin-boot-maker/)刻录的启动盘，然后全程一路绿灯，没有遇到任何问题地就把 Ubuntu 18.04 给安装好了，自己当时没当回事儿，因为自己那时用 Win32 Disk Imager 也把启动盘弄好了。这个时候我想，要不要试试深度启动盘制作工具？重新装一次说不定就好了。

然后，又是一顿操作，然后。。。竟然发现。。。也是一路绿灯！！！安装系统->安装引导->重启，没有任何阻碍，重启后，系统中出现了 Ubuntu 18.04 的启动项！！！除此之外，还有 Windows10 和 Deepin 15.7 的启动项，看来安装是没啥问题了。

其实吧，我本来是没想写这篇文章的，我弄到了这里以后，看到自己费尽千辛万苦终于把 Ubuntu 18.04 的系统启动项给鼓捣出来了，要是不写点儿文字下来，还真对不住自己，所以才有了这篇文章（PS：我这人可懒了，可不知怎的，突然想写点儿啥）。

### 3.6 柳暗花明

感觉这一路上就跟西天取经一样（膨胀了），历经各种磨难，搞得我都怀疑人生了，前面只是八十难，到了最后一站，还有个第八十一难。

一看启动项该有的都有了，想起 Windows10 上有无用的 Ubuntu 启动项，得，先去把它们删了吧，于是我抑制住自己的激动的心情，先行进入了 Windows10 系统，打开 EasyBCD 把之前加的那几个没卵用的启动项统统都给删了，留下了一个 Windows10 和 Deepin 15.7 的（Deepin 的这个只是出于情怀，实际上也没啥卵用）。

然后，我再次重启系统，选择进入了 Ubuntu 18.04 ，初次进入系统，可能需要配置一下，嗯，黑屏了一会儿，嗯，没事，我等一下就好了。。。一分钟过去了，两分钟过去了，五分钟过去了。。。屏幕还是黑屏，我开始方了，绝壁启动出问题了。好在老夫见多识广，这种情况也是见怪不怪了，尝试调出 tty 终端，`Ctrl+Alt+F1~F12` 挨个儿按一遍，到了 F6 的时候有反应了，进入了 tty6，让我登录，然后输入密码，登录成功了。

既然进了系统，说明离成功只剩最后一步之遥了：要把 GUI 界面给调出来。小事一桩，在 Google 上搜索了一下 `Ubuntu 18.04 tty`，果然出来个 [Ubuntu 18.04 no gui available only tty](https://stackoverflow.com/questions/50126179/after-upgrade-18-04-no-gui-available-only-tty) 的 StackOverflow 问答，点进去一看，好几个回答，都有赞，感觉应该稳了，依次输入如下命令：

```shell
sudo apt udpate
sudo apt upgrade
sudo apt install ubuntu-desktop
startx
```

一切 OK！进入系统了，分享一下进入系统的喜悦~

![喜悦]()

## 4. 后记

这次爬坑真的是让人胆战心惊，全程大约耗时 8 个小时，搞得我是心力交瘁哦，善哉善哉，老夫以后再也不乱搞了。但是话说回来了，绳命不息，折腾不止呀，哈哈哈~

其实我在整整 8 个小时的斗争过程中，遇到的问题远远不止上述这些，有一些小问题印象不深刻也就没写了，实际上呢，难受的一匹。

安装好 Ubuntu 18.04 以后，就要开始折腾它的 Mac 主题了，我真的是操碎了心呀~。关于 Ubuntu 18.04 如何定制 Mac 主题，我是参考的知乎这篇文章：[教程：为你的 linux 桌面带来 Mac OS Mojave 的体验](https://zhuanlan.zhihu.com/p/37852274)，如果以后在这个主题定制方面我积累了一些东西，也会单独开文章介绍的，这篇文章写到这里，也真是难为我了，真累。

## 5. 再说一句

其实在刻录启动盘的时候，还听说了一个工具，叫做 [Rufus](http://rufus.akeo.ie/)，不过因为做的时候它官网打不开，估计宕机了，所以没用这个工具，在我搞好了以后，官网又恢复访问了，神奇。

## 6. 参考资料

1. [after upgrade 18.04 no gui available only tty](https://stackoverflow.com/questions/50126179/after-upgrade-18-04-no-gui-available-only-tty)
2. [Rufus](http://rufus.akeo.ie/)
3. [Ubuntu Community Help Wiki-Grub2](https://help.ubuntu.com/community/Grub2)
4. [深度启动盘制作工具](https://www.deepin.org/original/deepin-boot-maker/)
5. [Failed to get canonical path of /cow](https://askubuntu.com/questions/254491/failed-to-get-canonical-path-of-cow)
6. [关于 U 盘安装 ubuntu-18.04 安装时候出现的 grub-efi-amd64-signed 的问题。](http://www.cnblogs.com/alonely/p/9222437.html)
7. [MBR Windows 10 + Debian 9.1 ext4](https://neosmart.net/forums/threads/mbr-windows-10-debian-9-1-ext4.15581/)
8. [Linux File Systems: Ext2 vs Ext3 vs Ext4](https://www.thegeekstuff.com/2011/05/ext2-ext3-ext4/?utm_source=feedburner)
9. [Dual boot 17.04 fresh install, stuck on grub prompt](https://ubuntuforums.org/showthread.php?t=2358581)
10. [教程：为你的 linux 桌面带来 Mac OS Mojave 的体验](https://zhuanlan.zhihu.com/p/37852274)
