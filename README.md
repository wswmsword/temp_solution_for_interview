# 题解

第一题、

```javascript
""
undefined
```

第二题、

在本地开发遇到跨域问题，这是因为浏览器的同源策略，域名、端口号和接口的域名、端口号不一致，如果只需要在本地开发，可以本地开启 Nginx 反向代理，如果需要在生产环境使用，需要让目标服务器开启 cors 服务。

第三题、

```javascript
function List() {
  this.clear();
  const argsAry = Array.from(arguments);
  this.add.apply(this, argsAry);
}
List.prototype.add = function() {
  const argsAry = Array.from(arguments);
  const addedLength = argsAry.length;
  this.list = argsAry.reduce((acc, cur) => {
    const [key, val] = cur;
    return acc.concat([[key, val]]);
  }, this.list);
  this.length += addedLength;
}
List.prototype.clear = function() {
  this.list = [];
  this.length = 0;
}
List.prototype.delete = function(key) {
  const idx = this.list.findIndex(item => {
    const [itemKey, ] = item;
    return key === itemKey;
  });
  if (idx < 0) {
    return false;
  }
  this.list.splice(idx, 1);
  --this.length;
  return true;
}
List.prototype.get = function(key) {
  const idx = this.list.findIndex(item => {
    const [itemKey, ] = item;
    return key === itemKey;
  });
  if (idx < 0) return undefined;
  return this.list[idx][1];
}
List.prototype.has = function(key) {
  const idx = this.list.findIndex(item => {
    const [itemKey, ] = item;
    return key === itemKey;
  });
  return idx > -1;
}
```

第四题、

```javascript
function request(methodName, paramObj) {
  return new Promise((resolve, reject) => {
    var params = '?'
    for (var key in paramObj) {
      params += key + '=' + paramObj[key] + '&'
    }
    var url = 'http://test.com/' + methodName + params
    var xhr = new XMLHttpRequest();
    xhr.onload = function (e) {
      const data = e.target.response;
      if (data.ret !== 0) {
        reject("error data");
      } else {
        resolve(data);
      }
    };
    xhr.open('GET', url);
    xhr.send();
  })
}

// 1、getInfo方法
async function getInfo(qq, cb) {
  let uid = null;
  let baseInfo = null;
  let payInfo = null;
  // 获取用户ID
  try {
    const data = await request('getUid', { qq: qq });
    uid = data.uid;
  } catch(e) {
    throw new Error("获取用户ID失败");
  }
  try {
    const data = await request('getUid', { qq: qq });
    baseInfo = data.info;
  } catch(e) {
    baseInfo = {};
  }
  
  try {
    const data = await request('getPayInfo', { uid: uid });
    payInfo = data.info;
  } catch(e) {
    payInfo = {};
  }
  cb({
    ...baseInfo,
    ...payInfo,
  });
}

// 2、开始调用getInfo
getInfo(123456, function (data) { console.log(data) })
```

第五题、

```javascript
function isSymmetryNumber(num){
  if (typeof num !== "number") {
    return false;
  }
  if (num < 0 || num > Number.MAX_SAFE_INTEGER) {
    return false;
  }
  const numStr = num + '';
  const idx = -1;
  const len = numStr.length;
  const isOdd = len % 2 === 1;
  const leftStr = numStr.slice(0, len / 2);
  const rightStr = numStr.slice(isOdd ? len / 2 + 1 : len / 2);
  return leftStr === Array.from(rightStr).reverse().join('');
}
```

第七题、

分层利于编码，结构清晰。例如 controller 层用来处理客户端传递的数据和 service 层返回的数据，并最终相应给客户端；service 层用来和数据库交互，是业务实际处理的地方；router 层处理路由。

## 题目详情

