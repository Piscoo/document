## JS工具函数
1. 为元素添加on方法
```
Element.prototype.on = Element.prototype.addEventListener;
NodeList.prototype.on = function (event, fn) {
    []['forEach'].call(this, function(el) {
        el.on(event, fn);
    });
    return this;
};
```

2. 为元素添加trigger方法
```
Element.prototype.trigger = function(type, data) {
    var event = document.createEvent("HTMLEvents");
    event.initEvent(type, true, true);
    event.data = data || {};
    event.eventName = type;
    event.target = this;
    this.dispatchEvent(event);
    return this;
};

NodeList.prototype.trigger = function(event) {
    []["forEach"].call(this, funciton(el) {
        el["trigger"](event);
    });
    return this;
};
```

3. 转义html标签
```
function HtmlEncode(text) {
    return text
        .replace(/&/g, "&")
        .replace(/\"/g, '"')
        .replace(/</g, "<")
        .replace(/>/g, ">");
}
```

4. HTML标签转义
```
function SaferHTML(templateData) {
    var s = templateData[0];
    for(var i = 1; i < arguments.length; i++) {
        var arg = String(arguments[i]);
        // Escape special characters in the substitution.
        s += arg
            .replace(/&/g, "&amp;")
            .replace(/</g, "&lt;")
            .replace(/>/g, "&gt;");
        // Don't escape special characters in the template.
        s += templateData[i];
    }
    return s;
}
// 调用
var html = SaferHTML`<p>这是一个html代码</p>`;
```

5. 跨浏览器绑定事件
```
function addEventSamp(obj, evt, fn) {
    if(!oTarget) {
        return;
    }
    if(obj.addEventListener) {
        obj.addEventListener(evt, fn, false);
    } else if(obj.attachEvent) {
        obj.attachEvent("on" + evt, fn);
    } else {
        oTarget["on" + sEvtType] = fn;
    }
}
```

7. 加入收藏夹
```
function addEavorite(sURL, sTitle) {
    try {
        window.external.addFavorite(sURL, sTitle);
    } catch(e) {
        try {
            window.sidebar.aaddPanel(sTitle, sURL, "");
        } catch(e) {
            alert("加入收藏夹失败，请使用Ctrl + D进行添加");
        }
    }
}
```

8. 提取页面代码中所有的网址
```
var aa = document.documentElement.outerHTML
    .match(
        /(url\(|src=|href=)[\"\']*([^\"\'\(\)\<\>\[\] ]+)[\"\'\)]*|(http:\/\/[\w\-\.]+[^\"\'\(\)\<\>\[\] ]+)/gi
    )
    .join("\r\n")
    .replace(/^(src=|href=|url\()[\"\']*|[\"\'\>\) ]*$/gim,"");
```

9. 动态加载脚本文件
```
function appendscript(src, text, reload, charset) {
    var id = hash(src + text);
    if(!reload && in_array(id, evalscripts)) return;
    if(reload && $(id)) {
        $(id).parentNode.removeChild($(id));
    }
    evalscripts.push(id);
    var scriptNode = document.createElement("script");
    scriptNode.type = "text/javascript";
    scriptNode.id = id;
    scriptNode.charset = charset
        ? charset
        : BROWSER.firefox
        ? document.characterSet
        : document.charset;
    try {
        if(src) {
            scriptNode.src = src;
            scriptNode.onloadDone = false;
            scriptNode.onload = function() {
                scriptNode.onloadDone = true;
                JSLOADED[src] = 1;
            };
            scriptNode.onreadystatechange = function() {
                if((scriptNode.readyState == "loaded" || scriptNode.readyState == "complete") && !scriptNode.onloadDone) {
                    scriptNode.onloadDone = true;
                    JSLOADED[src] = 1;
                }
            };
        } else if(text) {
            scriptNode.text = text;
        }
        document.getElementsByTagName("head")[0].appendChild(scriptNode);
    } catch(e) {}
}
```

10. 返回顶部的通用方法
```
function backTop(btnId) {
    var btn = document.getElementById(btnId);
    var d = document.documentElement;
    var b = document.body;
    window.onscroll = set;
    btn.style.display = "none";
    btn.onclick = function() {
        btn.style.display = "none";
        window.onscroll = null;
        this.timer = setInterval(function() {
            d.scrollTop -= Math.ceil((d.scrollTop + b.scrollTop) * 0.1);
            b.scrollTop -= Math.ceil((d.scrollTop + b.scrollTop) * 0.1);
            if(d.scrollTop + b.scrollTop == 0) {
                clearInterval(btn.timer, (window.onscroll = set));
            }
        }, 10);
    };
    function set() {
        btn.style.display = d.scrollTop + b.scrollTop > 100 ? "block" : "none";
    }
}
backTop("goTop");
```

