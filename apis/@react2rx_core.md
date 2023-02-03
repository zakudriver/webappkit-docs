## `actor`
> `actor`对象本身只包含一些信息属性，没有方法。`actor`对象相当于传达指令或者数据载体，执行具体的操作应该放在通过`epicOn`截取对应的`actor`对象来实现。

`actor`函数是关于`actor`对象一切操作的集合。包含：
* `actor`函数本身。即创建`actor`对象。
  ```typescript
  // 函数签名
  const actor = <TArg = any, TOpts = any>({
    group = "UN_GROUPED",
    name = "",
    effect,
    stage,
    arg = {} as TArg,
    opts = {} as TOpts,
  }: ActorOptions<TArg, TOpts>): Actor<TArg, TOpts>
  ```
* `of`函数。只传入`gourp`属性快捷创建`actor`对象。
  ```typescript
  // 函数签名
  actor.of = <TArg = any, TOpts = any>(group: string): Actor<TArg, TOpts>
  ```
* `sameGroupp`函数。判断两个`actor`对象的`gourp`属性是否相同。*sameGroup最后的p即是`predicate`*
  ```typescript
  // 函数签名
  actor.sameGroupp = <T extends Actor, P extends Actor>(a: T, b: P): boolean
  ```
* `likep`函数。判断两个`actor`对象是否相同。
  ```typescript
  // 函数签名
  actor.likep = <T extends Actor, P extends Actor>(a: T, b: P): boolean
  ```
* `namedWith`函数。修改`actor`对象的`name`属性。可选修改`opts`属性。返回新的`actor`对象。
  ```typescript
  // 函数签名
  actor.namedWith = <T extends Actor = Actor, TArg = ActorGenericNth<T, 0>, TOpts = ActorGenericNth<T, 1>>(
    actor: T,
    name: string,
    opts?: TOpts,
  ): TargetActor<T, TArg, TOpts>

  type TargetActor<T, P = ActorGenericNth<T, 0>, S = ActorGenericNth<T, 1>> = T extends RequestActor
    ? RequestActor<P, S, ActorGenericNth<T, 2>>
    : Actor<P, S>;
  ```
  1. 要修改的`actor`对象。
  2. `name`属性。
  3. 可选`opts`属性。
  4. **返回**新的`actor`对象。`TargetActor`是一个根据传入的`Actor`类型进行判断是`Actor`还是`RequestActor`类型的泛型工具函数。以达到在类型系统层面，传入`Actor`返回`Actor`类型，传入`RequestActor`返回`RequestActor`类型的效果。
* `effectWith`函数。修改`actor`对象的`effect`属性。返回新的`actor`对象。
  >`effect`属性的作用：`effect`属性是一个函数。`actor`对象传达指令时，如果拥有`effect`属性则会调用`effect`函数来修改`Store`的状态流。
  ```typescript
  // 函数签名
  actor.effectWith = <T extends Actor = Actor>(
    actor: T,
    effect: <TState>(state: TState, actor: Actor) => TState | undefined,
  ): T
  ```
  1. 要修改的`actor`对象。
  2. `effect`属性。
  3. **返回**新的`actor`对象。
* `invokeWith`函数。通过`Store`以调用`actor`对象。
  ```typescript
  // 函数签名
  actor.invokeWith = <T extends Actor>(actor: T, dispatcher: { dispatch: (actor: Actor) => void }): void
  ```
* `typeWith`函数。生成`actor`对象的type签名，即`@@${group}/${name}${stage}${opts}`。
  ```typescript
  // 函数签名
  actor.typeWith = <T extends Actor>(actor: T): string
  ```

## `requestActor`
> `requestActor`对象基于`actor`对象的扩充，增加了一些属性。所以`requestActor`对象可以使用`actor`上的所有函数，但`requestActor`函数则只有`requestActor`对象能够使用。
* `requestActor`函数本身。即创建`requestActor`对象。
  ```typescript
  // 函数签名
  const requestActor = <TReq = any, TRespBody = any, TError = any>({
    requestOptsFromReq,
    ...args
  }: RequestActorOptions<TReq, TRespBody>): RequestActor<TReq, TRespBody, TError>
  ```
