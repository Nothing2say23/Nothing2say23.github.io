---
title: Scoop 安装
author: Nothing2say
date: 2023-08-03 19:25:13
tags:
    - Scoop
    - Git
categories: 
    - 2023年8月日志
---
# Scoop 安装流程（更新中）

## 1. 设置一个安装目录：

{% asset_img image.png 显示img页面 %}

## 2. 安装前准备
Windows PowerShell的版本要求：**$psversiontable.psversion.major # should be >= 5.0**

查询版本号的方法：
>**先在PowerShell中输入**
>>$psversiontable 
>
>**再输入host或$host**
>>host
>
>**如下图**:

>>{% asset_img image-1.png 显示img-1页面 %}

## 3. 在PowerShell中设置Scoop安装权限
PowerShell 执行策略必须是以下策略之一：无限制、远程签名或绕过才能执行安装程序。例如：
>Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

## 4. 自定义安装
在所在的文件夹下，输入
>irm get.scoop.sh -outfile 'install.ps1'
>
>{% asset_img image-2.png 显示img-2页面 %}

当出现如下图的报错：
>{% asset_img image-3.png 显示img-3页面 %}

说明<font color=red>**接口方**</font>变更了<font color=red>**安全协议**</font>，而<font color=red>**客户端**</font>并未启用该协议。
解决方法：让客户端启用该协议。\
把所有可用的协议都启用，随你服务端将来怎么换。代码如下：
>  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Ssl3 -bor [Net.SecurityProtocolType]::Tls -bor [Net.SecurityProtocolType]::Tls11 -bor [Net.SecurityProtocolType]::Tls12

然后可以查看TLS协议是否都启用成功：
> [Net.ServicePointManager]:: SecurityProtocol
>
>{% asset_img image-4.png 显示img-4页面 %}

这时，安装根目录下应该有`install.ps1`:
>{% asset_img image-5.png 显示img-5页面 %}

说明`scoop`的安装脚本已经从服务器下载到本地，之后可按照不同的参数，执行如下代码进行安装：
> .\install.ps1 -?

例如：可以将 `scoop` 安装到自定义目录，配置 `scoop` 以将全局程序安装到自定义目录，并在安装过程中绕过系统代理。

>.\install.ps1 -ScoopDir 'D:\Applications\Scoop' -ScoopGlobalDir 'F:\GlobalScoopApps' -NoProxy

请注意，在PowerShell的管理员权限下执行时，需要在`.\install.ps1`后加入`-RunAsAdmin`，因此代码为：
>.\install.ps1 -RunAsAdmin -ScoopDir 'E:\Software\Scoop' -ScoopGlobalDir 'E:\Software\GlobalScoopApps' -NoProxy

或者可以在设置<font color=red>**用户环境**</font>和<font color=red>**全局环境**</font>后再进行安装，代码如下：
>$env:SCOOP='E:\Software\Scoop'
>
>$env:SCOOP_GLOBAL='E:\Software\GlobalScoopApps'
>[Environment]::SetEnvironmentVariable('SCOOP_GLOBAL', $env:SCOOP_GLOBAL, 'Machine')
>irm get.scoop.sh | iex
>
>如下图：
>>{% asset_img image-6.png 显示img-6页面 %}

说明安装成功，可在自定义目录下找到Scoop文件夹：
>{% asset_img image-7.png 显示img-7页面 %}

此时输入`scoop update`，可能会出现以下错误：
>{% asset_img image-8.png 显示img-8页面 %}

说明还需要安装`git`，按照提示执行：
>scoop install git\

会先提示安装`7zip`:
>{% asset_img image-9.png 显示img-9页面 %}

在从`main bucket`安装`git`时，中途连接断开，因而安装失败。\
解决方法：
>先找到所在的bucket的Main分支：
>>cd $env:SCOOP\buckets\Main
>
>然后在所在的Main分支目录下安装`git`:
>{% asset_img image-10.png 显示img-10页面 %}
>
>这里的安装失败是因为网络连接失败，因而可以尝试使用机场来连接。

重新连接后，安装`git`:
>{% asset_img image-11.png 显示img-11页面 %}

此时，`git`通过`scoop`安装成功。再次执行代码：
>scoop update
>
>{% asset_img image-12.png 显示img-12页面 %}

证明`scoop`已安装成功，且更新的镜像源已换为国内源

## 5.使用说明
简单解释下子目录中其他文件夹的含义：

* `apps`——所有通过`scoop`安装的软件都在里面。
* `buckets`——管理软件的仓库，用于记录哪些软件可以安装、更新等信息，默认添加main仓库，主要包含无需GUI的软件，可手动添加其他仓库或自建仓库，具体在推荐软件仓库中介绍。
* `cache`——软件下载后安装包暂存目录。
* `persit`——用于储存一些用户数据，不会随软件更新而替换。
* `shims`——用于软链接应用，使应用之间不会互相干扰，实际使用过程中无用户操作不必细究。
>{% asset_img image-14.png 显示img-14页面 %}

以下是一些常用的命令说明：

* `search`——搜索仓库中是否有相应软件。
* `install`——安装软件。
* `uninstall`——卸载软件。
* `update`——更新软件。可通过scoop update *更新所有已安装软件，或通过scoop update更新所有软件仓库资料及Scoop自身而不更新软件。
* `hold`——锁定软件阻止其更新。
* `info`——查询软件简要信息。
* `home`——打开浏览器进入软件官网。

如果忘记了，可通过输入`scoop help`来查询语法，以及更多不怎么常用的操作指导：
>{% asset_img image-15.png 显示img-15页面 %}

例如，可以通过`scoop bucket known`来查询安装的所有bucket：
>

