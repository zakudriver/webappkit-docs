## `useRequest`
将request整个生命周期接入了状态流，实现了`swr`(stale-while-revalidate)，缓存，缓存策略，轮询，重试，loadingDelay等，提供了完善的request功能的hook函数。

```typescript
// 函数签名
const useRequest = <TReq, TRespBody, TError>(
  actor: RequestActor<TReq, TRespBody, TError>,
  options: UseRequestOptions<TReq, TRespBody, TError>,
  deps: DependencyList = [],
): UseRequestReturn<TReq, TRespBody, TError>

interface UseRequestOptions<TReq, TRespBody, TError>
  extends RequestOptions<TReq, TRespBody, TError>,
    Partial<CachePluginOptions>,
    Partial<ExpiryPluginOptions>,
    Partial<LoadingDelayPluginOptions> {
  manual?: boolean;
  requestArgs?: TReq;
}

interface RequestOptions<TReq, TRespBody, TError> {
  polling?: number | RequestPolling;
  retry?: number | RequestRetry;
  onLoading?(actor: RequestActor<TReq, TRespBody, TError, RequestSpice>): void;
  onSuccess?(actor: RequestActorDone<RequestActor<TReq, TRespBody, TError>>): void;
  onFail?(actor: RequestActorFailed<RequestActor<TReq, TRespBody, TError>>): void;
  onFinish?(
    actor:
      | RequestActorDone<RequestActor<TReq, TRespBody, TError>>
      | RequestActorFailed<RequestActor<TReq, TRespBody, TError>>,
  ): void;
}

interface CachePluginOptions {
  cache?: CacheEnum | `${CacheEnum}`;
  cacheKey?: string;
  scope?: string;
  cacheSecs: number;
  cachePolicy: CachePolicy;
}

interface ExpiryPluginOptions {
  expiry?: boolean;
}

interface LoadingDelayPluginOptions {
  loadingDelay?: number;
}

interface UseRequestReturn<TReq, TRespBody, TError> {
  data?: TRespBody;
  error?: TError;
  request(arg?: TReq): void;
  loading: boolean;
  cancel(): void;
}

interface RequestRetry {
  count: number;
  delay?: number;
}

interface RequestPolling {
  count?: number;
  delay: number;
}
```

**UseRequestOptions**:
* `manual`: 是否手动触发请求。当`manual`为`true`时需求调用`request`手动触发，当`manual`为`false`时进入当前组件自动触发。*默认为 `true`*
* `requestArgs`: 请求参数。主要在`manual`为`false`时自动触发请求时设置以便携带参数。*默认为 `undefined`*
* `polling`: 轮询触发请求。*默认为 `undefined`，不轮询*
  - 当`polling`为`number`时，即表示传入的是轮询间隔时长(ms)；
  - 当`polling`为`RequestPolling`时，即完整设置轮询间隔时长`delay`和轮询次数`count`。
* `retry`: 错误重试请求。*默认为 `undefined`，不重试*
  - 当`retry`为`number`时，即表示传入的是重试次数，默认重试间隙时长`delay`为`500`；
  - 当`retry`为`RequestRetry`时，即完整设置重试间隔时长`delay`和重试次数`count`。
* `onLoading`: 触发`loading`执行的回调函数。因为存在缓存和`LoadingDelay`, `loading`触发的时机不一定在发起请求时立即触发，所以需要此回调函数。*默认为 `undefined`*
* `onSuccess`: 状态成功时执行的回调函数。包含请求所有信息和`requestActor`对象信息。*默认为 `undefined`*
* `onFail`: 状态失败时执行的回调函数。包含请求所有信息和`requestActor`对象信息。*默认为 `undefined`*
* `onFinish`: 状态成功/失败时都会执行的回调函数。包含请求所有信息和`requestActor`对象信息。*默认为 `undefined`*
* `cache`: 缓存类型。实现了`swr(stale-while-revalidate)`，请求响应后，能够触发所有同key`useRequest`的响应更新。*默认为 `undefined` 不缓存*
  - 当`cache`为`CacheEnum.LOCAL_STORAGE`时，缓存在`localStorage`。
  - 当`cache`为`CacheEnum.LOCAL_STORAGE_CROSS`时，缓存在`localStorage`，同步多tab页。
  - 当`cache`为`CacheEnum.TEMP_STORAGE`时，缓存在`Map`变量。
