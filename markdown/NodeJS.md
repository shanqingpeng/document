### 一、NodeJS入门

#### 1、NodeJS是什么

- 开源的、跨平台的JavaScript运行时环境
- 通俗的说：NodeJS就是一款能够运行JavaScript的应用程序

#### 2、NodeJS有什么用

- 开发服务器应用：
  - 如B站
- 开发工具类应用
  - Webpack: 前端打包工具
  - Vite：新型的前端构建工具
  - Babel：一款JavaScript编译器
- 开发桌面端应用
  - VSCode：前端代码编辑器
  - Figma：设计工具
  - Postman：接口测试工具

#### 3、NodeJS安装

- 中文官网地址：https://nodejs.org/zh-cn

- 下载安装包，执行安装

- 检查是否安装成功

  ```shell
  node -v
  ```

#### 4、使用Node执行JS代码

- 语法格式

  ```shell
  node 文件名.js
  ```

- 注意事项
  - Node.js中不能使用```BOM```和```DOM```的API，可以使用```console```和定时器API
  - Node.js中的顶级对象为```global```，也可以用```globalThis```访问顶级对象



### 二、Buffer

#### 1、Buffer介绍

- Buffer为缓冲区，是一个类似于Array的对象，用于表示固定长度的字节序列
- 换句话说，Buffer就是一段固定长度的内存空间，用于处理二进制数据
- Buffer的特点：
  - Buffer大小固定且无法调整
  - Buffer性能较好，可以直接对计算机内存进行操作
  - 每个元素的大小为一个字节（byte）

#### 2、Buffer操作

- 创建Buffer

  ```javascript
  // 方式1: 分配一个长度为10个字节的Buffer (会把原来内存中的数据清零)
  let buf_1 = Buffer.alloc(10);
  console.log('buf_1', buf_1);
  
  // 方式2：分配一个长度为20个字节的Buffer（不安全, 会保留原来内存中的数据）
  let buf_2 = Buffer.allocUnsafe(20);
  console.log('buf_2', buf_2);
  
  // 方式3：把一个字符串或数组转化为Buffer
  let buf_3 = Buffer.from('hello');
  console.log('buf_3', buf_3);
  
  let buf_4 = Buffer.from([105, 106, 107]);
  console.log('buf_4', buf_4);
  ```

- Buffer转为字符串

  ```javascript
  let buf_5 = Buffer.from([110, 114, 119]);
  console.log('buf_5', buf_5);
  
  var convert_1 = buf_5.toString();
  console.log('convert_1', convert_1);
  ```

- 修改Buffer的内容

  ```javascript
  let buf_6 = Buffer.from([120, 122, 123]);
  buf_6[0] = 111;
  var convert_2 = buf_6.toString();
  console.log('convert_2', convert_2);
  ```

- Buffer溢出

  ```javascript
  // 八位二进制存放最大的数为255, 超过255的数, 高位将被丢弃, 只保留8位二进制
  let buf_7 = Buffer.from([125, 110, 120]);
  buf_7[0] = 365;
  var convert_3 = buf_7.toString();
  console.log('convert_3', convert_3);
  ```

- Buffer存放中文

  ```javascript
  // UTF-8编码, 一个中文占3个字节
  let buf_8 = Buffer.from("你好");
  console.log('buf_8', buf_8);
  ```

  

### 三、计算机基础

### 四、fs模块

#### 1、文件写入

```javascript
const fs = require('fs');

// 1、异步写入
fs.writeFile('座右铭.txt', '三人行, 则必有我师焉', error => {
    if(error) {
        console.log('写入失败');
        return;
    }
    console.log('写入成功');
});
console.log('异步写入');

// 2、同步写入
fs.writeFileSync('座右铭sync.txt', '三人行, 则必有我师焉 sync ', error => {
    if(error) {
        console.log('写入失败');
        return;
    }
    console.log('写入成功');
});
console.log('同步写入');

// 3、异步文件追加
fs.appendFile('座右铭.txt', '\r\n 择其善者而从之, 择其不善者而改之', error => {
    if(error) {
        console.log('追加失败');
        return;
    }
    console.log('追加成功');
});


// 4、同步文件追加
fs.appendFileSync('座右铭sync.txt', '\r\n 择其善者而从之, 择其不善者而改之 sync', error => {
    if(error) {
        console.log('追加失败');
        return;
    }
    console.log('追加成功');
});
```

