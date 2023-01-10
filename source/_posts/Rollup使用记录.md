---
title: Rollup使用记录
date: 2023-01-10 10:58:20
tags: 前端工程化
excerpt: 记录使用Rollup的常用方法，相关使用规则，以及为什么选择Rollup……
---


# 概述
rollup是esm打包器，rollup和webpack类似，rollup更加的小巧，仅仅是一款esm的打包器。Rollup并不支持HMR这种高级特性
简介 | rollup.js 中文文档 | rollup.js中文网 (rollupjs.com)

# 上手
```js
// 安装
yarn add rollup --dev

// 使用
yarn rollup ./src/index.js --format iife --file dist/bundle.js
配置
// rollup.config.js
export default {
    input: 'src/index.js',
    output: {
        file: 'dist/bundle.js',
        format: 'iife'    
    }
}
```
# 使用配置中的参数
yarn rollup --config rollup.config.js // 可以指定不同的配置文件
配置项完整列表

```js
// rollup.config.js
export default { // 可以是一个数组（用于多个输入的情况）
  // 核心的输入选项
  external,
  input, // 必要项
  plugins,

  // 高级输入选项
  cache,
  onwarn,
  preserveEntrySignatures,
  strictDeprecations,

  // 危险区
  acorn,
  acornInjectPlugins,
  context,
  moduleContext,
  preserveSymlinks,
  shimMissingExports,
  treeshake,

  // 实验性
  experimentalCacheExpiry,
  perf,

  output: { // 必要项 (可以是一个数组，用于多输出的情况)
    // 核心的输出选项
    dir,
    file,
    format, // 必要项
    globals,
    name,
    plugins,

    // 高级输出选项
    assetFileNames,
    banner,
    chunkFileNames,
    compact,
    entryFileNames,
    extend,
    footer,
    hoistTransitiveImports,
    inlineDynamicImports,
    interop,
    intro,
    manualChunks,
    minifyInternalExports,
    outro,
    paths,
    preserveModules,
    sourcemap,
    sourcemapExcludeSources,
    sourcemapFile,
    sourcemapPathTransform,

    // 危险区
    amd,
    esModule,
    exports,
    externalLiveBindings,
    freeze,
    indent,
    namespaceToStringTag,
    noConflict,
    preferConst,
    strict,
    systemNullSetters
  },

  watch: {
    buildDelay,
    chokidar,
    clearScreen,
    skipWrite,
    exclude,
    include
  } | false
};
```
# 使用插件
- 加载其他类型的资源模块
- 导入commonjs模块
- 编译ecmascript新特性
- rollup支持使用插件的方式扩展，插件是rollup唯一的扩展方式

## 使用rollup-plugin-json
```js
// 安装插件
yarn add rollup-plugin-json --dev

// rollup.config.js
import json from 'rollup-plugin-json'
...plugins: [
    json()
]
```
# Rollup加载npm模块
## 使用rollup-plugin-node-resolve
```js
// 安装
yarn add rollup-plugin-node-resolve
// rollup.config.js
import resolve from 'rollup-plugin-node-resolve'
...plugins: [
    ...,
    resolve()
]
// src/index.js
import _ from 'lodash-es'
```
## Rollup加载Commonjs模块
```js
// 安装
yarn add rollup-plugin-commonjs --dev
// rollup.config.js
import commonjs from 'rollup-plugin-commonjs'
...plugins: [
    ...,
    commonjs()
]
// src/cjs-module.js
module.exports={
    foo:'bar'
}
// src/index.js
import cjs from './cjs-module'
console.log(cjs)
```
# Rolup代码拆分
```js
// src/index.js
import('./logger').then(({log})=> {
    log('代码拆分')
})
// rollup.config.js
export default {
    input:'src/index.js',
    output: {
        dir:'dist',
        format: 'amd'    
    }
}
```
# Rollup多入口打包
```js
// rollup.config.js
export default {
    // input: ['src/index.js', 'src/album.js']
    input: {
        foo:'src/index.js',
        bar:'src/album.js'    
    },
    output: {
        dir: 'dist',
        format: 'amd'    
    }
}
```
# Rollup选用原则

优点：
- 输出结果更加扁平
- 自动移出未引用代码
- 打包结果依旧完全可读

缺点
- 加载非ESM的第三方模块比较复杂
- 模块最终都被打包到一个函数中，无法实现HMR
- 浏览器环境中，代码拆分功能依赖AMD库
- 如果是开发应用程序的话，最好用webpack；如果是开发类库、框架，可以使用Rollup