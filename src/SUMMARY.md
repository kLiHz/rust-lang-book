# The Rust Programming Language

[Rust 编程语言](title-page.md)
[前言](foreword.md)
[介绍](ch00-00-introduction.md)

# 开始入门

- [开始入门](ch01-00-getting-started.md)
    - [安装](ch01-01-installation.md)
    - [Hello, World!](ch01-02-hello-world.md)
    - [Hello, Cargo!](ch01-03-hello-cargo.md)

- [编写一个猜谜游戏程序](ch02-00-guessing-game-tutorial.md)

- [常见编程概念](ch03-00-common-programming-concepts.md)
    - [变量与可变性](ch03-01-variables-and-mutability.md)
    - [数据类型](ch03-02-data-types.md)
    - [函数](ch03-03-how-functions-work.md)
    - [注释](ch03-04-comments.md)
    - [控制流](ch03-05-control-flow.md)

- [理解所有权 (Ownership)](ch04-00-understanding-ownership.md)
    - [什么是所有权?](ch04-01-what-is-ownership.md)
    - [引用与借用](ch04-02-references-and-borrowing.md)
    - [切片 (Slice) 类型](ch04-03-slices.md)

- [使用结构体 (structs) 类型以组织相互关联的数据](ch05-00-structs.md)
    - [定义与实例化结构体](ch05-01-defining-structs.md)
    - [一个使用结构体的示例程序](ch05-02-example-structs.md)
    - [成员方法 (method) 相关的语法](ch05-03-method-syntax.md)

- [枚举 (Enums) 和模式匹配 (Pattern Matching)](ch06-00-enums.md)
    - [定义一个枚举](ch06-01-defining-an-enum.md)
    - [`match` 控制流结构](ch06-02-match.md)
    - [使用 `if let` 简化控制流](ch06-03-if-let.md)

# Rust 基本知识

- [使用包 (Packages), Crate 和模块 (Modules) 管理日益增长的项目](ch07-00-managing-growing-projects-with-packages-crates-and-modules.md)
    - [包和 Crates](ch07-01-packages-and-crates.md)
    - [定义模块以控制作用域和私有性](ch07-02-defining-modules-to-control-scope-and-privacy.md)
    - [用路径引用模块树中的项](ch07-03-paths-for-referring-to-an-item-in-the-module-tree.md)
    - [使用 `use` 关键字将路径引入作用域](ch07-04-bringing-paths-into-scope-with-the-use-keyword.md)
    - [将模块拆分到不同的文件中](ch07-05-separating-modules-into-different-files.md)

- [常用的数据集合 (Collections)](ch08-00-common-collections.md)
    - [使用向量 (Vectors) 存储列表](ch08-01-vectors.md)
    - [使用字符串 (Strings) 存储 UTF-8 编码的文本](ch08-02-strings.md)
    - [将键及其关联值存储在散列表 (Hash Maps) 中](ch08-03-hash-maps.md)

- [错误处理](ch09-00-error-handling.md)
    - [用 `panic!` 处理不可恢复的错误](ch09-01-unrecoverable-errors-with-panic.md)
    - [用 `Result` 处理可恢复的错误](ch09-02-recoverable-errors-with-result.md)
    - [该 `panic!` 还是不该 `panic!`](ch09-03-to-panic-or-not-to-panic.md)

- [泛型, 特征 (Traits) 和生命期](ch10-00-generics.md)
    - [泛数据类型](ch10-01-syntax.md)
    - [特征 (Traits): 定义共同的行为](ch10-02-traits.md)
    - [使用生命期验证引用的有效性](ch10-03-lifetime-syntax.md)

- [编写自动化测试](ch11-00-testing.md)
    - [如何编写测试](ch11-01-writing-tests.md)
    - [控制测试如何被执行](ch11-02-running-tests.md)
    - [测试的组织](ch11-03-test-organization.md)

- [一个 I/O 项目: 构建一个命令行界面程序](ch12-00-an-io-project.md)
    - [接受命令行参数](ch12-01-accepting-command-line-arguments.md)
    - [读取文件](ch12-02-reading-a-file.md)
    - [重构以增强模块化和错误处理](ch12-03-improving-error-handling-and-modularity.md)
    - [采用测试驱动型开发, 实现库的功能](ch12-04-testing-the-librarys-functionality.md)
    - [使用环境变量进行工作](ch12-05-working-with-environment-variables.md)
    - [将错误信息写出到标准错误而非标准输出](ch12-06-writing-to-stderr-instead-of-stdout.md)