#### 2、文件读取

#### 3、文件移动和重命名

#### 4、文件删除

#### 5、文件夹操作

#### 6、查看资源状态

#### 7、相对路径问题



### 五、path模块

```javascript
const path = require('path');

// 1、拼接规范的绝对路径
// __dirname: 当前文件所在目录的绝对路径
console.log('1、拼接规范的绝对路径resolve: ', path.resolve(__dirname, './index.html'));

// 2、获取操作系统的路径分隔符sep
console.log('2、获取操作系统的路径分隔符sep: ', path.sep);

// 3、解析并返回对象parse
// __filename: 当前文件的绝对路径
console.log('3、解析并返回对象parse: ', path.parse(__filename));

// 4、获取文件名+扩展名
console.log('4、获取文件名+扩展名basename: ', path.basename(__filename));

// 5、获取文件所在目录的绝对路径
console.log('5、获取文件所在目录的绝对路径dirname: ', path.dirname(__filename))

// 6、获取文件的扩展名
console.log('6、获取文件的扩展名extname: ', path.extname(__filename));
```



### 六、HTTP协议

#### 1、http协议简介

- http协议全称超文本传输协议，是互联网应用最广泛的协议之一
- 它是基于TCP协议的应用层传输协议
- 简单来说就是客户端与服务器端进行数据传输的一种规则

#### 2、请求

#####  （1）请求报文结构

- 由请求行、请求头、请求体组成

##### （2）请求行

- 由```请求方式```、```URL```、```HTTP版本号```组成
- 请求方式：
  - GET：主要用于获取数据
  - POST：主要用于新增数据
  - PUT、PATCH：主要用于更新数据
  - DELETE：主要用于删除数据
  - HEAD、OPTIONS、CONNECT、TRACE：使用相对较少

- URL：统一资源定位符，用于定位资源，由协议名、主机名、端口号、路径、查询字符串组成
  - 协议名：http、https
  - 主机名：域名、IP地址
  - 端口号：默认端口号为80
  - 路径：用于定位资源
  - 查询字符串：向服务器传递参数
- HTTP版本号：
  - 1.0是1996年发布的
  - 1.1是1999年发布的
  - 2是2015年发布的
  - 3是2018年发布的

##### （3）请求头

- 由一系列键值对组成
- 记录客户端（一般是浏览器）相关的信息，如
  - 用户代理信息：浏览器版本、操作系统版本、浏览器内核
  - 浏览器能够处理的数据格式
  - 浏览器支持的压缩方式
  - 浏览器支持的语言
  - Cookie

- 记录浏览器与服务器之间交互的行为，如
  - connection保持连接
  - 升级为HTTPS

- 记录与请求体相关的信息
- 请求头相关字段解释：https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers

##### （4）请求体

- 请求体格式非常灵活，常用的有JSON和字符串

#### 3、响应

##### （1）响应报文结构

##### （2）响应行

- 由```HTTP版本号```、```响应状态码```、```响应状态描述```组成
- 响应状态码：
  - 200：请求成功
  - 403：禁止请求
  - 404：找不到资源
  - 500：服务器内部错误
- 响应状态描述
  - 查找响应状态码对应的描述：https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status

##### （3）响应头

- 记录与服务器相关的信息，如

  - 服务器型号

  - 服务器响应时间

    

- 记录与响应体相关的信息，如
  - 内容类型：声明响应体的格式与字符集
  - 内容长度：记录响应体的长度，单位是字节

##### （4）响应体

- 响应体内容格式非常灵活，常见的有：
  - HTML
  - CSS
  - JavaScript
  - 图片
  - 视频
  - JSON

#### 4、IP地址

