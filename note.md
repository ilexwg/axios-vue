# Vue中使用axios


## 安装

```bash
npm install axios
```


## 引入
```JavaScript
import axios from 'axios';
```


## 常见请求方法
- get: 获取数据
- post: 提交数据 (表单提交/文件上传)
- put: 更新数据 (将所有数据都推送到后端)
- patch: 更新数据 (只将修改的数据推送到后端)
- delete: 删除数据

具体使用什么方法是由后端同学决定的 (或者和后端同学商量来的)


## get 请求

```JavaScript
const url = 'http://jsonplaceholder.typicode.com/posts';

// 别名
axios.get(url).then(res => console.log(res.data));

// 别名, 带参数
axios.get(url, {
    params: {
        id: 666
    }
}).then(res => console.log(res.data

// axios方法, 不带参
axios({
    method: 'get',
    url
}).then(res => {
    console.log(res.data)
});
// axios方法, 带参
axios({
    method: 'get',
    url,
    params: {
        id: 666
    }
}).then(res => {
    console.log(res.data);
});
```


## post 请求

post请求的两种格式:
- form-data: 用来进行表单提交 (图片上传/文件上传)
- application/json:

==form-data==
```JavaScript
const url = 'http://jsonplaceholder.typicode.com/posts';

const data = {
    id: 666
}

// step1: 创建一个form-data对象
const formData = new FormData();

// step2: 填充form-data对象
for (const key in data) {
    formData.append(key, data[key]);
}

// step3: 发送请求
axios.post(url, formData).then(res => {
    console.log(res.data);
});

```


==application/json==
```JavaScript
// 别名
axios.post('http://example.com', {
    id: 666
}).then(res => {
    console.log(res.data);
});

// axios方法
axios({
    method: 'post',
    url: 'http://example.com',
    data: {
        id: 666
    }
}).then(res => {
    console.log(res.data);
});
```


## put 请求
```JavaScript
const url = 'https://jsonplaceholder.typicode.com/posts/1';

// 别名
axios.put(url, {
    title: 'foo',
    body: 'bar',
    id: 1,
    userId: 1
}).then(res => {
    console.log(res.data);
});

// axios方法
axios({
    method: 'put',
    url,
    data: {
        title: 'foo',
        body: 'bar',
        id: 1,
        userId: 1
    }
}).then(res => {
    console.log(res.data);
});
```


## patch 请求
```JavaScript
const url = 'https://jsonplaceholder.typicode.com/posts/1';

// 别名
axios.patch(url, {
    title: 'foo'
}).then(res => {
    console.log(res.data);
});

// axios方法
axios({
    method: 'patch',
    url,
    data: {
        title: 'foo'
    }
}).then(res => {
    console.log(res.data);
});
```


## delete 请求
```JavaScript
const url = 'https://jsonplaceholder.typicode.com/posts/1';

// 请求参数传递方式需要同后端同学沟通好
// 别名 (参数通过url传递)
axios.delete(url, {
    params: {
        id: 1
    }
}).then(res => {
    console.log(res.data)
});

// 别名 (参数通过请求体传递)
axios.delete(url, {
    id: 1
}).then(res => {
    console.log(res.data);
});

// axios方法 (参数通过url传递)
axios({
    method: 'delete',
    url,
    params: {
        id: 1
    }
}).then(res => {
    console.log(res.data);
});

// axios方法 (参数通过请求体传递)
axios({
    method: 'delete',
    url,
    data: {
        id: 1
    }
}).then(res => {
    console.log(res.data);
});
```

## 并发请求
同时进行多个请求, 并统一处理返回值

```JavaScript
const url1 = 'http://jsonplaceholder.typicode.com/posts/1';
const url2 = 'http://jsonplaceholder.typicode.com/users/1';

// axios.all() 的参数是一个数组, 数组中的每一项是一个axios请求
// axios.all() then的回调是axios.spread()方法
// axios.spread() 的参数是一个回调, 回调的参数是对应axios.all() 参数中数组的请求的
// res1 对应的是 "请求1" 的返回值
// res2 对应的是 "请求2" 的返回值
axios.all([
    axios.get(url1), // 请求1
    axios.get(url2)  // 请求2
]).then(
    axios.spread((res1, res2) => {
        console.log(res1.data, res2.data);
    })
);
```


## axios 实例

什么情况下会使用 axios 实例呢?
> 当有多个后端接口时, 不同接口的 `baseURL` 不一样; 或者不同接口需要设置不同的 `timeout` 时

