### 多进程的好处

一般将一些计算型的逻辑放在一个独立的进程来处理，这样主进程仍然可以流畅地响应界面事件，提高用户体验。

* 应用进程占用内存受限，内存占用越大，被系统杀死的可能性越大。多进程可以降低主进程被杀死的概率
* 子进程crash，不会影响主进程
* 主进程退出，子进程依然工作，适用于像推送等服务

### 设置android:process的两种形式

* android:process=":remote"

  实际的进程名为“com.example.processtest:remote”,私有进程，其他应用程序的组件不可以运行在该进程。

* android:process=”com.example.processtest.remote”

  全局进程

### 实例

