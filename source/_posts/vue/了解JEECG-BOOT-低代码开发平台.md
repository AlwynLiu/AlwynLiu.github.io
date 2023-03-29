---
title: 了解JEECG BOOT 低代码开发平台
date: 2023-01-14 11:46:55
categories: 前端
tags: vue
excerpt: 记录自己了解开源项目jeecg前端代码的过程……
---

## 起因

后续的开发中可能会使用到jeecg进行开发，所以准备提前了解一下jeecg的前端代码库是怎么样的，[代码资源](https://github.com/jeecgboot/jeecg-boot)，考虑现在的情况，直接看了vue3版本的源码。

## 项目依赖

### ant-design-vue

在项目中看到了ant-design-vue的依赖，自己没有使用过ant-design的ui库，都是使用的element的样式库。

### vxe-table xe-utils

这两个库也看到同事在使用，不过自己没有改相关的代码，就没有关心这两个库的使用。仔细看了vxe-table的使用文档，所支持的table的组件特别多，对table的封装也和项目中使用到的内部库ds-ui相似。

### ts

发现项目是ts写的，这个还是要多看多写，学了没实际项目开发就有点心虚。

## 本地运行代码

在github上下载下来代码，首先要安装依赖。采用yarn进行安装依赖，虽然看到项目是使用的pnpm安装的依赖，自己还是使用了yarn进行安装。

这个时候发现被提醒自己的node版本是14的版本，项目要求node版本要16以上,查看node官网的16版本的最新稳定版是16.19.0，于是得把node版本切换到16.19.0。

### 使用nvm修改node版本

```js
nvm install 16.19.0 // 安装16.19.0的node版本
nvm list // 查看nvm安装好的node版本
nvm use 16.19.0 // 使用node16.19.0版本，需要cmd有管理员权限，不然会失败
```

## 了解代码逻辑

### 单点登录

