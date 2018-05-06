# Android 基本组件规范

Android 基本组件指Activity 、Fragment 、Service 、BroadcastReceiver 、ContentProvider 等等

1. Activity 间的数据通信，对于数据量比较大的，避免使用Intent + Parcelable的方式，可以考虑EventBus 等替代方案，以免造成TransactionTooLargeException。
2. Activity#onSaveInstanceState()方法不是Activity 生命周期方法，也不保证一定会被调用。它是用来在Activity 被意外销毁时保存UI 状态的，只能用于保存临时性数据，例如UI 控件的属性等，不能跟数据的持久化存储混为一谈。持久化存储应该在Activity#onPause()/onStop()中实行。
3. Activity 间通过隐式Intent 的跳转，在发出Intent 之前必须通过resolveActivity检查，避免找不到合适的调用组件，造成ActivityNotFoundException 的异常。

    ```
    if (getPackageManager().resolveActivity(intent, PackageManager.MATCH_DEFAULT_
    ONLY) != null) {
    startActivity(intent);
    }else {
    // 找不到指定的Activity
    }
    ```
4. 避免在Service#onStartCommand()/onBind()方法中执行耗时操作，如果确实有需求，应改用IntentService 或采用其他异步机制完成。

5. BroadCastReceiver

    * 避免在BroadcastReceiver#onReceive()中执行耗时操作，如果有耗时工作，应该创建IntentService 完成，而不应该在BroadcastReceiver 内创建子线程去做。
    * 由于该方法是在主线程执行，如果执行耗时操作会导致UI 不流畅。可以使用****IntentService 、创建HandlerThread 或者调Context#registerReceiver
    (BroadcastReceiver, IntentFilter, String, Handler)方法等方式，在其他Wroker 线程执行onReceive 方法。BroadcastReceiver#onReceive()方法耗时超过10 秒钟，可能会被系统杀死。
6. 避免使用隐式Intent 广播敏感信息，信息可能被其他注册了对应BroadcastReceiver 的App 接收

    * 如果广播仅限于应用内，则可以使用LocalBroadcastManager#sendBroadcast()实现，避免敏感信息外泄和Intent 拦截的风险。

7. Fragment

    * 添加Fragment 时， 确保FragmentTransaction#commit() 在Activity#onPostResume()或者FragmentActivity#onResumeFragments()内调用.
      不要随意使用FragmentTransaction#commitAllowingStateLoss() 来代替，任何commitAllowingStateLoss()的使用必须经过code review，确保无负面影响
    * Activity 可能因为各种原因被销毁， Android 支持页面被销毁前通过Activity#onSaveInstanceState() 保存自己的状态。但如果FragmentTransaction.commit()发生 在Activity 状态保存之后，就会导致Activity 重建、恢复状态时无法还原页面状态，从而可能出错。为了避免给用户造成不好的体验，系统会抛出       IllegalStateExceptionStateLoss 异常。推荐的做法是在Activity 的onPostResume() 或onResumeFragments() （ 对FragmentActivity ） 里执行    FragmentTransaction.commit()，如有必要也可在onCreate()里执行。不要随意改用FragmentTransaction.commitAllowingStateLoss() 或者直接使用try-catch 避免crash，这不是问题的根本解决之道，当且仅当你确认Activity 重建、恢复状态时，本次commit 丢失不会造成影响时才可这么做。

8. 不要在Activity#onDestroy()内执行释放资源的工作，例如一些工作线程的销毁和停止，因为onDestroy()执行的时机可能较晚。可根据实际需要，在Activity#onPause()/onStop()中结合isFinishing()的判断来执行。
9. 如非必须，避免使用嵌套的Fragment。

    * 嵌套Fragment 是在Android API 17添加到SDK以及Support 库中的功能，Fragment嵌套使用会有一些坑，容易出现bug
    * onActivityResult()方法的处理错乱，内嵌的Fragment 可能收不到该方法的回调，需要由宿主Fragment 进行转发处理；
    * 突变动画效果；
    * 被继承的setRetainInstance()，导致在Fragment 重建时多次触发不必要的逻辑。

10. 总是使用显式Intent 启动或者绑定Service，且不要为服务声明Intent Filter，保证应用的安全性。如果确实需要使用隐式调用，则可为Service 提供Intent Filter并从Intent 中排除相应的组件名称，但必须搭配使用Intent#setPackage()方法设置Intent 的指定包名，这样可以充分消除目标服务的不确定性。
11. Service 需要以多线程来并发处理多个启动请求，建议使用IntentService，可避免各种复杂的设置。

    * Service 组件一般运行主线程，应当避免耗时操作，如果有耗时操作应该在Worker线程执行。可以使用IntentService 执行后台任务。

12. 对于只用于应用内的广播，优先使用LocalBroadcastManager 来进行注册和发送，LocalBroadcastManager 安全性更好，同时拥有更高的运行效率。
13. Activity 或者Fragment 中动态注册BroadCastReceiver 时，registerReceiver()和unregisterReceiver()要成对出现。
14. Android 基础组件如果使用隐式调用，应在 AndroidManifest.xml 中使用<intent-filter> 或在代码中使用 IntentFilter 增加过滤。