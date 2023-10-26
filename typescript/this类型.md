# this类型

'this'类型在 TypeScript 中用于指定方法内部的上下文类型，以便访问当前对象（类实例）的属性和方法。它有助于增强类型检查，并确保方法只能访问与当前实例相关的成员。
以下是有关 TypeScript 中'this'类型的说明和用法：

**在方法中使用 this 类型**
```typescript
class MyClass {
    value: number = 0;

    getValue(this: MyClass): number {
        return this.value;
    }
}

```
在上面的示例中，'this'类型用于指定getValue方法内部的上下文类型，以确保只能访问MyClass实例的属性。

**自动类型推断**
TypeScript 通常会自动推断'this'参数的类型，因此你不必显式指定。以下是一个示例：
```typescript
class MyClass {
    value: number = 0;

    getValue() {
        return this.value; // TypeScript 自动推断 this 的类型为 MyClass
    }
}
```

**箭头函数中的 this 类型**
箭头函数不需要'this'参数，因为它们捕获了外部上下文的'this'值。这意味着箭头函数中的'this'始终与定义它的上下文相关。例如
```typescript
class MyClass {
    value: number = 0;

    getValue = () => {
        return this.value; // 这里的 this 始终与 MyClass 实例相关
    }
}
```

**限制方法的使用**
使用'this'类型有助于限制方法的使用范围，确保它们只能在正确的上下文中调用。这有助于提高类型安全性

**参数数量**
当你在方法中使用'this'类型时，方法调用时可以省略'this'参数。TypeScript会自动处理'this'参数的传递。

Typescript官方文档
> https://www.typescriptlang.org/docs/handbook/2/classes.html#this-types
> https://www.typescriptlang.org/docs/handbook/2/functions.html#declaring-this-in-a-function