# 用 Rust 的方式思考

- [函数式编程语言的特性: 迭代器和闭包 (closure)](ch13-00-functional-features.md)
    - [闭包: 能捕获自身环境的匿名函数](ch13-01-closures.md)
    - [使用迭代器处理一系列的项目](ch13-02-iterators.md)
    - [改进之前的 I/O 项目](ch13-03-improving-our-io-project.md)
    - [性能对比: 循环 vs. 迭代器](ch13-04-performance.md)

- [更多关于 Cargo 和 Crates.io 的内容](ch14-00-more-about-cargo.md)
    - [使用发行配置自定义构建](ch14-01-release-profiles.md)
    - [发布一个 Crate 到 Crates.io](ch14-02-publishing-to-crates-io.md)
    - [Cargo 工作空间](ch14-03-cargo-workspaces.md)
    - [使用 `cargo install` 安装 Crates.io 上的二进制文件](ch14-04-installing-binaries.md)
    - [通过自定义命令扩展 Cargo 的功能](ch14-05-extending-cargo.md)

- [智能指针](ch15-00-smart-pointers.md)
    - [使用 `Box<T>` 以指向堆内存上的数据](ch15-01-box.md)
    - [使用 `Deref` 特征将智能指针视作常规的引用](ch15-02-deref.md)
    - [使用 `Drop` 特征执行清理代码](ch15-03-drop.md)
    - [`Rc<T>`, 采用引用计数的智能指针](ch15-04-rc.md)
    - [`RefCell<T>` 与内部可变性模式](ch15-05-interior-mutability.md)
    - [循环引用会导致内存泄漏](ch15-06-reference-cycles.md)

- [无惧并发](ch16-00-concurrency.md)
    - [使用线程同时执行代码](ch16-01-threads.md)
    - [使用消息传递在线程间通信](ch16-02-message-passing.md)
    - [共享状态并发](ch16-03-shared-state.md)
    - [`Sync` 和 `Send` 特征造就的可扩展并发](ch16-04-extensible-concurrency-sync-and-send.md)

- [Rust 面向对象编程的特性](ch17-00-oop.md)
    - [面向编程语言的特点](ch17-01-what-is-oo.md)
    - [使用面向不同类型值的特征对象 (Trait Objects)](ch17-02-trait-objects.md)
    - [实现面向对象的设计模式](ch17-03-oo-design-patterns.md)

## 高级话题

- [模式和匹配](ch18-00-patterns.md)
    - [能用到模式的各种场景](ch18-01-all-the-places-for-patterns.md)
    - [可反驳性 (Refutability): 某一模式是否会匹配失败](ch18-02-refutability.md)
    - [模式相关的语法](ch18-03-pattern-syntax.md)

- [高级特性](ch19-00-advanced-features.md)
    - [不安全的 Rust](ch19-01-unsafe-rust.md)
    - [高级特征 (Traits)](ch19-03-advanced-traits.md)
    - [高级类型](ch19-04-advanced-types.md)
    - [高级函数与闭包](ch19-05-advanced-functions-and-closures.md)
    - [宏 (Macro)](ch19-06-macros.md)

- [最后的项目: 构建一个多线程 Web 服务器程序](ch20-00-final-project-a-web-server.md)
    - [构建一个单线程 Web 服务器程序](ch20-01-single-threaded.md)
    - [将我们的单线程服务器程序变成多线程服务器程序](ch20-02-multithreaded.md)
    - [优雅地停机并执行清理工作](ch20-03-graceful-shutdown-and-cleanup.md)

---

- [附录](appendix-00.md)
    - [A - 关键字](appendix-01-keywords.md)
    - [B - 运算符与符号](appendix-02-operators.md)
    - [C - 可派生的特征 (Trait)](appendix-03-derivable-traits.md)
    - [D - 实用开发工具](appendix-04-useful-development-tools.md)
    - [E - Rust 版本](appendix-05-editions.md)
    - [F - 该书的翻译](appendix-06-translation.md)
    - [G - Rust 是如何构建的以及 "Nightly Rust"](appendix-07-nightly-rust.md)
