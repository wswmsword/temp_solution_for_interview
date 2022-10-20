试题

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

