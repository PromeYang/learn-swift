# URL操作

## NSURL

* IOS2.0 >>

用于创建一个URL对象, 可以实现访问各种属性, 可以读取文件的路径

**P.S. 将传进来的NSString 进行 UTF8 转码解决NSURL初始化失败**

## NSURLComponents

* IOS7.0 >>
* based on RFC 3986

跟NSURL类似的用法, 但不同之处在于，URL component属性是 readwrite 的。它提供了安全直接的方法来修改URL的各个部分.如果尝试赋值一个非法的scheme或port，会抛出一个异常.提供[percent-encoded]方法.

## NSURLConnection

* IOS2.0 >>

负责发送请求，建立客户端和服务器的连接。发送NSURLRequest的数据给服务器，并收集来自服务器的响应数据.可以取消正在发送的请求.

## NSURLRequest

* IOS2.0 >>
* 没有set方法, 如果要设置例如header的话需要使用NSMutableURLRequest来实现

用来进行URL请求, 请求的时候可以指定一个缓存的策略

## NSURLResponse

* IOS2.0 >>

用于接受URL请求的响应, 获取响应的数据

## NSURLCache

* IOS2.0 >>
* NSURLCache在每个UIWebView的的NSURLRequest请求中都会被调用。
* iOS设备上NSURLCache默认只能进行内存缓存。可以通过子类化NSURLCache来实现自定义的版本从而实现在DISK上缓存内容。
* 需要重写cachedResponseForRequest，这个会在请求发送前会被调用，从中我们可以判定是否针对此NSURLRequest返回本地数据。

可以用来将远程web服务器获取的数据缓存起来，减少对同一个url多次请求。

## NSURLSession

* IOS7.0 >>
* 如果用户强制将程序关闭，NSURLSession会断掉。
* 有三种task类型, data upload download

是新的网络接口, 跟NSURLConnection是类似的, 提供以下功能, 不同点是可以后台处理 

1.通过URL将数据下载到内存

2.通过URL将数据下载到文件系统

3.将数据上传到指定URL

4.在后台完成上述功能

## NSURLProtocol

* IOS2.0 >>

用来重定义整个URL Loading System, 对所有的请求可以进行统一的处理, 也可以自定义实现请求协议, 例如实现跟webview中js的通讯协议.主要功能:

1.自定义请求和响应

2.提供自定义的全局缓存支持

3.重定向网络请求

4.提供HTTP Mocking (方便前期测试)

5.其他一些全局的网络请求修改需求

## NSURLQueryItem

* IOS8.0 >>

主要用户来获取URL上的参数

## NSURLCredential

* IOS2.0 >>

安全要求更高的 web 服务，可能会要求请求的发送方提供一张或多张证书以确认身份。

程序可以保留 credential, 并有三种保留模式。


