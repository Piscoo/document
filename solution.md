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