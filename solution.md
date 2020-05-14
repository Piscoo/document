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

#### 解决iframe隐藏后iframe中的视频仍在播放
```
var ifr = document.getElementsByTagName("iframe")[0],
    ifrLink = ifr.getAttribute("src");
    ifr.setAttribute("src", ifrLink);
```

#### 动态的向下的箭头
```
main .heart{
animation: heart 1.3s ease-in-out 2.7s infinite alternate;
}

//margin-top会发生卡顿
@keyframes heart{
from{margin-top:0px;}
to{margin-top:-6px;}
}

//用transform就流畅了
@keyframes heart{
from{transform:translate(0,0)}
to{transform:translate(0,6px)}
}
```

#### 动态的向下的箭头
```
// html 
<div class="down">
    <div class="arrow arrow-1"></div>
        <div class="arrow arrow-2"></div>
</div>
// css
.down {
    position: absolute;
    bottom: 20px;
    left: 50%;
    transform: translateX(-50%);
    height: 40px;
    width: 40px;
    cursor: pointer;
    .arrow {
        position: absolute;
        -webkit-transform-origin: 50% 50%;
        transform-origin: 50% 50%;
        cursor: pointer;
        margin-left: -10px;
        top: 30px;
        left: 50%;
        &::before, &::after {
            background: #fff;
            content: '';
            display: block;
            height: 3px;
            position: absolute;
            top: 0;
            left: 0;
            width: 18px;
            border-radius: 30px;
        }
        &::before {
            -webkit-transform: rotate(45deg) translateX(-3px);
            transform: rotate(45deg) translateX(-3px);
            -webkit-transform-origin: top left;
            transform-origin: top left;
        }
        &::after {
            -webkit-transform: rotate(-45deg) translateX(3px);
            transform: rotate(-45deg) translateX(3px);
            -webkit-transform-origin: top right;
            transform-origin: top right;
        }
    }
    .arrow-1 {
        -webkit-animation: arrow-movement 2s ease-in-out infinite;
        animation: arrow-movement 2s ease-in-out infinite; 
    }
    .arrow-2 {
        -webkit-animation: arrow-movement 2s 1s ease-in-out infinite;
        animation: arrow-movement 2s 1s ease-in-out infinite;
    }
    @-webkit-keyframes arrow-movement {
        0% {
            opacity: 0;
            top: 30px;
        }
        30% {
            top: 10px;
        }
        70% {
            opacity: 1;
        }
        100% {
            opacity: 0;
        }
    }
    
    @keyframes arrow-movement {
        0% {
            opacity: 0;
            top: 30px;
        }
        30% {
            top: 10px;
        }
        70% {
            opacity: 1;
        }
        100% {
            opacity: 0;
        }
    }
}
```

##### 逐渐上移
```
.coverbox {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    position: absolute;
    width: 100%;
    height: 0;
    bottom: 0;
    left: 0;
    background: rgba(0, 156, 246, 0.5);
    transition: height 0.5s ease-out, opacity 0.2s ease-in, visibility 0.2s ease-in;
    visibility: 0;
    opacity: 0;
    &.active {
        opacity: 1;
        visibility: 1;
        height: 100%;
    }
    .img {
        width: 40px;
        height: 40px;
        border: 1px solid #fff;
    }
    .word {
        color: #fff;
    }
}
```

#### 从左滑入
```
// css
.box {
    opacity: 0;
    &.active {
        opacity: 1;
        animation: bounceInLeft 1s cubic-bezier(0.215, 0.610, 0.355, 1.000);
    }
    @keyframes bounceInLeft {
        0% {
            opacity: 0;
            transform: translate3d(-3000px, 0, 0);
        }
        60% {
            opacity: 1;
            transform: translate3d(25px, 0, 0);
        }
        75% {
            transform: translate3d(-10px, 0, 0);
        }
        90% {
            transform: translate3d(5px, 0, 0);
        }
        100% {
            transform: none;
        }
    }
}
// js
$(window).scroll(function(){
    let bHeight = document.body.clientHeight;
    if($(window).scrollTop() > ($(".box").offset().top - bHeight) && $(window).scrollTop() < ($(".box").offset().top + 150)){
        $('.box').addClass('active')
    }else{
        $('.box').removeClass('active')
    }
})
```

### 设备类型判断
```
if(/Android|webOS|iPhone|iPod|BlackBerry/i.test(navigator.userAgent)) {

    $('#bannervideo').removeAttr('autoplay');
    $('#editvideo').removeAttr('autoplay');
}
```
### 生成随机的评论总数
`Math.floor((Math.random()+1)*Math.pow(5,4));`

### 移动端的视频兼容处理
```
  //其他移动端浏览器只能让用户自己触发
  $(window).on('touchstart', function(){
    $(".second .coverimg").fadeOut(500);
    $("#editvideo").trigger('play');
  });
  // 兼容微信(微信可以自动播放)
  document.addEventListener("WeixinJSBridgeReady", function () {
    $(".second .coverimg").fadeOut(500);
    $("#editvideo").trigger('play');
  }, false);
```

### 实现hover时改变椭圆按钮的渐变背景
```
// html

<div class="download-btns">
    <a href="" class="download">
        <div class="move-bg">
            <div class="bg-color-left"></div>
            <div class="bg-color-right"></div>
        </div>
        <p>免费下载</p>
    </a>
    <a href="" class="buy">立即购买</a>
</div>

// css

.download-btns {
    a {
        display: inline-block;
        width: 240px;
        height: 50px;
        line-height: 50px;
        border-radius: 25px;
        overflow: hidden;
        font-size: 20px;
        font-weight: 500;
        text-align: center;
        text-decoration: none;
    }
    .download {
        margin-right: 50px;
        position: relative;
        transition: transform .3s;
        p{
            display: block;
            position: relative;
            height: 100%;
            line-height: 50px;
        }
        .move-bg{
            position: absolute;
            height: 50px;
            width: 480px;
            left: -240px;
            transition: all 0.3s cubic-bezier(0.47, 0, 0.745, 0.715);

            .bg-color-left{
                position: relative;
                float: left;
                width: 240px;
                height: 50px;
                background: linear-gradient(to left, rgba(30,231,255,1), rgba(105,89,255,1));
            }

            .bg-color-right{
                position: relative;
                float: left;
                width: 240px;
                height: 50px;
                background: linear-gradient(to right, rgba(30,231,255,1),  rgba(105,89,255,1));
            }
        }
        &:hover {
            .move-bg{
                transition: all 0.3s cubic-bezier(0.47, 0, 0.745, 0.715);
                transform: translateX(240px);

            }
        }
    }
    .buy {
        background: url("../img/new-mirror/borderbg.png") center no-repeat;
        background-size: 100% 100%;
        position: relative;
        &::before {
            content: '';
            position: absolute;
            top: 0;
            right: 100%;
            bottom: 0;
            left: 0;
            opacity: 0;
            height: 100%;
            z-index: -1;
            background-image: linear-gradient(to right, rgba(105,89,255,1), rgba(30,231,255,1));
            transition: right 0.4s cubic-bezier(0.47, 0, 0.745, 0.715), opacity 0.5s cubic-bezier(0.47, 0, 0.745, 0.715);
        }
        &:hover {
            &::before {
                right: 0;
                opacity: 1;
            }
        }
    }
}
```