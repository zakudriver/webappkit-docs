## 代码风格
建议开启`@rontrol-dev/eslint-config`，按`eslint`提示执行。

建议在**项目中**`eslint`配置加上对`interface`和`type`命名的约束。*`@rontrol-dev/eslint-config`还用于`webappkit`开发所以没有默认开启*
>即`interface`命名以`I`开头，`type`命名以`T`开头。以此与第三方库的`interface`和`type`区分。
```typescript
// Incorrect
interface ButtonProps {}

// Correct
interface IButtonProps {}
```

配置如下：
```json
"@typescript-eslint/naming-convention": [
      "error",
      {
        selector: "interface",
        format: ["PascalCase"],
        custom: {
          regex: "^I[A-Z]",
          match: true,
        },
      },
      {
        selector: "typeAlias",
        format: ["PascalCase"],
        custom: {
          regex: "^T[A-Z]",
          match: true,
        },
      },
    ],
```

## 目录结构

* **组件**
>组件目录建议以下结构。

  - Button
    - index.ts        // *required* 控制`export`导出哪些组件、函数、type或interface。
    - Button.tsx      // *required* Button 组件的实现。
    - types.ts        // *required* `type`或`interface`声明。
    - ButtonGroup.tsx // *optional* 根据组件复杂度或许会有多个组件组成。
    - icons.tsx       // *optional* 根据情况或许会有svg icon。
    - ctx.tsx         // *optional* 根据组件复杂度或许会有`react context`。
    - utils.tsx       // *optional* 根据组件复杂度或许会有辅助的工具函数。
    - tests           // *optional* 单元测试文件目录
      - Button.spec.tsx  // *optional* Button 单元测试文件
      - utils.spec.tsx   // *optional* utils 单元测试文件
      
* **hook**
  - use-debounce
    - index.ts        // *required* 控制`export`导出哪些函数、type或interface。
    - use_debounce.ts // *required* useDebounce hook的实现
    - types.ts        // *required* `type`或`interface`声明。
    - tests           // *optional* 单元测试文件目录n
      - use_debounce.spec.ts // *optional* useDebounce单元测试文件

* **普通模块**
>普通模块情况很多，只需遵守从`index.ts`导出模块即可。
   - actor
     - index.ts
     - actor.ts
     - types.ts
     - utils.ts
     - request_actor.ts
     - tests
       - actor.spec.ts

## 为什么还要从`index.ts`导出？
> 从`index.ts`看似有点画蛇添足，因为明明可以直接从文件导出。

在`angular`上有`model`的概念可以控制，在`golang`上也有以大写开头命名的函数或interface才允许导出。

因为从`index.ts`导出可以控制`export`导出哪些组件、函数、type或interface，哪些又不需要导出，使模块更具封装性，减少重名函数，interface或type的可能性。

而react不是框架或语言，没办法从语言层面控制，或很难从编译层面控制(除非有`angular`开发团队的技术力...)。从约定层面却很容易实现，只需开发者遵守即可。

## 关于文件夹和文件命名
>以下**只是建议**。同一个项目中能保持一致其实就很不错了。

* 文件夹名
  - react组件
  >建议使用reac约定俗称的风格，PascalCase。即`ButtonGroup`, `SelectInput`
  - 其他情况(**只是建议**)
  >驼峰命名很明显不是一个清晰明了的方案，在react组件上也是约定俗称不得已而为之。根据在github上看了很多第三方库的情况，建议使用kebab-case。即`local-cache`, `request-actor`

* 文件名
  - react组件
  >建议使用reac约定俗称的风格，PascalCase。即`ButtonGroup.tsx`, `SelectInput.tsx`
  - 其他情况(**只是建议**)
  >同上。根据在github上看了很多第三方库的情况，建议使用snake_case。即`request_actor.ts`, `request_epic.ts`, `loading_delay_plugin.ts`
