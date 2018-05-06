# UI和布局

1. 布局中不得不使用ViewGroup 多重嵌套时，不要使用LinearLayout 嵌套，改用RelativeLayout，可以有效降低嵌套数。

    * Android 应用页面上任何一个View 都需要经过 measure、layout、draw 三个步骤才能被正确的渲染
    * 页面拥上的View 越多，measure、layout、draw 所花费的时间就越久。要缩短这个时间，关键是保持View 的树形结构尽量扁平，而且要移除所有不需要渲染的View。理想情况下，总共的measure，layout，draw 时间应该被很好的控制在16ms以内
    * 要找到那些多余的View（增加渲染延迟的view），可以用Android Studio Monitor里的Hierarchy Viewer 工具

2. 在Activity 中显示对话框或弹出浮层时，尽量使用DialogFragment，而非Dialog/AlertDialog，这样便于随Activity生命周期管理对话框/弹出浮层的生命周期。
3. 源文件统一采用UTF-8 的形式进行编码。
4. 禁止在非UI 线程进行View 相关操作。
5. 文本大小使用单位sp，View 大小使用单位dp。对于TextView，如果在文字大小确定的情况下推荐使用wrap_content 布局避免出现文字显示不全的适配问题。
6. 禁止在设计布局时多次为子View 和父View 设置同样背景进而造成页面过度绘制，推荐将不需要显示的布局进行及时隐藏。
7. 灵活使用布局，推荐merge、ViewStub 来优化布局，尽可能多的减少UI布局层级，推荐使用FrameLayout，LinearLayout、RelativeLayout 次之。
8. 在需要时刻刷新某一区域的组件时，建议通过以下方式避免引发全局layout刷新

    * 设置固定的View 大小的宽高，如倒计时组件等；
    * 调用View 的layout 方法修改位置，如弹幕组件等；
    * 通过修改Canvas 位置并且调用invalidate(int l, int t, int r, int b)等方式限定刷新区域；
    * 通过设置一个是否允许requestLayout 的变量，然后重写控件的requestlayout、onSizeChanged 方法， 判断控件的大小没有改变的情况下， 当进入requestLayout 的时候，直接返回而不调用super 的requestLayout 方法。

9. 不能在Activity 没有完全显示时显示PopupWindow 和Dialog.

    * Android Activity 创建时的生命周期，按照 onCreate() -> onStart() -> onResume() ->onAttachedToWindow() -> onWindowFocusChanged() 的顺序， 其中在Activity#onAttachedToWindow() 时，Activity 会与它的 Window 关联，这时 UI 才会开始绘制，在 Activity#onWindowFocusChanged() 时，UI 才变成可交互状态.
    * 如果在 Window 未关联时就创建对话框，UI 可能显示异常。推荐的做法是在 Activity#onAttachedToWindow() 之后（ 其实最好是Activity#onWindowFocusChanged() 之后）才创建对话框。

10. 尽量不要使用AnimationDrawable，它在初始化的时候就将所有图片加载到内存中，特别占内存，并且还不能释放，释放之后下次进入再次加载时会报错。但是数量少的drawable还是可以接受的。
11. 不能使用ScrollView 包裹ListView/GridView/ExpandableListVIew;因为这样会把ListView 的所有Item 都加载到内存中，要消耗巨大的内存和cpu 去绘制图面

    * 推荐使用NestedScrollView

12. 不要在Android 的Application 对象中缓存数据。基础组件之间的数据共享请使用Intent 等机制，也可使用SharedPreferences 等数据持久化机制。
13. 使用Toast 时，建议定义一个全局的Toast 对象，这样可以避免连续显示Toast 时不能取消上一次Toast 消息的情况。即使需要连续弹出Toast，也应避免直接调用Toast#makeText。
14. 使用Adapter 的时候，如果你使用了ViewHolder 做缓存，在getView()的方法中无论这项convertView 的每个子控件是否需要设置属性(比如某个TextView设置的文本可能为null，某个按钮的背景色为透明，某控件的颜色为透明等)，都需要为其显式设置属性(Textview 的文本为空也需要设置setText("")，背景透明也需要设置)，否则在滑动的过程中，因为adapter item 复用的原因，会出现内容的显示错乱。