* `of`函数。只传入`name`属性和`requestOptsFromReq`属性快捷创建`actor`对象。`requestOptsFromReq`属性是一个组装http请求参数的函数。
  ```typescript
  // 函数签名
  requestActor.of = <TReq = any, TRespBody = any, TError = any>(
    name: string,
    requestOptsFromReq: (arg: TReq) => RequestOpts,
  ): RequestActor<TReq, TRespBody, TError>
  ```
* `startedWith`函数。通过`actor`或`requestActor`对象生成`RequestActorStarted`对象。`RequestActorStarted`对象表示请求成功状态并记录该请求的`requestActor`对象信息。
  ```typescript
  // 函数签名
  requestActor.startedWith = <T extends Actor>(actor: T): RequestActorStarted<T>

  type RequestActorStarted<T extends Actor> = Actor<AxiosRequestConfig<ActorGenericNth<T, 0>>, { parentActor: T }>;
  ```
  1. `RequestActorStarted`类型是一个`actor`对象。它的`arg`属性是`AxiosRequestConfig`类型，`AxiosRequestConfig`类型的泛型是响应类型，响应类型是最开始传入的`requestActor`对象的第一个泛型参数，即`ActorGenericNth<T, 0>`。`RequestActorCancel`，`RequestActorDone`和`RequestActorFailed`类型都大同小异。
* `cancelWith`函数。通过`actor`或`requestActor`对象生成`RequestActorCancel`对象。表取消状态。
  ```typescript
  // 函数签名
  requestActor.cancelWith = <T extends Actor>(actor: T): RequestActorCancel<T>
  ```
* `doneWith`函数。通过`actor`或`requestActor`对象生成`RequestActorDone`对象。表完成状态。
  ```typescript
  // 函数签名
  requestActor.doneWith = <T extends Actor>(actor: T): RequestActorDone<T>
  ```
* `failedWith`函数。通过`actor`或`requestActor`对象生成`RequestActorFailed`对象。表失败状态。
  ```typescript
  // 函数签名
  requestActor.failedWith = <T extends Actor>(actor: T): RequestActorFailed<T>
  ```
* `preRequestActorp`函数。判断传入的`actor`对象是不是`request`指令的`actor`对象。
  ```typescript
  // 函数签名
  requestActor.preRequestActorp = (actor: Actor): actor is RequestActor
  ```
* `cancelRequestActorp`函数。判断传入的`requestActor`对象是不是取消状态。
  ```typescript
  // 函数签名
  requestActor.cancelRequestActorp = (
    actor: Actor,
  ): actor is Actor<AxiosResponse<any>, { parentActor: RequestActor }>
  ```
* `failedRequestActorp`函数。判断传入的`requestActor`对象是不是失败状态。
  ```typescript
  // 函数签名
  requestActor.failedRequestActorp = (
    actor: Actor,
  ): actor is Actor<AxiosResponse<any>, { parentActor: RequestActor }>
  ```
* `requestActorp`函数。判断传入的`actor`对象是不是`requestActor`对象。
  ```typescript
  // 函数签名
  requestActor.requestActorp = (
    actor: Actor,
  ): actor is RequestActorDone<RequestActor> | RequestActorFailed<RequestActor> | RequestActorStarted<RequestActor>
  ```

## `createContext`
`createContext` 是一个简单的为快速创建带有**完整typescript类型**react context的`useContext`，`Provider`和`Context`的工具函数。

```typescript
// 函数签名
const createContext = <T>(value = {} as T): [<P extends T>() => P, Provider<T>, Context<T>]
```

*`createContext`函数*
1. `context`默认值。
2. **返回**包含`useContext`，`Provider`和`Context`的元祖。

## `useStore`
从context获取`Store`实例。

```typescript
// 函数签名
const useStore = <TState = any>(): Store<TState>
```

*`useStore`hook函数*
1. **返回**`Store`实例。

## `useObservableEffect`
在react中便捷订阅`rxjs`的`Observable`对象的方式之一。一般通过`tap`操作符从订阅的`Observable`对象中读取数据。

```typescript
// 函数签名
const useObservableEffect = (effect: () => ArrayOrNone<Observable<any>>, deps: any[] = []): void
```

*`useObservableEffect`hook函数*
1. 一个返回`Observable`或`Observable[]`或`undefined`的函数。
2. 重新触发`Observable`订阅的依赖。
3. 无**返回**。

```typescript
// e.g
useObservableEffect(() => {
    return resourceExpiresIn$.pipe(
      switchMap((expiresIn) => (expiresIn ? timer(expiresIn) : EMPTY)),
      tap(() => {
        request();
      }),
    );
  }, []);
```

