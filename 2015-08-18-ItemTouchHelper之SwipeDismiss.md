title: ItemTouchHelper之SwipeDismiss
categories: Android
date: 2015-08-18 15:37:15
tags: [RecyclerView,ItemTouchHelper]
---


## [ItemTouchHelper](https://developer.android.com/intl/zh-cn/reference/android/support/v7/widget/helper/ItemTouchHelper.html)

> This is a utility class to add swipe to dismiss and drag & drop support to RecyclerView.

它是一个非常强大的工具,感觉跟ViewDragHelper一样,不过ItemTouchHelper顾名思义是处理item的touch事件的.  
它能够让你非常容易实现侧滑删除,拖拽的功能.

今天,就拿它来实现以下侧滑删除!~

## 开始

首先我们看下ItemTouchHelper的构造方法`ItemTouchHelper(Callback callback)` 它需要我们传入一个Callback  
而刚好Android非常任性,已经帮我们实现了一个:`SimpleCallback`

甚至,在注释里已经给我们写好了样例!!!有兴趣的同学可以去看看,这里就不给出了.

接下来上代码!~

<!-- more -->

### 定义一个Adapter

这个adapter没什么特别的,挺简单的,相信大家会

```
public static class ListAdapter extends RecyclerView.Adapter<ListAdapter.ViewHolder> implements View.OnClickListener{
        private Context mContext;
        private List<String>mDatas;
        public ListAdapter(Context context,List<String> data){
            mDatas= data;
            mContext = context;
        }
        @Override
        public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {

            View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.material_list_item, parent, false);
            return new ViewHolder(view);
        }

        @Override
        public void onBindViewHolder(ViewHolder holder, int position) {
            holder.text.setText(mDatas.get(position));

            holder.text.setTag(position);
            holder.text.setOnClickListener(this);
        }

        @Override
        public int getItemCount() {
            return mDatas.size();
        }

        @Override
        public void onClick(View v) {
            TextView tv = (TextView) v;
            Toast.makeText(mContext, tv.getText()+";;"+tv.getTag(), Toast.LENGTH_SHORT).show();
        }

        public static class ViewHolder extends RecyclerView.ViewHolder{

            TextView text;
            public ViewHolder(View view){
                super(view);
                text = (TextView) view.findViewById(R.id.iv_item);
            }
        }

    }
```

### 定义一个RecyclerView

设置为垂直list的样式,并且与上面的Adapter关联

```
mRvList.setLayoutManager(new LinearLayoutManager(getActivity(), LinearLayoutManager.VERTICAL, false));
mRvList.setAdapter(mAdapter);
```

### 实例化一个ItemTouchHelper

再实例化ItemTouchHelper之前我们再看一下SimpleCallback的构造方法:

`ItemTouchHelper.SimpleCallback(int dragDirs, int swipeDirs)`

- dragDirs 代表你想拖拽的方向
- swipeDirs 你想滑动的方向

都分别有一下值,很好理解:

1. LEFT
2. RIGHT
3. START
4. END
5. UP
6. DOWN

### 动手
```
// 0 代表我不拖拽, ItemTouchHelper.RIGHT代表我往右滑动

new ItemTouchHelper(new ItemTouchHelper.SimpleCallback(0, ItemTouchHelper.RIGHT) {

			//在这个回调 我们处理滑动
            @Override
            public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
                Log.d(TAG, "onSwiped() called with " + "viewHolder = [" + viewHolder + "], direction = [" + direction + "]");
                //这里我们通过viewHolder获取position
                int position = viewHolder.getAdapterPosition();
                mData.remove(position);
                mAdapter.notifyItemRemoved(position);
                Toast.makeText(getActivity(), "拆散的position:"+position, Toast.LENGTH_SHORT).show();
            }

			// 暂时不处理移动事件...
            @Override
            public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder target) {
                Log.d(TAG, "onMove() called with " + "recyclerView = [" + recyclerView + "], viewHolder = [" + viewHolder + "], target = [" + target + "]");
                return false;
            }

        }).attachToRecyclerView(mRvList);
```

## 大功告成

来看看效果图:

![效果图](http://7xjgcv.com1.z0.glb.clouddn.com/ezgif.com-add-text.gif)

由于写在七夕,所以开启了恶搞拆情侣模式,哈哈~~~

## 总结

ItemTouchHelper跟ViewDragHelper一样,给我们带来了极大的方便,不经想问,Android还有什么Helper也这么牛逼而鲜为人知?

## 预告

不出意外,下期实现拖拽功能,再见!~

完整的代码在我的Github上~


## 参考资料
1. [Drag and Swipe with RecyclerView](https://medium.com/@ipaulpro/drag-and-swipe-with-recyclerview-b9456d2b1aaf)--[泡网翻译](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0630/3123.html)
