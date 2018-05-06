# BitmapAndDrawable

1. 加载大图片或者一次性加载多张图片，应该在异步线程中进行。图片的加载，涉及到IO 操作，以及CPU 密集操作，很可能引起卡顿。
2. 在ListView，ViewPager，RecyclerView，GirdView 等组件中使用图片时，应做好图片的缓存，避免始终持有图片导致内存溢出，也避免重复创建图片，引起性能问题。建议使用Fresco （ https://github.com/facebook/fresco ）、Glide（https://github.com/bumptech/glide）等图片库。
3. png 图片使用TinyPNG 或者类似工具压缩处理，减少包体积。
4. 应根据实际展示需要，压缩图片，而不是直接显示原图。手机屏幕比较小，直接显示原图，并不会增加视觉上的收益，但是却会耗费大量宝贵的内存。
5. 使用完毕的图片，应该及时回收，释放宝贵的内存。
6. 在Activity#onPause()或Activity#onStop()回调中，关闭当前activity 正在执行的的动画。
7. 在动画或者其他异步任务结束时，应该考虑回调时刻的环境是否还支持业务处理。例如Activity 的onStop()函数已经执行，且在该函数中主动释放了资源，此时回调中如果不做判断就会空指针崩溃。
8. 使用 inBitmap 重复利用内存空间，避免重复开辟新内存。

     ```
      public static Bitmap decodeSampledBitmapFromFile(String filename, int reqWidth, int
      reqHeight, ImageCache cache) {
        final BitmapFactory.Options options = new BitmapFactory.Options();
        ...
        BitmapFactory.decodeFile(filename, options);
        ...
        // 如果在Honeycomb 或更新版本系统中运行，尝试使用inBitmap
        if (Utils.hasHoneycomb()) {
          addInBitmapOptions(options, cache);
        }
        ...
          return BitmapFactory.decodeFile(filename, options);
        }
        private static void addInBitmapOptions(BitmapFactory.Options options,
        ImageCache cache) {
        // inBitmap 只处理可变的位图，所以强制返回可变的位图
          options.inMutable = true;
          if (cache != null) {
            Bitmap inBitmap = cache.getBitmapFromReusableSet(options);
          if (inBitmap != null) {
            options.inBitmap = inBitmap;
          }
        }
      }
     ```

9. 使用RGB_565 代替RGB_888，在不怎么降低视觉效果的前提下，减少内存占用。

    * ALPHA_8 代表8 位Alpha 位图；
    * ARGB_4444 代表16 位ARGB 位图；
    * ARGB_8888 代表32 位ARGB 位图；
    * RGB_565 代表8 位RGB 位图。

10. 尽量减少 Bitmap（BitmapDrawable）的使用，尽量使用纯色（ColorDrawable）、渐变色（GradientDrawable）、StateSelector（StateListDrawable）等与Shape 结合的形式构建绘图。
11. 谨慎使用gif 图片，注意限制每个页面允许同时播放的gif 图片，以及单个gif 图片的大小。
12. 大图片资源不要直接打包到apk，可以考虑通过文件仓库远程下载，减小包体积。
13. 根据设备性能，选择性开启复杂动画，以实现一个整体较优的性能和体验；
14. 在有强依赖 onAnimationEnd 回调的交互时，如动画播放完毕才能操作页
面， onAnimationEnd 可能会因各种异常没被回调（ 参考：
https://stackoverflow.com/questions/5474923/onanimationend-is-not-getting-called-onanimationstart-works-fine ）建议加上超时保护或通过 postDelay 替代onAnimationEnd。
15. 当View Animation 执行结束时，调用View.clearAnimation()释放相关资源。
16. 