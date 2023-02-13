## 升级建议

* 新开项目无脑选择
* 已有的中小型项目

## 相较于老版

1. **严格模式**完全可用;
2. 构建工具从`webpack`改为`vite`。开发环境构建更快(快10倍?)
3. `useRequest` 功能上相当于集合当初 `useRequest` 和 `useTempDataOfRequest`，并新增以下功能
   * 轮训(基于`rxjs`实现)
     - 可设置次数
     - 可设置间隔时间
   * 错误重试(`rxjs`实现)
     - 可设置次数
     - 可设置间隔时间
   * 缓存(基于状态流和request plugin实现)
     - 可设置过期时间；
     - 可选择存储介质 `Map` 和`localStorage`
     - 触发同样cacheKey都能触发更新
     - 缓存策略；(缓存优先 和 实效性优先)
   * 根据响应头 `cache-control` 或 `expires` 自动重新请求(`useTempDataOfRequest`功能之一；基于request plugin实现)
     
4. 一个依赖可以包含多个子依赖，减少依赖数量；
5. 暴露出的api拥有更智能的泛型支持和更完善的类型提示；
6. `react-router` v6.4，使用更简单；升级改动较大；
7. `axios` v1.2；改用`AbortController`取消请求；
8. `rxjs` v7.5；
9. 自己实现 `localforage` 包，减小打包体积；
10. 自己实现 `logger` 包(更好看log);

#### 看不见的优势

1. 函数式重构，代码简洁，性能提升(maybe?)，二次开发也更简单；
2. 源码采用更严格的类型检查和更智能的泛型支持；
3. 源码没有一个`typescripe`或`eslint`报错/警告；
4. 实现与`react` hook脱离(可用于`raect`以外)；
5. 及时更新和修复bug；
5. `useRequest` 诸多功能基于 plugin 实现，后续开放 plugin 接口供二次开发；