* `cacheKey`: 缓存key值。可以手动设置为一样/不一样的key来来控制触发同步响应。默认为 *根据发起请求的`requestActor`生成的key*。
* `scope`: `cacheKey`的作用域。用于手动区分同`requestActor`的请求，避免同key状态混淆。 *默认为 `undefined`*
* `cacheSecs`: 缓存有效时长，单位秒。*默认为 `60`*
* `cachePolicy`: 缓存策略。*默认为 `CachePolicyEnum.CACHE_FIRST`*
  - 当`cachePolicy`为`CachePolicyEnum.CACHE_FIRST`时，缓存存在并有效，则不发起请求使用缓存作为响应，否则发起请求，响应后写入缓存。
  - 当`cachePolicy`为`CachePolicyEnum.CACHE_AND_NETWORK`时，如果缓存存在并有效，则**先**使用缓存作为响应，然后马上发起请求，响应后**再次**使用响应并写入缓存；如果缓存不存在或无效，则发起请求，响应后写入缓存。*适用于响应及时性要求高的请求*
* `expiry`: 是否开启 根据响应头`cache-control`或`expires` 轮询触发请求。*默认为 `false` 不开启*
  - e.g: 响应头包含`"cache-control": "max-age=1"`，则每1s发起请求。
* `loadingDelay`: loading状态延迟时长，解决有些响应速度快频率loading状态切换的问题（例如开启缓存并`cachePolicy`为`CachePolicyEnum.CACHE_AND_NETWORK`时）。*默认为 `undefined` 不延长*
  - e.g: 当`loadingDelay`为500，则发起请求不立即触发loading，500ms后才触发。

**UseRequestReturn**:
* `data`: 成功状态响应数据。只包含响应数据本身，不含有请求信息和`requestActor`对象信息。方便组件中直接使用。
* `error`: 失败状态响应数据。只包含响应数据本身，不含有请求信息和`requestActor`对象信息。方便组件中直接使用。
* `request`: 请求方法。参数为请求参数。
* `loading`: loading状态。
* `cancel`: 取消请求方法。

## `useRequesting`
获取多个请求的loading状态。常用于第一次加载页面。

```typescript
// 函数签名
const useRequesting = (): Observable<boolean>
```

## `RequestProvider`
request的Provider组件。主要时通过传入`axios`配置和拦截器生成`axios`实例，和全局设置一些`useRequest`的options(`cacheSecs`, `loadingDelay`, `cachePolicy`, `retry`)。单独设置`useRequest`options优先级高于全局设置。

## `createAxiosInstance`
创建一个普通的`axios`实例。并对该实例的`paramsSerializer`，`transformRequest`和`transformResponse`进行处理。

```typescript
// 函数签名
const createAxiosInstance = (options: AxiosRequestConfig): AxiosInstance
```
1. `AxiosRequestConfig`配置。
2. **返回**`axios`实例。

## `applyInterceptors`
应用`axios`拦截器。

```typescript
// 函数签名
const applyInterceptors = (client: AxiosInstance, ...interceptors: RequestInterceptor[]): void

type RequestInterceptor = (
  request: AxiosInterceptorManager<AxiosRequestConfig>,
  response: AxiosInterceptorManager<AxiosResponse>,
) => void;
```
1. `axios`实例。
2. 数个拦截器函数。
2. 无**返回**。

## `createRequestEpicFromAxiosInstance`
根据`axios`实例创建`Epic`函数。以用于`epicOn`接入`Store`状态流，获取状态或`actor`对象发来的指令。

```typescript
// 函数签名
const createRequestEpicFromAxiosInstance = (client: AxiosInstance): Epic
```

## `createRequestEpic`
与`createRequestEpicFromAxiosInstance`类似。是`createAxiosInstance`+`applyInterceptors`+`createRequestEpicFromAxiosInstance`的组合。

```typescript
// 函数签名
const createRequestEpic = (options: AxiosRequestConfig, ...interceptors: RequestInterceptor[]): Epic
```

## `tapWhen`
`Store`状态流中流经与传入的`requestActor`对象相同时，触发回调函数。

```typescript
// 函数签名
const tapWhen =
  (next: () => void, ...actors: RequestActor[]): (actor$: Observable<Actor>) => Observable<never>
```

```tsx
{/* e.g */}
<>
  {epicOn(tapWhen(() => fetch(), postRequestActor))}
</>
{/* 调用postRequestActor后触发() => fetch(); */}
```

