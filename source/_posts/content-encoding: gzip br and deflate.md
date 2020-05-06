关于前端性能优化，我们都知道常见的一条是要开启gzip压缩，减少资源体积，但是，content-encoding 除了gzip还有别的选择吗？另外的几项选择都是什么意思呢？今天我们就来捋一捋：

前几天看阮老师的科技爱好者周刊，提到NGINX官方写了[一篇文章](https://www.nginx.com/blog/help-the-world-by-healing-your-nginx-configuration/)介绍如何通过NGINX配置减少互联网流量，其中讲到开启gzip，于是我想起去check一下自己的 [https://vislib.best](https://vislib.best) 开gzip了没有。结果发现，竟然真的没有开gzip，但是……这个 br 又是什么呢：

![https://vis.imfast.io/1.png](https://vis.imfast.io/1.png)

[https://vislib.best](https://vislib.best) 静态资源的 response headers 截图

那么就查一下吧：

MDN上提到了 content-encoding 的五个值，分别是 gzip、compress、deflate、identity、br。

- 其中gzip是最常用的，我们都知道，也是 unix 中 gzip 支持的格式，这个格式是采用的deflate算法。
- compress 是来源于unix的compress程序，但是这个程序已经从大多数的UNIX发行版消失了，很多浏览器也不再支持这一content-encoding 格式了，原因是当年compress的作者为这个算法申请了专利，使用这个算法需要付版权费，所以这个算法逐渐被gzip 或者 deflate替代，而如今虽然当初的专利已经失效，但是gzip已经占据了主导地位了。
- deflate 则是基于deflate算法的压缩，这种算法也是我们日常常见的zip压缩包和png图片格式采用的压缩算法。那既然deflate和gzip采用的压缩是一样的，那它与gzip到底有什么区别呢？其实就压缩算法来说，这二者并没有区别，主要的区别在于二者的校验和算法和数据格式不一样。
- identity 则是表示内容没有经过压缩，也是content-encoding没有设置时候的默认值。
- 那br是啥呢？MDN解释说这是一种使用了Brotli算法的格式，Brotli算法是一种专为http content-encoding设计的压缩算法，是2013-2016年间由谷歌的工程师发明、实现的，br这种格式大约在2016-2017年间被各大主流浏览器和服务器支持。

除了MDN上提到的这五种格式外，还有另外几种官方或者非官方的格式，这里就不一一介绍了，有兴趣的可以去看下维基上的[词条](https://en.wikipedia.org/wiki/HTTP_compression)。

接下来就来对比下这几种压缩格式吧，

首先是为什么gzip比deflate的使用更广泛呢？这二者的压缩算法一样的呀，而且其实deflate的速度要比gzip更快一些的。根据StackOverflow上的一个说法，这是因为很多浏览器多年以来都错误地实现了deflate算法(纳尼？？？具体是什么错误可以看本文的推荐阅读)，导致这一算法的实现逻辑很复杂，在很多浏览器版本上经常出问题，所以deflate的格式也属于不被推荐使用的一种。

那既然gzip和deflate实际上用的是同一种压缩算法，所以接下来就是gzip和br的对比。

具体实验我就不做了，贴一下别人的数据吧：

> Checking the top 1000 URLs on the internet, [brotli performance](https://blogs.akamai.com/2016/02/understanding-brotlis-potential.html) is:
14% smaller than gzip for JavaScript
21% smaller than gzip for HTML
17% smaller than gzip for CSS

可以看出来br的表现要明显优于gzip。当然这里只是压缩率，但是我们知道衡量一种压缩算法的性能压缩率只是一方面，压缩速度也很重要，毕竟我们提高压缩率的目的就是为了减少数据传输的时间，如果传输时间省下来了，却在压缩上多花了时间，那就没什么意义了。

当然这方面也已经有人做过实验了，具体实验数据贴在下面：

![https://vis.imfast.io/2.png](https://vis.imfast.io/2.png)

首先解释一下这个数据，第一列是压缩算法，括号中是压缩算法的level，其中brotli有11个level，gzip有9个level，对每个算法而言，level越高压缩效果越好，但是压缩的速度也就相应越慢。第二列是压缩的比例，第三列是速度，第四列和第五列是压缩速度和大小与gzip默认level(level6)的差异。可以看得出来，通过设置brotli的压缩level，可以达到比gzip压缩速度更高、压缩比也更高的水平，其中brotli的level4是压缩比与速度最平衡的。

但是压缩速度对我们来说也并非那么重要，为什么呢，因为我们的静态资源大多采用cdn的方式加载，因此一般来说静态资源只需要压缩一次，然后我们的cdn就会把压缩过的资源缓存起来，之后再分发资源的时候就不需要再次压缩了，所以更多时候cdn都会采用压缩效率最高的level去压缩。当然，如果是对不经过cdn的动态资源的压缩的话，那最高level就不是一个最好的选择，这时候选择level4的brotli算法更合适。

可以看得出来无论是静态资源还是动态资源，brotli的压缩性能都要比gzip好不少，而且根据caniuse，brotli的兼容性也很不错，主流浏览器基本都支持：

![https://vis.imfast.io/3.png](https://vis.imfast.io/3.png)

而且即使有浏览器不支持这一格式，服务器也可以根据浏览器设置的accept-encoding来提供降级处理，分发gzip压缩的资源，所以可以放心大胆地在你的网站上开启brotli。

那既然如此，如何开启brotli呢？

如果你的静态资源是使用cdn加载的话，可以找到你的cdn提供商，查看是否支持，例如cloudflare 中可以在speed ⇒ Optimization 中找到开启brotli的选项。

如果要在NGINX中配置brotli的话，可以参考[这篇文档](https://docs.nginx.com/nginx/admin-guide/dynamic-modules/brotli/)。

关于brotli和gzip，网上也有不少很有意思的讨论文章，这篇文章也是参考这些文章写的，所以贴在下面，有兴趣的可以点进去看看：

[https://blogs.akamai.com/2016/02/understanding-brotlis-potential.html](https://blogs.akamai.com/2016/02/understanding-brotlis-potential.html)

[https://stackoverflow.com/questions/388595/why-use-deflate-instead-of-gzip-for-text-files-served-by-apache](https://stackoverflow.com/questions/388595/why-use-deflate-instead-of-gzip-for-text-files-served-by-apache)

[https://expeditedsecurity.com/blog/nginx-brotli/](https://expeditedsecurity.com/blog/nginx-brotli/)

[https://www.alonehero.com/2019/10/13/gzip与brotli压缩算法对比/](https://www.alonehero.com/2019/10/13/gzip%E4%B8%8Ebrotli%E5%8E%8B%E7%BC%A9%E7%AE%97%E6%B3%95%E5%AF%B9%E6%AF%94/)

[http://www.freeoa.net/scheme/deploy/gzip-and-deflate-webapp_3249.html](http://www.freeoa.net/scheme/deploy/gzip-and-deflate-webapp_3249.html)