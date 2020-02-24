## Process of Create a New Vue Object
1. create a new object
 ```
 vue create my-object
 ```
 2. 关联远程git目录
 ```
 cd project
 git init
 git remote add origin "git-path"
 ```
 3. 添加dev 分支，然后切换到分支
 ```
 git branch [branch-name]
 git checkout -b [branch-name]
 // 创建并切换的命令
 git checkout -b [branch-name]
 ```
 4. 将新分支推送到github
 ```
 git push origin [brach-name]
 ```
 5. 项目内配置和安装相关依赖
    1. 添加 vue.config.js, QA.md
    2. 添加 api, utils, pages, store等文件夹
    3. 安装所需依赖
        - yarn `yarn`
        + sass `npm install sass`
        * vue-router `npm install vue-router`
        - vuex `npm install vuex --save`
        + vux `npm install vux -s`
        * axios `npm install axios`