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































