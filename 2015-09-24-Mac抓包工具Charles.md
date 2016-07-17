title: Mac抓包工具Charles
categories: Tools
date: 2015-09-24 10:24:21
tags: [Tools,Charles]
---


## [Charles](http://www.charlesproxy.com/latest-release/download.do)

Mac下的抓包工具,去官网下载,安装.
默认的Charles每天用30分后就需要再关闭再打开.
破解实属无奈之举,[下载破解jar](http://download.csdn.net/detail/kamouswjw/8603053)
替换对应的jar就行.  
## 手机抓包
### 准备
确保手机和电脑用的同一个Wifi!

### 查看电脑ip

1. 用命令
```
ifconfig
```

![结果](http://ww3.sinaimg.cn/large/98900c07gw1ewdemig8h8j20ke0b7tcb.jpg)

2. 网络
打开网络设置

![效果](http://ww4.sinaimg.cn/large/98900c07gw1ewdenunr0fj20il05nq3q.jpg)

<!-- more -->

### 手机设代理
将手机网络代理到自己的电脑.
1. 打开使用的wifi设置,设置好代理,端口默认填写8888(可以在Charles里设置).
2. charles会弹个框,选择允许即可.
![弹框](http://ww3.sinaimg.cn/large/98900c07gw1ewdesd5ce6j20ea05r75e.jpg)

OK,可以快乐的抓包了.

## 参考
> http://www.testwo.com/blog/6343
> http://www.99css.com/1272/
