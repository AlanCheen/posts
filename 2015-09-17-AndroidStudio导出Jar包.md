title: AndroidStudio导出Jar包
categories: Android
date: 2015-09-17 11:29:47
tags: [AndroidStudio,Gradle,Jar]
---


工具版本:  
Gradle 2.4  
AndroidStuido 1.4Beta3

在需要打jar包的Module下的`build.gradle`下配置如下:  

    PS:`rename`可以配置jar名字,`into`可以配置jar输出路径

```
task clearJar(type: Delete) {
    delete 'build/libs/yutils.jar'
}

task makeJar(type: Copy) {
    from('build/intermediates/bundles/release/')
    into('build/libs/')
    include('classes.jar')
    rename ('classes.jar', 'yutils.jar')
}

makeJar.dependsOn(clearJar, build)
```

执行命令:  
```Shell
./gradlew makeJar
```
这样在`build/libs/`下就能出现你所需要的jar包了.

![](http://ww2.sinaimg.cn/large/98900c07gw1ew5b02c58jj209e08ljs0.jpg)

参考
> [how-to-export-library-to-jar-in-android-studio](http://stackoverflow.com/questions/16763090/how-to-export-library-to-jar-in-android-studio)
