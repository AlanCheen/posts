title: Android MutilDex解决方案
categories: Android
date: 2016-01-06 17:01:58
tags: [Mutildex]
---

## 前言

最近也遇到了mutildex的问题,原因是一个dex.class不能超过65536,根据官网的指示[Building Apps with Over 65K Methods
](http://developer.android.com/intl/zh-cn/tools/building/multidex.html)
解决了,记录一下步骤


### step1
添加依赖 `compile 'com.android.support:multidex:1.0.0'`

### step2

配置app的`build.gradle`,新增`multiDexEnabled true`如下:   

```
 defaultConfig {
    multiDexEnabled true
 }
```
### step3

让App继承[MultiDexApplication](http://developer.android.com/intl/zh-cn/reference/android/support/multidex/MultiDexApplication.html)

### step4
重写App的`attachBaseContext`方法  
如下:  
```
 @Override
 protected void attachBaseContext(Context base) {
  super.attachBaseContext(base);
  MultiDex.install(this);
  }
```

## 获取当前项目的方法数
<!-- more -->

现在有个gradle插件可以检测当前项目所占的总方法数-->[dexcount](https://github.com/KeepSafe/dexcount-gradle-plugin),使用很简单.

### step1 配置app/build.gradle

```
buildscript {
    repositories {
        mavenCentral() // or jcenter()
    }
    dependencies {
        classpath 'com.getkeepsafe.dexcount:dexcount-gradle-plugin:0.3.1'
    }
}

apply plugin: 'com.getkeepsafe.dexcount'

```
### step2 build项目即可

编译项目,如` ./gradlew assembleDebug`  
就可以得知结果了:  
```
:app:assembleInternalDebug
Total methods in app-Internal-debug.apk: 58359
Total fields in app-Internal-debug.apk:  31843
Methods remaining in app-Internal-debug.apk: 7176
Fields remaining in app-Internal-debug.apk:  33692

BUILD SUCCESSFUL
```

### 后续

后来另外一个项目也到了需要使用mutildex的情况,然而当我配置完后,发现出现了另外的问题,`java.lang.OutOfMemoryError: GC overhead limit exceeded`,仔细检查,发现并没有配置错误,于是内心是崩溃的...  

搜索之后,发现这里有个讨论--->[stackoverflow](http://stackoverflow.com/questions/5839359/java-lang-outofmemoryerror-gc-overhead-limit-exceeded),最后添加了个配置到app/build.gradle文件即可

```
dexOptions {
        javaMaxHeapSize "4g"
    }
```

呵呵,又学了一招!~~


## More
1. 艺术探索中也有提到,可以看看  
2. [ Android 使用android-support-multidex解决Dex超出方法数的限制问题,让你的应用不再爆棚](http://blog.csdn.net/t12x3456/article/details/40837287)
