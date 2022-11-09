## 包和 Crates

关于模块系统 (module system), 我们要讨论的第一部分就是包 (package) 和 crate.

一个 *crate* 是 Rust 编译器一次所考虑的最小的代码量. 即便您执行 `rustc` 命令而非
`cargo` 命令, 并且只向其传递单独一个源文件 (如同我们很早之前在第一章的
"编译和运行一个 Rust 程序" 中所做的那样), 编译器也将该文件视作一个 crate.
Crate 中可以包含若干模块, 而这些模块可以被定义在其它文件中, 但也会同该 crate
一起被编译, 我们将会在接下来几节中见到.

一个 crate 可以以两种形式出现: 一个二进制 crate (binary crate) 或者一个库 crate
(library crate). *二进制 crate* 是能被您编译为可执行文件的程序, 比如命令行界面程序
(command-line program) 或者服务器程序. 每个这样的程序必须具有一个叫做 `main`
的函数, 其决定该可执行文件运行时发生的事情. 到目前为止, 我们创建的所有 crate
均为二进制 crate.

*库 crates* 中没有 `main` 函数, 它们也不能被编译为可执行程序. 取而代之地,
它们中会定义一些将会被共享于若干个项目之间的功能. 例如, 我们在
[第二章][rand]<!-- ignore --> 中用到的名为 `rand` 的 crate 就提供了生成随机数的功能.
当 Rust 使用者说到 "crate" 时, 多数时候指的是库 crate; 他们口中的 "crate"
和编程语言中普遍具备的 "库" 的概念是可以互换的.

*crate root* (crate 根) 是一个源文件, Rust 编译器从它开始并构造出您 crate
的根模块 (我们将在 ["通过定义模块来控制作用域和私有性"][modules]<!-- ignore -->
一节中详细讲解各模块).

包 (package) 是提供一系列功能的一个或多个 crate 的集合. 每个包中包含一个
*Cargo.toml* 文件, 用来描述如何构建出这些 crate.
Cargo 本身就是一个包含着您一直以来用以构建代码的命令行工具的二进制 crate 的包.
Cargo 包里面同时也包含着该二进制 crate 所依赖的库 crate. 其他项目也可以选择依赖
Cargo 的库 crate 以使用和 Cargo 命令行工具同样的逻辑.

一个包中可以包含任意多个二进制 crate, 但是至多只能包含一个库 crate.
一个包必须包含至少一个 crate, 无论是库 crate 还是二进制 crate.

让我们看看当我们新建一个包时发生了什么. 首先, 我们输入 `cargo new` 命令:

```console
$ cargo new my-project
     Created binary (application) `my-project` package
$ ls my-project
Cargo.toml
src
$ ls my-project/src
main.rs
```

在运行了 `cargo new` 之后, 我们使用 `ls` 来查看 Cargo 创建了哪些内容. 可以看到,
在当前项目的目录里, 有着一个 *Cargo.toml* 文件, 这就意味着我们得到了一个包.
此外, 还有一个含有 *main.rs* 的 *src* 目录. 用您的文本编辑器打开 *Cargo.toml*,
将会发现其中没有任何涉及 *src/main.rs* 的内容. 这是因为 Cargo 遵循一个将
*src/main.rs* 视作与该包同名的二进制 crate 的 crate root 的约定. 类似地, 
如果包的目录下含有 *src/lib.rs*, Cargo 将能够得知, 这个包里面包含一个包同名的库
crate, 而 *src/lib.rs* 是它的 crate root. Cargo 会将 crate root 文件传递给
`rustc`, 以构建对应的库文件或者二进制文件.

现在, 我们有了一个仅包含 *src/main.rs* 的包, 意味着它只包含一个名为 `my-project`
的二进制 crate. 如果一个包同时含有 *src/main.rs* 和 *src/lib.rs*, 则它包含两个
crate: 一个二进制的和一个库的, 两者均与包同名. 通过在 *src/bin* 目录下放置多个文件,
一个包将会包含多个二进制 crate: 每个文件都对应着一个独立的二进制 crate.

[modules]: ch07-02-defining-modules-to-control-scope-and-privacy.html
[rand]: ch02-00-guessing-game-tutorial.html#generating-a-random-number
