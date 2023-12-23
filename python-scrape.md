
# snippet 

用 Python 发送 HTTP 请求，1行 requests 有很多问题，比如如果对方服务挂了，程序就永远 hang 在发送请求的地方了。

这段 snippet 带有重试，连接保持，超时，基本上所有发送 HTTP 的地方都要这么做。

https://gist.github.com/laixintao/e9eae48a835e741969ae06af3ad45f71

https://x.com/qq3qiyu/status/1737728033384206710?s=20

看到有人讨论 python requests 的 timeout 问题，简而言之就是发现 requests 的总耗时会超过你设置的 timeout。

其实原理很简单，requests 支持两种 timeout：

* connect timeout: 建立连接的超时时间
* read timeout: 服务端每次响应的超时时间

默认情况下，timeout 设置的是 connect timeout + read timeout，你也可以通过 tuple 的形式分别设置。

需要注意的是，requests 并不支持 total timeout，就是实际上你没法控制这个 requests 请求总共会花多长时间。举一个极端的例子，如果这个服务端总能在每隔 read timeout 内回复点数据，那么这个请求实际上可以耗费无穷长的时间。

requests 官方的解释是，市面上已经有其他控制 total timeout 的包了，requests 没必要再做一个。这个第三方包就是 https://github.com/pnpnpn/timeout-decorator ，它通过设置 ITIMER_REAL 信号来实现对函数总耗时的控制。但是这个包仅支持主线程。

如果你使用 asyncio，那么 asyncio.wait_for 可以很轻松地为 coroutine 设置总耗时。aiohttp 的 timeout 看上去也是 total timeout。

注意：asyncio.wait_for 只是停止等待，杀 coroutine 需要另外手动处理（task.cancel()）。被杀的 coroutine 也需要经常通过 await asyncio.sleep(0) 检查自己有没有被杀。


# twitter crawler 

https://github.com/kaixinol/twitter_user_tweet_crawler

