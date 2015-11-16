# stackoverflow-netty-top-qa
http://stackoverflow.com/questions/tagged/netty?page=1&amp;sort=votes&amp;pagesize=15


## 对比 ##

- [Netty vs Apache MINA][http://stackoverflow.com/questions/1637752/netty-vs-apache-mina]

虽然MINA和Netty有相同的目标，但实际上有很大不同。你应该仔细考虑你的选择。
我们很幸运有很多MINA的经验，也有时间研究Netty。特别喜欢简洁的API和完善的文档。
性能也更好地跃然纸上。更重要的是我们知道[Trustin Lee](https://github.com/trustin)随时回答任何问题，而且他确实做到了。

我们发现在Netty中所有事情变得简单的多。某一段时期，我们尝试重新实现相同的功能（MINA已经存在），我们从头开始做起。
遵循良好的文档和示例我们完成了大多数的功能，用了很少的代码。

Netty 管道(Pipeline)很好地为我们服务，某些地方也比MINA更简单，所有业务就是一个handler，取决于你是否处理上游事件、下游事件，一起处理
或者消耗更底层的资源。在“重播中”快速消耗字节几乎是是一种乐趣。同样美妙的是可以动态地，很容易的重配置pipeline。

但是Netty这个耀眼的明星，恕我直言，Netty创建一个“覆盖所有”的pipeline 处理器。你可能已经在文档中阅读覆盖注解，但本质上赋予你当单行代码上的状态。
这样一来就没有杂乱，没有sessions的映射，synchronized和类似同步的语法，我们可以简单的声明常规变量（比如，“username”），并使用它们。

但当我碰到障碍时。MINA下我们已经有多协议支持的服务器，这样一来我们的应用程序协议可以跑在TCP/IP,HTTP和UDP上。当我们切换到Netty上，
我们短短几分钟内就增加了SSL和HTTPS，至今运行的不错。但是当我们遇到UDP的时候，我们遭遇了滑铁卢。MINA对用户非常友好，我们可以把UDP当成一个
“已连接”协议。但Netty没有这样的抽象。UDP是一种无连接，Netty也是这样对待。相比MIAN做的，Netty暴露很多原生的UDP无连接操作。这样一来，
在Netty下可用UDP做很多事情，相比你不能用MINA提供的高层次抽象，但这也正是我们所依赖的。

添加一种“连接的UDP”封装和相关的封装不是一件容易的事。来自时间的限制和Trustin的建议，最合适的方式是在Netty中实现我们自己的传输提供者，
这样虽然不是很快，但我们最终还是舍弃了Netty。

认真比较两者的不同之后，可以很快的选择他们，你可以预期那样测试和运行功能。如果你满意Netty可以做这些事，我也会毫不犹豫的去用它，而不是MINA。
如果你是在同一个应用上，采取从MINA转向Netty，那么就不值得计较两个APIs 有很大的不同，你应该考虑为Netty来一个真正的重写，你不会后悔这个决定。



--------------

下面是作者自己本人的回答，居然排名第二，非常有意思

MINA有更多创意的功能花费在复杂性，相对来说性能会差一些。
一些功能已经整合进核心内容，也由于太紧凑至于需要被删除掉，对于用于来说不是很必要的功能。
Netty中，我试图处理类似的争议问题，来保留已知MINA的强项。

如今，大部分在MINA中可用的功能同样可以用在Netty中。我的观点是，Netty拥有更简洁，更友好的文档API，因为Netty是重构MINA的结果和解决已知的问题。
如果你发现任何本质的功能丢失，请向论坛随时提出建议。我会很乐意解决你的问题。

同样需要注意的是Netty拥有更快的开发周期，简单的检查最近的发布日期就知道。同样，你也会考虑MINA团队会进行主要的重构，MINA3，意味着他们会
完全破坏API的兼容性。



- [Jetty和Netty有何不同？](http://stackoverflow.com/questions/5385407/whats-the-difference-between-jetty-and-netty)


N和J 字母的不同，先开个玩笑:)

**Jetty**是一个轻量级的Servlet容器，易于嵌入在Java程序中，同样很容易使用jetty客户端。

**Netty**是一个异步时间驱动的网络应用框架。你可以自定义servlet容器或者通过Netty框架示例来写Http客户端程序。

**追加**

忘了说Jetty8 和Apache 的tomcat7都支持server3.0 规范，但是Netty没有。因为它不是一个servlet容器。


- [Play框架或者Netty不能释放socket](http://stackoverflow.com/questions/13330937/play-framework-netty-does-not-release-socket)

[Play框架](https://github.com/playframework/playframework)

我确实遇到过类似的bug，但不是在Play中，而是在JVM（play框架运行在上面）。借助关闭还没有释放的文件句柄的channels 指针，直到强制关闭JVM。
我忘记是怎么找到bug 报告的，否则我会贴出它。但它是JVM中著名的bug。最后我不得不工作。目前最好的建议是用相同的channel/file 句柄尽可能的重构它。

- [在Play框架上执行SSL](http://stackoverflow.com/questions/7099361/enforce-ssl-on-play-framework)

有很多种方式可以执行SSL

第一种，你可以使用`secure()`方法来设置你所有的actions，例如

```
<a href="@{Application.index.secure()}">index page</a>
```

另一种方式，可能也是最好的，可以通过一个前端HTTP服务器，类似于Apache，Nginx或者[Lighttpd](http://www.lighttpd.net/).

前端http服务器的主要用处是你的应用可以通过9000端口访问，但是外网不可以访问。HTTP响应所有到来的请求，可以配置为仅接受HTTPS。
HTTPS 通过HTTP服务器处理，请求会转发给Play。

这样一来，会让整个Play框架运行良好，SSL是另一个应用程序卸载。

相同的方法可以应用于负载均衡上，而不是HTTP服务器，但我猜大部分人会使用更便宜的解决方法，比如使用HTTP服务器，除非它运行在一个合适的环境上。


- [Netty 如何使用线程池？](http://stackoverflow.com/questions/5474372/how-netty-uses-thread-pools)

以下来自于NioServerSocketChannelFactory  文档

`
NioServerSocketChannelFactory 创建一个基于NIO ServerSocketChannel的服务器端。
利用非阻塞I/O模式，引入NIO来高校服务很多同步链接。

线程如何工作
在一个NioServerSocketChannelFactory中有两种类型的线程；一种是boss线程，另一种是worker线程。

Boss 线程
每个绑定ServerSocketChannel 都会有自己的boss线程。比如，如果你打开两个服务端口，比如80和443，那么你会有两个boss线程。
一个boss线程接受到来的连接，直到该端口解绑。一旦成功接受一个线程，boss线程该Channel 给worker线程中的一个，而worker 线程是
NioServerSocketChannelFactory 管理的。

Worker 线程
一个NioServerSocketChannelFactory 可以有一个或多个worker线程。一个worker线程在非阻塞模式中，可以为一个或多个Channel
执行非阻塞线程的读和写。

`

在Nio模式中，boss线程关心全部绑定socket（监听socket）接口，worker线程关心Accepted-socket（包括IO和调用事件方法，比如messageReceived方法）



- [java中，哪一个NIO类库（Netty,Grizzly,keyonet,...）可以简单的后端实现](http://stackoverflow.com/questions/8269093/which-nio-library-netty-grizzly-kryonet-for-simple-backend-server-imple)


尝试翻译下这个文档，它回答了你的问题。[http://blog.xebia.fr/2011/11/09/java-nio-et-framework-web-haute-performance/](http://blog.xebia.fr/2011/11/09/java-nio-et-framework-web-haute-performance/)

如今，在一次法国背景，由VmWare（USI2011）举办的挑战赛，法国著名Java EE专家组，做了很多 NIO服务器[poc](http://wenku.baidu.com/link?url=Va9ibulwJdINVAIgzMRT6seE-Ap2_6zmSHMBfBNauZfNltKzxREUkZEg3TrFBftKgSbPDwxjDIZF3pI4C_WMzZ2ic5tclC5sf_SznrfcbEy)。它是构建一个简单的问答程序，
它可以承载处理一百万连接用户。

他们凭借伟大的结果赢得了挑战。他们的实现是Netty+Gemfire ，而且他们仅仅是用MemoryAwareThreadPool 替代了CachedThreadPool 

Netty看起来提供了很好地性能，和友好的文档。

他们也考虑过Deft，灵感来源于Tornado（python/facebook），但目前仍然有些不稳定。

**追加：** 这是上面回答中的[翻译链接](http://translate.google.com/translate?sl=auto&tl=en&js=n&prev=_t&hl=en&ie=UTF-8&u=http://blog.xebia.fr/2011/11/09/java-nio-et-framework-web-haute-performance/)



- [使用Jetty还是Netty](http://stackoverflow.com/questions/8639625/use-jetty-or-netty)

Jetty 从版本6开始([点击这里查看详情](http://wiki.eclipse.org/Jetty/Feature/Continuations))有很多支持异步请求处理，使用一个专有的API。
更近的版本支持了部分Servlet3.0 API中的异步API，类似于其他兼容性的实现。

除非你有很特殊的需求，否则使用Netty会看起来事倍功半，此外，Jetty将会用最小的努力来完成这项工作。


- [工作在很多客户端连接情况下，Netty线程模式是如何工作的？](http://stackoverflow.com/questions/7895964/how-does-the-netty-threading-model-work-in-the-case-of-many-client-connections)

这虽然不是你想要的答案，但是你可以使用相同的`NioClientSocketChannelFactory `来用多个`ChannelPipelineFactorys `创建单个`ClientBootstrap` ，
这样依次增加n数量的连接。下面是示例代码：

```
public static void main(String[] args)
{
    String host = "localhost";
    int port = 8090;
    ChannelFactory factory = new NioClientSocketChannelFactory(Executors
            .newCachedThreadPool(), Executors.newCachedThreadPool());
    MyHandler handler1 = new MyHandler();
    PipelineFactory factory1 = new PipelineFactory(handler1);
    AnotherHandler handler2 = new AnotherHandler();
    PipelineFactory factory2 = new PipelineFactory(handler2);
    ClientBootstrap bootstrap = new ClientBootstrap(factory);
    // At client side option is tcpNoDelay and at server child.tcpNoDelay
    bootstrap.setOption("tcpNoDelay", true);
    bootstrap.setOption("keepAlive", true);
    for (int i = 1; i<=50;i++){
        if(i%2==0){
            bootstrap.setPipelineFactory(factory1);
        }else{
            bootstrap.setPipelineFactory(factory2);
        }

        ChannelFuture future = bootstrap.connect(new InetSocketAddress(host,
                port));

        future.addListener(new ChannelFutureListener()
        {
            @Override
            public void operationComplete(ChannelFuture future) throws Exception
            {
                future.getChannel().write("SUCCESS");
            }
        });
    }
}
```

这个代码同样展示了pipeline 工厂在设置为不同的连接时有什么不同之处。基于你创建的连接，促使你在channel pipeline中调整编码/解码。

- [分析Netty性能](http://stackoverflow.com/questions/14613615/profiling-netty-performance)

Netty默认创建Runtime.getRuntime().availableProcessors() * 2 个角色。在你的示例中有16个。
这就意味着你可以同时处理16个channels，其他的channels会一直等待，直到你释放`ChannelUpstreamHandler.handleUpstream/SimpleChannelHandler.messageReceived`
处理器，所以不需要再这样（I/O）线程中做很多操作，否则你会在其他channels卡住。

- [对比基础的ServerSocket服务，Netty有什么好处？](http://stackoverflow.com/questions/8406914/benefits-of-netty-over-basic-serversocket-server)

Netty对比简单的从socket对，其主要的好处是使用了流。
Netty支持**非阻塞，异步I/O**（使用Java NIO API），当你使用流从sockets读写时（你会开启一个新的线程为每个从`ServerSocket`收到的连接）
你正在使用阻塞，同步I/O。Netty把握的尺度更好一些，当你的系统需要有能力同一时间处理很多（上千个）线程是很重要的，可能不值得使用类似Netty这样的框架。

一些背景信息：线程在一个操作系统中是相对昂贵的资源。每个线程都需要内存堆栈（类似占用2MB的大小空间）。当你创建上千个线程，会消耗很多内存；
同样，操作系统会限制创造线程的数量。所以你不需要为每个到来的连接启动一个新线程。异步I/O的目的是为了从线程中解耦（不是一对一的关系）。
这样会有更多的连接而不是线程创造的，而且无论何时事件发生在其中任何一个连接上面（比如，接收到数据的时候），一个线程从线程池中出来临时用于处理事件。

---------------------------------

- [如何写一个高性能的Netty客户端？](http://stackoverflow.com/questions/8444267/how-to-write-a-high-performance-netty-client)


1)如果客户端只是发送，没有接受，你就可以关闭读channel，以下是代码示例

```
channel.setReadable(false);
```

2) 可以通过多个客户端channel很容易的增加吞吐量，同样可以扩展。

3）以下的调整通常也可以提高性能（读或写）

- 有创建一个类似pipline的分段式事件驱动架构，通过OrderdMemoryAwareThreadPoolExecutor增加一个EXecutionHandler (最小，最大的chanel内存是可选项)

```
bootstrap.setPipelineFactory(new ChannelPipelineFactory() {
    @Override
    public ChannelPipeline getPipeline() throws Exception {
        return Channels.pipeline(
                executionHandler1,//sharable
                new MessageDecoderHandler(),
                new MessageEncoderHandler(),
                executionHandler2,//sharable
                new BusinessLogicHandler1(),
                new BusinessLogicHandler2());
    }
});
```

- 设置channel的writeBufferHighWaterMark 的值（确认设置比较大的值不会引起堵塞）


```
bootstrap.setOption("writeBufferHighWaterMark", 10 * 64 * 1024);
```


- 设置SO_READ, SO_WRITE的 缓存大小

```
bootstrap.setOption("sendBufferSize", 1048576);
bootstrap.setOption("receiveBufferSize", 1048576);
```

- 开启TCP无延迟

```
bootstrap.setOption("tcpNoDelay", true);
```
------------------------------

- [Netty相关文章和书籍](http://stackoverflow.com/questions/11357480/books-and-articles-on-netty)

当你需要搜索的时候，谷歌是更好的选择！

**追加：需翻墙**

- [Netty Tutorial Part 1: Introduction to Netty](http://seeallhearall.blogspot.tw/2012/05/netty-tutorial-part-1-introduction-to.html?ref=dzone)
- [Netty Tutorial Part 1.5: On Channel Handlers and Channel Options](http://seeallhearall.blogspot.com/2012/06/netty-tutorial-part-15-on-channel.html)


----------------------------

- [找出当前Java虚拟机有哪些已经打开的网络sockets](http://stackoverflow.com/questions/11669554/finding-out-what-network-sockets-are-open-in-the-current-java-vm)

可以加个钩子在`java.net.Socket`和`java.net.ServerSocket`上，而且监视所有这些类的新实例。完整代码可以在[资源库](https://github.com/orfjackal/jumi/blob/ff51720444cc23d7bfc102ba07a6211cbe9b2f7a/end-to-end-tests/src/test/java/fi/jumi/test/ReleasingResourcesTest.java)里面找到。
下面是该方法的概述：

当一个Socket 或者ServerSocket被安装以后，第一件事情是在其构造函数里面调用`setImpl()`方法，它是真正实现socket功能的类。默认的实现是`java.net.SocksSocketImpl`的实例
，但是它可以被自定义的`java.net.SocketImplFactory`覆盖





























