# GIT仓库以及部署代码

## 安装GIT
`sudo apt-get install git`

## 创建git 用户
`sudo adduser git`

## 设置git密码
`passwd git`

## git 用户添加到 www 组
`usermode -a -G www git `

## 下创建仓库 
`git init --bare --shared=group test.git`

## 设置仓库目录权限
`chown -R git:www test.git`

## 设置钩子
`vim hooks/post-receive`

写入以下内容
```   
    #!/bin/bash
    # /www/wwwroot/yijiuyun 为 web 根目录
   
    git --work-tree=/www/wwwroot/yijiuyun-test checkout -f
``` 
##钩子文件添加执行权限

`chmod +x hooks/post-receive`

## 网站根目录设置组用户写权限
` chmod g+w web`
