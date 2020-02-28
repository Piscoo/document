#vue-cli 3 的 vue.config.js 多域名跨域本地代理配置

#### 背景
在做项目时碰到了需要请求好几个域名下的接口的情况，而接口是确定没问题的，所以没有后台的同事介入，只能自己本地代理解决跨域的问题。

#### 思路
在 `devServer.proxy`中配置多个域名的代理，然后在 `pathRewrite`中replace成''，这样在请求后台接口时如果匹配到相应的代理名，如：`http://127.0.0.1:8088/passportapi/XXX/XXX`， 实际上是`http://127.0.0.1:8088/XXX/XXX`，而反向代理又能够实现本地代理而不出现跨域的情况，目的就达到了。

#### 代码

1. vue.config.js
```
module.exports = {
    runtimeCompiler: false,
    productionSourceMap: true,
    lintOnSave: true,
    filenameHashing: true,
    outputDir: 'dist',  // build打包后文件输出地址
    assetsDir: 'static',
    configureWebpack: {
        resolve: {
            extensions: ['.js', '.vue', '.json'],
            alias: {
                vue$: 'vue/dist/vue.esm.js',
                '@': resolve('src'),
                '@utils': resolve('src/utils'),
            },
        },
    },
    devServer: {
        port: 8088,
        host: '127.0.0.1',
        disableHostCheck: true,
        open: true,
        proxy: {
            '/passportapi': {
                target: 'https://aaaa.com',
                ws: true,
                changeOrigin: true,
                sesure: false,
                pathRewrite: {
                    '^/passportapi': ''
                }
            },
            'supportapi' : {
                target: 'https://bbbb.com',
                ws: true,
                changeOrigin: true,
                sesure: false,
                pathRewrite: {
                    '^/supportapi': ''
                }
            },
            '/lightapi': {
                target: 'https://cccc.com',
                ws: true,
                changeOrigin: true,
                sesure: false,
                pathRewrite: {
                    '^/lightapi': ''
                }
            }
        },
    },
};

```
2. request.js
```
import axios from 'axios';
axios.defaults.withCredentials = true;
const service = axios.create({
    baseURL: '/',
    timeout: 2000,
});
service.interceptors.request.use(
    (config) => {
        return config;
    },
    (error) => {
        console.log(error);
        return Promise.reject(error);
    },
);

service.interceptors.response.use(
    (response) => {
        const res = response.data;
        return res;
    },
    (error) => {
        console.log(`err${error}`);
        return Promise.reject(error);
    },
);

export default service;
```
3. api.js
```
import request from '@/utils/request';

export function testpass(data) {
    return request({
        url: '/passportapi/XXX/XXX',
        method: 'post',
        data,
    })
}

export function testsupport(data) {
    return request({
        url: '/supportapi/XXX/XXX',
        method: 'post',
        data,
    });
}
export function testlight(api) {
    return request({
        url: `/lightapi/XXX/XXX`,
        method: 'get',
    });
}

```

#### 总结
上面的情况比较复杂，由于接口的风格统一，如：`https://aaaa.com/api/XXX`，`https://bbbb.com/api/XXX`，`https://cccc.com/api/XXX`，也就是说域名后面的名字是一样的，所以需要多代理一层，也就导致`axios`请求的时候要`/passportapi/api/XXX`这么复杂，如果都不一样，直接使用域名后面第一个单词来做代理名区分，这样`axios`也就只要`/XXX`就行，`pathRewrite`也不用了。