title: 深入浅出设计模式
categories: Android
subtitle: "HeadFirstDesignPatterns"
date: 2016-07-20 22:20:17
tags: [设计模式]
---

## 前言

> 于 2016.8.3 更新
> 

今年的目标里有整理设计模式知识点,一直没有整理,最近效率还可以,希望能整理完.  

有部分内容(面向对象基础,设计原则)是一年多以前写的,那时候还非常稚嫩,还需慢慢加深理解。  

NOTE:**主要目的是给自己看,如果你想学设计模式,推荐看书,后面资料有给出.**  

笔记目录: 
- [面向对象基础-2015](http://yifeiyuan.me/2015/09/06/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E5%89%8D%E4%BC%A0%E4%B9%8B%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E5%9F%BA%E7%A1%80/)
- [设计原则(旧-2015版)](http://yifeiyuan.me/2015/09/06/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E5%BC%80%E7%AF%87%E4%B9%8B%E8%AE%BE%E8%AE%A1%E5%8E%9F%E5%88%99/)
- [设计原则(新-2016版)](http://yifeiyuan.me/2016/07/21/Design-Principles/)
- [UML 类图知识](http://yifeiyuan.me/2016/07/20/learn-uml/)
- [策略模式](http://yifeiyuan.me/2016/07/21/Strategy/)
- [观察这模式](http://yifeiyuan.me/2016/07/26/Observer/)
- [装饰者模式](http://yifeiyuan.me/2016/07/25/Decorator/)
- [工厂模式](http://yifeiyuan.me/2016/07/25/Factory/)
- [单例模式](http://yifeiyuan.me/2015/09/10/%E5%90%AC%E8%AF%B4%E6%AF%8F%E4%B8%AA%E4%BA%BA%E9%83%BD%E4%BC%9A%E5%86%99%E5%8D%95%E4%BE%8B/)
- [命令模式](http://yifeiyuan.me/2016/07/21/Command/)
- [适配器模式](http://yifeiyuan.me/2016/07/21/Adapter/)
- [外观模式](http://yifeiyuan.me/2016/07/25/Facade/)
- [模板方法模式](http://yifeiyuan.me/2016/07/26/Template-Method/)
- [迭代器模式](http://yifeiyuan.me/2016/07/26/Iterator/)
- [组合模式](http://yifeiyuan.me/2016/07/21/Composite/)
- [状态模式](http://yifeiyuan.me/2016/07/26/State/)
- [代理模式](http://yifeiyuan.me/2016/07/26/Proxy/)
- [复合模式](http://yifeiyuan.me/2016/07/25/Compound/)
- [桥接模式](http://yifeiyuan.me/2016/08/01/Bridge/)  
- [生成器模式](http://yifeiyuan.me/2016/08/01/Builder/)  
- [责任链模式](http://yifeiyuan.me/2016/08/01/Chain-of-Responsibility/)  
- [蝇量模式](http://yifeiyuan.me/2016/08/01/Flyweight/)  
- [解释器模式](http://yifeiyuan.me/2016/08/01/Interpreter/)  
- [中介者模式](http://yifeiyuan.me/2016/08/02/Mediator/)  
- [备忘录模式](http://yifeiyuan.me/2016/08/03/Memento/)  
- [原型模式](http://yifeiyuan.me/2016/08/03/Prototype/)  
- [访问者模式](http://yifeiyuan.me/2016/08/03/Visitor/)  


需要注意的是，从桥接模式开始,<<深入浅出设计模式>>一书对它们的讲解就非常少了（大概每个设计模式就只有两页的篇幅）,需要自己再买其他书看,学习.

另外这些读书笔记我在Github放了一份,跟其他书籍的读书笔记一起做成了一份Gitbook,阅读体验更好一些!--->[ReadingNotes](https://github.com/AlanCheen/ReadingNotes)

![设计模式整理](http://ww2.sinaimg.cn/large/98900c07jw1f66bcqb59jj20q10ct0vf.jpg)

## 感悟

1. 不需要严格遵照设计模式，可以适当调整以符合需求(模式是死的，人是活的)
2. 保持简单（KISS原则）
3. 设计、重构的时候考虑设计模式
4. 现在不需要，就别做 
5. 可能一直都不需要设计模式（helloworld就不要扯什么设计模式了）
6. 过度使用设计模式可能导致代码被过度工程化。应该总是用最简单的解决方案完成工作，并在真正需要模式的地方才使用它。 

实际上在开发过程中，自己已经不知不觉使用了不少设计模式，设计模式从实践中来，也特别需要在实践中去体会，不遇到困难，就不能真切深入的理解它。

有句话说的好，『没写过一万行代码，就别提什么设计模式』  

设计模式只停止于阅读是远远不够的，还需努力！  


## 设计模式推荐资料  
[HeadFirst设计模式](https://www.amazon.cn/Head-First%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F-%E5%BC%97%E9%87%8C%E6%9B%BC/dp/B0011FBU34/ref=sr_1_1?ie=UTF8&qid=1469026679&sr=8-1&keywords=%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BA%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)  
[设计模式：可复用面向对象软件的基础](https://www.amazon.cn/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6%E4%B8%9B%E4%B9%A6-%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F-%E5%8F%AF%E5%A4%8D%E7%94%A8%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E8%BD%AF%E4%BB%B6%E7%9A%84%E5%9F%BA%E7%A1%80-Erich-Gamma/dp/B001130JN8/ref=sr_1_1?ie=UTF8&qid=1469026646&sr=8-1&keywords=%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F+%E5%8F%AF%E5%A4%8D%E7%94%A8%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E8%BD%AF%E4%BB%B6%E7%9A%84%E5%9F%BA%E7%A1%80)  
[wiki:Design Patterns](https://en.wikipedia.org/wiki/Design_Patterns)  
[Android源码设计模式解析与实战](https://www.amazon.cn/Android%E6%BA%90%E7%A0%81%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E8%A7%A3%E6%9E%90%E4%B8%8E%E5%AE%9E%E6%88%98-%E4%BD%95%E7%BA%A2%E8%BE%89/dp/B0176QDPUW/ref=sr_1_1?s=books&ie=UTF8&qid=1469026788&sr=1-1&keywords=android+%E6%BA%90%E7%A0%81%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E8%A7%A3%E6%9E%90%E4%B8%8E%E5%AE%9E%E6%88%98)  
[EffectiveJava](https://www.amazon.cn/Sun-%E5%85%AC%E5%8F%B8%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%9B%E4%B9%A6-Effective-Java%E4%B8%AD%E6%96%87%E7%89%88-Joshua-Bloch/dp/B001PTGR52/ref=pd_sim_14_3?ie=UTF8&dpID=51bFt0sSAKL&dpSrc=sims&preST=_AC_UL160_SR121%2C160_&psc=1&refRID=2ZABEPEFGX2M1YM5HVZN)
  
