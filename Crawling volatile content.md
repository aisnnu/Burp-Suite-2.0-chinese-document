现代web应用经常包含不稳定的响应内容，具体指同样的url或函数会根据情景不同返回不同的响应内容。例如社交媒体的动态，用户评论，内联广告或者随机推荐内容会产生这种行为。

当前Burp爬虫的实现对这类响应内容处理的不好。它单独获取每个请求，然后处理响应寻找新的链接，无视响应的版本。

Burp的[新爬虫](https://github.com/Boreas514/Burp-Suite-2.0-chinese-document/blob/master/Burp's%20new%20crawler.md)采用了完全不同的方法，它不对URL结构做任何假设，只根据预期的路径进行爬取，并根据响应内容确定应用中各功能的位置。这意味着他可以健壮的处理这种不稳定的响应内容。

新的爬虫采用了多种算法来定位不稳定的响应内容背后的功能，也可以正确识别从不同地方访问到的同一个功能。这样，爬虫就可以专注于返回一套应用响应的“核心”元素，发现到不同应用和函数的关键url路由才是侦查的关键：![](https://portswigger.net/cms/images/00/f5/daa6692f160c-article-crawling-9.png)

在某些情况下其实是一个模板渲染出来的，但是返回的内容却不一样（比如携程旅游产品详情页）。这种情况下爬虫会当做是不同的功能抓取这些不同的响应内容，然后绘制一个不确定边缘的图。虽然这么做不是很好，但爬虫仍然可以可靠地找到这些变化的连接背后的内容：![](https://portswigger.net/cms/images/a3/c4/a60164ce8253-article-crawling-10.png)