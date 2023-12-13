+++
title = 'Hugo + GitHub 搭建'
date = 2023-12-13T15:14:14+08:00
+++

## Hugo 安装

下载地址 [Hugo](https://objects.githubusercontent.com/github-production-release-asset-2e65be/11180687/66df1346-b293-4ac5-bdc4-7088515c5569?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231213%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231213T053949Z&X-Amz-Expires=300&X-Amz-Signature=2deb087dbe334e82cff105459bec921a53825a54a2bc3e48eca48d91ac9f2e58&X-Amz-SignedHeaders=host&actor_id=63246934&key_id=0&repo_id=11180687&response-content-disposition=attachment%3B%20filename%3Dhugo_extended_0.121.1_windows-amd64.zip&response-content-type=application%2Foctet-stream)

> 由于后面要用到 `LoveIt` 主题，需要下载extended版本

> 添加环境变量不再赘述

## GitHub 新建仓库

为了域名简洁，新建一个仓库名为 `$(USRID).github.io` 的仓库

## 本地初始化文件夹

    hugo new site makrozhang.github.io

将会新建一个名为 `makrozhang.github.io` 的文件夹
> 只是为了和仓库名一致，起始可以任意取名

## 新建post

相当于新建一篇文章

    hugo new posts/first-post.md

会新建 `content/posts/first-post.md`

1. 删掉开头的 `draft: true` 或直接删除该行
2. 编辑内容...

## 初始化仓库

回到项目的根目录 执行:

    git init

## 下载主题

    git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt

修改主题配置
在 `hugo.toml` 文件中添加一行

    theme = "LoveIt"

## 本地预览

    hugo server

浏览器打开 [本地端口1313](http://localhost:1313/) 预览

## 生成网页文件

文章编辑结束，预览觉得没问题，就可以生成网页了
在 `hugo.toml` 文件中添加一行

    publishDir = "docs"

直接

    hugo

## 管理GitHub仓库

    git remote add origin https://github.com/XXX/REPO-NAME.git

然后把你的修改push上去

## 修改远程仓库的属性

仓库-Settings-Pages-Build and deployment-Branch

选择对应分支的`/docs`

将会出发一个Action 生成网页

## 最终效果

访问 `$(USRID).github.io/`
