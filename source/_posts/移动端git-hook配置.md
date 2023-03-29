---
title: 移动端git hook配置
date: 2022-03-10 09:41:51
tags: 前端工程化
excerpt: husky格式化配置……
---

## 安装依赖

当前接手的项目是用vue2脚手架配置的项目，于是安装了如下的版本的husky和lint-staged

```sh
cnpm i -D husky@1	
cnpm i -D lint-staged@8	 
cnpm i -D @commitlint/config-conventional @commitlint/cli
```

## package.json配置

```json
{
    "lint-staged": {
    "src/**/*.{js,vue}": [
      "eslint --fix",
      "git add"
    ]
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  }
}
```