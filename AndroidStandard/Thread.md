# 线程和进程间通讯

1. 不要通过Intent 在Android 基础组件之间传递大数据（binder transaction缓存为1MB），可能导致OOM。
2. 在Application 的业务初始化代码加入进程判断，确保只在自己需要的进程初始化。特别是后台进程减少不必要的业务初始化。

    ```
    public class MyApplication extends Application {
        @Override
        public void onCreate() {
        //在所有进程中初始化
        ....
        //仅在主进程中初始化
        if (mainProcess) {
        ...
        }
        //仅在后台进程中初始化
        if (bgProcess) {
        ...
        }
      }
   }
    ```

3. 新建线程时，必须通过线程池提供（AsyncTask 或者ThreadPoolExecutor或者其他形式自定义的线程池），不允许在应用中自行显式创建线程。
4. 线程池不允许使用Executors 去创建，而是通过ThreadPoolExecutor 的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。

    * FixedThreadPool 和SingleThreadPool ： 允许的请求队列长度为Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM；
    * CachedThreadPool 和ScheduledThreadPool ： 允许的创建线程数量为Integer.MAX_VALUE，可能会创建大量的线程，从而导致OOM。

5. 子线程中不能更新界面，更新界面必须在主线程中进行，网络操作不能在主线程中调用。
6. 尽量减少不同APP 之间的进程间通信及拉起行为。拉起导致占用系统资源，影响用户体验。
7. 新建线程时，定义能识别自己业务的线程名称，便于性能优化和问题排查。
8. ThreadPoolExecutor 设置线程存活时间(setKeepAliveTime)，确保空闲时线程能被释放。
9. 禁止在多进程之间用SharedPreferences 共享数据， 虽然可以(MODE_MULTI_PROCESS)，但官方已不推荐。
10. 谨慎使用Android 的多进程，多进程虽然能够降低主进程的内存压力，但会遇到如下问题

    * 首次进入新启动进程的页面时会有延时的现象（有可能黑屏、白屏几秒，是白屏还是黑屏和新Activity 的主题有关）；
    * 应用内多进程时，Application 实例化多次，需要考虑各个模块是否都需要在所有进程中初始化。