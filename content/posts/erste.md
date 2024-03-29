+++
title = 'Erste'
date = 2023-12-13T13:55:43+08:00
draft = false
+++

## 搭建Github

### 申请Github账号

### 在自己的电脑上生成SSH密钥

> [参考](https://blog.csdn.net/u013778905/article/details/83501204)

```bash
git config --global user.name "Makro Zhang"
git config --global user.email "makrozhang@outlook.com"

ssh-keygen -t rsa -C "makrozhang@outlook.com"
```

会在电脑用户目录下生成 `.ssh` 文件夹(注意是隐藏文件夹，须打开显示隐藏文件夹)

该文件夹下有两个文件，带后缀 `.pub` 的是公钥，复制内容放到对应仓库的Github账户里，顺带取个名字（指明是哪台电脑）

### clone到本地

```bash
git clone git@github.com:MakroZhang/makrozhang.github.io.git
```

注意使用SSH链接，若使用HTTPS链接后续还要账号密码

### 本地修改内容

### 本地提交

```bash
git commit -a -m '此处为任意备注'
```

提交到Github

```bash
git push
```
