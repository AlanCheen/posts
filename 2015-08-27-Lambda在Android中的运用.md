title: 使用Lambda在Android中的替代匿名类
categories: Java
date: 2015-09-01 13:41:40
tags: [Lambda,Java8,Java]
---

## Lambda

Lambda是第十一个希腊字母,大写Λ，小写λ,额,跑题了...
Lambda表达式 是Java8的新特性之一:
1. Lambda表达式
2. 函数式接口
3. 流API
4. 默认方法
5. 新的Date 和 Time API

Lambda表达式**取代了匿名类**，取消了模板，允许用函数式风格编写代码。

由于最近接触了RxJava,遇到了Lambda,立马就喜欢上了~所以就学习了一下.  

本文主要介绍一下Lambda在Android中替代匿名类的部分使用场景.  

<!-- more -->

## 在Android中使用Lambda

###  [gradle-retrolambda](https://github.com/evant/gradle-retrolambda)
> A gradle plugin for getting java lambda support in java 6, 7 and android

在Android中使用我们需要使用这个插件,按照配置配置完就行.

### 实战使用

由于Lambda 可以取代匿名类,所以我们先来挑几个匿名类练练手.  

这里先简单的提及一下Lambda的语法:`() -> {}`
咋一看可能觉得这个什么鬼啊?看不懂啊!  
不急,看几个例子先~

#### 设置监听事件

Android中我们设置监听一般这么写:
```Java
mFab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                doSomething();
            }
        });
```

这么写AS会提示说匿名类`new View.OnClickListener()`能被Lambda代替:
![提示](http://7xjgcv.com1.z0.glb.clouddn.com/lambda_1.png)

那么用Lambda可以如何写呢?

```Java
mFab.setOnClickListener(v->doSomething());
```
看到了么?
整个匿名内部类被`v->doSomething()`给代替了.  
是不是非常简单?

    Ps: 本例子中的`v`代表onClick传递的参数v,参数不能省略,但是可以随意命名.

#### 实现Runnable

在Android中我们起个线程一般这么写:
```
new Thread(new Runnable() {
            @Override
            public void run() {
                doSomething();
            }
        }).start();
```
而使用Lambda表达式的话可以这么写:
```Java
new Thread(() -> doSomething()).start();
```

另外实例化Runnable成员变量也可以这么写:

```Java
Runnable runnable = ()->doSomething();
```

看完这两个例子是否对Lambda感兴趣了呢?  
后续我还会继续学习,有兴趣的请继续关注~  

## 参考
> http://www.importnew.com/16436.html
> http://javarevisited.blogspot.sg/2014/02/10-example-of-lambda-expressions-in-java8.html
