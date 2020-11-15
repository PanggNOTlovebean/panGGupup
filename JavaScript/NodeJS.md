# Node.js

### 模块

```javascript
//export (main.js)
var s = 'Hello';

function greet(name) {
    console.log(s + ', ' + name + '!');
}

module.exports = greet;


//require
//相对路径
var greet = require('./hello');

//Node会依次在内置模块、全局模块和当前模块下查找hello.js，你很可能会得到一个错误：
var greet = require('hello');
```

### WebSocket

因为HTTP协议是一个请求－响应协议，请求必须先由浏览器发给服务器，服务器才能响应这个请求，再把数据发送给浏览器。换句话说，浏览器不主动请求，服务器是没法主动发数据给浏览器的。

HTML5推出了WebSocket标准，让浏览器和服务器之间可以建立无限制的全双工通信，任何一方都可以主动发消息给对方。

### Content-Type

#### application/x-www-form-urlencoded

HTTP会将请求参数用key1=val1&key2=val2的方式进行组织，并放到请求实体里面，注意如果是中文或特殊字符如"/"、","、“:" 等会自动进行URL转码。不支持文件，一般用于表单提交。

![application/x-www-form-urlencoded请求参数](https://img-blog.csdnimg.cn/20190111174842982.png)



![application/x-www-form-urlencoded报文](https://img-blog.csdnimg.cn/20190624231322976.png)



#### multipart/form-data

与application/x-www-form-urlencoded不同，这是一个多部分多媒体类型。首先生成了一个 boundary 用于分割不同的字段，在请求实体里每个参数以------boundary开始，然后是附加信息和参数名，然后是空行，最后是参数内容。多个参数将会有多个boundary块。如果参数是文件会有特别的文件域。最后以------boundary–为结束标识。multipart/form-data支持文件上传的格式，一般需要上传文件的表单则用该类型。

![multipart/form-data请求参数](https://img-blog.csdnimg.cn/20190111174827455.png)

![multipart/form-data报文](https://img-blog.csdnimg.cn/20190624231435322.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE0ODY5MDkz,size_16,color_FFFFFF,t_70)

#### application/json

JSON 是一种轻量级的数据格式，以“键-值”对的方式组织的数据。使用这个类型，需要参数本身就是json格式的数据，参数会被直接放到请求实体里，不进行任何处理。服务端/客户端会按json格式解析数据（约定好的情况下）

![application/json请求参数](https://img-blog.csdnimg.cn/20190111180712373.png)

![application/json报文](https://img-blog.csdnimg.cn/20190624231555162.png)



#### application/xml 和 text/xml

与application/json类似，这里用的是xml格式的数据，text/xml的话，将忽略xml数据里的编码格式。

#### request 的Content-Type

#### 建议：

1. 如果是一个restful接口（json格式），一般将Content-Type设置为application/json; charset=UTF-8；
2. 如果是文件上传，一般Content-Type设置为multipart/form-data
3. 如果普通表单提交，一般Content-Type设置为application/x-www-form-urlencoded

#### response的Content-Type

response的Content-Type设置建议：

1. 一般情况下不需要显示设置；
2. 如果是文件导出，Content-Type 设置为 multipart/form-data，并且添加一个Content-Disposition设置为attachment;fileName=文件.后缀。
   *注：Content-Disposition是Content-Type的扩展，告诉浏览器弹窗下载框，而不是直接在浏览器里展示文件。因为一般浏览器对于它能够处理的文件类型，如txt，pdf 等，它都是直接打开展示，而不是弹窗下载框。*

### REST API

REST请求和普通的HTTP请求有几个特殊的地方：

1. REST请求仍然是标准的HTTP请求，但是，除了GET请求外，POST、PUT等请求的body是JSON数据格式，请求的`Content-Type`为`application/json`；
2. REST响应返回的结果是JSON数据格式，因此，响应的`Content-Type`也是`application/json`。

#### 处理错误

这个问题实际上有两部分。

第一，当REST API请求出错时，我们如何返回错误信息？

第二，当客户端收到REST响应后，如何判断是成功还是错误？

第一类问题 由HTTP错误码解决

第二类问题 Http 200 表示正确 400 表示失败，用里面REST 里面的错误码表示更多的内容

使用字符串作为错误码！

错误代码命名规范为`大类:子类`，例如，口令不匹配的登录错误代码为`auth:bad_password`，用户名不存在的登录错误代码为`auth:user_not_found`。这样，客户端既可以简单匹配某个类别的错误，也可以精确匹配某个特定的错误。