11. 实现base64解码
```
function base64_decode(data) {
    var b64 =  "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";
    var o1,
    o2,
    o3,
    h1,
    h2,
    h3,
    h4,
    bits,
    i = 0,
    ac = 0,
    dec = "",
    tmp_arr = [];
    if(!data) {
        return data;
    }
    data += "";
    do {
        h1 = b64.indexOf(data.charAt(i++));
        h2 = b64.indexOf(data.charAt(i++));
        h3 = b64.indexOf(data.charAt(i++));
        h4 = b64.indexOf(data.charAt(i++));
        bits = (h1 << 18) | (h2 << 12) | (h3 << 6) | h4;
        o1 = (bits >> 16) & 0xff;
        o2 = (bits >> 8) & 0xff;
        o3 = bits & 0xff;
        if(h3 == 64) {
            tmp_arr[ac++] = String.fromCharCode(o1);
        } else if(h4 == 64) {
            tmp_arr[ac++] = String.fromCharCode(o1, o2);
        } else {
            tmp_arr[ac++] = String.fromCharCode(o1, o2, o3);
        }
    } while(i < data.length);
    dec = tmp_arr.join("");
    dec = utf8_decode(dec);
    return dec;
}
```

12. 确认是否是键盘有效输入值
```
function checkKey(iKey) {
    // 空格和异常
    if(iKey == 32 || iKey == 229) {
        return true;
    }
    // 数字
    if(iKey > 47 && iKey < 58) {
        return true;
    }
    // 字母
    if(iKey > 64 && iKey < 91) {
        return true
    }
    // 数字键盘1
    if(iKey > 95 && iKey < 108) {
        return true;
    }
    // 数字键盘2
    if(iKey > 108 && iKey < 112) {
        return true;
    }
    // 符号1
    if(iKey > 185 && iKey < 193) {
        return true;
    }
    // 符号2
    if(iKey > 218 && iKey < 223) {
        return true;
    }
    return false;
}
```

13. 压缩CSS样式代码
```
function compressCss(s) {
    // 压缩代码
    s = s.replace(/\/\*(.|\n)*?\*\//g, ""); // 删除注释
    s = s.replace(/\s*([\{\}\:\;\,])\s*/g, "$1");
    s = s.replace(/\,[\s\.\#\\d]*\{/g, "{"); // 容错处理
    s = s.replace(/;\s*;/g, ";"); // 去除连续分号
    s = s.match(/^\s*(\S+(\s+\S+)*)\s*$/); // 去除首尾空白
    return s == null ? "" : s[1];
}
```

14. 跨浏览器删除事件
```
function delEvent(obj, evt, fn) {
    if(!obj) {
        return;
    }
    if(obj.addEventListener) {
        obj.addEventListener(evt, fn, false);
    } else if(oTarget.attachEvent) {
        obj.attachEvent("on" + evt, fn);
    } else {
        obj["on" + evt] = fn;
    }
}
```

15. 判断是否以某个字符串结束
```
String.prototype.endWith = function(s) {
    var d = this.length - s.length;
    return d >= 0 && this.lastIndexOf(s) == d;
};
```

16. 返回脚本内容
```
function evalscript(s) {
    if(s.indexOf("<script") == -1) return s;
    var p = /<script[^\>]*?>([^\x00]*?)<\/script>/gi;
    var arr = [];
    while((arr = p.exec(s))) {
        var p1 = /<script[^\>]*?src=\"([^\>]*?)\"[^\>]*?(reload=\"1\")?(?:charset=\"([\w\-]+?)\")?><\/script>/i;
        var arr1 = [];
        arr1 = p1.exec(arr[0]);
        if(arr1) {
            appendscript(arr1[1], "", arr1[2], arr1[3]);
        } else {
            p1 = /<script(.*?)>([^\x00]+?)<\/script>/i;
            arr1 = p1.exec(arr[0]);
            appendscript("", arr1[2], arr1[1].indexOf("reload") != -1);
        }
    }
    return s;
}
```

17. 获取cookie值
```
function getCookie(name) {
    var arr = document.cookie.match(new RegExp("(^| )" + name + "=([^;]*)(;|$)"));
    if(arr != null) return unescape(arr[2]);
    return null;
}
```

