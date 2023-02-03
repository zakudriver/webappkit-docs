> *优先级由上到下*


## 开发体验

1. **实现符合直觉**
   >* ~直接把变量绑定到window对象~
   >* ~没有通过现有设施/架构实现~

2. **完备的单元测试**

3. **遵循函数式编程**
   >* ui = fn(state)。函数式编程与UI界面开发契合，react本身也推崇函数式编程；
   >* 代码抽象程度高，避免状态可变的副作用，容易排查错误，方便单元测试；
   >* 充分运用`typescript`的类型系统，函数重载与泛型；
   >* 代码非常简洁优雅；

4. **可测试性**
   >* 新增/修改 模块/函数/组件 不影响整体
   >* 没有业务代码
   
5. **可二次开发性**
   >* 中间件设计
   >* 提供可开发的基础设施(actor模式)和接口

6. **代码简洁、规范**

7. **严格`typescript`类型检查和`eslint`风格检查**


## 使用体验

1. **api设计符合直觉**

* ~~Incorrect~~
```typescript
   export interface CacheStorager {
      clear(): void;
      read<T = any>(key: string): T | undefined;
      write<T>(key: string, value: T): Promise<string | void>;
      remove(...key: string[]): void;
      has(key: string): boolean;
    }
    
    interface Incorrect {
      cacheName: string;
      storager: CacheStorager | "memoryStorager" | "localStorager";
    }
```

* Correct
```typescript
    interface Correct {
      storager: CacheStorager;
    }

    type CreateStorager = {
      (type: "localStorager", cacheName: string): CacheStorager;
      (type: "memoryStorager"): CacheStorager;
    };
```

2. **api/props基本不会有重大修改**
   >暴露的api/props基本不会有重大修改，内部可能会优化改动

3. **简洁 细优于繁**
   - ~~Incorrect~~: 一个试图包罗万象、props项爆炸、众多true or false选项的组件；
   - Correct: 通过多个组件包裹，函数包装实现；

## 性能

1. **不追求绝对性能**
   >直接用c/c++性能最好，但我们更应该用更高级/抽象程度更高的工具来简单地实现复杂的东西；

   抽象?
   既：元素 -> 组件 -> react组件 -> 生命周期 -> hook

   >直接精确操作dom性能更高，但抽象出hook能更简易地开发，更轻易地实现更复杂地功能。typescript，rxjs，graphql等工具同理。

2. **在范式/技术选型下追求性能**

```typescript
  const data = [
    { name: "a", age: 20 },
    { name: "b", age: 10 },
    { name: "c", age: 0 },
  ];

  // Performance
  const names = [];
  const ages = [];

  for (let i = 0; i < data.length; i++) {
    names.push(data[i].name);
    ages.push(data[i].age);
  }


  // Incorrect
  const names = data.map(({ name }) => name);
  const ages = data.map(({ age }) => age);

  // Correct
  const [names, ages] = data.reduce(
    ([$, $1], { name, age }) => {
      $.push(name);
      $1.push(age);
      return [$, $1];
    },
    [[], []] as [string[], number[]],
  );
```
