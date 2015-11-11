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
或者消耗更底层的资源。