创建 axios 实例需要使用 `axios.create()` 方法

```JavaScript
const axios1 = axios.create({
    baseURL: 'http://jsonplaceholder.typicode.com/posts',
    timeout: 1000
});

const axios2 = axios.create({
    baseURL: 'http://jsonplaceholder.typicode.com/users',
    timeout: 3000
});

axios1.get('/1').then(res => {
    console.log(res.data);
});

axios2.get('/1').then(res => {
    console.log(res.data);
});
```


## axios 常用配置

```JavaScript
axios.create({
    baseURL: 'http://jsonplaceholder.typicode.com/users', // 请求的域名, 基本地址
    timeout: 2000, // 请求超时时长 (ms)
    url: '/1', // 请求路径
    method: 'POST', // 请求方法: GET, POST, PUT, PATCH, DELETE
    params: { // 请求参数, 通过 params 设置的参数会拼接到 url 上
        id: 666
    },
    data: { // 请求参数, 通过 data 设置的参数会放到请求体中
        id: 666
    },
    headers: { // 请求头
        token: ''
    }
});
```

进行配置的三个地方:
- axios 全局配置

```JavaScript
axios.defaults.baseURL = 'http://jsonplaceholder.typicode.com';
axios.defaults.timeout = 1000;
```

- axios 实例配置

```JavaScript
// 在创建实例时进行配置
const instance = axios.create({
    timeout: 3000
});

// 在创建完实例后进行修改配置
const instance = axios.create();
instance.defaults.timeout = 3000;
```

- axios 请求配置

```JavaScript
axios.get('http://jsonplaceholder.typicode.com', {
    timeout: 5000
});
```

三种配置的优先级:

全局配置 < 实例配置 < 请求配置

也就是说, 如果同时在三个配置中都设置了 `timeout` 参数的值, 那么会以请求配置的值为 `timeout` 的值

举个例子:

```JavaScript
const axios1 = axios.create({
    baseURL: 'http://jsonplaceholder.typicode.com/users',
    timeout: 1000
});

const axios2 = axios.create({
    baseURL: 'http://jsonplaceholder.typicode.com/posts',
    timeout: 2000
});

// baseURL, timeout, method, url, params
axios1.get('/1', {
    params: {
        id: 100
    }
}).then(res => {
    console.log(res.data);
});

// baseURL, timeout: 3000, method, url
axios2.get('/1', {
    timeout: 3000
}).then(res => {
    console.log(res.data);
});
```


## axios 拦截器

拦截器: 在请求或响应处理前拦截他们

分为:
- 请求拦截器
- 响应拦截器

请求拦截器:

```JavaScript
axios.interceptors.request.use(config => {
    // 在发送请求前做些什么
    return config;
}, err => {
    // 请求错误时做些什么
    return Promise.reject(err);
});
```

响应拦截器:

```JavaScript
axios.interceptors.response.use(res => {
    // 请求成功对响应数据做处理
    return res; // return 出来的 res 会到请求成功的 then 中 ☀️
}, err => {
    // 响应错误时做些什么
    return Promise.reject(err);
});

axios.get('http://example.com').then(res => { // res ☀️
    console.log(res.data);
});
```

取消拦截器:

```JavaScript
const interceptor = axios.interceptors.request.use(config => {
    // do sth.
    return config;
});

axios.interceptors.request.eject(interceptor);
```

举个例子:
```JavaScript
// 需要登录状态的请求, eg微博需要登录之后才能发表评论
// 当我们使用拦截器的时候应该创建新的 axios 实例来设置拦截器, 以防止直接在 axios 上设置了对全局造成影响
const instanceWithToken = axios.create();

instanceWithToken.interceptors.request.use(config => {
    config.headers.token = '1234';
    return config;
}, err => {
    return Promise.reject(err);
});

// 不需要登录的接口就可以另创建一个 axios 实例发送请求
const instanceWithoutToken = axios.create();


// 移动端开发, 可以通过拦截器增加页面的体验度
const instanceMobile = axios.create();

instanceMobile.interceptors.request.use(config => {
    $('#loading').show(); // 比如使用了 jQuery 选择了 loading 的浮层, 让浮层展示出来, 让用户知道正在加载中
    return config;
}, err => {
    return Promise.reject(err);
});

instanceMobile.interceptors.response.use(res => {
    $('#loading').hide(); // 请求回数据了, 就把 loading 浮层给隐藏掉
    return res;
}, err => {
    return Promise.reject(err);
});
```


