
## cargo
包管理工具。

_变量在离开作用域后，就自动释放器占用的内存。_

**Cargo.toml 和 Cargo.lock**
* Cargo.toml 是 cargo 特有的项目数据描述文件。它存储了项目的所有元配置信息，如果 Rust 开发者希望 Rust 项目能够按照期望的方式进行构建、测试和运行，那么，必须按照合理的方式构建 Cargo.toml。
* Cargo.lock 文件是 cargo 工具根据同一项目的 toml 文件生成的项目依赖详细清单，因此我们一般不用修改它，只需要对着 Cargo.toml 文件撸就行了。

## 基本类型

### 变量
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
rust字符只能用''表示，""表示字符串。

单元类型就是()。

### 语句表达式
```rust
fn add_func(x: u32, y: u32) -> u32 {
    let x = x + 1;
    let y = y + 1; // 语句
    x + y; // 表达式
}

fn main() {
    let y = {
        let x = 6;
        x + 1
    };

    println("the val of y is: {}", y);
}
```

语句会执行操作，但没有返回值。
表达式会在求值后返回一个值，可以是语句的一部分。在表达式结尾处不能添加';'分号，否则会从表达式变为语句。
如果表达式不返回任何值，会隐式返回一个{}。

## 函数

永不返回的发散函数'!'。但是用'!'作为返回类型是，表示函数永不返回。
```rust
fn forever() -> ! {
    loop {

    }
}
```

## 所有权和借用
在栈上分配空间比在堆上分配空间要快，只需要将函数数据入栈即可。在堆上分配内存需要找到一块合适大小的内存空间。

### 所有权
**所有权原则**
1. Rust中每一个值都被一个变量所拥有，该变量被称为值的所有者。
2. 一个值同时只能被一个变量所拥有，或者说一个值只能拥有一个所有者。
3. 当所有着（变量）离开作用域范围时，这个值将被丢弃。

```rust
let x = 5;
let y = x;
// 没有发生所i有权转移，数据都在栈上，由于都是基本类型赋值是通过自动拷贝的方式实现。
```

```rust
let s1 = String::from("hello");
let s2 = s1; // 所有权发生转移
println!("{}", s1); // 执行会报错
```
函数的传参和返回值也会发生所有权转移。

### 引用与借用
获取变量的引用称之为**引用**。引用指向的变量默认是不可变的。  
_限制：同一个作用域，特定数据只能有一个可变引用。_    
_可变引用与不可变引用不能同时存在_

```rust
let mut s = String::from("hello");

let r1 = &s;
let r2 = &s;
println!("{} and {}.", r1, r2);

let r3 = &mut s;
println!("{}", r3);
```
编译器优化后r1、r2的作用域在println函数执行完后就结束了。r3可以顺利借用到可变引用。

**悬垂指针(Angling Reference)**
指针指向的值被释放掉了，而指针依然存在。

### 复合类型
_#![allow(unused_variables)] 告诉编译器忽略未使用的变量，不要抛出warning警告_   

#### 字符串
```
let s1 = "hello"; // &str
let s2 = String::from("hello world") // String
let s3 = &s2[1..3]' // &str
```
Rust中字符是Unicode类型，每个字符占用4个字节。
Rust在语言级别只有一种字符类型: str类型，通知以医用类型&str出现。str类型是硬编码进二进制文件中无法被修改。
String是标准库提供的可变长，可改变所有权的utf-8编码字符串。

**&str转String**
```
String::from("hello");
"hello".to_string()
```
**String转&str**
取引用即可
```
fn main() {
    let s = String::from("hello");
    say_hello(&s);
    say_hello(&s[..]);
    say_hello(s.as_str());
}

fn say_hello(s: &str) {
    println!("{}", s)
}
```

字符串中每种类型的字符占用的空间类型大小不一，不能使用下标的方式进行访问。如果使用切片获取字符串但没有包括字符的边界，则会报错。
```rust
let s = "中文";
let s2 = &s[0..2]; // 会报错
```


