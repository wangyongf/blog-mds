# 在 Mac 上使用 Charles 进行手机的 HTTPS 抓包

## 1. 小米篇

### 1.1 环境

-   MacOS 10.13.3
-   Charles 4.0.2
-   小米 6 Android7.1.1 MIUI9.2

### 1.2 在 Mac 上安装好 Charles 的 https 证书

-   打开 Charles，`Help`->`SSL Proxying`->`Install Charles Root Certificate`
-   输入系统密码进行授权
-   在“钥匙串访问”中找到`Charles`的证书

![](http://qiniu.54yongf.com/2018-02-05-15178007592821.jpg)

-   双击 Charles 的证书，全部选择`始终信任`

![](http://qiniu.54yongf.com/2018-02-05-15178009726902.jpg)

-   至此，Mac 端根证书安装完毕

### 1.3 在小米 6 上安装 https 证书

-   小米 6 和 Mac 连上同一个 WiFi，然后小米 6 连上 Mac 的代理。[小米 6 如何连代理？](#how-to-proxy)
-   打开 Charles，`Help`->`SSL Proxying`->`Install Charles Root Certificate on a Mobile Device or Remote Browser`
-   按照弹窗的提示进行操作，小米 6 的代理已经开了，用小米 6 手机浏览器打开`chls.pro/ssl`下载证书，默认会保存到 MIUI 的系统下载目录(Download)
-   下载好之后通知栏会有一个提示，直接点击这个通知是安装不了的，会提示安装失败，这是 MIUI 系统的限制。你可以打开系统文件浏览器，找到`Download`文件夹，然后找到证书文件`getssl.crt`
-   点击证书文件，系统会提示你输入锁屏密码，（如果没有设置锁屏密码，可能会引导你到设置锁屏密码的页面，设置好之后再重来一次就行了）输入之后开始导入证书。先给证书命个名，比如叫`Charles`，凭据用途默认`VPN和应用`，点击确定，系统会提示你`已安装Charles`
-   小米 6 上安装 https 证书成功！

### 1.4 使用 Charles 进行 https 抓包

前戏已经做完了，该办正事儿了。

1. 小米 6 连上 Mac 代理，打开 Charles（再啰嗦一下下 0\_-）
2. 随便打开一个 App，比如小米系统浏览器，肯定有一堆请求，盯着 Charles 的请求监控面板，可以看到有很多的请求，随便选择一个 https 请求，可以看到，https 请求已经可以抓包了

![](http://7xo5vs.com1.z0.glb.clouddn.com/2018-02-05-15178116732643.jpg)

### 1.5 QA

Q: 安装了证书还是无法使用 Charles 对 https 请求进行抓包？
A: 可能是没有开启 Charles 的 SSL 抓包。Charles 菜单栏：Proxy->SSL Proxying Settings->Enable SSL Proxying 开启一下

![Enable SSL Proxying](http://7xo5vs.com1.z0.glb.clouddn.com/blog-mds/Enable%20SSL%20Proxying.png)

Q: 可能出现的`没有可安装的证书`
A: 在 1.3 中可能出现使用 crt 证书文件提示`没有可安装的证书`的问题，经过实践之后发现使用`.pem`证书可以正常安装，步骤如下：

1. 在 Mac 上打开 Charles，菜单栏：Help->SSL Proxying->Save Charles Root Certificate...
2. 保存文件为`.pem`后缀的证书文件，文件名随意
3. 将该文件传送到手机上，方式任选，这里我推荐[Send Anywhere](https://send-anywhere.com/)
4. 如果没有设置手机锁屏密码，先设一个吧，不然安装证书的时候会有问题！（让你输入凭据密码，但自己根本就没有设置过！）
5. 手机设置->更多设置->系统安全->从 SD 卡安装，选择`.pem`证书文件在手机上的存储位置，如果是使用 Send Anywhere 传输的文件，位置是：内部存储设备->SendAnywhere。找到文件之后选择确定
6. 为证书命名，随便吧，Charles。点击确定，系统会提示【已安装 Charles】
7. done!
8. 上述步骤在红米 Note4 MIUI9.2 上验证通过

## 2. OPPO 篇

### 2.1 环境

-   MacOS 10.14
-   Charles 4.2.7
-   OPPO R15 ColorOS V5.0, Android 8.1.0

### 2.2 在 Mac 上安装好 Charles 的 https 证书

Note: 这一步不做也行。如果需要操作，参考上面的「小米篇」即可

### 2.3 在 OPPO R15 上安装 https 证书

-   OPPO R15 和 Mac 连上同一个 热点，然后 OPPO R15 连上 Mac 的代理
-   打开 Charles，`Help`->`SSL Proxying`->`Save Charles Root Certificate`
-   导出证书文件为 `.crt` 格式，保存到电脑中
-   然后用某种方式把这个文件传到手机里，可以用 QQ，或者我这里安利一波 [Send Anywhere](http://send-anywhere.com)，如果是用 SendAnywhere，那么传过去的文件在 `/SendAnywhere` 目录下
-   打开设置，`WLAN`->`高级设置`->`从存储设备安装`，在手机文件中找到你传过来的证书文件
-   ~~（这一步可能要，没有走的话，如果出错了，可以走走这一步看看）。设置手机锁屏密码~~
-   点击证书文件，开始导入证书。先给证书命个名，比如叫`Charles`，凭据用途默认`VPN和应用`，点击确定，系统会提示你`已安装Charles`
-   OPPO R15 上安装 https 证书成功！

## 3. 后记

1. 如果有任何问题，欢迎在我的博客留言，或者在博客的 github 仓库中[提 issue](https://github.com/wangyongf/blog-mds/issues/new)
2. Android 手机厂商很多，各个型号手机的设置方法可能不尽相同，而且就算是同一个厂商的手机，不同系统版本间也可能有差异，而且就算是同一个系统版本，不同型号手机（比如小米和红米）间也可能有差异，这里点名一下小米，+\_+
3. 我会慢慢更新这篇文章，尽可能多覆盖各种情况
4. 未完待续......

## 4. 已验证列表

-   [ ] 小米
    -   [x] 小米 6
-   [ ] OPPO
    -   [x] OPPO R15
-   [ ] ...

## 5. PS

### 5.1 小米手机如何连代理？

-   首先找到 Mac 的局域网 ip，在终端上`ifconfig en0`，inet 后面的就是本机 ip
-   打开小米 6，`设置`->`WLAN`->点击已连接的 WiFi 右边的箭头->滑到最下面，选择代理`手动`->输入主机名，主机名就是上面 Mac 的 ip->输入端口号 8888->点击右上角的确定
-   小米 6 代理设置完毕
