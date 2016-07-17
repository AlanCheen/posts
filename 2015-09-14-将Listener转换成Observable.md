title: (译)将Listener转换成Observable
categories: Java
date: 2015-09-14 09:42:09
tags: [RxJava,译]
---

在Java中使用listeners很常见.并且如果你使用RxJava,你会更乐意使用Observable替代listeners.  
但是如果你不得不处理一个你不能修改源代码的库呢?

    这里有一个简单的方法去创建一个围绕listener的Observable.

假设我们有个一接口`ValueUpdateListener`并且一个对象`ValueUpdater`将会调用我们的listener:
```Java
public interface ValueUpdateListener {

    void onValueChanged(@NonNull String value);

}

public class ValueUpdater {

    // in some point of the class...

    public void registerListener(ValueUpdateListener listener) {
        //...
    };

    public void unregisterListener(ValueUpdateListener listener) {
        //...
    };
}
```

我们可以创建一个Observable像这样:
     译者注:这里有些地方缺少了`final`关键字.

<!-- more -->

```Java
public Observable<String> observableListenerWrapper() {

    return Observable.create(new Observable.OnSubscribe<String>() {

        @Override
        public void call(Subscriber<? super String> subscriber) {
            ValueUpdateListener listener = new ValueUpdateListener() {

                @Override
                public void onValueChanged(@NonNull String value) {
                    if (subscriber.isUnsubscribed()) {
                        registerListener.unregisterListener(this);
                    } else {
                        subscriber.onNext(value);
                    }
                }
            };

            registerListener.registerListener(listener);
        }
    });
}
```
现在我们只需要去订阅:
```Java
observableListenerWrapper().subscribe(value -> {
    // do something with the new value
});
```

注意这个监听将会自动注销如果`onValueChanged`被调用并且这个observable取消订阅.

译者注:
其实有时候不需要`ValueUpdater`,更加简单,更多代码可以查看我的[PracticeDemo](https://github.com/AlanCheen/PracticeDemo/blob/master/PracticeDemos/lib/src/main/java/com/example/rx/ConvertListener.java)


阅读[原文](http://www.andreamaglie.com/rxjava-listener-to-observable/)