```
1、写出以下程序输出什么，为什么？
const people = {
  name:"",
  hello() {
    console.log(this.name)
  },
  hey:() => {
    console.log(this.name)
  }
}
people.hello()
people.hey()





2、请写出以下报错的原因和解决方案
[图片](https://docimg4.docs.qq.com/image/ppL5n77SkP90iTviYSDSYQ.jpeg?w=1156&h=776)


3、按题目要求编写代码
定义一个列表类List，该类必需包含至少以下两个成员：
- 属性length（表示列表中的元素个数）
- 方法add（向列表添加元素）
其中要求构造函数和add方法可以接收任意个数的参数。



4、按题目要求编写代码
背景：
通过qq号码，调用getUid接口获取用户uid，再依赖uid去分别获取用户基本信息和支付信息。
以下是实习生小明根据需求写的代码。
要求：
请你使用es新语法重写代码，使代码逻辑更加健壮，更加规范，更加清晰，减少嵌套。
function request(methodName, paramObj, cb){
    var params = '?'
    for (var key in paramObj) {
        params += key+'='+paramObj[key] + '&'
    }
    var url = 'http://test.com/'+methodName+params
    var xhr = new XMLHttpRequest();
    xhr.onload = function(e){ cb(e.target.response) };
    xhr.open('GET',url);
    xhr.send();
}

// 1、getInfo方法
function getInfo(qq, cb) {
  // 获取用户ID
  request('getUid',{qq:qq}, function(data){
    if(data.ret === 0){
      var uid= data.uid;

      // 获取用户基本信息
      request('getBaseInfo',{uid:uid}, function(data){
        if(data.ret === 0){
          var baseInfo = data.info;
        }else {
          var baseInfo = {};
        }

        // 获取用户支付信息
        request('getPayInfo',{uid:uid}, function(data){
          if(data.ret === 0){
            var payInfo = data.info;
          }else {
            var payInfo = {};
          }

          // 返回
          cb( Obejct.assign({},baseInfo, payInfo) );

        })

      })

    }else{
      throw new Error("获取用户ID失败");
    }
  })
}

// 2、开始调用getInfo
getInfo(123456,function(data){ console.log(data) })


5、按题目要求编写代码
写一个JS函数，判断数字是否为对称数（比如121从左到右读，和从右到左读都是一样的）
function isSymmetryNumber(num){

}
isSymmetryNumber（121）
isSymmetryNumber（123321）


6、请完成以下环境搭建
● 要求：
1、 使用chrome浏览器；
2、 选择一个HTTPS的网站（没具体要求，任意非敏感的、公开的网站即可）；
3、 浏览器访问该网站，并抓取其随意1条XHR的HTTPS请求包，并保存为SAZ格式文件；
4、 请抓取与个人信息无关的且是公开的请求，并将请求包的 Cookie 和 敏感信息（如个人隐私、登录态）删除；
5、 将上述PDF文档化，包含内容：
第一部分：教程（可以站在这个角度去写文档：假设你该篇文档，后续是给其他人看完，能快速搭建起环境的一篇参考文档，要求包含如何搭建环境、如何保存、如何去除敏感信息） ；
第二部分：记录自己的理解和原理；

● 交付物：
1、 saz文件
2、 pdf文档

● 提交：
最终将交付物（不做压缩）发给面试官（可直接发送或者通过网盘形式），面试官会根据你的交付物做进一步的面试和提问


7、附加题：按题目要求回答
附加题：考察NODEJS相关知识点，了解该知识点或喜欢钻研的同学可以尝试，加分题
常用的NodeJS Web框架（如KoaJS、ExpressJS、EggJS、NestJS），一般会抽象出这样的架构分层概念：Router层、Controller层、Service层，请阐述它们在实际编码中的作用。


8、附加题：按题目要求编写代码
附加题：考察MYSQL相关知识点，了解该知识点或喜欢钻研的同学可以尝试，加分题
现在需要在MYSQL的 test 数据库里，创建一张 广告表（table_name=ad），大致要求如下：
id（自增ID）	game_code（游戏英文缩）	game_type（游戏类型）	channel（渠道）	spend（渠道花费）
1	lol	moba	A	5
2	cf	fps	A	10
3	wzry	moba	B	6
4	pubgm	fps	B	11
5	pubgm	fps	A	1

● 可自己本地搭建MYSQL服务，或者使用在线SQL引擎（如 http://sqlfiddle.com/）
● 要求使用 MYSQL 语法：
1、 写出 创建表 和 插入上述模拟数据 的SQL Build Schema语句
2、 写出 查找每个【channel】里，按【spend】最高排序的【game_code】 的SQL语句，最终呈现的结果如下：
game_code（游戏英文缩）	channel（渠道）	spend（渠道花费）
lol	A	11
wzry	B	6
```