# @PostConstruct注释

PostConstruct注解使用在方法上，这个方法在对象依赖注入初始化之后执行。以下直接在依赖初始化后将数据库数据查询到一个静态集合中了

参考文档：https://blog.csdn.net/qq_26878363/article/details/104500263

https://www.cnblogs.com/erlou96/p/13753824.html

![image-20221020112020945](C:\Users\mucongcong\AppData\Roaming\Typora\typora-user-images\image-20221020112020945.png)

![image-20221020112034165](C:\Users\mucongcong\AppData\Roaming\Typora\typora-user-images\image-20221020112034165.png)

# InitializingBean接口

为bean提供了属性初始化后的处理方法，它只包括afterPropertiesSet方法，凡是继承该接口的类，在bean的属性初始化后都会执行该方法。以下直接写了一个类用于执行方法，并开启了一个定时任务

参考文档：https://segmentfault.com/a/1190000012461362

![image-20221020112723409](C:\Users\mucongcong\AppData\Roaming\Typora\typora-user-images\image-20221020112723409.png)

# DisposableBean接口

在Bean生命周期结束前调用destory()方法做一些收尾工作

参考文档：https://blog.csdn.net/u014082714/article/details/80857470，如bean结束了关闭连接

![image-20221020154331880](C:\Users\mucongcong\AppData\Roaming\Typora\typora-user-images\image-20221020154331880.png)

# @Scheduled

@Scheduled注解是spring boot提供的用于**定时任务**控制的注解,主要用于控制任务在某个指定时间执行,或者每隔一段时间执行.注意**需要配合@EnableScheduling**使用

参考文档：https://segmentfault.com/a/1190000038938579