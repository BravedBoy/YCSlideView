# YCSlideView

## 目录介绍
- 1.使用说明
- 2.功能说明
- 3.图片展示
- 4.其他介绍

## 1.使用说明
- **1.1 直接在项目build文件中添加库即可：compile 'cn.yc:YCSlideViewLib:1.1'**
- 关于具体的使用方法，可以直接参考代码

- **1.2 关于业务需求的来历**
- 产品提的需求：
- 1.2.1 要求在list页面，点击按钮，是list中的所有item的菜单控件从左向右滑出，然后再次点击按钮，则list中的所有item全部收缩。
- 1.2.2 点击list页面的任意item，则跳转到相应的页面；点击滑出的菜单控件，则删除该条item数据

- **1.3 在RecycleView的adapter中，如下所示**
```
public class ItemSlideAdapter extends RecyclerView.Adapter {

    private Activity activity;
	//创建帮助类
    private SlideHelper mISlideHelper = new SlideHelper();
    private List<YCBean> mData;
    private OnDeleteClickListener mDeleteListener;

    ItemSlideAdapter(Activity activity, OnDeleteClickListener mDeleteListener) {
        this.activity = activity;
        this.mDeleteListener = mDeleteListener;
    }

    public void setData(List<YCBean> mData) {
        this.mData = mData;
        notifyDataSetChanged();
    }

	//打开
    void slideOpen() {
        mISlideHelper.slideOpen();
    }

	//关闭
    void slideClose() {
        mISlideHelper.slideClose();
    }

    private HhItemClickListener mItemClickListener;
    void setOnItemClickListener(HhItemClickListener listener) {
        this.mItemClickListener = listener;
    }

    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View inflate = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_first, parent, false);
        OneSlideViewHolder oneSlideViewHolder = new OneSlideViewHolder(inflate, mItemClickListener, mDeleteListener);
		//oneSlideViewHolder的对象添加到mISlideHelper对象中
        mISlideHelper.add(oneSlideViewHolder);
        return oneSlideViewHolder;
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        ((OneSlideViewHolder) holder).bind(position);
    }

    @Override
    public int getItemCount() {
        return mData == null ? 0 : mData.size();
    }

	//注意，这个的ViewHolder一定要继承SlideViewHolder，并且实现必须的方法
    private class OneSlideViewHolder extends SlideViewHolder implements View.OnClickListener {

        private OnDeleteClickListener deleteListener;
        private HhItemClickListener mListener;

        private RelativeLayout rl_item;
        private FrameLayout fl_menu;
        private TextView item_tv_delete, tv_go_edit, tv_project_title;

        private OneSlideViewHolder(View view, final HhItemClickListener mItemClickListener, OnDeleteClickListener mDeleteListener) {
            super(view);
            this.mListener = mItemClickListener;
            this.deleteListener = mDeleteListener;

            rl_item = (RelativeLayout) view.findViewById(R.id.rl_item);
            fl_menu = (FrameLayout) view.findViewById(R.id.fl_menu);
            view.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    if (mListener != null) {
                        mListener.onItemClick(view, getAdapterPosition());
                    }
                }
            });

            item_tv_delete.setOnClickListener(this);
            tv_go_edit.setOnClickListener(this);
            fl_menu.setVisibility(View.GONE);
        }

        @Override
        public void doAnimationSet(int offset, float fraction) {
            rl_item.scrollTo(offset, 0);
        }

		//当关闭的时候，隐藏
        @Override
        public void onBindSlideClose(int state) {
            fl_menu.setVisibility(View.GONE);
        }
		
		//当打开的时候，展示
        @Override
        public void doAnimationSetOpen(int state) {
            fl_menu.setVisibility(View.VISIBLE);
        }

        void bind(int position) {
			//设置偏移量
            setOffset(70);
			//切花状态
            onBindSlide(rl_item);
            tv_project_title.setText(mData.get(position).getName());
        }

        @Override
        public void onClick(View view) {
            switch (view.getId()) {
                case R.id.item_tv_delete:
                    if (deleteListener != null) {
                        deleteListener.onEditDeleteClick(view, item_tv_delete, getAdapterPosition());
                    }
                    break;
                case R.id.tv_go_edit:

                    break;
                default:
                    break;
            }
        }
    }
}
```

- **1.4 当点击编辑按钮的时候，来回可切换状态**
```
if ("编辑".equals(tv_title_right.getText().toString())) {
                    tv_title_right.setText("取消编辑");
                    adapter.slideOpen();

                } else if ("取消编辑".equals(tv_title_right.getText().toString())) {
                    tv_title_right.setText("编辑");
                    adapter.slideClose();
                }
```
- 请参考代码，已经做出了很详细的注释

## 2.功能说明

## 3.图片展示
- 3.1 轮播图截图
- ![image](https://github.com/yangchong211/image/blob/master/image/slideview/ycslideview.gif)
- ![image](https://github.com/yangchong211/image/blob/master/image/slideview/state.gif)

## 4.其他介绍
**4.1版本更新说明**
- v1.0 将之前新芽项目的代码，抽取封装成库
- v1.1 去除了部分黄色警告

**4.2本人写的综合案例**
- [案例](https://github.com/yangchong211/LifeHelper)
- [说明及截图](https://github.com/yangchong211/LifeHelper/blob/master/README.md)
- 模块：新闻，音乐，视频，图片，唐诗宋词，快递，天气，记事本，阅读器等等
- 接口：七牛，阿里云，天行，干货集中营，极速数据，追书神器等等
- [持续更新目录说明](http://www.jianshu.com/p/53017c3fc75d)

**4.3其他**
- 知乎：https://www.zhihu.com/people/yang-chong-69-24/pins/posts
- 简书：http://www.jianshu.com/u/b7b2c6ed9284
- csdn：http://my.csdn.net/m0_37700275
- github：https://github.com/yangchong211
- 喜马拉雅听书：http://www.ximalaya.com/zhubo/71989305/
