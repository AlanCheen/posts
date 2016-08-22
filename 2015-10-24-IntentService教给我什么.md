title: IntentService教给我什么
categories: Android
date: 2015-10-24 16:55:33
tags: [IntentService,RTFSC]
---


## [IntentService](http://developer.android.com/intl/zh-cn/reference/android/app/IntentService.html)

前几天学习了Service,是时候来学习一下IntentService了.  

一如既往,先看下官网介绍:  
> IntentService is a base class for Services that handle asynchronous requests (expressed as Intents) on demand. Clients send requests through startService(Intent) calls; the service is started as needed, handles each Intent in turn using a worker thread, and stops itself when it runs out of work.
This "work queue processor" pattern is commonly used to offload tasks from an application's main thread. The IntentService class exists to simplify this pattern and take care of the mechanics. To use it, extend IntentService and implement onHandleIntent(Intent). IntentService will receive the Intents, launch a worker thread, and stop the service as appropriate.
All requests are handled on a single worker thread -- they may take as long as necessary (and will not block the application's main loop), but only one request will be processed at a time.

简单总结一下:  
service的子类,用于处理异步请求,并且在工作线程按顺序处理请求,工作完后自己停止.
使用也很简单,继承IntentService,实现`onHandleIntent(Intent)`方法即可.
特点:
1. 与Service不同,IntentService是起线程处理任务的
2. 自带队列,每个请求都会排队
3. 任务完成自己会调用`stopSelf`结束,无需我们操心
<!-- more -->

## 一探究竟

IntentService源码不多,截取了部分:
```Java
public abstract class IntentService extends Service {
    private volatile Looper mServiceLooper;
    private volatile ServiceHandler mServiceHandler;
    private String mName;
    private boolean mRedelivery;

    private final class ServiceHandler extends Handler {
        public ServiceHandler(Looper looper) {
            super(looper);
        }

        @Override
        public void handleMessage(Message msg) {
          //调用onHandleIntent
            onHandleIntent((Intent)msg.obj);
            // 处理完自己停止
            stopSelf(msg.arg1);
        }
    }

    public IntentService(String name) {
        super();
        mName = name;
    }

    public void setIntentRedelivery(boolean enabled) {
        mRedelivery = enabled;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        //开启个新的线程
        HandlerThread thread = new HandlerThread("IntentService[" + mName + "]");
        thread.start();
        //getLooper 来获取thread的looper
        mServiceLooper = thread.getLooper();
        //拿looper实例化 handler
        mServiceHandler = new ServiceHandler(mServiceLooper);
    }

    @Override
    public void onStart(Intent intent, int startId) {
        Message msg = mServiceHandler.obtainMessage();
        msg.arg1 = startId;
        msg.obj = intent;
        //封装intent的请求 用handler发送
        mServiceHandler.sendMessage(msg);
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        onStart(intent, startId);
        return mRedelivery ? START_REDELIVER_INTENT : START_NOT_STICKY;
    }

    @Override
    public void onDestroy() {
      //退出循环
        mServiceLooper.quit();
    }

    protected abstract void onHandleIntent(Intent intent);
}
```

成员变量不多,一个looper,一个handler,接下来分析一下:    
在onCreate里,实例化一个HandlerThread(继承自Thread,HandlerThread后面再说)并且启动线程,并且利用该HandlerThread线程的looper实例化handler.
onStart里接受的intent,封装成Message用handler发送,并在`handleMessage`中调用`onHandleIntent((Intent)msg.obj);`,再stopSelf.
另外在`onDestroy`里调用了looper.quit();退出looper.

这样intentService的机制原理也差不多看完了,其实不难,但是也能学到不少东西:  
1. `HandlerThread`类-->[官方资料](http://developer.android.com/intl/zh-cn/reference/android/os/HandlerThread.html)

> Handy class for starting a new thread that has a looper. The looper can then be used to create handler classes. Note that start() must still be called.

方便我们创建一个拥有looper的线程,可以用来创建handler.注意: start() 必须被调用!  

以前也不知道这个类,这次索性来看看HandlerThread的源码:
```Java
public class HandlerThread extends Thread {
    int mPriority;
    int mTid = -1;
    Looper mLooper;

    public HandlerThread(String name) {
        super(name);
        mPriority = Process.THREAD_PRIORITY_DEFAULT;
    }

    public HandlerThread(String name, int priority) {
        super(name);
        mPriority = priority;
    }

    protected void onLooperPrepared() {
    }

    @Override
    public void run() {
        mTid = Process.myTid();
        Looper.prepare();
        synchronized (this) {
            mLooper = Looper.myLooper();
            notifyAll();
        }
        Process.setThreadPriority(mPriority);
        onLooperPrepared();
        Looper.loop();
        mTid = -1;
    }

    /**
     * This method returns the Looper associated with this thread. If this thread not been started
     * or for any reason is isAlive() returns false, this method will return null. If this thread
     * has been started, this method will block until the looper has been initialized.  
     * @return The looper.
     */
    public Looper getLooper() {
        if (!isAlive()) {
            return null;
        }

        // If the thread has been started, wait until the looper has been created.
        synchronized (this) {
            while (isAlive() && mLooper == null) {
                try {
                    wait();
                } catch (InterruptedException e) {
                }
            }
        }
        return mLooper;
    }

    public boolean quit() {
        Looper looper = getLooper();
        if (looper != null) {
            looper.quit();
            return true;
        }
        return false;
    }

    public boolean quitSafely() {
        Looper looper = getLooper();
        if (looper != null) {
            looper.quitSafely();
            return true;
        }
        return false;
    }

    public int getThreadId() {
        return mTid;
    }
}
```

我们都知道在线程里实例化handler要调用`Looper.prepare();`和`Looper.loop();`,其实就是因为Looper
而HandlerThread有个成员变量mLooper,并且在run里面实例化了它,并且已经为我们封装好了Looper相关的方法~

注意`getLooper`方法,当mLooper为null时会一直wait,直到`run`方法里实例化mLooper并`notifyAll`,可以学习一下.

用法就拿一下IntentService里的代码吧:
```Java
HandlerThread thread = new HandlerThread("IntentService[" + mName + "]");
thread.start();
mServiceLooper = thread.getLooper();
mServiceHandler = new ServiceHandler(mServiceLooper);
```

2. Handler类的`handleMessage`方法所在的线程决定于它的Looper所在的线程,这个以前一直没留意.

## 总结

主要学习到了:
1. 可以利用Handler去维护一个队列,很简单
2. HandlerThread类
3. Handler的`handleMessage`方法所在的线程决定于它的Looper所在的线程

IntentService代码虽少,但是非常好.    
另外,Android还有很多很好的类需要我去发现,去学习!~  
又学了一招,哈哈!~  
