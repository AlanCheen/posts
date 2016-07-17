title: InstalMaterial学习笔记之Reveal效果
categories: Android
date: 2016-06-27 21:47:53
tags: [自定义View]
---


## 前言

本文记录[开源项目 InstalMaterial](https://github.com/frogermcs/InstaMaterial)学习到的Reveal效果
国内有对应博客的[翻译](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0204/2415.html)

先看一下效果图:  
![最后效果图](/img/reveal.gif)

<!-- more -->


**惯例,不过这次是我学到了什么**
1.  对自定义属性使用属性动画
2. 属性动画,get方法不一定需要
2.  最重要的是ViewTreeObserver.OnPreDrawListener()的使用,另一种过渡动画的实现方式

OK,开始吧

##### 首先
仔细看动画效果,其实就是一个半径不断变大的圆

那么来实现这个效果吧

1. 先定义个类RevealView,重载构造方法
2. 增加一个成员变量 radius 表示圆的半径
3. 增加一个Paint变量 mPaint 用来画

如此后代码如下:

```

public RevealView(Context context) {
    super(context);
    init();
}

public RevealView(Context context, AttributeSet attrs) {
    super(context, attrs);
    init();
}

public RevealView(Context context, AttributeSet attrs, int defStyleAttr) {    super(context, attrs, defStyleAttr);
    init();
}

private void init() {
    mPaint=new Paint();
    mPaint.setColor(getResources().getColor(R.color.primary_dark)); 
mPaint.setAntiAlias(true);
    mPaint.setStyle(Paint.Style.FILL);
}
private Paint mPaint;
private int radius;

```


#####接下来,我们重写onDraw,画个圆


```
@Override
protected void onDraw(Canvas canvas) {    
super.onDraw(canvas);    
//原点是中心    
canvas.drawCircle(getWidth() / 2, getHeight() / 2, radius, mPaint);
}
```

####接下去就是让radius得值不断发生变化了
到这里,可能有同学会想着起个thread不停去赋值,再invalidate.
其实不必这么麻烦,我们使用属性动画即可:

```
//这里我计算了斜对角线的长度,这样可以保证画的圆能够保证覆盖整个view
int maxRadius = (int) (Math.sqrt(Math.pow(getHeight(), 2)+ Math.pow(getWidth(), 2)));
ObjectAnimator revealAnimator = ObjectAnimator.ofInt(this, "radius", 0,maxRadius).setDuration(300);
revealAnimator.setInterpolator(new AccelerateInterpolator());
revealAnimator.start();
```

到这里熟悉属性动画的同学可能就知道,还差了getter setter方法
**Tip:其实事实上getter方法不是必须的,少了setter方法也不会崩溃,只是动画没有效果而已**
注意:
```
//Notice 这里传入了两个值 如果只传了一个,则会在动画开始时候去调用gettter方法
ObjectAnimator.ofInt(this, "radius", 0,maxRadius).setDuration(300);
```

OK,让我们加入setter方法:
```
public void setRadius(int radius) {
    this.radius = radius;
    Log.d(TAG, "setRadius "+radius);
}
```

恩,OK,问自己一下,这样,就好了吗?
答案是,没有!
我们还需要对setter方法加工一下
```
public void setRadius(int radius) {
    this.radius = radius;
    Log.d(TAG, "setRadius "+radius);
    //Notice 调用invalidate 之后 onDraw才会被调用
    invalidate();
}
```

至此,我们运行一下:

![reveal_效果1阶段.gif](http://upload-images.jianshu.io/upload_images/531570-0c0d9cf4e335246a.gif)

初始效果已经出来了,但是你会发现,动画结束后,我们的revealview把其他View都挡住了.

呵呵,这个还不简单吗?动画结束后给gone了不得了嘛?

```
//新增一个callback 回调动画结束
public void setCallback(Callback callback) {
    this.callback = callback;
}
private Callback callback;
public interface Callback{
    void onRevealEnd();
}

//在activity里设置
mVReveal.setCallback(new RevealView.Callback() {
            @Override
            public void onRevealEnd() {
//隐藏
                mVReveal.setVisibility(View.GONE);
//                todo 其他事情
            }
        });

```

然后跑起来试试:
![第一阶段效果.gif](http://upload-images.jianshu.io/upload_images/531570-225b16ca746c58ae.gif)

还不错吧?

##接下来我们运用到Activity跳转
这个时候我们需要用到**ViewTreeObserver.OnPreDrawListener()**

```
//在跳转的activity里
mVReveal.getViewTreeObserver().addOnPreDrawListener(new ViewTreeObserver.OnPreDrawListener() {
    @Override
    public boolean onPreDraw() {
       //必须remove掉 不然会重复调用
        mVReveal.getViewTreeObserver().removeOnPreDrawListener(this);
        mVReveal.startReveal();
        return false;
    }});
```
//按钮点击事件
```
Intent intent = new Intent(this, RevealActivity.class);intent.putExtra("location", location);startActivity(intent);overridePendingTransition(0, 0);//Notice 这个绝对不能省..不然没有效果
```
好了,差不多介绍到这里..
不过其实还有其他的优化扩展,
1.比如传坐标,指定圆心开始reveal
2.reveal结束后继续其他的动画
大家自己试试吧:[Github](https://github.com/AlanCheen/PracticeDemo).

###最后总结下
1. InstalMaterial 是一个值得你认真去学习的开源项目.
2. 时间允许的话一定要动手实践,不要觉得看着简单就以为自己会了,其实没你想的那么简单.就像我,看着这个效果很简单,原本觉得不用这么麻烦就可以实现,结果,最后还是一步一步照着frogermcs的代码写了下去,这样才能真正学到东西.
3. 自学不易,请坚持!

