## React严格模式 `StrictMode`

`StrictMode`是一个仅在开发模式下运行，用来突出显示应用程序中潜在问题的工具。与`Fragment`一样，`StrictMode`不会渲染任何可见的 UI。它为其后代元素触发额外的检查和警告。

- 识别不安全的生命周期
- 关于使用过时字符串 ref API 的警告
- 关于使用废弃的 findDOMNode 方法的警告
- 检测意外的副作用
- 检测过时的 context API

这里重点说明`检测意外的副作用`。严格模式不是自动 检测意外的副作用，而是通过刻意重复调用以下函数来实现。`(仅在开发模式)`

- class 组件的 `constructor`，`render` 以及 `shouldComponentUpdate` 方法
- class 组件的生命周期方法 `getDerivedStateFromProps`
- 函数组件体
- hook函数 `useState`，`useMemo`, `useEffect`或者`useReducer`
- hook函数 `useRef` 重新初始化。

**注意这些函数是重复调用，并不是简单的react渲染两次的关系，所以调用的顺序会和想象中的不一样。`useState`，`useMemo`, `useReducer`和`useRef`会在`useEffect`前调用两次。**

>react的开发团队认为，纯函数式的代码执行1次和n应该没有区别，所以在严格模式下模拟两次以便暴露问题。(http请求是一个例外，调用多次请求因为业务原因可能是有区别的，而且快速调用两次来不及响应是没有意义的)

### `rxjs` 在严格模式中的问题

rxjs是需要`subscribe`和`unsubscribe`操作的，并且它们的次数一一对应，没有`unsubscribe`的`Observable`对象会有内存泄露的风险。所以`subscribe`和`unsubscribe`操作应该放在`useEffect`中形成调用次数一一对应的关系。

```typescripe
const { subscribe, unsubscribe} = useMemo(() => craeteObservableIns(), [])

useEffect(() => {
  subscribe();
  return unsubscribe;
}, []);
```

在脚手架的开发环境是全程开启严格模式的。建议项目开发中也开启严格模式以便发现问题。
>脚手架的开发环境只在`useRequest`的参数 `manual`为`false`时，使用的`useEffectOnce`通过判断是否为开发环境来跳过调用两次检查。(不跳过也可以，但会因为请求再快速取消打印多余的取消请求日志)



## 二级packages
借鉴于`angular`的模块管理。一个依赖包可以包含多个二级依赖包，减少引入依赖数量，方便管理。

```typescript
// e.g
import { createBootstrap } from "@react2rx/bootstrap";
import { useConfig } from "@react2rx/bootstrap/config";
```

### 如何实现？
`typescript`目前的模块化解析不能通过`package.json`的`exports`指定引入`.d.ts`声明文件路径，只能指定源码的引入路径。查看`angular`的构建后模块目录发现，`.d.ts`声明文件路径只能通过模块所在的路径解析。所以打包构建的时候需要将自模块的`.d.ts`声明文件与源码分开打包，源码放在`dist`内，`.d.ts`声明文件放在真实路径。例如`@react2rx/persistence/local-cache`，`local-cache`子模块的`.d.ts`声明文件在`@react2rx/persistence/local-cache/index.d.ts`，而源码继续放在`@react2rx/persistence/dist/local-cache/index.mjs`


