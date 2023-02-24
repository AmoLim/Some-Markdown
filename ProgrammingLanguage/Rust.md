# 3. 常见编程概念

## 3.1 变量和可变性

在Rust中，可以运用 `let` 语句去定义变量，变量是默认不可变（immutable）的。

> 在尝试改变预设为不可变的值时，产生编译时错误是很重要的，因为这种情况可能导致 bug。如果一部分代码假设一个值永远也不会改变，而另一部分代码改变了这个值，第一部分代码就有可能以不可预料的方式运行。不得不承认这种 bug 的起因难以跟踪，尤其是第二部分代码只是 **有时** 会改变值。

Rust以这种方式保证了变量内容的安全性，当有另一部分代码尝试修改某些预设不变的值时，将会无法通过编译，从根本上杜绝了上述bug的产生。

变量的可变性是一个非常重要的特性，Rust提供了 `mut` 关键词，能显性声明一个变量具有可变性。`mut` 也向读者表明了其他代码将会改变这个变量值的意图。

示例代码
``` rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

### 常量

*常量(constants)* 是绑定到一个名称的不允许改变的值，虽然很类似于不可变变量，但是仍有不同。

1. 不允许对常量使用 `mut` 关键字，这保证了常量的永不可变特性。
2. 声明常量使用的是 `const` 而不是 `let`，并且***必须***注明常量的数据类型。
3. 常量只能设置为常量表达式，即此表达式必须在运行前就能计算出结果。

示例代码
``` rust
const THREE_HOURS_IN_SECONDS : u32 = 60 * 60 * 3;
```

注意，Rust对常量的命名约定是在单词间使用全大写和下划线连接。在定义常量时使用常量表达式而非常量本身，以此来增强代码的可读性。如上例，用60 * 60 * 3来定义而非10800这一结果值。

>在声明它的作用域中，常量在整个程序的生命周期都有效，此属性使得常量可以作为多处代码使用的全局范围的值，例如一个游戏中所有玩家可以获取的最高分或者光速。
>
>将遍布于应用程序中的硬编码值声明为常量，能帮助后来的代码维护人员了解值的意图。如果将来需要修改硬编码值，也只需修改汇聚于一处的硬编码值。

### 隐藏(Shadowing)

Rust的特性——隐藏(Shadowing)可以让我们可以定义一个与之前变量同名的新变量，就如同第一个变量被后一个第二个新变量***隐藏***了。这以为着在新变量被隐藏前或声明其的作用域结束前，编译器都看到的是第二个变量，任何使用该变量名的行为都会视为在使用第二个变量。

可以使用相同变量名称来隐藏第一个变量，或者重复使用 `let` 关键词来多次隐藏。

示例代码
``` rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");
}

```

运行结果
```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6
```

注意，隐藏与mut是有区别的。

- 当不小心对变量重新赋值时，若没有使用 `let` 关键字，则会导致编译错误。这个特性使得在写程序时，需要更加谨慎地考虑到变量值的安全性。
	通过使用 `let` 关键字，我们可以用这个值进行一些计算，但是在计算之后变量仍是不可变的。

- 使用 `let` 隐藏一个变量时，实际上是创造了一个新的变量，即我们可以改变这个变量名所绑定的变量类型，而 `mut` 关键字无法改变一个变量的类型。
	例如，假设程序请求用户输入空格字符来说明希望在文本之间显示多少个空格，接下来我们想将输入存储成数字（多少个空格）：
	``` rust
	fn main() {
	    let spaces = "   ";
	    let spaces = spaces.len();
	}
	```
	第一个 `spaces` 是字符串类型，第二个 `spaces` 则通过 `let` 用数字类型将字符串类型隐藏了起来。 隐藏使我们不必使用不同的名字，如 `spaces_str` 或 `spaces_num`，相反只需要使用 `spaces` 这一简单的名字即可。


## 3.2 数据类型