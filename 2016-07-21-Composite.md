title: 组合模式
categories: Android
tags:
  - 设计模式
date: 2016-07-21 23:09:44
---

> 设计模式专题系列: [设计模式](http://yifeiyuan.me/2016/07/20/design-patterns/)

# 组合模式

组合(Composite)模式：**允许你将对象组合成树形结构来表现“整体/部分”层次结构。组合能让客户以一致的方式处理个别对象以及对象组合。**  

组合模式比较复杂一些，跟迭代器模式配合威力强大。

![类图](http://ww1.sinaimg.cn/large/98900c07jw1f62hwemqvcj20xc0ljdhf.jpg)

<!-- more -->

树形结构：  

![](http://ww4.sinaimg.cn/large/98900c07jw1f62nmm1sfrj209d08oq33.jpg)

带子元素的元素称为 『节点』(node)  

没有子元素的元素称为 『叶节点』(leaf)  

包含其他组件的组件为『组合对象』，没有包含其他组件的组件为『叶节点对象』

**任何一个『节点』和『叶节点』都是一种『组合』。**    


## 注意点

组合模式让我们能用树型方式创建对象的结构，树里面包含了组合以及个别的对象。  
使用组合结构，我们能把相同的操作应用在组合和个别对象上。换句话说，在大多数情况下，我们可以忽略对象组合和个别对象之间的差别。  


使用组合模式的时候，Component需要包含`Leaf`和`Composite`，以致于它们都会包含一些自己不需要的方法，所以可能在不需要的方法里`throw  UnsupportedOperationExecption` 或者 返回`null`or`false`。   

并且这么做，其实是不符合单一职责原则的，但是这么做能换来『透明性』(transparency): **通过让组件的接口同时包含一些管理子节点和页节点的操作，客户就可以将组合和叶节点一视同仁**。也就是说，一个元素究竟是组合还是叶节点，对客户是透明的。  

## 要点

1. 组合模式提供一个结构，可同时包含个别对象和组合对象。
2. 组合模式允许客户对个别对象以及组合对象一视同仁。
3. 组合结构内的任意对象成为组件，组件可以是组合，也可以是叶节点。
4. 实现组合模式时，有许多设计上的折中。你要根据需要平衡透明性和安全性。


## 小结

组合最大的优势是：**让客户端更加简单，客户端不需要再操心面对的是组合对象还是叶节点对象，所以不需要写一大堆if语句来保证他们对正确的对象调用了正确的方法。通常，他们只需要对整个结构调用一个方法并执行操作就可以了。**  