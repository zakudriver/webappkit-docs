## `createBootstrap`
`createBootstrap`是创建`react2rx`程序的入口函数。在这个函数内会把必要的组件如`Store`, `StoreProvider`, `logger`, `ConfigProvider`, `persistence`, `PersistenceConnect`, `React2rxRouter`等实例和组件初始化。

```typescript
// 函数签名
const createBootstrap =
  <T extends BaseConfig>(config: T) =>
  (e: ReactElement | (() => ReactElement), ...middlewares: Middleware[])
```
1. 传入`Record<string, string>`类型的`config`参数。如`api_addr`，`env`，`version`等等。
2. **返回**一个接收react入口组件和`Store`中间件的函数。

## `ConfigProvider`
`ConfigProvider`在`@react2rx/bootstrap`的子模块`@react2rx/bootstrap/config`
`ConfigProvider`是`config`的`Provider`组件。

## `useConfig`
从`ConfigProvider`获取`config`数据的hook。

```typescript
// 函数签名
const useConfig = <T extends Record<string, string>>(): T & BaseConfig
```

## `confLoader`
`config`数据的加载器。从meta标签里读取`config`数据。meta标签里的数据来自于启动项目时，`devkit`脚本从`src-app/xxx/config.ts`中读取数据后注入到html文件的meta标签，

```typescript
// 函数签名
const confLoader = (): (<T extends BaseConfig>() => T)
```

## `createLogger`
创建`logger`中间件，应用于`Store`中间件。在`createBootstrap`函数中开发环境加载，一般不直接使用。
`logger`会打印所有经过`Store`状态流的`prev`, `current` , `next`数据，方便开发调试。

```typescript
// 函数签名
const createLogger =
  (options?: LoggerOptions): Middleware =>
  (api) =>
  (next)

interface LoggerOptions {
  styles?: Styles;
  level?: LoggerLevel | IndepLevel;
  titleFormatter?(title: [string, AsyncStage | undefined], time: string, took: number): string;
  stateTransformer?(arg: any): any;
  actionTransformer?(arg: Actor): Actor;
  errorTransformer?(arg: any): any;
}

interface Middleware<TState = any> {
  (api: MiddlewareAPI<TState>): (next: Dispatch) => Dispatch;
}
```
