title: 一步一步教你实现Periscope点赞效果
categories: Android
date: 2016-06-27 21:45:45
tags: [自定义View]
---


## 前言

现在视频应用越来越火,Periscope火起来后,国内也出现了不少跟风者,界面几乎跟Periscope一模一样.Periscope确实不错,点赞的效果也让人眼前一亮,很漂亮,于是乎,我就想着自己实现一下.

最终的效果图如下:

![最终效果图](http://ww2.sinaimg.cn/large/98900c07gw1f5lp94avvwg20el0gz146.gif)

录制的效果不太好,手机运行起来还是挺好看的.
不能说一模一样,但是也差不多了吧!~
<!-- more -->


惯例:
**通过本文你将学习到什么?**
1. 自定义view的一些基础方法以及一些注意点
2. 随机数的使用
3. 插补器的使用
3. **属性动画的高级用法**
4. **贝塞尔曲线在Android中的实现以及应用**

OK,如果感兴趣,那么跟着我来看看这个效果是如何实现的吧!

##仔细看看,这个效果中包含了什么细节?
1. 爱心出现在底部并且水平居中
2. 爱心的颜色/类型 随机
2. 爱心进入时候有一个缩放的动画
3. 缩放完毕后,开始变速向上移动,并且伴随alpha渐变效果
3. 爱心移动的轨迹光滑,是个曲线


**OK,接下去我们一个一个去实现它**

## 1.第一步,底部,水平居中
这个我相信大家很容易想到使用RelativeLayout,对,没错,那么我们先定义一个类姑且叫FavorLayout吧,继承自RelativeLayout,并且重载构造函数,并定义一些变量.
```
public class FavorLayout extends RelativeLayout{

private Random random = new Random();//用于实现随机功能

    private int dHeight;//爱心的高度
    private int dWidth;//爱心的宽度
private int mHeight;//FavorLayout的高度
    private int mWidth;//FavorLayout的宽度
public FavorLayout(Context context) {
        super(context);
    }
public FavorLayout(Context context, AttributeSet attrs) {

  super(context, attrs);
 //为了显示区域,我设置了一个背景颜色,随意
  setBackgroundColor(getResources().getColor(R.color.bg));
//init里做一些初始化变量的操作
  init();
}


//重写onMeasure 获取控件宽高
@Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        //注意!!  获取本身的宽高 需要在测量之后才有宽高
        mWidth = getMeasuredWidth();
        mHeight = getMeasuredHeight();
    }

}
```
定义完毕后我们来实现底部,水平居中:
```
//定义一个LayoutParams 用它来控制子view的位置
        private LayoutParams lp;

        //底部 并且 水平居中
        lp = new LayoutParams(dWidth, dHeight);
        lp.addRule(CENTER_HORIZONTAL, TRUE); //这里的TRUE 要注意 不是true
        lp.addRule(ALIGN_PARENT_BOTTOM, TRUE);
        //好了,之后只要给子view设置LayoutParams就可以实现了

```
 ## 2.接下来实现第二步,随机爱心
在Android实现随机数可以使用Random类,这里我只准备了3种不同的爱心,来看看代码如何实现吧:
```
//首先定义 3个代表不同爱心的drawable,以及他们的和drawables
    private Drawable red ;
    private Drawable yellow ;
    private Drawable blue ;
    private Drawable[] drawables ;

       //接下去我们初始化:
       //初始化显示的图片
        drawables = new Drawable[3];
        red = getResources().getDrawable(R.mipmap.red);
        yellow = getResources().getDrawable(R.mipmap.yellow);
        blue = getResources().getDrawable(R.mipmap.blue);
        //赋值给drawables
        drawables[0]=red;
        drawables[1]=yellow;
        drawables[2]=blue;

```
这样,下次取值时候只要使用如下代码,就能随机获取到爱心了:
```
drawables[random.nextInt(3)]//表示0-2的随机数,注意,3是取不到的,是个开区间
```

## 3.好了随机的爱心问题搞定,接下去让我们做缩放动画吧~
说到Android动画,我们以前常用Animation,它通常情况下能满足我们的需求,但是它的功能比较弱,并不是很好用.好在3.0后,强大的属性动画的出现,让动画在Android中实现起来变得非常容易.如果你还不知道属性动画怎么使用,赶紧去了解一下吧!
(以前在eoe上发过一篇帖子,很基础,[ObjectAnimator的运用](http://www.eoeandroid.com/thread-563698-1-1.html),有兴趣可以看下,提前说下,那是很久以前写的!)

废话不说了,那么具体怎么做呢,来看代码吧:
```
//我封装了一个方法  利用ObjectAnimator AnimatorSet来实现 alpha以及x,y轴的缩放功能
//target就是爱心
private AnimatorSet getEnterAnimtor(final View target) {

        ObjectAnimator alpha = ObjectAnimator.ofFloat(target,View.ALPHA, 0.2f, 1f);
        ObjectAnimator scaleX = ObjectAnimator.ofFloat(target,View.SCALE_X, 0.2f, 1f);
        ObjectAnimator scaleY = ObjectAnimator.ofFloat(target,View.SCALE_Y, 0.2f, 1f);
        AnimatorSet enter = new AnimatorSet();
        enter.setDuration(500);
        enter.setInterpolator(new LinearInterpolator());
        enter.playTogether(alpha,scaleX, scaleY);
        enter.setTarget(target);
        return enter;
    }
```
OK,缩放功能已经加了,第一个阶段也算是完成了,我们来看看效果吧:

偶,忘记说了,我们给外面的按钮设置点击事件,并且提供外部一个方法调用:
```
mBtnStartAnim.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mFavorLayout.addFavor();
            }
        });

public void addFavor() {

        ImageView imageView = new ImageView(getContext());
        //随机选一个
        imageView.setImageDrawable(drawables[random.nextInt(3)]);
        // 设置底部 水平居中
        imageView.setLayoutParams(lp);

        addView(imageView);
        Log.v(TAG, "add后子view数:"+getChildCount());

        Animator set = getEnterAnimtor(imageView);
        set.start();

}
```

好了这下可以运行看看效果啦:
![缩放效果图.gif](http://upload-images.jianshu.io/upload_images/531570-6b9b34c1061ba665.gif)

可以看到,爱心的颜色是随机的,并且也有了缩放动画.

到此,简单的功能我们已经完成了,那么接下去就是大头了.

## 4.实现贝塞尔曲线效果
我们怎么让爱心按照曲线移动?而且还有随机呢?

OK,接下去就是本文的主角贝塞尔曲线登场的时刻啦,这也是我实现这个效果学到的最重要的知识.

我是看这篇文章学习了解[贝塞尔曲线](http://blog.csdn.net/androidzhaoxiaogang/article/details/8680330)的,推荐一下.
简单来说,就是给定几个点,计算出一个曲线.(其实复杂得我看不懂)

简单了解贝塞尔曲线后,发现**三次方贝塞尔曲线
** 符合我们的要求
公式:

![公式.png](http://upload-images.jianshu.io/upload_images/531570-b905b01c6e5f63d6.png)

拿到了公式,先不要着急,我们先思考一下,明确一下它的参数是干什么的:
**公式中需要四个P,P0,是我们的起点,P3是终点,P1,P2是途径的两个点**
**而t则是我们的一个因子,取值范围是0-1,熟悉动画的同学应该就明白,0-1,对动画的作用有多么重大!!!所以看到这个,真是笑了~~**

因为需要自己实现贝塞尔,所以我想到了属性动画中的TypeEvaluator,它就是我们需要的.

好了,接下来,我们来实现吧~

```
//我们自定义一个BezierEvaluator 实现 TypeEvaluator
//由于我们view的移动需要控制x y 所以就传入PointF 作为参数,是不是感觉完全契合??
public class BezierEvaluator implements TypeEvaluator<PointF> {


    private PointF pointF1;//途径的两个点
    private PointF pointF2;
    public BezierEvaluator(PointF pointF1,PointF pointF2){
        this.pointF1 = pointF1;
        this.pointF2 = pointF2;
    }
    @Override
    public PointF evaluate(float time, PointF startValue,
                           PointF endValue) {

        float timeLeft = 1.0f - time;
        PointF point = new PointF();//结果

        PointF point0 = (PointF)startValue;//起点

        PointF point3 = (PointF)endValue;//终点
        //代入公式
        point.x = timeLeft * timeLeft * timeLeft * (point0.x)
                + 3 * timeLeft * timeLeft * time * (pointF1.x)
                + 3 * timeLeft * time * time * (pointF2.x)
                + time * time * time * (point3.x);

        point.y = timeLeft * timeLeft * timeLeft * (point0.y)
                + 3 * timeLeft * timeLeft * time * (pointF1.y)
                + 3 * timeLeft * time * time * (pointF2.y)
                + time * time * time * (point3.y);
        return point;
    }
}
```

到这一步,只要我们传入两个PonitF就能得到一个贝塞尔曲线了.接下来我们在FavorLayout中定义获取一个贝塞尔动画的方法:
```
private ValueAnimator getBezierValueAnimator(View target) {

        //初始化一个BezierEvaluator
        BezierEvaluator evaluator = new BezierEvaluator(getPointF(2),getPointF(1));

        //这里最好画个图 理解一下 传入了起点 和 终点
        ValueAnimator animator = ValueAnimator.ofObject(evaluator,new PointF((mWidth-dWidth)/2,mHeight-dHeight),new PointF(random.nextInt(getWidth()),0));//随机
        animator.addUpdateListener(new BezierListenr(target));
        animator.setTarget(target);
        animator.setDuration(3000);
        return animator;
    }

//这里涉及到另外一个方法:getPointF(),这个是我用来获取途径的两个点
// 这里的取值可以随意调整,调整到你希望的样子就好
/**
     * 获取中间的两个 点
     * @param scale
     */
    private PointF getPointF(int scale) {

        PointF pointF = new PointF();
        pointF.x = random.nextInt((mWidth - 100));//减去100 是为了控制 x轴活动范围,看效果 随意~~
        //再Y轴上 为了确保第二个点 在第一个点之上,我把Y分成了上下两半 这样动画效果好一些  也可以用其他方法
        pointF.y = random.nextInt((mHeight - 100))/scale;
        return pointF;
    }




```

眼尖的你一定发现了我给动画加了一个监听,**只有在回调里使用了计算的值,才能真正做到曲线运动**,否则没有效果哦:
```
private class BezierListenr implements ValueAnimator.AnimatorUpdateListener {

        private View target;

        public BezierListenr(View target) {
            this.target = target;
        }
        @Override
        public void onAnimationUpdate(ValueAnimator animation) {
            //这里获取到贝塞尔曲线计算出来的的x y值 赋值给view 这样就能让爱心随着曲线走啦
            PointF pointF = (PointF) animation.getAnimatedValue();
            target.setX(pointF.x);
            target.setY(pointF.y);
            // 这里偷个懒,顺便做一个alpha动画,这样alpha渐变也完成啦
            target.setAlpha(1-animation.getAnimatedFraction());
        }
    }
```

好了这个方法写完,让我们来测试一下效果如何吧!
对了,需要修改一下addFavor方法
```
public void addFavor() {

        ImageView imageView = new ImageView(getContext());
        //随机选一个
        imageView.setImageDrawable(drawables[random.nextInt(3)]);
        imageView.setLayoutParams(lp);
        addView(imageView);
        Log.v(TAG, "add后子view数:"+getChildCount());
        getBezierValueAnimator(imageView).start();
    }

```
好了,点击一下,看看效果吧!!!
![Bezier效果图.gif](http://upload-images.jianshu.io/upload_images/531570-bb9b19d2ace41d0a.gif)

怎么样?还不错吧???

缩放的效果我们做了,曲线效果也有了
那么接下去把他们连起来即可!!~

## 5.收尾,最终效果
##### 5.1 实现变速
```
// 我为了实现 变速效果 挑选了几种插补器
private Interpolator line = new LinearInterpolator();//线性
    private Interpolator acc = new AccelerateInterpolator();//加速
    private Interpolator dce = new DecelerateInterpolator();//减速
    private Interpolator accdec = new AccelerateDecelerateInterpolator();//先加速后减速
     // 在init中初始化
    private Interpolator[] interpolators ;

// 最终 init方法长这样:
private void init() {

        //初始化显示的图片
        drawables = new Drawable[3];
        red = getResources().getDrawable(R.mipmap.red);
        yellow = getResources().getDrawable(R.mipmap.yellow);
        blue = getResources().getDrawable(R.mipmap.blue);

        drawables[0]=red;
        drawables[1]=yellow;
        drawables[2]=blue;
        //获取图的宽高 用于后面的计算
        //注意 我这里3张图片的大小都是一样的,所以我只取了一个
        dHeight = red.getIntrinsicHeight();
        dWidth = red.getIntrinsicWidth();

        //底部 并且 水平居中
        lp = new LayoutParams(dWidth, dHeight);
        lp.addRule(CENTER_HORIZONTAL, TRUE);//这里的TRUE 要注意 不是true
        lp.addRule(ALIGN_PARENT_BOTTOM, TRUE);

        // 初始化插补器
        interpolators = new Interpolator[4];
        interpolators[0] = line;
        interpolators[1] = acc;
        interpolators[2] = dce;
        interpolators[3] = accdec;

    }

```

##### 5.2 合并前俩种动画,做成最终动画
```
private Animator getAnimator(View target){
        AnimatorSet set = getEnterAnimtor(target);

        ValueAnimator bezierValueAnimator = getBezierValueAnimator(target);

        AnimatorSet finalSet = new AnimatorSet();
        finalSet.playSequentially(set);
        finalSet.playSequentially(set, bezierValueAnimator);
        finalSet.setInterpolator(interpolators[random.nextInt(4)]);//实现随机变速
        finalSet.setTarget(target);
        return finalSet;
    }
```
##### 5.3 最后修改addFavor方法:
```
public void addFavor() {

        ImageView imageView = new ImageView(getContext());
        //随机选一个
        imageView.setImageDrawable(drawables[random.nextInt(3)]);
        imageView.setLayoutParams(lp);

        addView(imageView);
        Log.v(TAG, "add后子view数:"+getChildCount());

        Animator set = getAnimator(imageView);
        set.addListener(new AnimEndListener(imageView));
        set.start();

    }
```
在修改addFavor方法的同时我增加了一个监听,目的是为了在动画结束后,把爱心移除,不然,子view只增不减!!!!
```
private class AnimEndListener extends AnimatorListenerAdapter {
        private View target;

        public AnimEndListener(View target) {
            this.target = target;
        }
        @Override
        public void onAnimationEnd(Animator animation) {
            super.onAnimationEnd(animation);
            //因为不停的add 导致子view数量只增不减,所以在view动画结束后remove掉
            removeView((target));
            Log.v(TAG, "removeView后子view数:"+getChildCount());
        }
    }
```

## 6. 总结

呼!呼!~好了,最终的效果我们也实现了.

实际上,实现这个效果,我遇到了不少困难与问题,逐个击破后才迎来了最后的这个效果,收获颇丰.

希望大家看完后都能亲自动手去实现一下,相信你会学到更多东西!

写到这里,我发现,写文章比敲代码难多了啊!!!写文章花费的时间不少于我实现这个功能花费的时间!!!!真心的!!!!!(不知不觉,到端午了)

So,在这里无耻的请求大家,如果喜欢,请点赞,帮忙分享一下吧!~

欢迎关注我,第一时间获取新消息~

最后,祝大家端午快乐!

下次见~~

**更新**
源码已经上传至jcenter,集成超级方便--->[走起](https://github.com/AlanCheen/PeriscopeLayout)