18. 获取URL中GET参数值
```
function getGet(name) {
    querystr = window.location.href.split("?");
    if(querystr[1]) {
        GETs = querystr[1].split("&");
        GET = [];
        for(i = 0; i < GETs.length; i++) {
            tmp_arr = GETs[i].split("=");
            key = tmp_arr[0];
            GET[key] = tmp_arr[1];
        }
    }
    return GET[name];
}
```

19. 获取页面高度
```
function getPageHeight() {
    var g = document, a = g.body, f = g.documentElement, d = g.compatMode == "BackCompat" ? a : g.documentElement;
    return Math.max(f.scrollHeight, a.scrollHeight, d.clientHeight);
}
```

20. 获取页面scrollLeft
```
function getPageScrollLeft() {
    return document.documentElement.scrollLeft || document.body.scrollLeft;
}
```

21. 获取页面scrollTop
```
function getPageScrollTop() {
    return document.documentElement.scrollTop || document.body.scrollTop;
}
```

22. 获取页面可视宽高
```
function getPageViewSize() {
    var d = document;
    a = d.compatMode == "BackCompat" ? d.body : d.documentElement;
    return `宽：${a.clientWidth};高：${a.clientHeight}`
}
```

23. 获取页面被卷去的位置
```
function getScrollXY() {
    return document.body.scrollTop
        ? {
            x: document.body.scrollLeft,
            y: document.body.scrollTop
          }
        : {
            x: document.documentElement.scrollLeft,
            y: document.documentElement.scrollTop
        };
}
```

24. 判断是否为移动设备访问
```
function isMobileUserAgent() {
  return /iphone|ipod|android.*mobile|windows.*phone|blackberry.*mobile/i.test(
    window.navigator.userAgent.toLowerCase()
  );
}
```

25. 判断访问的移动设备类型
```
function mobileDeviceType() {
    return `Android: ${/android/i.test(navigator.userAgent.toLowerCase())};iOS: ${/iphone|ipad|ipod|Macintosh/i.test(navigator.userAgent.toLowerCase())}`;
}
```

26. 判断鼠标是否移出事件
```
function isMouseOut(e, handler) {
    if(e.type !== "mouseout") {
        return false;
    }
    var reltg = e.relatedTarget
        ? e.relatedTarget
        : e.type === "mouseout"
        ? e.toElement
        : e.fromElement;
    while(reltg && reltg !== handler) {
        reltg = reltg.parentNode;
    }
    return reltg !== handler;
}
```

27. 加载样式文件
```
function loadStyle(url) {
    try {
        document.createStyleSheet(url);
    } catch(e) {
        var cssLink = document.createElement("link");
        cssLink.rel = "stylesheet";
        cssLink.type = "text/css";
        cssLink.href = url;
        var head = document.getElementsByTagName("head")[0];
        head.appendChild(cssLink);
    }
}
```

28. 替换地址栏
```
function locationReplace(url) {
    if(history.replaceState) {
        history.replaceState(null, document.title, url);
        history.go(0);
    } else {
        location.replace(url);
    }
}
```

29. 延时执行
```
// 比如 sleep(1000) 意味着等待1000毫秒执行，还可以从promise，Generator，Async/Await等角度实现
// promise
const sleep = time => {
    return new Promise(resolve => setTimeout(resolve, time));
};
sleep(1000).then(() => {
    console.log(1);
});

// Generator
function* sleepGenerator(time) {
    yield new Promise(function(resolve, reject) {
        setTimeout(resolve, time);
    });
}
sleepGenerator(1000)
    .next()
    .value.then(() => {
        console.log(2);
    });

// async
function sleep(time) {
    return new Promise(resolve => setTimeout(resolve, time));
}
async function output() {
    let out = await sleep(1000);
    console.log(3);
    return out;
}
output();

function sleep(callback, time) {
    if(typeof callback === 'function') {
        setTimeout(callback, time);
    }
}
function output() {
    console.log(4);
}
sleep(output, 1000);
```

30. 清除空格
```
String.prototype.trim = function() {
    var reExtraSpace = /^\s*(.*?)\s+$/;
    return this.replace(reExtraSpace, "$1");
};

// 清除左空格
function leftTrim(s) {
    return s.replace(/^(\s*| *)/, "");
}
// 清除右空格
function rightTrim(s) {
    return s.replace(/(\s*| *)$/, "");
}

// 清除所有空格
function clearSpaces(srt) {
    return str.replace(/[ ]/g, '');
}
```