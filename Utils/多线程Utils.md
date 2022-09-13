# CountDownLatch

`CountDownLatch` 是多线程控制的一种工具，它被称为 `门阀`、 `计数器`或者 `闭锁`。这个工具经常用来用来协调多个线程之间的同步，或者说起到线程之间的通信（而不是用作互斥的作用）

<img src="https://s3.ax1x.com/2020/12/21/rdcSKK.png" alt="img" style="zoom: 33%;" />

https://www.cnblogs.com/cxuanBlog/p/14166322.html

# ScheduledExecutorService

定时任务，多线程处理

```
private ScheduledExecutorService scheduledPool;
//起个定时任务，每天凌晨3点更新一次
if (scheduledPool == null) {
    scheduledPool = Executors.newScheduledThreadPool(1);
    long initDelay = getTimeMillis("03:00:00") - System.currentTimeMillis();
    initDelay = initDelay > 0 ? initDelay : TimeUtils.MILLIS_OF_A_DAY + initDelay;
    scheduledPool.scheduleAtFixedRate(new FileRefreshRunner(), initDelay, TimeUtils.MILLIS_OF_A_DAY, TimeUnit.MILLISECONDS);
}
```