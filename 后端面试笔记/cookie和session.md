## cookie和session的区别是什么？

1. 存储位置不同：cookie存放于客户端；session存放于服务端。
2. 存储容量不同：单个cookie保存的数据<=4KB，一个站点最多保存20个cookie；而session并没有上限。
3. 存储方式不同：cookie只能保存ASCII字符串，并需要通过编码当时存储为Unicode字符或者二进制数据；session中能够存储任何类型的数据，例如字符串、整数、集合等。
4. 隐私策略不同：cookie对客户端是可见的，别有用心的人可以分析存放在本地的cookie并进行cookie欺骗，所以它是不安全的；session存储在服务器上，对客户端是透明的，不存在敏感信息泄露的风险。
5. 生命周期不同：可以通过设置cookie的属性，达到cookie长期有效的效果；session依赖于名为JSESSIONID的cookie，而该cookie的默认过期时间为-1，只需关闭窗口该session就会失效，因此session不能长期有效。
6. 服务器压力不同：cookie保存在客户端，不占用服务器资源；session保管在服务器上，每个用户都会产生一个session，如果并发量大的话，则会消耗大量的服务器内存。
7. 浏览器支持不同：cookie是需要浏览器支持的，如果客户端禁用了cookie，则会话跟踪就会失效；运用session就需要使用URL重写的方式，所有用到session的URL都要进行重写，否则session会话跟踪也会失效。
8. 跨域支持不同：cookie支持跨域访问，session不支持跨域访问。

##  cookie和session各自适合的场景是什么？

对于敏感数据，应存放在session里，因为cookie不安全。

对于普通数据，优先考虑存放在cookie里，这样会减少对服务器资源的占用。

## get请求与post请求有什么区别？

- GET在浏览器回退时是无害的，而POST会再次提交请求。
- GET产生的URL地址可以被Bookmark，而POST不可以。
- GET请求会被浏览器主动cache，而POST不会，除非手动设置。
- GET请求只能进行url编码，而POST支持多种编码方式。
- GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
- GET请求在URL中传送的参数是有长度限制的，而POST没有。
- 对参数的数据类型，GET只接受ASCII字符，而POST没有限制。
- GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
- GET参数通过URL传递，POST放在Request body中。