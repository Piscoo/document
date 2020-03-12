## 记录一下平时开发途中遇到的一些问题
1. vue -V 不是内部或外部命令，也不是可运行的程序或批处理文件
- 确认是否设置过，设置过则跳过
```
npm config set prefix "node目录/node_global"
npm config set prefix "node目录/node_cache"
```
- 找到 vue.cmd 所在的位置，将路径添加到系统环境变量的path中，重启命令窗口， vue -v检测是否成功

2. `store.registerModule is not a function`

#### vuex 的 index.js 中 export Vuex
```
export default new Vuex.Store({
    modules: {
        app,
        user,
    }
})
```

#### json对象和 formData对象转换

1. JSON对象转成formData对象（支持一层对象）
```
const formData = new FormData();
Object.keys(params).forEach((key) => {
    formData.append(key, params[key]);
});
```
2. formData对象转成JSON对象（支持一层对象）
```
var jsonData = {};
formData.forEach((value, key) => jsonData[key] = value);
```

#### vuex 页面刷新数据丢失
```
// 在 APP.vue 的created中
//在页面加载时读取sessionStorage里的状态信息
if (sessionStorage.getItem("store") ) {
    this.$store.replaceState(Object.assign({}, this.$store.state,JSON.parse(sessionStorage.getItem("store"))))
} 

//在页面刷新时将vuex里的信息保存到sessionStorage里
window.addEventListener("beforeunload",()=>{
    sessionStorage.setItem("store",JSON.stringify(this.$store.state))
})
```

#### vue 项目打包上线后所有资源404
```
// vue.config.js
module.exports = {
    publicPath: './',
}
```

#### 设置和删除cookie
```
//设置
const date = new Date();
date.setTime(date.getTime() + 24 * 60 * 60 * 1000);
const lastTime = date.toGMTString();
document.cookie = `api_token=${res.data.api_token};expires=${lastTime};`;
document.cookie = `identity_token=${res.data.identity_token};expires=${lastTime};`;
// 删除 => 只需设置失效时间为过去的时间，就会自动被删除
const iden=document.cookie.indexOf("identity_token=");
if(iden == -1){
    return;
} else{
    const date = new Date();
    date.setTime(date.getTime() - 24 * 60 * 60 * 1000);
    const lastTime = date.toGMTString();
    document.cookie = `identity_token='';expires=${lastTime};`;
}
```

#### 修改host内容
```
host 位于C:\Windows\System32\Drivers\etc  使用编辑工具进行修改并保存即可。
也可以：
管理员身份运行cmd
进入到 C:\Windows\System32\Drivers\etc
notepad hosts
修改并保存
```

#### hover 时按钮抖动
```
&:hover {
    animation: shake 0.82s cubic-bezier(.36, .07, .19, .97) both;
}
@keyframes shake {
    10%, 90% {
        transform: translate3d(-1px, 0, 0);
    }
    20%, 80% {
        transform: translate3d(2px, 0, 0);
    }
    30%, 50%, 70% {
        transform: translate3d(-4px, 0, 0);
    }
    40%, 60% {
        transform: translate3d(4px, 0, 0);
    }
}
```

#### 页面内锚点跳转不被header遮挡内容
```
#seeVideo:target {
    padding-top: 50px;
}
```

#### git add 报错
```
// 问题XXX
 LF will be replaced by CRLF in XXX
The file will have its original line endings in your working directory
// 办法
git config --global core.autocrlf false
```