##### （1）IP地址是什么

- IP地址本质是一个32位的二进制数
- 为了方便：把32位的二进制数分为4组，每组8位二进制，再把每一组的二进制转化为10进制，用点分隔开。如
  - 192.168.1.3

##### （2）IP地址有什么作用

- 用来表示互联网中的设备，实现设备间通信

##### （3）IP地址的分类

- 本地回环IP
  - 127.0.0.1 ~ 127.255.255.254
- 局域网IP（私网IP）
  - 192.168.0.0 ~ 192.168.255.255（常用）
  - 172.16.0.0 ~ 172.31.255.255（不常用）
  - 10.0.0.0 ~ 10.255.255.255（不常用）
- 广域网IP（公网IP）
  - 除上述外，都是广域网IP地址

#### 5、端口号

- 端口是应用程序的数字表示
- 一台现代计算机，有65536个端口号（0 ~ 65535）
- 一个应用程序可以使用一个或多个端口号
- 端口的主要作用：实现不同主机应用程序之间的通信



### 七、http模块

#### 1、创建服务器

```javascript
// 1、导入http模块
const http = require('http');

// 2、创建服务器对象, 回调函数在每次客户端发送请求后执行
const server = http.createServer((request, response) => {
    // 解决响应中文乱码问题
    response.setHeader('content-type', 'text/html;charset=utf-8');
    response.end('Hello Http Server');
});

// 3、监听端口, 启动服务器, 回调函数在服务器成功启动后执行
server.listen(9000, () => {
    console.log('服务器已启动...');
});
```

#### 2、注意事项

- 按 ```Ctrl + C```停止服务器

- 解决响应中文乱码问题

  ```javascript
  response.setHeader('content-type', 'text/html;charset=utf-8');
  ```

- 端口号被占用

  - 关闭占用端口号的应用程序
  - 修改端口号

- HTTP协议默认端口是80
- HTTPS协议默认端口是443

#### 3、获取请求报文

```java
// 创建服务器对象, 回调函数在每次客户端发送请求后执行
const server = http.createServer((request, response) => {
    // (1) 获取请求行
    console.log('1、请求方式: ', request.method);
    console.log('2、请求URL: ', request.url);
    console.log('3、HTTP协议版本号: ', request.httpVersion);
    
    // (2) 获取请求头
    console.log('4、HTTP请求头: ', request.headers);
    
    // (3) 获取请求体(了解, 后续有更简单的方式)
    let body = '';
    request.on('data', chunk => {
        body += chunk;
    });
    request.on('end', () => {
        console.log('5、请求体: ', body)
    })
        
    // (4) 获取URL中的路径和参数, 方式1 (旧版)
    // parse函数, 第二个参数传入true, 则返回的对象中的query会变为一个对象
    let res = url.parse(request.url, true);
    console.log('6、解析URL后生成的对象: ', res);
    console.log('7、请求URL中的路径: ', res.pathname);
    console.log('8、请求URL中的查询字符串: ', res.query);
    
    // (5) 获取URL中的路径和参数, 方式2 (推荐)
    const newUrl = new URL(request.url, 'http://127.0.0.1/9000');
    console.log('9、解析URL后生成的对象: ', newUrl);
    console.log('10、请求URL中的路径: ', newUrl.pathname);
    console.log('11、请求URL中的查询字符串: ', newUrl.searchParams);
    
    response.setHeader('content-type', 'text/html;charset=utf-8');
    response.end('Hello Node');
});
```

#### 4、设置响应报文

```javascript
// 创建服务器对象, 回调函数在每次客户端发送请求后执行
const server = http.createServer((request, response) => {
	// (1) 设置响应头(可以自定义)
    response.setHeader('content-type', 'text/html;charset=utf-8');

    if (newUrl.pathname === '/login') {
        // (2) 设置响应行
        response.statusCode = 200;
        response.statusMessage = 'success';
        
        // (3) 设置响应体, 可以多次调用, 方式1
        response.write('这是');
        // (4) 设置响应体, 必须调用且只能调用一次, 方式2
        response.end('登录页面');
    } else if (newUrl.pathname === '/register') {
        response.statusCode = 203;
        response.statusMessage = 'almostSuccess';
        response.end('注册页面');
    } else {
        // (5) 设置下响应体, 返回文件
        response.statusCode = 403;
        response.statusMessage = 'not found';
        let readFile = fs.readFileSync('../txt/座右铭.txt');
        console.log('读取到的文件: ', readFile.toString());
        response.end('错误页面');
    }
});
```