## `useObservableLayoutEffect`
在react中便捷订阅`rxjs`的`Observable`对象的方式之一。与`useObservableEffect`类似，区别在于是在`useEffect`还是`useLayoutEffect`中进行订阅`Observable`。

```typescript
// 函数签名
const useObservableLayoutEffect = (effect: () => ArrayOrNone<Observable<any>>, deps: any[] = []): void
```

## `useObservable`
在react中便捷订阅`rxjs`的`Observable`对象的方式之一。返回`useState`的state，state改变会触发渲染，方便直接在组件中消费`Observable`状态。

```typescript
// 函数签名
const useObservable = <T,>(ob$?: Observable<T> & { value?: T }, defaultValue?: T): T | undefined
```

*`useObservable`hook函数*
1. 一个`Observable`对象。
2. 可选默认状态。
3. **返回**从`Observable`对象中获取到的状态。

## `renderOn`
在react中根据`Observable`对象状态渲染组件。

```typescript
// 函数签名
const renderOn = <T,>(ob$: Observable<T>, render: (state?: T) => ReactNode): ReactElement
```

*`renderOn`函数*
1. 一个`Observable`对象。
2. 一个`render`函数，`render`函数的`state`参数是订阅后的`Observable`状态，`render`函数会随着`state`改变重新渲染。
3. **返回**react组件。

## `useConn`
从`Store`或其他`Observable`对象中通过`Mapper`函数筛选状态。`Mapper`接口的`equalFn`属性可以通过比较过滤筛选的状态。

```typescript
// 函数签名
const useConn = <T, TOutput>(
  ob$: Observable<T>,
  mapper: Mapper<T, TOutput>,
  deps: any[] = [],
): Volume<T, TOutput>

interface Mapper<T, TOutput> {
  (state?: T): TOutput;
  equalFn?: EqualFn;
}

interface Volume<T, TOutput = T> extends Observable<TOutput> {
  getValue(): TOutput;
}
```

*`useConn`hook函数*
1. `Observable`对象。
2. 实现`Mapper`接口的函数，`Mapper`接口的`equalFn`属性可以实现一个比较的`prev`和`current`值的函数。
3. 重新触发`Observable`订阅的依赖。
4. **返回**实现`Volume`接口的`Observable`对象。

## `useStoreConn`
与`useConn`类似。省略`Observable`参数，只从`Store`中筛选状态。

```typescript
// 函数签名
const useStoreConn = <T, TOutput>(
  mapper: Mapper<T, TOutput> = (v) => v as unknown as TOutput,
  deps: any[] = [],
): Observable<TOutput>
```

## `useStoreSelector`
与`useStoreConn`类似。省略订阅操作，直接返回`useState`的state。

```typescript
// 函数签名
const useStoreSelector = <T, TOutput>(mapper?: Mapper<T, TOutput>, deps: any[] = []): TOutput | undefined 
```

## `withEqualFn`
将普通函数组装成`Mapper`以实现`Mapper`接口。

```typescript
// 函数签名
const withEqualFn = <T, TOutput>(mapper: (state: T) => TOutput, equalFn: EqualFn): Mapper<T, TOutput>
```

## `useEpic`
从`Store`的状态流中通过`epic`函数获取流经的`actor`对象。方便根据`actor`对象上的信息进行一些操作。比如过滤到某个请求的`actor`对象来实现一旦发起某一个请求就执行xxx，或过滤到router操作的`actor`对象，实现每次跳转router就执行xxx。

```typescript
// 函数签名
const useEpic = <TState = {}, TActor extends Actor = Actor>(
  epic: Epic<TState, TActor>,
  deps: any[] = [],
): void

interface Epic<TState = {}, TActor extends Actor = Actor> {
  (actor$: Observable<TActor>, so$: Store<TState>): Observable<TActor>;
}
```

*`useEpic`hook函数*
1. 实现`Epic`接口的函数。
2. 重新触发`Store`中`actor$`订阅的依赖。
3. 无**返回**。

## `epicOn`
`useEpic`的另一种形式。在`jsx`中调用。

```typescript
// 函数签名
const epicOn = (epic: Epic, inputs?: any[]): ReactElement
```

