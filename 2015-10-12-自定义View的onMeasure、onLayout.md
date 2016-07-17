title: 自定义View的onMeasure、onLayout
categories: Android
date: 2015-10-12 09:43:50
tags: [自定义View,ViewGroup]
---

## 前言

自定义View有几个非常重要的流程:  
1. onFinishInflate()
2. onAttachedToWindow()
3. onMeasure(int widthMeasureSpec, int heightMeasureSpec)
4. onLayout(boolean changed, int l, int t, int r, int b)
5. onDraw(Canvas canvas)
6. onDetachedFromWindow()

这里来学习一下onMeasure(重点讲),onLayout(大致了解),另外这里也侧重[ViewGroup](http://developer.android.com/intl/zh-cn/reference/android/view/ViewGroup.html),因为vp比较难,如果把vp弄懂了,view应该也不在话下.

先讲几个知识点:  
1. onMeasure 负责测量大小,如果是View则测量自己,如果是ViewGroup则测量子View和自己.  
2. onMeasure 最终需要调用`setMeasuredDimension(int measuredWidth, int measuredHeight)`设置大小.
3. onMeasure后,严格来说是`setMeasuredDimension`调用后,可以通过`getMeasuredHeight()`,`getMeasuredWidth()`获得测量的宽高
4. onLayout 负责布局,即把子View放在哪里.  
5. onLayout 后可以调用`getWidth`,`getHeight`获取宽高,与之前的`getMeasuredXXX`不同,他们可能不相等.  
6. onMeasure onLayout 都可能执行很多次.

<!-- more -->

## [MeasureSpec](http://developer.android.com/intl/zh-cn/reference/android/view/View.MeasureSpec.html)

而测量我们需要`MeasureSpec`来帮助,它字面意思就是测量规则,它包括测量模式以及大小,它是一个32位的int值,它的`高2位`是测量的模式,`低30位`是测量的大小.

- 模式可以通过`MeasureSpec.getMode(int measureSpec)`获得
- 大小可以通过`MeasureSpec.getSize(int measureSpec)`获得

### 测量模式
模式有三种:  
1. MeasureSpec.EXACTLY
2. MeasureSpec.AT_MOST
3. MeasureSpec.UNSPECIFIED  

#### EXACTLY(精确模式)
```
* The parent has determined an exact size for the child. The child is going to be
* given those bounds regardless of how big it wants to be.
```
EXACTLY值为:0  

父View告诉你,你应该多少大小.  
当XMl里的宽高属性为`具体值`或者为`match_parent`,为EXACTLY.  

例:  
```xml
android:layout_width="200dp"
android:layout_height="match_parent"
```
#### AT_MOST(至多模式)
```
* The child can be as large as it wants up to the specified size.
```
AT_MOST值为:-2147483648

子控件大小最多为多少,在xml里配置的属性为`wrap_content`的时候.

**如果自定义View要支持wrap_content必须重写onMeasure,否则大小可能为0**

#### UNSPECIFIED(不指定模式)
```
* The parent has not imposed any constraint on the child. It can be whatever size
* it wants.
```
值为 1073741824
想多大多大,一般见不到,一般自定义View才用.

### 测试
新建一个ViewGroup,重写onMeasure并打印日志.
```Java
public class MyViewGroup extends ViewGroup {
  @Override
  protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
      super.onMeasure(widthMeasureSpec, heightMeasureSpec);
      logModeAndSize(widthMeasureSpec);
      logModeAndSize(heightMeasureSpec);
      Log.d(TAG, "onMeasure: height"+getMeasuredHeight()+";width:"+getMeasuredWidth());

  }     
}

//打印测量模式和大小
private void logModeAndSize(int measureSpec) {
    switch (MeasureSpec.getMode(measureSpec)) {
        case MeasureSpec.UNSPECIFIED:
            Log.d(TAG, "UNSPECIFIED: "+MeasureSpec.getSize(measureSpec));
            break;
        case MeasureSpec.AT_MOST:
            Log.d(TAG, "AT_MOST: "+MeasureSpec.getSize(measureSpec));
            break;
        case MeasureSpec.EXACTLY:
            Log.d(TAG, "EXACTLY: "+MeasureSpec.getSize(measureSpec));
            break;
    }
}

```

1. 测试`match_parent`和`wrap_content`
```xml
<yifeiyuan.practice.practicedemos.customview.MyViewGroup
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        />
```

得到日志:
```
D/MyViewGroup: EXACTLY: 1080
D/MyViewGroup: AT_MOST: 1692
D/MyViewGroup: onMeasure: height1692;width:1080
```
可以看到`match_parent`对应模式是EXACTLY
`wrap_content`对应模式是AT_MOST

顺带一提,如果是继承View,在这里的效果也是一样的.  

然而,如果继承LinearLayout,效果则不一样,可以看到测量后,高度为0了
```
D/MyViewGroup: EXACTLY: 1080
D/MyViewGroup: AT_MOST: 1692
D/MyViewGroup: onMeasure: height0;width:1080
```

2. 接下去测试具体值
```xml
<yifeiyuan.practice.practicedemos.customview.MyViewGroup
    android:layout_width="400dp"
    android:layout_height="wrap_content"
    android:background="#00ff00"
    />
```

log:
```
D/MyViewGroup: EXACTLY: 1200
D/MyViewGroup: AT_MOST: 1692
D/MyViewGroup: onMeasure: height1692;width:1200
```

可以看到具体值对应EXACTLY模式,这里View和ViewGroup也是一样.  

### 测量方法

知道测量的规则后,其实可以得出比较模板化的代码:

1. 适用于自定义View:  
```Java
private int measureWidth(int widthMeasureSpec){
    int result = 0;
    int size = MeasureSpec.getSize(widthMeasureSpec);
    int mode = MeasureSpec.getMode(widthMeasureSpec);
    if (mode == MeasureSpec.EXACTLY){
        result = size;
    }else{
        result = 100;// 实际上需要自己计算
        if (mode==MeasureSpec.AT_MOST){
            //至多模式,别超过了
            result = Math.min(result, size);
        }
    }
    return result;
}
```

2. ViewGroup
如果是自定义ViewGroup,那就各有不同了,每个ViewGroup都不一样,不过大致流程也差不多,就是测量子View再决定自己的大小.  

简单的例子如下,把所有子View的高度之和当做自己的高度:  
```java
int childcount = getChildCount();
int height = 0;
for (int i = 0; i < childcount; i++) {
    View child = getChildAt(i);
    child.measure(widthMeasureSpec, heightMeasureSpec);
    height += child.getMeasuredHeight();
}
setMeasuredDimension(MeasureSpec.getSize(widthMeasureSpec), height);
```

OK,差不多该知道的知识点也知道了,实践一下

## 实践

实现一个类似垂直的LinearLayout.  

### 配置xml
在xml里引用之前自定义的ViewGroup,并添加几个宽度高度背景色都不一样的View
包括`wrap_content`,`match_parent`,`xxxdp`,可见,不可见各种情况.
```xml
<yifeiyuan.practice.practicedemos.customview.MyViewGroup
    android:layout_width="300dp"
    android:layout_height="wrap_content"
    android:background="#0000ff"
    >

    <TextView
        android:id="@+id/tv1"
        android:layout_width="wrap_content"
        android:layout_height="60dp"
        android:background="#88ff33"
        android:text="我只是个TextView"
        android:textColor="#ffffff"
        android:textSize="20sp"
        tools:background="#333333"
        />


    <TextView
        android:id="@+id/tv2"
        android:layout_width="100dp"
        android:layout_height="wrap_content"
        android:background="#ff0000"
        android:text="Hello,ViewGroup"
        android:textColor="#ffffff"
        android:textSize="20sp"
        />

    <TextView
        android:id="@+id/tv3"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:background="#ee00ee"
        android:text="Hello,MatchParent"
        android:textColor="#ffffff"
        android:textSize="20sp"
        />

    <TextView
        android:id="@+id/tv4"
        android:layout_width="match_parent"
        android:layout_height="100dp"
        android:background="#44ff33"
        android:text="Hello,MatchParent"
        android:textColor="#ffffff"
        android:textSize="20sp"
        android:visibility="gone"
        />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="100dp"
        android:background="#ff00ff"
        android:text="Hello,MatchParent"
        android:textColor="#ffffff"
        android:textSize="20sp"
        android:visibility="invisible"
        />

    <View
        android:layout_width="50dp"
        android:layout_height="20dp"
        android:background="#000000"
        />

</yifeiyuan.practice.practicedemos.customview.MyViewGroup>
```
### 重写onMeasure

主要的思路是根据LayoutParams,给子View生成MeasureSpec规则,去测量各个子View的宽高,最终决定自己的宽高.  

需要注意的是:记得处理不可见的状态,因为`GONE`掉的View是没有宽高的,所以跳过它,提高效率.

Code:  
```Java
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {

    int height = 0;//group的计算高度
    int width = 0;//宽度

    int heightSize = MeasureSpec.getSize(heightMeasureSpec);
    int heightMode = MeasureSpec.getMode(heightMeasureSpec);

    int widthSize = MeasureSpec.getSize(widthMeasureSpec);
    int widthMode = MeasureSpec.getMode(widthMeasureSpec);

    int childcount = getChildCount();
    for (int i = 0; i < childcount; i++) {
        View child = getChildAt(i);
        //gone 的就无视掉
        if (child.getVisibility() == GONE) {
            continue;
        }
        LayoutParams lp = child.getLayoutParams();
        int widthSpec = 0;
        int heightSpec = 0;

        //根据LayoutParams,给子View生成MeasureSpec规则
        if (lp.width == LayoutParams.WRAP_CONTENT) {
            widthSpec = MeasureSpec.makeMeasureSpec(widthSize, MeasureSpec.AT_MOST);
        } else if (lp.width == LayoutParams.MATCH_PARENT) {
            widthSpec = MeasureSpec.makeMeasureSpec(widthSize, MeasureSpec.EXACTLY);
        } else {
            //其实xml里不会出现这样的情况
            widthSpec = MeasureSpec.makeMeasureSpec(lp.width, MeasureSpec.EXACTLY);
        }

        if (lp.height == LayoutParams.WRAP_CONTENT) {
            heightSpec = MeasureSpec.makeMeasureSpec(heightSize, MeasureSpec.AT_MOST);
        } else if (lp.height == LayoutParams.MATCH_PARENT) {
            heightSpec = MeasureSpec.makeMeasureSpec(heightSize, MeasureSpec.EXACTLY);
        } else {
            heightSpec = MeasureSpec.makeMeasureSpec(lp.height, MeasureSpec.EXACTLY);
        }

        child.measure(widthSpec, heightSpec);
        //把所有的子View的高度加起来,就是高度
        height += child.getMeasuredHeight();
        // 拿子View中的最大宽度当自己的宽度,保证所有子View能够显示全
        width = Math.max(width, child.getMeasuredWidth());
        Log.d(TAG, "onMeasure: i:" + i + ",width:" + child.getMeasuredWidth() + ",height:" + child.getMeasuredHeight());
    }

    // 再根据父view给自己的spec,处理自己的宽高
    // 这里没有显式处理Unspecified,其实已经计算了宽高,当做UNSPECIFIED的值了
    if (MeasureSpec.EXACTLY == widthMode) {
        width = widthSize;
    }else if (MeasureSpec.AT_MOST == widthMode) {
        width = Math.min(width, widthSize);
    }

    if (MeasureSpec.EXACTLY == heightMode) {
        height = heightSize;
    }else if (MeasureSpec.AT_MOST == heightMode) {
        height = Math.min(height, heightSize);
    }

    //一定要记得调用
    setMeasuredDimension(width, height);

    Log.d(TAG, "onMeasure: height" + getMeasuredHeight() + ";width:" + getMeasuredWidth());

}
```

注意:  
1. 要牢记测量模式与xml属性的对应关系  
2. 处理View不可见的情况  

### onLayout

测量完毕后我们需要布局.
onLayout相对简单,只要记录一下总高度,挨个放就行:  

```Java
@Override
protected void onLayout(boolean changed, int l, int t, int r, int b) {
    int childcount = getChildCount();
    int height = 0;
    for (int i = 0; i < childcount; i++) {
        View child = getChildAt(i);
        if (child.getVisibility() == GONE) {
            continue;
        }
        child.layout(l,t+height,l+child.getMeasuredWidth(),t+height+child.getMeasuredHeight());
        height += child.getMeasuredHeight();
        Log.d(TAG, "onLayout: i:" + i + ",width:" + child.getMeasuredWidth() + ",height:" + child.getMeasuredHeight());
    }
}
```

因为我在onMeasure,onLayout都打印了日志,来看一次循环的日志:
```
D/MyViewGroup: onMeasure: i:0,width:486,height:180
D/MyViewGroup: onMeasure: i:1,width:300,height:152
D/MyViewGroup: onMeasure: i:2,width:900,height:600
D/MyViewGroup: onMeasure: i:4,width:900,height:300
D/MyViewGroup: onMeasure: i:5,width:150,height:60
D/MyViewGroup: onMeasure: height1292;width:900
D/MyViewGroup: onLayout: i:0,width:486,height:180
D/MyViewGroup: onLayout: i:1,width:300,height:152
D/MyViewGroup: onLayout: i:2,width:900,height:600
D/MyViewGroup: onLayout: i:4,width:900,height:300
D/MyViewGroup: onLayout: i:5,width:150,height:60
```

可以看到测量的效果还是符合实际情况的~  

run起来看看:
![效果图](http://ww1.sinaimg.cn/large/98900c07gw1ex3eatxf2nj208f0aiwes.jpg)

OK,挺好,跟预期效果一样~~

## 总结

这里已经讲了onMeasure,onLayout的用法,其实并不难,只是需要耐心,仔细.  

看完了,相信对大家掌握自定义ViewGroup也有所帮助.  
虽然没有处理padding,margin值,但是相信这些你可以搞定.  

## 推荐书籍
Android群英传