#### 5、网页URL绝对路径

- 绝对路径可靠性比较强，容易理解，生产环境推荐使用

| 形式                      | 特点                                                         | 案例                   |
| ------------------------- | ------------------------------------------------------------ | ---------------------- |
| http://域名:端口号/路径名 | 完整形式。直接向目标资源发送请求，一般用于网站的外链         | https://www.baidu.com/ |
| //域名:端口号/路径名      | 与页面URL协议拼接后，形成完整URL，再发送请求。一般大型网站用的比较多。 | //www.baidu.com/web    |
| /路径名                   | 与页面URL协议、主机、端口号拼接，形成完整URL，再发送请求。中小型网站用的比较多 | /web                   |

#### 6、网页URL相对路径

- 当前网页的URL为：http://127.0.0.1:9000/test/a.html

| 形式               | 最终URL                                | 说明                            |
| ------------------ | -------------------------------------- | ------------------------------- |
| ./css/app.css      | http://127.0.0.1:9000/test/css/app.css | ```./```表示当前网页所在的目录  |
| js/app.js          | http://127.0.0.1:9000/test/js/app.js   | 与```./```相同                  |
| ../img/logo.png    | http://127.0.0.1:9000/img/logo.png     | ```../```表示当前网页的上级目录 |
| ../../mp4/show.mp4 | http://127.0.0.1:9000/mp4/show.mp4     | ``../``最多只能到根目录         |

- 相对路径在发送请求时，需要与当前网页的URL路径进行计算，得到完整URL后再，发送请求
- 相对路径依赖当前网页的路径，所以可靠性不高，生产环境不建议使用

### 八、模块化

#### 1、模块化是什么

- 将一个复杂的程序，按照一个规则，拆分成多个模块的过程就是模块化
- 拆分出的模块可以是一个或多个文件，且模块的内部数据是私有的，但是可以通过暴露的方式提供给其他模块使用

#### 2、模块化有什么用

- 减少命名冲突
- 提高代码复用性
- 降低代码维护成本

#### 3、模块暴露数据

```javascript
function hello() {
    console.log('这是hello函数...');
}

let world = 'Node';

// (1) 暴露数据, 方式1
module.exports = {
    hello: hello,
    world: world
}

// (2) 暴露数据, 方式2
// 不能使用 exports = value 的形式暴露数据
exports.hello = hello;
exports.world = world;
```

#### 4、导入模块

- 语法规则

  ```javascript
  const module = require(模块路径);
  
  // 例如:
  // (1) 导入自定义模块
  const index = require('./index.js');
  
  // (2) 导入node.js内置模块
  const fs = require('fs');
  ```

- 自定义的模块，模块路径建议写相对路径，且不能省略 ```./``` 和 ```../```

- ```js```文件和```json```导入时，可以省略扩展名（建议不要省略）

- 导入其他类型文件（如```.abc```、无扩展名文件）时，会被当作```js```文件处理

- 模块路径是一个文件夹时，会检查文件夹下是否存在```package.json```且```package.json```中是否存在```main```属性

  - 如果都存在，尝试导入```main```属性对应的文件，找不到则报错
  - 否则，尝试导入文件夹下的```index.js```和```index.json```，找不到则报错

- 导入node.js内置模块时，模块路径只需要写模块的名字即可，无需写```./``` 或 ```../```

### 九、包管理工具

### 十、express框架

### 十一、MongoDB

### 十二、Mongoose

### 十三、API接口

### 十四、接口测试工具

### 十五、会话控制

 
