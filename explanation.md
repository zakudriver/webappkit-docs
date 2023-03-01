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


## typescripe类型

### `typescript`类型系统究竟有多大作用？
> 在脚手架开发中有很大部分是为`typescript`编写的`interface`，泛型和一些类型体操般的泛型函数。`typescript`的类型系统真的值得我们花如此精力吗？

假如没有使用`typescript`，相信在编写代码实现上速度会快很多，但随着工程量的增加，各种函数作为参数的回调层层传递，在调用处需要传入什么参数，返回什么参数可能早已无迹可寻。在开发阶段可能会埋藏很多bug。在单元测试阶段或许能逐个暴露并修复。这样消耗的时间可能是差不多的。

暴露处的API对于使用者也是同样的问题，需要传入什么参数，返回什么参数都需要文档一一解释。

而`typescript`的类型能很好的解决的以上问题。在深度传递的变量或者函数使我们不必要关心其值本身，只需面向`interface`编程，只需知道传递到此的值肯定满足该`interface`即可，`interface`声明文件也能充当API文档的作用。而一系列泛型和类型体操般的泛型函数，可以使函数更灵活地调用，满足更多类型的参数类型。加上函数的重载使函数的功能性更加丰富。并且这系列优势都是建立在严丝合缝满足`typescripe`的类型系统。

并且在设计和编写`interface`时编码者对整个工程或系统在代码原理层面能有更清晰地认识，大大简化了设计阶段的心智压力，使更优雅和稳健的设计成为可能。
