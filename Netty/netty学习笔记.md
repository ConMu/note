[TOC]

学习笔记：https://dongzl.github.io/netty-handbook/#/

代码demo：https://codeantenna.com/a/GuwUV8EV4Q



# 介绍与应用场景

## 介绍

1. Netty由JBOSS提供，目前在github上开源https://github.com/netty/netty；
2. Netty特点：异步、基于事件驱动，可用于开发高性能、高可靠的网络IO框架；
3. Netty遵循TCP协议，面向Client端的高并发应用，或P2P数据传输；
4. Netty底层为NIO。

## 应用场景

1. 互联网：高性能RPC框架，Dubbo
2. 游戏：服务器
3. 大数据：Hadoop...

------

# Java IO模型

前言：IO模型即为采用何种通道发送/接收数据，决定了程序通信的性能。Java共支持3中IO模型：BIO，NIO，AIO。

## BIO

### 介绍（Blocking IO）

1. 同步阻塞，一个连接一个线程，即客户端请求服务器时需要启动一个线程进行处理。
2. 适用于连接数小且固定的架构，对服务器资源要求高，类与接口再java.io中

### 工作机制

1. 服务端开启ServerSocket
2. 客户端启动Socket进行通信，默认情况下服务端开启一个线程与之通信（也可用线程池）

<img src="https://dongzl.github.io/netty-handbook/_media/chapter02/chapter02_03.png" alt="img" style="zoom:50%;" />

### 应用实例

> 1. 使用 `BIO` 模型编写一个服务器端，监听 `6666` 端口，当有客户端连接时，就启动一个线程与之通讯。
> 2. 要求使用线程池机制改善，可以连接多个客户端。
> 3. 服务器端可以接收客户端发送的数据（`telnet` 方式即可）。
> 4. 代码：https://dongzl.github.io/netty-handbook/#/_content/chapter02?id=_25-java-bio-%e5%ba%94%e7%94%a8%e5%ae%9e%e4%be%8b

### 评价

1. 并发较大时，需创建大量线程处理连接，资源消耗大
2. 建立连接后，若线程无数据可读，则阻塞在read上，造成资源浪费

## NIO

### 介绍（non-blocking IO）

1. 同步非阻塞，java1.4产物，放于java.nio包下；
2. 三大核心：Channel（通道）、Buffer（缓冲区）、Selector（选择器）；
3. 面向缓冲，读取时可在缓冲区前后移动，增加读取数据的灵活性；
4. 线程从通道发送请求或处理数据，若无数据则不处理，无阻塞，写入也是非阻塞的；
5. 一个线程可以处理多个操作；
6. `HTTP 2.0` 使用了多路复用的技术，做到同一个连接并发处理多个请求，而且并发请求的数量比 `HTTP 1.1` 大了好几个数量级

> 案例代码：https://dongzl.github.io/netty-handbook/#/_content/chapter03?id=_31-java-nio-%e5%9f%ba%e6%9c%ac%e4%bb%8b%e7%bb%8d

### NIO与BIO比较

1. BIO以流的形式处理，NIO以块处理，效率NIO更高；
2. BIO阻塞，NIO非阻塞；
3. BIO基于字节流、字符流，NIO基于三大核心，数据流向为：读channel -> buffer，写buffer -> channel ，selector用于监听通道事件，节省线程开销。

### 工作机制

1. 一个channel对应一个buffer；
2. seletor一个线程，对应多个channel，channel注册到seletor；
3. 事件决定程序selector走哪个channel；
4. buffer底层是一个数组；
5. NIO的buffer可读可写（通过flip方法切换）。

<img src="https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_01.png" alt="img" style="zoom:50%;" />

### 缓冲区介绍（Buffer）

1. 本质是一个可读可写的内存块（容器对象）；
2. 缓冲区对象内置方法，可以了解缓冲区变化情况；
3. channel提供读写渠道，但都需要经过buffer。

<img src="https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_02.png" alt="img" style="zoom:50%;" />

