# Android 代理自动配置 PAC 研究

## 环境

* 小米 6 MIUI9.2 Android7.1.1
* MacOS 10.13.3
* Charles 4.0.2

## 什么是 PAC

PAC，全称 Proxy Auto Config，中文名代理自动配置。PAC 类似于配置文件，通过这个配置文件，浏览器或者其他 UA 可以对每一个 url 配置代理服务，比如这个 url 要走这个代理，那类 url 要走那个代理等。

## 简单的 PAC 示例

最简单的 PAC 示例：

```javascript
function FindProxyForURL(url, host) {
    return 'PROXY 172.18.104.54:8888; DIRECT';
}
```

这个 PAC 配置很简单，就是指定所有流量一律走代理`172.18.104.54:8888`，如果代理挂了，则直接连接不走代理。

再来一个比较简单的 PAC：

```javascript
function FindProxyForURL(url, host) {
    if (host == 'www.mydomain.com') {
        return 'DIRECT';
    }

    return 'PROXY myproxy:80; PROXY myotherproxy:8080; DIRECT';
}
```

上面这个 PAC 脚本也比较简单，大概意思就是：`www.mydomain.com`的流量直接走，不走代理；`www.mydomain.com`以外的流量默认走代理，先走`myproxy:80`代理，如果超时那就再走`myotherproxy:8080`，如果这个还走不通？那对不起，老子不走代理了，直接裸连吧~

## 怎么写 PAC

### 0. PAC 的一些基础知识

PAC 本质上就是一文本文件，不过一般以`.pac`结尾，比如`proxy.pac`等。PAC 文件里面包含一个 JavaScript 函数`FindProxyForURL(url, host)`，函数返回一个字符串，这个字符串就是代理的配置。函数有两个参数：`url`和`host`，`url`是浏览的地址 url 全路径，`host`是这个 url 中的主机名部分。

整个 PAC 文件就包含`FindProxyForURL(url, host)`这一个函数，该函数的返回值可能有三种情况：

1. `DIRECT`，就是直接连接，不通过代理
2. `PROXY http://www.example.com:8080`，http 代理的主机和端口，指定代理服务器的地址和端口号
3. `SOCKS socks5sample.com:1080`，socks5 代理的主机和端口，主机也可以用 IP 表示

一个自动代理可以是多个选择的组合，各个选择间用分号`;`隔开，PAC 的容灾性是比较好的，因为你可以同时给一个 url 配置多个代理，以分号隔开，如果第一个代理挂了，会自动选择第二个；如果第二个挂了，则会继续自动选择第三个......

### 1. PAC 的一些函数

1. isPlainHostName(host)：判断是否是本地主机
2. dnsDomainIs(host,domain) localHostOrDomainIs(host, "")：判断访问主机是否属于某个域和某个域名
3. isInNet(host, subnet1, subnet2)：访问 IP 是否在某个子网内
4. shExpMatch(host, "")：主机名判断，正则
5. url.substring(0,n)：字符串截取
6. myIpAddress()：本地 IP

### 2. PAC 支持的函数列表

完整的 PAC 支持的函数列表，可以看这里：http://findproxyforurl.com/pac-functions/

## 如何在小米 6 上配置 PAC 实现 Charles 抓包

### 1. 实现简单的 PAC 脚本

我本机的 IP 是 172.18.104.54，然后我需要用 Charles 进行抓包，所以端口号是 8888，于是就有了下面这个三行的 PAC 脚本：

```javascript
function FindProxyForURL(url, host) {
    return 'PROXY 172.18.104.54:8888; DIRECT';
}
```

### 2. 上传脚本到公网服务器上

将该脚本另存为`proxy.pac`，上传到我的公网服务器上，可以通过`http://121.42.Xx.Xx:8080/proxy.pac`访问到

### 3. 配置小米 6 的 PAC

* 将小米 6 和 Mac 连上同一个 WiFi 热点，然后配置小米 6 的代理

![](http://7xo5vs.com1.z0.glb.clouddn.com/2018-02-26-15196349045958.jpg)

* 点击上图红圈中右边的箭头，进入配置页面

![](http://7xo5vs.com1.z0.glb.clouddn.com/2018-02-26-15196350551095.jpg)

* 点击上图红圈中的`代理`，选择代理自动配置

![](http://7xo5vs.com1.z0.glb.clouddn.com/2018-02-26-15196351647103.jpg)

* 输入 PAC 网址，也就是上文中的`http://121.42.Xx.Xx:8080/proxy.pac`，点击页面右上角的确定即可
* 最后，打开 Charles，小米 6 随便访问一下网络，比如打开浏览器啥的，这时候 Charles 可能会弹个窗，默认选中`Deny`，`Access`即可
* 代理自动配置成功，已经可以使用 Charles 进行抓包了

![](http://7xo5vs.com1.z0.glb.clouddn.com/2018-02-26-15196356523135.jpg)

## 展望

上文中，我们已经实现了代理自动配置，但是，如果每次都得这么弄，那不得累死？但是，既然这个 PAC 配置文件是一个远程的配置文件，就可以动态修改呀，只需配置一次自动代理，以后修改的事情通过修改 PAC 文件来实现。不过，不还是很麻烦么？我是这么想的，可以实现一个 Mac 的状态栏应用程序，轻松编辑之后，就会修改远端 PAC 配置文件的内容，这样就可以轻松编辑代理，也可以在多套代理配置之间进行切换，岂不美哉。当然这些都是后话，毕竟这个东西还只是个想法，暂时也没做出来呢~

## 最后但同等重要的是

1. 如果有任何问题，欢迎在我的博客留言，或者在博客的 github 仓库中[提 issue](https://github.com/wangyongf/blog-mds/issues/new)

## 参考资料

* [PAC 脚本的编写](http://blog.csdn.net/ljinddlj/article/details/1809493)
* [Wikipedia Proxy auto-config](https://en.wikipedia.org/wiki/Proxy_auto-config)
