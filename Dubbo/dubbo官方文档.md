链接：https://dubbo.apache.org/zh/docs/

#### 快速开始

##### 定义服务(java)

- 定义服务接口

```java
package org.apache.dubbo.samples.basic.api;

public interface DemoService {
    String sayHello(String name);
}
```

- 服务方实现接口

```java
public class DemoServiceImpl implements DemoService {
    @Override
    public String sayHello(String name) {
        System.out.println("[" + new SimpleDateFormat("HH:mm:ss").format(new Date()) + "] Hello " + name +
                ", request from consumer: " + RpcContext.getContext().getRemoteAddress());
        return "Hello " + name + ", response from provider: " + RpcContext.getContext().getLocalAddress();
    }
}
```

- xml暴露服务

```xml
<bean id="demoService" class="org.apache.dubbo.samples.basic.impl.DemoServiceImpl"/>

<dubbo:service interface="org.apache.dubbo.samples.basic.api.DemoService" ref="demoService"/>
```

- 消费者xml引用

```xml
<dubbo:reference id="demoService" check="true" interface="org.apache.dubbo.samples.basic.api.DemoService"/>
```

- 消费者调用

```java
public static void main(String[] args) {
    ...
    DemoService demoService = (DemoService) context.getBean("demoService");
    String hello = demoService.sayHello("world");
    System.out.println(hello);
}
```

##### 编译服务 (看不懂...)

根据当前采用的语言，配置相应的 Protobuf 插件，编译后将生产语言相关的服务定义 stub，Java 语言生成的 stub 如下，核心是一个接口定义

```java
@javax.annotation.Generated(
value = "by Dubbo generator",
comments = "Source: DemoService.proto")
public interface DemoService {
    static final String JAVA_SERVICE_NAME = "org.apache.dubbo.demo.DemoService";
    static final String SERVICE_NAME = "demoservice.DemoService";

    org.apache.dubbo.demo.HelloReply sayHello(org.apache.dubbo.demo.HelloRequest request);

    CompletableFuture<org.apache.dubbo.demo.HelloReply> sayHelloAsync(org.apache.dubbo.demo.HelloRequest request);
}
```

##### 配置并加载服务

- 提供端实现服务

```java
public class DemoServiceImpl implements DemoService {
    private static final Logger logger = LoggerFactory.getLogger(DemoServiceImpl.class);

    @Override
    public HelloReply sayHello(HelloRequest request) {
        logger.info("Hello " + request.getName() + ", request from consumer: " + RpcContext.getContext().getRemoteAddress());
        return HelloReply.newBuilder()
                .setMessage("Hello " + request.getName() + ", response from provider: "
                        + RpcContext.getContext().getLocalAddress())
                .build();
    }

    @Override
    public CompletableFuture<HelloReply> sayHelloAsync(HelloRequest request) {
        return CompletableFuture.completedFuture(sayHello(request));
    }
}
```

- 提供端注册服务

```xml
<bean id="demoServiceImpl" class="org.apache.dubbo.demo.provider.DemoServiceImpl"/>
<dubbo:service serialization="protobuf" interface="org.apache.dubbo.demo.DemoService" ref="demoServiceImpl"/>
```

- 消费端引用服务

```xml
<dubbo:reference scope="remote" id="demoService" check="false" interface="org.apache.dubbo.demo.DemoService"/>
```

- 消费端使用服务

```java
public void callService() throws Exception {
    ...
    DemoService demoService = context.getBean("demoService", DemoService.class);
    HelloRequest request = HelloRequest.newBuilder().setName("Hello").build();
    HelloReply reply = demoService.sayHello(request);
    System.out.println("result: " + reply.getMessage());
}
```

#### 架构

##### 服务发现

<img src="https://dubbo.apache.org/imgs/architecture.png" alt="//imgs/architecture.png" style="zoom: 50%;" />

- 注册中心

```yaml
# application.properties
dubbo
 registry
  address: zookeeper://127.0.0.1:2181
```

##### 协议

**Triple协议：Triple 协议是 Dubbo3 推出的主力协议。Triple 意为第三代，通过 Dubbo1.0/ Dubbo2.0 两代协议的演进，以及云原生带来的技术标准化浪潮，Dubbo3 新协议 Triple 应运而生。**

协议的内容包含三部分:

1. 数据交换格式： 定义 RPC 的请求和响应对象在网络传输中的字节流内容，也叫作序列化方式
2. 协议结构： 定义包含字段列表和各字段语义以及不同字段的排列方式
3. 协议通过定义规则、格式和语义来约定数据如何在网络间传输。一次成功的 RPC 需要通信的两端都能够按照协议约定进行网络字节流的读写和对象转换。如果两端对使用的协议不能达成一致，就会出现鸡同鸭讲，无法满足远程通信的需求。

##### 服务流量管理

**流量管理的本质是将请求根据制定好的路由规则分发到应用服务上。**Dubbo提供了支持mesh方式的流量管理策略，可以很容易实现 [A/B测试](https://dubbo.apache.org/zh/docs/examples/routing/ab-testing-deployment/)、[金丝雀发布](https://dubbo.apache.org/zh/docs/examples/routing/canary-deployment/)、[蓝绿发布](https://dubbo.apache.org/zh/docs/examples/routing/blue-green-deployment/)等能力

<img src="https://dubbo.apache.org/imgs/v3/concepts/what-is-traffic-control.png" alt="What is traffic control" style="zoom:50%;" />