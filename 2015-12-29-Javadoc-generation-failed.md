title: Javadoc_generation_failed
categories: Android
date: 2015-12-29 19:24:23
tags: [jcenter,javadoc]
---



今天在升级Utils,把Utils升级,上传到JCenter的时候遇到了一个错误:

![Javadoc_generation_failed](http://ww1.sinaimg.cn/large/98900c07gw1ezgr025mh7j20oh04rmy2.jpg)

搜索后发现原因是代码里**包含了Html格式的代码**,我表示很疑惑,因为我并没有写过Html格式的代码呀,我写的是Java,怎么会有这个错误呢?  

然后百般寻找,发现格式化代码的时候,AS自动生成了一个`</p>`的标签,删除后就可以了!!!  

又长见识了!~~  
## 资料
	http://stackoverflow.com/questions/26552279/gradle-build-fails-with-javadoc-nullpointerexception
