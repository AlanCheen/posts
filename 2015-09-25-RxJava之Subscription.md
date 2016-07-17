title: RxJava之Subscription
categories: Java
date: 2015-09-25 09:57:33
tags: [RxJava]
---


## 前言

前面写过RxJava类似观察者模式,但是一直没提到RxJava如何取消订阅,今天就来学习一下.


## Subscription

RxJava中有个叫做`Subscription`的接口,可以用来取消订阅.  
```Java
public interface Subscription {
    /**
     * Stops the receipt of notifications on the {@link Subscriber} that was registered when this Subscription
     * was received.
     * <p>
     * This allows unregistering an {@link Subscriber} before it has finished receiving all events (i.e. before
     * onCompleted is called).
     */
    void unsubscribe();

    /**
     * Indicates whether this {@code Subscription} is currently unsubscribed.
     *
     * @return {@code true} if this {@code Subscription} is currently unsubscribed, {@code false} otherwise
     */
    boolean isUnsubscribed();
}
```
从上面可以看到,我们只需要调用`unsubscribe`就可以取消订阅,那么如何得到一个`Subscription`对象呢?

<!-- more -->

其实很简单:  
`Observable.subscribe()`方法可以返回一个`Subscription`的对象,即我们每次订阅都会返回.  
感觉`Subscription`就像一个订单,你下单了就会生成一个订单,而你也可以用这个订单取消订单.  

OK,内容其实不多,那么来练习一下吧.  

## 实战

我先写了以下代码来测试:  
```Java
Subscription subscription = Observable.just("Hello subscription")
        .subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                System.out.println(s);
            }
        });
System.out.println(subscription.isUnsubscribed());
subscription.unsubscribe();
System.out.println(subscription.isUnsubscribed());
```

在我想来输出的日志应该是这样的:
```
Hello subscription
false
true
```
但是,结果出乎我的意料,我运行之后是这样的:  
```
Hello subscription
true
true
```
诶?不对啊,明明我没有取消订阅啊,怎么就true了呢?

<!-- more -->

接下去我进源码探索了一下发现:  
在`Observable.subscribe()`里有这么一段代码:  
```Java
if (!(subscriber instanceof SafeSubscriber)) {
    // assign to `observer` so we return the protected version
    subscriber = new SafeSubscriber<T>(subscriber);
}
```

它会把我们传递的`subscriber`转成`SafeSubscriber`,接下去跟进发现:  
```Java
public void onCompleted() {
    if (!done) {
        done = true;
        try {
            actual.onCompleted();
        } catch (Throwable e) {
            // we handle here instead of another method so we don't add stacks to the frame
            // which can prevent it from being able to handle StackOverflow
            Exceptions.throwIfFatal(e);
            // handle errors if the onCompleted implementation fails, not just if the Observable fails
            _onError(e);
        } finally {
            // auto-unsubscribe
            unsubscribe();
        }
    }
}
```
原来**它在finally里自动取消了订阅!!**

那么让onCompleted晚点执行就行了:
```
Subscription subscription = Observable.just("Hello subscription")
        .subscribeOn(Schedulers.newThread())//起线程
        .subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                try {
                    Thread.sleep(5000);//睡5秒,延迟一下
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(s);
            }
        });

//默认subscription 调用完
System.out.println(subscription.isUnsubscribed());
subscription.unsubscribe();
System.out.println(subscription.isUnsubscribed());
```

Log:
```
false
true
```
注意,取消订阅了之后`Hello subscription
`并不会打印,你想,你取消了订阅报纸,报社还会给你发报纸么?

好了关于`Subscription`暂时就探索到这里了.  
接下去就是项目实战了,有兴趣的可以关注一下.
