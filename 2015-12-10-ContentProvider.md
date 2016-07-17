title: ContentProvider的authorities属性
categories: Android
date: 2015-12-10 10:13:01
tags: [ContentProvider,authorities]
---

## ContentProvider

## authorities属性的唯一性

关于ContentProvider,一直都了解不多,最近遇到个问题.
我们两个App都用了一个第三方库,当我想同时安装我们的App时,遇到了一个错误:`INSTALL FAILED CONFLICTING PROVIDER`,一直安装失败.  

搜索了一遍后在SF上找到了[答案](http://stackoverflow.com/questions/16267785/install-shows-error-in-console-install-failed-conflicting-provider)

原来` android:authorities` must be unique.

	To avoid conflicts, authority names should use a Java-style naming convention (such ascom.example.provider.cartoonprovider). Typically, it's the name 	of the ContentProvider subclass that implements the provider


在xml里配置`authorities`属性必须唯一,所以当我们两个app配置得一样时,就会失败,于是修改一下这个属性即可.

## 资料
[manifest/provider-element.html](http://developer.android.com/intl/zh-cn/guide/topics/manifest/provider-element.html#auth)