## `volume`
创建`Volume`对象。`Volume`对象是基于`Observable`对象从中通过`Mapper`函数筛选并保存状态。

```typescript
// 函数签名
const volume = <T, TOutput>(
  state$: Observable<T> & { value?: T },
  mapper: (state?: T) => TOutput,
  equalFn: EqualFn = shallowEqual,
): Volume<T, TOutput>
```

## `compose`
组装函数成中间件形式的洋葱模式函数。

```typescript
// 函数签名
const compose = <T>(...fns: Array<(...args: T[]) => T>): ((args: T) => T)
```

## `shallowEqual`
深度比较是否相等。

```typescript
// 函数签名
const shallowEqual = (objA: any, objB: any): boolean
```

## `composeEpics`
将多个`epic`函数组装成一个`epic`函数。即为 上一个`epic`函数的结果为下一个`epic`函数的参数 层层嵌套的一个函数。 

```typescript
// 函数签名
const composeEpics = <T>(...epics: Array<Epic<T>>): Epic<T>
```

## `piper`
适用于函数式编程的工具函数。

根据函数重载分为两种情况：
1. 传入的第一个参数为第二个参数（函数）的参数，第二个参数（函数）的结果为第三个参数（函数）的参数。。。以此类推。
2. 除了第一个参数都为元祖，即[(arg0, arg1, arg2) => r , arg1, arg2]。传入的第一个参数为第二个参数（元祖）的第一项（第一项是函数）的第一个参数，第二个参数（元祖）的剩余项是第一项的其他参数；第三个参数（元祖）的第一项（第一项是函数）的第一个参数是上个参数（元祖）的第一项函数的结果，第三个参数（元祖）的剩余项是第一项的其他参数。。。以此类推。

```typescript
// 函数签名
const piper: Piper = (target: unknown, ...fns: any[])

type Piper = {
  <T>(target: T): T;

  <T, A>(target: T, op1: UnaryFunction<T, A>): A;
  <T, P1 extends any[], A>(target: T, op1: OperatorTulpe<P1, T, A>): A;

  <T, A, B>(target: T, op1: UnaryFunction<T, A>, op2: UnaryFunction<A, B>): B;
  <T, P1 extends any[], A, P2 extends any[], B>(
    target: T,
    op1: OperatorTulpe<P1, T, A>,
    op2: OperatorTulpe<P2, A, B>,
  ): B;

  <T, A, B, C>(target: T, op1: UnaryFunction<T, A>, op2: UnaryFunction<A, B>, op3: UnaryFunction<B, C>): C;
  <T, P1 extends any[], A, P2 extends any[], B, P3 extends any[], C>(
    target: T,
    op1: OperatorTulpe<P1, T, A>,
    op2: OperatorTulpe<P2, A, B>,
    op3: OperatorTulpe<P3, B, C>,
  ): C;

  <T, A, B, C, D>(
    target: T,
    op1: UnaryFunction<T, A>,
    op2: UnaryFunction<A, B>,
    op3: UnaryFunction<B, C>,
    op4: UnaryFunction<C, D>,
  ): D;
  <T, P1 extends any[], A, P2 extends any[], B, P3 extends any[], C, P4 extends any[], D>(
    target: T,
    op1: OperatorTulpe<P1, T, A>,
    op2: OperatorTulpe<P2, A, B>,
    op3: OperatorTulpe<P3, B, C>,
    op4: OperatorTulpe<P4, C, D>,
  ): C;
};

interface UnaryFunction<T, R> {
  (source: T): R;
}

type OperatorTulpe<T extends any[], A, R> = [(a: A, ...args: T) => R, ...T];
```

```typescript
// e.g
const r = piper(
  0,
  (arg) => arg + 1,
  (arg) => `${arg}`,
); // "1"

const r = piper(
  { name: "zz" },
  [
    (a, b, c) => {
      return { a: a.name, b, c };
    },
    11,
    false,
  ],
  [
    (a, [b], c) => {
      return c(b + a.b);
    },
    [2],
    (arg: number) => arg,
  ],
); // 13
```

```typescript
const { of: aof, namedWith, doWith, effectOnWith, likep, invokeWith } = actor;

// before
const pong = effectOnWith(namedWith(aof("test"), "pong"), "pong", (state = 0) => state + 1);

// after
const pong = piper("test", [aof], [namedWith, "pong"], [effectOnWith, "pong", (state = 0) => state + 1]);
```
