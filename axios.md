# Axios 篇

Axios 是一个基于 Promise 的 HTTP 库,可以用在浏览器和 node.js 中。

> Axios 的特点包括:
>
> - 从浏览器中创建 XMLHttpRequests
> - 从 node.js 创建 http 请求
> - 支持 Promise API
> - 拦截请求和响应
> - 取消请求
> - 自动转换 JSON 数据
> - 客户端支持防御 [XSRF](./terminology.md/#xsrf)
>
> Axios 的诞生是为了解决 Angular 中`$http` 的一些问题,像`$http` 在处理多个请求的时候需要设置头部 OPTION 选项,而 Axios 直接支持多个并发请求.
>
> Axios 的工作原理是,当使用 axios 发起请求时,Axios 会返回一个 Promise,我们通过这个 Promise 的回调函数获取响应。
>
> 在这个 Promise 中,Axios 并不是直接发送网络请求,而是会使用一个 Adapter 即适配器。
>
> 适配器负责发送真正的网络请求,并将响应结果封装成 Promise 返回。Axios 默认使用的适配器是基于 XMLHttpRequest 的适配器。
>
> 我们也可以通过配置自定义适配器来实现使用其他方式发送请求,比如 node.js 的 http API。

[Axios](https://www.axios-http.cn/docs/req_config) 提供了非常强大的请求和响应处理功能:

> - 请求拦截器(Request Interceptor):在请求发送前可以对请求进行处理,比如在每个请求头里加上 token
> - 响应拦截器(Response Interceptor):在接收到响应后可以对响应进行处理,比如对结果进行过滤
> - 请求取消(Cancel Token): 通过传递 cancel token 可以取消已经发送的请求
> - 错误处理(Error Handling):默认情况下 httperror 会 reject promise,可以通过配置来对错误进行处理
> - 请求/响应数据转换(Transform):可以对请求数据和响应数据进行转换操作
> - 配置默认值(Defaults):可以设置通用的请求配置默认值
> - 实例和自定义配置:可以创建 axios 实例并配置独立的默认值
> - 支持 Promise: 通过 then 和 catch 处理请求结果
> - 支持 async/await: 使用 async/await 来更优雅地处理异步请求
> - 支持 TypeScript: 提供了类型声明文件方便 TS 项目使用

Axios 与原生 XHR 的区别:

> - Axios 是基于 Promise 实现,支持 async/await
> - XHR 基于回调函数
> - Axios 具有更强大和灵活的拦截器机制
> - Axios 默认将响应 json 解析为 JavaScript 对象
> - Axios 支持请求和响应数据转换
> - Axios 在 node.js 中也可以用,XHR 只能在浏览器中用
> - Axios 支持请求取消
> - Axios 支持防御 XSRF

Axios 的适用场景:

> - 需要兼容低版本浏览器的项目,可以使用 Axios 而不需要考虑 XHR 的兼容性问题
> - 需要支持服务端的项目,Axios 可以同时在浏览器和 Node.js 中使用
> - 需要拦截请求和响应的项目,Axios 提供强大的拦截器功能
> - 需要取消请求的项目,Axios 支持通过 Cancel Token 取消请求
> - 对性能有较高要求的项目,Axios 体积小速度快
> - 需要处理异步请求的项目,Axios 基于 Promise,支持 async/await

Axios 请求拦截器中的 config 对象的常见参数配置

> - url: 请求地址
> - method: 请求方法,如'GET','POST'
> - baseURL: 请求基地址
> - headers: 请求头信息
> - params: GET 参数
> - data: 请求主体数据
> - timeout: 请求超时时间
> - withCredentials: 是否携带 cookie
> - auth: 鉴权信息
> - responseType: 响应类型,如'json'
> - xsrfCookieName: xsrf cookie 的 key: `default(默认)`_`'XSRF-TOKEN'`_
> - xsrfHeaderName: 请求 headers 中 token 对应的 header key:`default(默认)`_`'X-XSRF-TOKEN'`_
> - onUploadProgress/onDownloadProgress: 上传/下载进度回调
> - cancelToken: 用于取消请求的 token
> - validateStatus: 定义 resolve 的 http 返回状态码
> - paramsSerializer: 参数序列化函数
> - maxContentLength: 响应内容最大长度
>
