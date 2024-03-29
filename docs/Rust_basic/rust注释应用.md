# 注释

注释(sttribute)是一种元数据标记，可用于`crate`、`module`，以及类型、函数等。

* `#[name]`: 作用于下一项。
* `#![name]`: 用于外部项(enclosing, crate)。

* `#![feature]`: 启用实验性(unstable)功能。
* `#![allow]`: 忽略警告。
* `#[cfg]`: 条件编译(test，debug，arch，os)。
* `#[inline]`: 函数内联。
* `#[test]`: 单元测试。
* `#[bench]`: 基准测试。

## 抑制警告

避免过多无害的编译提示，造成阅读障碍。

```rust
#![allow(dead_code)]
#![allow(unused_variables)]
#![allow(unused_imports)]
```

## 函数内联

向编译器提供函数内联建议。

* `#[inline]`
* `#[inline(always)]`
* `#[inline(never)]`

```rust
#[inline(always)]
fn add(x: i32, y: i32) -> i32 {
    x + y
}
```

## 条件编译

按平台和架构等条件选择编译。

```rust
#[cfg(debug_assertions)]
fn test() {
    println!("debug");
}

#[cfg(not(debug_assertions))]
fn test() {
    println!("release");
}
```

&nbsp;

[Attibutes](https://doc.rust-lang.org/reference/attributes.html)

[Conditional compilation](https://doc.rust-lang.org/reference/conditional-compilation.html)

[Procedural Macros](https://doc.rust-lang.org/reference/procedural-macros.html)
