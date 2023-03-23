#### 一、模块化

##### 1、模块化是什么

#####  2、模块化的好处

- 降低代码耦合度
- 提高代码复用性
- 便于代码维护

#####  3、怎么样模块化

- 创建模块：简单来说，创建一个```js```文件就是创建一个模块

- 暴露变量

  ```javascript
  // 方式一: 使用 exports 暴露变量
  exports.x = 10;
  exports.y = 20;
  exports.sayHello = function(name) {
      console.log('hello ' + name);
  }
  
  // 方式二: 使用 module.exports 暴露变量
  module.exports.x = 10;
  module.exports.y = 20;
  module.exports.sayHello = function(name) {
      console.log('hello ' + name);
  }
  
  // 也可以一次暴露多个变量
  module.exports = {
      x: 10,
      y: 20,
      sayHello: function(name) {
          console.log('hello ' + name);
      }
  }
  ```
  
- 引入模块

  ```javascript
  // 1、使用require()函数引入模块, 需要传入模块的路径(字符串类型)
  // 2、如果模块的路径为相对路径, 必须以./或者../开头, 表示当前路径
  // 3、如果引入模块的路径中包含.js作为后缀, 则.js可以省略
  var module_01 = require('./module-01.js');
  console.log(module_01);
  
  // 控制台打印:
  { a: 10, b: 20, sayHello: [Function: sayHello] }
  ```

  

#### 二、```npm```简介

##### 1、```npm```是什么

##### 2、```npm```常用命令

- 查看```npm```版本： ```npm -v``` 

  ```shell
  PS D:\code\github\nodejs\node> npm -v
  8.15.0
  ```

- 查看所有模块版本：```npm version```

  ```shell
  PS D:\code\github\nodejs\node> npm version
  {
    npm: '8.15.0',
    node: '16.17.0',
    v8: '9.4.146.26-node.22',
    uv: '1.43.0',
    zlib: '1.2.11',
    brotli: '1.0.9',
    ares: '1.18.1',
    modules: '93',
    nghttp2: '1.47.0',
    napi: '8',
    llhttp: '6.0.7',
    openssl: '1.1.1q+quic',
    cldr: '41.0',
    icu: '71.1',
    tz: '2022a',
    unicode: '14.0',
    ngtcp2: '0.1.0-DEV',
    nghttp3: '0.1.0-DEV'
  }
  ```

- 搜索```npm```包：```npm search 包名```

  ```shell
  PS D:\code\github\nodejs> npm search math
  NAME                      | DESCRIPTION          | AUTHOR          | DATE       | VERSION  | KEYWORDS
  math                      | Mathematical…        | =kzh            | 2011-09-19 | 0.0.3    |
  node-int64                | Support for…         | =broofa…        | 2015-04-04 | 0.4.0    | math integer int64
  mathjs                    | Math.js is an…       | =josdejong      | 2022-11-07 | 11.3.3   | math mathematics functions numeric algebra parser expression number bignumber complex fraction matrix unit
  is-number                 | Returns true if a…   | =doowb…         | 2018-07-04 | 7.0.0    | cast check coerce coercion finite integer is isnan is-nan is-num is-number isnumber isfinite istype kind math nan num number numericsimple-statistics         | Simple Statistics    | =tmcw           | 2022-10-26 | 7.8.0    | descriptive linear math probability regression statistics
  big-integer               | An arbitrary length… | =peterolson     | 2021-11-10 | 1.6.51   | math big bignum bigint biginteger integer arbitrary precision arithmetic
  ```

- 安装```npm```包：```npm install 包名``` 或者 ```npm i 包名```

  ```shell
  # 1、安装npm, 不保存到package.json的依赖中
  PS D:\code\github\nodejs> npm install math
  # 2、安装npm, 简写形式, 不保存到package.json的依赖中
  PS D:\code\github\nodejs> npm i math
  # 3、安装npm包,简写形式, 并保存到package.json的依赖中
  PS D:\code\github\nodejs> npm i math --save
  # 4、npm包全局安装（一般都是一些系统工具）
  PS D:\code\github\nodejs> npm i math -g
  
  added 1 package, and audited 2 packages in 1s
  
  found 0 vulnerabilities
  ```

- 删除```npm```包：```npm remove 包名``` 或者 ```npm r 包名```

  ```shell
  # 1、删除npm包
  PS D:\code\github\nodejs> npm remove math
  # 2、删除npm包, 简写形式
  PS D:\code\github\nodejs> npm r math
  
  up to date, audited 1 package in 200ms
  
  found 0 vulnerabilities
  ```