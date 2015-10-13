---
layout: post
title:  "Mac代理切换"
date:   2015-10-13
---

<p class="intro">
我平日里用Macbook 工作，每天离开公司后把电脑带回家，会使用goagentX的Shadowsocks 来自动翻墙，因为公司是自动翻墙，这样就有频繁切换代理的需求，然后就走上了代理切换的不归路。
</p>
##1.0 石器时代

最开始的时候，我是这么做的。
把公司的PAC代理文件就是 http://xxx/proxy.pac 复制到备忘录，然后每次来到公司，连上公司 wifi 进行切换的时候点开 system setting - network - advance - auto proxy 然后填入。
(请不要笑，事情总是要一步一步发展的。)

##2.0 铁器时代

后来，我意识到上面的做法太低效，所以引入(@zenhuang)[https://github.com/hzlzh]的办法 : 使用网络位置切换来切换代理。从此以后就过上了利用位置来切换代理的生活。


##3.0 工业时代

上面那个方法引发出了一个新的问题，使用 GoagentX 的自动PAC模式代理的时候会导致覆盖掉所有的auto pac设置，所以即使你的 location 切换回 office ， 也没有办法把公司的代理切回来(这里或许有其他的代理软件可以解决这个问题，但总体来说比较麻烦)。
所以引发出一个新的解决问题的思路，使用 Alfred 2 的 workflow来解决这个问题。

###3.1 编写一个 workflow 来解决这个问题

在 alfred 2 的 workflow 中新建一个 blank workflow 。 然后我们使用 keyword 来触发 workflow 的执行，这里我把 keyword 分别设置为 office 和 home。

![](../../assets/articleImage/2015-10-13-Mac-change-proxy/1.png)
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/2.png)

这里可以用 Applescript 来实现 ， 也可以用 Javascript 来实现 ，关于 Applescript 编程控制本机电脑可以参考 这篇文章 ，关于 Javascript 则可以参考 这篇文章，编写了两段简单的applescript来实现自己的目标：

![](../../assets/articleImage/2015-10-13-Mac-change-proxy/3.png)
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/4.png)

结果类似下面这样

![](../../assets/articleImage/2015-10-13-Mac-change-proxy/5.png)

引入workflow之后每天只需要一键即可切换代理，方便快捷，已经非常地方便。
虽然痛点已经非常小，小到可以接受的地步，毕竟每天只需要按几个键就可以切换。但，仍然有不足的地方，如果工作频繁要在 staff-wifi 和 office-wifi 下切换，有时候忘记切代理，还是觉得有小小的不便。

###3.2 后工业时代

由于 Alfred 2 不支持pac代理或者是端口，所以在 office-wifi 下如果我们要使用 Alfred 的其他功能需要把


##4.0 信息时代

其实从3.0~4.0只是非常小的一步，但却可以实现一键到无键的效果。实现无键的基本思路是依靠不同的网络作为判断的依据来切换代理配置。
我们借助一个工具叫做 controlPlane(http://www.controlplaneapp.com/) 。

> ControlPlane allows you to build configuration profiles, contexts in ControlPlane lingo, for your Mac based on where you are or what you are doing.  ControlPlane determines where you are or what you are doing based on a number of available evidence sources and then automatically reconfigures your Mac based on your preferences.  Evidence sources can include your current location, visible WiFi networks, attached USB devices, running applications and more.  You can even write your own evidence sources using shell scripts!

ControlPlane 可以让你的 Mac 根据你的位置等一系列状态来使得对应的配置生效，比如根据位置，根据USB设备，运行某一个APP或者是连接的 WIFI。

以下是使用指南:

1. Contexts tab 用于创建需要的情景模式，比如我这里需要的是home 和 office 两种不同情景模式下的代理配置，我就建立两个情景模式:
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/6.png)

2. Evidence Sounces tab用于定义触发来源，其中包括了激活程序，激活网络适配器，蓝牙，分配IP地址，DNS，Location等一系列齐全的监听源。这里我们需要根据wifi的来选择对应的配置，所以我们只需要监听Wifi网络对应的几个简单活动就行(监听得太多会消耗电脑性能，但是更重要的是，监听太多本身会混淆，使得结果可能适得其反，所以选择自己需要的就可以)：
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/7.png)

3. Rules 面板用于进一步定义监听源的监听规则，比如我们这里需要监听到Wifi的切换，在侦听到连入 Tencent-OfficeWiFi 的时候将情景模式换为 office (注意这里是前面定义的 context 情景模式而不是系统中的location)：
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/8.png)
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/9.png)

4. 最后在Actions tab下定义不同的情景模式下需要做什么事情即可，这里我写了两段用于切换代理和 GoAgentX 代理模式的 applescript（其实就是之前用于workflow那两段），并指定在不同模式下运行对应的文件即可：
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/10.png)
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/11.png)

ps:
关于更好的使用 office-wifi 来办公的一点补充:

alfred 2 的 workflow 是一个很强大的功能 ， 但很多功能依赖于网络，且 Alfred 并不支持 pac 或者 端口号，仅支持指定代理服务器。所以在 office-wifi 下需勾选使用系统的http代理设定
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/12.png)

并且在我们的系统代理设定中设定对应的 http 代理和 https 代理。
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/13.png)
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/14.png)

当然我们要求全自动化，所以上面这步其实是可以在我们编写的 applescript 里完成，
![](../../assets/articleImage/2015-10-13-Mac-change-proxy/15.png)

当然如果需要代理的软件太多 ， 可以直接使用 Proxifier 来解决更为优雅，Proxifier 的解决思路是收集软件调用到的底层命令，针对这些命令来使用智能代理的功能，有需要的可以直接下载Proxifier。
所以同理 Proxifier 不仅可以解决 Alfred 的网络问题，也可以解决 npm , brew , dropbox , wget , gem  等等。