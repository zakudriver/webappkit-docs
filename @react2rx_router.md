## `Router`
`Router`组件。由于新版`react-router`取消了对[`remix-run/history`](https://github.com/remix-run/history)的依赖，而是自己内部实现了一个轻量的`history`，不再提供`useHistory`hook，导致`history.listen`无法使用。所以基于`react-router`的`Router`封装，没有直接使用`BrowserRouter`作为`router`。

```typescript
// 函数签名
const Router: FC<PropsWithChildren<RouterProps>> = ({ basename, children })
```

## `useRouter`
相当于`useLocation`, `useNavigate`，`useParams`和`useHistory`的集合。

```typescript
// 函数签名
const useRouter = <T extends string | Record<string, string | undefined>>(): RouterContext<Partial<T>>

interface RouterContext<TParameters> {
  match: {
    params: TParameters;
    pathname: string;
    url: string;
  };
  location: Location;
  history: History;
}
```

## `HistoryProvider`
创建`history`和`Provider`组件。可从外部传入`history`（一般用于测试）。

```typescript
// 函数签名
const HistoryProvider: FC<PropsWithChildren<HistoryProviderProps>> = ({ children, hash, history: ht })
```

## `useHistory`
获取`History`实例

```typescript
// 函数签名
const useHistory = (): History
```


## `React2rxRouterConnect`
`router`与`Store`连接的组件。路由操作(`push`,`replace`,`go`,`back`,`forward`)将通过`actor`对象传递。方便在`Store`状态流中截取路由操作以做出一些处理。

```typescript
// 函数签名
const React2rxRouterConnect = (): null
```

## `React2rxRouter`
`React2rxRouter`是`HistoryProvider`,`React2rxConnect`,`Router`的打包，一般直接使用它即可。

```typescript
// 函数签名
const React2rxRouter: FC<PropsWithChildren<React2rxRouterProps>> = ({ children, basename, history })
```

## `useLinkClickHandler`
`useLinkClickHandler`是对点击事件屏蔽除鼠标左键以外，组合键和默认事件，并添加跳转路由逻辑。它是`Link`组件的主要实现。一般用直接使用。

```typescript
// 函数签名
const useLinkClickHandler = <E extends Element = HTMLAnchorElement>(
  to: To,
  { target, replace: replaceProp, state }: UseLinkClickHandlerOptions = {},
): ((event: MouseEvent<E>) => void)
```

## `Link`
`Link`组件是对a标签的包裹，并屏蔽除鼠标左键以外，组合键和默认事件，只包含跳转路由逻辑。

```typescript
// 函数签名
const Link = forwardRef<HTMLAnchorElement, LinkProps>(function LinkWithRef(
  { onClick, reloadDocument, replace = false, state, target, to, ...rest },
  ref,
)
```

## `NavLink`
`NavLink`组件是对`Link`的包裹。提供`active`属性，适用于导航的选中状态。

```typescript
// 函数签名
const NavLink = forwardRef<HTMLAnchorElement, NavLinkProps>(function NavLinkWithRef(
  {
    "aria-current": ariaCurrentProp = "page",
    caseSensitive = false,
    className: classNameProp = "",
    end = false,
    style: styleProp,
    to,
    exact = false,
    ...rest
  },
  ref,
)
```

## `useBlocker`
路由守卫。是对`history.block`的封装。适用于表单提交前对跳转路由的拦截操作。

```typescript
// 函数签名
const useBlocker = (blocker: Blocker, when = true): void
```

## `Prompt`
路由守卫组件。是`useBlocker`的应用，并增加弹窗提示功能。适用于表单提交前对跳转路由的拦截操作。

```typescript
// 函数签名
const Prompt: FC<PromptProps> = ({ message, when = true })
```

