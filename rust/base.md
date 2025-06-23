
## cargo
包管理工具。

**Cargo.toml 和 Cargo.lock**
* Cargo.toml 是 cargo 特有的项目数据描述文件。它存储了项目的所有元配置信息，如果 Rust 开发者希望 Rust 项目能够按照期望的方式进行构建、测试和运行，那么，必须按照合理的方式构建 Cargo.toml。
* Cargo.lock 文件是 cargo 工具根据同一项目的 toml 文件生成的项目依赖详细清单，因此我们一般不用修改它，只需要对着 Cargo.toml 文件撸就行了。


## 变量
变量默认是不可变的。声明可变变量需要是用“mut”关键字。

```rust
let x = 5;
x = 6; // 错误 变量默认不可变

let mut y = 5;
y = 6;

let _xx = 5;
let yy = 6; // 警告 如果变量创建却未被使用会报错，在变量名前加'_'

// 常量必须要声明类型
const num: u32 = 100;
```
常量可以在任何作用域内声明，包含全局作用域，在声明作用域内常量在程序的整个过程中都有效。

### 变量解构

```rust
let (a, mut b):(bool, bool) = (true, false);
b = true;
```

### 变量遮蔽
再同一作用域中，后声明的同一变量会覆盖前面的。
```rust
fn main() {
    let x = 5;
    let x = x + 1; // 6
    {
        let x = x * 2; // 12
        println!("{}", x);
    }
    println!("{}", x); // 6
}
```

### 序列(Range)
```rust
// 输出1到5
for i in 1..=5 {
    println!("{}", i)
}

// 输出1到4
for i in 1..5 {
    println!("{}", i)
}
```

### 字符、布尔、单元类型