## 6. 错误整理
当出现以下错误报告：
><font color=red>**XXX : 请求被中止: 未能创建 SSL/TLS 安全通道。**</font>\
>或者\
>{% asset_img image-13.png 显示img-13页面 %}

说明启用的TLS协议不够，则再次通过执行如下代码：
>  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Ssl3 -bor [Net.SecurityProtocolType]::Tls -bor [Net.SecurityProtocolType]::Tls11 -bor [Net.SecurityProtocolType]::Tls12

来启用所有TLS协议

## 7.相关工具对比
同类竞品对比：
1. Microsoft Store软件商店，含GUI的常规软件商店。
2. Chocolatey，另一个第三方命令行软件管理工具。
3. Winget，微软官方近两年的开源命令行软件管理工具。

|**评价指标**|**Microsoft Store**|**Chocolatey**|**Winget**|**Scoop**|
|-|:-:|:-:|:-:|:-:|
| GUI |&#x2714;|&#x2714;|&#x2716;|&#x2716;|
| 开源 |&#x2716;|&#x2716;|&#x2714;|&#x2714;|
| 软件搜索、安装、更新 |&#x2714;|&#x2714;|&#x2714;|&#x2714;|
| 已安装软件查看与卸载 |&#x2716;|&#x2714;|&#x2714;|&#x2714;|
| 回滚版本与锁定版本 |&#x2716;|&#x2714;|&#x2716;|&#x2714;|
| 导出与备份 |&#x2716;|&#x2714;|&#x2714;|&#x2714;|
| 数据管理与清理 |&#x2716;|&#x2716;|&#x2716;|&#x2714;|

[此对比属于主观评价](https://zhuanlan.zhihu.com/p/463284082)

* `GUI`——Windows 11的Microsoft Store颜值非常高，Chocolatey有一个不怎么好看的GUI，Scoop有第三方GUI。不过实际使用下来发现命令行一行代码的体验也不差，反而清晰明了更加高效，GUI在用户体验方面也不一定是优势。
* `开源`——我可太爱开源软件了，虽然开源软件一般不如商业软件稳定，但是我可以自己提需求甚至自己去改很是舒服。
* `软件搜索、安装、更新`——需求中基础的基础，不能实现的话也别用了。为了更好地使用最新版的软件，体验稳定的新功能，我可是积极的更新派，但是让我去使用不稳定的beta版本当测试就不是那么愿意了。
* `回滚版本与锁定版本`——并不是那么要紧的需求，但对于某些开发人员来说为了稳定就是非常重要的需求了。
* `导出与备份`——并不是那么要紧的需求，但对于某些人员来说快速安装系统是非常重要的需求。
* `数据管理与清理`——主要就是软件管理逻辑方面的需求了（强迫症需求），除了Scoop其他工具的数据管理一言难尽吧，虽然Scoop许多第三方仓库中的软件也没实现好。

<!-- <table>
    <tr>
        <th>评价指标 </th>
        <th>Microsoft Store </th>
        <th>pingjiazhibiao </th>
    </tr>
    <tr>
        <th>pingjiazhibiao </th>
        <th>pingjiazhibiao </th>
        <th>pingjiazhibiao </th>
    </tr>
    <tr>
        <th>pingjiazhibiao </th>
        <th>pingjiazhibiao </th>
        <th>pingjiazhibiao </th>
    </tr>
</table> -->

## 8. 社区提供的 bucket
**`extras`** 这个 bucket 是最有用，也是我们大部分人肯定会用到的仓库。除此之外，我们可以通过这个命令查看 Scoop 还能直接识别哪些 bucket：
>scoop bucket known

可以通过下面这个命令直接添加相应的 bucket：
>scoop bucket add <仓库名>

一些相关的仓库介绍：
* extras：就是我刚刚介绍的，Scoop 官方维护的一个仓库，涵盖了大部分因为种种原因不能被收录进主仓库的常用软件。地址： [lukesampson/scoop-extras](https://github.com/ScoopInstaller/Extras/tree/master)
* nirsoft：是一个 NirSoft 开发的小工具的安装合集。NirSoft 制作了大量的（dozens and dozens）小工具，包括系统工具、网络工具、密码恢复等等，孜孜不倦、持续更新。
    * Bucket 地址：[kodybrown/scoop-nirsoft](https://github.com/kodybrown/scoop-nirsoft)
    * NirSoft 官网地址：[NirSoft](http://www.nirsoft.net/)
* games：顾名思义，是游戏（和与游戏相关的工具）合集。包含了大量免费/开源的小游戏，地址：[Calinou/scoop-games](https://github.com/Calinou/scoop-games)

几个 bucket 都是 Scoop 官方维护认证的 bucket，当然我们也有很多由社区（用户）维护的 bucket。这里是一个按照 Github score（由 Star 数量、Fork 数量和 App 数量综合决定的 Github score）排列的 bucket 列表：[Scoop buckets by Github score](https://rasa.github.io/scoop-directory/search)
{% asset_img image-16.png 显示img-16页面 %}

搜索bucket的网站：<https://scoop.sh/#/>

## 9. 相关链接和参考
[Scoop 安装、配置国内镜像、备份与迁移 - FJianC的文章 - 知乎](https://zhuanlan.zhihu.com/p/594363658)

[Scoop——也许是Windows平台最好用的软件（包）管理器 - 爱丽丝的人偶一号的文章 - 知乎](https://zhuanlan.zhihu.com/p/463284082)

[scoopInstaller: scoop国内镜像优化库，能够加速scoop安装及bucket源文件，无需用户设置代理 (gitee.com)](https://gitee.com/glsnames/scoop-installer)

[给 Scoop 加上这些软件仓库，让它变成强大的 Windows 软件管理器](https://sspai.com/post/52710)

官网：<https://github.com/ScoopInstaller/Install.git>


