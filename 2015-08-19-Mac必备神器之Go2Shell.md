title: Mac必备神器之Go2Shell
categories: Tools
date: 2015-08-19 15:53:46
tags: [Tools]
---

## [Go2Shell](http://zipzapmac.com/Go2Shell)
一个可以快速在当前目录打开Shell的工具,小巧,非常好用!

但是它默认打开是*系统的iterm*,而我希望它打开**iTerm2**!~

## 设置默认iterm
而Go2Shell默认没有设置界面,需要用命令行打开(隐藏得真深啊!)  
在Shell中输入:  

```Shell
open -a Go2Shell --args config
```
会打开设置界面

![设置界面](http://7xjgcv.com1.z0.glb.clouddn.com/go2shell.png)

选择iTerm2 ,保存并退出即可!~

<!-- more -->

## 配合其他利器使用

Go2Shell还可以配合其他软件进一步提升效率,让你爽翻!~

### 配合Alfred

使用Alfred快捷打开,我这里输入2就可以快速打开

![效果图](http://7xjgcv.com1.z0.glb.clouddn.com/go2.png)

### 配合Finder

打开Finder,按住command键,拖动Go2Shell的图标到Finder菜单  
就可以在Finder快捷打开Go2Shell了

![效果图](http://7xjgcv.com1.z0.glb.clouddn.com/gotwoshell.png)

### 配合Manico
使用Manico给Go2Shell配置个快捷键,爽呆..

吐槽一下:
> 推荐在App Store下载旧版本,因为官方网站最新版本已经变了,打开Go2Shell变成了设置界面,虽然不用再输入命令打开设置界面了,但是,事实上这变得更加坑了!
 因为不能配合Alfred 和 Manico了!!!那还有个软用?
