title: Android中的IPC机制
categories: Android
date: 2015-12-03 10:43:29
tags: [Android开发艺术探索笔记,IPC]
---




## 前言

<!-- more -->


### 关于ShareUID的疑问
在Android的清单文件里可以给app配置`sharedUserId`属性

- `android:sharedUserId`

	The name of a Linux user ID that will be shared with other 	applications. By default, Android assigns each application 	its own unique user ID. However, if this attribute is set to 	the same value for two or more applications, they will all 	share the same ID — provided that they are also signed by the 	same certificate. Application with the same user ID can 	access each other's data and, if desired, run in the same 	process.




## 资料
[manifest-shareuid](http://developer.android.com/intl/zh-cn/guide/topics/manifest/manifest-element.html)
