# Packages 介绍

webappkit 由 `@react2rx`，`@rontrol`和`@rontrol-dev`三大部分组成。
未来或许会有 `@rontrol-ui` 组件库。

## @react2rx

>`@react2rx`完全脱离业务，是整个脚手架的核心实现。

### @react2rx/core
`@react2rx/core`是整个脚手架的核心。提供了`actor`对象，基于`rxjs`的状态流`store`，将状态流与`react`打通的一些列hook函数及组件，以及一些工具函数。

### @react2rx/router
`@react2rx/router`是基于[`react-router`](https://github.com/remix-run/react-router)和`@react2rx/core`的`actor`，为接入状态流所做的封装。提供了便捷操作`history`的hooks，`react-router`相关api，`Prompt`,`Link`，以及一些工具函数。由于新版`react-router`取消了对[`remix-run/history`](https://github.com/remix-run/history)的依赖，而是自己内部实现了一个轻量的`history`，不再提供`useHistory`hook，导致`history.listen`无法使用。所以没有直接使用`BrowserRouter`作为`router`。

### @react2rx/request
`@react2rx/request`是基于`axios`和`actor`所做的封装，将request整个生命周期接入了状态流，实现了`swr`(stale-while-revalidate)，缓存，缓存策略，轮询，重试，loadingDelay等等，提供了完善的request功能和hook函数，参数与响应的interface定义，以及一些工具函数。

### @react2rx/persistence
`@react2rx/persistence`是脚手架的缓存控制模块。控制将`Store`状态流中状态读写进缓存`LocalCache`的模块。

* `@react2rx/persistence/local-cache`子模块 用于创建缓存器。如果支持localStorage则基于localStorage创建，否则基于Map变量创建。

### @react2rx/bootstrap
`@react2rx/bootstrap`是脚手架的启动入口封装。包含了启动程序的必要组件和函数，暴露出可选选项以及状态流中间件参数，并在开发环境开启日志中间件，记录状态流、request、router等事件。

* `@react2rx/bootstrap/config`子模块 实现了加载项目配置并提供读取配置的函数
* `@react2rx/bootstrap/logger`子模块 实现了日志中间件

## @rontrol

>`@rontrol`围绕业务。是基于@react2rx开发的功能性组件或函数

### @rontrol/access
`@rontrol/access`提供登录加密；用户信息获取，用户权限控制；请求头authorization控制等关于用户权限控制的组件或函数。

### @rontrol/babel-plugin-access-control-autocomplete
`@rontrol/babel-plugin-access-control-autocomplete`是一个babel插件。给组件在编译阶段根据命名提供权限控制。

```typescript
export const AcComponent = hoc()(() => null);
export const AcSomeComponent = create(() => null);
    
↓ ↓ ↓ ↓ ↓ ↓

import { mustOneOfPermissions as _mustOneOfPermissions } from \\"@rontrol/access\\";
import { mustAllOfPermissions as _mustAllOfPermissions } from \\"@rontrol/access\\";
export const AcComponent = _mustAllOfPermissions()(hoc()(() => null), false, \\"AcComponent\\");
export const AcSomeComponent = _mustOneOfPermissions()(create(() => null), false, \\"AcSomeComponent\\");
```

### @rontrol/contexts
`@rontrol/contexts`提供带缓存的全局状态写入和读取。

### @rontrol/form
`@rontrol/form`表单功能加强。表单验证，并通过状态流实现缓存。

### @rontrol/lodash
`@rontrol/lodash`默认导出`lodash-es`

### @rontrol/notify
`@rontrol/notify`全局弹窗提示/确定功能。

### @rontrol/reactutils
一些react的hooks

### @rontrol/request
对request响应进行符合前后端约束的错误验证。

### @rontrol/search
搜索框表单。通过状态流实现缓存。

### @rontrol/styles
对`emotion`封装，提供Theme支持和`select`语法糖。

### @rontrol/webworkerutils
提供对webworker简易的使用方式。

```typescript
// concat.worker.ts
import { createWorker } from "@rontrol/webworkerutils";
export default createWorker(({ values }: { values: string[] }) => values.join("."));

// main.ts
const concat = fromWorker(() => import("./workers/concat.worker"));
const concatFromWorker = async () => {
  const ret = await concat({ values: ["1", "2"] });
}
```

### @rontrol/validators
常用的表单验证规则。

### @rontrol/uikit
一些dom、事件操作的函数。

## @rontrol-dev

>提供构建，部署，生成typescript接口代码和请求代码功能。包含vite配置，babel插件，devkit脚本和monobundle构建脚本。

### @rontrol-dev/babel-preset
`babel`的一些preset设置。

### @rontrol-dev/babel-preset-css-prop
针对`emotion`和theme功能开发的一些`babel`插件。

### @rontrol-dev/devkit
初始化，打包，发布等功能的cli脚本。

### @rontrol-dev/eslint-config
`eslint`配置。

### @rontrol-dev/generate
对文件写入操作进行的一些封装。

### @rontrol-dev/generate-client
基于`@rontrol-dev/ts-gen-client-from-openapi`，`@rontrol-dev/ts-gen-core`和`@rontrol-dev/ts-gen-definitions-from-json-schema`,
根据openapi文件在项目内生成 http参数和响应的`typescript接口`定义代码 和 对应的http请求的`typescript`代码。

### @rontrol-dev/monobundle
模块构建工具。将每个模块打包出commonjs版的cjs文件和esmodule版的mjs文件，以及`.d.ts`声明文件。

### @rontrol-dev/vite-presets
vite的presets配置。

## 子packages
借鉴于`angular`的模块管理。一个依赖包可以包含多个子依赖包，减少引入依赖数量，方便管理。

```typescript
// e.g
import { createBootstrap } from "@react2rx/bootstrap";
import { useConfig } from "@react2rx/bootstrap/config";
```

### 如何实现？
`typescript`目前的模块化解析不能通过`package.json`的`exports`指定引入`.d.ts`声明文件路径，只能指定源码的引入路径。查看`angular`的构建后模块目录发现，`.d.ts`声明文件路径只能通过模块所在的路径解析。所以打包构建的时候需要将自模块的`.d.ts`声明文件与源码分开打包，源码放在`dist`内，`.d.ts`声明文件放在真实路径。例如`@react2rx/persistence/local-cache`，`local-cache`子模块的`.d.ts`声明文件在`@react2rx/persistence/local-cache/index.d.ts`，而源码继续放在`@react2rx/persistence/dist/local-cache/index.mjs`