### 继承关系

- 继承关系

![img](https://ucc.alicdn.com/pic/developer-ecology/09112becd54b4f6fb61016778d346c5a.png)

- 属性（容量、缓冲终点、未知、标记）

![img](https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_05.png)

- 方法

![img](https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_06.png)

### 通道介绍（Channel）

#### 简介

1. 通道可同时进行读写（流同一时间只能进行一种）；
2. 通道可以异步读写；
3. 通道可以与缓冲进行交互。
4. Java中通道为一个接口：public interface Channel extends Closeable{}
5. `FileChannel` 用于文件的数据读写，`DatagramChannel` 用于 `UDP` 的数据读写，`ServerSocketChannel` 和 `SocketChannel` 用于 `TCP` 的数据读写。

#### FileChannel介绍

1. 作用：对本地文件进行IO
2. 常见方法：
   - read()：通道读取数据放至缓冲区
   - write()：缓冲区写入通道
   - transferFrom()：目标通道复制到当前通道
   - transferTo()：当前通道复制到目标通道

> 实例：
>
> https://dongzl.github.io/netty-handbook/#/_content/chapter03?id=_361-filechannel-%e7%b1%bb
>
> https://dongzl.github.io/netty-handbook/#/_content/chapter03?id=_363-%e5%ba%94%e7%94%a8%e5%ae%9e%e4%be%8b2-%e6%9c%ac%e5%9c%b0%e6%96%87%e4%bb%b6%e8%af%bb%e6%95%b0%e6%8d%ae
>
> https://dongzl.github.io/netty-handbook/#/_content/chapter03?id=_364-%e5%ba%94%e7%94%a8%e5%ae%9e%e4%be%8b3-%e4%bd%bf%e7%94%a8%e4%b8%80%e4%b8%aa-buffer-%e5%ae%8c%e6%88%90%e6%96%87%e4%bb%b6%e8%af%bb%e5%8f%96%e3%80%81%e5%86%99%e5%85%a5
>
> https://dongzl.github.io/netty-handbook/#/_content/chapter03?id=_365-%e5%ba%94%e7%94%a8%e5%ae%9e%e4%be%8b4-%e6%8b%b7%e8%b4%9d%e6%96%87%e4%bb%b6-transferfrom-%e6%96%b9%e6%b3%95

### 继承关系

![img](https://upload-images.jianshu.io/upload_images/7378149-83d09462db6d3f67.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

### 注意事项（channel与buffer）

1. `ByteBuffer` 支持类型化的 `put` 和 `get`，`put` 放入的是什么数据类型，`get` 就应该使用相应的数据类型来取出，否则可能有 `BufferUnderflowException` 异常
2. 可以将一个普通 `Buffer` 转成只读 `Buffer`
3. `NIO` 还提供了 `MappedByteBuffer`，可以让文件直接在内存（堆外的内存）中进行修改，而如何同步到文件由 `NIO` 来完成
4. `NIO` 还支持通过多个 `Buffer`（即 `Buffer`数组）完成读写操作

### 选择器介绍（Selector）

1. Selector（多路复用器）的出现，使得NIO一个线程可以处理多个客户端连接；
2. Selector可以检测通道上是否有事件发生（channel注册到selector）；
3. 只有在连接、通道有读写时会进行处理，提高资源利用效率，减少多线程上下文切换的开销。

![img](https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_10.png)

**补充**

1. Netty的NioEventLoop聚合了Selector，可同时并发处理上千客户端连接；
2. 当线程从Socket读写数据时，若无数据可用，则可以处理其他任务；
3. 一个 `I/O` 线程可以并发处理 `N` 个客户端连接和读写操作，这从根本上解决了传统同步阻塞 `I/O` 一连接一线程模型，架构的性能、弹性伸缩能力和可靠性都得到了极大的提升

### 相关方法

<img src="https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_11.png" alt="img" style="zoom:50%;" />

### 流程示意图

> 包含内容：Selector、SelectionKey、ServerSocketChannel、SocketChannel

![img](https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_22.png)

1. 客户端连接，通过ServerSocketChannel得到SocketChannel；
2. Selector通过select方法进行监听，返回事件发生个数；
3. socketChannel注册到Selector上（register方法），一个selector上可注册多个；
4. 注册返回selectionkey，于selector关联；
5. 通过selectionkey反向获取SocketChannel（channel()方法）；
6. 得到channel后进行业务处理。

> 编程入门：https://dongzl.github.io/netty-handbook/#/_content/chapter03?id=_39-nio-%e9%9d%9e%e9%98%bb%e5%a1%9e%e7%bd%91%e7%bb%9c%e7%bc%96%e7%a8%8b%e5%bf%ab%e9%80%9f%e5%85%a5%e9%97%a8

### NIO与零拷贝

#### 零拷贝

1. 与性能优化有关；
2. Java常用零拷贝方式：mmap（内存映射）、sendFile；
3. 从操作系统层讲就是没有cpu的拷贝。

#### 传统IO

**DMA**：`direct memory access` 直接内存拷贝（不使用 `CPU`）

```java
File file = new File("test.txt");
RandomAccessFile raf = new RandomAccessFile(file, "rw");

byte[] arr = new byte[(int) file.length()];
raf.read(arr);

Socket socket = new ServerSocket(8080).accept();
socket.getOutputStream().write(arr);
```

![img](https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_17.png)

#### mmap优化

通过内存映射，将文件映射到内核缓冲区，用户空间可以共享内核空间数据。网络传输时减少拷贝次数。

![img](https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_18.png)

#### sendFile优化

数据不经过用户态，直接从内核缓冲区进入SocketBuffer。减少一次上下文切换。

![img](https://dongzl.github.io/netty-handbook/_media/chapter03/chapter03_19.png)

#### 零拷贝的优势

1. 从操作系统的层面讲，内核缓冲区间是没有数据重复的；
2. 减少数据复制次数，提高性能，如更少的上下文切换、更少的CPU缓存伪共享。

#### mmap与sendFile区别

1. mmap适合小量数据读写，sendFile相反；
2. mmap 4次上下文切换，3次数据拷贝，snedFile 3次切换，最少2次拷贝；
3. `sendFile` 可以利用 `DMA` 方式，减少 `CPU` 拷贝，`mmap` 则不能（必须从内核拷贝到 `Socket`缓冲区）。

> 案例：https://dongzl.github.io/netty-handbook/#/_content/chapter03?id=_3148-nio-%e9%9b%b6%e6%8b%b7%e8%b4%9d%e6%a1%88%e4%be%8b

## AIO

1. `JDK7` 引入了 `AsynchronousI/O`，即 `AIO`。在进行 `I/O` 编程中，常用到两种模式：`Reactor` 和 `Proactor`。`Java` 的 `NIO` 就是 `Reactor`，当有事件触发时，服务器端得到通知，进行相应的处理
2. `AIO` 即 `NIO2.0`，叫做异步不阻塞的 `IO`。`AIO` 引入异步通道的概念，采用了 `Proactor` 模式，简化了程序编写，有效的请求才启动线程，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用
3. 目前 `AIO` 还没有广泛应用，`Netty` 也是基于 `NIO`，而不是 `AIO`，因此我们就不详解 `AIO` 了，有兴趣的同学可以参考[《Java新一代网络编程模型AIO原理及Linux系统AIO介绍》](http://www.52im.net/thread-306-1-1.html)

## IO模型对比

|          | BIO      | NIO                    | AIO        |
| -------- | -------- | ---------------------- | ---------- |
| IO模型   | 同步阻塞 | 同步非阻塞（多路复用） | 异步非阻塞 |
| 编程难度 | 简单     | 复杂                   | 复杂       |
| 可靠性   | 差       | 好                     | 好         |
| 吞吐量   | 低       | 高                     | 高         |

# Netty概述

1. 官网https://netty.io/
2. 优点
   1. 设计优雅：适用于各种传输类型的统一 `API` 阻塞和非阻塞 `Socket`；基于灵活且可扩展的事件模型，可以清晰地分离关注点；高度可定制的线程模型-单线程，一个或多个线程池。
   2. 使用方便：详细记录的 `Javadoc`，用户指南和示例；没有其他依赖项，`JDK5（Netty3.x）`或 `6（Netty4.x）`就足够了。
   3. 高性能、吞吐量更高：延迟更低；减少资源消耗；最小化不必要的内存复制。
   4. 安全：完整的 `SSL/TLS` 和 `StartTLS` 支持。
   5. 社区活跃、不断更新：社区活跃，版本迭代周期短，发现的 `Bug` 可以被及时修复，同时，更多的新功能会被加入。

![img](https://dongzl.github.io/netty-handbook/_media/chapter04/chapter04_01.png)

# Netty架构

## 线程模型介绍

1. 目前存在的线程模型：传统阻塞`I/O`模型  与 ` Reactor`模式；
2. 根据 `Reactor` 的数量和处理资源池线程的数量不同，有 `3` 种典型的实现单 `Reactor` 单线程；单 `Reactor`多线程；主从 `Reactor`多线程
3. `Netty` 线程模式（`Netty` 主要基于主从 `Reactor` 多线程模型做了一定的改进，其中主从 `Reactor` 多线程模型有多个 `Reactor`）

## 传统阻塞I/O模型

### 原理图

![img](https://dongzl.github.io/netty-handbook/_media/chapter05/chapter05_01.png)

### 特点

1. 阻塞IO获取数据；
2. 每个连接都需要独立线程；

### 缺点

1. 并发高时，创建大量线程，系统资源开销大；
2. 连接创建后，如果数据暂时不可读，该线程会阻塞，造成资源浪费；

## Reactor模式

### 原理图

![img](https://dongzl.github.io/netty-handbook/_media/chapter05/chapter05_03.png)

### 特点

1. 基于事件驱动，`Reactor`模式，通过一个或多个输入同时传递给服务处理器的模式；
2. 服务器端程序处理多个请求，并将它们同步分派到相应的处理线程，因此又叫`Dispatcher`模式；
3. 使用IO复用监听事件，收到事件后，分发给某个线程，实现网络服务高并发。

### 核心组成

1. `Reactor`：直译为反应器，反应器在一个单独线程中执行，负责监听与分发事件，分发给适当的处理程序来对IO事件作出反应。
2. `Handlers`：处理程序执行IO事件要完成的实际事件，反应器通过调度适当处理程序相应IO事件，处理程序执行非阻塞操作。

### 单Reactor单线程

#### 介绍

![img](https://dongzl.github.io/netty-handbook/_media/chapter05/chapter05_04.png)

1. Select是IO复用模型中的标准API，实现一个阻塞对象监听多个链路请求；
2. 反应器监控客户端请求事件，收到事件后通过Dispatch分发；
3. 如果是建立连接请求，则通过Acceptor处理，然后创建Handler对象处理后续业务；
4. 如果不是建立连接请求，则分发到对应Handler响应；
5. Handler完成`read` -> 业务处理 -> `send`流程。

#### 分析

1. 优点：模型简单，没有多线程、进程通信、竞争的问题，全部都在一个线程中完成
2. 缺点：
   1. 性能问题，只有一个线程，无法完全发挥多核 `CPU` 的性能。`Handler`在处理某个连接上的业务时，整个进程无法处理其他连接事件，很容易导致性能瓶颈
   2. 可靠性问题，线程意外终止，或者进入死循环，会导致整个系统通信模块不可用，不能接收和处理外部消息，造成节点故障
3. 使用场景：客户端的数量有限，业务处理非常快速，比如 `Redis` 在业务处理的时间复杂度 `O(1)` 的情况

### 单Reactor多线程

![img](https://dongzl.github.io/netty-handbook/_media/chapter05/chapter05_05.png)