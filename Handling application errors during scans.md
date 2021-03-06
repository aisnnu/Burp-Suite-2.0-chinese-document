# 扫描时处理应用错误

这种情况你见过多少次了？
![](https://portswigger.net/cms/images/29/f3/bfc0299de4a2-article-too_many_errors.png)
正如之前说的，burp旧的扫描器单独处理每个request。如果运行时出现网络错误，这项扫描就会被丢弃然后继续下一个扫描。这种实现有两个局限性：

- 没有为丢弃的扫描项准备重试机制。如果出现间歇性故障，举个例子目标应用停止响应了几分钟，那么用户必须手动寻找那些被丢弃的扫描项然后重新塞回队列。

- 如果目标应用永久不可用，那么Burp将继续执行，依次启动每个扫描项，导致错误，并最终丢弃它。

burp新的扫描器以一种更健壮的方式实现应用错误处理。包括以下几点：

- Burp以尽可能细致的方式跟踪错误。如果单个操作导致错误，Burp会将该操作标记为失败，然后继续下一个操作。如果在同一个活动级上重复的操作失败了，那么整个活动级将被标记为失败，Burp将接着执行其他内容。

- 在完成扫描的每个阶段之后，Burp可以执行可配置数量的后续遍历，以重试特定的失败操作。这可以处理间歇性中断故障，而不需要用户手动重新扫描任何项目。

- 如果发生了太多错误(连续发生或全部发生)，Burp可以选择暂停整个扫描。这可以避免在应用程序宕机时无限期地工作，并允许用户或应用程序所有者在扫描恢复之前解决问题。

有单独的选项来配置Burp如何在扫描的爬虫和审计阶段处理应用程序错误:
![](https://portswigger.net/cms/images/be/82/467b39f11b9f-article-handling_application_errors_during_crawl.png)

![](https://portswigger.net/cms/images/51/c1/f747360b9801-article-handling_application_errors_during_audit.png)

在扫描的审计阶段，在细致跟踪错误并在重复错误发生时的能力特别有用。Burp首先在单个审计检查级别上跟踪错误，然后是单个插入点，然后是被审计的请求，最后是整个扫描。这很好地处理了web应用防火墙，这些防火墙可能根据某些审计检查中使用的特定有效负载选择性地删除连接，也可以删除对特定参数进行任何修改的连接。
