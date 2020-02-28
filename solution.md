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