title: Android给apk签名那点事
categories: Android
date: 2015-11-20 16:16:13
tags: [APK加固,签名]
---

## 前言
  前几天老大的6p一打开应用就奔溃,于是与让我给应用适配6.0.后来最后发布内测版本后,发现又是打开立即奔溃,在确定我代码push之后,找了一下原因,最终发现是梆梆加固搞的鬼.
  然后老大又让我去尝试其他加固方案,又因为加固后又需要重新加固,所以就有了这篇笔记.
<!-- more -->


## 签名

### 签名是为了什么

简单来说,签名可以保证我们的应用可以正常升级,并且不被别人覆盖.
算是一个标识.

### 工具
- keytool 是个密钥和证书管理工具,可以用来生成证书.  
- jarsigner 工具利用密钥仓库中的信息来产生或校验 Java 存档 (JAR) 文件的数字签名  


使用keytool生成证书:
``` Shell  
keytool -genkey -keystore test.keystore  -alias test -keyalg RSA -validity 10000
```

参数解释:    
1. -genkey     产生证书文件    
2. -keystore   指定密钥库的.keystore文件中   
3. -keyalg     指定密钥的算法,这里指定为RSA(非对称密钥算法)  
4. -validity   为证书有效天数，这里我们写的是10000天   
5. -alias      产生别名   

![效果图](http://ww1.sinaimg.cn/large/98900c07gw1ey7j7gp4sfj20l80ayjth.jpg)

***网上搜索到的生成证书的方法都尼玛是错的,都是误导人的,具体请看后续!!!***

另外keytool还能查看信息:

```
test  keytool -list -keystore test.keystore
```

结果:
```
➜  test  keytool -list -keystore test.keystore
输入密钥库口令:

密钥库类型: JKS
密钥库提供方: SUN

您的密钥库包含 1 个条目

test.keystore, 2015-11-20, PrivateKeyEntry,
证书指纹 (SHA1): 21:94:9F:48:7D:38:EE:5A:63:16:8F:46:1B:6E:73:89:53:7D:7B:5C
```

### 签名

可以使用jarsigner 来签名,例子如下:

```
jarsigner -verbose -keystore test.keystore -signedjar -signed.apk unsigned.apk 'test.keystore'
```

参数说明:
1. -verbose：指定生成详细输出  
2. -keystore：指定数字证书存储路径  
3. -signedjar：该选项的三个参数为  签名后的apk包  未签名的apk包  数字证书别名**(注意顺序)**

![效果](http://ww2.sinaimg.cn/large/98900c07gw1ey7kh4jhlej20la04c404.jpg)
(原来用的t.apk,发现不利于理解,于是后面改名了)

遇到坑了,找不到xxx证书链什么鬼!
后来搜索了很久之后才看到了一句*要跟别名一样*,我就在想我原来用的代码是这样的:
```
keytool -genkey -keystore test.keystore  -alias test.keystore -keyalg RSA -validity 10000
```
诶?好像没问题啊,test.keystore的别名test.keystore,是一样的啊,后来想想不对啊,test.keystore的后缀不应该加入上去吧,于是去掉keystore后再次重新生成一遍
```
keytool -genkey -keystore test.keystore  -alias test -keyalg RSA -validity 10000
```

然后再次执行签名:  
```
jarsigner -verbose -keystore test.keystore -signedjar signed.apk t.apk 'test'
```

成功了!~So happy!!~~  
![最终效果](http://ww4.sinaimg.cn/large/98900c07gw1ey7kwykdo9j2046042gls.jpg)

不过其实后面还顺带了一个警告:  
`未提供 -tsa 或 -tsacert, 此 jar 没有时间戳。如果没有时间戳, 则在签名者证书的到期日期 (2043-04-07) 或以后的任何撤销日期之后, 用户可能无法验证此 jar。`
想来似乎没什么,就没管了.  

吐个槽:**国内的有些文章真的很坑人,几篇错误的文章尼玛到处复制黏贴,到处都是,真是给跪了**


## debug.keystore的故事

说点小插曲,小知识:  

1. AndroidStudio会自动生成一个默认的`debug.keystore`,它存放在`~/.android/`目录下.
2. 如果一不小心删了(其实我是rm了它),只要重新run一遍debug版应(不指定signingConfig),AS会再次自动创建
3. 它默认密码为 android


## 小结
一个加固导致崩溃引发的血案~  
最终让我学会了如何用cmd来签名APK,来装逼,哦哈哈~~~
新技能get!~


## 后续--如何查看APK的签名呢?
 > update 2016-01-13

 将APK改成zip文件(其实APK就是带有签名的zip文件),然后解压,签名放在`META-INF`目录下名为`CERT.RSA`的文件里,通过命令行可以获取:


 ```
 keytool -printcert -file CERT.RSA
 ```

可以获得到信息如下:

![CERT.RSA-效果图](http://ww2.sinaimg.cn/large/98900c07gw1ezy269lh6xj20k705c3zu.jpg)