## axios 错误处理

请求错误时进行的处理

```JavaScript
const instance = axios.create();

instance.get('http://example.com').then(res => {
    console.log(res.data);
}).catch(err => {
    console.log(err);
});
```

实际开发过程中一般添加统一的错误处理

```JavaScript
const instance = axios.create();

instance.interceptors.request.use(config => {
    return config;
}, err => {
    // 请求错误: 一般 HTTP 状态码 4xx
    // 401: 超时
    // 404: not found
    $('#error').show();
    setTimeout(() => {
         $('#error').hide();
    }, 2000);

    return Promise.reject(err);
});

instance.interceptors.response.use(res => {
    return res;
}, err => {
    // 响应错误: 一般 HTTP 状态码 5xx
    // 500: 系统错误
    // 502: 系统重启
    $('#error').show();
    setTimeout(() => {
         $('#error').hide();
    }, 2000);

    return Promise.reject(err);
});

// 如果想单独处理某个请求的错误再用 catch 来单独处理
instance.get('http://example.com')
    .then(res => {
        console.log(res.data);
    })
    .catch(err => {
        // 单独处理错误
        console.log(err);
    });
```


## axios 取消请求

用于取消正在进行的 HTTP 请求

实际中很少会用到

```JavaScript
const source = axios.CancelToken.source();

axios.get('http://jsonplaceholder.typicode.com/users/1', {
    cancelToken: source.token
}).then(res => {
    console.log(res.data);
}).catch(err => {
    console.log(err);
});

// 取消请求, cancel 中的 message 是可选的, 它会传入到请求的 catch 中
source.cancel('取消这次请求~');
```


## axios 封装

为什么要封装?

- 减少冗余代码
- 统一请求格式

```JavaScript
// contact_api.js

const CONTACT_API = {
  // 获取联系人列表
  getContactList: {
    method: 'get',
    url: '/contactList'
  },
  // 新建联系人 form-data
  newContactForm: {
    method: 'post',
    url: '/contact/new/form'
  },
  // 新建联系人 json
  newContactJson: {
    method: 'post',
    url: '/contact/new/json'
  },
  // 编辑联系人
  editContact: {
    method: 'put',
    url: '/contact/edit'
  },
  // 删除联系人
  delContact: {
    method: 'delete',
    url: '/contact'
  }
};

export default CONTACT_API;
```


```JavaScript
// Http.js

import axios from 'axios';
import service from './contact_api';
import {Toast} from 'vant';

// service 循环遍历输出不同的请求方法
const instance = axios.create({
  baseURL: 'http://localhost:9000/api',
  timeout: 1000
});

const Http = {}; // 包裹请求方法的容器

// 请求格式/参数的统一
for (const key in service) {
  const {url, method} = service[key];

  Http[key] = async function (
    params, // 请求参数 get(放到url上), post/put/patch(放到data上), delete(放到url上)
    isFormData = false, // 是否是form-data请求
    config = {} // 配置参数
  ) {
    let newParams = {};

    // Content-Type 是否为 form-data 的判断
    if (params && isFormData) {
      newParams = new FormData();

      for (const key in params) {
        newParams.append(key, params[key]);
      }
    } else {
      newParams = params;
    }

    // 不同请求的判断
    let response = {}; // 请求的返回值
    if (method === 'post' || method === 'put' || method === 'patch') {
      try {
        response = await instance[method](url, newParams, config);
      } catch (err) {
        response = err;
      }
    } else if (method === 'get' || method === 'delete') {
      config.params = newParams;
      try {
        response = await instance[method](url, config);
      } catch (err) {
        response = err;
      }
    }

    return response;
  };
}

// 拦截器的添加
instance.interceptors.request.use(
  config => {
    // 发起请求前做些什么
    Toast.loading({
      mask: false,
      duration: 0, // 0 一直存在
      forbidClick: true,
      message: '加载中...'
    });
    return config;
  }, err => {
    // 请求错误
    Toast.clear();
    Toast('请求失败, 请稍后再试!');
    return Promise.reject(err);
});

instance.interceptors.response.use(
  res => {
    // 请求成功
    Toast.clear();
    return res.data;
  }, err => {
    Toast.clear();
    Toast('请求失败, 请稍后再试!');
    return Promise.reject(err);
  });

export default Http;
```