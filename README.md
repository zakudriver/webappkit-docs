# webappkit

[webappkit](https://git.querycap.com/fe/webappkit) 是一个完全基于[`typescript`](https://www.typescriptlang.org/)，遵循函数式代码风格编写，[`react`](https://reactjs.org/)和[`rxjs`](https://rxjs.dev/)为核心开发的前端脚手架，旨在集开发、打包、发布于一体，提供简单的使用体验，创建高效的单页面应用。

## 环境

- node：16.x
- pnpm: 7.x
- npm 源：https://registry.npmjs.org 或 https://registry.yarnpkg.com, 禁止使用淘宝源
- typescript only

## clone即用

[webapp](https://git.querycap.com/fe/webapp) 模板

```bash
pnpm i

pnpm devkit dev sg
```

## 技术选型

#### 核心

- `react`
- `rxjs` ([常用的rxjs操作符文档](https://rxjs-cn.github.io/learn-rxjs-operators/))

#### 具有一定生态、不可替代的工具库

- [`react-router`](https://github.com/remix-run/react-router)
- [`axios`](https://github.com/axios/axios)
- [`lodash`](https://www.lodashjs.com/)
- [`emotion`](https://emotion.sh/docs/introduction)
- [`date-fns`](https://date-fns.org/)

