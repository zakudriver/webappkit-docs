## `persistence`
用于创建`Persistence`。`Persistence`是控制将`Store`状态流中状态读写进缓存`LocalCache`的模块。

```typescript
// 函数签名
const persistence = (name: string, temp = false): Persistence

interface Persistence {
  hydrate<T extends Record<string, any> = {}>(): T;
  connect(store$: Store): () => void;
}
```
1. `LocalCache`的`name`属性。
2. `temp`是否手动开启用`Map`变量来作为缓存。主要用于测试。
3. **返回**`Persistence`对象。

## `PersistenceConnect`
`PersistenceConnect`组件。用于将`Persistence`与`Store`连接。

```typescript
// 函数签名
const PersistenceConnect: FC<PersistenceConnectProps> = ({ persistence })
```

## `localCache`
`localCache`在`@react2rx/persistence`的子模块`@react2rx/persistence/local-cache`
用于创建`LocalCache`。如果环境不支持`localStorage`则会创建`Map`变量的`LocalCache`。

```typescript
// 函数签名
const localCache = ({ name = "app", temp }: LocalCacheOptions): LocalCache

interface LocalCache {
  clear(): void;
  writeByRecord(arg: Record<string, any>): Promise<Array<string | void> | void>;
  read<T = any>(key: string): T | undefined;
  readByExpired<T = any>(key: string, expireField?: string): T | undefined;
  write<T>(key: string, value: T): Promise<string | void>;
  remove(...key: string[]): void;
  iterate(iterator: (value: any | undefined, key: string, index: number) => void): void;
  has(key: string): boolean;
  keyPrefix: string;
  name: string;
}
```
